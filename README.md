# Website Deployment on Kubernetes

This repository contains the configuration to deploy a website to Kubernetes using the `jefrinpeter/jefrindockerimage:latest` Docker image.

## Files

- **k8s-deployment.yaml**: Kubernetes deployment configuration.
- **k8s-service.yaml**: Kubernetes service configuration.

## Deployment Steps

1. Apply the deployment and service YAML files:

```bash
kubectl apply -f k8s/k8s-deployment.yaml
kubectl apply -f k8s/k8s-service.yaml
