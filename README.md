Here’s the updated version with the use of SMTP for email notifications:

CI/CD Pipeline for Node.js Application

Project Overview

This project demonstrates a complete CI/CD pipeline for a Node.js application. The pipeline is designed to automate testing, building, and deploying the application to a Kubernetes cluster, ensuring a streamlined and efficient deployment process. Notifications are also integrated to provide real-time feedback on deployment success or failure via SMTP.

Features
	1.	Automated Testing: Runs tests automatically on pull requests to ensure code quality.
	2.	Dockerized Build: Builds a Docker image for the application.
	3.	Kubernetes Deployment: Deploys the Docker image to an AWS EKS (Elastic Kubernetes Service) cluster.
	4.	Notifications: Sends email notifications via SMTP for successful or failed deployments.

Architecture
	•	Jenkins: Orchestrates the CI/CD pipeline.
	•	AWS EKS: Hosts the Kubernetes cluster.
	•	Docker Hub: Stores the Docker images.
	•	GitHub: Hosts the application code.

Prerequisites
	•	AWS account with EKS configured.
	•	Jenkins server with the following plugins installed:
	•	NodeJS Plugin
	•	Docker Pipeline Plugin
	•	Email Extension Plugin
	•	Docker Hub account.
	•	GitHub repository with the application code.
	•	AWS CLI and kubectl installed and configured.

Jenkins Pipeline

The Jenkinsfile for the pipeline includes the following stages:
	1.	Clone Code from GitHub:
	•	Checks out the latest code from the main branch.
	2.	Node.js Build:
	•	Installs dependencies using npm install.
	3.	Build Docker Image:
	•	Creates a Docker image for the Node.js application.
	4.	Push Docker Image to Docker Hub:
	•	Authenticates with Docker Hub and pushes the image.
	5.	Deploy to Kubernetes:
	•	Applies Kubernetes manifests to deploy the application to the EKS cluster.
	6.	Post-Deployment Notifications:
	•	Sends email notifications based on the success or failure of the deployment using SMTP.

Dockerfile

FROM node:20-alpine

WORKDIR /app

COPY package*.json ./ 

RUN npm install

COPY . . 

EXPOSE 3000

CMD ["npm", "start"]

Kubernetes Manifest

nodejsapp.yaml

---
kind: Deployment
apiVersion: apps/v1
metadata:
  name: nodejs-app
  namespace: default
  labels:
    app: nodejs-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nodejs-app
  template:
    metadata:
      labels:
        app: nodejs-app
    spec:
      containers:
      - name: nodejs-app
        image: "veeruved/nodejsapp-1.0:latest"
        ports:
          - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: nodejs-app
  namespace: default
spec:
  selector:
    app: nodejs-app
  type: LoadBalancer
  ports:
  - name: http
    targetPort: 3000
    port: 80

Setup and Configuration
	1.	Jenkins Setup:
	•	Installed Jenkins on an AWS EC2 t2.medium instance for the master node.
	•	Configured NodeJS plugin with Node.js runtime.
	•	Added GitHub and Docker Hub credentials.
	2.	Kubernetes Cluster:
	•	Used AWS EKS with one t2.medium instance as the master node and two t2.micro instances as worker nodes.
	•	Configured aws eks update-kubeconfig for cluster access.
	3.	GitHub Repository:
	•	The application code is hosted at: GitHub Repo
	4.	Docker Hub Repository:
	•	Docker image pushed to: veeruved/nodejsapp-1.0

Notifications

Email notifications are configured in Jenkins using the Email Extension Plugin and SMTP settings. Emails are sent to patilvaradraj18@gmail.com for deployment success or failure.

How to Run
	1.	Push changes to the GitHub repository.
	2.	The Jenkins pipeline is triggered automatically.
	3.	Upon successful completion:
	•	Docker image is built and pushed to Docker Hub.
	•	Kubernetes manifests are applied to deploy the application to the EKS cluster.
	•	Notifications are sent to the configured email.

Challenges and Solutions
	•	AWS EKS Configuration:
	•	Ensured proper IAM roles and policies for cluster access.
	•	Docker Image Deployment:
	•	Configured Docker Hub credentials securely in Jenkins.
	•	Pipeline Debugging:
	•	Used Jenkins logs extensively to troubleshoot issues.

Future Improvements
	•	Integrate a testing stage for unit and integration tests.
	•	Add monitoring for Kubernetes pods using tools like Prometheus and Grafana.
	•	Enhance security by using AWS Secrets Manager for sensitive credentials.

Conclusion

This CI/CD pipeline simplifies the deployment of a Node.js application, leveraging Jenkins, Docker, and Kubernetes to provide a scalable and reliable solution. The setup ensures code quality, seamless deployment, and real-time feedback, meeting the requirements of modern DevOps practices.
