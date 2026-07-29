# Comparación de modelos BCI con EEG

Este proyecto compara cuatro pipelines para clasificar imaginación motora
izquierda y derecha a partir de señales EEG:

1. CSP + LDA (baseline).
2. CSP + PSD + LDA.
3. CSP + PSD + FOOOF + SVM.
4. CSP + wavelets + XGBoost.

El análisis utiliza cinco canales: `Fz`, `C3`, `Cz`, `C4` y `Pz`.

## Notebooks

- `01_Modelo_CSP_LDA.ipynb`: baseline basado en cuatro log-varianzas CSP.
- `02_Modelo_CSP_PSD_LDA.ipynb`: concatena CSP con potencia theta, alpha y
  beta por canal.
- `03_Modelo_CSP_PSD_FOOOF_SVM.ipynb`: agrega parámetros periódicos y
  aperiódicos de FOOOF y clasifica con SVM.
- `04_Modelo_CSP_Wavelets_XGBoost.ipynb`: combina CSP con características
  wavelet y XGBoost.
- `05_Estadisticas_Comparacion.ipynb`: comparación emparejada de los cuatro
  pipelines usando al sujeto como unidad experimental.

## Dataset

Los notebooks esperan encontrar:

```text
../data/all_epochs_clean-epo.fif
```

El dataset no se incluye en el repositorio. Debe ser un objeto `Epochs` de MNE
con una columna `subject` en sus metadatos y los cinco canales indicados.

Dataset empleado en el análisis:

- 14.312 épocas.
- 60 sujetos.
- 5 canales.
- 512 Hz.
- Ventana de 0,5 a 3,5 segundos.
- Clases `Left` y `Right` prácticamente balanceadas.

## Instalación

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Después se pueden ejecutar los notebooks en orden.

## Metodología

La evaluación entre sujetos utiliza particiones agrupadas para que una misma
persona no aparezca simultáneamente en entrenamiento y prueba. La evaluación
individual utiliza folds estratificados. Las transformaciones aprendidas, como
CSP y el escalado, permanecen dentro de los pipelines para evitar fuga de
información.

El notebook estadístico calcula una puntuación media por sujeto y compara los
modelos mediante Wilcoxon emparejado, corrección de Holm, intervalos bootstrap
y tamaño de efecto.

## Nota sobre FOOOF

El proyecto mantiene FOOOF porque forma parte de los modelos solicitados. El
paquete está deprecado en favor de `specparam`, por lo que una migración futura
debe validarse cuidadosamente.

