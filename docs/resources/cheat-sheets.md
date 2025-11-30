# 🚀 Kubernetes Cheat Sheets

Quick reference guides for common Kubernetes operations and commands.

## Basic Kubernetes Commands

### Cluster Information
```bash
# Get cluster info
kubectl cluster-info

# View nodes
kubectl get nodes

# View node details
kubectl describe node <node-name>

# View cluster version
kubectl version --short
```

### Pod Operations
```bash
# Get pods
kubectl get pods

# Get pods with more details
kubectl get pods -o wide

# Describe pod
kubectl describe pod <pod-name>

# Delete pod
kubectl delete pod <pod-name>

# View pod logs
kubectl logs <pod-name>

# Follow pod logs
kubectl logs -f <pod-name>

# Execute command in pod
kubectl exec -it <pod-name> -- /bin/bash
```

### Deployment Management
```bash
# Get deployments
kubectl get deployments

# Create deployment from file
kubectl create -f deployment.yaml

# Apply changes
kubectl apply -f deployment.yaml

# Scale deployment
kubectl scale deployment/<deployment-name> --replicas=3

# Rollback deployment
kubectl rollout undo deployment/<deployment-name>

# Check rollout status
kubectl rollout status deployment/<deployment-name>
```

### YAML Management
```bash
# Get pod as YAML
kubectl get pod <pod-name> -o yaml

# Get pod as JSON
kubectl get pod <pod-name> -o json

# Dry run apply
kubectl apply --dry-run=client -f deployment.yaml

# Validate YAML
kubectl apply --dry-run=client -f deployment.yaml
```

## Common YAML Snippets

### Basic Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    ports:
    - containerPort: 80
```

### Basic Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

### Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

### ConfigMap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: "production"
  APP_DEBUG: "false"
  config.json: |
    {
      "database": {
        "host": "db-service",
        "port": 5432
      }
    }
```

### Secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
data:
  username: YWRtaW4=  # admin (base64 encoded)
  password: MWYyZDFlMmU2N2Rm  # 1f2d1e2e67df (base64 encoded)
```

## Port Forwarding
```bash
# Port forward to a pod
kubectl port-forward svc/<service-name> 8080:80

# Port forward to deployment
kubectl port-forward deployment/<deployment-name> 8080:80
```

## Resource Limits
```bash
# Set resource limits on pod
kubectl set resources deployment/<deployment-name> --limits=cpu=500m,memory=512Mi --requests=cpu=250m,memory=256Mi
```

## Context and Namespace
```bash
# Get current context
kubectl config current-context

# Switch context
kubectl config use-context <context-name>

# Get namespaces
kubectl get namespaces

# Set namespace for current session
kubectl config set-context --current --namespace=<namespace-name>

# Apply with namespace
kubectl apply -f deployment.yaml -n <namespace-name>
```

## Common Troubleshooting Commands
```bash
# Get all resources in namespace
kubectl get all

# Get events
kubectl get events

# Check pod status with previous state
kubectl get pods --show-labels

# Check pod readiness
kubectl get pods -o jsonpath='{.items[*].status.containerStatuses[*].ready}'

# Check pod restarts
kubectl get pods --sort-by='.status.containerStatuses[0].restartCount'