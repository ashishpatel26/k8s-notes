# 🔧 Kubernetes Troubleshooting Guide

A comprehensive guide to diagnosing and resolving common Kubernetes issues.

## 📋 Table of Contents
- [Common Pod Issues](#common-pod-issues)
- [Networking Problems](#networking-problems)
- [Storage Issues](#storage-issues)
- [Performance Problems](#performance-problems)
- [Security Issues](#security-issues)
- [Cluster Problems](#cluster-problems)
- [Debugging Tools](#debugging-tools)
- [Common Error Codes](#common-error-codes)

## 🚨 Common Pod Issues

### 1. Pod CrashLoopBackOff
**Symptoms**: Pod keeps restarting with CrashLoopBackOff status.

**Diagnosis**:
```bash
# Check pod status
kubectl get pods

# View pod events
kubectl describe pod <pod-name>

# Check pod logs
kubectl logs <pod-name> --previous

# Check container logs
kubectl logs <pod-name> -c <container-name>
```

**Solutions**:
- Check application logs for errors
- Verify resource limits are sufficient
- Check image availability and permissions
- Verify environment variables and configuration

**Example Fix**:
```bash
# Check if image can be pulled
kubectl describe pod <pod-name> | grep -A 5 "Events"

# Check resource usage
kubectl top pod <pod-name>
```

### 2. Pod Pending
**Symptoms**: Pod stays in Pending state.

**Diagnosis**:
```bash
# Check pod events
kubectl describe pod <pod-name>

# Check node resources
kubectl describe nodes

# Check taints on nodes
kubectl describe nodes | grep Taints
```

**Solutions**:
- Check if nodes have sufficient resources
- Remove taints from nodes if needed
- Check if PVC can be bound
- Verify node selectors and affinity rules

**Example Fix**:
```bash
# Remove taint from node
kubectl taint nodes <node-name> <taint-key>:NoSchedule-

# Check PVC status
kubectl get pvc
```

### 3. ImagePullBackOff
**Symptoms**: Pod can't pull container image.

**Diagnosis**:
```bash
# Check image pull events
kubectl describe pod <pod-name> | grep -A 10 "Events"

# Check image registry access
kubectl get secret <image-pull-secret> -o yaml
```

**Solutions**:
- Verify image name and tag
- Check registry credentials
- Ensure registry is accessible
- Check image size and pull limits

**Example Fix**:
```bash
# Test image pull manually
docker pull <image-name>

# Update image pull secret
kubectl create secret docker-registry regcred \
  --docker-server=<registry-url> \
  --docker-username=<username> \
  --docker-password=<password>
```

### 4. ContainerCreating
**Symptoms**: Pod stuck in ContainerCreating state.

**Diagnosis**:
```bash
# Check pod events
kubectl describe pod <pod-name>

# Check node status
kubectl get nodes -o wide

# Check kubelet logs
journalctl -u kubelet -f
```

**Solutions**:
- Check kubelet status on nodes
- Verify container runtime (Docker/Containerd)
- Check disk space on nodes
- Verify kernel parameters

**Example Fix**:
```bash
# Restart kubelet
systemctl restart kubelet

# Check disk space
df -h
```

## 🌐 Networking Problems

### 1. Service Not Accessible
**Symptoms**: Service endpoints are not reachable.

**Diagnosis**:
```bash
# Check service endpoints
kubectl get endpoints <service-name>

# Check service configuration
kubectl describe service <service-name>

# Test connectivity
kubectl exec -it <pod-name> -- wget -qO- <service-name>
```

**Solutions**:
- Verify service selector matches pod labels
- Check service type and port configuration
- Verify network policies
- Check DNS resolution

**Example Fix**:
```bash
# Test DNS resolution
kubectl exec -it <pod-name> -- nslookup <service-name>

# Check network policies
kubectl get networkpolicy
```

### 2. Ingress Not Working
**Symptoms**: Ingress doesn't route traffic to services.

**Diagnosis**:
```bash
# Check ingress status
kubectl get ingress <ingress-name>

# Check ingress controller logs
kubectl logs -n ingress-nginx <ingress-controller-pod>

# Test connectivity
curl -H "Host: <domain>" http://<ingress-ip>
```

**Solutions**:
- Verify ingress annotations
- Check TLS certificate configuration
- Ensure ingress controller is running
- Verify domain DNS configuration

**Example Fix**:
```bash
# Check ingress controller status
kubectl get pods -n ingress-nginx

# Test ingress configuration
kubectl describe ingress <ingress-name>
```

### 3. Network Policy Blocking Traffic
**Symptoms**: Pods can't communicate with each other.

**Diagnosis**:
```bash
# Check network policies
kubectl get networkpolicy -o wide

# Test connectivity between pods
kubectl exec -it <source-pod> -- ping <target-pod>

# Check pod labels
kubectl get pods --show-labels
```

**Solutions**:
- Review network policy rules
- Verify pod labels match policy selectors
- Check ingress/egress rules
- Test policy changes incrementally

**Example Fix**:
```bash
# Temporarily disable network policy
kubectl delete networkpolicy <policy-name>

# Test connectivity without policy
kubectl exec -it <source-pod> -- wget -qO- <target-service>
```

## 💾 Storage Issues

### 1. PVC Bound Failed
**Symptoms**: PVC stays in Pending state.

**Diagnosis**:
```bash
# Check PVC status
kubectl get pvc

# Check PV status
kubectl get pv

# Check storage class
kubectl get storageclass
```

**Solutions**:
- Verify storage class configuration
- Check available PVs
- Ensure storage backend is accessible
- Check PVC resource requests

**Example Fix**:
```bash
# Check storage class provisioner
kubectl get storageclass -o yaml

# Create PV manually if needed
kubectl create -f pv.yaml
```

### 2. ReadWriteOnce vs ReadOnlyMany
**Symptoms**: Can't write to mounted volume.

**Diagnosis**:
```bash
# Check PVC access mode
kubectl get pvc <pvc-name> -o yaml

# Check PV access mode
kubectl get pv <pv-name> -o yaml
```

**Solutions**:
- Use correct access mode for workload
- Create new PVC with appropriate mode
- Use ReadWriteMany for shared storage

**Example Fix**:
```bash
# Create new PVC with ReadWriteOnce
kubectl create -f new-pvc.yaml
```

### 3. Volume Mount Issues
**Symptoms**: Container can't access mounted volume.

**Diagnosis**:
```bash
# Check pod events
kubectl describe pod <pod-name>

# Check volume mount configuration
kubectl get pod <pod-name> -o yaml | grep -A 10 volumeMounts
```

**Solutions**:
- Verify mount path exists
- Check permissions on volume
- Ensure volume is properly formatted
- Test volume access manually

**Example Fix**:
```bash
# Check volume permissions
kubectl exec -it <pod-name> -- ls -la /mount/path

# Fix permissions if needed
kubectl exec -it <pod-name> -- chmod 755 /mount/path
```

## ⚡ Performance Problems

### 1. High CPU Usage
**Symptoms**: Pods or nodes have high CPU usage.

**Diagnosis**:
```bash
# Check resource usage
kubectl top pods
kubectl top nodes

# Check pod resource limits
kubectl get pod <pod-name> -o yaml | grep -A 5 resources

# Check container metrics
kubectl top pod <pod-name> --containers
```

**Solutions**:
- Increase resource limits
- Optimize application code
- Scale horizontally
- Use vertical pod autoscaler

**Example Fix**:
```bash
# Update resource limits
kubectl set resources deployment/<deployment-name> \
  --limits=cpu=1000m,memory=2Gi \
  --requests=cpu=500m,memory=1Gi
```

### 2. Memory Issues
**Symptoms**: Pods are OOMKilled or have high memory usage.

**Diagnosis**:
```bash
# Check memory usage
kubectl top pods --sort-by=memory

# Check OOM events
kubectl describe pod <pod-name> | grep -i "oom"

# Check memory limits
kubectl get pod <pod-name> -o yaml | grep -A 5 resources
```

**Solutions**:
- Increase memory limits
- Optimize memory usage
- Use memory-efficient images
- Implement proper caching

**Example Fix**:
```bash
# Update memory limits
kubectl set resources deployment/<deployment-name> \
  --limits=memory=2Gi \
  --requests=memory=1Gi
```

### 3. Slow Response Times
**Symptoms**: Applications are responding slowly.

**Diagnosis**:
```bash
# Check network latency
kubectl exec -it <pod-name> -- ping <target-service>

# Check database connectivity
kubectl exec -it <pod-name> -- mysql -h <db-host> -u <user> -p

# Check application logs
kubectl logs <pod-name> --tail=100
```

**Solutions**:
- Optimize database queries
- Use caching strategies
- Scale horizontally
- Check network configuration

**Example Fix**:
```bash
# Check database performance
kubectl exec -it <pod-name> -- mysql -e "SHOW PROCESSLIST;"
```

## 🔒 Security Issues

### 1. RBAC Problems
**Symptoms**: Service accounts can't access resources.

**Diagnosis**:
```bash
# Check service account
kubectl get serviceaccount <sa-name>

# Check role bindings
kubectl get rolebinding <rb-name>

# Test permissions
kubectl auth can-i create pods --as=system:serviceaccount:<namespace>:<sa-name>
```

**Solutions**:
- Verify role permissions
- Check role bindings
- Ensure service account exists
- Test permissions incrementally

**Example Fix**:
```bash
# Create role binding
kubectl create rolebinding <rb-name> \
  --role=<role-name> \
  --serviceaccount=<namespace>:<sa-name>
```

### 2. Image Security Issues
**Symptoms**: Images have vulnerabilities.

**Diagnosis**:
```bash
# Scan images for vulnerabilities
trivy image <image-name>

# Check image signatures
cosign verify <image-name>

# Check image pull policy
kubectl get pod <pod-name> -o yaml | grep imagePullPolicy
```

**Solutions**:
- Use base images with fewer vulnerabilities
- Implement image scanning
- Use signed images
- Update regularly

**Example Fix**：
```bash
# Update image to latest secure version
kubectl set image deployment/<deployment-name> \
  <container-name>=<new-image>:<tag>
```

### 3. Network Policy Issues
**Symptoms**: Pods can't communicate due to security policies.

**Diagnosis**:
```bash
# Check network policies
kubectl get networkpolicy

# Test connectivity
kubectl exec -it <source-pod> -- wget -qO- <target-service>

# Check pod labels
kubectl get pods --show-labels
```

**Solutions**:
- Review network policy rules
- Verify pod labels
- Test policy changes
- Use least privilege principle

**Example Fix**:
```bash
# Create permissive network policy for testing
kubectl create -f permissive-netpol.yaml
```

## 🏗️ Cluster Problems

### 1. Node Not Ready
**Symptoms**: Node shows NotReady status.

**Diagnosis**:
```bash
# Check node status
kubectl get nodes

# Check node conditions
kubectl describe node <node-name>

# Check kubelet status
systemctl status kubelet
```

**Solutions**:
- Restart kubelet service
- Check container runtime
- Verify network configuration
- Check disk space

**Example Fix**:
```bash
# Restart kubelet
systemctl restart kubelet

# Check container runtime
systemctl status containerd
```

### 2. API Server Issues
**Symptoms**: Can't access Kubernetes API.

**Diagnosis**:
```bash
# Check API server status
kubectl cluster-info

# Check API server logs
kubectl logs -n kube-system <api-server-pod>

# Test API connectivity
curl -k https://<api-server-ip>:6443/healthz
```

**Solutions**:
- Check API server resources
- Verify certificate validity
- Check network connectivity
- Restart API server if needed

**Example Fix**:
```bash
# Check API server resources
kubectl top pods -n kube-system
```

### 3. etcd Issues
**Symptoms**: Cluster state is inconsistent.

**Diagnosis**:
```bash
# Check etcd status
kubectl get endpoints -n kube-system kube-etcd

# Check etcd logs
kubectl logs -n kube-system <etcd-pod>

# Check cluster health
kubectl get componentstatuses
```

**Solutions**:
- Check etcd disk space
- Verify etcd cluster members
- Backup etcd data
- Restore from backup if needed

**Example Fix**:
```bash
# Check etcd cluster members
ETCDCTL_API=3 etcdctl --endpoints=<etcd-endpoint> member list
```

## 🔧 Debugging Tools

### 1. kubectl Debug Plugin
```bash
# Create debug container
kubectl debug -it <pod-name> --image=busybox --target=<container-name>

# Create debug pod
kubectl debug -it <pod-name> --image=busybox --share-processes
```

### 2. Lens IDE
```bash
# Use Lens for visual debugging
# Download from: https://k8slens.dev/
# Features:
# - Visual pod status
# - Real-time logs
# - Resource monitoring
# - Network topology
```

### 3. k9s
```bash
# Interactive Kubernetes CLI
k9s

# Common k9s commands:
# - :pods - View pods
# - :deploy - View deployments
# - :logs - View logs
# - :exec - Execute command
# - :describe - Describe resource
```

### 4. stern
```bash
# Multi-container log tailing
stern <pod-name> -n <namespace>

# Filter by container
stern <pod-name> -c <container-name>

# Filter by namespace
stern <pod-name> -n <namespace>
```

## 🚨 Common Error Codes

### 1. Error from server (Forbidden)
**Cause**: Insufficient permissions.

**Solution**:
```bash
# Check current context
kubectl config current-context

# Check permissions
kubectl auth can-i create deployments

# Create role binding if needed
kubectl create clusterrolebinding cluster-admin-binding \
  --clusterrole=cluster-admin \
  --user=<username>
```

### 2. Error from server (NotFound)
**Cause**: Resource doesn't exist.

**Solution**:
```bash
# Check if resource exists
kubectl get <resource-type> <resource-name>

# Check namespace
kubectl config current-context

# Create resource if needed
kubectl create -f <resource-file>
```

### 3. Error from server (AlreadyExists)
**Cause**: Resource already exists.

**Solution**:
```bash
# Check existing resource
kubectl get <resource-type> <resource-name>

# Apply changes instead of create
kubectl apply -f <resource-file>

# Delete and recreate if needed
kubectl delete <resource-type> <resource-name>
kubectl create -f <resource-file>
```

### 4. Error from server (Timeout)
**Cause**: Operation took too long.

**Solution**:
```bash
# Increase timeout
kubectl get <resource-type> --watch --timeout=60s

# Check resource status
kubectl describe <resource-type> <resource-name>

# Check cluster health
kubectl get nodes
```

## 📊 Monitoring and Alerting

### 1. Prometheus Alerts
```yaml
# Example alert for high CPU usage
groups:
- name: kubernetes-apps
  rules:
  - alert: HighCPUUsage
    expr: sum(rate(container_cpu_usage_seconds_total[5m])) by (pod) > 0.8
    for: 5m
    labels:
      severity: warning
    annotations:
      summary: "High CPU usage on {{ $labels.pod }}"
      description: "Pod {{ $labels.pod }} has high CPU usage"
```

### 2. Grafana Dashboards
```bash
# Import pre-built dashboards
kubectl apply -f https://raw.githubusercontent.com/kubernetes-monitoring/kubernetes-mixin/master/dashboards/kubernetes.json
```

### 3. Logging Setup
```yaml
# Fluentd configuration for logging
apiVersion: v1
kind: ConfigMap
metadata:
  name: fluentd-config
data:
  fluent.conf: |
    <source>
      @type tail
      path /var/log/containers/*.log
      pos_file /var/log/fluentd-containers.log.pos
      tag kubernetes.*
      format json
      time_format %Y-%m-%dT%H:%M:%S.%NZ
    </source>
```

## 🔄 Recovery Procedures

### 1. Pod Recovery
```bash
# Restart deployment
kubectl rollout restart deployment/<deployment-name>

# Scale to zero and back
kubectl scale deployment/<deployment-name> --replicas=0
kubectl scale deployment/<deployment-name> --replicas=3

# Delete and recreate pod
kubectl delete pod <pod-name>
```

### 2. Node Recovery
```bash
# Drain node
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data

# Cordon node
kubectl cordon <node-name>

# Restart node
systemctl restart kubelet
```

### 3. Cluster Recovery
```bash
# Check cluster status
kubectl get componentstatuses

# Check etcd health
ETCDCTL_API=3 etcdctl --endpoints=<etcd-endpoint> endpoint health

# Restore from backup if needed
velero restore create restore-1 --from-backup <backup-name>
```

## 🎯 Best Practices for Troubleshooting

1. **Start with the basics**: Check pod status, logs, and events
2. **Use the process of elimination**: Isolate variables one by one
3. **Document everything**: Keep track of what you've tried
4. **Test incrementally**: Make small changes and verify
5. **Use debugging tools**: Leverage k9s, stern, and Lens
6. **Monitor continuously**: Set up alerts for common issues
7. **Learn from incidents**: Post-mortem and improve
8. **Stay updated**: Keep up with Kubernetes best practices

## 📚 Additional Resources

- [Kubernetes Troubleshooting Documentation](https://kubernetes.io/docs/tasks/debug-application-cluster/)
- [Kubernetes Debugging Guide](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/)
- [Kubernetes Troubleshooting Checklist](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#troubleshooting-checklist)
- [Kubernetes Best Practices](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
- [Kubernetes Security Best Practices](https://kubernetes.io/docs/concepts/security/)

Remember to always test changes in a non-production environment first and have proper backup procedures in place.