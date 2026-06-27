# k8s-advanced-demo

A hands-on Kubernetes project demonstrating Helm, Ingress, and Horizontal Pod Autoscaler (HPA) on a local Minikube cluster with GitHub Actions CI/CD.

---

## 🇬🇧 English

### Overview

This project deploys a simple Flask REST API to a local Kubernetes cluster using industry-standard tooling:

- **Helm** — package management for Kubernetes manifests
- **NGINX Ingress** — external traffic routing
- **HPA** — automatic pod scaling based on CPU usage
- **GitHub Actions** — CI/CD pipeline that builds and pushes Docker images to Docker Hub

### Tech Stack

| Tool | Purpose |
|------|---------|
| Python / Flask | REST API |
| Docker | Containerization |
| Kubernetes / Minikube | Container orchestration |
| Helm | Kubernetes package manager |
| NGINX Ingress Controller | External routing |
| HPA + Metrics Server | Autoscaling |
| GitHub Actions | CI/CD |
| Docker Hub | Image registry |

### Architecture

```
GitHub Actions
     │
     ▼
Docker Hub (fulyag/demo-app:latest)
     │
     ▼
Minikube Cluster
├── NGINX Ingress Controller  ← internet traffic
├── Helm Chart (demo-app)
│   └── Flask Pods
└── HPA (min: 1, max: 5, CPU target: 50%)
```

### Prerequisites

- Docker
- Minikube
- kubectl
- Helm

### Installation & Setup

**1. Start Minikube and enable addons**

```bash
minikube start --cpus=2 --memory=4096
minikube addons enable ingress
minikube addons enable metrics-server
```

**2. Build the Docker image inside Minikube**

```bash
eval $(minikube docker-env)
docker build -t fulyag/demo-app:latest .
```

**3. Deploy with Helm**

```bash
helm install demo-app helm/demo-app
```

**4. Add local DNS entry**

```bash
echo "127.0.0.1 demo-app.local" | sudo tee -a /etc/hosts
```

**5. Start Minikube tunnel**

```bash
minikube tunnel
```

**6. Test the application**

```bash
curl http://demo-app.local/
# {"message": "Hello from k8s-advanced-demo!", "pod": "<pod-name>"}

curl http://demo-app.local/health
# {"status": "ok"}
```

### HPA Demo

To trigger autoscaling, run a load test:

```bash
hey -z 120s -c 50 http://demo-app.local/
```

Watch pods scale automatically:

```bash
kubectl get hpa --watch
```

When CPU exceeds 50%, HPA scales up to a maximum of 5 pods. When load decreases, it scales back down to 1.

### CI/CD Pipeline

On every push to `main`, GitHub Actions:
1. Builds the Docker image
2. Pushes it to Docker Hub as `fulyag/demo-app:latest`

Required GitHub secrets:
- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`

### API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Returns greeting and pod name |
| GET | `/health` | Health check |

---

## 🇹🇷 Türkçe

### Genel Bakış

Bu proje, basit bir Flask REST API'sini yerel Kubernetes ortamına endüstri standardı araçlarla deploy etmektedir:

- **Helm** — Kubernetes manifest'lerini paket olarak yönetme
- **NGINX Ingress** — Dış trafiği cluster içine yönlendirme
- **HPA** — CPU kullanımına göre otomatik pod scaling
- **GitHub Actions** — Docker image'ı build edip Docker Hub'a push eden CI/CD pipeline

### Teknoloji Yığını

| Araç | Amaç |
|------|------|
| Python / Flask | REST API |
| Docker | Containerization |
| Kubernetes / Minikube | Container orchestration |
| Helm | Kubernetes paket yöneticisi |
| NGINX Ingress Controller | Dış trafik yönlendirme |
| HPA + Metrics Server | Otomatik ölçekleme |
| GitHub Actions | CI/CD |
| Docker Hub | Image registry |

### Mimari

```
GitHub Actions
     │
     ▼
Docker Hub (fulyag/demo-app:latest)
     │
     ▼
Minikube Cluster
├── NGINX Ingress Controller  ← dış trafik
├── Helm Chart (demo-app)
│   └── Flask Pod'ları
└── HPA (min: 1, max: 5, CPU hedef: %50)
```

### Gereksinimler

- Docker
- Minikube
- kubectl
- Helm

### Kurulum

**1. Minikube'u başlat ve eklentileri etkinleştir**

```bash
minikube start --cpus=2 --memory=4096
minikube addons enable ingress
minikube addons enable metrics-server
```

**2. Docker image'ı Minikube içinde build et**

```bash
eval $(minikube docker-env)
docker build -t fulyag/demo-app:latest .
```

**3. Helm ile deploy et**

```bash
helm install demo-app helm/demo-app
```

**4. Yerel DNS kaydı ekle**

```bash
echo "127.0.0.1 demo-app.local" | sudo tee -a /etc/hosts
```

**5. Minikube tunnel başlat**

```bash
minikube tunnel
```

**6. Uygulamayı test et**

```bash
curl http://demo-app.local/
# {"message": "Hello from k8s-advanced-demo!", "pod": "<pod-adı>"}

curl http://demo-app.local/health
# {"status": "ok"}
```

### HPA Demosu

Otomatik scaling'i tetiklemek için yük testi çalıştır:

```bash
hey -z 120s -c 50 http://demo-app.local/
```

Pod'ların otomatik arttığını izle:

```bash
kubectl get hpa --watch
```

CPU %50'yi geçince HPA maksimum 5 pod'a kadar scale eder. Yük bitince tekrar 1 pod'a döner.

### CI/CD Pipeline

`main` branch'e her push'ta GitHub Actions otomatik olarak:
1. Docker image'ı build eder
2. `fulyag/demo-app:latest` olarak Docker Hub'a push eder

Gerekli GitHub secret'ları:
- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`

### API Endpoint'leri

| Method | Endpoint | Açıklama |
|--------|----------|----------|
| GET | `/` | Selamlama mesajı ve pod adı döner |
| GET | `/health` | Sağlık kontrolü |
