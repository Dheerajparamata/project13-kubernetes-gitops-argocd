Project 13 – Kubernetes GitOps with Argo CD

Project Overview

This project demonstrates GitOps-based continuous deployment using Kubernetes and Argo CD. Application configurations are stored in GitHub, and Argo CD continuously monitors the repository and synchronizes the desired state with the Kubernetes cluster.

The application is deployed using Nginx with two replicas.

Technologies Used

Docker Desktop

Kubernetes (kind)

Argo CD v3.6.1

Git and GitHub

YAML

Nginx

Windows PowerShell

Architecture

       Developer
           |
           v
        GitHub
           |
           v
        Argo CD
           |
           v
      Kubernetes
           |
           v
     Nginx Deployment
        (2 Replicas)
           |
           v
      Nginx Service
           |
           v
      Web Application

Project Structure

project13-kubernetes-gitops-argocd/
│
└── k8s/
    ├── deployment.yaml
    └── service.yaml

Kubernetes Environment

Component

Configuration

Platform

Docker Desktop

Cluster type

kind

Kubernetes version

v1.34.3

Nodes

2

Namespace

project13-gitops

Application

project13-nginx

Container image

nginx:1.27

Replicas

2

Service type

NodePort

NodePort

30083

Implementation Steps

1. Enable Kubernetes in Docker Desktop

Open Docker Desktop.

Navigate to Settings → Kubernetes.

Enable Kubernetes.

Apply and restart.

Verify the cluster:

kubectl get nodes

2. Create Project Namespace

kubectl create namespace project13-gitops

3. Install Argo CD

kubectl apply -n project13-gitops -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Verify installation:

kubectl get pods -n project13-gitops

4. Access Argo CD Dashboard

Start port forwarding:

kubectl port-forward svc/argocd-server -n project13-gitops 8080:443

Open:

https://localhost:8080

Default username:

admin

Retrieve the initial password:

$ARGOCD_PASSWORD = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String((kubectl -n project13-gitops get secret argocd-initial-admin-secret -o jsonpath="{.data.password}")))

Display the password locally:

$ARGOCD_PASSWORD

5. Configure Kubernetes Manifests

Two Kubernetes manifest files were created inside the k8s directory.

deployment.yaml

Defines an Nginx Deployment with two replicas using the nginx:1.27 image.

service.yaml

Defines a NodePort Service that exposes Nginx on port 30083.

6. Push Configuration to GitHub

git add .
git commit -m "Add Kubernetes deployment and service manifests"
git push origin main

GitHub Repository:

https://github.com/Dheerajparamata/project13-kubernetes-gitops-argocd

7. Configure Argo CD Application

An Argo CD application named project13-nginx was created with the following configuration:

Setting

Value

Application Name

project13-nginx

Project

default

Repository

GitHub Project 13 repository

Revision

main

Path

k8s

Destination

https://kubernetes.default.svc

Namespace

project13-gitops

Sync Policy

Automatic

Auto-Sync

Enabled

Prune

Enabled

Self Heal

Enabled

8. Configure RBAC Permissions

During deployment, a namespace mismatch was identified in the Argo CD application controller ClusterRoleBinding.

The ServiceAccount subject was updated to use:

subjects:
  - kind: ServiceAccount
    name: argocd-application-controller
    namespace: project13-gitops

The permission was verified using:

kubectl auth can-i list clusterroles.rbac.authorization.k8s.io --as=system:serviceaccount:project13-gitops:argocd-application-controller

Result:

yes

9. Verify Application Synchronization

kubectl get applications -n project13-gitops

The Argo CD application reached:

Sync Status: Synced

Health Status: Healthy

10. Access the Nginx Application

Port forwarding was used to access the application locally:

kubectl port-forward svc/project13-nginx 8081:80 -n project13-gitops

Open the application:

http://localhost:8081

The Nginx welcome page was displayed successfully.

Verification Commands

Check Kubernetes nodes:

kubectl get nodes

Check Argo CD pods:

kubectl get pods -n project13-gitops

Check application deployment:

kubectl get deployments -n project13-gitops

Check application pods:

kubectl get pods -n project13-gitops

Check service:

kubectl get svc -n project13-gitops

Check Argo CD applications:

kubectl get applications -n project13-gitops

Project Results

Successfully created a Kubernetes cluster using Docker Desktop.

Installed and configured Argo CD.

Connected Argo CD with a GitHub repository.

Created Kubernetes Deployment and Service manifests.

Configured automatic synchronization, pruning and self-healing.

Identified and resolved an RBAC namespace mismatch.

Achieved Synced and Healthy application status.

Successfully deployed Nginx with two replicas.

Accessed the Nginx welcome page through local port forwarding.

Output Screenshots

Add your actual project screenshots in this section.

Docker Desktop Kubernetes Cluster

Kubernetes Nodes

Argo CD Installation

Argo CD Dashboard

GitHub Repository Structure

Kubernetes Deployment YAML

Kubernetes Service YAML

Argo CD Application Configuration

Argo CD Healthy and Synced Status

Nginx Welcome Page

Conclusion

This project provided practical experience with Kubernetes GitOps using Argo CD. It demonstrated how application configurations stored in GitHub can be synchronized with a Kubernetes cluster, enabling automated deployment and application management.

Project 13 – Completed Successfully!
