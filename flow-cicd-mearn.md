# 🚀 Step 1: Code Quality & Vulnerability Scanning with SonarQube

This step documents the integration of **SonarQube** for code quality analysis and vulnerability scanning within the CI/CD pipeline for both the frontend and backend services.

## 📦 Tools Used

- **SonarQube**: Self-hosted on port `9000`
- **SonarScanner**: CLI tool to send code analysis reports to SonarQube

## 📸 Visual Summary

The following images summarize the SonarQube configuration and integration steps:
1. **Token generation for authentication** (used with `sonar.login`)
2. **Scanner setup instructions** for the backend project
3. **Linux OS scanner command preparation**
4. **Full backend configuration snippet**
5. **Frontend scanner command**
6. **Backend scanner command**
7. **SonarQube UI project token view**
8. **Full scanner CLI instructions**
9. **CLI commands for frontend and backend** with valid project keys
10. **SonarQube dashboard view of backend project**
11. **Integration preview in Jenkins job**
12. **Execution snippet showing scanner call from CI pipeline**

## ⚙️ CLI Scanner Commands

To scan the **frontend**:
```bash
sonar-scanner \
  -Dsonar.projectKey=frontend \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://54.159.16.96:9000 \
  -Dsonar.login=squ_972b5fe911a59ab18e5f969cb3c767ce5b3dfccc
```

To scan the **backend**:
```bash
sonar-scanner \
  -Dsonar.projectKey=backend \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://54.159.16.96:9000 \
  -Dsonar.login=squ_972b5fe911a59ab18e5f969cb3c767ce5b3dfccc
```

> 📌 **Note**: These commands are executed in the Jenkins pipeline prior to building and pushing Docker images. The project key must match the name used in SonarQube's dashboard.

## ✅ Outcome

Once executed, both frontend and backend projects appear on the SonarQube dashboard with detailed reports on:
- Bugs
- Code Smells
- Vulnerabilities
- Coverage
- Duplications

This enables continuous feedback on code health as part of the CI process.



# 🚀 Step 2: Credentials Management and DevSecOps Integration

## Overview

In this step, we configure the credentials in Jenkins and Amazon ECR, which are necessary for securely accessing and pushing images as part of our DevSecOps pipeline. Below are the main tasks illustrated by the screenshots provided.

---

## 🔐 Jenkins Credentials Configuration

1. **Secret Text: sonar-token**
   - Kind: Secret text
   - Scope: Global
   - ID: `sonar-token`
   - Purpose: Authentication token for SonarQube.

2. **Secret Text: ACCOUNT_ID**
   - Kind: Secret text
   - Scope: Global
   - ID: `ACCOUNT_ID`
   - Purpose: AWS account ID reference.

3. **Secret Text: ECR_REPO1**
   - Kind: Secret text
   - Scope: Global
   - ID: `ECR_REPO1`
   - Purpose: AWS ECR Backend repo URI.

4. **Secret Text: ECR_REPO2**
   - Kind: Secret text
   - Scope: Global
   - ID: `ECR_REPO2`
   - Purpose: AWS ECR Frontend repo URI.

5. **Username & Password: GITHUB-APP**
   - Kind: Username with password
   - Scope: Global
   - ID: `GITHUB-APP`
   - Username: GitHub username used in pipeline access

6. **AWS Credentials**
   - ID: `aws-creds`
   - Description: AWS Access Key & Secret for pushing/pulling from ECR.

---

## 📦 AWS ECR Configuration

- Created two private repositories in AWS ECR:
  - `backend`: Stores backend Docker images.
  - `frontend`: Stores frontend Docker images.
- Region: `us-east-1`
- Encryption: `AES-256`
- Tag immutability: `Disabled`
- Scan frequency: `Manual`

---

## 🔐 DevSecOps Integration

These credentials and private repositories will be used in the upcoming Jenkins pipeline to:
- Authenticate securely with SonarQube for static code analysis.
- Push built images to ECR using stored secret URIs and credentials.
- Maintain secure GitHub interaction for code checkout and triggers.

> **🛡️ Note**: Credentials are stored in Jenkins using the `Global` scope to be accessible by all relevant jobs, following best security practices.

---



# 🚀 Step 3: Install and Configure Jenkins Plugins

This step involves downloading and configuring essential plugins for setting up a robust CI/CD pipeline using Jenkins. Below is a table describing the purpose of each plugin:

| Plugin Name            | Version     | Purpose                                                                 |
|------------------------|-------------|-------------------------------------------------------------------------|
| **Docker**             | 1.6.2       | Integrates Jenkins with Docker to build and run containers.             |
| **Docker Commons**     | Latest      | Provides shared Docker functionality for other Docker-related plugins. |
| **Docker Pipeline**    | Latest      | Enables Docker container usage within Jenkins pipelines.               |
| **Docker API**         | 3.3.6       | Offers the docker-java API for interacting with Docker from Jenkins.   |
| **NodeJS**             | 1.6.1       | Allows execution of NodeJS scripts as part of the build step.          |
| **OWASP Dependency-Check** | 5.5.1  | Scans for known vulnerabilities in project dependencies.               |
| **SonarQube Scanner**  | 2.17.2      | Integrates SonarQube to analyze and measure code quality.              |

> ✅ Each plugin has been installed and configured using the “Install Automatically” feature where available.

You can now proceed to configure Jenkins jobs using these tools in subsequent pipeline stages.




# 🚀Step 4: Configure Webhook Between Jenkins and SonarQube for Quality Gate

In this step, we will configure Jenkins to communicate with SonarQube by adding the necessary server details and token. This setup enables Jenkins to perform code quality analysis and enforce quality gates during CI/CD pipeline executions.

### 🔧 Configuration in Jenkins

As shown in the image, the following configuration is done under:
**`Manage Jenkins` > `Configure System` > `SonarQube installations`**

* **Name**: `sonar-server`
* **Server URL**: `http://54.159.16.96:9000`
* **Authentication Token**: `sonar-token` (Secret created under Jenkins credentials)

> ✅ Click `Apply` then `Save` after completing the configuration.

---

## 📌 Create Webhook in SonarQube for Jenkins Quality Gate

1. Log in to your SonarQube server (`http://54.159.16.96:9000`).
2. Navigate to:
   **`Administration` > `Configuration` > `Webhooks`**
3. Click on `Create` webhook:

   * **Name**: Jenkins
   * **URL**: `http://<JENKINS_URL>/sonarqube-webhook/`

This allows SonarQube to notify Jenkins about the result of the quality gate status after each analysis.

---

## 📘 Difference Between Code Quality Analysis and Quality Gate

| Concept                   | Description                                                                                                                                                                                                                          |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Code Quality Analysis** | A process executed by SonarQube to scan the source code, detecting code smells, bugs, vulnerabilities, duplications, etc. It produces a detailed report of the code base health.                                                     |
| **Quality Gate**          | A policy made up of threshold rules (e.g., 0 bugs, coverage > 80%) that must be met for a project to be considered "healthy". If a project fails the quality gate, the CI/CD pipeline can be configured to stop or reject the build. |

---

## ✅ Outcome

* Jenkins is now connected to SonarQube for running static code analysis.
* Quality gates are enforced to maintain code standards before proceeding with deployment stages in the pipeline.

---

# 🚀Step 5: CI Pipeline for Three-Tier Kubernetes App

## 🧩 Project Context
This guide demonstrates the CI/CD process for deploying a frontend and backend of a three-tier Kubernetes-based application using Jenkins, GitHub, Amazon ECR, and SonarQube.

---

## ✅ Step 1: Configure Jenkins Pipeline

A new Jenkins item is created for the backend service:
```plaintext
Item Name: Three-tier-backend
Type: Pipeline
```

Pipeline definition uses a Jenkinsfile configured with the following stages:
- **Git Checkout**
- **SonarQube Analysis**
- **Quality Gate**
- **OWASP Dependency Check**

---

## 🔎 Step 2: SonarQube Integration

SonarQube analyzes the code quality:
- ✅ 0 Bugs
- ✅ 0 Vulnerabilities
- ⚠️ 4 Security Hotspots (0% reviewed)
- ✅ A rating on Reliability, Security, and Maintainability

---

## 🏷️ Step 3: Push Docker Image to AWS ECR

Image pushed to private ECR:
```plaintext
Repository: backend
Tag: 1
Size: 353 MB
```

---

## 📦 Step 4: Update Kubernetes Deployment YAML

Frontend deployment image is updated via Jenkins:
```yaml
image: 407622020962.dkr.ecr.us-east-1.amazonaws.com/frontend:3
```

Backend deployment YAML:
```yaml
image: 407622020962.dkr.ecr.us-east-1.amazonaws.com/backend:1
```

Change applied using:
```bash
sed -i s/:2/:3/ deployment.yaml
git commit -m "Update deployment Image to version 3"
git push origin master
```

---

## ✅ Final Result

- Frontend and backend Docker images are version-controlled and deployed.
- Code quality and security are verified with SonarQube.
- GitHub holds deployment files and version updates.
- Jenkins automates the full build → scan → deploy process.

---

## 📌 Tools Used

| Tool         | Purpose                        |
|--------------|--------------------------------|
| Jenkins      | CI  Orchestration            |
| GitHub       | Version Control & Webhook      |
| AWS ECR      | Docker Image Registry          |
| SonarQube    | Code Quality & Security Scan   |
| Kubernetes   | Container Orchestration        |



# 🚀Step 6 : MongoDB Deployment on Kubernetes using ArgoCD (CD step)

This guide demonstrates how to deploy a MongoDB instance as part of a three-tier architecture using Kubernetes, ArgoCD, and manifest files stored in a GitHub repository.

---

## 📁 Repository Info

**GitHub Repository:**  
[End-to-End Kubernetes Three-Tier DevSecOps Project](https://github.com/hossam141/App-MERN-Stack-DevOpsified.git)

**Path for Database Manifests:**  
`Kubernetes-Manifests-file/Database`

---

## 🧩 Application Configuration in ArgoCD

- **Application Name:** `three-tier-database`
- **Namespace:** `three-tier`
- **Project:** `default`
- **Status:** `Progressing` → `Healthy`
- **Sync Status:** `Synced`
- **Target Revision:** `HEAD`
- **Destination Cluster:** `in-cluster`

---

## 📦 Resources Deployed

- **Persistent Volume (PV):** `mongo-pv`
- **Persistent Volume Claim (PVC):** `mongo-volume-claim`
- **Secret:** `mongo-sec`
- **Service:** `mongodb-svc`
- **Deployment:** `mongodb`
- **ReplicaSet:** `mongodb-<hash>`
- **Pod:** `mongodb-<hash>`

---

## 🔧 Commands Used

### 1. Create Namespace
```bash
kubectl create ns three-tier
```

### 2. Get All Resources in Namespace
```bash
kubectl get all -n three-tier
```

Expected output includes:
- `pod/mongodb-xxxxx` (Running)
- `service/mongodb-svc`
- `deployment.apps/mongodb`
- `replicaset.apps/mongodb-xxxxx`

### 3. Validate PVC Binding
```bash
kubectl get pvc -n three-tier
```

Expected output:
- `mongo-volume-claim` should be **Bound** to `mongo-pv`

---

## ✅ ArgoCD Sync and Health Check

Once manifests are committed and ArgoCD syncs the application:
- **Health Status**: `Healthy`
- **Sync Result**: `Sync OK`
- **Deployment Comment:** `Update deployment Image to version 1`

---

## 📊 Visual Representation

- The application is structured in a tree showing links between:
  - `three-tier-database` → `mongo-pv`, `mongo-volume-claim`, `mongo-sec`, `mongodb-svc`, `mongodb`

---

## 👨‍💻 Notes

- Ensure the image used in the `mongodb` deployment is correctly configured.
- The PVC should match the storage class and access mode (`RWO`).
- Confirm the port `27017` is exposed internally via the service.

---

## 🔚 Conclusion

You have successfully deployed a MongoDB service inside a Kubernetes cluster, managed via GitOps with ArgoCD.


# 🚀 Step 7: Deploy Backend and Frontend Applications with ArgoCD (CD step)

In this step, we will deploy the backend and frontend parts of a three-tier application using ArgoCD.

---

## 🚀 Backend Deployment

1. **Navigate to ArgoCD UI**
   - URL Format: `http://<argocd-server-address>`
   - Login with your credentials.

2. **Create Backend Application**
   - Click on `+ NEW APP`.
   - Fill in the details:
     - **Application Name**: `three-tier-backend`
     - **Project Name**: `default`
     - **Sync Policy**: `Manual`
     - **Repository URL**: GitHub repo containing your manifests
     - **Revision**: `HEAD`
     - **Path**: `Kubernetes-Manifests-file/Backend`
     - **Cluster URL**: `https://kubernetes.default.svc`
     - **Namespace**: `three-tier`

3. **Click CREATE** to finish app creation.
4. **Click SYNC** to deploy resources to the cluster.

---

## 🌐 Frontend Deployment

1. **Create Frontend Application**
   - Click on `+ NEW APP`.
   - Fill in the details:
     - **Application Name**: `three-tier-frontend`
     - **Project Name**: `default`
     - **Sync Policy**: `Automatic`
     - Enable **Self Heal**
     - **Repository URL**: GitHub repo containing your manifests
     - **Revision**: `HEAD`
     - **Path**: `Kubernetes-Manifests-file/Frontend`
     - **Cluster URL**: `https://kubernetes.default.svc`
     - **Namespace**: `three-tier`

2. **Click CREATE**.
3. Deployment will start automatically thanks to the automatic sync policy.

---

## ✅ Final Confirmation

- From the **ArgoCD dashboard**, confirm both `three-tier-backend` and `three-tier-frontend` are **Synced** and **Healthy**.
- The resource trees should show the correct deployment structure, including pods and services.



# 🚀 Step 8: Expose Backend API and Frontend via Ingress

## Ingress Manifest (`ingress.yaml`)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mainlb
  namespace: three-tier
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}]'
    # alb.ingress.kubernetes.io/subnets: "subnet-0aa439b4ddafcca10,subnet-0b95df318219145ca,subnet-0ccca36474902829a"
spec:
  ingressClassName: alb
  rules:
    - host: mearnappdevops.study
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: api
                port:
                  number: 3500
          - path: /healthz
            pathType: Exact
            backend:
              service:
                name: api
                port:
                  number: 3500
          - path: /ready
            pathType: Exact
            backend:
              service:
                name: api
                port:
                  number: 3500
          - path: /started
            pathType: Exact
            backend:
              service:
                name: api
                port:
                  number: 3500
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend
                port:
                  number: 3000
```

## Deployment via ArgoCD

1. Open ArgoCD Web UI.
2. Click on `+ NEW APP`.
3. Set the following:
   - **Application Name**: `three-tier-ingress`
   - **Project Name**: `default`
   - **Sync Policy**: Manual or Automated as per your preference.
4. In the Source section:
   - **Repo URL**: `https://github.com/hossam141/App-MERN-Stack-DevOpsified.git`
   - **Revision**: `HEAD`
   - **Path**: `Kubernetes-Manifests-file/`
5. In the Destination section:
   - **Cluster URL**: `https://kubernetes.default.svc`
   - **Namespace**: `three-tier`
6. Click **CREATE** and then **SYNC** the application.

## Route 53 Configuration

1. Navigate to AWS Route 53 → Hosted Zones.
2. Ensure the domain `mearnappdevops.study` exists.
3. Create a new A record:
   - **Record name**: `subdomain` (or leave blank for root).
   - **Alias**: Enabled.
   - **Target**: Alias to Application Load Balancer (`dualstack.k8s-threetie-mainlb-<id>.us-east-1.elb.amazonaws.com`).
   - **Routing policy**: Simple routing.
   - **Evaluate target health**: Yes.
4. Click **Create records**.

## Validation

- Verify your domain (e.g., `http://mearnappdevops.study.study`) loads the frontend.
- Use `kubectl get ing -n three-tier` to confirm the ingress is pointing to the correct address.