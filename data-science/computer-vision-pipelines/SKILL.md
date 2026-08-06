---
name: computer-vision-pipelines
description: Guía operativa para visión por computadora clásica y profunda. Úsala cuando el usuario trabaje con OpenCV, torchvision, YOLO/Ultralytics, MMDetection o modelos de fundación visual (SAM 2, DINOv2); necesite detectar/segmentar objetos, hacer seguimiento en video, elegir métricas (mAP, IoU), o diseñar el pipeline de aumento de datos y exportación a producción (ONNX/TensorRT).
---

# Visión por Computadora: Pipelines de Producción

## Cuándo se usa esta skill
Cualquier tarea sobre imágenes o video: desde procesamiento clásico de píxeles hasta detección/segmentación con deep learning y modelos de fundación.

## ⚠️ Vigencia de este contenido
Las versiones específicas de modelos citadas envejecen en meses. Verifica antes de recomendar como la opción actual:
- Si YOLO sigue en la versión citada (YOLO26) o ya hay una posterior con el mismo o mejor comportamiento NMS-free
- Si MMDetection sigue "en declive" frente a alternativas o el panorama cambió

## Mapa de decisión de framework

| Necesidad | Framework | Nota |
|---|---|---|
| Captura de cámara/video, transformaciones geométricas clásicas, filtros | **OpenCV** | No sirve para entrenar arquitecturas de deep learning desde cero — es preprocesamiento/interfaz de hardware |
| Ingesta de datasets estándar y aumentos acelerados por GPU para entrenar | **torchvision** (`transforms.v2`) | Integración nativa con tensores PyTorch |
| Detección/segmentación en tiempo real, producción, hardware de borde | **YOLO (Ultralytics)** | Diseño single-shot; versiones recientes (YOLO26) eliminan el post-procesamiento NMS |
| Investigación, benchmarking riguroso de arquitecturas exóticas | **MMDetection** | Muy modular pero con curva de aprendizaje alta; en declive frente a YOLO/HF por su complejidad |
| Transfer learning desde modelos de fundación (ViT, DINOv2), tareas zero-shot | **Hugging Face Transformers (visión)** | No es la opción para latencia crítica en microcontroladores |

## Combinaciones ganadoras
- **"Edge-Precision Cascade" (YOLO + SAM 2)**: YOLO localiza objetos rápido (bounding box); esa caja se pasa como *prompt* a SAM 2, que genera la máscara detallada y **mantiene el seguimiento temporal en video** vía su memoria de streaming (`SAM2VideoPredictor`) — no proceses el video cuadro a cuadro como imágenes sueltas, se pierde la consistencia de identidad y se dispara el costo de cómputo (~80% más).
- **"Foundational Linear Probing" (DINOv2 + PyTorch)**: usa embeddings congelados de DINOv2 (sin fine-tuning) y entrena solo un clasificador lineal encima — ideal cuando hay pocos datos etiquetados (ej. imágenes médicas).
- **Albumentations + torchvision.transforms.v2**: aumentos de datos coordinados (imagen + máscara + bounding box a la vez) antes de alimentar el entrenamiento.

## Anti-patrón que más degrada el rendimiento
Ejecutar transformaciones pesadas de OpenCV **dentro del bucle del `DataLoader` de PyTorch** en CPU: satura los hilos de la CPU y deja la GPU esperando datos en vez de entrenar. Mueve esas transformaciones a `torchvision.transforms.v2` o Albumentations, diseñadas para ejecutarse en paralelo.

## Métricas: qué reportar según la tarea
- **Detección de objetos**: **mAP@50** (coincidencia al 50% IoU) y **mAP@50:95** (promedio en rango de umbrales, más exigente).
- **IoU (Intersection over Union)**: solapamiento entre la caja predicha y la real — base de mAP.
- **Segmentación**: mIoU, Boundary F1; en video además **IDF1** (consistencia de identidad del objeto seguido en el tiempo).

## Flujo de producción recomendado
`OpenCV` (captura/interfaz de video) → `Albumentations`/`torchvision.transforms.v2` (aumentos) → `YOLO` (inferencia rápida) → exportar a `ONNX`/`TensorRT` para maximizar FPS en GPU de producción.

## Glosario mínimo
- **Bounding Box / OBB**: caja delimitadora (la OBB añade ángulo de rotación, útil para objetos inclinados).
- **NMS (Supresión de No Máximos)**: elimina cajas redundantes sobre el mismo objeto; los modelos "NMS-free" (YOLO26+) ya no lo necesitan como paso de post-procesamiento.
- **Segmentación semántica vs. de instancias vs. panóptica**: por clase (sin distinguir individuos) / por individuo / ambas combinadas.
