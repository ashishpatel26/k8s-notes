# 🏆 Kubernetes Best Practices

A comprehensive guide to following industry best practices for deploying and managing applications in Kubernetes.

## 📋 Table of Contents
- [Security Best Practices](#security-best-practices)
- [Resource Management](#resource-management)
- [Application Design](#application-design)
- [Monitoring and Observability](#monitoring-and-observability)
- [Networking](#networking)
- [Storage](#storage)
- [CI/CD and GitOps](#cicd-and-gitops)
- [Cost Optimization](#cost-optimization)
- [Documentation](#documentation)

## 🔒 Security Best Practices

### 1. Use RBAC Properly
```yaml
# Always use specific roles instead of cluster-admin
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: production
  name: deployment-manager
rules:
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

### 2. Implement Pod Security Standards
```yaml
# Use Pod Security Admission
apiVersion: policy/v1
kind: PodSecurityPolicy
metadata:
  name: restricted-psp
spec:
  privileged: false
  allowPrivilegeEscalation: false
  requiredDropCapabilities:
    - ALL
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    - 'persistentVolumeClaim'
```

### 3. Use Sealed Secrets for Sensitive Data
```bash
# Always encrypt secrets at rest
kubectl create secret generic db-secret \
  --from-literal=password=$(openssl rand -base64 32) \
  --from-literal=username=admin
```

### 4. Image Security
```bash
# Use specific image tags instead of 'latest'
image: nginx:1.21.0

# Use minimal base images
image: alpine:3.15

# Scan images for vulnerabilities
trivy image your-image:tag
```

## 📊 Resource Management

### 1. Set Resource Requests and Limits
```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
      - name: app
        image: your-app:1.0
        resources:
          requests:
            cpu: "250m"
            memory: "256Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
```

### 2. Implement Horizontal Pod Autoscaler
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: app-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

### 3. Use Resource Quotas
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-resources
spec:
  hard:
    requests.cpu: "4"
    requests.memory: "8Gi"
    limits.cpu: "8"
    limits.memory: "16Gi"
    pods: "10"
```

## 🏗️ Application Design

### 1. Use Health Checks
```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
      - name: app
        image: your-app:1.0
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
```

### 2. Implement Proper Labels and Selectors
```yaml
# Use consistent labels across all resources
metadata:
  labels:
    app: my-app
    version: "1.0"
    environment: production
    team: backend
```

### 3. Use Init Containers for Setup
```yaml
apiVersion: v1
kind: Pod
spec:
  initContainers:
  - name: init-db
    image: busybox:1.35
    command: ['sh', '-c', 'until nslookup db-service; do echo waiting for db; sleep 2; done']
  containers:
  - name: app
    image: your-app:1.0
```

## 👁️ Monitoring and Observability

### 1. Use Prometheus Operator
```yaml
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: prometheus
spec:
  serviceAccountName: prometheus
  serviceMonitorSelector:
    matchLabels:
      team: backend
  podMonitorSelector:
    matchLabels:
      team: frontend
```

### 2. Implement Logging
```yaml
# Use sidecar containers for logging
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: app
    image: your-app:1.0
  - name: fluentd
    image: fluent/fluentd:v1.16
    volumeMounts:
    - name: logs
      mountPath: /var/log
```

### 3. Set Up Alerting
```yaml
# Use Prometheus AlertManager
apiVersion: monitoring.coreos.com/v1
kind: Alertmanager
metadata:
  name: alertmanager
spec:
  route:
    groupBy: ['alertname']
    groupWait: 30s
    groupInterval: 5m
    repeatInterval: 12h
    receiver: 'web.hook'
```

## 🌐 Networking

### 1. Use Network Policies
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: app-network-policy
spec:
  podSelector:
    matchLabels:
      app: my-app
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: production
    ports:
    - protocol: TCP
      port: 8080
```

### 2. Use Ingress Controllers
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  tls:
  - hosts:
    - app.example.com
    secretName: app-tls
  rules:
  - host: app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app-service
            port:
              number: 80
```

### 3. Use Service Mesh for Complex Applications
```yaml
# Use Istio for service-to-service communication
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: app-vs
spec:
  hosts:
  - app-service
  http:
  - route:
    - destination:
        host: app-service
        subset: v1
    - destination:
        host: app-service
        subset: v2
      weight: 20
```

## 💾 Storage

### 1. Use Persistent Volume Claims
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: fast-ssd
```

### 2. Use Storage Classes
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp3
  replication-type: none
allowVolumeExpansion: true
```

### 3. Backup Important Data
```bash
# Use Velero for backups
velero backup create app-backup \
  --include-namespaces production \
  --schedule "0 2 * * *" \
  --ttl 720h
```

## 🔄 CI/CD and GitOps

### 1. Use GitOps Principles
```yaml
# Use ArgoCD for GitOps
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
spec:
  source:
    repoURL: https://github.com/my-org/my-app.git
    targetRevision: HEAD
    path: manifests
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  project: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### 2. Use Helm Charts
```bash
# Package applications as Helm charts
helm package my-chart
helm install my-app ./my-chart-1.0.0.tgz
```

### 3. Use Kustomize for Environment Management
```yaml
# Use Kustomize for environment-specific configurations
base/
  deployment.yaml
  service.yaml
overlays/production/
  kustomization.yaml
  deployment.yaml
```

## 💰 Cost Optimization

### 1. Use Cluster Autoscaler
```yaml
apiVersion: autoscaling/v2beta2
kind: ClusterAutoscaler
metadata:
  name: cluster-autoscaler
spec:
  scaleDown:
    enabled: true
    delayAfterAdd: 10m
    delayAfterDelete: 10s
    delayAfterFailure: 10m
    unneededTime: 10m
```

### 2. Use Spot Instances
```yaml
# Use spot instances for fault-tolerant workloads
apiVersion: v1
kind: NodePool
metadata:
  name: spot-pool
spec:
  template:
    spec:
      taints:
      - key: "spot-instance"
        value: "true"
        effect: "NoSchedule"
```

### 3. Monitor Costs
```bash
# Use kubectl-cost for cost monitoring
kubectl cost --pod --all-namespaces
```

## 📚 Documentation

### 1. Document Your Applications
```markdown
# My Application

## Overview
This application provides...

## Architecture
- Frontend: React app
- Backend: Node.js API
- Database: PostgreSQL

## Deployment
- Namespace: production
- Replicas: 3
- Resource limits: 500m CPU, 1Gi memory

## Monitoring
- Prometheus metrics: /metrics
- Health checks: /health
- Logs: Fluentd sidecar
```

### 2. Use OpenAPI Specifications
```yaml
# Document APIs with OpenAPI
openapi: 3.0.0
info:
  title: My API
  version: 1.0.0
paths:
  /users:
    get:
      summary: Get users
      responses:
        '200':
          description: List of users
```

### 3. Keep Configuration in Git
```bash
# Always commit configuration changes
git add .
git commit -m "feat: update resource limits for production"
git push origin main
```

## 🚀 Performance Optimization

### 1. Use Caching
```yaml
# Use image pull secrets for private registries
apiVersion: v1
kind: Secret
metadata:
  name: registry-secret
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: <base64-encoded-registry-auth>
```

### 2. Optimize Container Images
```dockerfile
# Use multi-stage builds
FROM golang:1.21 as builder
WORKDIR /app
COPY . .
RUN go build -o app

FROM alpine:3.15
COPY --from=builder /app/app .
CMD ["./app"]
```

### 3. Use Resource Optimization Tools
```bash
# Use kubectl-neat to clean up YAML
kubectl-neat deployment.yaml > clean-deployment.yaml
```

## 🛡️ Disaster Recovery

### 1. Implement Backup Strategies
```bash
# Regular backups
velero schedule create daily-backup \
  --schedule "0 2 * * *" \
  --ttl 720h \
  --include-namespaces production,staging
```

### 2. Use Multi-Zone Deployments
```yaml
# Spread pods across availability zones
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - my-app
            topologyKey: "kubernetes.io/hostname"
```

### 3. Test Disaster Recovery
```bash
# Regular disaster recovery drills
velero restore create restore-1 \
  --from-backup daily-backup-20231201 \
  --namespace-mappings production:disaster-recovery
```

## 🔧 Operational Excellence

### 1. Use GitOps for All Changes
```bash
# Always use GitOps for deployments
argocd app sync my-app
argocd app wait my-app
```

### 2. Implement Canary Deployments
```yaml
# Use Istio for canary deployments
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: app-canary
spec:
  hosts:
  - app.example.com
  http:
  - route:
    - destination:
        host: app-service
        subset: stable
      weight: 90
    - destination:
        host: app-service
        subset: canary
      weight: 10
```

### 3. Use Automated Testing
```bash
# Test deployments before applying
kubectl apply --dry-run=client -f deployment.yaml
kubectl apply --server-dry-run -f deployment.yaml
```

## 📈 Continuous Improvement

### 1. Monitor Performance Metrics
```bash
# Use kubectl top for resource monitoring
kubectl top pods
kubectl top nodes
kubectl top deployments
```

### 2. Regular Security Audits
```bash
# Use kube-bench for security compliance
kube-bench run --targets=node
kube-bench run --targets=controls
```

### 3. Stay Updated
```bash
# Regularly update Kubernetes versions
kubectl get nodes -o wide
kubectl version --client
```

## 🎯 Summary

Following these best practices will help you:
- Improve security posture
- Optimize resource usage
- Enhance application reliability
- Reduce operational overhead
- Ensure compliance and governance
- Enable faster deployments
- Improve monitoring and observability

Remember to adapt these practices to your specific use case and organizational requirements.