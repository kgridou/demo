# Minikube Deployment Guide

This guide walks you through deploying the Spring Boot demo application to Minikube.

## Prerequisites

- [Minikube](https://minikube.sigs.k8s.io/docs/start/) installed
- [kubectl](https://kubernetes.io/docs/tasks/tools/) installed
- Docker installed (for building images)

## Quick Start

### 1. Start Minikube

```bash
minikube start
```

### 2. Configure Docker to Use Minikube's Docker Daemon

This allows you to build images directly in Minikube's environment:

```bash
# On Windows (PowerShell)
& minikube -p minikube docker-env --shell powershell | Invoke-Expression

# On Windows (CMD)
@FOR /f "tokens=*" %i IN ('minikube -p minikube docker-env --shell cmd') DO @%i

# On Linux/Mac
eval $(minikube docker-env)
```

### 3. Build the Docker Image

```bash
docker build -t demo:latest .
```

Verify the image was built:

```bash
docker images | grep demo
```

### 4. Deploy to Kubernetes

Apply the Kubernetes manifests:

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

### 5. Verify Deployment

Check if pods are running:

```bash
kubectl get pods
kubectl get deployments
kubectl get services
```

Wait for pods to be in `Running` state:

```bash
kubectl wait --for=condition=ready pod -l app=demo --timeout=300s
```

### 6. Access the Application

Get the Minikube service URL:

```bash
minikube service demo-service --url
```

Or open it directly in your browser:

```bash
minikube service demo-service
```

The application will be accessible at `http://<minikube-ip>:30080`

## Useful Commands

### View Logs

```bash
# View logs from all pods
kubectl logs -l app=demo

# View logs from a specific pod
kubectl logs <pod-name>

# Follow logs in real-time
kubectl logs -f -l app=demo
```

### Scale the Application

```bash
# Scale to 3 replicas
kubectl scale deployment/demo-app --replicas=3

# Verify scaling
kubectl get pods
```

### Update the Application

After making code changes:

```bash
# Rebuild the image
docker build -t demo:latest .

# Restart the deployment to pull the new image
kubectl rollout restart deployment/demo-app

# Check rollout status
kubectl rollout status deployment/demo-app
```

### Debug

```bash
# Describe a pod to see events and issues
kubectl describe pod <pod-name>

# Get into a running container
kubectl exec -it <pod-name> -- /bin/sh

# Check service endpoints
kubectl get endpoints demo-service
```

### Clean Up

```bash
# Delete Kubernetes resources
kubectl delete -f k8s/service.yaml
kubectl delete -f k8s/deployment.yaml

# Stop Minikube
minikube stop

# Delete Minikube cluster
minikube delete
```

## Configuration

### Environment Variables

To add environment variables, edit `k8s/deployment.yaml` and add them under `spec.template.spec.containers[0].env`:

```yaml
env:
- name: SPRING_PROFILES_ACTIVE
  value: "prod"
- name: YOUR_ENV_VAR
  value: "your-value"
```

### Resource Limits

Current resource configuration in `k8s/deployment.yaml`:
- Requests: 256Mi memory, 250m CPU
- Limits: 512Mi memory, 500m CPU

Adjust these based on your application's needs.

### Health Checks

The deployment includes liveness and readiness probes that check `/actuator/health`.

**Note:** The current configuration assumes Spring Boot Actuator is enabled. If not using Actuator, you'll need to:

1. Add the dependency to `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

2. Or remove/modify the health check endpoints in `k8s/deployment.yaml`

## Troubleshooting

### Pods in ImagePullBackOff State

Ensure you've configured Docker to use Minikube's daemon before building:
```bash
eval $(minikube docker-env)  # Linux/Mac
```

### Pods in CrashLoopBackOff State

Check pod logs:
```bash
kubectl logs <pod-name>
```

Common issues:
- Application port mismatch (ensure app runs on port 8080)
- Health check endpoints not available
- Insufficient resources

### Service Not Accessible

Verify the service is running:
```bash
kubectl get svc demo-service
minikube service list
```

### Health Checks Failing

If you don't have Spring Boot Actuator configured, temporarily comment out the health checks in `k8s/deployment.yaml`.

## Additional Resources

- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Spring Boot on Kubernetes](https://spring.io/guides/gs/spring-boot-kubernetes/)
