# Asclepius

**AI-powered skin cancer detection web service.** Asclepius classifies uploaded skin images as benign or malignant using a TensorFlow.js model served through a Hapi.js REST API, deployed on Google Cloud Platform.

![til](https://raw.githubusercontent.com/srnurizki/webservice-cancer/master/asclepius/demo.gif)

## Overview

Asclepius provides a lightweight backend service for binary skin cancer classification. Users submit an image, the model runs inference in real time, and the prediction result is persisted for record-keeping. The project was built to satisfy a machine-learning deployment submission for Dicoding Indonesia's *Machine Learning Application on Google Cloud* course.

## Features

- Binary classification (cancer / non-cancer) from a single uploaded image
- Real-time inference served over a REST API
- Prediction history persisted per request
- Structured error handling and input validation
- Cloud-native deployment with least-privilege access control

## Tech Stack

| Layer | Technology |
|---|---|
| Model | TensorFlow.js |
| Backend | Hapi.js |
| Backend hosting | Google Cloud Run |
| Frontend hosting | Google App Engine |
| Model storage | Google Cloud Storage |
| Prediction records | Firestore |
| Container registry | Google Artifact Registry |
| Access control | Google Cloud IAM |

## Model

- **Input:** 224x224x3 RGB image
- **Output:** sigmoid probability
- **Classification threshold:** 0.5

## Architecture

```
Client -> Cloud Run (Hapi.js API) -> TensorFlow.js model (inference)
                 |
                 |--> Cloud Storage (model artifact)
                 `--> Firestore (prediction history)

Static frontend -> App Engine
```

The backend is containerized and deployed to Cloud Run via Artifact Registry. Port binding is read from the `PORT` environment variable at runtime rather than hardcoded, to match Cloud Run's dynamic port assignment.

## API Endpoints

> Verify exact paths and payload shapes against the current implementation before publishing; the summary below reflects the last known submission version.

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/predict` | Accepts an image upload, runs inference, stores the result, and returns the prediction |
| `GET` | `/predict/histories` | Returns stored prediction history |

## Local Development

```bash
# install dependencies
npm install

# set required environment variables (see .env.example)
cp .env.example .env

# run locally
npm run start
```

## Deployment

The service is deployed to Cloud Run as a Docker container built and pushed to Artifact Registry. Model artifacts are stored in Cloud Storage and loaded at startup. Frontend static assets are served separately via App Engine.

Reviewer/read-only access to cloud resources is scoped with an IAM Viewer role, following least-privilege principles.

## Known Limitations

- Single-image, single-label binary classification only (no multi-class or multi-lesion detection)
- No confidence calibration reporting beyond raw sigmoid output

## Author

Satryo Akbar Nurizki - [GitHub](https://github.com/srnurizki)

## Acknowledgements

Built as part of Dicoding Indonesia's *Machine Learning Application on Google Cloud* course submission.
