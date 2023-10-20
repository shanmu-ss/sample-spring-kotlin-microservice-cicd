# ArgoCD Installation and Application Creation Guide

## Table of Contents

- [Introduction](#introduction)
- [ArgoCD Installation](#argocd-installation)
  - [Azure Kubernetes Cluster](#azure-kubernetes-cluster)
  - [Local Windows Machine with Kubernetes](#local-windows-machine-with-kubernetes)
  - [ArgoCD CLI Installation](#argocd-cli-installation)
- [Creating an ArgoCD Application](#creating-an-argocd-application)
- [Additional Resources](#additional-resources)

---

## Introduction

ArgoCD is a declarative, GitOps continuous delivery tool for Kubernetes. It allows users to maintain and manage their Kubernetes resources using git repositories as the source of truth for the declared desired state of applications.

---

## ArgoCD Installation

### Azure Kubernetes Cluster

#### Set Up Azure Kubernetes Service (AKS)

1. Login to Azure CLI:

```bash
az login
```

2. Create a new AKS cluster (replace placeholders with your desired names and configurations):

```bash
az aks create --resource-group <ResourceGroupName> --name <ClusterName> --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

3. Get credentials to interact with your AKS cluster:

```bash
az aks get-credentials --resource-group <ResourceGroupName> --name <ClusterName>
```

#### Install ArgoCD on AKS

1. Create a namespace for ArgoCD:

```bash
kubectl create namespace argocd
```

2. Install ArgoCD:

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

3. Access ArgoCD by exposing the ArgoCD server using a LoadBalancer:

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

After a few minutes, retrieve the external IP with:

```bash
kubectl get svc -n argocd
```

---

## Kubernetes Installation on a Local Windows Machine

Before installing ArgoCD on your local Windows machine, you need to have a Kubernetes cluster running. Here's a guide to setting up a local Kubernetes cluster on Windows using Minikube:

### Prerequisites:

- **Windows 10 Pro or Enterprise**: Minikube requires a virtualization-capable environment. Ensure you have Hyper-V or VirtualBox installed.
- **Install Chocolatey**: Chocolatey is a package manager for Windows.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

### Installing Minikube:

1. **Install Minikube using Chocolatey**:

```powershell
choco install minikube
```

2. **Install kubectl**:

kubectl is the command-line tool for interacting with Kubernetes clusters. Install it using Chocolatey:

```powershell
choco install kubernetes-cli
```

3. **Start Minikube**:

```powershell
minikube start --vm-driver=hyperv
```

Note: If you're using VirtualBox, replace `--vm-driver=hyperv` with `--vm-driver=virtualbox`.

4. **Verify Installation**:

To ensure Minikube is up and running:

```powershell
minikube status
```

You should see the status of the minikube, host, kubelet, and API server. If they're all running, you're good to go!

5. **Access Kubernetes Dashboard**:

Minikube comes with a built-in dashboard that allows you to manage and monitor your Kubernetes cluster:

```powershell
minikube dashboard
```

This will open up the Kubernetes dashboard in your default web browser.

### Conclusion:

You now have a running Kubernetes cluster on your local Windows machine using Minikube. You can proceed with the ArgoCD installation steps mentioned in the previous sections.

After setting up Kubernetes on your local Windows machine, you can follow the "Local Windows Machine with Kubernetes" section in the provided guide to install ArgoCD.

---

### Local Windows Machine with Kubernetes

Ensure your local Kubernetes cluster (e.g., Minikube or Docker Desktop) is running and then follow the same steps as the AKS installation above.

---

### ArgoCD CLI Installation

On Windows, download the latest CLI:

```powershell
# Navigate to a directory of your choice, e.g., C:\tools
Set-Location C:\devtools

# Download the latest Windows release of ArgoCD
Invoke-WebRequest -Uri https://github.com/argoproj/argo-cd/releases/latest/download/argocd-windows-amd64.exe -OutFile argocd.exe

# Optionally, add C:\tools to your system PATH if it's not already (this step might require you to restart your PowerShell session)
$env:PATH += ";C:\devtools"

```

Then, make the binary available in your PATH.

---

## Creating an ArgoCD Application

1. **Prerequisites**:
   - ArgoCD and CLI installed.
   - Access to the given GitHub repository.
   - Ensure you're logged in to your ArgoCD instance using the CLI.

2. **Create the Application**:

```bash
argocd app create spring-kotlin-app-test \
--repo https://github.com/shanmu-ss/sample-spring-kotlin-microservice-cicd.git \
--path k8s/ \
--dest-namespace default \
--dest-server https://kubernetes.default.svc \
--sync-policy automated
```

3. **Verify the Application**:

To ensure the application was created correctly:

```bash
argocd app get spring-kotlin-app-test
```

---

## Additional Resources

- [Official ArgoCD Documentation](https://argoproj.github.io/argo-cd/)
- [Azure Kubernetes Service (AKS) Documentation](https://docs.microsoft.com/en-us/azure/aks/)
- [ArgoCD CLI Command Guide](https://argoproj.github.io/argo-cd/user-guide/commands/)

---
