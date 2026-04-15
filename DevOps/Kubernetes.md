

| [[#Architecture]]    |     |
| -------------------- | --- |
| [[#Services]]        | 1   |
| [[#Init containers]] | 2   |
| [[#Multi-container]] | 3   |
| [[#Ingress]]         | 4   |
| [[#Gateway]]         | 5   |

# Architecture

![[Pasted image 20260407175857.png]]



Here's how Kubernetes architecture works, broken into its two fundamental layers:Kubernetes has two major layers — a **control plane** and one or more **worker nodes**.

**Control plane** — this is the cluster's brain. Every decision (scheduling, scaling, healing) originates here:

- **API server** is the single entry point. All communication — from `kubectl`, from internal components, from applications — goes through it via REST.
- **etcd** is a distributed key-value store that holds the entire cluster state. Think of it as the source of truth.
- [[#Scheduler]] watches for unscheduled pods and picks the best node for them based on resource availability, affinity rules, and constraints.
- **Controller manager** runs a set of control loops (Deployment controller, ReplicaSet controller, etc.) that continuously compare the _actual_ state with the _desired_ state and correct any drift.
- **Cloud controller manager** handles cloud-specific logic like provisioning load balancers or persistent volumes when running on AWS, GCP, or Azure.

**Worker nodes** — these are the machines that actually run your workloads:

- **kubelet** is an agent on every node. It receives pod specs from the API server and ensures the right containers are running and healthy.
- **kube-proxy** maintains network rules (via iptables or IPVS) so that pods can reach each other and services are properly routed.
- **Container runtime** (containerd, CRI-O) is what actually starts and stops containers on the host OS.
- **Pods** are the smallest deployable unit — one or more containers that share a network namespace and storage, always scheduled together.

The fundamental flow is: you declare _desired state_ (via `kubectl apply`) → API server writes it to etcd → controllers notice the gap → scheduler picks a node → kubelet brings the pod up. Kubernetes then continuously reconciles reality against that declaration.

# Scheduler

The Kubernetes scheduler is responsible for deciding **which worker node a new pod should run on**. It never runs the pod itself — it just assigns it. Here's how it works:

**The scheduling cycle has two phases:**

**1. Filtering (finding feasible nodes)** The scheduler eliminates nodes that can't run the pod based on hard requirements:

- Does the node have enough CPU and memory?
- Does it match any [[#nodeSelector and nodeAffinity]] rules?
- Does it have the required [[#Taints]] tolerated by the pod?
- Are the requested volumes available on that node?

Any node that fails even one filter is removed from consideration.

**2. Scoring (picking the best node)** Among the nodes that passed filtering, the scheduler ranks them using a set of scoring functions:

- **Resource balance** — prefer nodes where the pod's resource request fits most evenly (avoids hotspots)
- **Pod affinity/anti-affinity** — prefer nodes near (or far from) other specific pods
- **Image locality** — prefer nodes that already have the container image cached
- **Topology spread** — distribute pods evenly across zones or racks for resilience

Each function produces a score, they're weighted and summed, and the highest-scoring node wins.

**What triggers the scheduler?** When you create a pod (directly or via a Deployment), the API server writes it to etcd with `nodeName` unset. The scheduler watches for exactly these "unbound" pods, runs its two-phase logic, and writes the chosen node name back to the API server. The kubelet on that node then picks it up and starts the containers.

**What if no node passes filtering?** The pod stays in `Pending` state. The scheduler keeps retrying as the cluster changes — a node might free up resources, a new node might join, or a taint might be removed.

**Extensibility** The default scheduler covers most use cases, but you can customize it via:

- **Priority classes** — high-priority pods can preempt lower-priority ones if resources are tight
- **Scheduling profiles** — tune which plugins run and their weights
- **Custom schedulers** — run a second scheduler alongside the default for specialized workloads (GPU jobs, batch pipelines, etc.)

In short, the scheduler is a continuous watch loop: spot unbound pods → filter nodes → score survivors → bind the winner. It's deliberately stateless between decisions, so it scales well and recovers cleanly from failures.


A **node** in a Kubernetes (k8s) cluster is a physical or virtual machine that runs containerized workloads. Every node is managed by the control plane and contains the services needed to run pods.

## Types of Nodes

**Control Plane Node (Master Node)** Manages the cluster. Runs components like the API server, scheduler, and etcd. Doesn't usually run application workloads.

**Worker Node** Where your actual application containers run. A cluster typically has many of these.

## Key Components on Every Worker Node

**kubelet** — The primary agent on each node. It communicates with the control plane and ensures containers are running in their pods as instructed.

**kube-proxy** — Manages network rules on the node, enabling communication to/from pods both inside and outside the cluster.

**Container Runtime** — The software that actually runs containers (e.g., containerd, CRI-O, or Docker). The kubelet talks to it via the CRI (Container Runtime Interface).

## What Runs on a Node

Nodes run **Pods**, which are the smallest deployable unit in k8s. Each pod contains one or more containers. The scheduler decides which node a pod lands on based on available resources and constraints.

## Node Resources

Each node exposes its capacity to the cluster:

- **CPU** (in millicores, e.g. `500m` = 0.5 cores)
- **Memory** (in bytes, e.g. `2Gi`)
- **Storage** and **GPU** (if applicable)

The control plane tracks these to make smart scheduling decisions.

## Useful Commands

```bash
# List all nodes
kubectl get nodes

# Detailed info about a node (capacity, conditions, pods)
kubectl describe node <node-name>

# Check resource usage per node
kubectl top nodes
```

## Node States

A node can be in various conditions:

- `Ready` — healthy and accepting pods
- `MemoryPressure` / `DiskPressure` / `PIDPressure` — resource stress
- `NotReady` — unreachable or unhealthy

In short, nodes are the **compute workers** of a Kubernetes cluster — the machines that actually do the heavy lifting of running your applications.


##  Services 

Imagine you have a set of Pods running your application (e.g., a frontend web server). These Pods are **ephemeral** - they can be destroyed and recreated frequently due to:

- Scaling up/down
- Rolling updates
- Node failures
- Crashes

**Each time a Pod is recreated, it gets a new IP address.**

This creates huge problems:
- How do other applications find your frontend if its IP keeps changing?
- How does load balancing work across multiple Pod replicas?
- How do you provide a stable network identity?

## The Solution: Kubernetes Service

A **Service** provides a **stable, persistent network endpoint** for a group of Pods, acting as an abstraction layer that decouples frontend clients from backend Pods.

---

## Key Purposes of a Service

### 1. **Stable Network Identity**
- Services get a **stable IP address** and **DNS name** that never changes
- Other applications can reliably connect to this endpoint
- The Service persists even when Pods come and go

### 2. **Load Balancing**
- Automatically distributes network traffic across all healthy Pods
- Uses round-robin algorithm by default
- Ensures no single Pod gets overwhelmed

### 3. **Service Discovery**
- Other applications can find your Service via:
  - **Environment variables** (legacy)
  - **DNS** (preferred) - `my-service.namespace.svc.cluster.local`

### 4. **Loose Coupling**
- Frontend doesn't need to know about individual backend Pods
- Backend Pods can be scaled, updated, or replaced without affecting clients

---

## Service Types and Use Cases

### 1. **ClusterIP** (Default)
- **Purpose**: Internal communication within the cluster
- **Visibility**: Only accessible from inside the cluster
- **Use Case**: Database service that should only be reached by other microservices

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  type: ClusterIP
  selector:
    app: backend
  ports:
  - port: 80
    targetPort: 8080
```

### 2. **NodePort**
- **Purpose**: External access to a specific port on every cluster node
- **Visibility**: Accessible from outside the cluster
- **Use Case**: Development, testing, or when you need direct node access

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  type: NodePort
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 9376
    nodePort: 30007  # Optional: 30000-32767 range
```

### 3. **LoadBalancer**
- **Purpose**: External access via cloud provider's load balancer
- **Visibility**: Publicly accessible
- **Use Case**: Production web applications exposed to the internet

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-web-service
spec:
  type: LoadBalancer
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 9376
```

### 4. **ExternalName**
- **Purpose**: Proxy to external services outside the cluster
- **Use Case**: Connecting to cloud databases or external APIs

```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-database
spec:
  type: ExternalName
  externalName: my-database.example.com
```

---

## Real-World Example

### Without Service (Chaos)
```
Frontend Pod → Backend Pod IP: 10.244.1.2
Backend Pod gets recreated → New IP: 10.244.1.3
Frontend can't find backend anymore! ❌
```

### With Service (Stability)
```
Frontend Pod → Backend Service: 10.103.228.135 (stable)
              ↓
Backend Service load balances to:
- Backend Pod 1: 10.244.1.2
- Backend Pod 2: 10.244.1.3
- Backend Pod 3: 10.244.1.4
```

**Even when Pod IPs change, the Service IP remains constant!** ✅

---

## How Services Work with Selectors

Remember our previous discussion about labels and selectors? This is where they come together:

```yaml
# Backend Pods (have labels)
apiVersion: v1
kind: Pod
metadata:
  name: backend-pod-1
  labels:                    # ← LABELS
    app: my-backend
    tier: api
spec:
  containers:
  - name: nginx
    image: nginx

# Backend Service (has selector)
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:                 # ← SELECTOR
    app: my-backend         # "Find all Pods with app=my-backend"
  ports:
  - port: 80
    targetPort: 8080
```

The Service's **selector** continuously watches for Pods that match its criteria and includes them in the load balancing pool.

---

## Summary: Why Services Are Essential

| Problem | Solution via Service |
|---------|---------------------|
| Pods have changing IPs | **Stable VIP** (Virtual IP) |
| Multiple Pod replicas | **Automatic load balancing** |
| Finding other services | **DNS-based service discovery** |
| External access | **NodePort/LoadBalancer** types |
| Application updates | **Zero-downtime** traffic shifting |

**In essence:** Services are the **network abstraction** that makes microservices architecture possible in Kubernetes by providing reliable, discoverable, and load-balanced access to your application components.


In Kubernetes, a **multi-container pod** is a single Pod that runs two or more containers that are tightly coupled and share the same lifecycle. These containers operate as a single cohesive unit on the same node. 

  

Key Characteristics

- Shared Network: All containers in the pod share a single IP address and port space. They communicate with each other via localhost.
- Shared Storage: Containers can mount the same
    
    Volumes
    
      
    to share data on disk, which is useful for tasks like log processing or data synchronization.
- Unified Lifecycle: All containers in the pod are started, stopped, and scaled together. If one container fails, the entire pod's status may be affected depending on the
    
    Restart Policy
    
      
    .

Common Design Patterns

1. Sidecar Pattern: A secondary container "helps" the main application without modifying its code. Examples include log collectors (e.g.,
    
    Fluentd
    
      
    ) or monitoring agents.
2. Ambassador Pattern: Acts as a proxy or "ambassador" to manage network communication between the main container and external services, such as service discovery or encryption.
3. Adapter Pattern: Standardizes or "adapts" the output of the main application (like logs or metrics) into a format expected by external systems.
4. Init Containers: Specialized containers that run and complete before the main application containers start. They are typically used for setup tasks like database migrations or configuration preparation. [[2]

Best Practices & Constraints

- Avoid Over-coupling: Only put containers in the same pod if they absolutely must share resources or lifecycle. Independent microservices should generally stay in separate pods to allow independent scaling.
- Port Conflicts: Since they share the same network namespace, two containers in the same pod cannot listen on the same port.
- Resource Allocation: Resource requests and limits are defined per container, but the pod's total resource usage is the sum of its containers. 
Would you like a YAML manifest example for a multi-container pod using one of these patterns?

  ---
  


# Init containers

In Kubernetes, **init containers** are specialized containers that run and complete their tasks before any application containers in a Pod start. They are primarily used to perform setup logic, such as preparing configurations, waiting for external services, or initializing data. [[1]
  

Key Characteristics

- Sequential Execution: If a Pod has multiple init containers, they run one at a time in the order specified in the YAML.
- Run to Completion: Each init container must exit successfully (exit code 0) before the next one starts and before the main application containers are launched.
- Blocking Nature: The main app container will not start until all init containers have finished successfully.
- Lifecycle: Unlike regular containers, they do not support probes (
    
    liveness
    
      
    , readiness, or startup) because they are not long-running.

Common Use Cases

- Waiting for Dependencies: Delaying app startup until a database or API service is reachable.
- Data Preparation: Cloning a Git repository or downloading configuration files into a
    
    shared volume
    
      
    for the main container to use.
- Security: Running setup tools that require high privileges or utilities (like , , or ) that you don't want to include in the production app image.
- Database Migrations: Running schema updates before the application connects to the database. 
Example Configuration

  

Init containers are defined in the section of a Pod manifest: [[10]

_(Source: Kubernetes Official Documentation)_

  

Resource Management

- Effective Requests/Limits: Kubernetes calculates the Pod's resource requirements as the higher of:

1. The sum of all application containers' requests/limits.
2. The highest single request/limit among all init containers.

- Scheduling: This ensures the node has enough capacity for the most resource-intensive initialization task, even if the app itself uses less. [[1]

Native Sidecars Note: Since Kubernetes v1.29, you can set on an init container to make it a Native Sidecar. These start during the init phase but continue running alongside the main application. [[4]
  

---

# Multi-container

In Kubernetes, a multi-container pod is a single deployable unit that contains two or more containers that are tightly coupled and share the same resources, such as network and storage. This approach is typically used to support a primary application with "helper" processes that must reside on the same logical host. 

  

Key Characteristics

- Shared Network: All containers in the pod share the same IP address and port space. They communicate with each other via .
- Shared Storage: Containers can mount the same Volumes, allowing them to read and write to the same files for data exchange.
- Shared Lifecycle: Containers in a pod are scheduled, started, and stopped together on the same node.
- Atomic Deployment: Kubernetes manages the pod as a single entity; if the pod is moved to another node, all its containers move together. 
Common Design Patterns

1. Sidecar Pattern: A helper container enhances the main application (e.g., a logging agent that collects logs or a local monitoring exporter).
2. Ambassador Pattern: A container acts as a proxy for the main application, handling external connections like service discovery or specialized security.
3. Adapter Pattern: A container transforms the main application's output (e.g., reformatting logs or metrics) to match the requirements of an external system.
4. Init Containers: Specialized containers that run to completion before the main application containers start, often used for setup tasks like database migrations. [[6]

When to Use (and When Not To)

- Use when: Containers are tightly coupled, meaning they require direct access to each other’s files or need to share the same network identity.
- Avoid when: Containers can function independently. It is generally better to use separate pods for independent services (like a web app and its database) to allow them to scale and fail independently.

---


# Ingress

In Kubernetes (k8s), ==Ingress is an API object that manages external access to services within a cluster, typically via HTTP and HTTPS==. It acts as a "smart router" that can consolidate multiple routing rules into a single resource. [1, 2, 3]

## Key Functions of Ingress

- Host-Based Routing: Directs traffic to different services based on domain names (e.g., `api.example.com` vs. `web.example.com`).
- Path-Based Routing: Directs traffic based on URL paths (e.g., `example.com/api` vs. `example.com/app`).
- SSL/TLS Termination: Manages security certificates and decrypts HTTPS traffic at the entry point before passing it to backend services.
- Load Balancing: Distributes incoming requests across multiple backend pods to ensure high availability. [4, 5, 6, 7, 8, 9]


## Core Components

To use Ingress, you need two distinct parts:

1. Ingress Resource: A YAML file where you define your specific routing rules.
2. Ingress Controller: The actual software (often a reverse proxy like NGINX, Traefik, or HAProxy) that implements those rules and manages the traffic. [5, 7, 10, 11]

## Ingress vs. LoadBalancer Service

|Feature [3, 12, 13, 14, 15]|Ingress|LoadBalancer Service|
|---|---|---|
|OSI Layer|Layer 7 (Application)|Layer 4 (Transport)|
|Cost|More cost-effective for many services (shares one IP)|Often expensive (provisions a new cloud IP per service)|
|Routing|Complex host/path-based rules|Simple, direct mapping to one service|

Note that the Kubernetes project now recommends the [Gateway API](https://gateway-api.sigs.k8s.io/) as a more advanced successor to Ingress for new projects. [9, 16]

Are you looking for a YAML example of an Ingress resource, or do you need help choosing an Ingress controller for your specific environment?

---


# Gateway


The Kubernetes Gateway API is ==the modern, role-oriented successor to the traditional [Ingress API](https://kubernetes.io/docs/concepts/services-networking/ingress/)==. While Ingress focused primarily on simple HTTP/S routing using a single monolithic resource, the Gateway API breaks these concerns into separate, modular resources that align with how organizations actually manage infrastructure. [1, 2, 3]

## Why use Gateway API instead of Ingress?

- Role-Oriented Design: It separates responsibilities between infrastructure providers (GatewayClass), cluster operators (Gateway), and application developers (HTTPRoute).
- Built-in Advanced Routing: Features like traffic splitting (for canary rollouts), header-based matching, and request redirects are part of the core specification. With Ingress, these required non-portable, vendor-specific annotations.
- Multi-Protocol Support: Beyond just HTTP/S, it natively supports protocols like gRPC, TCP, and UDP.
- Cross-Namespace Routing: Allows a single Gateway to securely route traffic to services across different namespaces, which was difficult and often insecure with standard Ingress. [1, 4, 5, 6]

## Core Resources

1. GatewayClass: Defines the "type" of gateway (e.g., an NGINX-based controller or a cloud-managed load balancer). Managed by Infrastructure Providers.
2. Gateway: An instance of a GatewayClass that defines where the cluster listens for traffic (ports, protocols, and TLS certificates). Managed by Cluster Operators.
3. HTTPRoute / GRPCRoute: Defines specific routing rules (paths, headers, weights) to backend services. Managed by Application Developers. [4, 7, 8, 9]

## Implementation Comparison

|Feature [5, 6, 9, 10, 11]|Ingress|Gateway API|
|---|---|---|
|Primary Focus|Basic HTTP/S Routing|L4 and L7 Service Networking|
|Configuration|Single resource + many annotations|Multiple modular resources|
|Portability|Low (vendor-specific annotations)|High (standardized specification)|
|Traffic Splitting|Requires custom annotations|Native "weight" support in Routes|

## Getting Started

The Gateway API is delivered as [Custom Resource Definitions (CRDs)](https://gateway-api.sigs.k8s.io/) and requires a compatible controller to function. Popular implementations include Istio, Envoy Gateway, NGINX Gateway Fabric, and Cilium. [1, 7, 8]

---



  

Both are ways to **constrain which nodes a pod can land on**, but they differ in expressiveness.

---

# nodeSelector and nodeAffinity

A flat key-value match. The pod only schedules on nodes that have _all_ the specified labels:

```yaml
spec:
  nodeSelector:
    disktype: ssd
    region: us-east-1
```

That's it. No operators, no OR logic, no soft preferences. If no node matches, the pod stays `Pending`. It's blunt but easy to reason about.

---

**`nodeAffinity`** — the expressive version

Same idea, but with full boolean logic and optional soft preferences:

```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: disktype
                operator: In
                values: [ssd, nvme]
              - key: region
                operator: NotIn
                values: [eu-west-1]
      preferredDuringSchedulingIgnoredDuringExecution:
        - weight: 80
          preference:
            matchExpressions:
              - key: zone
                operator: In
                values: [us-east-1a]
```

The two rule types behave very differently:

||`required...`|`preferred...`|
|---|---|---|
|Hard or soft?|Hard — must match or pod is `Pending`|Soft — scheduler tries, but won't block|
|Scoring|Not scored, just filtered|Adds weighted score to matching nodes|
|Pod stays if node label changes?|Yes (ignored during execution)|Yes|

The supported **operators** are what make `nodeAffinity` powerful: `In`, `NotIn`, `Exists`, `DoesNotExist`, `Gt`, `Lt`. You can express things like "must be on a node with more than 8 CPUs" or "anywhere except nodes tagged as spot instances."

---

**When to use which**

- Use `nodeSelector` for simple, stable requirements — e.g. "this pod needs an SSD" with a single label.
- Use `nodeAffinity` when you need OR logic across values, exclusion rules, or a soft preference with a fallback.
- Use `nodeAffinity` + `podAffinity`/`podAntiAffinity` together when placement needs to be relative to _other pods_ (e.g. "co-locate with the cache pod, but spread replicas across zones").

The `IgnoredDuringExecution` suffix on both rule types means Kubernetes won't evict a running pod if a node's labels change after scheduling — a deliberate tradeoff for stability. A future `RequiredDuringExecution` mode (in progress in the Kubernetes project) would enable eviction on label change.

---
# Taints
	Taints go on nodes, **tolerations** go on pods. Together they form an opt-in repulsion system — the opposite of affinity.

---

**The mental model**

- A taint says: _"I repel pods unless they explicitly tolerate me."_
- A toleration says: _"I can handle that taint — don't repel me."_

A toleration doesn't _attract_ a pod to a node (that's affinity's job). It merely _removes the barrier_. You often combine both: affinity to pull a pod toward a node, toleration to let it past the taint.

---

**Tainting a node**

```bash
kubectl taint nodes node1 gpu=true:NoSchedule
```

This taint has three parts:

|Part|Example|Meaning|
|---|---|---|
|Key|`gpu`|Label-like identifier|
|Value|`true`|Optional qualifier|
|Effect|`NoSchedule`|What happens to intolerant pods|

**Three effects:**

- `NoSchedule` — intolerant pods are never scheduled here. Already-running pods are unaffected.
- `PreferNoSchedule` — soft version. Scheduler avoids the node but will use it if there's no alternative.
- `NoExecute` — strongest. Intolerant pods are not scheduled _and_ any already running are evicted.

---

**Adding a toleration to a pod**

```yaml
spec:
  tolerations:
    - key: gpu
      operator: Equal
      value: "true"
      effect: NoSchedule
```

Or tolerate any value for a key:

```yaml
    - key: gpu
      operator: Exists
      effect: NoSchedule
```

Or tolerate everything (dangerous — use only for system-level pods):

```yaml
    - operator: Exists
```

---

**`NoExecute` and the `tolerationSeconds` option**

`NoExecute` is special because it can evict running pods. You can soften it with a grace period:

```yaml
    - key: node.kubernetes.io/not-ready
      operator: Exists
      effect: NoExecute
      tolerationSeconds: 300
```

This tells Kubernetes: "let this pod keep running for 300 seconds on a not-ready node before evicting it." Useful for pods that can tolerate brief node hiccups without restarting.

---

**Built-in taints Kubernetes adds automatically**

Kubernetes itself taints nodes under certain conditions — and system components tolerate them by default:

|Taint|Condition|
|---|---|
|`node.kubernetes.io/not-ready`|Node health check failing|
|`node.kubernetes.io/unreachable`|Node unreachable from control plane|
|`node.kubernetes.io/memory-pressure`|Node is low on memory|
|`node.kubernetes.io/disk-pressure`|Node is low on disk|
|`node.kubernetes.io/unschedulable`|Node was cordoned with `kubectl cordon`|

---

**Common real-world patterns**

- **Dedicated GPU nodes** — taint all GPU nodes with `gpu=true:NoSchedule`, add toleration only to GPU workloads. CPU pods can't accidentally land there.
- **Spot/preemptible nodes** — taint with `spot=true:NoExecute`, tolerate only in batch jobs that can handle eviction.
- **Node maintenance** — `kubectl cordon` adds `unschedulable:NoSchedule`. `kubectl drain` adds `NoExecute` and evicts pods gracefully.
- **Control plane isolation** — control plane nodes carry a `node-role.kubernetes.io/control-plane:NoSchedule` taint by default, keeping user workloads off them.

The taint/toleration system is essentially a **blacklist with an override mechanism** — nodes reject everything by default once tainted, and pods carry the credentials to get through.