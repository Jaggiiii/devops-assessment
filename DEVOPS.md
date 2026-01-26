# 🚀 Full-Stack DevOps Automation Project

## Project Overview
This repository demonstrates a complete end-to-end DevOps workflow for a full-stack application using industry-standard practices. The project transitions a local development setup into a fully automated, cloud-hosted production environment.

**The Application:**
* **Frontend:** React (Vite)
* **Backend:** Django REST API

**The Goal:**
To containerize the application, automate build and deployment via CI/CD, deploy to AWS EC2, and provision infrastructure using Terraform.

---

## 🛠 Tech Stack

| Component | Technology | Description |
| :--- | :--- | :--- |
| **Frontend** | React + Vite | Fast, modern frontend framework. |
| **Backend** | Django (REST API) | Python-based backend logic. |
| **Containerization** | Docker | Multi-stage builds, non-root user security. |
| **Orchestration** | Docker Compose | Managing multi-container environments. |
| **CI/CD** | GitHub Actions | Automated testing and deployment. |
| **Cloud Provider** | AWS EC2 | Virtual server hosting. |
| **IaC** | Terraform | Infrastructure provisioning as code. |

---

## 📦 Phase 1: Containerization & Orchestration

### What Was Done
* **Custom Dockerfiles:** Created specialized Dockerfiles for both Frontend and Backend, tailored specifically to meet their respective dependency and runtime requirements.
* **Optimization:** Used **multi-stage builds** to minimize image size.
* **Security:** Configured containers to run as **non-root users**.
* **Orchestration:** Configured `docker-compose.yml` to bundle services.
* **Networking:** Backend is **isolated**; Frontend communicates via an internal Docker network (Nginx Reverse Proxy).



### 💻 Running Locally (No Cloud/Terraform)

**Prerequisites:**
You need to install the following tools. Please refer to their official documentation for installation instructions:
* **Git:** [Official Docs](https://git-scm.com/doc)
* **Docker:** [Official Docs](https://docs.docker.com/get-docker/)
* **Docker Compose:** [Official Docs](https://docs.docker.com/compose/install/)

**Installation Steps:**

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/Jaggiiii/devops-assessment](https://github.com/Jaggiiii/devops-assessment)
    cd devops-assessment
    ```

2.  **Start the application:**
    ```bash
    docker-compose up --build
    ```
    *(Note: The `--build` flag ensures that Docker compiles the images from the Dockerfiles in `frontend/` and `backend/` before starting, so you don't need to build them manually.)*

3.  **Access the application:**
    * **Frontend:** `http://localhost:8080`
    * **Backend:** Not exposed directly (Internal traffic only).

---

## 🔄 Phase 2: CI/CD Pipeline

### Automation Strategy
We utilize **GitHub Actions** to trigger a workflow on every push to the `main` branch.



### The Workflow
1.  **Trigger:** Code pushed to `main`.
2.  **Build:** GitHub Actions builds Docker images for Frontend and Backend.
3.  **Registry:** Images are pushed to **Docker Hub**.
4.  **Deploy:** GitHub Actions connects to AWS EC2 via SSH.
5.  **Update:** EC2 pulls the latest images and restarts containers using Docker Compose.

### GitHub Secrets Configuration
The following secrets must be configured in the repository settings:

* `DOCKERHUB_USERNAME`
* `DOCKERHUB_TOKEN`
* `SERVER_HOST` (EC2 Public IP)
* `SERVER_USER` (usually `ubuntu` or `ec2-user`)
* `SERVER_SSH_KEY` (Private PEM key content)
* `DEPLOY_PATH`

---

## ☁️ Deployment Strategy

### Option A: Manual Deployment (Without Terraform)
Initially, the deployment was tested manually to understand the underlying requirements.
1.  **Launch EC2:** Manually launched an instance via AWS Console.
2.  **Security Group:** Configured to allow SSH (22) and HTTP (80).
3.  **Setup:** Connected via SSH. You must install **Docker** and **Docker Compose** on the server (refer to official docs).
4.  **Deploy:** Pulled images and ran `docker-compose up`.

### Option B: Infrastructure as Code (Terraform)
This is the preferred, automated method used in the final phase.

**Tools Used:**
You need to install the following tools locally. Please refer to their official documentation for specific instructions:
* **Terraform:** [Official Docs](https://developer.hashicorp.com/terraform/install)
* **AWS CLI:** [Official Docs](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

**Provisioning Steps:**

1.  **Initialize Terraform:**
    ```bash
    cd terraform/
    terraform init
    ```

2.  **Review the Plan:**
    ```bash
    terraform plan
    ```

3.  **Apply Infrastructure:**
    ```bash
    terraform apply
    ```

**Post-Provisioning (One-Time Setup):**
After Terraform creates the instance, you simply SSH in, install Docker (via `yum` or `apt` as per official docs), and the CI/CD pipeline takes over for all future updates.

---

## 🔒 Security Architecture

* **Port Isolation:** The Backend API (Port 8000) is **blocked** from the public internet via AWS Security Groups.
* **Reverse Proxy:** Nginx acts as the single entry point (Port 80), routing `/api` requests internally to the backend.
* **Credential Safety:** No sensitive keys are hardcoded; all secrets are managed via GitHub Secrets.
* **Dynamic Addressing:** The frontend does not rely on hardcoded Backend IPs; it uses relative paths handled by Nginx.

---

## 🔧 Troubleshooting Log

| Issue | Cause | Solution |
| :--- | :--- | :--- |
| **Django DisallowedHost** | EC2 IP missing in settings. | Added EC2 Public IP/Hostname to `ALLOWED_HOSTS`. |
| **Frontend Connection Fail** | Backend IP hardcoded in React. | Switched to relative paths & Nginx proxy pass. |
| **Git Push Failure** | Large binary files committed. | Updated `.gitignore` to exclude `.terraform` & binaries. |
| **Backend Exposed** | Port 8000 open in Security Group. | Removed inbound rule for 8000; strictly internal now. |

---

##  Repository Structure

```text
├── backend/                # Django Application
├── frontend/               # React Application
├── terraform/              # IaC Configuration Files
├── .github/workflows/      # CI/CD Pipeline (main.yml)
├── docker-compose.yml      # Orchestration
├── DEVOPS.md               # Documentation
└── README.md               # General Overview
```

##  Final Summary

* **Phase 1 Completed:** Dockerization and orchestration logic established.
* **Phase 2 Completed:** Fully automated CI/CD pipeline deploying to AWS.
* **Phase 3 Completed:** Infrastructure provisioned automatically using Terraform.
* **Security:** Secure architecture implemented with no public backend exposure.
* **Result:** A fully automated, scalable, and production-ready setup.

---
# Maintained by: D Jagadeeswar Rao**
