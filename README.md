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

Example (Python):

```python
import requests

response = requests.get("http://127.0.0.1:8000/health")
print(response.json())
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

Example (Python with requests):

```python
import requests

payload = {
    "age": 30,
    "weight": 70.0,
    "height": 1.75,
    "income_lpa": 8.5,
    "smoker": False,
    "city": "Mumbai",
    "occupation": "private_job"
}

response = requests.post("http://127.0.0.1:8000/predict", json=payload)
print(response.json())
```

Example (Python with httpx for async):

```python
import httpx
import asyncio

async def predict():
    payload = {
        "age": 30,
        "weight": 70.0,
        "height": 1.75,
        "income_lpa": 8.5,
        "smoker": False,
        "city": "Mumbai",
        "occupation": "private_job"
    }
    
    async with httpx.AsyncClient() as client:
        response = await client.post("http://127.0.0.1:8000/predict", json=payload)
        print(response.json())

asyncio.run(predict())
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

