# Kubernetes-Practice- Examples
This repository has beginner friendly kubernetes basic components examples for practice
## 1. Pod – 
- Pod is the smallest Deployable unit in k8s.
- Pod contains one or more containers.
- These container will share same network,storage and any other specifications.
- Pod will have unique IP Address in k8s cluster.

**Ports in pod spec-**
1. **port** --> This is the port on the Service itself inside the cluster. the Service listens on port 80. **Eg:** _port: 80_
2. **targetPort** --> This is the port on the Pod/container that the Service routes traffic to. The Service forwards incoming traffic on port to the Pod’s targetPort. **Eg:** _targetPort: 80_
3. **containerPort** --> It tells Kubernetes what port the container (inside the Pod) is listening on. The targetPort in the Service should match this container port. **Eg:** _containerPort: 8080_
							   
**Here’s the traffic flow:**  
		User/Client → Service (port) → Pod (targetPort) → Container (containerPort)  
		Note: most cases --> targetPort == containerPort

### a. Single container pod
**Example:** - [Pod with Single container](01-pods/pod-with-single-container.yaml)
### b. Pod with Environmental variables
**Example:** - [Pod with Environmental variables](01-pods/pod-with-env.yaml)
### c. Multi-container pod
**Example:** - [Pod with Multiple containers](01-pods/pod-with-multi-container.yaml)
### d. Pod with resources
**Example:** - [Pod with Resources](01-pods/pod-with-resources.yaml)  

**Types of QoS (Resources in pod spec):**  
**What is QoS? -**  
	QoS stands for Quality of Service. It's a system Kubernetes uses to classify and prioritize Pods, especially when a node is running low on resources like CPU or memory.  
	When a node is under pressure, the Kubernetes scheduler (kubelet) might need to terminate (evict) Pods to free up resources.  
	The QoS class of a Pod determines its priority in this eviction process.  
	There are three QoS classes - 

	1. Guaranteed (Highest Priority) -
		How it's set: Every container in the Pod must have both a memory and a CPU request and limit, and the values for requests and limits must be identical.
		Behavior: These are the last Pods to be evicted. They are guaranteed the resources they request.
		Eg:
			resources:
			  requests:
				memory: "256Mi"
				cpu: "500m"
			  limits:
				memory: "256Mi"  # Same as requests = Guaranteed QoS
				cpu: "500m"
				
	2. Burstable (Medium Priority) - 
		How it's set: The Pod has at least one container with a CPU or memory request. It doesn't meet the criteria for Guaranteed. Your selected code is a perfect example of this.
		Behavior: The container is guaranteed its request amount of resources but can "burst" and use more, up to its limit, if those resources are available on the node. These Pods are evicted after BestEffort Pods but before Guaranteed Pods.
		Eg:
			resources:
			  requests:
				memory: "128Mi"
				cpu: "250m"
			  limits:
				memory: "512Mi"  # Higher than requests = Burstable QoS
				cpu: "1000m" 
				
	3. BestEffort (Lowest Priority) - 
		How it's set: The containers in the Pod have no memory or CPU requests or limits set at all.
		Behavior: These are the first Pods to be evicted when the node is under resource pressure. They run with whatever resources are leftover on the node.
		Eg: No resources will be mentioned here.
### e. Pod with Readiness
**Example:** - [Pod with Readiness](01-pods/pod-with-readiness.yaml)
### f. Pod with Liveness
**Example:** - [Pod with Liveness](01-pods/pod-with-liveness.yaml)  

**The difference between liveness probe and readiness probe:**

   **Liveness Probe:**
    	- Determines if a container is running properly
        - If it fails, Kubernetes will restart the container
        - Used to detect application deadlocks or crashes
        - In your example, it checks every 10 seconds after a 30-second initial delay

   **Readiness Probe:**
        - Determines if a container is ready to receive traffic
        - If it fails, Kubernetes removes the Pod from Service endpoints (no traffic sent)
        - Used to prevent premature traffic to containers still initializing
        - In your example, it checks every 5 seconds after only a 5-second initial delay

   _Simply put: Liveness probe restarts unhealthy containers, while Readiness probe controls traffic routing to containers._

## 2. ReplicaSets  
Definition:A ReplicaSet ensures that a specified number of pod replicas are running at any given time.  
- Maintains the desired number of pod replicas.  
- Automatically replaces failed pods.  

**Example:** - [Sample Replicaset](02-replicasets/replica.yaml)  
In the example, It will run 3 pods with the nginx image. If one pod crashes, ReplicaSet will create a new one to maintain 3.

## 3. Deployments
A Deployment is a higher-level abstraction that manages ReplicaSets and provides features like rolling updates, rollbacks, and versioning.  
- Manages ReplicaSets.  
- Enables zero-downtime updates.  
- Supports rollback to previous versions.  

**Example:** - [Sample Deployment](03-deployments/deployment.yaml)  
In above example, It is also runs 3 pods, but now you can:  
- Update the image to nginx:1.15 from 1.14 → Kubernetes will create a new ReplicaSet and gradually replace old pods.  
- Roll back if something goes wrong. 

**The difference between Deployments and ReplicaSets**  
| Feature            | ReplicaSet                     | Deployment                            |
|--------------------|--------------------------------|---------------------------------------|
| Manages Pods       | ✅ Yes                         | ✅ Yes (via ReplicaSet)              |
| Rolling Updates    | ❌ No                          | ✅ Yes                               |
| Rollbacks          | ❌ No                          | ✅ Yes                               |
| Versioning         | ❌ No                          | ✅ Yes                               |
| Recommended Usage  | ❌ Not usually directly used   | ✅ Preferred for managing apps       |

## 4. Services
Kubernetes Services expose your application running in Pods to other parts of your cluster or to the outside world.  
There are four main types of services:  
### a. ClusterIP:
🔹 **Use Case:** Internal communication between services within the cluster.  
🔹 **Accessible From:** Only inside the cluster.  
🔹 Most secure (not exposed externally).  
**Example:** - [Sample ClusterIP Service](04-services/service-clusterIP.yaml), [Deployment](04-services/deployment.yaml)  
### b. NodePort:
🔹 **Use Case:** Expose service on a static port on each node.  
🔹 **Accessible From:** Outside the cluster via <NodeIP>:<NodePort>.  
🔹 Simple way to expose services externally. Port range: **30000–32767**  
**Example:** - [Sample NodePort Service](04-services/service-nodePort.yaml), [Deployment](04-services/deployment.yaml)   
### c. LoadBalancer:  
🔹 **Use Case:** Expose service externally using a cloud provider’s load balancer.  
🔹 **Accessible From:** Internet (public IP).  
🔹 Automatically provisions a public IP.  
**Example:** - [Sample LoadBalancer Service](04-services/service-loadbalancer.yaml), [Deployment](04-services/deployment.yaml)
### d. Headless
A Headless Service is a Kubernetes service without a ClusterIP. Instead of load-balancing traffic, it lets you directly access individual pod IPs or DNS records.  
🔹 **Use Case:** Used when you want to discover individual pods (e.g., for StatefulSets).  
🔹 **Accessible From:** Only inside the cluster.  
🔹 This service will not have a virtual IP. Instead, DNS queries to _my-headless-service.default.svc.cluster.local_ will return the IPs of all matching pods.  
🔹 No ClusterIP assigned (clusterIP: None).  
**Example:** -  [Sample Headless Service](04-services/service-headless.yaml), [StatefulSet for Headless service](04-services/statefulset-for-headless-svc.yaml) -->  This headless service allows DNS resolution to each MongoDB pod like: mongo-0.mongo.default.svc.cluster.local, mongo-1.mongo.default.svc.cluster.local
                  
**Summary Table :**
| Service Type   | Exposes To         | Use Case                          | Notes                                 |
|----------------|--------------------|-----------------------------------|---------------------------------------|
| ClusterIP      | Internal only      | Internal app communication        | Default, most secure                  |
| NodePort       | External via Node  | Simple external access            | Limited port range                    |
| LoadBalancer   | External via cloud | Production-grade external access  | Requires cloud provider               |
| Headless       | Internal only      | Pod-level access & discovery      | No ClusterIP, returns pod IPs         |

## 5. Volumes
### a. EmptyDir
**Use case:** Temporary storage shared between containers in a pod.  
**Data Lifetime:** Data is deleted when the pod is deleted.  
**Example:** [EmptyDir Volume](05-volumes/emptyDir.yaml)  

### b. HostPath
**Use case:** Mounts a file or directory from the host node into the pod.  
**Note:** Tightly coupled to the node, not portable.  
**Example:** [HostPath Volume](05-volumes/hostPath.yaml)  

### c. PersistentVolumeClaim 
**Use case:** Requests storage from a PersistentVolume (PV).  
**Note:** Decouples storage from pods, supports dynamic provisioning.  
**Example:** [PV including PVC](08-persistentVolumes) - Databases like MySQL or MongoDB needing durable storage.  

### d. NFS
**Use case:** Shared storage across multiple pods and nodes using an external NFS server.  
**Note:** Requires an NFS server setup.  
**Example:** [NFS Volume](05-volumes/nfs.yaml)  

### e. ConfigMap (In Volumes)
_ConfigMaps and Secrets can be used in two main ways: Volumes and Enviornment variables (Here as volumes)_  
**Use case:** Inject configuration files into pods  
- ConfigMaps store non-sensitive configuration data.  
- Can be mounted as files or used as environment variables.  
**Example:** [Config-volume](05-volumes/config-deployment.yaml)  
This example deployment mounts ConfigMap as **volumes**:  
- ConfigMap is mounted at `/etc/config`.  
- Useful when your app reads config from files.

### f. Secrets (In Volumes_)
**Use case:** Securely inject sensitive data like passwords or tokens.  
- Secrets are base64 encoded and securely managed.  
- Ideal for storing credentials, tokens, and keys.  
**Example:** [Secrets-volume](05-volumes/secret-deployment.yaml)  
This example deployment mounts Secret as **volumes**:  
- Secret is mounted at `/etc/secret`.  
Useful when your app reads secrets from files.

## 6. ConfigMap (As env variables)
**Use case:** ConfigMaps are ideal for non-sensitive configuration like log levels or mode  
**Example:** [ConfigMap](06-configmap/configmap.yaml), [ConfigMap-deployment](06-configmap/configmap-deployment.yaml)  
In this example,  
 - `APP_MODE` is injected from ConfigMap  

## 7. Secrets (As env variables)
**Use case:** Secrets are used for sensitive data like passwords and tokens  
**Example:** [Secrets](07-secrets/secrets.yaml), [Secret-deployment](07-secrets/secret-deployment.yaml)  
In this example,  
- `DB_PASSWORD` is injected from Secret.

### 🔐 ConfigMap vs Secret in Kubernetes

| Feature           | ConfigMap                        | Secret                             |
|------------------|----------------------------------|------------------------------------|
| Purpose           | Store non-sensitive config data  | Store sensitive data (e.g., passwords) |
| Encoding          | Plain text                       | Base64 encoded                     |
| Usage Methods     | Volume mount, Env variables      | Volume mount, Env variables        |
| Typical Use Cases | App settings, flags              | Credentials, tokens, keys          |

### ✅ Usage Options

1. **As Volumes**: Mounted as files inside containers.  
2. **As Environment Variables**: Injected using `valueFrom.configMapKeyRef` or `valueFrom.secretKeyRef`.

## 8. Persistent Volume  
**Example:** [PV Deployment](08-persistentVolumes)  
**What is a Persistent Volume (PV)?**  
A Persistent Volume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically created using Storage Classes. It’s like a pre-configured hard drive that your pods can use to store data persistently.

Think of it as a USB drive that you plug into your computer (the pod). Even if the computer restarts, the data on the USB stays.

**🧩 How Does It Work?**
Here’s a simple step-by-step explanation:  
**1. Persistent Volume (PV)**  
- Created by the cluster admin or dynamically by Kubernetes.  
- Represents actual storage (like NFS, AWS EBS, etc.).  
- Lives independently of any pod.  
**2. Persistent Volume Claim (PVC)**  
- A request for storage by a user or application.  
- Specifies how much storage is needed and access mode (e.g., ReadWriteOnce).  
- Think of it like asking for a USB drive with specific size and features.  
**3. Binding**  
- Kubernetes matches a PVC to a suitable PV.  
- Once matched, the PV is bound to the PVC and can’t be used by another claim.  
**4. Pod Uses the PVC**  
- The pod mounts the PVC like a volume.  
- Now the pod can read/write data to the persistent storage.  

**🔁 Lifecycle Summary**
[Admin/User] --> Creates PV  
[App/User] --> Creates PVC  
Kubernetes --> Matches PVC to PV (Binding)  
Pod --> Uses PVC as a Volume  

## 9. StorageClass  
**Example:** [Storage Class Deployment ](09-storageClass)  
**What Is a Storage Class?**  
A StorageClass in Kubernetes defines how storage is provisioned dynamically. Instead of manually creating a Persistent Volume (PV), you can let Kubernetes automatically create one when a PVC is made.  

Think of it like ordering a custom USB drive online:  
- You specify the size and type.  
- The system provisions it for you.  
**How It Works**  
-> Storage Class defines the type of storage (e.g., AWS EBS, GCE PD, etc.).  
-> PVC refers to the StorageClass by name.  
-> Kubernetes dynamically creates a PV that matches the PVC request.  

✅ **Correct Understanding of StorageClass, PVC, and PV**  
🔹 **StorageClass**  
Defines how storage should be provisioned (e.g., type, filesystem, cloud provider).  
It does not request storage — it provides the instructions for provisioning.  
🔹 **PersistentVolumeClaim (PVC)**  
Requests storage from Kubernetes.  
Refers to a StorageClass to specify how the storage should be provisioned.  
Triggers the creation of a PersistentVolume (PV) if dynamic provisioning is enabled.  
🔹 **PersistentVolume (PV)**  
Is automatically created by Kubernetes based on the StorageClass when a PVC is submitted.  
Represents the actual storage resource that gets bound to the PVC.  

**🔁 Correct Flow**  
[User] --> Creates PVC --> Refers to StorageClass  
[StorageClass] --> Tells Kubernetes how to provision PV  
[Kubernetes] --> Creates PV --> Binds PV to PVC  
[Pod] --> Uses PVC as a volume  

**NOTE:** If we don't specify Storage Class, then the flow will be-  
[Admin] --> Creates PV manually  
[User] --> Creates PVC --> Does NOT specify StorageClass (PVC is bound to a pre-existing PV that matches its request.)  
[Kubernetes] --> Matches PVC to an existing PV (based on size, access mode)  
[Pod] --> Uses PVC as a volume  

## 10. StatefulSet
**Example:** [StatefulSet](10-statefulset/statefulset.yaml)  
> Note: `nginx` is used in StatefulSet examples for simplicity, but real-world StatefulSets are better suited for databases and stateful services.  
- Manages stateful applications with stable network identity and persistent storage(data storage that remains intact even if the pod using it is deleted, restarted, or rescheduled to another node).  
- **Use case:** databases  
### 🆚 Deployment vs StatefulSet  
- **Deployment** is used for stateless applications like web servers.  
- **StatefulSet** is used for stateful applications like databases.  
- StatefulSet ensures each pod has:  
  - A stable name (`pod-0`, `pod-1`)  
  - Its own persistent volume  
  - Stable DNS for inter-pod communication  

## 11. DaemonSet
**Example:** [Daemonset](11-daemonset/deamon-deploy.yaml)  
- Ensures a pod runs on every node.  
- **Use case:** log collectors, monitoring agents.  

## 12. Ingress  
- Manages external access to services via HTTP/HTTPS.  
- **Use case:** routing traffic to services using host/path rules.  

**Example:** [Ingress](12-ingress)

This example setup demonstrates how to expose an internal application using Ingress.  
#### ✅ Components:
1. **Deployment**: Runs the `nginx` web server.
2. **Service**: Exposes the deployment inside the cluster.
3. **Ingress**: Routes external HTTP traffic to the service.

#### 🔧 Prerequisites:
- An **Ingress Controller** must be installed (e.g., NGINX Ingress Controller).  
- DNS or `/etc/hosts` entry should point `nginx.local` to your cluster IP.  

#### 🧪 Testing:

kubectl apply -f nginx-deployment.yaml  
kubectl apply -f service.yaml  
kubectl apply -f ingress.yaml  

Then add below line into your /etc/hosts:  
`<your-cluster-ip> nginx.local`  

## 13. Taint and Tolerations
**Example:** [Toleration](13-taint-tolerations/pod-with-tolerations.yaml)  
- Taint a node before deploying above pod using below command.  
- Taint a node: `kubectl taint nodes <node-name> example-key=example-value:NoSchedule`  
- Check taints: `kubectl describe node <node-name>`  
- Tolerations allow pods to be scheduled on tainted nodes.  

## 14. NodeSelector
**Example:** [Node Selector](14-nodeSelector/nodeselector.yaml)  
- Schedule pods on nodes with specific labels.
- Add label: `kubectl label nodes <node-name> disktype=ssd`

## 15. NodeAffinity
**Example:** [Node Affinity](15-affinities/nodeAffinity.yaml)  
- Advanced scheduling based on node labels.
- Supports required and preferred rules.
- Add label: `kubectl label nodes <node-name> disktype=ssd`

## 16. Pod Affinity
**Example:** [Pod Affinity](15-affinities/podAffinity.yaml)  
- Schedule pods near other pods with matching labels.
- Use case: co-located microservices.

## 17. Pod Anti-Affinity
**Example:** [Pod Anti-Affinity](15-affinities/podAntiAffinity.yaml)   
- Avoid scheduling pods on the same node as others with matching labels.
- Use case: high availability.