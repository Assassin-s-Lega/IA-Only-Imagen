# Investigación de Modelos de Generación de Imagen y Video

## Modelos Comerciales (Closed Source)
- **Midjourney**: Líder en calidad estética. Utiliza arquitecturas de difusión propietarias (v6+). Muy enfocado en el "prompt engineering" y curación de datos.
- **Runway (Gen-3 Alpha)**: Especializado en video. Alta consistencia temporal y control de cámara.
- **Pika (Pika 1.5)**: Enfocado en efectos físicos y animaciones cortas.
- **Grok-2 (xAI)**: Utiliza **Flux.1** (de Black Forest Labs) para la generación de imágenes.
- **MiniMax / Hunyuan (Tencent)**: Modelos chinos de alto rendimiento. HunyuanVideo es uno de los primeros SOTA en video con pesos abiertos.

## Modelos Open Source (Pesos Abiertos)
- **Flux.1 (Black Forest Labs)**:
    - Arquitectura: **Diffusion Transformer (DiT)** con **Flow Matching**.
    - Parámetros: ~12B.
    - Características: Excelente renderizado de texto, anatomía humana y adherencia al prompt.
- **Stable Diffusion 3.5 (Stability AI)**:
    - Arquitectura: **MM-DiT (Multimodal Diffusion Transformer)**.
    - Parámetros: 8.1B (Large), 2.5B (Medium).
    - Técnica: Rectified Flow.
- **Wan2.1 (Alibaba)**:
    - Arquitectura: DiT con **VAE 3D Causal**.
    - Video: Soporta 480p/720p/1080p.
    - Eficiencia: El modelo 1.3B corre en GPUs de consumo (8GB VRAM).
- **HunyuanVideo (Tencent)**:
    - Arquitectura: DiT.
    - SOTA en video de código abierto antes de Wan2.1.

## Fundamentos Técnicos Comunes
1. **VAE (Variational Autoencoder)**: Comprime la imagen/video a un espacio latente para reducir el cómputo. En video, se usan VAEs 3D para comprimir el tiempo.
2. **DiT (Diffusion Transformer)**: Reemplaza la tradicional U-Net por bloques Transformer, permitiendo escalar mejor el modelo con más datos y parámetros.
3. **Flow Matching / Rectified Flow**: Una alternativa al proceso de difusión estándar que permite trayectorias de ruido más directas (líneas rectas), mejorando la velocidad y calidad de muestreo.
4. **Text Encoders**: T5-XXL y CLIP son los estándares para entender los prompts.
