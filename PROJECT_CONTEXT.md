# Contexto del proyecto: Alzheimer AI Scan

## 1. Identificación y resumen ejecutivo

**Nombre del proyecto:** Alzheimer AI Scan  
**Repositorio:** `AlzheimerScanAI`

Alzheimer AI Scan es un prototipo de sistema de apoyo al diagnóstico asistido
orientado a detectar patrones tempranos asociados al deterioro cognitivo y a la
enfermedad de Alzheimer a partir de resonancias magnéticas cerebrales (RMN).
Busca combinar predicción reproducible con explicaciones visuales que ayuden a
interpretar los resultados.

## 2. Justificación y propósito

El diagnóstico temprano del Alzheimer es clínicamente relevante para facilitar
una evaluación y atención oportunas. Sin embargo, revisar exhaustivamente
neuroimágenes requiere experiencia y tiempo, y los cambios estructurales sutiles
pueden ser difíciles de identificar de forma consistente.

El proyecto busca explorar herramientas que ayuden a analizar estas imágenes,
sin sustituir la valoración clínica. La transparencia es un requisito central:
una predicción debe acompañarse de información interpretable sobre los patrones
que influyeron en ella. El sistema no debe tratarse como una caja negra ni
presentarse como autoridad diagnóstica.

## 3. Datos de referencia

La referencia inicial es **OASIS-1 (Open Access Series of Imaging Studies)**,
un estudio transversal de neuroimagen. El proyecto utiliza resonancias
cerebrales T1 procesadas junto con variables clínicas y demográficas de apoyo.

La escala **CDR (Clinical Dementia Rating)** es la referencia clínica objetiva
disponible para describir el nivel de deterioro. Puede utilizarse para
distinguir participantes con CDR 0 de participantes con puntuaciones mayores,
siempre que la tarea, la población y los registros seleccionados se definan con
claridad. Una puntuación CDR distinta de cero indica deterioro según esa escala,
pero no constituye por sí sola un diagnóstico etiológico confirmado de
Alzheimer. Los datos clínicos incompletos deben mantenerse como desconocidos y
no inferirse como controles sanos.

El conjunto de referencia incluye sesiones de fiabilidad y puede contener
visitas repetidas de una misma persona. El número de filas no necesariamente
equivale al número de sujetos independientes. Antes de entrenar o evaluar, se
deben verificar la correspondencia entre identificadores, imágenes y
metadatos, y evitar que visitas de una misma persona queden repartidas entre
entrenamiento y evaluación.

## 4. Objetivos principales

- **Clasificación automatizada:** explorar modelos que identifiquen patrones
  asociados al deterioro con sensibilidad y precisión adecuadas. El rendimiento
  debe demostrarse mediante evaluación rigurosa; es un objetivo, no una
  capacidad clínica asumida.
- **Explicabilidad clínica (xAI):** producir explicaciones visuales que
  destaquen regiones anatómicas relevantes para cada predicción y permitan
  contrastar la salida del modelo con el conocimiento clínico. Áreas como el
  hipocampo o los ventrículos son hipótesis de interés, no hallazgos que deban
  darse por demostrados.
- **Optimización y reproducibilidad:** adoptar prácticas de MLOps para registrar
  datos y configuraciones de experimentos, métricas, artefactos y resultados,
  y facilitar la comparación sistemática de modelos.

## 5. Flujo de trabajo conceptual

1. Ingesta, verificación y preparación de neuroimágenes y metadatos clínicos.
2. Modelado predictivo de patrones visuales asociados a las etiquetas clínicas
   definidas para el estudio.
3. Evaluación con métricas adecuadas y controles contra fugas de información.
4. Generación de artefactos de interpretabilidad visual superpuestos o
   referenciados sobre la anatomía correspondiente.
5. Registro de configuraciones, métricas y artefactos para hacer reproducibles
   los experimentos.

## 6. Alcance y criterios de éxito

El alcance actual es un **prototipo funcional, reproducible y evaluado
experimentalmente** para asistencia a la investigación sobre RMN y deterioro
cognitivo. Se considerará exitoso si el flujo de datos y evaluación puede
repetirse, el rendimiento se informa con métricas apropiadas y las explicaciones
visuales son coherentes, documentadas y revisables.

Quedan fuera del alcance actual el diagnóstico autónomo, la recomendación de
tratamientos, el uso clínico sin validación prospectiva y aprobación
correspondiente, y las afirmaciones de que una predicción de CDR equivale a
confirmar Alzheimer. La generalización a otros centros, poblaciones,
protocolos de adquisición o enfermedades también requiere validación adicional.

## Principios para el desarrollo

- Tratar el sistema como apoyo a investigación y evaluación, no como sustituto
  del personal clínico.
- Diferenciar deterioro cognitivo medido por CDR de diagnóstico confirmado de
  Alzheimer.
- Preservar la procedencia y calidad de los datos; documentar valores faltantes
  y no convertirlos silenciosamente en etiquetas.
- Evitar fugas de información entre particiones, especialmente entre visitas
  repetidas de una misma persona.
- Informar limitaciones, incertidumbre y métricas por clase; no presentar una
  sola métrica como garantía de utilidad clínica.
- Preferir decisiones explicables y experimentos reproducibles sobre mejoras
  de rendimiento difíciles de auditar.
