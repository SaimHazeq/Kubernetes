# Kubernetes Interview Q&A

A structured Kubernetes interview preparation guide covering Pods, Namespaces, Nodes, Services, ReplicaSets, Deployments, Scheduling, Affinity, Taints, Resource Management, and Monitoring.

---

## Pods

### 1. Run a command to view all the pods in the current namespace.

`kubectl get pods`

Note: create an alias (`alias k=kubectl`) and get used to `k get po`

### 2. Run a pod called "nginx-test" using the "nginx" image.

`k run nginx-test --image=nginx`

### 3. Assuming that you have a Pod called "nginx-test", how to remove it?

`k delete po nginx-test`

### 4. In what namespace the <code>etcd</code> pod is running? list the pods in that namespace.

`k get po -n kube-system`

Let's say you didn't know in what namespace it is. You could then run `k get po -A | grep etc` to find the Pod and see in what namespace it resides.

### 5. List pods from all namespaces.

`k get po -A`

The long version would be `kubectl get pods --all-namespaces`.

### 6. Write a YAML of a Pod with two containers and use the YAML file to create the Pod (use whatever images you prefer).

```
cat > pod.yaml <<EOL
apiVersion: v1
kind: Pod
metadata:
  name: test
spec:
  containers:
  - image: alpine
    name: alpine
  - image: nginx-unprivileged
    name: nginx-unprivileged
EOL

k create -f pod.yaml
```

If you ask yourself how would I remember writing all of that? no worries, you can simply run `kubectl run some_pod --image=redis -o yaml --dry-run=client > pod.yaml`.                                                                       
If you ask yourself "how am I supposed to remember this long command" time to change attitude ;)

### 7. Create a YAML of a Pod without actually running the Pod with the kubectl command (use whatever image you prefer).

`k run some-pod -o yaml --image nginx-unprivileged --dry-run=client > pod.yaml`

### 8. How to test a manifest is valid?

with `--dry-run` flag which will not actually create it, but it will test it and you can find this way, any syntax issues.

`k create -f YAML_FILE --dry-run`

### 9. How to check which image a certain Pod is using?

`k describe po <POD_NAME> | grep -i image`

### 10. How to check how many containers run in single Pod?

`k get po POD_NAME` and see the number under "READY" column.

You can also run `k describe po POD_NAME`

### 11. Run a Pod called "remo" with the the latest redis image and the label 'year=2017'.

`k run remo --image=redis:latest -l year=2017`

### 12. List pods and their labels.

`k get po --show-labels`

### 13. Delete a Pod called "nm".

`k delete po nm`

### 14. List all the pods with the label "env=prod".

`k get po -l env=prod`

To count them: `k get po -l env=prod --no-headers | wc -l`

### 15. Create a static pod with the image <code>python</code> that runs the command <code>sleep 2017.

First change to the directory tracked by kubelet for creating static pod: `cd /etc/kubernetes/manifests` (you can verify path by reading kubelet conf file)    
Now create the definition/manifest in that directory
`k run some-pod --image=python --command sleep 2017 --restart=Never --dry-run=client -o yaml > static-pod.yaml`

### 16. Describe how would you delete a static Pod.

Locate the static Pods directory (look at `staticPodPath` in kubelet configuration file).
Go to that directory and remove the manifest/definition of the staic Pod (`rm <STATIC_POD_PATH>/<POD_DEFINITION_FILE>`)

## Troubleshooting Pods

### 1. You try to run a Pod but see the status "CrashLoopBackOff". What does it means? How to identify the issue?

The container failed to run (due to different reasons) and Kubernetes tries to run the Pod again after some delay (= BackOff time).

Some reasons for it to fail:
  - Misconfiguration - misspelling, non supported value, etc.
  - Resource not available - nodes are down, PV not mounted, etc.

Some ways to debug:

1. `kubectl describe pod POD_NAME`
   1. Focus on `State` (which should be Waiting, CrashLoopBackOff) and `Last State` which should tell what happened before (as in why it failed)
2. Run `kubectl logs mypod`
   1. This should provide an accurate output of 
   2. For specific container, you can add `-c CONTAINER_NAME`
3. If you still have no idea why it failed, try `kubectl get events`

### 2. What the error <code>ImagePullBackOff</code> means?

Most likely you didn't write correctly the name of the image you try to pull and run. Or perhaps it doesn't exists in the registry.

You can confirm with `kubectl describe po POD_NAME`

### 3. How to check on which node a certain Pod is running?

`k get po POD_NAME -o wide`

### 4. Run the following command: <code>kubectl run ohno --image=sheris</code>. Did it work? why not? fix it without removing the Pod and using any image you would like.

Because there is no such image `sheris`. At least for now :)

To fix it, run `kubectl edit ohno` and modify the following line `- image: sheris` to `- image: redis` or any other image you prefer.

### 5. You try to run a Pod but it's in "Pending" state. What might be the reason?

One possible reason is that the scheduler which supposed to schedule Pods on nodes, is not running. To verify it, you can run `kubectl get po -A | grep scheduler` or check directly in `kube-system` namespace.

### 6. How to view the logs of a container running in a Pod?

`k logs POD_NAME`

### 7. There are two containers inside a Pod called "some-pod". What will happen if you run `kubectl logs some-pod`

It won't work because there are two containers inside the Pod and you need to specify one of them with `kubectl logs POD_NAME -c CONTAINER_NAME`


## Namespaces

### 1. List all the namespaces.

`k get ns`

### 2. Create a namespace called 'alle'.

`k create ns alle`

### 3. Check how many namespaces are there.

`k get ns --no-headers | wc -l`

### 4. Check how many pods exist in the "dev" namespace.

`k get po -n dev`

### 5. Create a pod called "kartos" in the namespace dev. The pod should be using the "redis" image.

If the namespace doesn't exist already: `k create ns dev`

`k run kratos --image=redis -n dev`

### 6. You are looking for a Pod called "atreus". How to check in which namespace it runs?

`k get po -A | grep atreus`


## Nodes

### 1. Run a command to view all nodes of the cluster.

`kubectl get nodes`

Note: create an alias (`alias k=kubectl`) and get used to `k get no`

### 2. Create a list of all nodes in JSON format and store it in a file called "some_nodes.json".

`k get nodes -o json > some_nodes.json`

### 3. Check what labels one of your nodes in the cluster has.

`k get no minikube --show-labels`

## Services

### 1. Check how many services are running in the current namespace.

`k get svc`

### 2. Create an internal service called "sevi" to expose the app 'web' on port 1991.
 
`kubectl expose pod web --port=1991 --name=sevi`

### 3. How to reference by name a service called "app-service" within the same namespace?

app-service

### 4. How to check the TargetPort of a service?

`k describe svc <SERVICE_NAME>`

### 5. How to check what endpoints the svc has?

`k describe svc <SERVICE_NAME>`

### 6. How to reference by name a service called "app-service" within a different namespace, called "dev"?

app-service.dev.svc.cluster.local

### 7. Assume you have a deployment running and you need to create a Service for exposing the pods. This is what is required/known:

* Deployment name: jabulik
* Target port: 8080
* Service type: NodePort
* Selector: jabulik-app
* Port: 8080

`kubectl expose deployment jabulik --name=jabulik-service --target-port=8080 --type=NodePort --port=8080 --dry-run=client -o yaml -> svc.yaml`

`vi svc.yaml` (make sure selector is set to `jabulik-app`)

`k apply -f svc.yaml`


## ReplicaSets

### 1. How to check how many replicasets defined in the current namespace?

`k get rs`

### 2. You have a replica set defined to run 3 Pods. You removed one of these 3 pods. What will happen next? how many Pods will there be?

There will still be 3 Pods running theoretically because the goal of the replica set is to ensure that. so if you delete one or more Pods, it will run additional Pods so there are always 3 Pods.

### 3. How to check which container image was used as part of replica set called "repli"?

`k describe rs repli | grep -i image`

### 4. How to check how many Pods are ready as part of a replica set called "repli"?

`k describe rs repli | grep -i "Pods Status"`

### 5. How to delete a replica set called "rori"?

`k delete rs rori`

### 6. How to modify a replica set called "rori" to use a different image?

`k edis rs rori`

### 7. Scale up a replica set called "rori" to run 5 Pods instead of 2.

`k scale rs rori --replicas=5`

### 8. Scale down a replica set called "rori" to run 1 Pod instead of 5.

`k scale rs rori --replicas=1`

## Troubleshooting ReplicaSets

### 1. Fix the following ReplicaSet definition

```yaml
apiVersion: apps/v1
kind: ReplicaCet
metadata:
  name: redis
  labels:
    app: redis
    tier: cache
spec:
  selector:
    matchLabels:
      tier: cache
  template:
    metadata:
      labels:
        tier: cachy
    spec:
      containers:
      - name: redis
        image: redis
```
**Answer**
kind should be ReplicaSet and not ReplicaCet :)

### 2. Fix the following ReplicaSet definition

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: redis
  labels:
    app: redis
    tier: cache
spec:
  selector:
    matchLabels:
      tier: cache
  template:
    metadata:
      labels:
        tier: cachy
    spec:
      containers:
      - name: redis
        image: redis
```
**Answer**
The selector doesn't match the label (cache vs cachy). To solve it, fix cachy so it's cache instead.

## Deployments

### 1. How to list all the deployments in the current namespace?

`k get deploy`

### 2. How to check which image a certain Deployment is using?

`k describe deploy <DEPLOYMENT_NAME> | grep image`

### 3. Create a file definition/manifest of a deployment called "dep", with 3 replicas that uses the image 'redis'.

`k create deploy dep -o yaml --image=redis --dry-run=client --replicas 3 > deployment.yaml `

### 4. Remove the deployment `depdep`.

`k delete deploy depdep`

### 5. Create a deployment called "pluck" using the image "redis" and make sure it runs 5 replicas.

`kubectl create deployment pluck --image=redis --replicas=5`

### 6. Create a deployment with the following properties:

* called "blufer"
* using the image "python"
* runs 3 replicas
* all pods will be placed on a node that has the label "blufer"

**Answer**
`kubectl create deployment blufer --image=python --replicas=3 -o yaml --dry-run=client > deployment.yaml`

Add the following section (`vi deployment.yaml`):

```
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedlingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: blufer
            operator: Exists
```

`kubectl apply -f deployment.yaml`

## Troubleshooting Deployments

### 1. Fix the following deployment manifest.

```yaml
apiVersion: apps/v1
kind: Deploy
metadata:
  creationTimestamp: null
  labels:
    app: dep
  name: dep
spec:
  replicas: 3
  selector:
    matchLabels:
      app: dep
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: dep
    spec:
      containers:
      - image: redis
        name: redis
        resources: {}
status: {}
```

**Answer**

Change `kind: Deploy` to `kind: Deployment`

### 2. Fix the following deployment manifest.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: dep
  name: dep
spec:
  replicas: 3
  selector:
    matchLabels:
      app: depdep
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: dep
    spec:
      containers:
      - image: redis
        name: redis
        resources: {}
status: {}
```
**Answer**

The selector doesn't match the label (dep vs depdep). To solve it, fix depdep so it's dep instead.

## Scheduler

### 1. How to schedule a pod on a node called "node1"?

`k run some-pod --image=redix -o yaml --dry-run=client > pod.yaml`

`vi pod.yaml` and add:

```
spec:
  nodeName: node1
```

`k apply -f pod.yaml`

Note: if you don't have a node1 in your cluster the Pod will be stuck on "Pending" state.
</b></details>

## Node Affinity

### 1. Using node affinity, set a Pod to schedule on a node where the key is "region" and value is either "asia" or "emea".

`vi pod.yaml`

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedlingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: region
          operator: In
          values:
          - asia
          - emea
```

### 2. Using node affinity, set a Pod to never schedule on a node where the key is "region" and value is "neverland".

`vi pod.yaml`

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedlingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: region
          operator: NotIn
          values:
          - neverland
```

## Labels and Selectors

### 1. How to list all the Pods with the label "app=web"?

`k get po -l app=web`

### 2. How to list all objects labeled as "env=staging"?

`k get all -l env=staging`

### 2. How to list all deployments from "env=prod" and "type=web"?

`k get deploy -l env=prod,type=web`

## Node Selector

### 1. Apply the label "hw=max" on one of the nodes in your cluster.

`kubectl label nodes some-node hw=max`

### 2. Create and run a Pod called `some-pod` with the image `redis` and configure it to use the selector `hw=max`.

```
kubectl run some-pod --image=redis --dry-run=client -o yaml > pod.yaml

vi pod.yaml

spec:
  nodeSelector:
    hw: max

kubectl apply -f pod.yaml
```

### 3. Explain why node selectors might be limited.

Assume you would like to run your Pod on all the nodes with with either `hw` set to max or to min, instead of just max. This is not possible with nodeSelectors which are quite simplified and this is where you might want to consider `node affinity`.

## Taints

### 1. Check if there are taints on node "master".

`k describe no master | grep -i taints`

### 2. Create a taint on one of the nodes in your cluster with key of "app" and value of "web" and effect of "NoSchedule". Verify it was applied.

`k taint node minikube app=web:NoSchedule`

`k describe no minikube | grep -i taints`

### 3. You applied a taint with <code>k taint node minikube app=web:NoSchedule</code> on the only node in your cluster and then executed <code>kubectl run some-pod --image=redis</code>. What will happen?

The Pod will remain in "Pending" status due to the only node in the cluster having a taint of "app=web".

### 4. You applied a taint with <code>k taint node minikube app=web:NoSchedule</code> on the only node in your cluster and then executed <code>kubectl run some-pod --image=redis</code> but the Pod is in pending state. How to fix it?

`kubectl edit po some-pod` and add the following

```
  - effect: NoSchedule
    key: app
    operator: Equal
    value: web
```

Exit and save. The pod should be in Running state now.

### 5. Remove an existing taint from one of the nodes in your cluster.

`k taint node minikube app=web:NoSchedule-`

## Resources Limits

### 1. Check if there are any limits on one of the pods in your cluster.

`kubectl describe po <POD_NAME> | grep -i limits`

### 2. Run a pod called "yay" with the image "python" and resources request of 64Mi memory and 250m CPU.

`kubectl run yay --image=python --dry-run=client -o yaml > pod.yaml`

`vi pod.yaml`

```
spec:
  containers:
  - image: python
    imagePullPolicy: Always
    name: yay
    resources:
      requests:
        cpu: 250m
        memory: 64Mi
```

`kubectl apply -f pod.yaml`

### 3. Run a pod called "yay2" with the image "python". Make sure it has resources request of 64Mi memory and 250m CPU and the limits are 128Mi memory and 500m CPU.

`kubectl run yay2 --image=python --dry-run=client -o yaml > pod.yaml`

`vi pod.yaml`

```
spec:
  containers:
  - image: python
    imagePullPolicy: Always
    name: yay2
    resources:
      limits:
        cpu: 500m
        memory: 128Mi
      requests:
        cpu: 250m
        memory: 64Mi
```

`kubectl apply -f pod.yaml`

## Monitoring

### 1. Deploy metrics-server.

`kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml`

### 2. Using metrics-server, view the following:

* top performing nodes in the cluster
* top performing Pods

* top nodes: `kubectl top nodes`
* top pods: `kubectl top pods`


## Scheduler

### 1. Can you deploy multiple schedulers?

Yes, it is possible. You can run another pod with a command similar to:

```
spec:
  containers:
  - command:
    - kube-scheduler
    - --address=127.0.0.1
    - --leader-elect=true
    - --scheduler-name=some-custom-scheduler
...
```

### 2. Assuming you have multiple schedulers, how to know which scheduler was used for a given Pod?

Running `kubectl get events` you can see which scheduler was used.

### 3. You want to run a new Pod and you would like it to be scheduled by a custom scheduler. How to achieve it?

Add the following to the spec of the Pod:

```
spec:
  schedulerName: some-custom-scheduler
```
