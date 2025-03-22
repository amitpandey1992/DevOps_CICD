# Jenkins Pipeline Documentation

## Overview
This Jenkins pipeline automates the CI/CD process for a Java-based project using Maven, SonarQube, Nexus, Docker, and AWS EKS. The pipeline includes code compilation, testing, security scanning, quality checks, artifact deployment, containerization, and Kubernetes deployment.

## Prerequisites 
Ensure that the following tools and configurations are available:
- **Jenkins installed and configured**
- **Java Development Kit (JDK 17) installed**
- **Maven installed and configured**
- **SonarQube server configured**
- **Trivy installed for security scans**
- **Docker installed and configured**
- **AWS CLI configured with access to EKS**
- **Nexus repository setup for artifact storage**
- **Git repository with source code**
- **Valid credentials for Git, SonarQube, Nexus, DockerHub, and AWS EKS**

## Pipeline Stages

![image](https://github.com/user-attachments/assets/c7ffbbb0-a4aa-4709-b43d-95421ae261e8)


### 1. Git Checkout
- Fetches the source code from the Git repository.
- Requires a valid Git credential (`git-cred`).

```sh
git clone -b main https://github.com/amitpandey1992/DevOps_CICD.git
```

### 2. Compile
- Compiles the source code using Maven.

```sh
mvn compile
```

### 3. Test
- Runs unit tests using Maven.

```sh
mvn test
```

### 4. File System Scan
- Scans the file system using Trivy for vulnerabilities.

```sh
trivy fs --format table -o trivy-fs-report.html .
```
![image](https://github.com/user-attachments/assets/55530467-c7d1-4cef-b049-cf3ed1fddd6f)


### 5. SonarQube Analysis
- Performs static code analysis using SonarQube.
- Requires SonarQube scanner configured as `sonar`.

```sh
sonar-scanner -Dsonar.projectName=BoardGame -Dsonar.projectKey=BoardGame -Dsonar.java.binaries=.
```

### 6. Quality Gate Check
- Waits for SonarQube quality gate results.

```sh
waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
```
![image](https://github.com/user-attachments/assets/224224e7-dce6-4c8c-8b9e-8b9c5f83acf2)

### 7. Build
- Packages the application using Maven.

```sh
mvn package
```

### 8. Publish to Nexus
- Deploys the artifact to Nexus repository.
- Requires Nexus credentials (`global-settings`).

```sh
mvn deploy
```
![image](https://github.com/user-attachments/assets/1fc3f6b1-db3f-48eb-bfc5-acf20d9f7da6)

### 9. Build & Tag Docker Image
- Builds a Docker image from the application.
- Tags the image for DockerHub.
- Requires Docker credentials (`docker-cred`).

```sh
docker build -t amitpandey18/boardshack:latest .
```

### 10. Docker Image Scan
- Scans the Docker image for vulnerabilities using Trivy.

```sh
trivy image --format table -o trivy-image-report.html amitpandey18/boardshack:latest
```
![image](https://github.com/user-attachments/assets/383359dc-3716-41f4-bf73-d6cc45ecc9f1)

### 11. Push Docker Image
- Pushes the Docker image to DockerHub.

```sh
docker push amitpandey18/boardshack:latest
```
![image](https://github.com/user-attachments/assets/e4a9e7fa-d416-4f63-9191-5af5e93df32d)

### 12. Deploy to AWS EKS
- Deploys the application to an Amazon EKS cluster.
- Requires AWS credentials (`aws-eks-credentials`).

```sh
aws eks update-kubeconfig --name boardgame-cluster --region us-east-1
kubectl apply -f deployment-service.yaml
```
![image](https://github.com/user-attachments/assets/d4f469d8-b620-47fd-b8a2-67f0a5393c01)

## Required Jenkins Plugins
To run this pipeline successfully, install the following plugins:

1. **Pipeline** – Supports scripted and declarative pipelines.
2. **Git Plugin** – Enables Git repository integration.
3. **Maven Integration Plugin** – Allows building Java projects with Maven.
4. **SonarQube Scanner Plugin** – Integrates SonarQube for code analysis.
5. **Trivy Plugin (optional)** – Supports security scanning for files and Docker images.
6. **Pipeline: AWS Steps** – Provides AWS CLI support in Jenkins pipelines.
7. **Kubernetes CLI Plugin** – Enables Kubernetes interaction through `kubectl`.
8. **Docker Pipeline Plugin** – Facilitates building and pushing Docker images.
9. **Nexus Artifact Uploader Plugin** – Supports artifact uploads to Nexus.
10. **Email Extension Plugin (optional)** – Sends email notifications on pipeline status.

## Conclusion
This Jenkins pipeline automates the CI/CD workflow for a Java application, ensuring smooth code integration, testing, security checks, artifact management, and deployment to AWS EKS. By integrating quality gates and security scanning, it enhances application reliability before deployment.
# BoardgameListingWebApp

## Description

**Board Game Database Full-Stack Web Application.**
This web application displays lists of board games and their reviews. While anyone can view the board game lists and reviews, they are required to log in to add/ edit the board games and their reviews. The 'users' have the authority to add board games to the list and add reviews, and the 'managers' have the authority to edit/ delete the reviews on top of the authorities of users.  

## Technologies

- Java
- Spring Boot
- Amazon Web Services(AWS) EC2
- Thymeleaf
- Thymeleaf Fragments
- HTML5
- CSS
- JavaScript
- Spring MVC
- JDBC
- H2 Database Engine (In-memory)
- JUnit test framework
- Spring Security
- Twitter Bootstrap
- Maven

## Features

- Full-Stack Application
- UI components created with Thymeleaf and styled with Twitter Bootstrap
- Authentication and authorization using Spring Security
  - Authentication by allowing the users to authenticate with a username and password
  - Authorization by granting different permissions based on the roles (non-members, users, and managers)
- Different roles (non-members, users, and managers) with varying levels of permissions
  - Non-members only can see the boardgame lists and reviews
  - Users can add board games and write reviews
  - Managers can edit and delete the reviews
- Deployed the application on AWS EC2
- JUnit test framework for unit testing
- Spring MVC best practices to segregate views, controllers, and database packages
- JDBC for database connectivity and interaction
- CRUD (Create, Read, Update, Delete) operations for managing data in the database
- Schema.sql file to customize the schema and input initial data
- Thymeleaf Fragments to reduce redundancy of repeating HTML elements (head, footer, navigation)

## How to Run

1. Clone the repository
2. Open the project in your IDE of choice
3. Run the application
4. To use initial user data, use the following credentials.
  - username: bugs    |     password: bunny (user role)
  - username: daffy   |     password: duck  (manager role)
5. You can also sign-up as a new user and customize your role to play with the application! 😊


---

**Author:** Amit Pandey  
**Repository:** [GitHub - DevOps_CICD](https://github.com/amitpandey1992/DevOps_CICD)


