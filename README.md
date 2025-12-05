

# My React App

## Project Overview

This project is a **React + Vite** application that has been fully containerized using **Docker** and deployed on **Railway**.
It uses **GitHub Actions** to implement a **CI/CD pipeline**, which ensures that every push to the `main` branch automatically builds, pushes a new Docker image, and redeploys the updated app on Railway.

---

## Features

* React with Vite frontend
* Dockerized for containerized deployment
* Continuous deployment with GitHub Actions
* Automatic updates: any GitHub push updates the live app
* Deployment via Docker Hub images

---

## Tech Stack

* **Frontend:** React 18, Vite
* **Containerization:** Docker
* **CI/CD:** GitHub Actions
* **Hosting:** Railway
* **Docker Hub Repository:** [hajrarizwan/my-react-app](https://hub.docker.com/repository/docker/hajrarizwan/my-react-app/general)
* **Live Deployment:** [Railway Project](https://railway.com/project/f55d2548-62aa-44e0-8fcb-736adf781f5f/service/8b51e576-8ea0-49e6-9ba1-c3ad4be68a35?environmentId=2330b688-cdc8-487a-a70f-fd20b946ee0d)

---

## Team Members

* Hajra Rizwan 
* Mahnoor Asif
* Azka Humayon
* Aleeha Akhlaq

---

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd my-react-app
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Run Locally

```bash
npm run dev
```

Open your browser at `http://localhost:5173` (or the URL printed in the terminal).

---

## Dockerization

### 1. Build Docker Image Locally

```bash
docker build -t my-react-app:local .
```

### 2. Run Local Docker Container

```bash
docker run -p 8080:8080 my-react-app:local
```

Open `http://localhost:8080` to see the app running inside Docker.

### 3. Push Image to Docker Hub

```bash
docker login
docker tag my-react-app:local hajrarizwan/my-react-app:latest
docker push hajrarizwan/my-react-app:latest
```

Now the image is available in Docker Hub: [Docker Hub Repository](https://hub.docker.com/repository/docker/hajrarizwan/my-react-app/general)

---

## Railway Deployment

### 1. Deploy from Docker Hub

* Go to your **Railway project**
* Select **Deploy from Docker Hub**
* Enter your image: `hajrarizwan/my-react-app:latest`
* Set **PORT=80** if needed
* Deploy the service

### 2. Deployment Workflow

* Railway pulls the Docker image and runs it as a live service
* Any changes require updating the Docker image in Docker Hub

---

## CI/CD with GitHub Actions

This workflow ensures that **every push to the `main` branch** automatically:

1. Builds the React app
2. Builds a Docker image with a **unique tag** (GitHub run number)
3. Pushes the Docker image to Docker Hub
4. Triggers Railway to deploy the new image

### GitHub Actions Secrets Required

| Secret Name          | Value                          |
| -------------------- | ------------------------------ |
| `DOCKER_USERNAME`    | Docker Hub username            |
| `DOCKER_PASSWORD`    | Docker Hub access token        |
| `RAILWAY_API_KEY`    | Railway API key from dashboard |
| `RAILWAY_PROJECT_ID` | Your Railway project ID        |

### Example Workflow File (`.github/workflows/deploy.yml`)

```yaml
name: Deploy to Railway (Auto Update)

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Node
        uses: actions/setup-node@v4
        with:
          node-version: 22.12.0

      - name: Install dependencies
        run: npm ci

      - name: Build project
        run: npm run build

      - name: Set image tag
        id: vars
        run: echo "TAG=${{ github.run_number }}" >> $GITHUB_ENV

      - name: Build Docker image
        run: docker build -t my-react-app:${{ env.TAG }} .

      - name: Log in to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Push Docker image
        run: |
          docker tag my-react-app:${{ env.TAG }} ${{ secrets.DOCKER_USERNAME }}/my-react-app:${{ env.TAG }}
          docker push ${{ secrets.DOCKER_USERNAME }}/my-react-app:${{ env.TAG }}

      - name: Update latest tag
        run: |
          docker tag my-react-app:${{ env.TAG }} ${{ secrets.DOCKER_USERNAME }}/my-react-app:latest
          docker push ${{ secrets.DOCKER_USERNAME }}/my-react-app:latest

      - name: Trigger Railway deployment
        run: |
          curl -X POST https://backboard.railway.app/webhook/deploy/${{ secrets.RAILWAY_PROJECT_ID }} \
          -H "Authorization: Bearer ${{ secrets.RAILWAY_API_KEY }}" \
          -H "Content-Type: application/json" \
          -d '{"image": "${{ secrets.DOCKER_USERNAME }}/my-react-app:'${{ env.TAG }}'"}'
```

---

## How Everything Works Together

1. **Local Development:** You can run the app locally using `npm run dev` or Docker for testing.
2. **Docker Hub Image:** After testing, you push your Docker image to Docker Hub (`latest` or unique tags).
3. **Railway Deployment:** Railway pulls the Docker Hub image and serves it as a live app.
4. **CI/CD Automation:** GitHub Actions automates the process: build → Docker → push → Railway deploy.
5. **Continuous Updates:** Every GitHub push to `main` automatically triggers a new build and deployment.

---

## Links

* [Docker Hub](https://hub.docker.com/repository/docker/hajrarizwan/my-react-app/general)
* [Railway Deployment](https://railway.com/project/f55d2548-62aa-44e0-8fcb-736adf781f5f/service/8b51e576-8ea0-49e6-9ba1-c3ad4be68a35?environmentId=2330b688-cdc8-487a-a70f-fd20b946ee0d)

