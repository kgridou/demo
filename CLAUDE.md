# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Spring Boot 3.5.6 application using Java 25, configured with Maven. The project follows standard Spring Boot conventions with a minimal starter setup.

## Common Commands

### Build and Run
- Build the project: `mvnw clean install` or `./mvnw clean install` (Unix/Mac)
- Run the application: `mvnw spring-boot:run` or `./mvnw spring-boot:run`
- Package as JAR: `mvnw package`

### Testing
- Run all tests: `mvnw test`
- Run a specific test class: `mvnw test -Dtest=DemoApplicationTests`
- Run tests with coverage: `mvnw test jacoco:report` (if jacoco plugin is added)

### Maven Wrapper
The project uses Maven Wrapper (`mvnw` and `mvnw.cmd`), so Maven doesn't need to be installed globally. Use `mvnw` on Windows or `./mvnw` on Unix-based systems.

## Architecture

### Package Structure
- `com.example.demo` - Root package containing the main application class
- `src/main/resources/application.properties` - Spring Boot configuration file

### Main Components
- `DemoApplication.java` - Spring Boot application entry point with `@SpringBootApplication` annotation

### Configuration
Application properties are stored in `src/main/resources/application.properties`. The application name is currently set to "demo".

## Minikube/Kubernetes Deployment

### Build and Deploy
- Build Docker image: `docker build -t demo:latest .`
- Apply Kubernetes manifests: `kubectl apply -f k8s/`
- Access service: `minikube service demo-service --url`

### Minikube Setup
Before building the Docker image, configure your shell to use Minikube's Docker daemon:
- Windows (PowerShell): `& minikube -p minikube docker-env --shell powershell | Invoke-Expression`
- Linux/Mac: `eval $(minikube docker-env)`

### Monitor and Debug
- View pods: `kubectl get pods`
- View logs: `kubectl logs -l app=demo`
- Restart deployment: `kubectl rollout restart deployment/demo-app`

See `MINIKUBE_DEPLOYMENT.md` for complete deployment instructions and troubleshooting.
