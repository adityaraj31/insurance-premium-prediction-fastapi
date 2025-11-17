# Insurance Premium Prediction (FastAPI)

Small FastAPI service that predicts an insurance premium category given user inputs (age, height, weight, income, smoker status, city, occupation). The project ships a model in `model/` and exposes a REST `POST /predict` endpoint.

**Quick Start**

- **Prerequisites:** Python 3.10+ (the repository includes a virtual environment named `myenv`).
- **Install:** create and activate a virtual environment, then install requirements:

```powershell
python -m venv myenv
.\myenv\Scripts\Activate.ps1
pip install -r requirements.txt
```

- **Run locally:**

```powershell
python -m uvicorn app:app --reload --port 8000
```

**API**

- **Health:** `GET /health`

Example:

```bash
curl http://127.0.0.1:8000/health
```

- **Predict:** `POST /predict` — accepts a JSON body matching the `UserInput` schema and returns a `PredictionResponse`.

Request body fields (from `schema/user_input.py`):

- `age` (int)
- `weight` (float, kg)
- `height` (float, meters)
- `income_lpa` (float, annual salary in LPA)
- `smoker` (bool)
- `city` (string)
- `occupation` (one of `retired`, `freelancer`, `student`, `government_job`, `business_owner`, `unemployed`, `private_job`)

Example `curl` request:

```bash
curl -X POST "http://127.0.0.1:8000/predict" \
  -H "Content-Type: application/json" \
  -d '{"age":30, "weight":70.0, "height":1.75, "income_lpa":8.5, "smoker":false, "city":"Mumbai", "occupation":"private_job"}'
```

PowerShell (Invoke-RestMethod) example:

```powershell
#$body can be created as a hashtable and converted to JSON to avoid quoting issues
#$body = @{ age=30; weight=70.0; height=1.75; income_lpa=8.5; smoker=$false; city='Mumbai'; occupation='private_job' }
#$json = $body | ConvertTo-Json
#Invoke-RestMethod -Method Post -Uri http://127.0.0.1:8000/predict -Body $json -ContentType 'application/json'
```

Example response (shape defined in `schema/prediction_response.py`):

```json
{
  "predicted_category": "High",
  "confidence": 0.84,
  "class_probabilities": { "Low": 0.01, "Medium": 0.15, "High": 0.84 }
}
```

**Project Structure**

- `app.py` — FastAPI application and endpoints
- `model/` — trained model code and artifacts (e.g. `model.pkl`, `predict.py`)
- `schema/` — Pydantic models for request/response validation
- `config/` — configuration (e.g. `city_tier.py`)
- `requirements.txt` — Python dependencies

**Notes & Best Practices**

- The `model/` directory contains the trained model file(s). These are typically large binary files and should not be committed to version control. Add `model/*.pkl` and `model/*.joblib` to your `.gitignore` (the repository's `.gitignore` should also exclude `myenv/`, `__pycache__/`, `.vscode/`, `.env`, logs and common Python artifacts).
- If you plan to deploy, ensure you pin versions in `requirements.txt` and use a production-ready server and process manager.

**Troubleshooting**

- If the app fails to start, confirm your virtual environment is activated and dependencies are installed.
- Check `app.py` for import errors and ensure `model/predict.py` can load the model file referenced by `MODEL_VERSION`.

---

If you'd like, I can also:

- add or update the `.gitignore` file with the recommended entries,
- add a small `README` badge or CI instructions,
- or create a minimal `Dockerfile` to containerize the app.
