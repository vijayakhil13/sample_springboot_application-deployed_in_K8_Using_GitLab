# sample_springboot_application-deployed_in_K8_Using_GitLab

A sample Spring Boot application demonstrating build and deployment to Kubernetes using GitLab CI/CD.

## Table of contents
- [Project overview](#project-overview)
- [Prerequisites](#prerequisites)
- [Build and run locally](#build-and-run-locally)
- [Docker image and registry](#docker-image-and-registry)
- [Kubernetes deployment](#kubernetes-deployment)
- [GitLab CI/CD](#gitlab-cicd)
- [Environment variables / configuration](#environment-variables--configuration)
- [Troubleshooting](#troubleshooting)
- [License](#license)
- [Author](#author)

## Project overview
This repository contains a simple Spring Boot application and example manifests / CI configuration to build, publish a container image, and deploy the app to a Kubernetes cluster using GitLab CI/CD.

Use this repository as a starting point to adapt pipelines and manifests for your own applications.

## Prerequisites
- Java JDK 11+ or 17+ (depending on the project pom configuration)
- Maven (or Gradle, if you switch)
- Docker (or BuildKit/kaniko in CI)
- kubectl configured to access your cluster
- A container registry (GitLab Container Registry, Docker Hub, etc.)
- GitLab project with CI/CD enabled (for automated builds and deploys)

## Build and run locally
1. Build the app:
   ```
   mvn clean package
   ```
2. Run locally:
   ```
   java -jar target/<your-artifact>.jar
   ```
   Replace `<your-artifact>.jar` with the generated JAR filename (check target/).

## Docker image and registry
1. Build the Docker image:
   ```
   docker build -t registry.example.com/group/project:latest .
   ```
2. Push to registry:
   ```
   docker push registry.example.com/group/project:latest
   ```
   Replace `registry.example.com/group/project` with your container registry path (for GitLab: `registry.gitlab.com/<namespace>/<project>`).

## Kubernetes deployment
Example manifests should live under a `k8s/` or `manifests/` directory (create if missing). Typical workflow:
1. Edit `k8s/deployment.yaml` to point to your image (tag).
2. Apply manifests:
   ```
   kubectl apply -f k8s/
   ```
3. Verify:
   ```
   kubectl get pods,svc -l app=<your-app-label>
   ```
Scale, update image tags, or use rolling updates as needed:
```
kubectl set image deployment/<deployment-name> <container-name>=registry...:vX
```

## GitLab CI/CD
A basic `.gitlab-ci.yml` pipeline should include stages to:
- build (maven)
- build-and-push-image (docker build & push)
- deploy (kubectl apply) — typically protected and run only on main branch or when manually triggered

Important CI tips:
- Store registry credentials and KUBECONFIG or Kubernetes cluster credentials as protected CI/CD variables or use the GitLab Kubernetes integration / agent for secure cluster access.
- Use tags/runners that have Docker or use Kaniko for building images in Kubernetes runners.

## Environment variables / configuration
Externalize configuration via:
- application.properties / application.yml
- environment variables (recommended in containers)
- Kubernetes Secrets for sensitive values

Example env in a Deployment manifest:
```yaml
env:
  - name: SPRING_PROFILES_ACTIVE
    value: "prod"
  - name: DATABASE_URL
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: url
```

## Troubleshooting
- Pod logs:
  ```
  kubectl logs -f <pod-name>
  ```
- Describe pod for events:
  ```
  kubectl describe pod <pod-name>
  ```
- Common issues:
  - Image not found: ensure image pushed to registry and imagePullSecrets configured.
  - CrashLoopBackOff: check logs and readiness/liveness probe config.

## License
Add your preferred license (e.g., MIT, Apache-2.0). Create a LICENSE file if you want this to be explicit.

## Author
Your name or GitHub handle: @vijayakhil13
