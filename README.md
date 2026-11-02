# Insurance Premium Prediction API

A machine learning application that predicts an insurance premium category from user information through a REST API built with **FastAPI**. It uses **Pydantic** to validate incoming data and a pre-trained scikit-learn model to generate predictions.

## Project Overview

The API accepts details such as age, height, weight, income, smoking status, city, and occupation. It derives useful features—including BMI, age group, lifestyle risk, and city tier—and passes them to the trained model.

The response includes:
- The predicted insurance premium category
- The model's confidence score
- Probability scores for each available category

> **Note:** This project predicts a premium category, not an exact insurance premium amount.

## Features

- REST API built with FastAPI
- Request validation using Pydantic
- Automatic feature engineering:
  - **BMI:** calculated from weight and height
  - **Age group:** young, adult, middle-aged, or senior
  - **Lifestyle risk:** low, medium, or high based on smoking status and BMI
  - **City tier:** tier 1, tier 2, or tier 3 based on the city
- ML predictions using a pre-trained model
- Confidence score and class probability output
- Health-check endpoint with model version and load status
- Dockerfile for containerizing the API
- Interactive API documentation through Swagger UI

## Tech Stack

| Technology | Purpose |
|---|---|
| Python | Application development |
| FastAPI | REST API framework |
| Pydantic | Input validation and derived fields |
| Pandas | Preparing input data for prediction |
| Scikit-learn | Machine learning model |
| Uvicorn | ASGI server |
| Docker | Containerization |

## Project Structure

```text
Insurance-Premium-Prediction-FastAPI/
│
├── app.py
├── config/
│   └── city_tier.py
├── model/
│   ├── model.pkl
│   └── predict.py
├── schema/
│   ├── user_input.py
│   └── prediction_response.py
├── Dockerfile
├── requirements.txt
└── README.md
```

## How It Works

1. A client sends user details to the `/predict` endpoint.
2. FastAPI uses the Pydantic `UserInput` schema to validate the request.
3. The schema derives BMI, age group, lifestyle risk, and city tier.
4. The prediction module loads the pre-trained model and prepares the input as a Pandas DataFrame.
5. The model predicts a category and calculates class probabilities.
6. The API returns the prediction, confidence score, and probability distribution.

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/shanu202000/Insurance-Premium-Prediction-FastAPI.git
cd Insurance-Premium-Prediction-FastAPI
```

### 2. Create and activate a virtual environment

**Windows (PowerShell):**

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

**Windows (Command Prompt):**

```cmd
python -m venv venv
venv\Scripts\activate
```

**macOS / Linux:**

```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Run the API

Run this command from the project root directory:

```bash
uvicorn app:app --reload
```

The API will be available at:

- Base URL: http://127.0.0.1:8000
- Swagger UI: http://127.0.0.1:8000/docs
- ReDoc: http://127.0.0.1:8000/redoc

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | Welcome message |
| GET | `/health` | API and model health check |
| POST | `/predict` | Predict the insurance premium category |

### Health Check

**Request:**

```http
GET /health
```

**Example response:**

```json
{
  "status": "OK",
  "version": "1.0.0",
  "model_loaded": true
}
```

### Make a Prediction

**Request:**

```http
POST /predict
Content-Type: application/json
```

**Example request body:**

```json
{
  "age": 30,
  "weight": 75,
  "height": 1.75,
  "income_lpa": 12,
  "smoker": false,
  "city": "Chandigarh",
  "occupation": "private_job"
}
```

**Example response shape:**

```json
{
  "response": {
    "predicted_category": "Medium",
    "confidence": 0.8432,
    "class_probabilities": {
      "Low": 0.01,
      "Medium": 0.8432,
      "High": 0.1468
    }
  }
}
```

*The values above illustrate the response format; actual predictions depend on the model and input data.*

### Input Fields

| Field | Type | Validation / Description |
|---|---|---|
| `age` | Integer | Greater than 0 and less than 120 |
| `weight` | Float | Greater than 0 (kg) |
| `height` | Float | Greater than 0 and less than 2.5 (metres) |
| `income_lpa` | Float | Annual income in lakh per annum; greater than 0 |
| `smoker` | Boolean | Whether the user smokes |
| `city` | String | City name; normalized to title case |
| `occupation` | String | One of the supported values below |

Supported occupation values:

```text
retired
freelancer
student
government_job
business_owner
unemployed
private_job
```

## Run with Docker

Make sure Docker is installed and running.

### 1. Build the image

```bash
docker build -t insurance-premium-api .
```

### 2. Run the container

```bash
docker run -p 8000:8000 insurance-premium-api
```

Open http://127.0.0.1:8000/docs to access the API documentation.

## Model and Input Notes

- The model is loaded from `model/model.pkl` when the prediction module is imported.
- The model file must be present at that path when the application starts.
- Input validation and feature derivation are implemented in `schema/user_input.py`.
- City-to-tier mappings are maintained in `config/city_tier.py`.
- The model version exposed by the health endpoint is currently `1.0.0`.

## Future Improvements

- Add automated unit and API tests with Pytest.
- Add CI checks using GitHub Actions.
- Add structured logging and more consistent error handling.
- Add model performance and prediction monitoring.
- Add a deployment workflow for a cloud platform.
- Add a frontend interface for easier interaction.

## Author

**Shanu Kumar**

- GitHub: https://github.com/shanu202000

## License

No license is currently specified in this repository. Add a license file if you intend to define permissions for reuse and distribution.
