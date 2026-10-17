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
│       └── images      # Enlace simbólico a las imágenes originales
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
