# TFG Análisis Predictivo de Series Temporales en el Entorno Retail - Anexo de Código

Este repositorio contiene el cuaderno y la estructura mínima para reproducir el trabajo del TFG sobre modelos de predicción de series temporales en el sector retail. Está pensado como anexo: incluye instrucciones de entorno, dependencias y organización básica de archivos.

## Alumno
2026 Álvaro Tébar Folch, Universidad Europea de Valencia


## Contenido
- `TFG_M5_Forecasting_Tebar.ipynb`: cuaderno principal con el análisis y los experimentos.
- `requirements.txt`: dependencias de Python para ejecutar el cuaderno.
- `data/`: carpeta para ubicar los datos localmente (no se versiona el dataset por tamaño/licencia).
- `.gitignore`: exclusiones típicas para Python/Jupyter/macOS.

## Requisitos
- Python 3.11 recomendado
- Pip y virtualenv (o conda, si lo prefieres)
- Compilador C/C++ instalado (recomendado para `prophet`)

## Instalación rápida
1. Crear y activar entorno virtual
   
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate  # Windows: .venv\Scripts\activate
   ```

2. Instalar dependencias
   
   ```bash
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

3. Abrir Jupyter Lab o Notebook
   
   ```bash
   pip install jupyterlab  # si no lo tienes
   jupyter lab
   # o: jupyter notebook
   ```

## Datos (M5)
- Por defecto, el cuaderno usa directamente el dataset de Kaggle con `BASE = '/kaggle/input/competitions/m5-forecasting-accuracy/'`.
- Si ejecutas el cuaderno fuera de Kaggle y quieres trabajar localmente, cambia la variable `BASE` al inicio del cuaderno para apuntar a tu ruta local del dataset o utiliza la Kaggle API para descargarlo previamente.

Este repositorio no incluye datos ni carpeta `data/` para evitar versionar archivos pesados o con restricciones de licencia.

## Reproducibilidad
- Tras ajustar `BASE`, ejecuta las celdas en orden. Algunas secciones pueden tardar (por ejemplo, Prophet/ARIMA) dependiendo del hardware.
- Si Prophet tarda en compilar dependencias en la primera instalación, es normal. Alternativamente, instala `cmdstanpy` de forma independiente si necesitas acelerar compilación.

## Documentación de Datos
- Descripción detallada del dataset y su uso: ver [docs/Datos.md](docs/Datos.md).
- Página oficial de la competición M5 (Accuracy) en Kaggle: https://www.kaggle.com/competitions/m5-forecasting-accuracy


## Contacto
Para dudas o mejoras, abre un issue o contacta al autor del TFG.
