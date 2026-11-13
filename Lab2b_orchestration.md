# Lab 2b: Container Orchestration Concepts

Welcome to the hands-on lab session on **Docker Swarm & Kubernetes Dependability Concepts**!

This lab covers container orchestration concepts with a focus on Docker Swarm and Kubernetes architecture, security models, and operational characteristics.

This lab introduces Kubernetes orchestration using Minikube, a tool that allows you to run a single-node Kubernetes cluster locally. You will learn how to install Minikube, start a cluster, and deploy a simple application. This is a foundational step in understanding Kubernetes orchestration.

**Important Note:** This tutorial does not entail any report. The goal is to gain hands-on experience with Kubernetes orchestration using Minikube.

You can find more resources on the course slides.

Before starting this lab, ensure you have completed the following:
- **Lab2a_containerization.md**: Lab Guide: Containerization with Docker, Docker Compose, and Podman

---

## 1. Prerequisites & Required Tools

Before starting this lab, ensure you have the following installed:
- Docker (for containerization)

Follow the official Minikube installation guide: [https://minikube.sigs.k8s.io/docs/start/](https://minikube.sigs.k8s.io/docs/start/)

## 2. Stage 1: Theory & Lecture Alignment

Review the provided lecture slides (`Lab2b_Orchestration.pdf`) and keep the following key architectural concepts in mind:

### Key Dependability Concepts
1. **Container Orchestration Platforms:**
   - **Docker Swarm:** Docker's built-in orchestration tool that uses a declarative approach for service definition, with a manager-worker architecture where the manager node schedules services and worker nodes execute containers. It provides basic scheduling, scaling, and self-healing capabilities.
   - **Kubernetes (K8s):** A more feature-rich container orchestration platform developed by Google, using a master-node architecture with advanced features like auto-scaling, rolling updates, service discovery, load balancing, and extensive ecosystem integration for production-grade deployments.

## 3. Stage 2. Starting Minikube

Once Minikube is installed, start a local cluster:

```bash
minikube start --driver=docker
```

This command starts a Minikube cluster using Docker as the driver.

## 4. Stage 3. Kubernetes 101 Tutorial

Follow the official Kubernetes 101 tutorial: [https://minikube.sigs.k8s.io/docs/tutorials/kubernetes_101/](https://minikube.sigs.k8s.io/docs/tutorials/kubernetes_101/)

## 5. Stage 4. Stopping Minikube

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
