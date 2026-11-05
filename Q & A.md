### 1. What are the main components of Kubernetes architecture? 
**Kubernetes architecture is divided into two main parts:**  
- **1. Control Plane**
- **1. Worker Nodes**

**1. The Control plane**   
**Managing the entire cluster. It's main components are;**   
  
**1. kube-apiserver:**    
**The API server acts as the entry point to the cluster.**  
**All communication between users, worker nodes, and control plane components happens through the API Server.**  
  
**2. etcd:**   
**It is a distributed key-value database.**   
**It stores all Kubernetes cluster information.**   
**It maintains the cluster's desired and current state information.**  
  
**3. kube-shceduler:**   
**It is responsible for scheduling nely created Pods onto suitale worker nodes.**  
**It checks available CPU, memory, resource requests, node selectors, taints, and tolerations.**  
  
**4. kube-controller-manager:**   
**It runs different controllers that continuosly monitor the cluster.**  
**It ensures the actual cluster state matches the desired state.**    
**It automatically takes corrective actions when something goes wrong.**   
  
**5. cloud-controller-manager:**   
**It integrates Kubernetes with cloud providers such as AWS, Azure, and GCP.**  
**It manages cloud-specific resources.**  

**2. The Worker Nodes**   
**Run the actual application workloads. Their main components are;**  
  
**1. kubelet:**    
**It is an agent running on every worker node.**      
**It communicates with the API server.**     
**It ensures containers inside Pods are running as expected.**    
  
**2. kube-proxy:**   
**It maintains network rules on worker nodes.**     
**It enables communication between Kubernetes Services and backend Pods.**    
  
**3. Container Runtime:**   
**It is responsible for running containers.**   
**It pulls container images from registries.**      
**It starts, stops, and manages containers.**     
    
**The Workflow is;**   
**1. kubectl: Sends the deployment request to the API Server.**  
**2. kube-apiserver: Validates the request and stores the desired state in etcd.**  
**3. Deployment Controller: Creates a ReplicaSet.**  
**4. ReplicaSet Controller: Ensures the required number of Pods are created.**  
**5. kube-scheduler: Selects suitable worker nodes for unscheduled Pods.**  
**6. kubelet: Receives the Pod assignment and instructs the container runtime.**  
**7. Container Runtime: Pulls the Docker image and starts the containers.**  
**8. kube-proxy: Maintains networking rules to enable Service-based communication.**   
  
**The application is now running inside the Kubernetes cluster.**

### 2. What is a Pod?
**A Pod is the smallest deployable unit in Kubernetes. It acts as a wrapper around one or more containers that run together.**  
**Containers inside a Pod share networking, storage volumes, and resources.**

### 3. Why is Pod the smallest deployable unit?
**A Pod is called the smallest deployable unit because Kubernetes schedules, deploy, and manages containers through Pods rather than managing individual containers directly.**

### 4. What is a Deployment? 
**A Deployment is a Kubernetes resource tat manages and maintain Pods.**  
**It ensures the desired number of Pod replicas are running and provides features like scaling, rolling updates, and rollbacks.**

### 5. What is the difference between Pod and Deployment? 
**A Pod is the smallest deployable unit in Kubernetes that runs containers, whereas a Deployment is a higher-level resource that manages Pods through ReplicaSet.**

### 6. What is a ReplicaSet?
**A ReplicaSet is a Kubernetes resource that ensures a specified number of identical Pod replicas are running at all times.**  
**If a Pod crashes, gets deleted, or fails, the ReplicaSet automatically creates a replacement Pod to maintain the desired replica count.**

## 7. What is the difference between Deployment and ReplicaSet? 
**Deployment is a higher-level Kubernetes resource that manages ReplicaSet and provides features like rolling updates, rollback, and scaling**  
**ReplicaSet is responsible for maintaining the desired number of Pod replicas. If a pod crashes or gets deleted, ReplicaSet creates a replacement.**

### 8. What is a Service in Kubernetes?  
**A Service is a Kubernetes resource that provides a stable network endpoint to access a group of Pods.**  
**Since Pods are temporary and their IP addresses can change when they are recreated, a Service provides a stable IP address and DNS name for communication.**  

**Types of Kubernetes Services**  

**1. ClusterIP: Exposes the application internally within the Kubernetes cluster. (Default type)**  

**2. NodePort: Exposes the application on a specific port of each Worker Node.**  

**3. LoadBalancer: Expose the application externally using a cloud provider's load balancer.**  

**4. ExternalName: Maps a Kubernetes Service to an external DNS name.**  

### 9. Why do we need Services?   
**we need Services in Kubernetes because Pods are ephemeral, and their IP addresses can change when they are recreated.**  
**A Service provides a stable IP address and DNS name to access Pods.**  
**It also enables service discovery, load balancing, and communication between different application components.** 

### 10. What is kubectl?
**kubectl is a command-line tools used to interact with and manage Kubernetes clusters. It communicates with the Kubernetes API Server to perform operations such as deploying applications, managing Pods, checking cluster status, viewing logs, and troubleshooting issues.**

### 11. What is kubelet?
**Kubelet is an agent that runs on every Worker Nods in a Kubernetes cluster. It communicates with the API Server and ensures that the containers inside assigned Pods are running as expected.**

### 12. How does Kubernetes schedule a Pod?          
**Kubernetes uses kube-scheduler to schedule Pods onto suitable Worker Nodes. When a new Pod is created, the API Server stores its information, and the scheduler detects that it is unassigned.**  
**It filters nodes based on resource availability and scheduling constraints, scores the eligible nodes, and selects the most suitable one.**  
**After assignment, the kubelet on that Worker Node communicates with the container runtime to start the containers.**
     
### 13. What happens if a node goes down?   
**If a Worker Node goes down, the Kubernetes Control Plane detects the failure.**  
**The Node Controller marks the Noda as NotReady.**  
**After the eviction period, Pods managed by Deployments or ReplicaSet are recreated and scheduled onto healthy Worker Nodes, provided sufficient resources are available.**  
**Kubernetes Services then route traffic to the healthy Pods, helping maintain application availability.**

### 14. What is the difference between init containers and normal containers?    
**Init Containers are special containers that runs before the main application containers start.**  
**They perform initialization tasks such as checking database connectivity, downloading configurations, or preparing the environment.**  

**Normal Containers run the actual application workload inside a Pod.**

### 15. What are sidecar containers?
**A sidecar Container is an additional container running alongside the main application container inside the same Pod.**  
**It provides supporting functionality such as logging, monitoring, security, or proxy services without modifying the main application.**

### 16. What is a pause container? 
**A Pause Container is a special infrastructure container used b Kubernetes container runtime to establish and maintain the shared namespaces of a Pod.**  
**it provides a shared network namespace so that multiple containers inside the same Pod can communicate using localhost and share the same Pod IP address.**

What are liveness probes?             
What are readiness probes      
What are startup probes?      
What happens if a liveness probe fails?        
How do you debug a failing Pod?       
How do you exec into a container?       
What is rolling update in Kubernetes?   
What is rolling rollback?   
How do you rollback a deployment?   
What is a DaemonSet?    
What is a StatefulSet?   
Difference between StatefulSet and Deployment?   
What is a Job?   
What is a CronJob?       
How does Kubernetes networking work?     
What is the Pod network?    
What is CNI?       
What are popular CNI plugins?     
What is kube-proxy?   
What is ClusterIP?    
What is NodePort?   
What is LoadBalancer?   
What is Ingress?   
What is Ingress Controller?    
Difference between Ingress and LoadBalancer?   
What is DNS in Kubernetes?   
How does service discovery work?   
What is a Persistent Volume (PV)?   
What is a Persistent Volume Claim (PVC)?    
What is the difference between PV and PVC?    
What is a StorageClass?    
What is a volume in Kubernetes?    
Types of volumes?      
What happens to data if a Pod dies?   
What is ConfigMap?   
What is Secret?   
Difference between ConfigMap and Secret?   
How do you pass environment variables from ConfigMap?    
What is RBAC?   
What are Roles and RoleBindings?    
Difference between Role and ClusterRole?    
What is a ServiceAccount?  
What are Network Policies?     
How do you restrict container privileges?   
What is Horizontal Pod Autoscaler (HPA)?    
What metrics does HPA use?     
What is Vertical Pod Autoscaler?    
How do you monitor Kubernetes?    
How do you collect logs?   
How do you debug cluster issues?     
Pod stuck in Pending state. What do you check?    
Pod stuck in CrashLoopBackOff. What do you do?    
Pod cannot pull image.   
Service not reachable.    
Pod not scheduling.    
Node not ready.    
Deployment not updating.   
PVC stuck in pending.     
High CPU usage in Pods.    
How do you debug networking issues?      
How do you deploy applications to Kubernetes using CI/CD?    
How do you implement blue-green deployment?    
What is canary deployment?      
How do you rollback a failed deployment?        
How do you handle secrets securely?       
How do you upgrade Kubernetes cluster?       
How do you backup etcd?       
How do you manage multiple environments?   
What is IAM Roles for Service Accounts (IRSA)?      
How do you expose services in EKS?         
What are taints and tolerations?    
What are node selectors?     
What are affinity and anti-affinity rules?    
What are resource requests and limits?     
What happens when a Pod exceeds memory limit?     
What is OOMKilled?     
How do you build Docker images for Kubernetes?     
What is Helm?     
What is Helm chart?    
What is ArgoCD?    
