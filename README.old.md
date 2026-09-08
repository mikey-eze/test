# IntendCrop — SIH 26131

An Android-first agricultural early-warning and decision-support prototype built around the SIH 26131 concept.

## What is included

- **AI Crop Doctor:** image picker, image preview, confidence, symptoms, actions and escalation path.
- **Early Risk Radar:** localized crop risk score, drivers and preventive actions.
- **Soil Intelligence:** pH, N, P, K and EC input with a clear lab-vs-estimate distinction.
- **Market Intelligence:** price, distance, transport estimate and net realization comparison.
- **Krishi AI Assistant:** contextual chat with source labels.
- **Officer Command Center:** case queue, validation/request-info actions and hotspot-map integration point.
- **FastAPI backend:** runnable API with deterministic baseline AI adapter and clean replacement point for a real ML model.
- **Offline fallback:** the Android app remains demonstrable if the Python service is unavailable.

## Open in Android Studio

1. Extract the ZIP.
2. Open the `IntendCropSIH26131` folder in Android Studio.
3. Let Gradle sync.
4. Use an Android emulator (API 26+) or a physical Android phone.
5. Run the `app` configuration.

## Run the AI service (recommended for full demo)

Windows:

```bat
cd backend
run_windows.bat
```

Linux/macOS:

```bash
cd backend
chmod +x run_linux.sh
./run_linux.sh
```

The API runs on port **8000**. The Android emulator reaches the host service through `10.0.2.2:8000`.

## Production AI upgrade path

The project deliberately separates the UI/API contract from model inference. Replace `detect_with_model()` in `backend/main.py` with a trained model adapter (YOLO/ONNX/PyTorch/TFLite) trained on locally relevant crops and diseases. Keep confidence calibration, human validation and an abstain/low-confidence path.

For on-device inference, place a TFLite/ONNX model under `app/src/main/assets/models/` and add a model runner without changing the screens or domain models.

## How this stands out

The prototype is not just a leaf classifier. It demonstrates the closed loop:

**Detect → Predict → Recommend → Act → Verify → Learn**

The architecture has explicit extension points for weather APIs, GIS hotspot layers, government knowledge retrieval, officer workflows, notifications, voice I/O, user authentication, real APMC feeds and validated feedback storage.

## Suggested SIH demo sequence

1. Open Home and show the intelligence cockpit.
2. Scan a rice leaf and show confidence + actions.
3. Open Risk Radar and show a high-risk forecast with drivers.
4. Enter soil values and show crop-aware guidance.
5. Compare markets using net realization rather than headline price.
6. Ask the AI assistant a crop question.
7. Open Officer Command Center and show the validation queue/hotspot integration.
8. Explain the feedback loop and production ML upgrade path.
