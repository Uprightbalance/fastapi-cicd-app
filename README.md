# Production-Style CI/CD for a Containerized FastAPI Application on AWS

## Project Summary

This project demonstrates a production-style CI/CD implementation for a containerized FastAPI application deployed on AWS.

The goal is not just to host an API, but to model a realistic delivery workflow where infrastructure is provisioned as code, application changes are automatically validated, containerized, published, and rolled out to a managed compute platform with minimal manual intervention.

The system is built around:

- **FastAPI** for the application service
- **Docker** for packaging
- **Jenkins** for CI/CD orchestration
- **Terraform** for infrastructure provisioning
- **Amazon ECS Fargate** for container runtime
- **Amazon ECR** for image storage
- **Application Load Balancer (ALB)** for traffic routing and health-based service exposure

---

## Objective

This project was designed to answer a practical DevOps question:

How do you take a simple application from source code to a repeatable, automated, cloud-hosted deployment using production-relevant tooling?

Rather than focusing only on application development, this repository emphasizes:

- **build reproducibility**
- **deployment automation**
- **immutable delivery**
- **infrastructure consistency**
- **cloud-native operational patterns**

---

### Delivery Flow

1. Developer pushes code to GitHub
2. Jenkins pipeline is triggered
3. Application dependencies are installed
4. Automated tests are executed with `pytest`
5. Docker image is built
6. Image is pushed to Amazon ECR
7. ECS service is updated
8. ECS replaces running tasks with the new application version
9. Application is served through an Application Load Balancer

---

## Why This Architecture

This implementation intentionally favors **clarity, reproducibility, and realistic operational design** over unnecessary platform complexity.

### Key design choices include:

- **ECS Fargate instead of Kubernetes**
  - Reduces operational overhead for a single-service deployment
  - Avoids cluster/node management
  - Keeps focus on delivery automation rather than orchestration administration

- **Jenkins instead of a fully managed CI service**
  - Demonstrates pipeline-as-code in a self-managed CI/CD environment
  - Reflects tooling still widely used in enterprise delivery workflows

- **Terraform instead of manual AWS provisioning**
  - Ensures infrastructure is version-controlled and reproducible
  - Makes environment rebuilds and changes auditable

- **Private ECS tasks behind a public ALB**
  - Prevents direct public exposure of application containers
  - Centralizes ingress and health-based traffic routing through the load balancer

- **Container image deployment through ECR**
  - Supports immutable delivery
  - Decouples build from runtime execution

---

## Architectural Decisions and Tradeoffs

### 1. ECS Fargate for Runtime

**Decision:** Use ECS Fargate as the application runtime.

**Why:**
- Simplifies deployment of containerized workloads
- Removes the need to manage EC2 worker nodes
- Provides a production-relevant AWS-native deployment target

**Tradeoff:**
- Less flexible than Kubernetes for advanced orchestration patterns
- Less portable if moving away from AWS-specific services

---

### 2. Jenkins for CI/CD Orchestration

**Decision:** Use Jenkins as the CI/CD engine.

**Why:**
- Enables pipeline-as-code using a `Jenkinsfile`
- Common in self-hosted and enterprise environments
- Good fit for demonstrating explicit build and deploy stages

**Tradeoff:**
- Requires maintenance, plugin management, and credential handling
- More operational overhead than GitHub Actions or other managed CI systems

---

### 3. Terraform for Infrastructure as Code

**Decision:** Provision infrastructure using Terraform.

**Why:**
- Keeps infrastructure declarative and version controlled
- Makes platform changes traceable and repeatable
- Reduces dependency on manual AWS console configuration

**Tradeoff:**
- Introduces state management complexity
- Requires disciplined change control to avoid infrastructure drift

> **Note:** In a production implementation, Terraform state should be stored remotely (e.g., S3 + DynamoDB locking) rather than locally.

---

### 4. ALB-Based Traffic Routing

**Decision:** Expose the application through an Application Load Balancer.

**Why:**
- Provides health-check based routing
- Decouples external traffic from container task lifecycle
- Enables rolling deployment support

**Tradeoff:**
- Adds cost and infrastructure complexity compared to direct public exposure

---

### 5. Simple ECS Rollout Strategy

**Decision:** Trigger deployment by publishing a new image and updating the ECS service.

**Why:**
- Keeps release automation straightforward
- Suitable for a single-service project
- Demonstrates the core mechanics of automated deployment

**Tradeoff:**
- Less controlled than revision-pinned deployment promotion
- Could be further improved with explicit task definition versioning, image digest pinning, or blue/green deployment patterns

---

## Repository Structure

```bash
.
├── Dockerfile
├── Jenkinsfile
├── README.md
├── app
│   ├── __init__.py
│   └── main.py
├── fastAPIinfra
│   ├── alb.tf
│   ├── ecr.tf
│   ├── ecs.tf
│   ├── output.tf
│   ├── provider.tf
│   ├── security.tf
│   ├── variables.tf
│   └── vpc.tf
├── requirements.txt
└── tests
    ├── __init__.py
    └── test_main.py
