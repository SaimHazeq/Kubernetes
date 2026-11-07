### 1. What are the main components of Kubernetes architecture? 
**Kubernetes architecture is divided into two main parts:**  
- **1. Control Plane**
- **1. Worker Nodes**

- **1. The Control plane**   
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

- **2. The Worker Nodes**   
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

### 7. What is the difference between Deployment and ReplicaSet? 
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

### 17. What are liveness probes?
**A Liveness Probe is used to check whether an application container is still functioning properly.**  
**If the health fails repeatedly, Kubernetes restarts the container automatically.**  
**It helps detect issues like application deadlocks or unresponsive processes and provides automatic recovery.**

### 18. What are readiness probes 
**A Readiness Probe is used to check whether an application is ready to receive traffic.**  
**If the readiness check fails, Kubernetes removes the Pod from the Service endpoints, preventing traffic from reaching it.**  
**Once the application becomes healthy again, Kubernetes adds it back to the available endpoints.**  
**Unlike liveness probes, readiness probes do not restart containers.**

### 19. What are startup probes?   
**A Startup Probe checks whether an application has successfully started inside a container.**  
**It is mainly used for applications that require more time to initialize, such as Java application.**  
**Until the startup probe succeeds, Kubernetes does not execute liveness and readiness probes.**  
**This prevents premature container restarts during application startup.**

### 20. What happens if a liveness probe fails?
**If a Liveness Probe fails repeatedly and reaches its configured `failureThreshold`, kubelet restarts the container.**  
**This helps Kubernetes recover automatically from application issues such as deadlocks or unresponsive processes.**  

**Important: A liveness probe failure restarts the container, not necessarily the entire Pod.**  
**The Pod itself can remain in the Running state while its container restarts.**  
### 21. How do you debug a failing Pod?
**Common Pod Failures;**   
- **Pending**     
  **Insufficient CPU/Memory or scheduling issues**

- **CrashLoopBackOff**     
  **Application repeatedly crashing**

- **ImagePullBackOff**       
  **Incorrect image name or registry authentication**

- **ErrImagePull**     
  **Image cannot be pulled**

- **OOMKilled**      
  **Container exceeded its memory limit**

- **CreateContainerConfigError**        
  **Missing ConfigMap, Secret, or invalid configuration**

- **Running but NotReady**        
  **Readiness probe failing**

**When a Pod fails, I first check its status using `kubectl get pods`.**  
**Then I use `kubectl describe pod` to investigate events and identify the issue.**  
**I check application logs using `kubectl logs`, including previous container logs if the container has restarted.**  
**I also check resource utilization using `kubectl top pod` and inspect events.**  
**Based on the error, I troubleshoot image-pulling issues, resource limits, application configuration, networking, or health probes.**  
**After identifying the root cause, I fix the issue and verify the Pod is running and ready.**  

**Important Commands to Remember**
```
kubectl get pods                        #Pod status
kubectl describe pod <pod-name>         #Describe the Pod
kubectl logs <pod-name>                 #Pod logs
kubectl logs <pod-name> --previous      #Previous container logs
kubectl get events                      #Check pod Events
kubectl top pod <pod-name>              #Resource usage
kubectl top nodes
kubectl exec -it <pod-name> -- /bin/sh  #Access the container
```

### 22. How do you exec into a container?  
**`kubectl exec -it <pod-name> -- /bin/bash`**  
**`kubectl exec -it <pod-name> -- bin/sh`**  

**Remember**  
- **`-i` = Interactive mode.**
- **`-t` = Allocates a terminal**
- **`-c` = Specifies the container.**
- **`--` = Separates kubectl options from the command executed inside the container.**

### 23. What is rolling update in Kubernetes?   
**A rolling update is a Kubernetes deployment strategy that gradually replaces old Pods with new Pods without taking the application completely offline.**  
**Kubernetes creates new Pods, waits until they become Ready, and then terminates old Pods.**  
**We can control the update using `maxSurge` and `maxUnavailabe`, and roll back if the deployment fails.**  

**Important Deployment settings**  
- **`maxSurge`: Maximum additional Pods Kubernetes can create during an update.**
- **`maxUnavailabe`: Maximum Pods that can be unavailable during an update.**  

**Rolling Update Commands**

```  
kubectl set image deployment/myapp myapp=nginx:1.27     
kubectl rollout status deployment/myapp               #Check rollout status
kubectl rollout history deployment/myapp              #Check rollout history
kubectl rollout undo deployment/myapp                 #Rollback if wrong
```

### 24. What is rolling rollback? 
**Rollback in Kubernetes means reverting a Deployment to a previously working version if the latest deployment fails or cause issues.**   

**Rollback Commands**  

```
kubectl rollout history deployment/myapp                #Check rollout history
kubectl rollout undo deployment/myapp                   #Rollback to previous
kubectl rollout undo deployment/myapp --to-revision=2   #Rollback to specific version  
kubectl rollout status deployment/myapp                 #Rollout status
```   


| Rolling Update                       | Rollback                                         |
  | ---------------------------------- | ------------------------------------------------ |
| Deploy a new version                 | Reverts to an older version                      |
| Replaces old pods with new Pods      | Replaces current Pods with previous-version Pods |
| Used during application upgrades     | Used when a deployment causes problems           |


### 25. What is a DaemonSet?  
**A DaemonSet is a Kubernetes controller that ensures a copy of a Pods runs on every eligible worker node.**  
**When a new node joins the cluster, the DaemonSet automatically schedules a Pod on it.**  
**It is commonly used for logging, monitoring, networking, and security agents.**

### 26. What is a StatefulSet?
**A StatefulSet is a Kubernetes controller used to manage stateful application such as MySQL, PostgreSQL, and MongoDB.**  
**It provides stable Pod identities, persistent storage, and ordered deployment and termination.**  
**Even if a Pod is recreated, it retains its identity and can reconnect to its existing persistent volume.**

### 27. Difference between StatefulSet and Deployment? 
**Deployment is mainly used for stateless application where Pods are interchangeable, such as Nginx and frontend application.**

**StatefulSet is used for stateful application like MySQL, and MongoDB that require stable Pod identities, persistent storage, and ordered deployment.**  

**In short, Deployment manages interchangeable replicas, whereas StatefulSet manages Pods with stable identities.**

### 28. What is a Job?
**A Job in Kubernetes is used to run a task until it completes successfully.**  
**It creates Pods to execute tasks such as database backups, migrations, and batch processing.**  
**If a Pod fails, Kubernetes can retry the task based on the Job's retry configuration.**  

### 29. What is a CronJob?
**A CronJob in Kubernetes is used to schedule Jobs at specific times or intervals, similar to Linux cron. For example, we can use a CronJob to run database backups every day at midnight, clean logs periodically, or generate scheduled reports.**

### 30. How does Kubernetes networking work?   
**Kubernetes networking allows Pods, Services, and external users to communicate with each other across the cluster.**  

**Kubernetes follows 4 main networking principles.**  

**1. Pod-to-Pod communication**  
- **Every Pod gets its own IP address.**
- **Pods can communicate directly with other Pods across nodes.**
- **CNI plugins such as Calico, Cilium, and AWS VPC CNI handle Pod networking.**

**2. Pod-to-Service communication**  
- **Pod IPs can change when Pods are recreated.**
- **Kubernetes Services provide a stable IP and DNS name to access a group of Pods.**
- **kube-proxy typically implements Service networking rules using iptables or IPVS. Some CNI solutions, such Cilium, can replace kube-proxy.**

**3. External-to-Pod communication**  
- **ClusterIp : Internal cluster communication**
- **NodePort : Exposes application through a port on each node.**
- **LoadBalancer : Exposes application through an external load balancer.**
- **ExternalName : Maps a Service name to an external DNS name.**

**4. DNS-based service discovery**
- **Kubernetes uses CoreDNS for internal service discovery.**
- **A frontend Pod can use this DNS name to communicate with the backend Service without knowing individual Pod IPs.**

**Kubernetes networking enables communication between Pods, Services, and external users. Every Pod gets its own IP address, and Pods can communicate across nodes through a CNI plugin.**  
**Services provide stable IP addresses and DNS names because Pod IPs can change.**  
**CoreDNS handles service discovery, while kube-proxy typically manages Service traffic routing.**  
**For external access, we use NodePort, LoadBalancer, or Ingress.**  

**Important: CNI handles Pod networking, CoreDNS handles DNS resolution, and kube-proxy typically handles Service networking.**

  
### 31. What is the Pod network?   
**A Pod Network is the network that allows Kubernetes Pods to communicate with each other using their IP addresses, even when they are running on different worker nodes.**  
- **Every Pods gets a unique IP address.**
- **Containers inside the same Pod share the same network namespace and IP address.**
- **Pods can communicate directly without requiring NAT in the standard Kubernetes networking model.**
- **CNI plugins provide Pod networking.**

### 32. What is CNI? 
**CNI stands for Container Network Interface. It is a standard used by Kubernetes container runtimes to configure Pod networking.**  
**CNI plugins assign IP addresses, configure network interfaces, and enable communication between Pods.**

### 33. What is Ingress? 
**Ingress is a Kubernetes resource used to manage external HTTP and HTTPS traffic entering the cluster.**  
**It supports hostname-based and path-based routing and forwards requests to the appropriate Kubernetes Services.**  

### 34. What is Ingress Controller?     
**Ingress Controller is a component in Kubernetes that manages external HTTP and HTTPS traffic entering the cluster.**  
**It watches ingress resource, reads their routing rules, and forwards request to the appropriate Kubernetes Services.**  

**Suppose you have three application:**   
**`app.example.com` → Frontend Service**  
**`api.exmaple.com` → Backend Service**  
**`example.com/admin`**  

**The Ingress Controller:**  
- **Receives incoming HTTP/HTTPS request.**
- **Check the hostname or URL path.**
- **Matches the request against Ingress rules.**
- **Routes traffic to the correct Kubernetes Service.**
- **The Service forwards traffic to the backend Pods.**
   
    
### 35. Difference between Ingress and LoadBalancer?     
| Ingress                                                 | LoadBalancer                                               |
| --------------------------------------------------------| -----------------------------------------------------------|
| Routes HTTP/HTTPS traffic                               | Expose a Service externally                                |
| Supports hostname and path-based routing                | Does not provide native hostname/path routing              |
| Can route multiple applications through one entry point | Typically provisions an external load balancer per service |  
| Requires an Ingress Controller                          | Requires load balancer integration                         |


### 36. What is DNS in Kubernetes?  
**DNS in Kubernetes provides service discovery, allowing Pods to communicate with Services using DNS names instead of IP addresses.**  
**CoreDNS handles DNS resolution inside the cluster. For example, a frontend Pod can access a backend Service using `<service-name>.<namespace>.svc.cluster.local` even if the backend Pod IPs change.**

### 37. How does service discovery work?  
**Service discovery in Kubernetes allows applications to find and communicate with other applications without manually configuring Pod IP addresses.**  
**Kubernetes Services provide stable endpoints, while CoreDNS resolves Service names into IP addresses. The Service then forwards traffic to healthy backend Pods.**  
**This makes communication relaible even when Pods are recreated or their IP addresses change.**

### 38. What is a Persistent Volume (PV)?
**A Persistent Volume is a storage resource in Kubernetes that provides persistent storage independent of Pod lifecycle.**   
**It allows application like MySQL and PostgreSQL to retain data even when Pods are deleted or recreated.**  
**Pods access PVs through Persistent Volume Claims.**

### 39. What is a Persistent Volume Claim (PVC)?  
**A Persistent Volume Claim is a request for storage in Kubernetes.**  
**An application requests storage through a PVC, and Kubernetes binds it to a suitable Persistent Volume.**  
**Pods then mount the PVC to access Persistent storage. For example, a MySQL Pod can use a 10GB PVC to store database files even if the Pod is recreated.**

### 40. What is the difference between PV and PVC? 
| PV                                      | PVC                             |  
----------------------------------------- | --------------------------------|  
| Actual storage resource                 | Storage request                 |  
| Provides storage capacity               | Request storage capacity        |  
| Managed by cluster/storage provisioning | Crated by users or applications |  
| Independent of Pod lifecycle            | Used by Pods to access storage  |


### 41. What is a StorageClass?
**A StorageClass is a kubernetes resource that defines how storage should be dynamically provisioned.**  

**In simple terms, it allows Kubernetes to automatically create storage when an application requests it through a PVC, without requiring an administrator to manually create a PV first.**   

**How does it work:**
- **1. Administrator configures a StorageClass.**
- **2. User creates a PVC referencing that StorageClass.**
- **3. Kubernetes calls the configured provisioner.**
- **4. The provisioner creates the actual storage.**
- **5. Kubernetes creates a PV and binds it to the PVC.**
- **6. Pod mounts the PVC and uses the storage.**  

### 42. What is a volume in Kubernetes?  
**A Volume in Kubernetes provides storage to containers inside a Pod.**  
**It allows containers to share data and retain data across container restarts.**    
  
**Kubernetes supports different types of Volumes;**
- **emptyDir : Temporary storage shared between containers in a pod**

- **hostPath : Mounts a directory from the worker node**

- **persistentVolumeClaim : Uses persistent storage through a PVC**

- **configMap : Mounts configuratin files**

- **secret : Mounts sensitive data**

- **NFS : Uses network file storage**

      
### 43. What happens to data if a Pod dies?
**If a Pod dies, data stored inside its container filesystem is lost when the container is removed.**  
**Data in emptyDir survives container restarts but is deleted when the Pod is removed.**  
**To preserve application data across Pod deletion or recreation, we use Persistent Volumes and Persistent Volume Claims.**  
**For Example, a MySQL Pod can use an AWS EBS-backed PVC to retain database data.**  

### 44. What is ConfigMap?
**A ConfigMap is a Kubernetes object to store non-sensitive configuration data separately form application container images.**  
**It helps you manage application configuration without rebuilding the Docker image.**  

**ConfigMap Stores non-sensitive information like;**  
- **Database hostname**
- **Application environment.**
- **API endpoints.**
- **Application configuration files.**
- **Log levels.**

### 45. What is Secret? 
**A Secret is a Kubernetes object used to store sensitive information such as Passwords, API kays, and authentication tokens.**  
**Instead of hardcoding credentials inside application code or Docker images, we store them in Secrets.**  

**Secrets Stores non-sensitive information like;**  
- **Database passwords.**
- **API keys.**
- **SSH credentials.**
- **TLS certificates.**
- **Authentication tokes.**

### 46. Difference between ConfigMap and Secret? 
| ConfigMap                              | Secret                                   | 
|----------------------------------------|------------------------------------------|  
| Non-sensitive configuration            | Sensitive information                    |  
| Store Application settings             | Store passwords, tokens, keys            |  
| In plain text                          | Base4 encoded in YAML/API representation | 
| Not intended for secrets               | Encryption at rest can be configured     |  
| Environment variables or mounted files | Environment variables or mounted files   | 


### 47. How do you pass environment variables from ConfigMap? 
**We can pass environment variables from a ConfigMap using `envFrom` or `valueFrom`.**  

**With `envFrom`, all key-value pairs from the ConfigMap are inected into the container as environment variables.**  

**With `valueFrom`. `configMapKeyRef`, we can inject only a specific key. This helps keep application configuration separate from Docker images.**


### 48. What is RBAC?  
**RBAC(Role-Based-Access-Control) is a Kubernetes authorization mechanism used to control who can access Kubernetes resource and what actions thy can perform.**  
**In simple terms, RBAC defines who can do what in a Kubernetes cluster.**  

**4 main RBAC components**  
- **Role: Defines permissions within namespace**
  
- **ClusterRole: Defines permissions across the cluster.**
  
- **RoleBinding: Assigns a Role or ClusterRole to users or groups within a namespace.**
  
- **ClusterRoleBinding: Assigns a ClusterRole across the cluster.**

**Common RBAC verbs**    
- **get = View a specific resource**
- **list = List resources**
- **watch = Monitor resource changes**
- **create = Create resources**
- **update = Update resources**
- **patch = Partially update resources**
- **delete = Delete resources**   


### 49. What is a ServiceAccount? 
**A ServiceAccount is an identity used by applications running inside Kubernetes Pods to authenticate with the Kubernetes API Server.**  
**We assign permissions to the ServiceAccount using RBAC and attach it to the Pod through `serviceAccountName`.**  
**This allows applications to access only the Kubernetes resources they need, following the principle of least privilege.**    

**Important AWS EKS Point: For application running in EKS that need access to AWS services such as S3, we commonly use EKS Pod Identity or IAM Roles for Service Account (IRSA).**  
**This allows Pods to access AWS resources without storing AWS access keys inside containers.**

### 50. What are Network Policies?
**NetworkPolicy is a Kubernetes resource used to control incoming and outgoing network traffic for Pods. It works like a firewall by defining which Pods, namespaces, or IP ranges can communicate with selected Pods.**  
**For example, I can allow backend Pods to access database Pods on port 3306 while blocking unwanted traffic. NetworkPolicy enforcement requires a compatible CNI plugin.**  

### 51. How do you restrict container privileges? 
**We restrict container privileges using Security Contexts, Linux features, and Pod Security Standards. We ensure containers run as non-root users, disable privilege escalation, drop unnecessary Linux capabilities, use read-only root filesystems, and apply seccomp profiles.**   

**Important Security Settings**  
- **`runAsNonRoot: true`  = Prevents running as root**
- **`runAsUser: 1000`     = Runs container with a non-root user**
- **`privileged: false`   = Prevents privileged container access**
- **`alloPrivilegeEscalation: false`  = Prevents processes from gaining additional privileges**
- **`readOnlyRootFilesystem`: true  = Prevents modifications to the container's root filesystem**
- **`capabilities.drop: [ALL]`  = Removes unnecessary Linux capabilities**
- **`seccompProfile`  = Restrict system calls available to the container**


### 52. What is Horizontal Pod Autoscaler (HPA)?
**It automatically scales the number of Pod replicas based on CPU, memory, or custom metrics.**  
**For example, if CPU utilization increases beyond the configured target of 70%, HPA increases the number of replicas.**   
**When traffic decreases, it scales them down.**  

- **HPA typically requires Metrics Server for CPU and memory metrics.**
- **CPU utilization targets depend on CPU resource request being configured.**
- **HPA works with Deployments and StatefulSets.**
- **HPA does not create additional worker nodes; Cluster Autoscaler handles node scaling.**
     
### 53. What is Vertical Pod Autoscaler?  
***It automatically adjust CPU and memory resource request based on application usage.**  
**For example, if an application consistently requires more memory than its current allocation, VPA recommends or applies higher resource request.**  
**It helps prevent resource shortages and optimize resource utilization.** 

**Unlike HPA, which scales the number of Pods, VPA adjust resource allocated to containers.**


### 54. How do you monitor Kubernetes?   
**I monitor Kubernetes using Prometheus and Grafana. Prometheus collects metrics from Kubernetes components, worker nodes, Pods, and applications, while Grafana provides dashboards for visualization.**  

**I use Node Exporter for node-level metrics and kube-state-metrics for Kubernetes object status. I also configure Alertmanager for notifications.**  

**Additionally, I use kubectl commands like `get pods`,`top nodes`, `top pods`, `describe`, and `logs` for troubleshooting.**  

**I monitor CPU, memory, Pod restarts, node health, application latency, and error rates.**  

**1. Cluster level:**
- **Cluster health and API Server availability.**
- **Number of worker nodes.**
- **Node CPU and memory utilization.**
- **Node readiness status.**

**2. Pod level:**
- **Pod CPU and memory consumption.**
- **Pod restarts and failures.**
- **Pending Pods and CrashLoopBackOff.**
- **OOMKilled containers.**

**3. Application level:**
- **Application response time.**
- **HTTP 4xx and 5xx errors.**
- **Request rate.**
- **Application availability.**

**4. Infrastructure level:**  
- **Disk usage.**
- **Network traffic.**
- **Persistent Volume capacity.**
- **Container resource utilization.**


### 55. How do you collect logs? 
**I implement centralized logging using Fluent Bit. Applications write logs to stdout and stderr, which are collected by agents running as DaemonSets on worker nodes.**  
**These agents forward logs to centralized platforms like AWS CloudWatch. I use Grafana to search, filter, and analyze logs.**  
**This helps troubleshoot application errors, container failures, and production incidents across the cluster.**


### 56. How do you debug cluster issues?    
**When debugging Kubernetes cluster issues, I follow a systematic troubleshooting approach.**  

**First, I check cluster and node health using `kubectl get nodes`.**  

**Then I check Pod status, events, and logs using `kubectl get pods`, `kubectl describe`, and `kubectl logs`.**  

**I investigate CPU and memory utilization using `kubectl top`.**  

**If necessary, I troubleshoot networking, DNS, CNI, storage, and application configurations.**  

**After identifying the root cause, I apply the fix and verify that the cluster and application are healthy.**


### 57. Pod stuck in Pending state. What do you check? 
**When a Pod is stuck in Pending state, I first run `kubectl describe pod` and check the Events section to identify the scheduling issue.**  

**I check node resource availability using `kubectl top nodes` and `kubectl describe node`.** 

**I also verify PVC binding, StorageClass configuration, node affinity, node selectors, and taints and toleration.**  

**For example, if the scheduler reports insufficient memory, I check resource request and node capacity.**   

**Depending on the root cause, I optimize resource request or scale the worker nodes.**  

**Finally, I verify that the Pod is successfully scheduled and running.**  

**Remember these 5 major causes;**  
**1. Insufficient CPU or memory.**  
**2. PVC or Storage provisioning issues.**  
**3. Node selectors of affinity mismatch.**  
**4. Taints and tolerations mismatch.**  
**5. Node capacity or Pod limit reached.**  

### 58. Pod stuck in CrashLoopBackOff. What do you do? 
**When a Pod enters CrashLoopBackOff, I first check its status using `kubectl get pods`, then run `kubectl describe pod` to investigate events, restart counts, and termination reasons.**  

**Next, I check the previous container logs using `kubectl logs --previous`, because they often reveal why the application crashed.**  

**I investigate common causes like application errors, incorrect configurations, missing environment variables, database connectivity, OOMKilled, and liveness probe failures.**  

**After identifying the root cause, I fix the configuration or resource issue and monitor the Pod to ensure it becomes stable.**  

**Remember these major causes;**  
**1. Application crash**  
**2. Incorrect command**  
**3. Missing environment variables**  
**4. Database unavailable**  
**5. OOMKilled**  
**6. Liveness probe failure**  
**7. Permission denied**  
**8. Missing dependencies**


### 59. Pod cannot pull image. 
**When a Pod cannot pull an image, I first check its status using `kubectl get pods`, then run `kubectl describe pod` and examine the Events section.**  

**I check whether the image name and tags are correct, whether the registry is accessible, and whether authentication credentials are configured properl.**  

**In AWS EKS, I also verify the worker node IAM role has the required ECR permissions and check network connectivity.**  

**After fixing the root cause, I verify that the image is pulled successfully and the Pod starts running**  

**Remember these 4 major causes;**  
**1. Incorrect image name or tag.**
**2. Private registry authentication failure.**  
**3. Missing ECR IAM permissions.**  
**4. Network connectivity issues.**  


### 60. Service not reachable. 
**The most common causes are incorrect Service Selectors, unhealthy Pods, port mismatches, NetworkPolicies, or DNS issues.**  

**When a Kubernetes Service is not reachable, I first check the Service configuration using `kubectl get svc` and `kubectl describe svc`.**  

**Then I verify its endpoints using `kubectl get endpoints` or EndpointSlices.**  

**If there are no endpoints, I check whether Service selectors match Pod label and whether the Pods are Ready.**  

**Next, I verify port and targetPort configurations, test connectivity from another Pod, and investigate DNS, NetworkPolicies, CNI, and Ingress if required.**  

**Finally, I validate that the Service is routing traffic successfully.**


### 61. Pod not scheduling.  
**When a Pod is not scheduling, I first check its status using `kubectl get pods` and run `kubectl describe pod` to investigate FailedScheduling events.**  

**I check node CPU and memory availability, allocatable resources, taints and tolerations, node selectors, affinity rules, and PVC status.**  

**For example, if the scheduler reports insufficient CPU, I check resource request and node capacit.**  

**Depending on the root cause, I optimize resource requests or scale worker nodes using Cluster Autoscaler in EKS.**  

**Finally, I verify that the Pod is successfully scheduled and running.**  

**Remember these  major causes;**  
**1. Insufficient CPU or memory.**  
**2. Node taints and tolerations mismatch.**  
**3. Node selector or affinity mismatch.**  
**4. Maximum Pod capacity reached.**  
**5. PVC or storage provisioning issues.**  
**6. Node unavailable or unhealthy.**  


### 62. Node not ready.    
**When a Kubernetes node becomes NotReady, I first check `kubectl get nodes` and then use `kubectl describe node` to investigate node conditions and events.**  

**I check kubelet and containerd status, their logs, CPU and memory utilization, disk space, and network connectivity with the API Server.**  

**In AWS EKS, I also investigate EC2 Instance health, security groups, and node IAM permissions where relevant.**  

**After identifying the root cause, I recover the node or replace it if necessary.**  

**Finally, I verify that the node returns to Ready and workloads are healthy.**  

**Remember these 5 major causes;**  
**1. Kubelet is stopped or unhealthy.**  
**2. Container runtime (containerd) failure.**  
**3. MemoryPressure, DiskPressure, or PIDPressure.**  
**4. Network connectivity problems with the API Server.**  
**5. EC2 Instance or underlying infrastructure failure.**


### 63. Deployment not updating. 
**When a Kubernetes Deployment is not updating, I first check its status using `kubectl get deployment` and `kubectl rollout status`.**   

**Then I inspect the Deployment events and ReplicaSets using `kubectl describe deployment` and `kubectl get rs`.**  

**I check whether new Pods are failing because of image pull errors, insufficient resources, application crashes, or readiness probe failures.**  

**I also verify the image version and rolling update strategy.**  

**After identifying the root cause, I fix the issue and monitor the rollout.**  

**If the new release is impacting production, I use `kubectl rollout undo deployment` to restore the previous stable version.**  


### 64. PVC stuck in pending.     
### 65. High CPU usage in Pods.    
### 66. How do you debug networking issues?      
### 67. How do you deploy applications to Kubernetes using CI/CD?    
### 68. How do you implement blue-green deployment?    
### 69. What is canary deployment?      


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
How do you build Docker images for Kubernetes?     
What is Helm?     
What is Helm chart?    
What is ArgoCD?    
