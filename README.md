# EKS WordPress Deployment

This project provides a step-by-step guide and resources to set up an Amazon Elastic Kubernetes Service (EKS) cluster, deploy a WordPress application, and configure monitoring tools. The deployment includes the necessary infrastructure setup, application configuration, and monitoring integration.

## Project Overview

The repository is structured to accomplish the following:

1. **EKS Cluster Setup**  
   Scripts and configurations to provision an EKS cluster on AWS, including underlying infrastructure components such as:
   - VPCs (Virtual Private Clouds)
   - Subnets
   - Security Groups and IAM Roles

2. **WordPress Deployment**  
   Kubernetes manifests and Helm charts are provided to deploy a WordPress application in the EKS cluster. This setup ensures:
   - Scalability and high availability of the WordPress application
   - Persistent storage using Amazon EFS (Elastic File System) to maintain application data across pod restarts

3. **Monitoring Tools**  
   Integration of monitoring solutions to provide real-time insights into the performance and health of the EKS cluster and the WordPress application:
   - **Prometheus** for metrics collection
   - **Grafana** for visualization and alerting

## Usage

Follow the steps below to utilize the resources in this repository:

1. **Set Up EKS Cluster**  
   Use the provided configurations to create an EKS cluster. This involves provisioning the necessary network infrastructure and creating roles and policies for cluster operation.

2. **Deploy WordPress Application**  
   Deploy WordPress using the Kubernetes manifests or Helm charts provided in the repository. The deployment includes:
   - Persistent storage for data retention
   - LoadBalancer service for external access to WordPress

3. **Install Monitoring Tools**  
   Configure monitoring for the EKS cluster and WordPress by installing Prometheus and Grafana. This setup allows for:
   - Performance tracking and system health visibility
   - Customizable dashboards and alerts in Grafana based on metrics from Prometheus

## Prerequisites

- AWS CLI installed and configured
- Helm installed for managing Kubernetes applications
- kubectl configured to connect to your EKS cluster

## Conclusion

This repository provides a robust framework for deploying WordPress on AWS using Kubernetes, with integrated monitoring to ensure operational insights. It serves as a valuable resource for both learning and production-ready deployments of WordPress on EKS.
