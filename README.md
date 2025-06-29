# 🚀 App-MERN-Stack-DevOpsified

This repository contains the CI/CD and deployment layer of a production-ready MERN Stack application, integrated with full DevSecOps and GitOps practices using **Jenkins**, **ArgoCD**, **SonarQube**, **Trivy**, and **Kubernetes** on **AWS EKS**.

---

## 📦 Repository Structure

```
App-MERN-Stack-DevOpsified/
├── Jenkinsfile
├── Kubernetes-Manifests-file/
│   ├── Backend/
│   ├── Frontend/
│   └── Database/
└── ingress.yaml
```

---

## 🔧 Tech Stack

| Layer            | Tools & Technologies                          |
|------------------|-----------------------------------------------|
| CI/CD            | Jenkins, GitHub Webhook, Docker, AWS ECR     |
| Security         | SonarQube (Code Quality), Trivy (OWASP Scan) |
| GitOps           | ArgoCD                                        |
| Orchestration    | Kubernetes on AWS EKS                         |
| Monitoring       | Prometheus + Grafana                          |
| Ingress & DNS    | ALB Ingress Controller + AWS Route 53         |

---

## 🧪 CI Pipeline with Jenkins

### 🛠 Stages:
1. **Git Checkout**  
2. **SonarQube Static Code Analysis**  
3. **Trivy Vulnerability Scan**  
4. **Docker Image Build & Push to AWS ECR**  
5. **Commit updated Kubernetes manifests**  

Each Jenkins job is connected to GitHub via webhook and configured with credentials and plugins for AWS, Docker, SonarQube, and Node.js.

---

## 🔐 Credentials & Secrets Management

- AWS Access Keys, ECR Repos, GitHub credentials, and SonarQube tokens are securely stored in Jenkins credentials store.
- ECR image repositories:
  - `frontend`
  - `backend`

---

## 🚀 CD Pipeline with ArgoCD

ArgoCD is used to deploy the following components from GitHub:

- `Database`: MongoDB with PVC and Secrets
- `Backend`: Node.js REST API with load-balanced service
- `Frontend`: React SPA
- `Ingress`: ALB routing to `/api` and `/`

ArgoCD Applications are created per component using the `Kubernetes-Manifests-file` directory.

---

## 🌐 Ingress Routing (ALB + Route53)

The application is exposed using ALB ingress controller.  
Custom domain: `http://mearnappdevops.study`

Ingress paths include:
- `/api` → backend
- `/` → frontend
- `/healthz`, `/ready`, `/started` → liveness probes

---

## 📊 Monitoring & Observability

### Tools Used:
- **Prometheus** (via Helm)
- **Grafana** with Kubernetes Dashboard (ID: 6417)

Access via LoadBalancer IPs; secrets managed via Kubernetes.

---

## 📂 How to Deploy

### 1. Backend & Frontend via ArgoCD
```bash
Path: Kubernetes-Manifests-file/Backend/
Path: Kubernetes-Manifests-file/Frontend/
```

### 2. MongoDB
```bash
Path: Kubernetes-Manifests-file/Database/
```

### 3. Ingress
```bash
File: ingress.yaml
```

---

## 📸 Pipeline Flow Diagram

For a full visual representation, refer to the architecture and CI/CD flow files included in this repository:
- [`flow-cicd-mearn.md`](./docs/flow-cicd-mearn.md)

---

## ✅ Outcome

- Code pushed to GitHub triggers Jenkins pipeline
- Code quality and vulnerabilities are checked automatically
- Docker images are published to ECR
- ArgoCD syncs Kubernetes manifests for deployment
- Prometheus and Grafana monitor the system continuously

---

## 🤝 Contributing

Feel free to fork this repo and adapt the pipeline to your own stack. PRs and suggestions are welcome!

---

## 📬 Contact

For feedback or collaboration:  
**Hossam** – [GitHub Profile](https://github.com/hossam141)

---

## 🏷️ Tags

#DevOps #GitOps #Jenkins #Terraform #ArgoCD #AWS #EKS #SonarQube #Prometheus #Grafana #MERN #CI/CD #Kubernetes #OpenSource #InfrastructureAsCode
