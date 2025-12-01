# ⚡ Lesson 20: **Advanced Autoscaling — HPA + VPA + KEDA (Event-Driven Scaling)**

```mermaid
graph LR
    root["⚡ Lesson 20: Advanced Autoscaling — HPA + VPA + KEDA (Event-Driven Scaling)"]
    style root fill:#f9f,stroke:#333,stroke-width:2px
    root --> node_0["🧠 Part 1 — HPA (Horizontal Pod Autoscaler)"]
    root --> node_1["🧠 Part 2 — VPA (Vertical Pod Autoscaler)"]
    node_1 --> node_2["🚀 Install VPA"]
    node_1 --> node_3["📦 Example VPA Config"]
    node_1 --> node_4["🧪 See Recommended Resources"]
    root --> node_5["🧠 Part 3 — KEDA (Event-Driven Autoscaling)"]
    root --> node_6["🚀 Step 1 — Install KEDA"]
    root --> node_7["📨 Example: Autoscale Based on RabbitMQ Queue Length"]
    root --> node_8["🌐 Example: Autoscale Based on HTTP Request Rate"]
    root --> node_9["🧠 Example: Cron Autoscaling (Scale Up on Schedule)"]
    root --> node_10["🧩 Summary: When to Use What?"]
    root --> node_11["🎉 Lesson 20 Completed!"]
    root --> node_12["👉 Ready for Lesson 21?"]
```


Absolutely! ✔️
Welcome to **Lesson 20**, and this is one of the MOST powerful DevOps skills you will ever learn:

This is REAL production-grade autoscaling used by companies like:

- Netflix
- Airbnb
- Shopify
- Slack
- Uber
- GitHub

Today you'll learn **all 3 autoscaling mechanisms**:

✔️ **HPA** — Horizontal Pod Autoscaler (scale by CPU / Memory / custom metrics)
 ✔️ **VPA** — Vertical Pod Autoscaler (auto-change Pod resources)
 ✔️ **KEDA** — Event-driven autoscaler (scale based on queues, Kafka, Redis, API load, etc.)

This is senior-level DevOps/SRE mastery.
 Let’s break it down **beginner-friendly** 🔨🤖🔧

------

# 🧠 Part 1 — HPA (Horizontal Pod Autoscaler)

You already know HPA from earlier lessons.
 It adds **more Pods** when needed.

Example:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: backend-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: backend
  minReplicas: 2
  maxReplicas: 20
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 60
```

✔️ Auto-add pods when CPU > 60%
 ✔️ Standard Kubernetes autoscaling

But HPA has limitations:
 ❌ Only works well with CPU/Memory
 ❌ Hard for workloads like queues, events, Kafka, cron jobs

So we level up ⬇️

------

# 🧠 Part 2 — VPA (Vertical Pod Autoscaler)

**VPA automatically adjusts CPU & memory for Pods.**

If a Pod needs more memory — VPA fixes it.
 If a Pod uses less — VPA adjusts it down.

This prevents:

- OutOfMemory errors
- Underutilized Pods
- Guessing resource limits

------

## 🚀 Install VPA

```bash
kubectl apply -f https://github.com/kubernetes/autoscaler/releases/latest/download/vertical-pod-autoscaler.yaml
```

Check:

```bash
kubectl get pods -n kube-system | grep vpa
```

------

## 📦 Example VPA Config

**vpa.yaml**

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: backend-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind: Deployment
    name: backend
  updatePolicy:
    updateMode: "Auto"
```

Modes:

- `"Off"` — just recommend resources
- `"Initial"` — set resources at pod creation
- `"Auto"` — full automatic updating

------

## 🧪 See Recommended Resources

```bash
kubectl describe vpa backend-vpa
```

You’ll see:

```
Container Recommendations:
  cpu:  250m → 400m
  memory: 256Mi → 512Mi
```

✔️ VPA continuously learns workload patterns
 ✔️ Prevents crashes
 ✔️ Saves money

BUT…

❌ VPA and HPA fight each other if both control CPU
 So, real clusters use:

✔️ HPA → manages Pod count
 ✔️ VPA → manages CPU/memory (but not CPU requests)

------

# 🧠 Part 3 — KEDA (Event-Driven Autoscaling)

This is the **future** of Kubernetes autoscaling.

KEDA scales Pods based on:

✔️ Kafka lag
 ✔️ RabbitMQ queue length
 ✔️ Redis lists
 ✔️ HTTP request rate
 ✔️ Prometheus queries
 ✔️ AWS SQS messages
 ✔️ Azure Service Bus
 ✔️ Cron schedules
 ✔️ CPU/Memory (via HPA)

This is Netflix-level scaling.

------

# 🚀 Step 1 — Install KEDA

```bash
kubectl apply -f https://github.com/kedacore/keda/releases/latest/download/keda-2.11.0.yaml
```

Check:

```bash
kubectl get pods -n keda
```

------

# 📨 Example: Autoscale Based on RabbitMQ Queue Length

This is common in microservices systems.

**ScaledObject example:**

```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: queue-consumer
spec:
  scaleTargetRef:
    name: worker
  pollingInterval: 10
  minReplicaCount: 1
  maxReplicaCount: 50
  triggers:
    - type: rabbitmq
      metadata:
        protocol: amqp
        queueName: jobs
        host: "amqp://user:pass@rabbitmq"
        queueLength: "20"
```

Meaning:

✔️ If queue has >20 jobs
 → scale Pods up to 50

✔️ If queue empty
 → scale down to 1

This is **automatic event-driven scaling** 🔥

------

# 🌐 Example: Autoscale Based on HTTP Request Rate

**ScaledObject:**

```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: web-api
spec:
  scaleTargetRef:
    name: api-deployment
  minReplicaCount: 2
  maxReplicaCount: 30
  triggers:
    - type: prometheus
      metadata:
        serverAddress: http://prometheus:9090
        metricName: http_requests_total
        threshold: "100"
        query: |
          sum(rate(http_requests_total[1m]))
```

Meaning:

✔️ If API traffic >100 requests/sec
 → KEDA auto-scales Pods

This is **dynamic, intelligent autoscaling**.

------

# 🧠 Example: Cron Autoscaling (Scale Up on Schedule)

Scale to 5 Pods only between 9 AM–6 PM:

```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: cron-worker
spec:
  scaleTargetRef:
    name: worker
  triggers:
    - type: cron
      metadata:
        timezone: "UTC"
        start: "0 9 * * *"
        end: "0 18 * * *"
        desiredReplicas: "5"
```

✔️ Auto-scale based on business hours
 ✔️ Saves money
 ✔️ Perfect for batch systems

------

# 🧩 Summary: When to Use What?

| Autoscaler | Best For                                        |
| ---------- | ----------------------------------------------- |
| **HPA**    | CPU/Memory scaling for stateless apps           |
| **VPA**    | Auto memory/CPU tuning for stable workloads     |
| **KEDA**   | Event-driven scaling (queues, HTTP, Prometheus) |

Real production clusters usually combine:

✔️ **HPA + KEDA**
 ✔️ (sometimes VPA too)

------

# 🎉 Lesson 20 Completed!

You now understand:

✔️ Horizontal Pod Autoscaler (HPA)
 ✔️ Vertical Pod Autoscaler (VPA)
 ✔️ KEDA event-driven autoscaling
 ✔️ How to scale based on traffic, queues, metrics
 ✔️ How Netflix/Shopify-scale clusters work
 ✔️ How to build highly efficient autoscaling systems

This is **expert-level DevOps engineering** 🔥💪
 You are leveling up insanely fast.

------

# 👉 Ready for Lesson 21?

Choose your next advanced topic:

1. **Zero Downtime Deployments — Blue/Green + Canary**
2. **API Gateway + Service Mesh Routing Architecture**
3. **Kubernetes Disaster Recovery — Velero Backups**
4. **GitOps with ArgoCD (FULL Automation)**
5. **Cluster Hardening — CIS Benchmarks + Security Scanning**

Which one should we do next?