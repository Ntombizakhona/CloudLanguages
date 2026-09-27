
Kubernetes in the Cloud
=======================

The Container Orchestrator
--------------------------

You’ve built a portfolio with a frontend, backend API, and database. You’ve deployed it to a Linux server in the cloud.

But here’s the problem: your application runs on one server.

Think about it:

*   _What happens when that server crashes?_ → **Your entire portfolio goes down**
*   _Black Friday traffic spike?_ → **One server can’t handle it**
*   _Need to update your code?_ → **Downtime for users**
*   _Server maintenance required?_ → **More downtime**

Your deployed portfolio is a single point of failure. One server, one chance, one prayer.

In the real world, companies don’t run one server. They run hundreds…sometimes thousands… of containers that need to work together seamlessly. But managing all those containers manually? Impossible.

**That’s where Kubernetes comes in.**

Kubernetes (K8s) is the operating system for the cloud. It takes your containers and orchestrates them: automatically deploying, scaling, and healing your applications. It’s the industry standard for running production workloads:

*   Google created it (based on 15 years of internal experience)
*   Every major cloud provider offers managed Kubernetes (GKE , EKS, AKS)
*   90% of organizations using containers use Kubernetes

If you’re serious about cloud, be serious about Kubernetes as well.

Topics
------

1.  Kubernetes architecture and core concepts
2.  Pods, Deployments, and Services explained
3.  Deploying applications to Kubernetes clusters
4.  Configuration management with ConfigMaps and Secrets
5.  Scaling and load balancing strategies
6.  Persistent storage for stateful applications
7.  Monitoring and troubleshooting K8s clusters
8.  Deploying your portfolio to Kubernetes
9.  CI/CD integration with GitOps

1 | Why Kubernetes Rules the Cloud
----------------------------------

### The Problem Kubernetes Solves

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    THE CONTAINER MANAGEMENT NIGHTMARE                   │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Without Kubernetes:                                                   │
│   ──────────────────                                                    │
│                                                                         │
│   ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐       │
│   │Container│  │Container│  │Container│  │Container│  │Container│       │
│   │   App1  │  │   App2  │  │ Database│  │  Cache  │  │  Queue  │       │
│   └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘       │
│        │            │            │            │            │            │
│        ▼            ▼            ▼            ▼            ▼            │
│   YOU manually manage:                                                  │
│   ─────────────────────                                                 │
│   • Which server runs which container?                                  │
│   • What if a container crashes at 3 AM?                                │
│   • How do we scale from 5 to 500 containers?                           │
│   • How do containers find each other?                                  │
│   • How do we update without downtime?                                  │
│   • What about secrets and configuration?                               │
│                                                                         │
│   Answer: You don't sleep. Ever. 😰                                     │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      WITH KUBERNETES                                    │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   YOU:                                                                  │
│   "I want 3 copies of my app, always running, with this configuration"  │
│                                                                         │
│   KUBERNETES:                                                           │
│   ────────────                                                          │
│   ✓ Schedules containers across servers automatically                   │
│   ✓ Restarts crashed containers instantly                               │
│   ✓ Scales up/down based on demand                                      │
│   ✓ Provides service discovery and load balancing                       │
│   ✓ Handles rolling updates with zero downtime                          │
│   ✓ Manages secrets and configuration                                   │
│   ✓ YOU SLEEP PEACEFULLY 😴                                             │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Kubernetes in the Cloud Ecosystem

Every major cloud provider offers a managed Kubernetes service, handling the complexity of running the control plane so you can focus on deploying applications.

**Google Cloud** offers _GKE (Google Kubernetes Engine)_, which provides the tightest integration since Google created Kubernetes. Features like Autopilot mode handle node management automatically, and you benefit from Google’s deep expertise in container orchestration.

**Amazon Web Services** provides _EKS (Elastic Kubernetes Service)_, the most popular combination of cloud provider and Kubernetes. Integration with other AWS services like IAM, VPC, and CloudWatch makes EKS a natural choice for organizations already invested in the AWS ecosystem.

**Microsoft Azure** delivers _AKS (Azure Kubernetes Service)_, which offers the best experience for organizations using the Microsoft stack. Integration with Azure Active Directory, Azure DevOps, and Visual Studio makes development workflows seamless.

Self-managed Kubernetes remains an option on any cloud provider by installing Kubernetes on virtual machines yourself. This approach gives you full control but requires significantly more operational expertise.

### Docker vs Kubernetes

A common point of confusion for newcomers is the relationship between Docker and Kubernetes. They’re complementary tools that solve different problems.

Docker builds and runs individual containers. It packages your application with all its dependencies into a portable unit that runs consistently anywhere. When you’re developing locally, Docker lets you run your app, test changes, and share your work with teammates. Docker answers the question: **“How do I package and run my application?”**

Kubernetes orchestrates many containers across many machines. It doesn’t replace Docker, it uses Docker (or other container runtimes) under the hood. Kubernetes answers different questions: **“How do I run 100 copies of my application? How do I keep them running when servers fail? How do I balance traffic between them? How do I update them without downtime?”**

Think of it this way: Docker is a shipping container that packages your application for transport. Kubernetes is the shipping port that manages thousands of containers, deciding where each one goes, replacing damaged ones, and routing cargo efficiently.

For development, you need Docker. For production at scale, you need both.

2 | Kubernetes Architecture
---------------------------

### The Big Picture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    KUBERNETES CLUSTER ARCHITECTURE                      │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   ┌───────────────────────────────────────────────────────────────┐     │
│   │                     CONTROL PLANE (Master)                    │     │
│   │                                                               │     │
│   │  ┌─────────────┐  ┌─────────────┐  ┌──────────────────────┐   │     │
│   │  │ API Server  │  │   etcd      │  │ Controller Manager   │   │     │
│   │  │             │  │             │  │                      │   │     │
│   │  │ Gateway for │  │ Key-value   │  │ Ensures desired      │   │     │
│   │  │ all K8s     │  │ store for   │  │ state matches        │   │     │
│   │  │ requests    │  │ cluster     │  │ actual state         │   │     │
│   │  │             │  │ data        │  │                      │   │     │
│   │  └─────────────┘  └─────────────┘  └──────────────────────┘   │     │
│   │                                                               │     │
│   │  ┌─────────────────────────────────────────────────────────┐  │     │
│   │  │                      Scheduler                          │  │     │
│   │  │    Decides which node should run each new pod           │  │     │
│   │  └─────────────────────────────────────────────────────────┘  │     │
│   └───────────────────────────────────────────────────────────────┘     │
│                              │                                          │
│                              ▼                                          │
│   ┌────────────────────────────────────────────────────────────────┐    │
│   │                      WORKER NODES                              │    │
│   │                                                                │    │
│   │  ┌─────────────────────┐      ┌─────────────────────┐          │    │
│   │  │     Node 1          │      │     Node 2          │          │    │
│   │  │  ┌───────────────┐  │      │  ┌───────────────┐  │          │    │
│   │  │  │   kubelet     │  │      │  │   kubelet     │  │          │    │
│   │  │  │ (Node Agent)  │  │      │  │ (Node Agent)  │  │          │    │
│   │  │  └───────────────┘  │      │  └───────────────┘  │          │    │
│   │  │  ┌───────────────┐  │      │  ┌───────────────┐  │          │    │
│   │  │  │  kube-proxy   │  │      │  │  kube-proxy   │  │          │    │
│   │  │  │ (Networking)  │  │      │  │ (Networking)  │  │          │    │
│   │  │  └───────────────┘  │      │  └───────────────┘  │          │    │
│   │  │                     │      │                     │          │    │
│   │  │  ┌─────┐ ┌─────┐    │      │  ┌─────┐ ┌─────┐    │          │    │
│   │  │  │ Pod │ │ Pod │    │      │  │ Pod │ │ Pod │    │          │    │
│   │  │  └─────┘ └─────┘    │      │  └─────┘ └─────┘    │          │    │
│   │  └─────────────────────┘      └─────────────────────┘          │    │
│   │                                                                │    │
│   └────────────────────────────────────────────────────────────────┘    │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

The **Kubernetes control plane** consists of several interconnected components that work together to manage your cluster.

The **API Server** acts as the entry point for all Kubernetes commands, functioning like a reception desk that receives and processes every request.

Behind it, **etcd** serves as the cluster’s distributed key-value store, maintaining all configuration and state data, essentially the database that remembers everything about your cluster.

The **Scheduler** works like an assignment manager, analyzing resource requirements and deciding which worker node should run each new pod.

Meanwhile, the **Controller Manager** operates as quality control, continuously comparing the cluster’s actual state against your desired state and making adjustments to reconcile any differences.

On each worker node, the **kubelet** acts as a local supervisor, receiving instructions from the control plane and ensuring containers run correctly within their pods.

Finally, **kube-proxy** handles networking on each node like a router, maintaining network rules that enable communication between pods and directing traffic to the appropriate destinations.

### Core Kubernetes Objects

```
┌─────────────────────────────────────────────────────────────────────────┐
│                     KUBERNETES OBJECTS HIERARCHY                        │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Deployment                                                            │
│   ─────────                                                             │
│   "I want 3 replicas of my app"                                         │
│       │                                                                 │
│       ▼                                                                 │
│   ReplicaSet                                                            │
│   ──────────                                                            │
│   "I'll ensure exactly 3 pods exist"                                    │
│       │                                                                 │
│       ├──────────────┬──────────────┐                                   │
│       ▼              ▼              ▼                                   │
│   ┌───────┐      ┌───────┐      ┌───────┐                               │
│   │ Pod 1 │      │ Pod 2 │      │ Pod 3 │                               │
│   │       │      │       │      │       │                               │
│   │┌─────┐│      │┌─────┐│      │┌─────┐│                               │
│   ││Cont.││      ││Cont.││      ││Cont.││                               │
│   │└─────┘│      │└─────┘│      │└─────┘│                               │
│   └───────┘      └───────┘      └───────┘                               │
│                                                                         │
│   Service                                                               │
│   ───────                                                               │
│   "Access all 3 pods via one stable IP/DNS name"                        │
│       │                                                                 │
│       └──────────────────────────────────────┐                          │
│                                               ▼                         │
│                                   ┌─────────────────┐                   │
│                                   │  Load Balancer  │                   │
│                                   │   10.0.0.5:80   │                   │
│                                   └─────────────────┘                   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

Understanding Kubernetes objects is essential because everything you deploy is defined as an object with a specification.

**Pods** are the smallest deployable units in Kubernetes. A pod contains one or more containers that share storage and network resources. While you can run a single container in a pod, multi-container pods enable patterns like sidecar containers that add logging or security features to your main application.

**ReplicaSets** ensure a specified number of pod replicas run at any given time. If a pod crashes, the ReplicaSet creates a replacement. If you have too many pods, it terminates extras. You rarely create ReplicaSets directly instead, _Deployments_ manage them for you.

**Deployments** provide declarative updates for pods and ReplicaSets. You describe the desired state (three replicas of version 2.0), and the Deployment controller changes the actual state to match. Deployments enable rolling updates, rollbacks, and scaling and they’re what you’ll use most often.

**Services** provide stable network endpoints for accessing pods. Since pods are ephemeral and their IP addresses change, you need a consistent way to reach them. A Service gives you a stable IP and DNS name that routes traffic to healthy pods automatically.

**ConfigMaps** store non-sensitive configuration data as key-value pairs. Instead of hardcoding database hostnames or feature flags in your container, you store them in ConfigMaps and inject them as environment variables or files.

**Secrets** store sensitive data like passwords, API keys, and certificates. They’re similar to _ConfigMaps_ but encoded and intended for sensitive information. Kubernetes handles them more carefully, though for true security you’ll want additional tools like HashiCorp Vault.

**Ingress** manages external HTTP and HTTPS access to services. Rather than exposing each service individually, Ingress lets you define routing rules, sending traffic for api.example.com to your backend service and example.com to your frontend.

3 | Setting Up Your Kubernetes Environment
------------------------------------------

### Option A: Local Development with Minikube

```
# Install Minikube (Linux/macOS)
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
# Start local cluster (uses Docker as driver)
minikube start --driver=docker --memory=4096 --cpus=2
# Verify installation
kubectl cluster-info
kubectl get nodes
# Output:
# NAME       STATUS   ROLES           AGE   VERSION
# minikube   Ready    control-plane   1m    v1.28.0
```

### Option B: Docker Desktop (Windows/Mac)

If you’re on Windows or Mac, Docker Desktop has Kubernetes built in:

1.  Install [Docker Desktop](https://www.docker.com/products/docker-desktop/)
2.  Open Settings → Kubernetes → check “Enable Kubernetes” → Apply & Restart
3.  Wait for the green dot, then verify:

```
kubectl get nodes
# Output:
# NAME             STATUS   ROLES           AGE   VERSION
# docker-desktop   Ready    control-plane   5m    v1.28.x
```

No minikube needed. Docker Desktop handles everything.

### Option C: Cloud Managed Kubernetes

**AWS EKS (Elastic Kubernetes Service):**

```
# Install eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
# Create cluster (takes ~15-20 minutes)
eksctl create cluster \
  --name portfolio-cluster \
  --region us-west-2 \
  --nodes 3 \
  --node-type t3.medium
# Verify
kubectl get nodes
```

**Google Cloud GKE**

```
# Create cluster
gcloud container clusters create portfolio-cluster \
  --num-nodes=3 \
  --zone=us-central1-a \
  --machine-type=e2-medium
# Get credentials
gcloud container clusters get-credentials portfolio-cluster --zone=us-central1-a
```

**Azure AKS**

```
# Create resource group
az group create --name portfolio-rg --location eastus
# Create cluster
az aks create \
  --resource-group portfolio-rg \
  --name portfolio-cluster \
  --node-count 3 \
  --node-vm-size Standard_B2s \
  --generate-ssh-keys
# Get credentials
az aks get-credentials --resource-group portfolio-rg --name portfolio-cluster
```

### Install kubectl (Kubernetes CLI)

```
# Linux
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
# macOS (Homebrew)
brew install kubectl
# Windows (Chocolatey)
choco install kubernetes-cli
# Verify installation
kubectl version --client
```

4 | Your First Kubernetes Deployment
------------------------------------

### Step 01: Create a Deployment

```
# web-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-app
        image: nginx:1.21
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
```

### What This YAML Means

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    KUBERNETES YAML ANATOMY                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   apiVersion: apps/v1      ← Which K8s API to use                       │
│   kind: Deployment         ← What type of object                        │
│   metadata:                                                             │
│     name: web-app          ← Name for this deployment                   │
│                                                                         │
│   spec:                    ← Desired state specification                │
│     replicas: 3            ← Run 3 copies of this pod                   │
│                                                                         │
│     selector:              ← How to find pods for this deployment       │
│       matchLabels:                                                      │
│         app: web-app       ← Match pods with label 'app: web-app'       │
│                                                                         │
│     template:              ← Pod template (what each pod looks like)    │
│       metadata:                                                         │
│         labels:                                                         │
│           app: web-app     ← Label each pod                             │
│       spec:                                                             │
│         containers:        ← Container(s) in each pod                   │
│         - name: web-app    ← Container name                             │
│           image: nginx:1.21 ← Docker image to run                       │
│                                                                         │
│         resources:         ← CPU/Memory limits                          │
│           requests:        ← Minimum guaranteed                         │
│           limits:          ← Maximum allowed                            │
│                                                                         │
│         livenessProbe:     ← "Is the container alive?"                  │
│         readinessProbe:    ← "Is the container ready for traffic?"      │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Step 02: Create A Service

```
# web-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
spec:
  selector:
    app: web-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer  # Exposes externally
```

### Understanding Service Types

Kubernetes offers several Service types, each suited to different access patterns.

**ClusterIP** is the default type, creating an internal IP address accessible only within the cluster. Use ClusterIP for backend services that other pods need to reach but external users don’t. Your frontend pods can reach your database through a ClusterIP service, but the internet cannot.

**NodePort** exposes the service on each node’s IP at a static port between 30000 and 32767. This works for development and testing when you need external access without a cloud load balancer. Access your service at any node’s IP plus the assigned port.

**LoadBalancer** provisions an external load balancer through your cloud provider. This is the standard choice for production services that need internet access.

1.  On AWS, it creates an ELB.
2.  On GCP, a Cloud Load Balancer
3.  On Azure, an Azure Load Balancer.

Users access your service through the load balancer’s public IP.

**ExternalName** maps a service to a DNS name rather than selecting pods. Use this when you need to access external services (like a managed database) through Kubernetes’ service discovery mechanism.

### Step 03: Deploy to Kubernetes

```
# Apply the deployment
kubectl apply -f web-app-deployment.yaml
# Output: deployment.apps/web-app created
# Apply the service
kubectl apply -f web-app-service.yaml
# Output: service/web-app-service created
# Check deployment status
kubectl get deployments
# NAME      READY   UP-TO-DATE   AVAILABLE   AGE
# web-app   3/3     3            3           30s
# Check pods
kubectl get pods
# NAME                       READY   STATUS    RESTARTS   AGE
# web-app-6b7b8c7d4f-abc12   1/1     Running   0          30s
# web-app-6b7b8c7d4f-def34   1/1     Running   0          30s
# web-app-6b7b8c7d4f-ghi56   1/1     Running   0          30s
# Check services
kubectl get services
# NAME              TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)
# web-app-service   LoadBalancer   10.0.0.5       203.0.113.50    80:30080/TCP
# Watch pods in real-time
kubectl get pods -w
```

5 | Configuration Management
----------------------------

### ConfigMaps for Application Configuration

```
# app-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  # Simple key-value pairs
  DATABASE_HOST: "mysql.default.svc.cluster.local"
  DATABASE_PORT: "3306"
  APP_ENV: "production"
  LOG_LEVEL: "info"
  
  # Configuration file (multiline)
  nginx.conf: |
    server {
        listen 80;
        server_name localhost;
        
        location / {
            root /usr/share/nginx/html;
            index index.html;
        }
        
        location /health {
            access_log off;
            return 200 "healthy\n";
        }
    }
```

### Secrets for Sensitive Data

```
# app-secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
data:
  # Values must be base64 encoded
  DATABASE_USERNAME: YWRtaW4=           # echo -n 'admin' | base64
  DATABASE_PASSWORD: c3VwZXJzZWNyZXQ=   # echo -n 'supersecret' | base64
  API_KEY: bXktYXBpLWtleS0xMjM0NQ==     # echo -n 'my-api-key-12345' | base64
``````
# Create secrets from command line
kubectl create secret generic app-secrets \
  --from-literal=DATABASE_USERNAME=admin \
  --from-literal=DATABASE_PASSWORD=supersecret \
  --from-literal=API_KEY=my-api-key-12345
```

### Using ConfigMaps and Secrets in Deployments

```
# deployment-with-config.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-app
        image: nginx:1.21
        ports:
        - containerPort: 80
        
        # Environment variables from ConfigMap
        env:
        - name: DATABASE_HOST
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: DATABASE_HOST
        - name: APP_ENV
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_ENV
              
        # Environment variables from Secret
        - name: DATABASE_USERNAME
          valueFrom:
            secretKeyRef:
              name: app-secrets
              key: DATABASE_USERNAME
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: app-secrets
              key: DATABASE_PASSWORD
              
        # Mount config file
        volumeMounts:
        - name: config-volume
          mountPath: /etc/nginx/conf.d/default.conf
          subPath: nginx.conf
          
      volumes:
      - name: config-volume
        configMap:
          name: app-config
```

6 | Scaling and Load Balancing
------------------------------

### Manual Scaling

```
# Scale to 5 replicas
kubectl scale deployment web-app --replicas=5
# Scale down to 2 replicas
kubectl scale deployment web-app --replicas=2
# Check scaling status
kubectl get deployment web-app
kubectl get pods -l app=web-app
```

### Horizontal Pod Autoscaler (HPA)

```
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-app
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
``````
# Apply HPA
kubectl apply -f hpa.yaml
# Monitor HPA
kubectl get hpa
kubectl describe hpa web-app-hpa
# Watch scaling in action
kubectl get hpa -w
```

### How Auto-Scaling Works

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    KUBERNETES AUTOSCALING                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Normal Load (CPU ~30%)                                                │
│   ─────────────────────                                                 │
│                                                                         |
│   ┌─────┐  ┌─────┐  ┌─────┐                                             │
│   │ Pod │  │ Pod │  │ Pod │                                             │
│   │ 30% │  │ 30% │  │ 30% │    3 replicas (minimum)                     │
│   └─────┘  └─────┘  └─────┘                                             │
│                                                                         │
│                     ▼ Traffic increases                                 │
│                                                                         │
│   High Load (CPU ~80%)                                                  │
│   ────────────────────                                                  │
│                                                                         │
│   ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐                  │
│   │ Pod │  │ Pod │  │ Pod │  │ Pod │  │ Pod │  │ Pod │                  │
│   │ 80% │  │ 80% │  │ 80% │  │ 80% │  │ 80% │  │ 80% │                  │
│   └─────┘  └─────┘  └─────┘  └─────┘  └─────┘  └─────┘                  │
│                                                                         │
│   HPA automatically scaled to 6 replicas!                               │
│                                                                         │
│                     ▼ Traffic decreases                                 │
│                                                                         │
│   Back to Normal (CPU ~25%)                                             │
│   ─────────────────────────                                             │
│                                                                         │
│   ┌─────┐  ┌─────┐  ┌─────┐                                             │
│   │ Pod │  │ Pod │  │ Pod │                                             │
│   │ 25% │  │ 25% │  │ 25% │    Scaled back to 3 replicas                │
│   └─────┘  └─────┘  └─────┘                                             │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Ingress for Advanced Routing

```
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: portfolio-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - portfolio.example.com
    - api.portfolio.example.com
    secretName: portfolio-tls
  rules:
  - host: portfolio.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
  - host: api.portfolio.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: backend-service
            port:
              number: 8080
```

7 | Persistent Storage
----------------------

### PersistentVolume and PersistentVolumeClaim

```
# storage.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: standard  # Use cloud provider default
```

### Database Deployment with Persistent Storage

```
# mysql-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: root-password
        - name: MYSQL_DATABASE
          value: "portfolio_db"
        ports:
        - containerPort: 3306
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: mysql
  ports:
    - port: 3306
      targetPort: 3306
  type: ClusterIP  # Internal access only
```

8 | Monitoring and Troubleshooting
----------------------------------

### Essential kubectl Commands

```
# ===============================================
# VIEWING RESOURCES
# ===============================================
# Get all resources in current namespace
kubectl get all
# Get pods with more details
kubectl get pods -o wide
# Get pods across all namespaces
kubectl get pods --all-namespaces
# Watch pods in real-time
kubectl get pods -w
# ===============================================
# INSPECTING RESOURCES
# ===============================================
# Describe pod (detailed info + events)
kubectl describe pod <pod-name>
# Describe deployment
kubectl describe deployment <deployment-name>
# Get YAML of existing resource
kubectl get deployment web-app -o yaml
# ===============================================
# LOGS AND DEBUGGING
# ===============================================
# View pod logs
kubectl logs <pod-name>
# Follow logs in real-time
kubectl logs -f <pod-name>
# Logs from previous crashed container
kubectl logs <pod-name> --previous
# Logs from specific container in multi-container pod
kubectl logs <pod-name> -c <container-name>
# ===============================================
# INTERACTIVE DEBUGGING
# ===============================================
# Execute command in pod
kubectl exec <pod-name> -- ls /app
# Interactive shell in pod
kubectl exec -it <pod-name> -- /bin/bash
# Port forward to access pod locally
kubectl port-forward <pod-name> 8080:80
# Port forward to service
kubectl port-forward service/web-app-service 8080:80
# ===============================================
# RESOURCE USAGE
# ===============================================
# Node resource usage
kubectl top nodes
# Pod resource usage
kubectl top pods
# Sort by CPU
kubectl top pods --sort-by=cpu
# ===============================================
# EVENTS AND TROUBLESHOOTING
# ===============================================
# View cluster events
kubectl get events --sort-by=.metadata.creationTimestamp
# Events for specific pod
kubectl describe pod <pod-name> | grep -A 20 Events
```

### Common Issues and Solutions

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    KUBERNETES TROUBLESHOOTING GUIDE                     │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Pod Status: Pending                                                   │
│   ─────────────────────                                                 │
│   Causes:                                                               │
│   • Not enough resources (CPU/Memory)                                   │
│   • No nodes match pod's nodeSelector                                   │
│   • PersistentVolume not available                                      │
│                                                                         │
│   Debug:                                                                │
│   $ kubectl describe pod <pod-name>                                     │
│   $ kubectl get events                                                  │
│                                                                         │
│   ─────────────────────────────────────────────────────────────────     │
│                                                                         │
│   Pod Status: CrashLoopBackOff                                          │
│   ──────────────────────────────                                        │
│   Causes:                                                               │
│   • Application crash on startup                                        │
│   • Missing environment variables                                       │
│   • Failed health checks                                                │
│                                                                         │
│   Debug:                                                                │
│   $ kubectl logs <pod-name> --previous                                  │
│   $ kubectl describe pod <pod-name>                                     │
│                                                                         │
│   ─────────────────────────────────────────────────────────────────     │
│                                                                         │
│   Pod Status: ImagePullBackOff                                          │
│   ─────────────────────────────                                         │
│   Causes:                                                               │
│   • Image doesn't exist                                                 │
│   • Wrong image name/tag                                                │
│   • Private registry authentication failed                              │
│                                                                         │
│   Debug:                                                                │
│   $ kubectl describe pod <pod-name> | grep -i image                     │
│   $ docker pull <image-name>  # Test locally                            │
│                                                                         │
│   ─────────────────────────────────────────────────────────────────     │
│                                                                         │
│   Service Not Accessible                                                │
│   ──────────────────────                                                │
│   Causes:                                                               │
│   • Selector doesn't match pod labels                                   │
│   • Wrong port configuration                                            │
│   • Pods not ready                                                      │
│                                                                         │
│   Debug:                                                                │
│   $ kubectl get endpoints <service-name>                                │
│   $ kubectl describe service <service-name>                             │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Final Thoughts

You’ve Achieved Cloud-Native Status
-----------------------------------

Think about what you’ve accomplished:

Before Kubernetes, your portfolio was a single server: fragile, limited, manual. One crash, one traffic spike, one update = downtime.

Now your portfolio is **cloud-native**:

*   **High Availability**: Multiple replicas across nodes
*   **Auto-Scaling**: Handles traffic spikes automatically
*   **Self-Healing**: Crashed containers restart instantly
*   **Zero-Downtime Deploys**: Updates roll out seamlessly
*   **Infrastructure as Code**: Everything defined in YAML

Concluding Remarks
------------------

Kubernetes gives your application superpowers: automatic scaling, self-healing, and zero-downtime deployments. But there’s one more critical skill you need: **Java**.

![Kubernetes — The Container Orchestrator](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*hEuZBgN_WLS92jBrJcjeiQ.png)

Languages Portfolio Progress
----------------------------

✅ **01 — English**: Technical Communication And Prompt Engineering

✅ **02 — Mathematics:** Logic And Problem-Solving Fundamentals

✅ **03 — HTML:** Web Structure And Semantic Markup

✅ **04 — CSS:** Styling And Responsive Design

✅ **05 — JavaScript:** Interactive Web Development

✅ **06 — Python:** Backend Logic And Cloud Automation

✅ **07 — Git:** Version Control And Deployment

✅ **08 — Linux:** The Cloud Operating System

✅ **09 — SQL:** Cloud Data Management

✅ **10 — Kubernetes:** Container Orchestration At Scale

⬜ **11 — Java:** Enterprise Cloud Development

⬜ **12 — Terraform:** Infrastructure as Code

⬜ **Bonus — Agentic IDEs:** AI-Powered Cloud Development

Additional Resources
--------------------

### [**Kubernetes Documentation**](https://kubernetes.io/docs/home/)

> Kubernetes is an open source container orchestration engine for automating deployment, scaling, and management of containerized applications. The open source project is hosted by the Cloud Native Computing Foundation ([CNCF](https://www.cncf.io/about)).

### [**AWS EKS Documentation**](https://docs.aws.amazon.com/eks/)

> Amazon Elastic Kubernetes Service (Amazon EKS) is a managed service that makes it easy for you to run Kubernetes on AWS without needing to install and operate your own Kubernetes clusters.

### [**Google GKE Documentation**](https://cloud.google.com/kubernetes-engine/docs)

> Deploy, manage, and scale containerized applications on Kubernetes, powered by Google Cloud.

### [**Azure AKS Documentation**](https://docs.microsoft.com/en-us/azure/aks/)

> AKS allows you to quickly deploy a production ready Kubernetes cluster in Azure. Learn how to use AKS with these quickstarts, tutorials, and samples.


---
# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Kubernetes in the Cloud](https://ntombizakhona.medium.com/kubernetes-in-the-cloud-987d8d5c50ee)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**27 September 2026**
