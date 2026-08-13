# Estudio de Requisitos Técnicos para Implementación en Máquinas Locales

Para ejecutar sistemas de IA generativa de imagen y video con capacidades de orquestación y visión, se requiere una infraestructura equilibrada entre potencia de cómputo y ancho de banda de memoria.

## 1. Requisitos de Hardware (Especificaciones 2026)

### Nivel Base (Entusiasta / Desarrollo Inicial)
- **GPU**: NVIDIA RTX 3090 / 4090 (24GB VRAM). Es el estándar mínimo para modelos como Flux.1 o Wan2.1 sin una degradación masiva de velocidad.
- **RAM**: 64GB DDR5. Necesaria para manejar los datasets en memoria durante el entrenamiento de LoRAs.
- **Almacenamiento**: 2TB NVMe Gen4. Los pesos de los modelos y los datasets de video ocupan cientos de gigabytes.
- **CPU**: Intel i7/i9 o AMD Ryzen 7/9 (12+ núcleos). Crucial para el pre-procesamiento de datos y la orquestación.

### Nivel Profesional (Producción Local / Entrenamiento Intensivo)
- **GPU**: 2x NVIDIA RTX 5090 (o equivalentes de 32GB+ VRAM) conectadas vía NVLink/PCIe 5.0. Permite ejecutar el Orquestador y varios Expertos simultáneamente.
- **RAM**: 128GB+ DDR5.
- **Refrigeración**: Sistemas líquidos o estaciones de trabajo con flujo de aire optimizado para cargas de 24/7.

---

## 2. Stack de Software y Dependencias
- **Sistema Operativo**: Ubuntu 24.04 LTS (Recomendado por su estabilidad con drivers de NVIDIA).
- **Entorno de Ejecución**: 
  - **Docker con NVIDIA Container Toolkit**: Para aislar dependencias y facilitar el despliegue.
  - **Python 3.11+** y **PyTorch 2.5+** (con soporte para Triton y Flash Attention 2).
- **Herramientas de Interfaz**:
  - **ComfyUI**: La herramienta más potente para orquestación modular de nodos.
  - **InvokeAI**: Para una experiencia de usuario más pulida y enfocada en diseño.
- **Modelos de Visión**: Ultralytics YOLOv12/26 integrado vía API local o librería de Python.

---

## 3. Puesta en Funcionamiento (Paso a Paso)

1. **Configuración de Drivers**: Instalación de CUDA 12.x y drivers propietarios de NVIDIA.
2. **Gestión de Modelos**: 
   - Descarga de pesos base desde **Hugging Face**.
   - Organización de modelos en carpetas específicas: `/checkpoints`, `/loras`, `/vae`, `/controlnet`.
3. **Pipeline de Orquestación**:
   - Configuración de un servidor local (ej. FastAPI) que reciba el prompt, llame al orquestador (SLM) y dirija la petición al nodo de ComfyUI correspondiente.
4. **Optimización de Inferencia**:
   - Uso de **Quantization (GGUF/EXL2)** para reducir el uso de VRAM de los modelos grandes (ej. pasar un modelo de 24GB a 12GB con mínima pérdida de calidad).
   - Implementación de **Model Offloading**: Mover modelos de la VRAM a la RAM del sistema cuando no estén en uso activo.

## 4. Mantenimiento y Seguridad
- **Privacidad**: Al ser local, los datos no salen de la máquina, lo cual es ideal para propiedad intelectual sensible.
- **Actualizaciones**: Scripts automatizados para hacer `git pull` de los repositorios de modelos y herramientas semanalmente, dado el ritmo frenético de la industria.
