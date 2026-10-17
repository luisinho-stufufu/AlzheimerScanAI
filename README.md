# AlzheimerScanAI

Estructura inicial para desarrollar y ejecutar un pipeline de machine learning
con Jupyter Notebooks. Los módulos reutilizables viven en `src/`; los notebooks
deben orquestar el análisis y apoyarse en esos módulos, evitando duplicar lógica.

## Estructura

```text
AlzheimerScanAI/
├── config/             # Configuración del proyecto y de los experimentos
├── data/
│   └── raw/
│       ├── images      # Enlace simbólico a las imágenes originales
│       └── oasis-cross-sectional-5708aa0a98d82080.xlsx
│                           # Enlace simbólico a metadatos OASIS-1
│   └── processed/      # Splits locales y enlaces a los volúmenes
├── Graphics/           # Visualizaciones del análisis
├── docs/
│   ├── changes/        # Registro de cambios implementados
│   ├── experiments/    # Registro de experimentos y resultados
│   └── notes/          # Notas, incluida la preparación de datos OASIS-1
├── models/             # Pesos y artefactos de modelos entrenados
├── notebooks/          # Exploración, entrenamiento y evaluación interactiva
├── outputs/            # Figuras, métricas y resultados generados
├── src/
│   ├── data/           # Carga, validación y preprocesamiento de datos
│   ├── models/         # Arquitecturas y componentes de modelos
│   └── utils/          # Funciones auxiliares compartidas
├── .gitignore
└── requirements.txt
```

## Datos

`data/raw/images` apunta directamente a
`/run/media/luisinho/Datos/Data/images`. El enlace no copia las imágenes; el
disco externo debe estar montado en esa ruta para acceder a ellas. Los datos
originales no se duplican en el repositorio.

La hoja de metadatos enlazada en `data/raw/` se encuentra junto a los datos
externos en `Data/text/`. Con `openpyxl` instalado, prepara la tarea binaria y
su vista local de enlaces simbólicos mediante:

```bash
python -m src.data.prepare_binary_splits
```

El comando usa la sesión base `MR1`, asigna `CDR = 0` a `cdr_0` y `CDR > 0` a
`cdr_gt_0`, y crea particiones por sujeto. Los manifiestos se guardan en
`data/processed/binary_cdr/splits/`; los volúmenes T1 procesados se organizan
por partición y clase bajo `data/processed/binary_cdr/images/`. Son artefactos
locales excluidos de Git. Los gráficos y outputs visuales del proyecto se
redactan en inglés.

El notebook `notebooks/01_inspect_split_images.ipynb` carga un sujeto
determinista de cada partición y muestra los cortes sagital, coronal y axial de
su volumen T1 procesado.

Las recomendaciones y límites de normalización están en
`docs/notes/mri-normalization.md`; el notebook
`notebooks/02_compare_mri_normalization.ipynb` compara un volumen T1 de
entrenamiento antes y después de aplicar z-score dentro de la máscara cerebral.

## Entorno

Desde la raíz del proyecto, crea y activa un entorno virtual e instala las
dependencias:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter lab
```

## Git y seguimiento del trabajo

El proyecto está inicializado como repositorio Git en la rama `main`. Los
cambios de implementación se documentan en `docs/changes/`; los experimentos
y sus resultados, en `docs/experiments/`; y las notas generales, en
`docs/notes/`. Los pesos generados y el contenido de `outputs/` se mantienen
fuera del control de versiones; sus carpetas sí se conservan.
