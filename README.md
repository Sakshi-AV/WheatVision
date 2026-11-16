# WheatVision 🌾

[![Python](https://img.shields.io/badge/Python-3.9%20%7C%203.10%20%7C%203.11%20%7C%203.12-blue.svg)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Flask-3.0.3-green.svg)](https://flask.palletsprojects.com/)
[![ONNX Runtime](https://img.shields.io/badge/ONNX%20Runtime-1.18.1-orange.svg)](https://onnxruntime.ai/)
[![Vercel Ready](https://img.shields.io/badge/Deployment-Vercel-black.svg)](https://vercel.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**WheatVision** is an intelligent, deep-learning-powered web application for automated wheat grain detection, species classification, and agronomic food suitability estimation. Designed for cereal scientists, farmers, millers, and food technologists, it transforms visual grain samples into actionable cereal chemistry and quality insights in seconds.

---

## 📌 Table of Contents

- [Core Features](#-core-features)
- [Two-Stage AI Pipeline](#-two-stage-ai-pipeline)
- [Supported Wheat Species](#-supported-wheat-species)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Local Installation & Setup](#-local-installation--setup)
- [Configuration (.env)](#-configuration-env)
- [API Reference](#-api-reference)
- [Deployment on Vercel](#-deployment-on-vercel)
- [Scientific Disclaimer](#-scientific-disclaimer)
- [License](#-license)

---

## 🌟 Core Features

- **High-Accuracy Two-Stage Deep Learning**:
  - **Stage 1 (Binary Detector)**: Determines if the uploaded image contains genuine wheat grains (confidence threshold: $\ge 77\%$).
  - **Stage 2 (Species Classifier)**: Classifies the grain into one of four distinct wheat species (confidence threshold: $\ge 93\%$).
- **Optimized ONNX Runtime Engine**: Fast CPU inference without heavy PyTorch or TensorFlow dependencies, perfect for serverless and low-latency environments.
- **Agronomic & Cereal Chemistry Insights**: Automatically maps classified species to literature-backed protein content ranges (% dry weight), gluten profiles, and recommended food applications (pasta, bread, farro soups, flatbreads).
- **User Authentication & History**: Secure session-based authentication (Werkzeug scrypt password hashing) and user-specific scan history powered by SQLite.
- **Modern, Responsive Frontend**: Elegant agricultural-themed UI built with Vanilla JavaScript, CSS3, and Font Awesome—featuring drag-and-drop file uploads, image previews, and real-time inference feedback.
- **Cloud & Serverless Ready**: Native support for zero-config Vercel Serverless Functions with automatic `/tmp` database and upload fallback.

---

## 🧠 Two-Stage AI Pipeline

```
                     ┌────────────────────────┐
                     │  Uploaded Grain Image  │
                     └───────────┬────────────┘
                                 │
                                 ▼
                     ┌────────────────────────┐
                     │ Stage 1: Wheat Detector│
                     │  (ONNX Binary Model)   │
                     └───────────┬────────────┘
                                 │
                 ┌───────────────┴───────────────┐
                 │                               │
        Confidence < 77%                Confidence >= 77%
                 │                               │
                 ▼                               ▼
      ┌─────────────────────┐       ┌────────────────────────┐
      │   Reject Sample     │       │Stage 2: Species Model  │
      │ "Non-Wheat Grain"   │       │(ONNX 4-Class Classifier│
      └─────────────────────┘       └────────────┬───────────┘
                                                 │
                                                 ▼
                                    ┌────────────────────────┐
                                    │ Cereal Reference Data  │
                                    │ • Protein Content %    │
                                    │ • Gluten Elasticity    │
                                    │ • Food Suitability     │
                                    │ • Scientific Citations │
                                    └────────────────────────┘
```

---

## 🌾 Supported Wheat Species

WheatVision classifies four commercially and historically significant wheat species:

| Species | Scientific Name | Ploidy | Typical Protein Range | Primary Food Applications |
| :--- | :--- | :--- | :--- | :--- |
| **Common / Bread Wheat** | *Triticum aestivum* | Hexaploid ($2n=6x=42$) | **10.0% – 15.0%** | Sandwich bread, hearth loaves, cakes, pastries, household flour |
| **Durum / Pasta Wheat** | *Triticum durum* | Tetraploid ($2n=4x=28$) | **12.0% – 16.0%** | Extruded pasta (spaghetti, macaroni), semolina (sooji), couscous |
| **Emmer / Farro Medio** | *Triticum dicoccum* | Tetraploid ($2n=4x=28$) | **12.5% – 17.5%** | Traditional Italian farro soups, artisanal sourdough, rustic porridge |
| **Polish Wheat** | *Triticum polonicum* | Tetraploid ($2n=4x=28$) | **13.0% – 18.0%** | Heirloom pasta, rustic flatbreads, multigrain specialty flour |

---

## 🛠 Tech Stack

- **Frontend**: HTML5, CSS3, Vanilla JavaScript (ES6+), Font Awesome, Google Fonts (*Playfair Display* & *DM Sans*)
- **Backend**: Python 3.9+, Flask 3.0, Werkzeug, Flask-CORS, python-dotenv
- **Computer Vision & Inference**: ONNX Runtime, NumPy, Pillow (PIL)
- **Database**: SQLite3 (with automatic serverless `/tmp` migration)
- **Deployment**: Vercel Serverless Functions (`@vercel/python` WSGI runtime)

---

## 📁 Project Structure

```text
WheatVision/
├── api/
│   └── index.py               # Vercel serverless WSGI entrypoint
├── backend/
│   ├── app.py                 # Flask application factory, CORS, and core routes
│   ├── auth.py                # User registration, login, logout, and session handling
│   ├── database.py            # SQLite database schema, connection pooling, and migrations
│   ├── prediction.py          # /api/predict and history management routes
│   ├── predictor.py           # ONNX inference manager and image preprocessors
│   ├── data/
│   │   └── protein_reference.json # Cereal chemistry literature database
│   ├── models/
│   │   ├── wheat_detector.onnx    # Stage 1 binary detection model (~16.8 MB)
│   │   └── wheat_species.onnx     # Stage 2 species classification model (~17.5 MB)
│   └── requirements.txt       # Backend dependencies
├── frontend/
│   ├── CSS/
│   │   └── style.css          # Modern UI stylesheet
│   ├── js/
│   │   └── script.js          # Client-side state, API calls, and animations
│   ├── assets/                # Hero imagery and icons
│   └── index.html             # Single-page application entrypoint
├── run_local.bat              # One-click Windows development launcher
├── vercel.json                # Vercel routing rules and serverless bundle config
├── .vercelignore              # Excludes heavy .keras models and local artifacts
├── requirements.txt           # Vercel root dependency manifest
└── README.md                  # Project documentation
```

---

## 💻 Local Installation & Setup

### Option 1: Quick Launch (Windows)

Double-click `run_local.bat` or run:
```powershell
.\run_local.bat
```
This automatically boots the server at `http://127.0.0.1:5000` and launches your browser.

---

### Option 2: Manual Setup

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/Sakshi-AV/WheatVision.git
   cd WheatVision
   ```

2. **Create and Activate a Virtual Environment**:
   ```bash
   # Windows
   python -m venv .venv
   .venv\Scripts\activate

   # Linux / macOS
   python3 -m venv .venv
   source .venv/bin/activate
   ```

3. **Install Dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

4. **Run the Flask Backend**:
   ```bash
   cd backend
   python app.py
   ```

5. **Open the Application**:
   Navigate to [http://127.0.0.1:5000](http://127.0.0.1:5000) in your web browser.

---

## ⚙️ Configuration (.env)

You can customize runtime behavior by creating a `backend/.env` file:

```env
# Flask Secret Key for Session Encryption
SECRET_KEY=replace-with-a-secure-random-key

# Database and Storage Paths
DATABASE_PATH=instance/wheatvision.db
UPLOAD_FOLDER=uploads
MAX_UPLOAD_MB=5

# AI Model Confidence Thresholds
WHEAT_THRESHOLD=0.77
SPECIES_THRESHOLD=0.93

# CORS Allowed Origins (comma-separated)
ALLOWED_ORIGINS=http://127.0.0.1:5000,http://localhost:5000,http://127.0.0.1:5500
```

---

## 📡 API Reference

### Health & System
- `GET /api/health` — Checks database connectivity, model availability, and runtime environment.

### Authentication
- `POST /api/auth/register` — Register a new account (`name`, `email`, `password`).
- `POST /api/auth/login` — Sign in and initialize session (`email`, `password`).
- `POST /api/auth/logout` — Terminate current session.
- `GET /api/auth/me` — Retrieve the currently authenticated user.

### Predictions & Grain Analysis
- `POST /api/predict` — Analyze a grain image (`multipart/form-data` with `image` field).
  - Returns: Detector status, confidence scores, classified species, protein range, food suitability, and scientific literature notes.
- `GET /api/history` — Fetch recent prediction history for the authenticated user.
- `DELETE /api/history/<id>` — Delete a specific prediction record.
- `GET /api/reference` — Retrieve the full cereal science reference encyclopedia.

---

## 🚀 Deployment on Vercel

WheatVision is preconfigured for single-command or continuous deployment on **Vercel**:

### Continuous Deployment via GitHub (Recommended)

1. **Push your code to GitHub**:
   ```bash
   git push origin main
   ```
2. **Import into Vercel**:
   - Go to [vercel.com](https://vercel.com) and click **"Add New..."** &rarr; **"Project"**.
   - Select your repository: `Sakshi-AV/WheatVision`.
   - Set **Framework Preset** to `Other` and leave **Root Directory** as `./`.
   - Click **Deploy**.

### Direct Deployment via Vercel CLI

```bash
npx vercel login
npx vercel --prod
```

> **Note**: `vercel.json` and `.vercelignore` ensure only the lightweight `.onnx` models are bundled, staying well within Vercel's serverless package constraints.

---

## 🔬 Scientific Disclaimer

> The nutritional profiles, protein percentages, and food suitability insights presented by WheatVision are derived from published peer-reviewed cereal chemistry and agricultural literature (e.g., *Journal of Cereal Science*, *Journal of Experimental Botany*, *AACC International*).
> 
> The vision system identifies wheat species based on external morphological grain characteristics and **does not directly measure the physical chemical nitrogen or protein content** of the uploaded grain sample. Physical protein levels vary based on cultivar, nitrogen fertilization, precipitation, and environmental factors.

---

## 📄 License

This project is licensed under the [MIT License](https://opensource.org/licenses/MIT).
