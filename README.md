# Spring Boot Demo Application

A minimal Spring Boot 3.5.6 application demonstrating containerization and Kubernetes deployment on Minikube.

## Prerequisites

- Java 25
- Maven (or use the included Maven wrapper)
- Docker
- Minikube (for Kubernetes deployment)
- kubectl

## Quick Start

### Build and Run Locally

```bash
# Build the project
./mvnw clean install

# Run the application
./mvnw spring-boot:run
```

On Windows, use `mvnw` instead of `./mvnw`.

### Run Tests

```bash
./mvnw test
```

## Docker

### Build Docker Image

```bash
docker build -t demo:latest .
```

### Run Docker Container

```bash
docker run -p 8080:8080 demo:latest
```

## Kubernetes Deployment

For complete Kubernetes deployment instructions, see [MINIKUBE_DEPLOYMENT.md](MINIKUBE_DEPLOYMENT.md).

### Quick Deployment to Minikube

```bash
# Start Minikube
minikube start

# Configure Docker to use Minikube's daemon
eval $(minikube docker-env)  # Linux/Mac
# Or for Windows PowerShell:
# & minikube -p minikube docker-env --shell powershell | Invoke-Expression

# Build the image
docker build -t demo:latest .

# Deploy to Kubernetes
kubectl apply -f k8s/

# Access the application
minikube service demo-service --url
```

## Project Structure

```
demo/
├── .github/
│   └── workflows/
│       └── ci.yml              # CI/CD pipeline
├── k8s/
│   ├── deployment.yaml         # Kubernetes deployment manifest
│   └── service.yaml            # Kubernetes service manifest
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/demo/
│   │   │       └── DemoApplication.java
│   │   └── resources/
│   │       └── application.properties
│   └── test/
│       └── java/
│           └── com/example/demo/
│               └── DemoApplicationTests.java
├── Dockerfile                  # Multi-stage Docker build
├── pom.xml                     # Maven configuration
├── CLAUDE.md                   # Development guide for Claude Code
├── MINIKUBE_DEPLOYMENT.md      # Kubernetes deployment guide
└── README.md                   # This file
```

## CI/CD

This project includes a GitHub Actions workflow (`.github/workflows/ci.yml`) that:

1. Builds and tests the application
2. Creates a Docker image
3. Deploys to Minikube for integration testing
4. Validates the service is accessible
5. Lints the Dockerfile

The workflow runs on pushes and pull requests to `main` and `develop` branches.

## Technology Stack

- **Java**: 25
- **Spring Boot**: 3.5.6
- **Build Tool**: Maven 3.9
- **Container**: Docker
- **Orchestration**: Kubernetes
- **Local K8s**: Minikube

## Configuration

Application configuration is in `src/main/resources/application.properties`.

### Kubernetes Configuration

- **Replicas**: 2
- **Service Type**: NodePort (port 30080)
- **Container Port**: 8080
- **Resource Limits**: 512Mi memory, 500m CPU
- **Resource Requests**: 256Mi memory, 250m CPU

## Development

For detailed development instructions and architecture overview, see [CLAUDE.md](CLAUDE.md).

## License

This is a demo project for educational purposes.
