### 1. What are the main components of Kubernetes architecture? 
**Kubernetes architecture is divided into two main parts:**  
- **1. Control Plane**
- **2. Worker Nodes**

**1. The Control plane**   
**Managing the entire cluster. It's main components are;**   
  
**i). kube-apiserver:**    
**The API server acts as the entry point to the cluster.**  
**All communication between users, worker nodes, and control plane components happens through the API Server.**  
  
**ii). etcd:**   
**It is a distributed key-value database.**   
**It stores all Kubernetes cluster information.**   
**It maintains the cluster's desired and current state information.**  
  
**iii). kube-shceduler:**   
**It is responsible for scheduling nely created Pods onto suitale worker nodes.**  
**It checks available CPU, memory, resource requests, node selectors, taints, and tolerations.**  
  
**iv). kube-controller-manager:**   
**It runs different controllers that continuosly monitor the cluster.**  
**It ensures the actual cluster state matches the desired state.**    
**It automatically takes corrective actions when something goes wrong.**   
  
**v). cloud-controller-manager:**   
**It integrates Kubernetes with cloud providers such as AWS, Azure, and GCP.**  
**It manages cloud-specific resources.**  

**2. The Worker Nodes**   
**Run the actual application workloads. Their main components are;**  
  
**i). kubelet:**    
**It is an agent running on every worker node.**      
**It communicates with the API server.**     
**It ensures containers inside Pods are running as expected.**    
  
**ii). kube-proxy:**   
**It maintains network rules on worker nodes.**     
**It enables communication between Kubernetes Services and backend Pods.**    
  
**iii). Container Runtime:**   
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

### 17. What are Liveness probes?
**A Liveness Probe is used to check whether an application container is still functioning properly.**  
**If the health fails repeatedly, Kubernetes restarts the container automatically.**  
**It helps detect issues like application deadlocks or unresponsive processes and provides automatic recovery.**

### 18. What are Readiness probes? 
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
**When a PVC is stuck in Pending state, I first check its status using `kubectl get pvc` and investigate the Events section using `kubectl describe pvc`.**  

**Then I verify StorageClass configuration, PV availability, requested storage capacity, and access modes.**  

**If dynamic provisioning is enables, I check the CSI driver and its logs.**  

**In AWS EKS, I specifically investigate the EBS CSI driver, IAM permissions, and Availability Zone compatibility.**  

**Remember these 5 major causes;**  
**1. No matching PersistenVolume.**  
**2. Incorrect or missing StorageClass**  
**3. CSI driver failure.**  
**4. Insufficient storage capacity.**  
**5. Availability Zone or volume topology mismatch.**  


### 65. High CPU usage in Pods. 
**When I encounter high CPU usage in Kubernetes, I first identify the affected Pods using `kubectl top pods`.**  

**Then I check container-level CPU consumption, resource request and limits, and application logs.**  

**In production, I use Prometheus and Grafana to investigate CPU utilization, throttling, traffic patterns, and performance trends.**  

**If the issue is caused by increased traffic, I use HPA to scale the number of replicas. If it's caused by inefficient application code or background processing, I investigate and optimize the application.**  

**Finally, I monitor the workload to ensure CPU utilization and application performance return to normal.**  

**Remember these 5 troubleshooting area;**  
**1. Identify high-CPU Pods using `kubectl top pods`.**  
**2. Check CPU requests and limits.**  
**3. Investigate CPU throttling.**  
**4. Analyze application performance and traffic.**  
**5. Scale using HPA or optimize the application.**  


### 66. How do you debug networking issues? 
**I debug networking issues layer by layer. First, I check node and Pod health using `kubectl get nodes` and `kubectl get pods -o wide`.**  

**Then I verify Pod-to-Pod connectivity, Service configuration, endpoints, and port mappings. If Service-name resolution fails, I investigate CoreDNS.**  

**Next, I check NetworkPolicies, CNI plugin health, and node-level networking. In EKS, I specifically check AWS VPC CNI, IP availability, security groups, route tables, and NAT Gateway configuration.**  

**Finally, if external access is failing, I investigate the Ingress controller, Load Balancer, and target group health.**  

**I use `kubectl describe`, application logs, and connectivity tests to identify the exact failure point before making changes.**   

- **One Pod affected → Investigate that Pod and its configuration.**
- **One Node affected → Investigate node networking and CNI.**
- **All Pods affected → Investigate cluster networking, DNS, or shared infrastructure.**
- **Internal traffic works but external traffic fails → Investigate routing, security groups, NAT, or Load Balancer configuration.**


### 67. How do you deploy applications to Kubernetes using CI/CD? 
**I use Jenkins to automate application deployment to Kubernetes.**  

**Whenever a developer pushes code to GitHub, a webhook triggers the Jenkins pipeline. Jenkins checks out the source code, builds the application, runs unit tests, and perform SonarQube code analysis.**  

**Next, it builds a Docker image, scans it using Trivy, and pushes the approved image to Amazon ECR.**  

**After that, Jenkins connects to the EKS cluster using authorized AWS and Kubernetes credentials. It updates the Kubernetes Deployment with the new image using kubectl or Helm.**  

**Kubernetes performs a rolling update, creating new Pods and gradually termination old ones. I verify the deployment using `kubectl rollout status` and check Pod health, logs and events.**  

**Finally, I monitor the application using Prometheus, Grafana, and CloudWatch. If deployment fails, I investigate the root cause and roll back to the previous stable version.**  


### 68. How do you implement blue-green deployment? 
**I implement Blue-Green deployment in Kubernetes using two separate Deployments, Blue and Green.**  

**Blue runs the existing production version, while Green runs the new application version.**  

**First, I deploy the new version to Green and wait until all Pods become Ready. Then I perform health checks and smoke tests to verify the application.**  

**Once everything is working correctly, I switch the Kubernetes Service selector from Blue to Green. This redirects production traffic to the new version.**  

**After deployment, I monitor application health, errors and latency using Prometheus and Grafana.**  

**If I notice any critical issue, I can quickly roll back by changing the Service selector to Blue.**  

**I usually automate this complete processing using Jenkins, Kubernetes manifests or Helm, and Amazon EKS.**  

### 69. What is canary deployment?      
**Canary deployment is a deployment strategy where we release a new application version to a small percentage of production users before making it available to everyone.**  

**For example, suppose my application is running version 1. I deploy version 2 alongside it and initially route around 10% of traffic to the new version while the remaining 90% continues using version 1.**  

**I monitor application metrics such as HTTP errors, response time, CPU and memory using Prometheus and Grafana.**  

**If everything looks good, we gradually increase traffic to the new version, such as 25%, 50%, and finally 100%.**  

**If we detect any critical issue, we stop the rollout and route traffic back to the stable version.**   

**In Kubernetes, I can implement this using Argo Rollouts, or an Ingress controller that supports weighted traffic routing.**  


### 70. How do you handle secrets securely?
**I handle secrets securely by never hardcoding credentials in application code, Docker images, or Git repositories.**  

**In Kubernetes, I use Kubernetes Secrets to manage sensitive information such as database passwords and API keys. I restrict access through RBAC and enable encryption at rest.**  

**For production workloads on AWS EKS, I prefer AWS Secrets Manager integrated through the Secrets Store CSI Driver.**  

**I use IAM roles through EKS Pod Identity or IRSA to allow application to access only the secrets they require.**  

**In Jenkins, I use Jenkins Credentials or IAM roles instead of hardcoding credentials in pipeline scripts.**  

**I also follow least-privilege access, secret rotation, TLS encryption, and auditing to reduce the risk of credential exposure.**  


### 71. How do you upgrade Kubernetes cluster? 
**I follow a planned, step-by-step process to upgrade a Kubernetes cluster.**  

**First, I check the current cluster and node versions and review the target version's release notes, deprecated APIs and compatibility requirements.**  

**Before upgrading production, I ensure application data, persistent volumes and configurations are backed up.**  

**In EKS, I upgrade the control plane first, Once it is healthy, I upgrade compatible add-ons such as VPC CNI, CoreDNS and kube-proxy.**  

**Next, I upgrade the worker node using managed node group updates or rolling node replacement. I make sure application have sufficient replicas and check PodDisruptionBudgets to minimize downtime.**  

**Finally, I validate node health, Pod status, networking, DNS, storage and application availability.**  

**I monitor the cluster using Prometheus, Grafana and CloudWatch throughout the process.**  


### 72. How do you backup etcd?  

**AWS EKS , AWS manages the underlying etcd infrastructure, so we cannot take etcd snapshots. Instead, we use solutions such as Velero and AWS Backup to protect Kubernetes resource and persistent application data.**    
  
**In a self-managed Kubernetes cluster, etcd stores the entire cluster state, including Deployments, Pods, Services, Secrets and ConfigMaps.**  

**We can take backup using the `etcdctl snapshot save` command with the appropriate endpoint, CA certificate, client certificate and private key.**  

**After creating the snapshot, We can verify it using `etcdctl snapshot status` and upload it to secure external storage such as Amazon S3 with encryption and restricted access.**  

**We can automate backup using cron jobs or a backup solution and configure retention policies and monitoring.**  

**We also periodically test restoration to ensure the backup are recoverable.**  


### 73. How do you manage multiple environments? 
**I manage multiple environments such as Dev, QA, Staging and Production using Terraform, Kubernetes, Helm and Jenkins.**  

**For infrastructure, I use reusable Terraform modules with separate environments configurations and isolated state files.**  

**In Kubernetes, I use separate namespaces or clusters depending on the isolation requirements.**  

**I use Helm charts to deploy the same application across environments with different values files for replica counts, resource limits and configurations.**  

**For CI/CD, Jenkins automates the deployment process. First, the application is built and tested, then a Docker image is created and pushed to ECR.**  

**The same immutable image is promoted from Dev to QA, then Staging and finally Production after successful testing and approval.**  

**I also maintain separate secrets, IAM permissions and access controls for each environment.**  

**This approach provides consistency, security and controlled deployments.**  


### 74. What is IAM Roles for Service Accounts (IRSA)?
**IRSA(IAM Roles for Service Account) is an AWS feature that allows Kubernetes Pods running in an EKS cluster to securely access AWS services using IAM permissions, without storing AWS access keys inside the Pods.**

**"It connects a Kubernetes ServiceAccount with an AWS IAM Role."**

**We associate an IAM Role with a Kubernetes ServiceAccount using the EKS OIDC provider. When a Pod uses that ServiceAccount, it receives temporary AWS credentials through AWS STS.**  

**For example, if an application running in EKS needs to access S3, I create an IAM Role with the required S3 permissions, associate it with a ServiceAccount, and configure the Pod to use that ServiceAccount.**  

**This provides fine-grained access control, follows least privilege, and avoids hardcoding AWS credentials.**  

**REMEMBER**
**IRSA is different from granting permissions to the worker node's IAM Role. With IRSA, you can give one application S3 access while another application has access to DynamoDB, even if both run on the same worker node.**  

**Also, IRSA requires correct OIDC trust-policy conditions restricting access to the intended namespace and ServiceAccount. Without those restrictions, other workloads may be able to assume the role.**


### 75. How do you expose services in EKS?  
**In EKS, we expose applications using four main Kubernetes mechanisms:**  
- **ClusterIP**
- **NodePort**
- **LoadBalancer**
- **Ingress**

**ClusterIP is used for internal communication between services. NodePort exposes an application through a port on worker nodes. LoadBalancer provisions an AWS load balancer, commonly an NLB when using the AWS Load Balancer Controller. Ingress is used for HTTP and HTTPS routing, and in EKS we commonly use the AWS Load Balancer Controller to provision an ALB.**  

**In Production, I generally use ALB Ingress for public-facing applications, ClusterIP for internal microservices, and private networking for databases. This provides centralized routing and avoids creating a separate load balancer for every application.**  

### 76. What are taints and tolerations? 
**Taints and Tolerations are Kubernetes scheduling mechanisms used to control workload placement.**  

**A taint is applied to a Node to prevent Pods from being scheduled there unless they have a matching tolerations. A toleration is configured in a Pod specification to allow it to run on that tainted Node.**    

**There are three taint effect;**  
- **`NoSchedule`→ Prevents new Pods without matching toleration from being scheduled.**  
- **`PreferNoSchedule` → Kubernetes tries to avoid scheduling those Pods but may still schedule them.**  
- **`NoExecute` → Prevents new Pods and evicts existing Pods that don't tolerate the taint.**  
  
**For example, in an EKS production environment, if I want to dedicate certain worker nodes to production workloads, I apply a NoSchedule taint to those nodes and configure matching tolerations in production Pods.**  

**This helps isolate workloads and reserve dedicated compute resources.**  

**IMPOTANT**
**Toleration does not guarantee that the Pod will be scheduled on that node. It only removes the taint-based restriction. Use `nodeSelector` or node affinity to target specific nodes.**  


### 77. What are node selectors? 
**Node Selector is a Kubernetes scheduling mechanism that allows us to schedule Pods on specific worker nodes based on node labels.**  

**For example, in an EKS cluster, if we have separate node groups for general workloads and memory-intensive applications, we can label the memory-optimized nodes with `workload=memory-intensive` and configure the application's Pod specification with the same node selector.**  

**Kubernetes will schedule that Pod only on nodes matching the specified label.**  


### 78. What are affinity and anti-affinity rules? 
**Affinity and Anti-Affinity are Kubernetes scheduling mechanisms that control where Pods should or should not run based on Node or Pod labels.**  

- **Affinity: Attracts a Pod towards specific Nodes or other Pods.**
- **Anti-Affinity: Avoids placing a Pod on specific Nodes or alongside certain Pods.**

**There are three main types: Node Affinity, Pod Affinity, and Pod Anti-Affinity.**  
- **Node Affinity: Schedules Pods on Nodes based on specific labels.**
**Example: Suppose you have memory-optimized nodes and want to schedule a memory-intensive application only on those nodes.**
- **Pod Affinity: Schedules a Pod close to other Pods based on their labels.**
**Example: Suppose your frontend and backend applications need to run in the same availability zone for better communication.**
- **Pod Anti-Affinity: Prevents matching Pods from running close together.**
**Example: Suppose your application has three replicas. You want them distributed across different worker nodes to improve availability.**

  
  
**Node Affinity schedules Pods based on Node labels. Pod Affinity places Pods close to other matching Pods, while Pod Anti-Affinity keeps matching Pods away from each other.**  

**For example, in an EKS production environment, I can use Node Affinity to schedule payment applications on dedicated production nodes as Pod Anti-Affinity to distribute their replicas across different worker nodes for high availability.**   

**We can use required rules for strict scheduling and preferred rules when scheduling is flexible.**  

**Two important Node Affinity types:**  
- **`requiredDuringSchedulingIgnoreDuringExecution`: Hard rule. The Pod must match the condition to be scheduled.**
- **`preferredDuringSchedulingIgnoreDuringExecution`: Soft rule. Kubernetes prefers matching Nodes but can choose another Node.**


### 79. What are resource requests and limits? 
**Resource request and limits are used in Kubernetes to manage CPU and memory consumption of containers.**   

**Request define the minimum resource required by a container, and Kubernetes uses them to decide which Node can accommodate the Pod.**  

**Limits define the maximum resource a container can consume.**  

**For example, if I configure a CPU request of 250 millicores and a limit of 500 millicore, Kubernetes schedules tha Pod based on its 250m request, while the container can use up to 500m CPU.**  

**If the container exceeds its CPU limit, it gets throttled. If it exceeds its memory limit, it can be terminated with an OOMKilled error.**   

**In production, I monitor resource usage and configure requests and limits based on actual application requirements to avoid resource contention and performance issues.**  

- **CPU is a compressible resource: Kubernetes throttles CPU usage.**
- **Memory is an incompressible resource: The container can be killed when it exceeds its memory limit.**
  

### 80. How do you build Docker images for Kubernetes? 
**First, we create a Dockerfile based on the application requirements. Then I build the application, run unit test, and create a Docker images using the Docker build command.**  

**After building the image, I scan it using Trivy for vulnerabilities, tag it with a unique version, and push it to Amazon ECR.**  

**Then, in my Jenkins CI/CD pipeline, I authenticate with the EKS cluster and deploy the ECR image using Kubernetes Deployment YAML or Helm.**  

**Finally, I verify the deployment using kubectl rollout status, check Pod health, and monitor application logs.**  

**I also follow Docker best practices, such as using lightweight base images, running containers as non-root users, avoiding hardcoded secrets, and using versioned image tags.**    


### 81. What is Helm and Helm chart?
**Helm is a package manager for Kubernetes that helps us install, configure, upgrade, and manage Kubernetes applications easily.**  

**In simple words, Helm allows us to manage multiple Kubernetes YAML files using reusable templates called Helm Charts.**  

**Without Helm, we manually manage multiple Kubernetes YAML files such as;**
- **Deployment**
- **Service**
- **ConfigMap**
- **Secret**
- **Ingress**
- **HorizontalPodAutoscaler**

**With Helm, we package these resources into one reusable Helm Chart and deploy them using a single command.**    

**1. Why do we use Helm?**  
**Suppose you have an application running in three environments:**  
- **Devlopment**
- **QA**
- **Production**

**Without Helm, you might maintain separate YAML files for each environment.**  
**This creates duplication and makes configuration management difficult.**  

**
### 83. What is ArgoCD?    
