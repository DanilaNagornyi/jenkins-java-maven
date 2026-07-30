# Complete CI/CD Pipeline with AWS EKS and ECR

<div align="center">

![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Maven](https://img.shields.io/badge/Apache_Maven-C71A36?style=for-the-badge&logo=apachemaven&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonwebservices&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)

</div>

This capstone project demonstrates an automated CI/CD pipeline for a Java Spring Boot application. Jenkins builds and packages the application, creates a Docker image, pushes it to a private Amazon ECR repository, and deploys the new version to an Amazon EKS cluster.

## Technologies

- Jenkins and Groovy
- Java, Spring Boot, and Maven
- Docker
- Amazon ECR
- Amazon EKS and Kubernetes
- Git and GitHub

## Pipeline Flow

The pipeline in `Jenkinsfile` performs the following stages:

1. **Increment version** - increments the application version in `pom.xml` and creates an image tag from the application version and Jenkins build number.
2. **Build application** - runs `mvn clean package` to test and package the Spring Boot application.
3. **Build and push image** - builds the Docker image and pushes it to the private Amazon ECR repository.
4. **Deploy to EKS** - substitutes the pipeline environment variables in the Kubernetes manifests and applies them with `kubectl`.
5. **Commit version update** - commits the updated application version and pushes it back to the `jenkins-jobs` branch.

## Kubernetes Deployment

The `kubernetes` directory contains:

- `deployment.yaml` - deploys two replicas of the application and pulls the versioned image from ECR.
- `service.yaml` - exposes the application on port 80 and routes traffic to container port 8080.

The deployment uses the `aws-registry-key` image pull secret to access the private ECR repository.

## Repository Structure

```text
.
├── src/                         # Java application source and tests
├── kubernetes/
│   ├── deployment.yaml          # Kubernetes Deployment
│   └── service.yaml             # Kubernetes Service
├── Dockerfile                   # Application container image
├── Jenkinsfile                  # Complete CI/CD pipeline
├── pom.xml                      # Maven configuration and application version
└── script.groovy                # Groovy functions used during Jenkins exercises
```

## Prerequisites

The pipeline expects the following infrastructure and Jenkins configuration:

- an Amazon EKS cluster and a private Amazon ECR repository;
- Jenkins with Maven 3.9, Docker, `kubectl`, and access to the EKS cluster;
- an `aws-registry-key` secret in the Kubernetes cluster;
- Jenkins credentials with the IDs used in `Jenkinsfile` for ECR, AWS, and GitHub access.

The supporting Jenkins-to-EKS configuration and setup notes are documented in the [deploy-to-eks-from-jenkins](https://github.com/DanilaNagorniy/deploy-to-eks-from-jenkins) repository.

## What I Learned

This project gave me practical experience connecting several CI/CD stages into one Jenkins pipeline. I worked with Groovy pipeline scripts, Jenkins credentials, application versioning, Docker images in a private AWS registry, and automated Kubernetes deployments to EKS. I also learned how the CI and deployment parts depend on credentials, cluster access, and consistent environment variables across Jenkins and Kubernetes manifests.
