# Plan B: Personalización de Modelos SOTA y Guía Estratégica

Este plan se enfoca en tomar modelos de vanguardia (como **Flux.1**, **Wan2.1** o **Seedream**) y adaptarlos a necesidades específicas mediante técnicas de personalización profunda y sistemas de guía persistente.

## 1. Selección del Modelo Base
- **Imagen**: **Flux.1 [dev]** por su excelente manejo de texto y anatomía, o **Stable Diffusion 3.5 Large** por su flexibilidad.
- **Video**: **Wan2.1** por su eficiencia en VRAM y capacidad de generar videos largos con consistencia temporal.

---

## 2. Metodología de Personalización (Fine-Tuning)

### A. Entrenamiento mediante Dreambooth + LoRA
- **Datos**: 15-30 imágenes de alta resolución (sujeto, estilo o producto).
- **Proceso**: 
  - Etiquetado manual detallado (ej. "un retrato de [IDENTIFICADOR] en estilo [ESTILO]").
  - Entrenamiento de la red UNet/Transformer y el CLIP Text Encoder simultáneamente para asegurar que el modelo asocie palabras específicas con la nueva identidad visual.
- **Regularización**: Uso de "Class Images" (imágenes genéricas de la misma categoría) para evitar que el modelo olvide cómo generar otros elementos similares.

### B. Inyección de Conocimiento vía Prompt del Sistema
Para guiar las generaciones posteriores sin necesidad de re-entrenar constantemente, se implementa un **Wrapper de Sistema**:
- **System Prompt Persistente**: Instrucciones ocultas que se anteponen a cada petición del usuario.
  - *Ejemplo*: "Eres un motor de renderizado cinematográfico. Siempre prioriza la iluminación volumétrica, el desenfoque de movimiento natural y evita colores saturados artificiales. Si el usuario pide un humano, asegúrate de que la anatomía siga los estándares de [IDENTIFICADOR] entrenado."
- **RAG Visual (Retrieval-Augmented Generation)**: Antes de generar, el sistema busca en una base de datos local imágenes similares a las que el usuario desea y las inyecta como "Image Prompt" o referencia visual (IP-Adapter).

---

## 3. Implementación de Control de Salida
- **Negative Prompts Maestros**: Una lista curada de conceptos prohibidos (ej. "deformed, low quality, watermarks") inyectada automáticamente.
- **Filtros de Visión Post-Generación**: Integración de un modelo de visión ligero para clasificar la calidad estética y descartar automáticamente fallos técnicos antes de que el usuario los vea.

---

## 4. Estrategia de Re-entrenamiento con Datos Generados
1. **Curación Humana**: El usuario marca como "Favoritas" ciertas generaciones.
2. **Extracción de Características**: El sistema analiza qué prompts y parámetros produjeron esas imágenes exitosas.
3. **Fine-tuning Incremental**: Cada mes, el modelo se actualiza ligeramente con las 100 mejores imágenes generadas y validadas, refinando su "gusto" hacia las preferencias específicas del usuario o empresa.
