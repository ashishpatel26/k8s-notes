# 📚 Kubernetes Glossary

A comprehensive glossary of Kubernetes terminology, concepts, and acronyms.

## 🏗️ Core Concepts

### **Kubernetes (K8s)**
An open-source container orchestration platform for automating deployment, scaling, and management of containerized applications.

### **Container**
A lightweight, standalone, executable package that includes everything needed to run a piece of software, including the code, runtime, system tools, system libraries, and settings.

### **Pod**
The smallest deployable unit in Kubernetes that contains one or more containers. Pods share network and storage resources.

### **Node**
A worker machine in Kubernetes, which may be a virtual or physical machine. Each node contains the services necessary to run pods.

### **Cluster**
A set of nodes that run containerized applications managed by Kubernetes. Provides the compute resources for running applications.

### **Control Plane**
The set of components that make decisions about the cluster (e.g., scheduling, scaling, rolling updates).

### **etcd**
Consistent and highly-available key-value store used as Kubernetes' backing store for all cluster data.

### **kube-apiserver**
The API server validates and configures data for the api objects which include pods, services, replication controllers, and more.

### **kube-scheduler**
The component on the master that watches for newly created pods with no assigned node, and selects a node for them to run on.

### **kube-controller-manager**
Runs controllers which process background tasks in the cluster. Logically, each controller is a separate process, but to reduce complexity, they are all compiled into a single binary and run in a single process.

### **kubelet**
The primary node agent that runs on each node. It ensures that containers are running in a pod.

### **kube-proxy**
Maintains network rules on nodes. These network rules allow network communication to your pods from network sessions inside or outside of your cluster.

### **Container Runtime**
The software that is responsible for running containers (e.g., Docker, containerd, CRI-O).

## 📦 Objects

### **Deployment**
Declarative way to manage applications. Provides declarative updates for Pods and ReplicaSets.

### **ReplicaSet**
Ensures that a specified number of pod replicas are running at any given time.

### **StatefulSet**
Manages the deployment and scaling of a set of pods, and provides persistent and stable storage and networking.

### **DaemonSet**
Ensures that all (or some) nodes run a copy of a pod. As nodes are added to the cluster, pods are added to them.

### **Job**
Creates one or more pods and ensures that a specified number of them successfully terminate.

### **CronJob**
Creates Jobs on a time-based schedule.

### **Service**
An abstraction that defines a logical set of Pods and a policy by which to access them.

### **Ingress**
Manages external access to the services in a cluster, typically HTTP/HTTPS.

### **ConfigMap**
Allows you to decouple configuration artifacts from container images.

### **Secret**
Used to store sensitive data such as passwords, tokens, and keys.

### **PersistentVolume (PV)**
A piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.

### **PersistentVolumeClaim (PVC)**
A request for storage by a user. It is similar to a Pod but volumes exist independently of any Pod.

### **StorageClass**
Provides a way for administrators to describe the "classes" of storage they offer.

### **Namespace**
Provides a scope for Names. Names of resources need to be unique within a Namespace but not across Namespaces.

### **ServiceAccount**
An account that provides an identity for processes running in a Pod.

### **Role**
Defines a set of permissions within a Namespace.

### **ClusterRole**
Defines a set of permissions for the entire cluster.

### **RoleBinding**
Grants the permissions defined in a Role to a User or ServiceAccount.

### **ClusterRoleBinding**
Grants the permissions defined in a ClusterRole to a User or ServiceAccount.

## 🌐 Networking

### **ClusterIP**
Exposes the service on a cluster-internal IP. Only reachable from within the cluster.

### **NodePort**
Exposes the service on each Node's IP at a static port (the same port on every Node).

### **LoadBalancer**
Exposes the service externally using a cloud provider's load balancer.

### **ExternalName**
Maps the service to the externalName field (e.g., my-database.example.com), by returning a CNAME record with that name.

### **Ingress Controller**
A piece of software that provides reverse proxy, configurable traffic routing, and TLS termination for Kubernetes services.

### **Network Policy**
Controls the flow of traffic between pods based on labels and selectors.

### **CNI (Container Network Interface)**
A standard for container networking that allows different networking plugins to be used with container runtimes.

### **CoreDNS**
The DNS server for Kubernetes clusters.

### **Service Mesh**
A dedicated infrastructure layer for handling service-to-service communication.

### **mTLS (mutual TLS)**
A method where both the client and server authenticate each other by verifying digital certificates.

## 🔧 Storage

### **Volume**
A directory accessible to all containers in a pod.

### **PersistentVolume**
A piece of storage provisioned for use with Kubernetes clusters.

### **PersistentVolumeClaim**
A request for storage by a user.

### **StorageClass**
Defines the "classes" of storage offered in a cluster.

### **Dynamic Provisioning**
Automatically creates storage when it's requested.

### **Static Provisioning**
Manually creates storage before it's requested.

### **Read-Once**
Volume can be mounted as read-write by a single node at a time.

### **ReadOnlyMany**
Volume can be mounted as read-only by many nodes.

### **ReadWriteOnce**
Volume can be mounted as read-write by a single node.

### **ReadWriteMany**
Volume can be mounted as read-write by many nodes.

## 🔒 Security

### **RBAC (Role-Based Access Control)**
A method of regulating access to computer or network resources based on the roles of individual users.

### **Pod Security Policy (PSP)**
Cluster-level resource that controls security-sensitive aspects of the pod specification.

### **Pod Security Admission (PSA)**
A built-in admission controller that enforces Pod Security Standards.

### **Sealed Secrets**
A Kubernetes controller and tool for managing encrypted secrets.

### **Network Policy**
Controls the flow of traffic between pods based on labels and selectors.

### **Service Account Token Volume Projection**
Mounts service account tokens as volumes in pods.

### **Pod Security Context**
Security settings for a pod or container.

### **Security Context**
Security settings for a container.

### **Capabilities**
Linux kernel security features that can be enabled or disabled for a container.

### **AppArmor**
Linux kernel security module that allows administrators to confine programs to a limited set of resources.

### **SELinux**
Security-Enhanced Linux, a Linux kernel security module.

## 📊 Monitoring and Observability

### **Prometheus**
An open-source monitoring system with a dimensional data model, flexible query language, and efficient time series database.

### **Grafana**
An open-source platform for monitoring and observability.

### **Alertmanager**
Handles alerts sent by client applications such as the Prometheus server.

### **Metrics**
Quantitative measurements of system behavior.

### **Logs**
Records of events that occur in a system.

### **Traces**
Records of the path that a request takes through a system.

### **Service Level Objectives (SLO)**
Service level objectives define the target level of desired reliability and performance.

### **Service Level Indicators (SLI)**
Service level indicators measure the performance of a service.

### **Service Level Agreement (SLA)**
Service level agreements define the level of service expected by a customer.

### **APM (Application Performance Monitoring)**
Tools and processes for monitoring the performance of applications.

### **Distributed Tracing**
A method for monitoring applications in a microservices architecture.

### **OpenTelemetry**
An open-source observability framework.

## 🚀 Deployment and Orchestration

### **Helm**
The package manager for Kubernetes.

### **Chart**
A collection of files that describe a related set of Kubernetes resources.

### **Release**
A running instance of a chart in a Kubernetes cluster.

### **Value**
Configuration values for a chart.

### **Kustomize**
A configuration customization engine for Kubernetes.

### **Kustomization**
A set of resources that have been customized using Kustomize.

### **ArgoCD**
A declarative, GitOps continuous delivery tool for Kubernetes.

### **Flux**
A GitOps operator for Kubernetes.

### **CI/CD (Continuous Integration/Continuous Deployment)**
Automated processes for building, testing, and deploying applications.

### **GitOps**
A way of implementing Continuous Delivery for cloud-native applications.

### **Canary Deployment**
A technique to reduce the risk of introducing a new software version by slowly rolling out the change to a small subset of users.

### **Blue-Green Deployment**
A deployment strategy that reduces downtime and risk by running two identical production environments.

### **Rolling Update**
A deployment strategy that gradually replaces old instances with new ones.

### **Rollback**
Reverting to a previous version of an application.

## 🏛️ Architecture

### **Microservices**
An architectural style that structures an application as a collection of loosely coupled services.

### **Monolith**
A single, unified unit that contains all of the application's code and functionality.

### **Serverless**
A cloud-computing execution model where the cloud provider runs the server and dynamically manages the allocation of machine resources.

### **Function as a Service (FaaS)**
A cloud computing service that allows users to execute code in response to events without the complex infrastructure.

### **Infrastructure as Code (IaC)**
Managing and provisioning infrastructure through code and software development techniques.

### **Platform as a Service (PaaS)**
A cloud computing model that delivers applications over the internet.

### **Software as a Service (SaaS)**
A software licensing and delivery model in which software is licensed on a subscription basis and is centrally hosted.

### **Multi-Cloud**
Using multiple cloud computing services from different providers.

### **Hybrid Cloud**
A computing environment that combines a public cloud and a private cloud.

### **Edge Computing**
Distributed computing paradigm that brings computation and data storage closer to the location where it is needed.

## 📈 Scaling and Performance

### **Horizontal Pod Autoscaler (HPA)**
Automatically scales the number of pods in a deployment, replica set, or stateful set based on observed CPU utilization.

### **Vertical Pod Autoscaler (VPA)**
Automatically adjusts the resources of pods in a deployment or replica set.

### **Cluster Autoscaler**
Automatically adjusts the size of a Kubernetes cluster based on the resource requests.

### **Pod Disruption Budget**
Ensures that a certain number of replicas of a pod are running during voluntary disruptions.

### **Resource Quota**
Limits the amount of resources that can be consumed in a namespace.

### **Limit Range**
Specifies resource limits and requests for containers in a namespace.

### **Quality of Service (QoS)**
Classes that determine the scheduling and eviction behavior of pods.

### **BestEffort**
The lowest QoS class for pods that don't specify resource requests or limits.

### **Burstable**
The middle QoS class for pods that specify resource requests but not limits.

### **Guaranteed**
The highest QoS class for pods that specify equal resource requests and limits.

### **Node Affinity**
Constraints that allow you to schedule pods on specific nodes.

### **Pod Affinity/Anti-Affinity**
Constraints that allow you to schedule pods relative to other pods.

## 🔧 Tools and Utilities

### **kubectl**
The command-line tool for Kubernetes.

### **minikube**
A tool that makes it easy to run Kubernetes locally.

### **kind**
Kubernetes in Docker - a local Kubernetes cluster using Docker container "nodes".

### **kubeadm**
A tool for bootstrapping a Kubernetes cluster.

### **kops**
A production-grade Kubernetes installation, management, and monitoring tool.

### **kubeflow**
A machine learning toolkit for Kubernetes.

### **Istio**
An open platform to connect, secure, control, and observe microservices.

### **Linkerd**
A lightweight, service mesh for Kubernetes.

### **Calico**
A networking and network security solution for containers and virtual machines.

### **Flannel**
A network fabric for containers, designed to simplify networking across multiple hosts.

### **Cilium**
An open source software for eBPF-based networking, observability, and security.

### **OPA (Open Policy Agent)**
An open source, general-purpose policy engine.

### **Gatekeeper**
A policy controller for Kubernetes that enables CRD-based admission control.

## 📋 Common Commands

### **kubectl get**
Display one or many resources.

### **kubectl describe**
Show detailed information about a resource.

### **kubectl create**
Create a resource from a file or from stdin.

### **kubectl apply**
Apply a configuration to a resource.

### **kubectl delete**
Delete resources by filenames, stdin, resources and names, or by resources and label selector.

### **kubectl exec**
Execute a command in a container.

### **kubectl logs**
Print the logs for a container in a pod.

### **kubectl port-forward**
Forward one or more local ports to a pod.

### **kubectl proxy**
Run a proxy to the Kubernetes API server.

### **kubectl scale**
Set a new size for a Deployment, ReplicaSet, Replication Controller, or StatefulSet.

### **kubectl rollout**
Manage the rollout of a resource.

### **kubectl top**
Display resource (CPU/memory) usage.

### **kubectl explain**
Documentation of resources.

### **kubectl auth**
Inspect authorization.

### **kubectl config**
Modify kubeconfig files.

## 🎯 Common Acronyms

### **K8s**
Kubernetes (8 letters between K and s).

### **CI/CD**
Continuous Integration/Continuous Deployment.

### **IaC**
Infrastructure as Code.

### **SRE**
Site Reliability Engineering.

### **SLA**
Service Level Agreement.

### **SLO**
Service Level Objective.

### **SLI**
Service Level Indicator.

### **API**
Application Programming Interface.

### **CLI**
Command Line Interface.

### **GUI**
Graphical User Interface.

### **HTTP**
Hypertext Transfer Protocol.

### **HTTPS**
Hypertext Transfer Protocol Secure.

### **TCP**
Transmission Control Protocol.

### **UDP**
User Datagram Protocol.

### **DNS**
Domain Name System.

### **TLS**
Transport Layer Security.

### **SSL**
Secure Sockets Layer.

### **JSON**
JavaScript Object Notation.

### **YAML**
YAML Ain't Markup Language.

### **XML**
eXtensible Markup Language.

### **SQL**
Structured Query Language.

### **NoSQL**
Not Only SQL.

### **REST**
Representational State Transfer.

### **gRPC**
Google Remote Procedure Call.

### **RPC**
Remote Procedure Call.

### **SDK**
Software Development Kit.

### **IDE**
Integrated Development Environment.

### **VM**
Virtual Machine.

### **VMware**
Virtual Machine Ware.

### **AWS**
Amazon Web Services.

### **GCP**
Google Cloud Platform.

### **Azure**
Microsoft Azure.

### **VM**
Virtual Machine.

### **VM**
Virtual Machine.

### **VM**
Virtual Machine.

## 🔄 Common Workflows

### **GitOps Workflow**
1. Developer commits code changes to Git repository
2. CI/CD pipeline builds and containerizes the application
3. Container image is pushed to container registry
4. GitOps tool (ArgoCD/Flux) detects changes in Git
5. GitOps tool applies changes to Kubernetes cluster
6. Deployment is rolled out with zero downtime
7. Monitoring and alerting track deployment health

### **Canary Deployment Workflow**
1. Deploy new version to canary environment
2. Route small percentage of traffic to canary
3. Monitor canary performance and metrics
4. If canary performs well, gradually increase traffic
5. If canary has issues, rollback automatically
6. Once canary receives 100% traffic, remove old version

### **Blue-Green Deployment Workflow**
1. Deploy new version to green environment
2. Run both blue (current) and green (new) versions
3. Route all traffic to green environment
4. Monitor green environment for issues
5. If issues occur, route traffic back to blue
6. Once green is stable, decommission blue environment

### **Troubleshooting Workflow**
1. Identify the problem (logs, metrics, user reports)
2. Gather information (kubectl describe, kubectl logs)
3. Form a hypothesis about the root cause
4. Test the hypothesis (make changes, observe results)
5. Fix the problem (apply changes, restart services)
6. Verify the fix (check logs, metrics, user feedback)
7. Document the solution and prevention measures

## 📚 Additional Resources

### **Official Documentation**
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Kubernetes Glossary](https://kubernetes.io/docs/reference/glossary/)
- [Kubernetes API Reference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.28/)

### **Community Resources**
- [Kubernetes Slack](https://kubernetes.slack.com/)
- [Kubernetes Forums](https://discuss.kubernetes.org/)
- [Kubernetes GitHub](https://github.com/kubernetes/kubernetes)

### **Learning Resources**
- [Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [Kubernetes Certified Administrator (CKA)](https://www.cncf.io/certification/cka/)
- [Kubernetes Certified Application Developer (CKAD)](https://www.cncf.io/certification/ckad/)

### **Tools and Utilities**
- [k9s](https://k9scli.io/) - Terminal UI for Kubernetes
- [Lens](https://k8slens.dev/) - Kubernetes IDE
- [stern](https://github.com/stern/stern) - Multi container log tailing
- [kubectx](https://github.com/ahmetb/kubectx) - Switch between contexts
- **kubectl neat** - Clean up Kubernetes YAML files

This glossary provides a comprehensive reference for Kubernetes terminology and concepts. It covers everything from basic concepts to advanced topics, making it useful for beginners and experienced users alike.