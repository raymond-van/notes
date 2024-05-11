## Kubernetes
### Benefits
- Resource management
	- optimizes the placement of containers on nodes based on resource requests / limits thereby maximizing use of hardware
- Scaling
	- automatically add or remove container instances based on load
	- load balancing
- Resilience / Availability
	- performs health checks which constantly monitors your containers, restarting them if they fail
	- define the number of replicas and ensure they are always running
- Deployments
	- Rolling updates and canary deployments allow you to deploy new versions with minimal downtime and risk
- Networking and Service Discovery
	- see below
### Components
#### Pod
- fundamental units of deployment in Kubernetes
- represent a group of one or more containers that are tightly coupled and designed to run together on a single node
- for each pod you can define the minimum amount of resources (requests) needed and the maximum amount (limits) the pod can consume
#### Node
- physical/virtual machines that run the containerized workloads
- contains Kubelet that communicates with control plane
- contains Kube-proxy which handles network-routing and load-balancing within the cluster
- contains Container Runtime that enables containers to run (e.g.: Docker, containerd)
- each node has a total amount of available CPU and memory
	- pods are scheduled to a node only if it has enough capacity
#### Kubelet
- agent that runs on every worker node
- ensures that containers as described in the Pod specs are running and healthy
- manages lifecycle of pods: starts/stops/restarts containers
- allocates resources (cpu/memory) to containers according to specs
- performs health checks (liveness and readiness probes) and reports back to control plane
- bridge between the control plane and the individual node it runs on
- communicates with API server
#### Control Plane
- central management system for a cluster
- contains an API server, scheduler, controller, etcd
- creates PodSpecs
	- YAML/JSON file that defines what containers should run in a pod
##### API Server
- acts as the single point of entry for all Kubernetes API requests
- receives commands from users or tools and translates them into instructions for the cluster
##### Scheduler
- takes Pod specifications from the API server and decides on the most suitable node to run them on
- considers factors like resource availability and Pod requirements
##### Controller
- specialized control loops that constantly monitor the state of the cluster and take actions to ensure it reaches the desired state as specified in Pod Specs
##### etcd
- highly available, distributed key-value store holding the cluster's configuration and state
- source of truth for Kubernetes and is used to keep the actual cluster state in line with the desired state (Pod Specs)
#### Cluster
- consists of Control Plane and Worker nodes
### Cluster creation flow
1. **Deployment:** You define your application using PodSpecs, which specify the container images, resource requirements, and configurations for your application
2. **API Server:** These PodSpecs are submitted to the Kubernetes API server
3. **Scheduler:** The scheduler in the control plane selects the most appropriate node to run each Pod based on the specified requirements and current cluster state
4. **API Server to Kubelet:** The API server instructs the kubelet on the chosen node regarding the Pod it should manage
5. **Kubelet:** The kubelet pulls the necessary container images, uses the container runtime to start the containers within the Pod, and allocates resources as specified
6. **Pod Management:** The kubelet continuously monitors the health of the containers within the pod, restarts them if they fail according to restart policies, and reports their health back to the control plane
7. **Controllers:** The controllers in the control plane constantly ensure the desired state of the cluster. If a Pod becomes unhealthy or needs to be scaled up/down, the controllers issue instructions to the API server and kubelets to rectify the situation
### Volumes & Data Persistence
#### Ephemeral Volumes
- created and destroyed along with Pod
- used for temporary space for workloads, caching
#### Network-based Volumes
- external storage mounted onto pods (e.g. AWS EBS)
#### Persistent Volumes (PV)
- Storage units provisioned by a cluster administrator or dynamically created using Storage Classes
- exists independently of a pod's lifecycle
#### Persistent Volume Claims (PVC)
- a pod's request to use a persistent volume
- Kubernetes will then find a suitable PV to match the PVC requirements
#### Storage Classes
- YAML template that defines a type of storage that can be dynamically provisioned for use
- includes the underlying storage provisioner (e.g.: AWS EBS, NFS), storage settings (encryption)
- combined with PVCs, Kubernetes can automatically create PVs on demand when Pods require them
### Ingress Controller
- reads and interprets Ingress rules and then implements an actual load balancer / reverse proxy to route traffic
- Implementation:
	- Nginx Ingress Controller
#### Ingress (rules)
- set of rules focusing on how to handle network traffic entering your cluster from the outside world
- deals with routing web traffic based on URL paths to different services in your cluster
- can act as a Layer 7 Load Balancer
### Service Mesh
- focuses on network traffic within your cluster
- Provides features like:
	- Fine-grained traffic control 
		- routing, load balancing at the microservice level)
    - Secure service-to-service communication 
	    - encryption, authentication
    - Observability 
	    - metrics, traces, logs about service communication
    - Resiliency 
	    - retries, timeouts, circuit breakers
- **Sidecar Proxies:** Service meshes work by injecting 'sidecar' proxies (like Istio) next to each of your application pods
	- these proxies intercept traffic and implement the service mesh's logic
### Pod Management
### Deployments
- stateless apps where replicas are interchangeable (web servers, API backends)
- easy to deploy, update, rollback and scale as you don't need to worry about state
### StatefulSets
- stateful with additional overhead
- assigns stable, unique network identifierse
- used when stable identity, ordered scaling, and persistent storage for your Pods (databases, clustered apps)
- used when services rely on persistent identifiers or ordered operations or any application needed to recognize individual pod members
- e.g.: DBs (needs a stable identify for communication, primary DB needs to be started before read-replicas are created) 
### DaemonSets
- ensures **only one** of these Pods run on **each node**
- used for logging agents, daemons that gather node-specific metrics
- used for system services that need to be present on each node for cluster-wide functioning
### Service Discovery
- Instead of using ephemeral IP addresses that are constantly changing as pods get created/destroyed/moved, Service discovery ensures that services can find and communicate with each other
- can communicate using service name rather than ephemeral IP addresses
- helps facilitate traffic 