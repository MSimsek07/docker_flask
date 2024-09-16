
# MLOps Tutorial: Automating CI/CD Workflow for Dockerized Flask App using GitHub Actions

This project demonstrates how to set up an automated CI/CD pipeline for a simple Flask application using **Docker**, **GitHub Actions**, and **PyTest** for unit testing. The project will build, test, and deploy a Docker image of the Flask app to **Docker Hub** in an automated way, ensuring continuous integration and continuous deployment (CI/CD) best practices.

## Table of Contents
- [Project Overview](#project-overview)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Setup Guide](#setup-guide)
- [CI/CD Workflow](#cicd-workflow)
- [How to Run](#how-to-run)
- [Contact](#contact)

## Project Overview

In this project, we will:
1. Create a simple Flask application.
2. Write unit test cases for the Flask app using PyTest.
3. Build a Docker image of the Flask app.
4. Push the Docker image to Docker Hub.
5. Automate the entire process using **GitHub Actions**.

## Prerequisites

To run this project locally, you'll need:
- [Git](https://git-scm.com/)
- [Python](https://www.python.org/downloads/)
- [Docker](https://www.docker.com/products/docker-desktop)
- [GitHub Account](https://github.com/) for GitHub Actions and Docker Hub.
- [Docker Hub Account](https://hub.docker.com/)

Ensure you have the following installed:
- **Python 3.9 or later**
- **Flask** and **PyTest** packages
- **Docker**

### Tools Used:
- **GitHub** - For version control and CI/CD automation with GitHub Actions.
- **Docker** - To containerize the Flask application.
- **PyTest** - For writing unit tests.
- **GitHub Actions** - For setting up an automated CI/CD pipeline.

## Project Structure

```bash
.
├── app.py                # Flask app definition
├── requirements.txt      # Python dependencies
├── test_app.py           # Unit tests for Flask app using PyTest
├── Dockerfile            # Docker configuration file
├── .dockerignore         # Files to be ignored in Docker build
├── .github
│   └── workflows
│       └── ci_cd.yaml    # GitHub Actions workflow file for CI/CD
└── README.md             # Project documentation
```

## Setup Guide

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/MSimsek07/docker_flask.git
   cd docker_flask
   ```

2. **Install Dependencies**:
   Ensure you have a Python virtual environment set up:
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # For Linux/Mac
   # or
   venv\Scriptsctivate  # For Windows
   ```

   Then install the dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. **Run the Flask App Locally**:
   ```bash
   python app.py
   ```

   Visit `http://localhost:5000` to view the running app.

4. **Run Unit Tests**:
   ```bash
   pytest
   ```

## CI/CD Workflow

### GitHub Actions Automation

The CI/CD process is handled by a **GitHub Actions** workflow (`.github/workflows/ci_cd.yaml`). It automatically builds, tests, and pushes the Docker image to Docker Hub on every code commit to the main branch.

### Steps in the CI/CD Pipeline:

1. **Build and Test**:
   - GitHub Actions runs the Flask app unit tests using **PyTest**.
   - If all tests pass, the Docker image is built.

2. **Docker Image Build**:
   - A Docker image for the Flask app is built based on the **Dockerfile**.
   - The image is tagged with the app name and the current Git commit SHA.

3. **Publish to Docker Hub**:
   - The Docker image is pushed to Docker Hub using stored secrets (`DOCKER_USERNAME` and `DOCKER_PASSWORD`).

### Secrets for Docker Hub Authentication

To push the Docker image to Docker Hub, you need to set up secrets in your GitHub repository:
1. Go to your repository settings.
2. Navigate to **Secrets and variables** > **Actions**.
3. Add two secrets:
   - `DOCKER_USERNAME`: Your Docker Hub username.
   - `DOCKER_PASSWORD`: A Docker Hub access token (generated from your Docker Hub account).

### Example CI/CD Workflow File (`ci_cd.yaml`)

```yaml
name: Build, Test, and Deploy to Docker Hub

on:
  push:
    branches:
      - main

jobs:
  build-test-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up Python 3.9
      uses: actions/setup-python@v2
      with:
        python-version: '3.9'

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: Run unit tests
      run: |
        pytest

    - name: Log in to Docker Hub
      run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

    - name: Build and tag Docker image
      run: docker build . -t ${{ secrets.DOCKER_USERNAME }}/flask-app:${{ github.sha }}

    - name: Push Docker image to Docker Hub
      run: docker push ${{ secrets.DOCKER_USERNAME }}/flask-app:${{ github.sha }}
```

## How to Run

1. **Clone the Project**: 
   Follow the setup steps above to clone and run the project locally.
   
2. **Push Code Changes**: 
   Push code changes to the main branch to trigger the CI/CD pipeline.

3. **Monitor GitHub Actions**:
   - Visit the **Actions** tab in your GitHub repository to monitor the build process.
   - Check Docker Hub for the new image after a successful build.

4. **Run the Docker Image**:
   Once the image is pushed to Docker Hub, you can pull and run it locally:
   ```bash
   docker pull <DOCKER_USERNAME>/flask-app:<GIT_COMMIT_SHA>
   docker run -p 5000:5000 <DOCKER_USERNAME>/flask-app:<GIT_COMMIT_SHA>
   ```

## Contact

For any queries or issues, please reach out to the repository owner at [masimsek.official@gmail.com](mailto:masimsek.official@gmail.com).
