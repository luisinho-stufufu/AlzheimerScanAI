# Recomendaciones de normalización para RMN T1 de OASIS-1

## Resumen y decisión inicial

Los valores de intensidad de RMN se expresan en unidades arbitrarias: no son
medidas físicas directamente comparables entre sujetos o escáneres. Además de
la documentación del pipeline de FreeSurfer, se inspeccionó el
`scripts/recon-all.log` incluido en los datos de ejemplo. Confirma que la
generación de `mri/T1.mgz` ejecutó `mri_nu_correct.mni` sobre `orig.mgz` y
después `mri_normalize -g 1 nu.mgz T1.mgz`. En una etapa posterior se generó
`brain.mgz` mediante `mri_normalize -mask brainmask.mgz -aseg aseg.mgz
norm.mgz brain.mgz`; `brainmask.mgz` procede del paso de extracción de cerebro.
Por tanto, el `T1.mgz` actual **no es el T1 original sin procesar** y un z-score
adicional es una transformación experimental sobre una imagen ya corregida y
normalizada por FreeSurfer.

Recomendación para el siguiente baseline:

1. Mantener el volumen FreeSurfer de origen y no volver a aplicar por defecto
   otra corrección de campo de bias.
2. Revisar visualmente la orientación, la cobertura y la segmentación/eliminación
   de cráneo. Como alternativa de entrada, comparar `brain.mgz` con el
   `T1.mgz` que se usa actualmente; ambas salidas y sus máscaras deben pasar
   control de calidad.
3. Comparar el T1 FreeSurfer tal cual con un **z-score por volumen**, calculado
   solo sobre los vóxeles de la región cerebral y manteniendo el fondo en cero.
   Es una línea base sencilla para una CNN, no una normalización clínicamente
   validada ni una decisión final.
4. Comparar ambos tratamientos con idénticas particiones por sujeto y métrica,
   utilizando validación. No seleccionar el método mirando el conjunto de test.

El notebook [`../../notebooks/02_compare_mri_normalization.ipynb`](../../notebooks/02_compare_mri_normalization.ipynb)
muestra cortes e histogramas antes y después de ese z-score en un sujeto del
conjunto de entrenamiento. El método mantiene fuera del z-score los vóxeles de
fondo con una máscara binaria derivada de `brainmask.mgz > 0`. En esta versión
de FreeSurfer, `brainmask.mgz` conserva intensidades cerebrales y **no es por sí
solo una máscara binaria**; su conversión a máscara se hace explícita en el
notebook. Se comprueba que la máscara y `T1.mgz` compartan geometría.

## Técnicas: uso, ventajas y límites

| Técnica | Recomendación para este proyecto |
|---|---|
| **Corrección de bias field / intensidad no uniforme** (p. ej., N3/N4) | FreeSurfer documenta `mri_nu_correct` antes de producir `T1.mgz`. Evitar repetir N4 de forma automática; podría corregirse dos veces. Aplicarla solo si el control de calidad evidencia inhomogeneidad residual, y evaluar esa variante separadamente. |
| **Skull stripping / máscara cerebral** | Útil para excluir aire, cráneo y tejidos extracraneales de estadísticas y aprendizaje. El pipeline observado genera `brainmask.mgz` desde la extracción cerebral y `brain.mgz` en una etapa posterior de normalización cerebral. Inspeccionar errores de máscara porque eliminar anatomía periférica puede sesgar la señal. En este conjunto, `brainmask.mgz` contiene intensidades de imagen: formar la máscara como `brainmask > 0` es una decisión explícita que requiere control visual. |
| **Z-score por volumen dentro de máscara** | Candidato recomendado para una línea base de deep learning: restar la media y dividir por la desviación estándar de los vóxeles cerebrales de cada sujeto. No usar el fondo en las estadísticas ni sustituir valores de fondo por un valor distinto de cero tras la transformación. Registrar estadísticas y controlar desviación nula. |
| **Escalado a rango fijo (min–max / percentiles)** | Puede ser una comparación sencilla para modelos, pero debe definirse si los límites se calculan por sujeto o con estadísticas del entrenamiento. El clipping puede saturar intensidades y eliminar diferencias potencialmente útiles; no asumir que mejora a z-score. |
| **Estandarización basada en tejidos** (p. ej., WhiteStripe) | Tiene motivación para comparar intensidades de tejidos entre adquisiciones; requiere localizar tejido de referencia de forma fiable. Considerarla si se amplía a escáneres/protocolos diversos o se demuestra variación residual. No es necesaria de entrada para este conjunto FreeSurfer homogéneamente procesado. |
| **Histogram matching** | No recomendar como default. Puede hacer que coincidan distribuciones que difieren por anatomía o patología y borrar variación biológica. Si se evalúa, definir una referencia usando solo entrenamiento y comprobar explícitamente que conserva contraste y morfología relevantes. |
| **Normalización espacial / registro** | Es distinta de normalización de intensidad. `T1.mgz` se conforma en FreeSurfer, pero esto no significa que todos los cerebros estén deformados a una plantilla común. Mantener la geometría NIfTI/affine y verificar orientación/espaciado; comparar registro a plantilla solo como experimento aparte porque la deformación puede alterar estructuras y mapas de explicación. |

## Protocolo experimental propuesto

- Usar la partición actual por sujeto y fijar la semilla.
- Registrar la versión/archivo FreeSurfer seleccionado (`T1.mgz` o `brain.mgz`),
  la máscara usada y las estadísticas de intensidad.
- Comparar al menos: (A) `T1.mgz` tal cual; (B) el mismo `T1.mgz` con z-score
  dentro de máscara. Como análisis separado, comparar la salida `brain.mgz`.
- Ajustar cualquier límite global, plantilla de intensidades o estadística de
  conjunto solo con entrenamiento; aplicar sin reestimar a validación/test.
  Para z-score estrictamente por sujeto, calcular parámetros de cada imagen de
  entrada de forma independiente y no usar sus etiquetas.
- Verificar las máscaras en sujetos con CDR 0 y CDR > 0, comprobar imágenes y
  distribuciones por clase, y asegurar que no desaparezcan voxeles anatómicos
  relevantes. Medir sensibilidad, especificidad y AUROC con incertidumbre,
  además de rendimiento por clase; no decidir mirando el test.
- La comparación visual de un caso solo confirma cómo se ve la transformación;
  no demuestra que normalice mejor la cohorte ni que aumente la validez clínica.

## Fuentes

- FreeSurfer, **Recon-all process table (stable v6.0)**: documenta la
  corrección de intensidad, el paso `mri_normalize` que genera `T1.mgz` y el
  posterior volumen `brain.mgz`. El `recon-all.log` del sujeto de ejemplo
  confirma los pasos correspondientes en la copia OASIS local.
  <https://surfer.nmr.mgh.harvard.edu/fswiki/ReconAllTableStableV6.0>
- FreeSurfer, **mri_normalize**: describe su normalización de materia blanca
  hacia intensidades alrededor de 110.
  <https://surfer.nmr.mgh.harvard.edu/fswiki/mri_normalize>
- FreeSurfer, **mri_watershed**: describe la salida cerebral con cráneo y
  tejidos extracerebrales eliminados.
  <https://surfer.nmr.mgh.harvard.edu/fswiki/mri_watershed>
- Shinohara RT et al. **Statistical normalization techniques for magnetic
  resonance imaging.** *NeuroImage: Clinical*. 2014;6:9–19.
  <https://pmc.ncbi.nlm.nih.gov/articles/PMC4215426/>
- TorchIO, **Standardize intensity transform**: z-score opcionalmente calculado
  sobre una máscara.
  <https://github.com/TorchIO-project/torchio/blob/main/src/torchio/transforms/intensity/standardize.py>
