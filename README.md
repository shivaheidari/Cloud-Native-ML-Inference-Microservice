# Cloud-Native Emotion ML Inference Service

This project is a containerized Machine Learning Microservice deployed on Kubernetes. It exposes an API for emotion recognition (predicting emotions from input data) and is backed by a MongoDB database for persistence and state management.

## Architecture Overview

The infrastructure is defined using Kubernetes manifests and consists of the following components:

1.  **Emotion API (Deployment & Service)**
    *   **Image**: `shiva2020/emoimage:latest`
    *   **Description**: The core machine learning inference service running as a stateless REST API.
    *   **Internal Routing**: Exposes port `8080` internally and is mapped to port `80` by its ClusterIP Service.

2.  **MongoDB Database (Deployment & Service)**
    *   **Image**: `mongo:latest`
    *   **Description**: A standard MongoDB instance for storing API metadata, prediction logs, or user records.
    *   **Security**: Secured via Kubernetes Secrets mapped as environment variables.

3.  **Ingress**
    *   **Host**: `emotion-api.local`
    *   **Description**: Routes external HTTP traffic to the internal `emotion-api` ClusterIP service.

## Prerequisites

*   A running Kubernetes cluster (Minikube, Docker Desktop, EKS, GKE, etc.)
*   `kubectl` command-line tool configured to communicate with your cluster.
*   An Ingress Controller (e.g., NGINX Ingress Controller) installed on your cluster.

## Deployment Instructions

1.  **Configure Local DNS (Optional)**
    If you are running this locally, map the Ingress host to your `localhost` (or Minikube IP) in your `/etc/hosts` file:
    ```bash
    127.0.0.1 emotion-api.local
    ```

2.  **Deploy the configuration**
    Apply the Kubernetes manifests in the following order to ensure dependencies (like secrets) exist before the deployments are created:

    ```bash
    # 1. Apply Secrets
    kubectl apply -f k8s/secret.yaml
    
    # 2. Deploy the Database
    kubectl apply -f k8s/mongodb.yaml
    
    # 3. Deploy the Machine Learning API
    kubectl apply -f k8s/deployment.yaml
    kubectl apply -f k8s/service.yaml
    
    # 4. Configure External Access
    kubectl apply -f k8s/ingress.yaml
    ```

3.  **Verify the deployment**
    Ensure all pods are in the `Running` state:
    ```bash
    kubectl get pods,svc,ingress
    ```