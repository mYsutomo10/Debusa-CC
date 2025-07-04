# Buah Sayur Classification API
A machine learning-powered REST API for classifying fruits and vegetables using TensorFlow.js. This service can identify 10 different types of fruits and vegetables from uploaded images and provides educational content including descriptions, benefits, and interactive quizzes.

## Features

- **Image Classification**: Classify 10 types of fruits and vegetables from uploaded images
- **Educational Content**: Get Wikipedia descriptions and health benefits for each classified item
- **Interactive Quiz**: Answer questions about the classified fruits/vegetables
- **Cloud Storage**: Store prediction results in Google Firestore
- **Docker Support**: Containerized application for easy deployment
- **Google Cloud Run**: Production-ready deployment on Google Cloud Platform

## Supported Classifications

The API can classify the following fruits and vegetables:
- **Apel** (Apple)
- **Brokoli** (Broccoli) 
- **Jeruk** (Orange)
- **Kangkung** (Water Spinach)
- **Mangga** (Mango)
- **Pisang** (Banana)
- **Strawberry**
- **Terong** (Eggplant)
- **Toge** (Bean Sprouts)
- **Wortel** (Carrot)

## Technology Stack

- **Runtime**: Node.js
- **Framework**: Hapi.js
- **Machine Learning**: TensorFlow.js
- **Database**: Google Cloud Firestore
- **Containerization**: Docker
- **Deployment**: Google Cloud Run
- **External APIs**: Wikipedia API

## Project Structure

```
buahsayur/
├── src/
│   ├── exceptions/           # Custom error classes
│   │   ├── ClientError.js
│   │   └── InputError.js
│   ├── features/
│   │   └── quiz/            # Quiz functionality
│   │       ├── index.js
│   │       └── questions.js
│   ├── server/              # Server configuration
│   │   ├── handler.js       # Route handlers
│   │   ├── routes.js        # API routes
│   │   └── server.js        # Server setup
│   └── services/            # Business logic services
│       ├── benefitList.js   # Health benefits data
│       ├── loadModel.js     # ML model loader
│       ├── postPrediction.js # Prediction service
│       ├── storeData.js     # Firestore operations
│       └── wikipediaApi.js  # Wikipedia integration
├── Dockerfile
├── package.json
└── README.md
```

## Quick Start

### Prerequisites

- Node.js (v20 or higher)
- npm or yarn
- Google Cloud Platform account
- Docker (for containerization)

### Local Development

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd buahsayur
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Set up environment variables**
   Create a `.env` file in the root directory:
   ```env
   MODEL_URL=https://storage.googleapis.com/saved-model-900/model-debusa-tfjs/model.json
   PORT=3000
   ```

4. **Run the development server**
   ```bash
   npm run start:dev
   ```

5. **Test the API**
   The server will be running at `http://localhost:3000`

## 📡 API Endpoints

### 1. Predict Classification

**POST** `/predict`

Classify an uploaded image of fruit or vegetable.

**Request:**
- Content-Type: `multipart/form-data`
- Body: `image` (file upload)

**Response:**
```json
{
  "status": "success",
  "message": "Model is predicted successfully",
  "data": {
    "id": "uuid-string",
    "result": "Apel",
    "description": "Wikipedia description...",
    "benefit": "Health benefits...",
    "url": "https://id.wikipedia.org/wiki/Apel",
    "createdAt": "01 January 2024, 12:00:00",
    "quiz": {
      "question": "Apa warna apel yang matang?",
      "options": ["Biru", "Hijau", "Merah", "Ungu"]
    }
  }
}
```

### 2. Submit Quiz Answer

**POST** `/submit`

Submit an answer to a quiz question.

**Request:**
- Content-Type: `multipart/form-data`
- Body: 
  - `question`: The quiz question
  - `option`: Selected answer option

**Response (Correct Answer):**
```json
{
  "status": "success",
  "message": "Selamat! Kamu menjawab dengan benar!",
  "explanation": "Detailed explanation of the correct answer..."
}
```

**Response (Incorrect Answer):**
```json
{
  "status": "fail",
  "message": "Yah, jawaban salah. Ayo coba lagi!"
}
```

## Testing with Postman

### Local Testing

1. **Start the server**
   ```bash
   npm run start:dev
   ```

2. **Create a POST request**
   - URL: `http://localhost:3000/predict`
   - Method: POST
   - Body: form-data
   - Key: `image`
   - Value: Upload an image file

3. **Send the request**
   You should receive a JSON response with the classification results.

### Testing Quiz Functionality

1. **Create a POST request**
   - URL: `http://localhost:3000/submit`
   - Method: POST
   - Body: form-data
   - Keys: `question` and `option`

## Docker Deployment

### Build Docker Image

```bash
docker build -t gcr.io/capstone-buahsayur/debusa-app:latest .
```

### Run Locally with Docker

```bash
docker run -p 3000:3000 -e PORT=3000 gcr.io/capstone-buahsayur/debusa-app:latest
```

### Push to Google Container Registry

```bash
docker push gcr.io/capstone-buahsayur/debusa-app:latest
```

## Google Cloud Deployment

### 1. Set up Google Cloud Storage

1. Create a Google Cloud Storage bucket
2. Upload your TensorFlow.js model files to the bucket
3. Make the bucket publicly accessible or configure appropriate permissions
4. Update the `MODEL_URL` environment variable

### 2. Set up Firestore

1. Enable Firestore in your Google Cloud Project
2. Create a database in Native mode
3. Set up authentication credentials

### 3. Deploy to Cloud Run

1. Go to Google Cloud Run in the GCP Console
2. Create a new service
3. Select "Deploy one revision from an existing container image"
4. Use the image: `gcr.io/capstone-buahsayur/debusa-app:latest`
5. Configure environment variables:
   - `MODEL_URL`: Your model URL
   - `PORT`: 3000
6. Deploy the service

## 🔧 Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `MODEL_URL` | URL to the TensorFlow.js model | Required |
| `PORT` | Server port | 3000 |

### Model Requirements

- The model should be a TensorFlow.js model (model.json + weight files)
- Input shape: [416, 416, 3] (RGB images)
- Output: 10 classes corresponding to the supported fruits/vegetables

## Troubleshooting

### Common Issues

1. **Model Loading Error**
   - Verify the `MODEL_URL` is accessible
   - Check if the model files are properly formatted

2. **Image Upload Error**
   - Ensure the image is in a supported format (JPEG, PNG)
   - Check if the image size is within the 5MB limit

3. **Firestore Connection Error**
   - Verify Google Cloud credentials are properly configured
   - Check if Firestore is enabled in your GCP project

### Debug Mode

Run the server with additional logging:
```bash
DEBUG=* npm run start:dev
```
