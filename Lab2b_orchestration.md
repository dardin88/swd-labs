# Lab 3: Orchestration with Minikube

Welcome to the hands-on lab session on **Orchestration with Minikube**!

This lab introduces Kubernetes orchestration using Minikube, a tool that allows you to run a single-node Kubernetes cluster locally. You will learn how to install Minikube, start a cluster, and deploy a simple application. This is a foundational step in understanding Kubernetes orchestration.

**Important Note:** This tutorial does not entail any report. The goal is to gain hands-on experience with Kubernetes orchestration using Minikube.

---

## 1. Prerequisites & Required Tools

Before starting this lab, ensure you have the following installed:
- Docker (for containerization)

Follow the official Minikube installation guide: [https://minikube.sigs.k8s.io/docs/start/](https://minikube.sigs.k8s.io/docs/start/)

## 2. Stage 1. Starting Minikube

Once Minikube is installed, start a local cluster:

```bash
minikube start --driver=docker
```

This command starts a Minikube cluster using Docker as the driver.

## 3. Stage 2. Kubernetes 101 Tutorial

Follow the official Kubernetes 101 tutorial: [https://minikube.sigs.k8s.io/docs/tutorials/kubernetes_101/](https://minikube.sigs.k8s.io/docs/tutorials/kubernetes_101/)

## 4. Stage 3. Stopping Minikube

When you are done working with Minikube, stop the cluster to free up resources:

```bash
minikube stop
```

## Final Checklist

- [ ] Installed Minikube following the official guide
- [ ] Started Minikube cluster with Docker driver
- [ ] Completed the Kubernetes 101 tutorial
- [ ] Deployed a simple application to the Minikube cluster
- [ ] Stopped Minikube cluster when done
- [ ] Understood the basics of Kubernetes orchestration
