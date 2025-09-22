# Project Working Screenshots

Here are some working screenshots of the project:

## Screenshots

### Screenshot 1
![Screenshot 1](working-ss/1.png)


## Overview

MONITOR-LOGS-MICROSERVICE is a microservice designed to collect, store, and visualize user activity logs from various services in a cloud-native environment. It provides RESTful APIs for logging actions and a web interface for viewing logs. The service is built with Flask, uses MySQL for storage, and is fully containerized and orchestrated with Kubernetes.

---

## Features
- Centralized logging of user actions from multiple services
- REST API for log ingestion
- Web UI for log visualization
- MySQL-backed persistent storage
- Kubernetes manifests for deployment, scaling, and security
- Dockerized for easy local and cloud deployment

---

## Architecture & Workflow

```
          +-------------------+
          |  Client Services  |
          | (flask-app, etc.) |
          +--------+----------+
                   |
                   | POST /monitor/log
                   v
          +-----------------------+
          |  Flask Monitor App    |
          |  (REST API & Web UI)  |
          +----------+------------+
                     |
                     | SQL (ORM)
                     v
          +-----------------------+
          |      MySQL DB         |
          +-----------------------+
```

### Sequence (UML) Diagram (PlantUML)
```plantuml
@startuml
actor Client
participant "Flask Monitor API" as API
database MySQL

Client -> API: POST /monitor/log (user action)
API -> MySQL: INSERT user_actions
Client -> API: GET /monitor (view logs)
API -> MySQL: SELECT user_actions
API -> Client: Rendered HTML table
@enduml
```

---

## API Endpoints

### 1. Log User Action
- **POST** `/monitor/log`
  - **Body (JSON):**
    - `user_id` (string)
    - `service` (string)
    - `endpoint` (string)
    - `action_type` (string)
    - `request_data` (object)
    - `response_summary` (string)
    - `ip_address` (string)
    - `user_agent` (string)
  - **Response:** `{ "success": true }`

### 2. View Logs (Web UI)
- **GET** `/monitor`
  - Renders an HTML table of the latest 200 user actions.

---

## Database Schema

Table: `user_actions`

| id | timestamp | user_id | service | endpoint | action_type | request_data | response_summary | ip_address | user_agent |
|----|-----------|---------|---------|----------|-------------|--------------|------------------|------------|------------|

---

## Running Locally (Docker)

1. **Clone the repository:**
   ```bash
   git clone <repo-url>
   cd MONITOR-LOGS-MICROSERVICE
   ```
2. **Build the Docker image:**
   ```bash
   cd backend/database
   docker build -t flask-monitor:latest .
   ```
3. **Run MySQL (example):**
   ```bash
   docker run --name mysql -e MYSQL_ROOT_PASSWORD=rootpass -e MYSQL_DATABASE=ashdb -p 3306:3306 -d mysql:8.1
   ```
4. **Run the Flask Monitor app:**
   ```bash
   docker run --name flask-monitor --link mysql:mysql -e MYSQL_URI="mysql+pymysql://root:rootpass@mysql:3306/ashdb" -p 6000:6000 flask-monitor:latest
   ```
5. **Access the Web UI:**
   - Open [http://localhost:6000/monitor](http://localhost:6000/monitor)

---

## Kubernetes Deployment

1. **Set up Kubernetes cluster and context**
2. **Apply secrets:**
   ```bash
   kubectl apply -f backend/k8s/secrets/
   ```
3. **Deploy MySQL StatefulSet:**
   ```bash
   kubectl apply -f backend/k8s/statefulset/
   ```
4. **Deploy Flask Monitor:**
   ```bash
   kubectl apply -f backend/k8s/deployment/
   kubectl apply -f backend/k8s/services/
   ```
5. **Apply network policies, HPA, and RBAC:**
   ```bash
   kubectl apply -f backend/k8s/networkpolicies/
   kubectl apply -f backend/k8s/hpa/
   kubectl apply -f backend/k8s/rbac/ --recursive
   ```
6. **Access the service:**
   - Use `kubectl port-forward svc/flask-monitor 6000:6000 -n ashapp` and open [http://localhost:6000/monitor](http://localhost:6000/monitor)

---

## Requirements

- Python 3.12 (for Docker build)
- Flask, Flask-SQLAlchemy, PyMySQL
- MySQL 8.x
- Docker
- Kubernetes (v1.21+ recommended)

---





