🏗️ Let’s Build
---------------

### 9 | Deploy Your Portfolio to Kubernetes

Throughout this series, you’ve built:

*   **HTML/CSS/JavaScript:** Frontend interface
*   **Python:** Backend API with Flask
*   **Git**: Version control and GitHub
*   **Linux:** Deployed to the cloud
*   **SQL:** Database for persistent storage

Right now your portfolio runs on a single server. If that server goes down, your site goes down. Now let’s deploy your portfolio to **Kubernetes** with high availability, auto-scaling, and zero-downtime deployments!

### Portfolio Architecture on Kubernetes

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    PORTFOLIO ON KUBERNETES                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Internet                                                              │
│      │                                                                  │
│      ▼                                                                  │
│   ┌─────────────────┐                                                   │
│   │     Ingress     │  ← HTTPS + Domain routing                         │
│   │ portfolio.com   │                                                   │
│   └────────┬────────┘                                                   │
│            │                                                            │
│      ┌─────┴─────┐                                                      │
│      │           │                                                      │
│      ▼           ▼                                                      │
│   ┌────────┐  ┌────────────┐                                            │
│   │Frontend│  │ Backend    │                                            │
│   │Service │  │ Service    │                                            │
│   │  :80   │  │   :5000    │                                            │
│   └────┬───┘  └─────┬──────┘                                            │
│        │            │                                                   │
│   ┌────┴────┐  ┌────┴────┐                                              │
│   │         │  │         │                                              │
│   ▼         ▼  ▼         ▼                                              │
│ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐                                         │
│ │Nginx│ │Nginx│ │Flask│ │Flask│   ← Pods (auto-scaled)                  │
│ │ Pod │ │ Pod │ │ Pod │ │ Pod │                                         │
│ └─────┘ └─────┘ └─────┘ └─────┘                                         │
│                    │                                                    │
│                    ▼                                                    │
│              ┌──────────┐                                               │
│              │  MySQL   │  ← StatefulSet with PVC                       │
│              │  Service │                                               │
│              └──────────┘                                               │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Step 01: Project Structure

First, organize your portfolio for Kubernetes deployment:

```
portfolio-k8s/
├── frontend/
│   ├── Dockerfile
│   ├── nginx.conf
│   ├── index.html
│   ├── css/
│   │   └── styles.css
│   ├── js/
│   │   └── main.js
│   └── images/
│       └── profile.jpg
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── app.py
│   └── config.py
├── k8s/
│   ├── namespace.yaml
│   ├── secrets.yaml
│   ├── mysql.yaml
│   ├── backend.yaml
│   ├── frontend.yaml
│   ├── hpa.yaml
│   └── ingress.yaml
├── scripts/
│   ├── deploy.sh
│   └── init-db.sql
└── README.md
```

Before we deploy, let’s understand what each Kubernetes manifest file does, think of it like setting up a restaurant.

The **namespace.yaml** file acts like the restaurant name on the door, creating an isolated space called languages-portfolio that keeps all your resources organized and separate from other applications in the cluster.

The **secrets.yaml** file functions as the safe in the back office, securely storing sensitive information like database passwords where only authorized pods can access them.

The **mysql.yaml** file serves as your filing cabinet, deploying MySQL with persistent storage that survives pod restarts so your data is never lost.

The **backend.yaml** file represents your kitchen staff, running two copies of your Flask API that prepare and serve data to hungry users.

Similarly, **frontend.yaml** acts as the front-of-house staff, deploying two Nginx pods that greet visitors and serve your website’s HTML, CSS, and JavaScript.

The **hpa.yaml** file works like a manager who calls in extra staff when things get busy, automatically scaling your backend between 2 and 6 pods based on CPU usage.

Finally, **ingress.yaml** serves as the front door with your address on it, routing external internet traffic to the correct services based on domain names and URL paths.

**Create this structure:**

```
mkdir -p languagesportfolio/{frontend/{css,js,images},backend,k8s,scripts}
cd languagesportfolio
```

### Step 02: Install The Tools

**Windows (Recommended):** Docker Desktop — one install, everything included.

_Docker Desktop bundles Docker, Kubernetes, and kubectl in a single installer. No extra downloads, no application control headaches._

1.  Download Docker Desktop from [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/)
2.  Run the installer (accept defaults)
3.  Restart your computer when prompted
4.  Open Docker Desktop and wait for the whale icon in the system tray to say “Docker Desktop is running”

**Enable Kubernetes inside Docker Desktop:**

1.  Click the gear icon or **⚙️ Settings** in Docker Desktop
2.  Go to **Kubernetes** in the left sidebar
3.  Check **Enable Kubernetes**
4.  Click **Apply & Restart**
5.  Wait 2–3 minutes (or thirty, well, until it’s done). A green dot appears next to **Kubernetes** when it’s ready

_That’s it. Docker Desktop just gave you Docker + Kubernetes + kubectl in one step._

**macOS:** Same as above. Docker Desktop works the same way on Mac.

**Linux (alternative)**: Minikube

On Linux, you can use Minikube instead:

```
# Install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install kubectl /usr/local/bin/kubectl
# Start the cluster
minikube start --driver=docker --memory=4096 --cpus=2
```

Verify everything works (all platforms):

```
docker --version          # Docker version 29.x or higher
kubectl version --client  # Client Version: v1.28.x
```

### Step 03: Verify Your Kubernetes Cluster

If you enabled Kubernetes in Docker Desktop, your cluster is already running. Let’s confirm:

```
kubectl get nodes
# Expected output:
# NAME                    STATUS   ROLES           AGE   VERSION
# desktop-control-plane   Ready    control-plane   5m    v1.28.x
```

If you see **Ready**, you’re good. If not:

1.  Open Docker Desktop → Settings → Kubernetes → make sure the checkbox is ticked and the green dot is showing
2.  If it says _Starting_ just wait a minute or two or three

**For Linux/Minikube users:**

```
minikube start - driver=docker - memory=4096 - cpus=2
kubectl get nodes
# NAME STATUS ROLES AGE VERSION
# minikube Ready control-plane 1m v1.28.0
```

_You now have a working Kubernetes cluster. It’s like having a tiny AWS/Azure/GCP on your laptop._

### Step 04: Understand The Docker Files

Before Kubernetes can run your app, it needs container images. That’s what the Dockerfiles do.

**Frontend Dockerfile**

```
LanguagesPortfolio/frontend/Dockerfile
``````
FROM nginx:1.25-alpine
# Remove default nginx config
RUN rm /etc/nginx/conf.d/default.conf
# Copy custom nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf
# Copy frontend files
COPY index.html /usr/share/nginx/html/
COPY about.html /usr/share/nginx/html/
COPY css/ /usr/share/nginx/html/css/
COPY js/ /usr/share/nginx/html/js/
COPY images/ /usr/share/nginx/html/images/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

**What this does in plain English:**

1.  Start with a lightweight Nginx image
2.  Replace the default config with ours (which knows how to proxy `/api/` calls to the backend)
3.  Copy our HTML, CSS, JS, and images into the container
4.  Listen on port 80

**Backend Dockerfile**

```
LanguagesPortfolio/backend/Dockerfile
``````
FROM python:3.11-slim
WORKDIR /app
# Install dependencies first (for Docker layer caching)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
# Copy application code
COPY app.py .
COPY config.py .
COPY portfolio_manager.py .
# Create data directory for JSON persistence
RUN mkdir -p data
EXPOSE 5000
# Run with gunicorn for production
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "2", "app:app"]
```

**What this does:**

1.  Start with a Python 3.11 image
2.  Install Flask and other dependencies
3.  Copy the application code
4.  Run with gunicorn (a production-grade server, not Flask’s dev server)

### Step 05: Build The Docker Images

```
# Navigate to your project
cd LanguagesPortfolio
# Build the frontend image
docker build -t languages-portfolio-frontend:latest ./frontend
# Build the backend image
docker build -t languages-portfolio-backend:latest ./backend
```

**Test them locally first** _(optional but recommended)_:

```
# Run the backend
docker run -d -p 5000:5000 --name test-backend languages-portfolio-backend:latest
# Check it works
curl http://localhost:5000/api/health
# Should return: 
StatusCode        : 200
StatusDescription : OK
Content           : {"data":{"status":"healthy","timestamp":"2026-05-20T13:23:20.824932","uptime":"API is running normally"},"message":"Portfolio
                    API is healthy","success":true}
# Clean up
docker stop test-backend && docker rm test-backend
```

Docker Desktop’s Kubernetes shares the same Docker engine, so your images are already available to the cluster with no extra loading step needed.

**For Linux/Minikube users only** _(Minikube runs its own Docker, so it needs the images loaded separately)_

```
minikube image load languages-portfolio-frontend:latest
minikube image load languages-portfolio-backend:latest
```

### Step 06: Deploy To Kubernetes One File At A Time

This is the exciting part. We’ll apply each manifest file and explain what happens.

**6a. Create the namespace** (an isolated space for our app):

**Paste this into namespace.yaml:**

```
apiVersion: v1
kind: Namespace
metadata:
  name: languages-portfolio
  labels:
    app: languages-portfolio
    project: cloud-languages-blog
```

**Apply:**

```
kubectl apply -f k8s/namespace.yaml
```

**What happened?** _Kubernetes created a namespace called_ `_languages-portfolio_`_. Think of it as a folder that keeps our app's resources separate from everything else._

**Verify:**

```
kubectl get namespaces
# You should see 'languages-portfolio' in the list
```

**6b. Create the secrets** (database passwords):

**Paste this into secrets.yaml:**

```
# IMPORTANT: In production, use a secrets manager (AWS Secrets Manager, etc.)
# These base64 values are for local development only.
# Generate your own: echo -n 'your-value' | base64
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
  namespace: languages-portfolio
type: Opaque
data:
  # echo -n 'rootpassword123' | base64
  mysql-root-password: cm9vdHBhc3N3b3JkMTIz
  # echo -n 'portfolio_password' | base64
  mysql-password: cG9ydGZvbGlvX3Bhc3N3b3Jk
```

**Apply:**

```
kubectl apply -f k8s/secrets.yaml
```

**What happened?** _Kubernetes stored the MySQL passwords securely. Pods can read these values, but they’re not stored in plain text._

**6c. Deploy MySQL** (the database):

**Paste this into mysql.yaml:**

```
# --- PersistentVolumeClaim: gives MySQL a place to store data that survives restarts ---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
  namespace: languages-portfolio
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# --- Deployment: runs the MySQL container ---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
  namespace: languages-portfolio
  labels:
    app: mysql
spec:
  replicas: 1          # Databases typically run as a single instance
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
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-root-password
        - name: MYSQL_DATABASE
          value: "portfolio_db"
        - name: MYSQL_USER
          value: "portfolio_app"
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-password
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
        - name: init-script
          mountPath: /docker-entrypoint-initdb.d
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pvc
      - name: init-script
        configMap:
          name: mysql-init
---
# --- ConfigMap: holds the SQL init script ---
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-init
  namespace: languages-portfolio
data:
  init-db.sql: |
    CREATE DATABASE IF NOT EXISTS portfolio_db;
    USE portfolio_db;
    CREATE TABLE IF NOT EXISTS contacts (
        id INT PRIMARY KEY AUTO_INCREMENT,
        name VARCHAR(100) NOT NULL,
        email VARCHAR(255) NOT NULL,
        subject VARCHAR(200),
        message TEXT NOT NULL,
        ip_address VARCHAR(45),
        created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        is_read BOOLEAN DEFAULT FALSE
    );
    CREATE TABLE IF NOT EXISTS page_views (
        id BIGINT PRIMARY KEY AUTO_INCREMENT,
        page_path VARCHAR(500),
        referrer VARCHAR(500),
        user_agent TEXT,
        ip_address VARCHAR(45),
        viewed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    );
---
# --- Service: gives MySQL a stable network address inside the cluster ---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
  namespace: languages-portfolio
spec:
  selector:
    app: mysql
  ports:
  - port: 3306
    targetPort: 3306
  type: ClusterIP       # Only accessible inside the cluster
```

**Apply:**

```
kubectl apply -f k8s/mysql.yaml
```

**What happened?** _Kubernetes created:_

*   _A_ **_PersistentVolumeClaim_** _(1GB of disk space that survives pod restarts)_
*   _A_ **_ConfigMap_** _with the SQL init script (creates the tables)_
*   _A_ **_Deployment_** _running one MySQL 8.0 pod_
*   _A_ **_Service_** _called_ `_mysql-service_` _so other pods can connect to it_

**Wait for MySQL to be ready:**

```
kubectl get pods -n languages-portfolio -w
# Wait until you see:
# NAME                     READY   STATUS    RESTARTS   AGE
# mysql-xxxxxxxxxx-xxxxx   1/1     Running   0          30s
# Press Ctrl+C to stop watching
```

**6d. Deploy the backend** (Flask API)

**Paste this into backend.yaml:**

```
# --- Deployment: runs the Flask API containers ---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: languages-portfolio
  labels:
    app: backend
spec:
  replicas: 2            # Two copies for reliability
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: languages-portfolio-backend:latest
        imagePullPolicy: IfNotPresent    # Use local image in Minikube
        ports:
        - containerPort: 5000
        env:
        - name: DATABASE_HOST
          value: "mysql-service"         # Points to the MySQL Service name
        - name: DATABASE_PORT
          value: "3306"
        - name: DATABASE_NAME
          value: "portfolio_db"
        - name: DATABASE_USER
          value: "portfolio_app"
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-password
        - name: FLASK_DEBUG
          value: "False"
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "300m"
        # Kubernetes checks this endpoint to know if the container is alive
        livenessProbe:
          httpGet:
            path: /api/health
            port: 5000
          initialDelaySeconds: 15
          periodSeconds: 20
        # Kubernetes checks this endpoint before sending traffic
        readinessProbe:
          httpGet:
            path: /api/health
            port: 5000
          initialDelaySeconds: 5
          periodSeconds: 10
---
# --- Service: gives the backend a stable address for the frontend to reach ---
apiVersion: v1
kind: Service
metadata:
  name: backend-service
  namespace: languages-portfolio
spec:
  selector:
    app: backend
  ports:
  - port: 5000
    targetPort: 5000
  type: ClusterIP        # Only accessible inside the cluster (frontend proxies to it)
```

**Apply:**

```
kubectl apply -f k8s/backend.yaml
```

**What happened?** _Kubernetes created:_

*   A **Deployment** running 2 Flask API pods
*   Environment variables pointing to `mysql-service` for database access
*   **Health checks** so Kubernetes knows if a pod is alive and ready
*   A **Service** called `backend-service` for the frontend to reach

**6e. Deploy the frontend** (Nginx)

**Paste this into frontend.yaml:**

```
# --- Deployment: runs the Nginx frontend containers ---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: languages-portfolio
  labels:
    app: frontend
spec:
  replicas: 2            # Two copies for reliability
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: languages-portfolio-frontend:latest
        imagePullPolicy: IfNotPresent    # Use local image in Minikube
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "50m"
          limits:
            memory: "128Mi"
            cpu: "200m"
        livenessProbe:
          httpGet:
            path: /healthz
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 15
        readinessProbe:
          httpGet:
            path: /healthz
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 10
---
# --- Service: exposes the frontend to the outside world ---
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
  namespace: languages-portfolio
spec:
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer      # Makes the frontend accessible from outside the cluster
```

**Apply:**

```
kubectl apply -f k8s/frontend.yaml
```

**What happened?** _Kubernetes created:_

*   A **Deployment** running 2 Nginx pods
*   A **LoadBalancer Service** that exposes the frontend to the outside world

**6f. Apply the autoscaler**

**Paste this into hpa.yaml:**

```
# --- HorizontalPodAutoscaler: automatically adds/removes backend pods based on CPU ---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: backend-hpa
  namespace: languages-portfolio
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: backend
  minReplicas: 2         # Never go below 2 pods
  maxReplicas: 6         # Never go above 6 pods
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70   # Scale up when average CPU > 70%
```

**Apply:**

```
kubectl apply -f k8s/hpa.yaml
```

**What happened?** _Kubernetes will now automatically scale the backend between 2 and 6 pods based on CPU usage._

### Step 07: Check Everything Is Running

```
# See all pods
kubectl get pods -n languages-portfolio
# Expected output:
# NAME                        READY   STATUS    RESTARTS   AGE
# mysql-xxxxxxxxxx-xxxxx      1/1     Running   0          2m
# backend-xxxxxxxxxx-xxxxx    1/1     Running   0          1m
# backend-xxxxxxxxxx-yyyyy    1/1     Running   0          1m
# frontend-xxxxxxxxxx-xxxxx   1/1     Running   0          45s
# frontend-xxxxxxxxxx-yyyyy   1/1     Running   0          45s
# See all services
kubectl get services -n languages-portfolio
# Expected output:
# NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)
# mysql-service      ClusterIP      10.96.x.x       <none>        3306/TCP
# backend-service    ClusterIP      10.96.x.x       <none>        5000/TCP
# frontend-service   LoadBalancer   10.96.x.x       <pending>     80:3xxxx/TCP
# See the autoscaler
kubectl get hpa -n languages-portfolio
```

### Step 08: Open Your Portfolio In The Browser

**Docker Desktop (Windows/Mac):** The LoadBalancer service maps to `localhost` automatically. Just open your browser:

```
http://localhost
```

If port 80 is already in use on your machine, check which port was assigned:

```
kubectl get services -n languages-portfolio
# Look at the PORT(S) column for frontend-service
# e.g. 80:31234/TCP means you can also use http://localhost:31234
```

**Test the API through the frontend’s Nginx proxy:**

```
curl http://localhost/api/health
```

**For Linux/Minikube users:**

```
minikube service frontend-service -n languages-portfolio
```

### Step 09: See Kubernetes Self-Healing in Action

his is the magic. Let’s kill a pod and watch Kubernetes bring it back:

```
# List the backend pods
kubectl get pods -n languages-portfolio -l app=backend
# Delete one of them (copy a pod name from above)
kubectl delete pod <pod-name> -n languages-portfolio
# Immediately watch what happens
kubectl get pods -n languages-portfolio -l app=backend -w
# You'll see:
# 1. The pod enters "Terminating" state
# 2. A NEW pod is created automatically
# 3. The new pod goes from "ContainerCreating" → "Running"
# 4. Your website never went down because the other pod was still serving traffic!
```

This is why Kubernetes is powerful. You told it “I want 2 backend pods” and it will always maintain that, no matter what.

### Step 10: Scale Your Application

**Manual scaling:**

```
# Scale frontend to 4 pods
kubectl scale deployment frontend --replicas=4 -n languages-portfolio
# Watch the new pods appear
kubectl get pods -n languages-portfolio -l app=frontend -w
# Scale back down
kubectl scale deployment frontend --replicas=2 -n languages-portfolio
```

**The autoscaler (HPA) does this automatically for the backend.** Check its status:

```
kubectl get hpa -n languages-portfolio
# Output:
# NAME          REFERENCE            TARGETS   MINPODS   MAXPODS   REPLICAS
# backend-hpa   Deployment/backend   10%/70%   2         6         2
```

When CPU usage goes above 70%, Kubernetes automatically adds more backend pods. When traffic drops, it removes them. You pay for what you use.

### Step 11: The One-Command Deploy Script

Instead of running each `kubectl apply` manually, use the deploy script:

```
#!/bin/bash
# deploy.sh — Deploy the LanguagesPortfolio to Kubernetes
# Usage: ./scripts/deploy.sh
set -e   # Stop on any error
echo ""
echo "=========================================="
echo "  LanguagesPortfolio — K8s Deployment"
echo "=========================================="
echo ""
# ------------------------------------------
# Step 1: Build Docker images
# ------------------------------------------
echo "📦 Building Docker images..."
echo "  → Building frontend image..."
docker build -t languages-portfolio-frontend:latest ./frontend
echo "  → Building backend image..."
docker build -t languages-portfolio-backend:latest ./backend
echo "  ✅ Images built successfully"
echo ""
# ------------------------------------------
# Step 2: Load images into Minikube (local only)
# ------------------------------------------
if command -v minikube &> /dev/null; then
    echo "📤 Loading images into Minikube..."
    minikube image load languages-portfolio-frontend:latest
    minikube image load languages-portfolio-backend:latest
    echo "  ✅ Images loaded into Minikube"
    echo ""
fi
# ------------------------------------------
# Step 3: Apply Kubernetes manifests (order matters!)
# ------------------------------------------
echo "🚀 Applying Kubernetes manifests..."
echo "  → Creating namespace..."
kubectl apply -f k8s/namespace.yaml
echo "  → Creating secrets..."
kubectl apply -f k8s/secrets.yaml
echo "  → Deploying MySQL database..."
kubectl apply -f k8s/mysql.yaml
echo "  → Waiting for MySQL to be ready..."
kubectl wait --for=condition=available --timeout=120s deployment/mysql -n languages-portfolio
echo "  → Deploying backend API..."
kubectl apply -f k8s/backend.yaml
echo "  → Deploying frontend..."
kubectl apply -f k8s/frontend.yaml
echo "  → Applying autoscaler..."
kubectl apply -f k8s/hpa.yaml
echo ""
# ------------------------------------------
# Step 4: Wait for everything to be ready
# ------------------------------------------
echo "⏳ Waiting for all deployments to be ready..."
kubectl wait --for=condition=available --timeout=120s deployment/backend -n languages-portfolio
kubectl wait --for=condition=available --timeout=120s deployment/frontend -n languages-portfolio
echo ""
# ------------------------------------------
# Step 5: Show status
# ------------------------------------------
echo "=========================================="
echo "  ✅ Deployment Complete!"
echo "=========================================="
echo ""
echo "📋 Pods:"
kubectl get pods -n languages-portfolio
echo ""
echo "🌐 Services:"
kubectl get services -n languages-portfolio
echo ""
# If using Minikube, show the URL
if command -v minikube &> /dev/null; then
    echo "🔗 To open the portfolio in your browser, run:"
    echo "   minikube service frontend-service -n languages-portfolio"
fi
``````
# Make it executable (Linux/macOS)
chmod +x scripts/deploy.sh
# Run it
./scripts/deploy.sh
```

This script (`LanguagesPortfolio/scripts/deploy.sh`) does everything in order:

1.  Builds both Docker images
2.  Loads them into Minikube (skipped on Docker Desktop)
3.  Applies all Kubernetes manifests in the correct order
4.  Waits for everything to be ready
5.  Shows you the final status

### ⛔ End of Building Tutorial⛔

10 | Useful Commands for Troubleshooting
----------------------------------------

When things don’t work (and they will, that’s normal), here’s how to debug:

```
# See what's happening with a pod
kubectl describe pod <pod-name> -n languages-portfolio
# Read a pod's logs (like reading console.log or print statements)
kubectl logs <pod-name> -n languages-portfolio
# Follow logs in real-time (like tail -f)
kubectl logs -f <pod-name> -n languages-portfolio
# Open a shell inside a running pod (like SSH-ing into a server)
kubectl exec -it <pod-name> -n languages-portfolio -- /bin/sh
# See events (Kubernetes tells you what it's doing)
kubectl get events -n languages-portfolio --sort-by=.metadata.creationTimestamp
# Check resource usage
kubectl top pods -n languages-portfolio
```

### **Common Issues And Fixes:**

1.  If a pod is stuck in `**Pending**`, use `kubectl describe pod <name>` and check whether Docker Desktop needs more CPU or memory.
2.  If a pod is in `**CrashLoopBackOff**`, run `kubectl logs <name>` to find application errors such as a Python traceback.
3.  If a pod shows `**ImagePullBackOff**`, use `kubectl describe pod <name>` and rebuild or load the missing Docker image.
4.  If a **Service has no endpoints**, run `kubectl get endpoints <svc>` and check that its selector matches the pod labels.
5.  If the application **cannot connect to the database**, check the backend and MySQL pod logs to determine whether MySQL is ready

11 | Clean Up Protocol
----------------------

Since Kubernetes is basically roadkill for a Portfolio Project, so when you’re done experimenting:

```
# Delete everything in the namespace
kubectl delete namespace languages-portfolio
```

### **Docker Desktop**

Kubernetes keeps running in the background. To disable it, go to Docker Desktop → Settings → Kubernetes → uncheck “Enable Kubernetes”.

### **Minikube (Linux)**

```
minikube stop     # Pause the cluster (saves resources)
minikube delete   # Remove the cluster entirel
```

12 | CI/CD Integration (Optional Bonus)
---------------------------------------

### GitHub Actions for Kubernetes Deployment

```
# .github/workflows/deploy.yml
name: Deploy to Kubernetes
on:
  push:
    branches: [main]
env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Log in to Container Registry
      uses: docker/login-action@v2
      with:
        registry: ${{ env.REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
    - name: Build and push Backend
      uses: docker/build-push-action@v4
      with:
        context: ./backend
        push: true
        tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/backend:${{ github.sha }}
    - name: Build and push Frontend
      uses: docker/build-push-action@v4
      with:
        context: ./frontend
        push: true
        tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/frontend:${{ github.sha }}    - name: Configure kubectl
      uses: azure/k8s-set-context@v3
      with:
        method: kubeconfig
        kubeconfig: ${{ secrets.KUBE_CONFIG }}
    - name: Deploy to Kubernetes
      run: |
        # Update image tags
        sed -i "s|IMAGE_TAG|${{ github.sha }}|g" k8s/*.yaml
        # Apply manifests
        kubectl apply -f k8s/
        # Wait for rollout
        kubectl rollout status deployment/backend -n portfolio
        kubectl rollout status deployment/frontend -n portfolio
```

### Rolling Updates (Zero Downtime)

```
# deployment-strategy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # One extra pod during update
      maxUnavailable: 0  # Never reduce below desired count
  # ... rest of deployment
# Trigger a rolling update
kubectl set image deployment/backend backend=your-registry/backend:v2
# Watch the rollout
kubectl rollout status deployment/backend
# Rollback if something goes wrong
kubectl rollout undo deployment/backend
# View rollout history
kubectl rollout history deployment/backend
```

---
# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Kubernetes in the Cloud](https://ntombizakhona.medium.com/kubernetes-in-the-cloud-987d8d5c50ee)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**27 September 2026**
