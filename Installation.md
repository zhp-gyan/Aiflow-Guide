# Airflow-Guides
### **Important Concepts**
  - Installation & Setup
  - Core Concepts
  - Airflow Basics
  - Airflow Intermediate
  - Advanced Airflow
  - Airflow in Production
  - Advanced Topics
  - Projects

## Installation & Setup

- Local Machine Installation
- Docker Installation
- Cloud Installation

### Pre-Requisites
 - Python >=3.6
 - pip
 - virtual environment(optional but recommended)

### Local Machine Installation

- Setup Virtual Environment

  ```zsh
  python3 -m venv airflow_env
  source airflow_env/bin/activate
  ```

- Install Airflow

  ```zsh
  export AIRFLOW_HOME=~/airflow
  pip install apache-airflow
  ```

- Initialize Airflow Database

  ```zsh
  airflow db init
  ```

- Create a User

  ```zsh
  airflow users create \
      --username admin \
      --firstname FIRST_NAME \
      --lastname LAST_NAME \
      --role Admin \
      --email admin@example.com
  ```
  
- Start Airflow Scheduler and Web Server

```zsh
  airflow scheduler
  airflow webserver --port 8080
```

- Access Airflow UI
  - Open a web browser and navigate to `http://localhost:8080`


### Docker Installation

- **Prerequisites**
  - Docker
  - Docker Compose
 
**Steps**

- Create a Docker Compose File
- Create a `docker-compose.yaml` file with the following content:
 ```yaml
version: '3.7'
services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow

  redis:
    image: redis:latest

  airflow:
    image: apache/airflow:2.4.1
    environment:
      AIRFLOW__CORE__EXECUTOR: CeleryExecutor
      AIRFLOW__CORE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
      AIRFLOW__CELERY__BROKER_URL: redis://redis:6379/0
      AIRFLOW__CELERY__RESULT_BACKEND: db+postgresql://airflow:airflow@postgres/airflow
    volumes:
      - ./dags:/opt/airflow/dags
    ports:
      - "8080:8080"
    command: ["bash", "-c", "airflow db init && airflow users create --username admin --password admin --firstname Admin --lastname Admin --role Admin --email admin@example.com && airflow scheduler & airflow webserver"]

  airflow-worker:
    image: apache/airflow:2.4.1
    environment:
      AIRFLOW__CORE__EXECUTOR: CeleryExecutor
      AIRFLOW__CORE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
      AIRFLOW__CELERY__BROKER_URL: redis://redis:6379/0
      AIRFLOW__CELERY__RESULT_BACKEND: db+postgresql://airflow:airflow@postgres/airflow
    volumes:
      - ./dags:/opt/airflow/dags
    depends_on:
      - postgres
      - redis
  ```

  - Start Services
   ```zsh
    docker-compose up -d
   ```
  - Access Airflow UI
    - Open a web browser and navigate to `http://localhost:8080`

### Cloud Installation (Google Cloud Composer)

- **Prerequisites**
  - Google Cloud account
  - Google Cloud SDK installed and initialized
  - Billing account setup
 
**Steps**

- Enable Composer API
  ```bash
  gcloud services enable composer.googleapis.com
  ```
- Create a Cloud Composer Environment
  ```bash
  gcloud composer environments create my-environment \
    --location us-central1 \
    --zone us-central1-a \
    --image-version composer-2.0.0-airflow-2.1.0
  ```

- Deploy DAGs
  - Upload your DAG files to the dags folder in the Cloud Storage bucket created for your environment.

- Access Airflow UI
  - Navigate to the Airflow UI link provided in the Google Cloud Console for your Composer environment.
