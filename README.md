# TFG M5 Forecasting - Anexo de Código

Este repositorio contiene el cuaderno y la estructura mínima para reproducir el trabajo del TFG sobre la competición M5 (Forecasting). Está pensado como anexo: incluye instrucciones de entorno, dependencias y organización básica de archivos.

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
- La ruta por defecto en el cuaderno apunta a Kaggle: `BASE = '/kaggle/input/competitions/m5-forecasting-accuracy/'`.
- Para ejecutarlo localmente, descarga el dataset desde Kaggle y colócalo en `data/m5/` manteniendo la misma estructura de archivos que Kaggle (por ejemplo `sales_train_evaluation.csv`, etc.).
- Edita la celda de constantes al inicio del cuaderno y cambia:
  
  ```python
  BASE = 'data/m5/'
  ```

En la carpeta `data/` no se versionan archivos binarios por tamaño/licencias. Se incluye un `README.md` con notas y un `.gitkeep` para conservar la carpeta vacía en git.

## Reproducibilidad
- Tras ajustar `BASE`, ejecuta las celdas en orden. Algunas secciones pueden tardar (por ejemplo, Prophet/ARIMA) dependiendo del hardware.
- Si Prophet tarda en compilar dependencias en la primera instalación, es normal. Alternativamente, instala `cmdstanpy` de forma independiente si necesitas acelerar compilación.

## Licencia
Este proyecto se distribuye bajo la licencia MIT.

Copyright (c) 2026 Álvaro Tébar Folch, Universidad Europea de Valencia

Consulta el archivo `LICENSE` para el texto completo.

## Contacto
Para dudas o mejoras, abre un issue o contacta al autor del TFG.
