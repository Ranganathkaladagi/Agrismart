# AgriSmart — Smart Agricultural Advisory & Crop Recommendation Platform

AgriSmart (Har Kisaan Ka Smart Saathi) is an intelligent agricultural decision-support system designed to empower farmers and agricultural experts. Powered by machine learning and real-time environmental data, AgriSmart provides data-driven crop recommendations, soil image analysis, weather forecasts, historical rainfall queries, and an interactive analytics dashboard.

---

## Key Features

- **ML-Powered Crop Recommendation**: Recommends the optimal crops based on soil nutrient levels (Nitrogen, Phosphorus, Potassium), soil pH, ambient temperature, humidity, and annual rainfall using a trained Random Forest classifier model.
- **Soil Vision Classifier**: Analyzes uploaded soil images to classify soil types (e.g., Alluvial, Black, Red, Laterite, Peat) to aid soil management decisions.
- **Real-Time Weather Integration**: Secure server-side weather proxy querying live weather metrics (temperature, humidity, weather condition) and reverse-geocoding coordinates to exact location names via OpenStreetMap Nominatim.
- **Historical Rainfall Database Lookup**: Fast lookup of average annual rainfall records across Indian states and districts.
- **Multilingual Support**: Built-in translation engine supporting multiple languages (English, Hindi, Punjabi, Marathi, Gujarati, Telugu, Tamil, Kannada, Bengali, Malayalam).
- **Interactive Analytics Dashboard**: Displays feature importance rankings of the machine learning model along with crop requirement distributions.
- **Production-Ready WSGI Setup**: Pre-configured with Flask, Gunicorn, and Render deployment specifications.

---

## Tech Stack

### Backend
- **Language**: Python 3.11+
- **Framework**: Flask 3.1, Flask-CORS
- **Machine Learning & Data Processing**: scikit-learn, pandas, numpy, joblib, Pillow
- **WSGI / Production Server**: Gunicorn

### Frontend
- **Interface**: HTML5, CSS3 (CSS Custom Properties, Dynamic Responsive Layouts), JavaScript (ES6+)
- **Localization**: Client-side translation engine (`translator.js`)

---

## Project Structure

```
AgriSmart/
├── backend/
│   ├── app.py                      # Primary Flask application and REST API endpoints
│   ├── model.pkl                   # Trained Random Forest crop prediction model
│   ├── Crop_recommendation.csv     # NPK and climatic crop dataset
│   ├── rainfall.csv                # District and state-level historical rainfall dataset
│   ├── requirements.txt            # Python dependencies
│   ├── train_model.py              # ML model training script
│   ├── train_soil_model.py         # Soil vision classifier training script
│   └── test_model.py               # Model sanity verification script
├── frontend/
│   ├── home.html                   # Landing page
│   ├── index.html                  # Crop predictor dashboard interface
│   ├── analytics.html              # Model & dataset analytics page
│   ├── script.js                   # Application interactivity and backend API integration
│   ├── style.css                   # Custom responsive styling
│   └── translator.js              # Multilingual translation module
├── index.html                      # Root web entry redirect
└── render.yaml                     # Render deployment configuration
```

---

## API Documentation

### Health Check
- **`GET /api/health`**
  - **Description**: Returns operational status and API version.
  - **Response**:
    ```json
    {
      "status": "ok",
      "service": "AgriSmart API",
      "version": "2.0",
      "language": "Python 3.11 / Flask"
    }
    ```

### Crop Recommendation
- **`POST /predict`**
  - **Description**: Accepts soil and weather metrics, returning top recommended crops and confidence scores.
  - **Headers**: `Content-Type: application/json`
  - **Request Body**:
    ```json
    {
      "N": 90,
      "P": 42,
      "K": 43,
      "temperature": 20.87,
      "humidity": 82.0,
      "ph": 6.5,
      "rainfall": 202.93
    }
    ```
  - **Response**:
    ```json
    {
      "best_crop": {
        "crop": "rice",
        "confidence": 98.5
      },
      "top3": [
        { "crop": "rice", "confidence": 98.5 },
        { "crop": "jute", "confidence": 1.2 },
        { "crop": "papaya", "confidence": 0.3 }
      ]
    }
    ```

### Soil Image Classification
- **`POST /predict-soil`**
  - **Description**: Upload an image file of soil for automatic visual classification.
  - **Body Format**: `multipart/form-data` with `image` file field.
  - **Response**:
    ```json
    {
      "soil": "Alluvial Soil",
      "confidence": 94.2
    }
    ```

### Live Weather Proxy
- **`GET /api/weather?lat=<latitude>&lon=<longitude>`**
  - **Description**: Secure backend proxy fetching current weather and reverse geocoded place information.
  - **Response**:
    ```json
    {
      "name": "Pune, Maharashtra, India",
      "district": "Pune",
      "temp": 24.5,
      "humidity": 68.0,
      "description": "Scattered clouds",
      "lat": 18.5204,
      "lon": 73.8567
    }
    ```

### Historical Rainfall Lookup
- **`GET /rainfall/<state>`**
  - **Description**: Retrieves average annual rainfall (mm) for a specific Indian state.
- **`GET /rainfall?city=<district>`**
  - **Description**: Retrieves annual rainfall (mm) for a given district.

### Model & Dataset Analytics
- **`GET /api/analytics`**
  - **Description**: Returns dataset statistics, category breakdowns, and Random Forest feature importances.

---

## Setup and Installation

### Prerequisites
- Python 3.11 or higher
- `pip` (Python package manager)

### Installation Steps

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/AgriSmart.git
   cd AgriSmart
   ```

2. **Set Up a Virtual Environment**:
   ```bash
   # On Windows
   python -m venv venv
   .\venv\Scripts\activate

   # On macOS/Linux
   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Install Dependencies**:
   ```bash
   pip install -r backend/requirements.txt
   ```

4. **Environment Variables (Optional)**:
   Set the OpenWeatherMap API key if customized:
   ```bash
   # On Windows (PowerShell)
   $env:OWM_API_KEY="your_openweather_api_key"

   # On macOS/Linux
   export OWM_API_KEY="your_openweather_api_key"
   ```

---

## Running the Application

### Local Development Server

Run the Flask application from the root directory:

```bash
python backend/app.py
```

The application will start at `http://localhost:5000`. Open your browser and navigate to:
- `http://localhost:5000/` or `http://localhost:5000/home` — Landing Page
- `http://localhost:5000/predictor` — Crop Predictor Interface
- `http://localhost:5000/analytics` — Analytics Dashboard

---

## Deployment

AgriSmart includes a ready-to-use `render.yaml` configuration file for deploying to Render.

- **Build Command**: `pip install -r backend/requirements.txt`
- **Start Command**: `gunicorn --chdir backend app:app --workers=2 --bind=0.0.0.0:$PORT`
- **Environment**: Python 3.11
