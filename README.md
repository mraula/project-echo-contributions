# Project Echo — ML Engine Contributions

Portfolio write-up of my work on **[Project Echo](https://github.com/DataBytes-Organisation/Project-Echo)**,
a real-time Australian wildlife-monitoring platform built at DataBytes.
IoT Raspberry Pi sensors capture audio in the field, on-device ML classifies the species,
and detections flow via MQTT to a FastAPI backend and a live-map dashboard — deployed as a
10-service stack on GCP/GKE.

> The platform code lives in the DataBytes org repo. This repo documents **my specific
> contributions** and links to the real pull requests, rather than republishing org code.

**Role:** ML Engine Co-Lead (T3 2025) → Project Lead (T1 2026)
**Stack:** Python · PyTorch · TensorFlow/TFLite · ONNX · MQTT · Docker · Kubernetes (GKE) · MLflow · FastAPI

---

## Highlights

### Edge-inference pipeline — >99% bandwidth reduction
[PR #903](https://github.com/DataBytes-Organisation/Project-Echo/pull/903)

Designed and shipped an IoT edge-inference pipeline running **EfficientNetV2 (TFLite)
directly on Raspberry Pi nodes**. A dual-mode MQTT design sends structured detections
(~300 bytes of JSON) instead of raw audio (~500 KB) — a **>99% cut in per-detection
bandwidth** — while preserving full backward compatibility.

### Kubernetes hostname-resolution fix
[PR #860](https://github.com/DataBytes-Organisation/Project-Echo/pull/860)

Diagnosed and fixed a hostname-resolution bug that broke both local Docker Compose and
GKE cloud deploys. Root cause: `API_HOST` not propagating correctly between Kubernetes
service names and Compose hostnames.

### Model optimisation — PyTorch → ONNX → TFLite INT8
[PR #819](https://github.com/DataBytes-Organisation/Project-Echo/pull/819) · [PR #845](https://github.com/DataBytes-Organisation/Project-Echo/pull/845)

Built the quantisation pipeline achieving **69–71% model size reduction** while classifying
**100+ wildlife species** in real time. Verified EfficientNet operator compatibility for the
standard TFLite runtime (no custom ops), and built a multi-backend inference engine
(`ModelLoader` + abstract `InferenceEngine`) switching dynamically between ONNX Runtime and
the TFLite Interpreter.

### MQTT integration module — 14 unit tests
[PR #888](https://github.com/DataBytes-Organisation/Project-Echo/pull/888)

Built the engine-side MQTT integration with **14 unit tests** covering payload validation,
broker configuration, and race-condition handling.

### Training framework + MLflow
PyTorch training framework (Hydra config) supporting EfficientNetV2 and PANNs models,
ArcFace / Circle loss, mixed-precision (bfloat16) training, SpecAugment, and LMDB caching —
with all experiments tracked in MLflow.

---

## Architecture

```
IoT Sensors → MQTT Broker → ML Engine → TensorFlow Serving → FastAPI → MongoDB/Redis → HMI
```

## Links
- Platform (org repo): https://github.com/DataBytes-Organisation/Project-Echo
- LinkedIn: https://www.linkedin.com/in/raul-movila
