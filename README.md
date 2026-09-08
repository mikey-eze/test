# IntendCrop — SIH 26131 — Real Local Prototype

**Detect → Predict → Recommend → Act → Verify → Learn**

This version keeps the original IntendCrop feature set and upgrades the core architecture around the most important requirement: **real image processing and a persistent local data layer**.

## What is now real

- Android sends the actual selected image to the laptop backend.
- Backend decodes and validates the image.
- Image quality is checked.
- A plant-presence visual gate rejects obvious non-plant images instead of inventing a diagnosis.
- Detection records are persisted in SQLite.
- Farmer cases are persisted in SQLite.
- Officer case status changes are persisted.
- Feedback/outcomes are persisted.
- A trained disease model can be plugged into `backend/models/disease_model.pt` without changing the Android API contract.
- Risk calculation uses supplied weather values rather than one fixed score.

## Important detection behavior

The project **does not fake disease predictions** when no trained disease model is available.

Current pipeline:

```text
Camera / Gallery
      ↓
Image validation
      ↓
Image quality gate
      ↓
Plant-presence gate
      ↓
Trained disease model (when installed)
      ↓
Confidence + result
      ↓
Database
      ↓
Advisory / expert verification
```

To enable actual trained disease classification/detection, install the optional `ultralytics` dependency and place a validated model at:

`backend/models/disease_model.pt`

The model must be trained for the crops/diseases you want to claim. The application deliberately abstains instead of returning fake disease names when that model is missing.

## Laptop = local server

Your laptop runs:

- FastAPI backend: port `8000`
- SQLite database: `backend/intendcrop.db`
- AI model: `backend/models/`
- Uploaded analysis images: `backend/uploads/`

Android Emulator uses:

`http://10.0.2.2:8000/`

A physical phone on the same Wi-Fi should use the laptop's LAN address, for example:

`http://192.168.x.x:8000/`

## Backend APIs

```text
GET    /health
POST   /detect
GET    /risk
POST   /soil
GET    /markets
POST   /chat
POST   /cases
GET    /cases
PATCH  /cases/{id}
POST   /feedback
```

## Database tables

- `detections`
- `cases`
- `feedback`

The SQLite database is intentionally local for the prototype. Later it can be moved to PostgreSQL by setting `DATABASE_URL` without changing the API layer.

## Run backend — Windows

```bat
cd backend
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

Optional trained model support:

```bat
pip install ultralytics
```

Then place the model in:

```text
backend/models/disease_model.pt
```

## Run Android

1. Open the project root in Android Studio.
2. Let Gradle sync.
3. Start the backend on the laptop.
4. Run the Android app.
5. Select/capture a plant image.
6. Run **AI diagnosis**.
7. The actual image is sent to the laptop.
8. The backend processes it and stores the detection.

## Product scope

IntendCrop is designed for **anyone who grows plants/crops**, including farmers, home growers, gardeners, students and small growers. The farmer-friendly simplicity remains a design principle, but the product is not restricted to farmers.

The complete product also includes:

- AI Crop Doctor
- Early Risk Radar
- Weather/natural hazard intelligence
- Disease/pest hotspots
- Soil intelligence
- Actionable advisory
- Multilingual AI assistant
- Speaking/voice assistance
- Farmer/expert connection
- Officer Command Center
- Trusted agricultural sources
- Market/APMC intelligence
- Feedback and learning loop

## Next model/data upgrade

The biggest remaining technical upgrade is the **validated disease/pest model and real external data feeds**. The backend contracts are already separated so these can be added later without redesigning the Android screens.

Never present AI predictions as confirmed diagnoses, laboratory soil results, or guaranteed outbreak predictions.


## Corrected architecture

Android now uses the FastAPI service for detection, risk, soil, markets, chat, and officer cases. The Android app no longer contains a hard-coded Gemini API key or fabricated demo diagnosis fallback.

### Run backend
```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8000
```

For a real disease detector, train a crop-specific model and place the resulting `disease_model.pt` in `backend/models/`. See `backend/train_model.py`. Without a trained model, detection intentionally abstains instead of inventing a disease.

### Android emulator
The app connects to `http://10.0.2.2:8000/`, which points from the Android emulator to the host machine's port 8000.
