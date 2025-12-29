# Cloud Infrastructure: Containers & Kubernetes

## Evolution of Deployment
1.  **Bare Metal**: Installing Node.js directly on a physical server rack.
2.  **Virtual Machines (VM)**: AWS EC2. A "fake" computer running on a real computer. Contains full OS (Linux/Windows). slow boot (minutes).
3.  **Containers**: Docker. Shares the Host OS kernel but isolates the application "User Space". Fast boot (seconds).

## Docker Concepts

### Image
*   **Definition**: The Blueprint / Recipe. Read-only.
*   **Contains**: Node.js runtime + Your Code + `node_modules`.
*   **Built via**: `Dockerfile`.

### Container
*   **Definition**: The Running Instance. The "Cake" baked from the recipe.
*   **Feature**: "It works on my machine". If it runs in Docker on your laptop, it runs in Docker on AWS. Eliminates "missing dependency" bugs.

## Orchestration: Kubernetes (K8s)
When you have 1 container, you use Docker Run.
When you have 500 containers working together, you use Kubernetes.

### Core K8s Objects (For Backend Devs)

#### 1. Pod
The smallest unit. Usually contains **1 Container** (e.g., your API).
*   *Note*: Pods are mortal. If they die, K8s creates a new one. Never rely on a Pod IP staying the same.

#### 2. Deployment
Manages Pods.
*   "I want 3 replicas of `api-v1`."
*   K8s ensures 3 are running. If a server crashes, K8s starts the missing Pods on a different server.
*   **Rolling Update**: Changes `api-v1` to `api-v2` gradually (1 by 1) so users experience zero downtime.

#### 3. Service
The Internal Load Balancer.
*   Since Pod IPs change, the **Service** gives a stable IP (`10.1.1.50`).
*   Traffic to the *Service* is load-balanced to the healthy *Pods*.

#### 4. Ingress
The External Router.
*   Handles incoming HTTP traffic (`domain.com`) and routes it to the internal **Service**.
*   This is usually where you configure SSL/TLS certificates.

## Managed Services vs Self-Hosted
*   **Self-Hosted K8s**: You manage the Master nodes. (Painful).
*   **Managed (EKS/GKE)**: AWS/Google manages the Master. You just give them Docker images. (Standard).
*   **Serverless (Fargate/Cloud Run)**: You don't even see the servers. Just "Run this Container". (Easiest for startups).
