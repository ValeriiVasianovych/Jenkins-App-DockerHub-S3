# Flask Application CI/CD Pipeline

This repository contains a CI/CD pipeline for a Flask application. The pipeline is configured using Jenkinsfile and utilizes Docker for building and testing the application, and AWS S3 for storing artifacts.

## Pipeline Overview

The pipeline consists of the following stages:

1. **Cleanup Workspace**: Cleans workspace before starting the build.
2. **Checkout**: Checks out the source code from the repository.
3. **Build Docker Image**: Builds a Docker image for the Flask application.
4. **Test Docker Image**: Tests the Docker image by running it and performing a simple HTTP request to ensure it returns a 200 status code.
5. **Login to Docker Hub**: Logs into Docker Hub using provided credentials.
6. **Push Docker Image**: Pushes the built Docker image to Docker Hub.
7. **Create Archive**: Archives the source code and Dockerfile.
8. **Upload to S3**: Uploads the archive to AWS S3 bucket.

## Prerequisites

Before running the pipeline, ensure the following prerequisites are met:

- Jenkins is set up with necessary plugins for pipeline execution.
- Docker is installed on the Jenkins server.
- AWS CLI is installed on the Jenkins server.
- Docker Hub and AWS S3 credentials are configured in Jenkins credentials store.

## Usage

1. Create a Jenkins pipeline job and point it to this repository.
2. Configure the pipeline job to use the Jenkinsfile provided.
3. Configure necessary Jenkins global credentials for Docker Hub and AWS S3.
4. Run the pipeline job.

## Repository Structure

```
.
├── app.py
├── Dockerfile
├── Jenkinsfile
├── README.md
├── requirements.txt
└── templates
    └── index.html
```

- `app.py`: Main Flask application code.
- `Dockerfile`: Dockerfile for building the Docker image.
- `Jenkinsfile`: Jenkins pipeline script defining the CI/CD stages.
- `README.md`: This file providing an overview of the project.
- `requirements.txt`: List of Python dependencies.
- `templates/index.html`: HTML template for the Flask application.