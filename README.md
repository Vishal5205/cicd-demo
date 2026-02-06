# End-to-End DevOps CI/CD Pipeline (GitOps)

## Architecture

Architecture
<img width="1024" height="800" alt="image" src="https://github.com/user-attachments/assets/361ff2f4-0bf7-4f3e-8249-9fe95092bf32" />

---

## Tech Stack

- GitHub
- Jenkins
- SonarCloud
- Docker
- DockerHub
- Kubernetes
- ArgoCD
- AWS EC2

---

## Flow

1. Developer pushes code to GitHub
2. Webhook triggers Jenkins
3. Jenkins runs SonarCloud scan
4. Jenkins builds Docker image
5. Jenkins pushes image to DockerHub
6. Jenkins updates Kubernetes manifest in GitHub
7. ArgoCD detects Git change
8. ArgoCD deploys to Kubernetes
9. Application exposed via NodePort

---

## Features

- Automated CI/CD
- GitOps deployment
- Static code analysis
- Docker containerization
- Kubernetes orchestration
- ArgoCD continuous delivery
- Versioned images
- Quality Gates

---

## Author

Vishal
