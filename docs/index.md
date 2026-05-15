# 💣 Explosive Ordnance Disposal Virtual Ordnance Identification (EODVOID)

## Title and Description

EODVOID is a research and prototype system that uses computer vision to assist Explosive Ordnance Disposal (EOD) technicians by identifying suspected ordnance from still RGB images. The system is designed as a containerized inference service that returns a predicted ordnance class/type along with a confidence score, supporting reproducibility and future integration.

## Architecture Overview

![EODVOID Cloud Architecture](docs/diagrams/EODVOID_architecture_diagram.png)

---

## Problem

EOD technicians must often identify explosive hazards in high-risk environments using manual visual inspection and reference materials. This work can require close proximity to potentially hazardous items and is complicated by the large number of global ordnance variants and real-world image degradation (for example corrosion, partial burial, and poor lighting).

---

## Motivation

This project is motivated by the need to:

- Reduce time-on-target
- Lower cognitive load
- Decrease technician exposure to hazards during identification

A repeatable, automated decision-support tool may improve consistency and speed of classification while clearly communicating uncertainty via confidence scores.

---

## Related Work

This project is informed by existing work in:

- Deep-learning object detection and classification
- Synthetic data generation to address limited real-world labeled data
- Containerized inference services and cloud deployment patterns

---

## 📋 Anticipated Tasks

- Vision Document
- Requirements Document
- Design Document
- Project Plan
- Data Dictionary & Schema
- Dataset construction (synthetic + real-world)
- Model training & evaluation
- Inference API implementation
- Web UI client development
- Containerization and deployment
- Test requirements verification
- User guide and maintenance / monitoring plan

---

## ⚠️ Capacity Gaps

Capacity gaps are constraints on resources that can limit execution:

- Limited availability of labeled real-world ordnance imagery
- Dependence on synthetic data generation to expand training coverage
- Restricted compute resources for large-scale experimentation and training

---

## ⚠️ Capability Gaps

Capability gaps are limitations in what the system can reliably do today:

- Sim-to-real generalization challenges (model robustness on degraded/field imagery)
- Ordnance coverage limited to project’s defined classes/types
- Latency and connectivity constraints for time-sensitive operational settings

---

## AI2C Fit

EODVOID aligns with AI2C goals by:

- Applying AI to a real-world safety problem
- Building a reproducible ML pipeline
- Integrating data engineering, ML, and DevOps
- Delivering a deployable proof-of-concept

---

## RFIs for Stakeholders

To confirm constraints and ensure the prototype evaluates the right targets:

- Average time required for manual identification
- Acceptable latency thresholds for AI-assisted identification
- Priority ordnance types for future expansion
- Connectivity assumptions (offline/edge vs cloud-connected)
- Labeling standards and acceptable accuracy thresholds

---

## 👥 Mentor Information

- Dr. Myroslaw Hohil - myroslaw.e.hohil.civ@army.mil
- Mr. Kelly Albertson - kelly.r.albertson.civ@army.mil

---

## Customer Information

**Primary end user:**  
Explosive Ordnance Disposal Technician

**Customer / stakeholder point of contact:**
myroslaw.e.hohil.civ@army.mil

**Operational context:**  
Still images captured by Soldiers, robotic operators, or UAV operators under variable lighting, distance, and environmental conditions.

---

# 🚀 Quick Start (Recommended)

For most users, the fastest way to run EODVOID locally is with Docker Compose. All commands are run from repository root.

## 1. Clone the repository

```bash
git clone https://github.com/EODVOID-AITech-Capstone/EODVOID.git
cd EODVOID
```

## 2. Create your local environment file

```bash
cp .env.example .env
```

## 3. Configure the model path

Place your trained YOLO checkpoint under:

```text
ml/models/checkpoints/
```

Then update `EODVOID_MODEL_PATH` in `.env` so it matches the container path, for example:

```env
EODVOID_MODEL_PATH=/app/ml/models/checkpoints/best.pt
```

## 4. Start the application

```bash
docker compose up --build
```

Once startup completes, the services will be available at:

- Web UI: `http://localhost:9002`
- API: `http://localhost:8000`
- Inference: `http://localhost:8001`

## 5. Stop the application

```bash
docker compose down
```

> Note: The application can start without a model file, but inference requests will fail until the configured checkpoint exists.

---

# ⚙️ Installation Instructions

These instructions describe how to clone the repository and prepare the system for local development, containerization, and Kubernetes deployment. They are intended for developers and contributors.

## Repository Layout

- `apps/web/` - Next.js frontend
- `apps/api/` - Python API service and backend requirements
- `apps/inference/` - Python inference service and backend requirements
- `ml/` - training workflows plus datasets, model artifacts, and reports
- `scripts/`, `deploy/`, `configs/`, `docs/` - utilities, infrastructure, and documentation


## 1. Clone repository

```bash
git clone https://github.com/EODVOID-AITech-Capstone/EODVOID.git
cd EODVOID
```

---

## 2. System Requirements

### Overview

The EODVOID system is a containerized application composed of:
- Frontend web interface (Next.js)
- API service (FastAPI)
- Inference service (YOLO-based detection)

All components are orchestrated using Docker and Docker Compose.

---

### Minimum Requirements (CPU-only)

These requirements are sufficient to run the system for demonstration and basic usage.

- **Operating System**
  - Linux (Ubuntu 20.04+ recommended)
  - macOS (Intel or Apple Silicon)
  - Windows (via WSL2 recommended)

- **Hardware**
  - CPU: 4+ cores
  - RAM: 8 GB minimum (16 GB recommended)
  - Disk Space:
    - Application: ~5–10 GB
    - Dataset (optional): 20–50+ GB depending on usage

---

### Recommended Requirements (Improved Performance)

- **Hardware**
  - CPU: 8+ cores
  - RAM: 16–32 GB
  - GPU (optional but recommended):
    - NVIDIA GPU with 6–12 GB VRAM

- **Disk**
  - SSD strongly recommended

---

### GPU Acceleration (Optional)

The inference service can leverage GPU acceleration if available.

Requirements:
- NVIDIA GPU
- NVIDIA drivers installed
- NVIDIA Container Toolkit configured for Docker

> Note: The system runs fully on CPU, but inference latency will be significantly higher.

---

### Network & Ports

The application uses the following default ports:

- Frontend (web UI): `9002`
- API service: `8000`
- Inference service: `8001`

Ensure these ports are available on the host machine.

Service URL expectations:

- API -> inference traffic should use the internal Compose network URL:
  - `EODVOID_INFERENCE_URL=http://inference:8001/detect`
- Browser -> API traffic should use a host-reachable URL:
  - `NEXT_PUBLIC_API_URL=http://localhost:8000` for same-machine local use
  - `NEXT_PUBLIC_API_URL=http://<host-or-ip>:8000` for remote/shared access

---

### Performance Expectations

- **CPU-only execution**
  - Inference may take several seconds per image
  - Suitable for testing and demonstration

- **GPU-enabled execution**
  - Significantly faster inference (near real-time depending on hardware)

---

### Dataset Considerations

- The full dataset is **not required** to run the application
- A small sample dataset is sufficient for testing
- Full dataset (50k+ images) requires significant storage

---


## 3. Prerequisites

### Required for container deployment (recommended)

- Docker Engine (v20.x+ recommended)
- Docker Compose (v2+)

> This is the primary and recommended way to run the system.

---

### Optional: Kubernetes Deployment

If deploying to a Kubernetes environment:

- kubectl (compatible with your cluster version)
- Kubernetes cluster (e.g., AKS, kind, minikube)

> Note: Kubernetes deployment is optional and requires additional configuration beyond the default setup.

---

### Required for local development (non-containerized)

- Python 3.10
- pip / virtualenv

---

### Optional but recommended

- make (for simplified commands)
- curl (for API testing)

---

## 4. Python environment (for local development only)
Note: Optional if running everything via Docker
Note: Torch dependencies are installed separately due to hardware-specific builds (CPU vs CUDA).

Linux / Mac:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip

# API dependencies
pip install -r apps/api/requirements.txt

# Inference service dependencies
pip install -r apps/inference/requirements-inference-cpu.txt
pip install -r apps/inference/requirements.txt

```

Windows:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r apps/api/requirements.txt
pip install -r apps/inference/requirements-inference-cpu.txt
pip install -r apps/inference/requirements.txt
```

For GPU-based training environments, install:

```bash
pip install -r apps/inference/requirements-training-gpu.txt
```

---

## 5. Configure environment variables
Create a local .env file, export variables in your shell, or configure environment variables via Kubernetes.

Example:

```env
EODVOID_MODEL_PATH=ml/models/checkpoints/best.pt
EODVOID_CONFIDENCE_THRESHOLD=0.05
EODVOID_API_HOST=0.0.0.0
EODVOID_API_PORT=8000
EODVOID_INFERENCE_HOST=0.0.0.0
EODVOID_INFERENCE_PORT=8001
EODVOID_INFERENCE_URL=http://inference:8001/detect
```

For local non-container development, set:

```env
EODVOID_INFERENCE_URL=http://127.0.0.1:8001/detect
```

---

### Model Requirement

The inference service requires a trained YOLO model checkpoint.

Place your model file in the repository at:

ml/models/checkpoints/<your_model>.pt

Use the path that matches how you are running the service:

- Local non-container development:
  - `EODVOID_MODEL_PATH=ml/models/checkpoints/<your_model>.pt`
- Docker / containerized runtime:
  - `EODVOID_MODEL_PATH=/app/ml/models/checkpoints/<your_model>.pt`

If the model is not present, inference requests will fail.

---

## [Optional]: Run the API locally [Local Non-Kubernetes Quick Start (Development Only)] 
This local, non-containerized quick start is provided for development convenience only and is not representative of the intended deployment architecture.

This project consists of multiple services that must be run together.

```bash
cd apps/inference
set -a && source ../../.env
uvicorn main:app --host "${EODVOID_INFERENCE_HOST:-0.0.0.0}" --port "${EODVOID_INFERENCE_PORT:-8001}"

cd ../api
set -a && source ../../.env
uvicorn api.main:create_application --factory --host "${EODVOID_API_HOST:-0.0.0.0}" --port "${EODVOID_API_PORT:-8000}"
```

## [Optional]: Run the web UI locally

```bash
cd apps/web
npm install
npm run dev
```

## 6. Build Docker images
Expected workflow from the repository root:

```bash
# This commands builds API, Inference, and Web Services
docker compose up --build

# This command runs the containers without executing a build
docker compose up

# This command shuts down your containers
docker compose down
```

Note: When deploying to a remote Kubernetes cluster (e.g., AKS), container images must be pushed to a container registry accessible by the cluster (such as Azure Container Registry) before applying the Kubernetes manifests.

---

## 7. Cloud Deployment (AKS)

The current cloud deployment path uses:

- Azure Resource Group
- Azure Container Registry (ACR)
- Azure Kubernetes Service (AKS)
- NGINX ingress controller
- cert-manager + Let’s Encrypt for TLS

### Prerequisites

- Terraform
- Azure CLI
- kubectl
- Docker
- a real DNS record pointed at the ingress public IP

### 1. Provision Azure infrastructure

```bash
cd deploy/terraform
cp terraform.tfvars.example terraform.tfvars
```

Update `terraform.tfvars` with your real Azure values, then run:

```bash
terraform init
terraform plan
terraform apply
```

### 2. Connect kubectl to the cluster

```bash
az aks get-credentials --resource-group <resource-group> --name <cluster-name>
```

### 3. Build and push container images

Log in to ACR:

```bash
az acr login --name <acr-name>
```

Build and push the images:

```bash
docker build -f deploy/docker/Dockerfile.web -t <acr-login-server>/eodvoid-web:latest .
docker build -f deploy/docker/Dockerfile.inference -t <acr-login-server>/eodvoid-inference:latest .

docker push <acr-login-server>/eodvoid-web:latest
docker push <acr-login-server>/eodvoid-inference:latest
```

### 4. Update Kubernetes manifests

Before applying workloads:

- replace placeholder image references in:
  - `deploy/k8s/frontend.yaml`
  - `deploy/k8s/inference.yaml`
- replace `your-email@example.com` in `deploy/k8s/cluster-issuer.yaml`
- confirm the configured model path in `deploy/k8s/inference.yaml`
- ensure the model file exists in the mounted volume at the same path

Model storage note:

- the inference deployment mounts `eodvoid-model-pvc` at `/app/ml/models/checkpoints`
- that PVC is intended to be backed by Azure Files via the `azurefile-csi` storage class
- the model checkpoint must be uploaded into that backing file share so the pod can see it at the exact `EODVOID_MODEL_PATH` configured in `deploy/k8s/inference.yaml`
- a bound PVC alone is not enough; the checkpoint file itself must be present in the share

### 5. Install cert-manager

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/latest/download/cert-manager.yaml
```

Wait for cert-manager pods to become ready before continuing.

### 6. Apply the Kubernetes manifests

```bash
kubectl apply -f deploy/k8s/namespace.yaml
kubectl apply -f deploy/k8s/inference.yaml
kubectl apply -f deploy/k8s/inference-hpa.yaml
kubectl apply -f deploy/k8s/frontend.yaml
kubectl apply -f deploy/k8s/cluster-issuer.yaml
kubectl apply -f deploy/k8s/ingress.yaml
```

### 7. Verify the deployment

```bash
kubectl get pods -n eodvoid
kubectl get svc -n eodvoid
kubectl get ingress -n eodvoid
kubectl get hpa -n eodvoid
kubectl get certificate -n eodvoid
```

If DNS and TLS are configured correctly, the application should be reachable at your ingress hostname, for example:

```text
https://eodvoid.app
```

---

# 🧪 Testing Instructions

## Model Evaluation Testing (ML-Level)

Testing phases:

1. Clean synthetic imagery
2. Environmental synthetic imagery
3. Real-world clean imagery
4. Real-world degraded imagery

Model performance is evaluated using held-out datasets and standard object-detection metrics, including:

- mAP@0.5
- mAP@0.5:0.95
- Precision
- Recall

Evaluation is performed during training and validation phases using the Ultralytics YOLO framework. Detailed evaluation scripts and reports will be added as the project matures.

Example:

```bash
# Train (runs validation each epoch automatically)
yolo detect train data=ml/data/datasets/synth_clean/data.yaml model=yolo26s.pt epochs=50 imgsz=640

# Final evaluation on held-out test split
yolo detect val data=ml/data/datasets/synth_clean/data.yaml model=runs/detect/train/weights/best.pt split=test
```

---

## Software Testing (Code-Level)

Automated checks currently include:

- backend linting with `ruff`
- backend format checking with `black --check`
- backend syntax checks with `python -m compileall`
- backend tests with `pytest -q tests`
- frontend production build
- Docker image build checks for API, inference, and web

Run the backend test suite locally:

```bash
pytest -q tests
```

Manual API smoke checks:

```bash
# Health check
curl http://localhost:8000/health

# Sample prediction request:
curl -X POST "http://localhost:8000/detect" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@path/to/example.jpg"
```

### Load Testing With Locust

Install Locust in your active Python environment:

```bash
pip install locust
```

The repository includes a simple Locust test at `tests/locust/locustfile.py`. It expects a test image named `sample.jpg` in the same directory.

Run it like this:

```bash
cd tests/locust
locust -f locustfile.py -H https://eodvoid.app
```

Then open the Locust UI at:

```text
http://localhost:8089
```

Recommended starting point:

- Users: `5`
- Spawn rate: `1`

As you increase load, monitor the cluster with:

```bash
kubectl get pods -n eodvoid
kubectl top pods -n eodvoid
kubectl logs -n eodvoid deploy/eodvoid-inference --tail=100
```

---

# 🚀 Usage Example
These instructions describe how to use the system after it has been installed and deployed. Once the inference API is implemented, you will be able to submit an image and receive a structured JSON response.

Note: The Web UI is optional and intended for demonstration and non-technical users. All inference logic resides in the backend service. This deployment model allows integration with existing laptops or tablets using a standard web browser.

## Option A — Access via ingress (recommended cloud usage)

After DNS, ingress, and TLS are configured, open the application in a browser at your ingress hostname, for example:

```text
https://eodvoid.app
```

The web UI is served at `/`, and the frontend sends inference requests to `/detect` through the ingress.

---

## Option B — Access via Kubernetes port-forwarding (local cluster debugging)

Expose inference directly:

```bash
kubectl port-forward -n eodvoid svc/eodvoid-inference 8001:8001
```

Expose the frontend directly:

```bash
kubectl port-forward -n eodvoid svc/eodvoid-frontend 9002:80
```

Then open:

```text
http://localhost:9002
```

## Option C — Direct API usage (without Web UI)

Send an image to the inference service:

```bash
curl -X POST "https://eodvoid.app/detect" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@path/to/example.jpg"
```

Expected response format (example)

```json
{
  "predicted_class": "fuze",
  "predicted_type": "AR-30",
  "confidence": 0.87,
  "detections": [
    {
      "label": "fuze_ar30",
      "confidence": 0.87,
      "bbox": [120,45,310,260]
    }
  ]
}
```

---

## DoD-Constrained Environment Considerations

The local development workflow has been validated using Docker Compose in a standard development environment. Running the system in a DoD-managed or otherwise restricted environment may require additional approvals or configuration.

Potential constraints include:

- Approval to run Docker or another container runtime
- Approval to bind local service ports used by the web, API, and inference services
- Access to GitHub or an internal mirrored repository
- Access to Azure Blob Storage for downloading datasets, checkpoints, and model results
- Access to public package registries such as PyPI and npm, or use of internal mirrors
- Enterprise TLS/certificate configuration for Git, Python, Node, Docker, and AzCopy
- Antivirus/EDR review of local services, scripts, and model checkpoint files
- Data handling restrictions for imagery, annotations, and model artifacts

If public internet access is restricted, future maintainers may need to use:

- an internal Git repository mirror
- prebuilt/scanned container images
- offline Python wheel and npm dependency bundles
- an approved artifact transfer mechanism
- internally hosted model checkpoints and datasets

---

# 📜 License

This project is developed for academic and research purposes. Licensing details will be added before public release.

---
