# Fase 1: preparación de datos OASIS-1

## Inventario clínico inicial

Revisión del archivo
`oasis_cross-sectional-5708aa0a98d82080.xlsx` (hoja
`oasis_cross-sectional`):

- 436 filas de sesión: 416 identificadores `MR1` y 20 sesiones de fiabilidad
  `MR2`.
- 416 sujetos únicos al agrupar los identificadores por participante y contar
  solo su sesión base `MR1`.
- 235 sujetos tienen CDR en `MR1`; 181 no tienen etiqueta CDR.
- Distribución de CDR entre los sujetos etiquetados: CDR 0: 135; CDR 0.5: 70;
  CDR 1: 28; CDR 2: 2. No se observan sujetos con CDR 3.
- Los 20 registros `MR2` corresponden a visitas repetidas, no a participantes
  adicionales. No tienen CDR y no deben contarse como sujetos independientes.

La visualización está en [`../../Graphics/CDR_distribution.svg`](../../Graphics/CDR_distribution.svg).
Los textos de gráficos y outputs se mantienen en inglés para su presentación.
La barra «Missing CDR» muestra los 181 sujetos `MR1` sin etiqueta; queda
separada de las clases clínicas.

## Tratamiento de CDR ausente

No imputar los CDR ausentes como cero ni asignarles una clase basada en edad,
imagen u otras variables. Mantenerlos como desconocidos y excluirlos del
entrenamiento y evaluación supervisados cuyo objetivo sea predecir CDR. Podrán
considerarse por separado para exploración no supervisada o aprendizaje
autosupervisado, siempre documentando su procedencia y evitando usarlos para
ajustar decisiones sobre el test.

Los valores CDR describen deterioro en una escala clínica y no confirman por sí
solos la etiología de Alzheimer. En particular, una tarea binaria CDR 0 frente
a CDR > 0 debe describirse como clasificación de deterioro según CDR, no como
diagnóstico de Alzheimer.

## Esquema inicial de particiones

La copia local de FreeSurfer contiene un volumen procesado `mri/T1.mgz` para
226 de los 235 sujetos etiquetados. Nueve IDs con CDR (2 controles CDR 0 y 7
con CDR > 0) no tienen un directorio/volumen correspondiente en la copia local;
se registran como excluidos por falta de imagen y no se imputan ni cuentan en
las particiones. Por tanto, la tarea binaria local tiene 133 sujetos CDR 0 y 93
CDR > 0 (226 en total).

Para el primer experimento supervisado, utilizar una división estratificada
aproximada 70/15/15 de esos 226 sujetos con imagen disponible:

| Partición | CDR 0 | CDR > 0 | Total |
|---|---:|---:|---:|
| Entrenamiento | 93 | 65 | 158 |
| Validación | 20 | 14 | 34 |
| Test | 20 | 14 | 34 |
| Total | 133 | 93 | 226 |

Estratificar por la etiqueta binaria, pero hacer la asignación por sujeto, no
por fila/imagen. Derivar el grupo del identificador del participante ignorando
el sufijo de visita (por ejemplo, `MR1`/`MR2`) y mantener todas las sesiones de
un mismo sujeto en una sola partición. La preparación actual usa solo la sesión
base `MR1` y un volumen T1 procesado por participante. Las sesiones `MR2` no se
añaden al conjunto supervisado. Se fija la semilla 42 y se guardan inventarios
locales de los sujetos por partición.

Este tamaño deja conjuntos de validación y test pequeños, por lo que sus
métricas tendrán incertidumbre considerable. La división sirve como punto de
partida reproducible, no como validación clínica concluyente. Considerar
validación cruzada agrupada y estratificada dentro del conjunto de desarrollo,
sin tocar el test hasta cerrar las decisiones del modelo; informar intervalos
de confianza y sensibilidad/especificidad además de métricas agregadas.

No iniciar una clasificación de cuatro niveles como objetivo principal: solo
hay dos sujetos con CDR 2, insuficientes para representar y evaluar esa clase
de manera fiable en entrenamiento, validación y test. No combinar niveles
clínicos sin justificarlo previamente.

## Aumento de datos

No aplicar aumento para «crear» sujetos ni como sustituto de más datos. Primero
construir un baseline sin aumento y comprobar correspondencia sujeto-imagen,
calidad, orientación, registro y protocolo de preprocesamiento. Con 226 sujetos
etiquetados disponibles —93 con CDR > 0— existe riesgo alto de sobreajuste,
sobre todo si se entrenan modelos volumétricos grandes desde cero.

Después del baseline, probar aumentos moderados y plausibles para RMN (por
ejemplo, pequeñas transformaciones espaciales o variaciones de intensidad),
aplicados únicamente a ejemplos de entrenamiento y dentro de cada partición.
No transformar validación/test. Revisar que la transformación no altere
anatómicamente la etiqueta ni oculte estructuras relevantes. Aumentos
sintéticos no incrementan el número de sujetos independientes; considerar
transfer learning o modelos más sencillos y reportar la comparación sin/con
aumento.

## Fuente y limitaciones de inventario

La descripción oficial del conjunto OASIS está disponible en
<https://sites.wustl.edu/oasisbrains/datasets/>. La tabla local describe
sesiones y medidas clínicas. Se selecciona un `mri/T1.mgz` procesado por sujeto
como entrada inicial. Los enlaces simbólicos
organizados y los manifiestos se generan localmente mediante
`python -m src.data.prepare_binary_splits`; no se copian los archivos de imagen
y los artefactos de `data/processed/` no se suben al repositorio. Los nueve
sujetos etiquetados cuyo volumen no se encontró quedan registrados en
`excluded.csv`. Antes de interpretar resultados, se debe revisar la calidad de
los volúmenes FreeSurfer y confirmar que este procesamiento es apropiado para
la tarea.
