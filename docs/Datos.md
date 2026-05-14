# Datos del Proyecto (M5 Forecasting)

Este documento describe con detalle los ficheros CSV utilizados en el TFG, todos provenientes de la competición M5 Forecasting (Walmart) de Kaggle.

## 1. Descripción General
- Origen: Competición M5 Forecasting (Accuracy), Kaggle.
- Granularidad base: serie diaria por combinación producto×tienda (≈ 30.490 series).
- Estructura jerárquica: Total → Estado (CA, TX, WI) → Tienda (10 tiendas: CA_1..CA_4, TX_1..TX_3, WI_1..WI_3) → Categoría (FOODS, HOBBIES, HOUSEHOLD) → Departamento (FOODS_1..3, HOBBIES_1..2, HOUSEHOLD_1..2) → Producto.
- Horizonte de predicción oficial: 28 días.
- Periodo temporal: desde 2011-01-29 (d_1) hasta d_1913 para entrenamiento, y d_1914–d_1941 para validación tipo competición.

Ruta por defecto en el cuaderno: `BASE = '/kaggle/input/competitions/m5-forecasting-accuracy/'`.

## 2. Ficheros CSV Principales

### 2.1 sales_train_validation.csv
- Contenido: ventas históricas diarias para cada producto×tienda hasta d_1913.
- Dimensiones típicas: 30.490 filas × (6 metadatos + 1.913 días) ≈ 1.919 columnas.
- Columnas clave:
  - id: identificador único de la serie (ej.: FOODS_3_090_CA_1_validation)
  - item_id: producto
  - dept_id: departamento
  - cat_id: categoría
  - store_id: tienda (ej.: CA_1)
  - state_id: estado (CA, TX, WI)
  - d_1 … d_1913: ventas diarias (unidades). Enteros no negativos
- Uso en el cuaderno: análisis exploratorio, entrenamiento de modelos y baselines a nivel SKU y agregaciones.

### 2.2 sales_train_evaluation.csv
- Contenido: igual que validation, pero extendido hasta d_1941 para validación local (28 días adicionales).
- Dimensiones típicas: 30.490 filas × (6 metadatos + 1.941 días).
- Uso en el cuaderno: validación “tipo competición” en el horizonte de 28 días.

### 2.3 calendar.csv
- Contenido: mapeo de cada día `d_i` a su fecha de calendario y atributos temporales/eventos.
- Dimensiones típicas: ~1.969 filas (un registro por día) × ~14 columnas.
- Columnas clave:
  - date: fecha (YYYY-MM-DD)
  - d: identificador del día (ej.: d_1)
  - wm_yr_wk: semana comercial (entero)
  - weekday, wday: nombre y código de día de la semana
  - month, year: mes y año
  - event_name_1, event_type_1, event_name_2, event_type_2: eventos/festivos
  - snap_CA, snap_TX, snap_WI: indicador de cupones SNAP por estado (0/1)
- Uso en el cuaderno: ingeniería de variables temporales, alineación de fechas y unión con precios vía `wm_yr_wk`.

### 2.4 sell_prices.csv
- Contenido: precios de venta semanales por producto y tienda.
- Dimensiones típicas: ~684.000 filas × 4 columnas.
- Columnas clave:
  - store_id, item_id, wm_yr_wk: clave compuesta
  - sell_price: precio de venta (float)
- Uso en el cuaderno: variables exógenas de precio; se une a las series diarias mediante `wm_yr_wk` proveniente de `calendar.csv`.

### 2.5 sample_submission.csv
- Contenido: plantilla de envío con 28 columnas de predicción por serie (F1…F28).
- Columnas:
  - id: identificador de serie para envío
  - F1…F28: predicciones de los 28 días de horizonte
- Uso en el cuaderno: no esencial salvo si se replicara el formato de competición.

## 3. Partición y Uso en Este Trabajo
- Entrenamiento: d_1…d_1913.
- Validación local “tipo competición”: d_1914…d_1941 (test ciego).
- En el cuaderno, la ruta por defecto apunta a Kaggle: `BASE = '/kaggle/input/competitions/m5-forecasting-accuracy/'`.
  - Para ejecución fuera de Kaggle, se puede cambiar `BASE` a una ruta local equivalente tras descargar el dataset con la API de Kaggle o manualmente.

## 4. Preprocesamiento (Resumen)
- Conversión de ventas a formato largo (`melt`) para facilitar agregaciones y uniones.
- Integración de `calendar` (por `d`/`date` y `wm_yr_wk`) y `sell_prices` (por `item_id`, `store_id`, `wm_yr_wk`).
- Ingeniería de variables: día de la semana, mes, año, eventos/festivos, indicadores SNAP, retardos (lags) y medias móviles (7, 14, 28 días).
- Control de memoria: uso de liberación de objetos intermedios (gc) en pasos costosos.

## 5. Métrica
- Métrica oficial M5: WRMSSE (Weighted RMSSE) multi-nivel jerárquico.
- En este TFG se reporta RMSE a nivel de serie/agregado para comparar modelos (Naive, ARIMA/SARIMA, Prophet, XGBoost), indicando su diferencia con la métrica oficial.

## 6. Supuestos y Limitaciones
- No se versionan datos en este repositorio; se consumen desde Kaggle por defecto.
- Estacionalidad semanal (m=7) marcada; efectos por estado/tienda y eventos (SNAP/festivos).
- Precios con variación semanal; imprescindible sincronización por `wm_yr_wk`.

## 7. Referencias
- Página oficial M5 (Accuracy) en Kaggle: https://www.kaggle.com/competitions/m5-forecasting-accuracy
- M5 Competitors' Guide (PDF) enlazado desde la página de la competición en Kaggle.

## 8. Ejemplo de Carga e Integración (Ilustrativo)
```python
import pandas as pd

BASE = '/kaggle/input/competitions/m5-forecasting-accuracy/'

sales = pd.read_csv(BASE + 'sales_train_evaluation.csv')
cal   = pd.read_csv(BASE + 'calendar.csv')
price = pd.read_csv(BASE + 'sell_prices.csv')

# Despivotar ventas a formato largo
d_cols = [c for c in sales.columns if c.startswith('d_')]
sales_long = sales.melt(
    id_vars=['id','item_id','dept_id','cat_id','store_id','state_id'],
    value_vars=d_cols,
    var_name='d',
    value_name='sales'
)

# Unir calendario (selección de columnas relevantes)
df = sales_long.merge(
    cal[['d','date','wm_yr_wk','wday','month','year','event_name_1','event_type_1','snap_CA','snap_TX','snap_WI']],
    on='d', how='left'
)

# Unir precios (precio es semanal; unir por item_id, store_id y wm_yr_wk)
df = df.merge(price, on=['store_id','item_id','wm_yr_wk'], how='left')
```
