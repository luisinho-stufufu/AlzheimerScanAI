# Cambios implementados

Registra aquí cada cambio relevante, idealmente con fecha, objetivo, archivos
afectados y validación realizada. Para cambios grandes, crea un archivo propio
con una descripción detallada.

## 2026-10-17 — Estructura inicial

- Se creó la estructura modular para notebooks, código fuente, configuración,
  modelos, datos y resultados.
- Se enlazaron las imágenes originales desde el disco externo.
- Se inicializó el repositorio Git y se añadieron las dependencias base.

## 2026-10-17 — Preparación binaria de OASIS-1

- Se añadió un generador reproducible de particiones por sujeto para `CDR = 0`
  frente a `CDR > 0`, con semilla fija y manifiestos locales.
- Se organizó un volumen T1 procesado por sujeto mediante enlaces simbólicos,
  sin copiar ni modificar el conjunto original.
- Se documentaron explícitamente las etiquetas ausentes y los sujetos sin
  volumen T1 disponible en los directorios locales.
- Se añadieron gráficos de distribución de CDR y de particiones; todo el texto
  visible de los gráficos se mantiene en inglés.

## 2026-10-17 — Inspección visual de volúmenes por partición

- Se añadió `notebooks/01_inspect_split_images.ipynb` para cargar un sujeto
  determinista de entrenamiento, validación y test y mostrar sus cortes
  centrales axial, coronal y sagital.
- Se añadió `nibabel` a las dependencias para leer volúmenes FreeSurfer MGZ.
- Se verificó la ejecución de las celdas contra tres volúmenes T1 locales.
