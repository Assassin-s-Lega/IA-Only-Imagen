# Plan A: Creación de un Sistema de IA Visual Orquestado desde Cero

Este plan detalla la construcción de un ecosistema modular donde un **Orquestador Central** gestiona múltiples **Modelos Expertos** pequeños, integrando visión computacional avanzada para guiar y validar la generación.

## 1. Arquitectura del Sistema: "Vision-Guided Mixture of Experts" (VG-MoE)

El sistema se divide en tres capas principales:

### A. Capa de Percepción y Control (Modelo de Visión)
- **Componente**: Integración de **YOLOv12** (o la versión más reciente YOLO26).
- **Función**: 
  - Analizar imágenes/videos de entrada para extraer etiquetas, poses y segmentación.
  - Actuar como "Crítico" en el bucle de entrenamiento, validando si el contenido generado cumple con los requisitos anatómicos o físicos.
  - Generar mapas de control (ControlNet) dinámicos para guiar a los modelos generativos.

### B. Capa Generativa (Expertos Especializados)
- **Componente**: Múltiples modelos **Diffusion Transformer (DiT)** pequeños (de 1B a 3B parámetros).
- **Especialización**: Cada modelo se entrena mediante **LoRA** o **Fine-tuning parcial** en dominios específicos (ej. rostros humanos, paisajes cinematográficos, dinámicas de fluidos).
- **Eficiencia**: Al ser modelos pequeños, pueden cargarse y descargarse rápidamente de la VRAM o ejecutarse en paralelo.

### C. Capa de Orquestación (El Router)
- **Componente**: Un modelo de lenguaje pequeño (SLM) como **Llama-3-8B** o un clasificador vectorial.
- **Función**: 
  - Descomponer el prompt del usuario en tareas específicas.
  - Seleccionar qué experto(s) deben activarse.
  - Fusionar las salidas de los expertos si es necesario (Multi-LoRA merging).

---

## 2. Estrategia de Entrenamiento con Datos Pequeños y Bucles Sintéticos

### Entrenamiento en Datasets Reducidos
- **Técnica**: Uso de **LoRA (Low-Rank Adaptation)** con rangos (rank) de 32 o 64.
- **Requisito**: Mínimo 20-50 imágenes de alta calidad por concepto o 5-10 clips de video consistentes.
- **Aumentación**: Uso de YOLOv12 para generar máscaras de segmentación automáticas, permitiendo entrenar al modelo en sujetos específicos sin ruido de fondo.

### Bucle de Retroalimentación Iterativo (Self-Correcting Loop)
1. **Generación**: El sistema genera 1000 imágenes/videos basados en prompts aleatorios dentro del dominio.
2. **Filtrado Crítico**: YOLOv12 analiza los resultados. Se descartan aquellos con errores anatómicos (ej. manos con 6 dedos) o inconsistencias físicas detectadas por la visión.
3. **Re-entrenamiento**: Los mejores resultados ("Gold Data") se reintroducen en el dataset de entrenamiento para una segunda fase de fine-tuning.
4. **Prevención de Colapso**: Se mantiene siempre un 20% de datos reales originales para evitar que el modelo degrade su creatividad al entrenar solo con sus propios errores.

---

## 3. Flujo de Trabajo del Sistema
1. **Entrada**: El usuario envía un prompt: *"Hombre corriendo en Marte estilo cyberpunk"*.
2. **Orquestación**: El Router identifica: [Sujeto: Humano], [Acción: Correr], [Estilo: Cyberpunk], [Entorno: Marte].
3. **Visión**: YOLOv12 recupera una pose de referencia de "correr" de la base de datos.
4. **Generación**: Se activan los expertos de "Cyberpunk" y "Marte", guiados por la pose de visión.
5. **Validación**: El sistema verifica que el resultado sea coherente antes de entregarlo.
