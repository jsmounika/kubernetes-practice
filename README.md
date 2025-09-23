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


## 2. Deployments
**Example:** - [Sample Deployment](02-deployments/deploy.yaml)

## 3. ReplicaSets
**Example:** - [Sample Replicaset](03-replicasets/replica.yaml)

**The difference between Deployments and ReplicaSets**  

## 4. Services

### a. ClusterIP
### b. NodePort
### c. Headless
## 5. Volumes
### a. EmptyDir
emptyDir
Use case: Temporary storage shared between containers in a pod.
Example: A web server and a log processor sharing logs.
Note: Data is deleted when the pod is deleted.

### b. HostPath
 hostPath
Use case: Mounts a file or directory from the host node into the pod.
Example: Accessing logs or configuration files from the host.
Note: Tightly coupled to the node, not portable.

### c. PersistentVolumeClaim
persistentVolumeClaim (PVC)
Use case: Requests storage from a PersistentVolume (PV).
Example: Databases like MySQL or MongoDB needing durable storage.
Note: Decouples storage from pods, supports dynamic provisioning.

### d. NFS
nfs (Network File System)
Use case: Shared storage across multiple pods and nodes.
Example: Shared media files or backups.
Note: Requires an NFS server setup.

### e. CSI
CSI (Container Storage Interface)
Use case: Pluggable storage from cloud providers or third-party vendors.
Example: AWS EBS, Azure Disk, Google Persistent Disk.
Note: Modern and flexible way to integrate external storage.

## 6. ConfigMap
configMap & secret
Use case: Inject configuration data or sensitive information.
Example: API keys, passwords, or app settings.
Note: Mounted as files or environment variables.

## 7. Secrets
## 8. Persistent Volume  
What is a Persistent Volume (PV)?
A Persistent Volume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically created using Storage Classes. It’s like a pre-configured hard drive that your pods can use to store data persistently.

Think of it as a USB drive that you plug into your computer (the pod). Even if the computer restarts, the data on the USB stays.

🧩 How Does It Work?
Here’s a simple step-by-step explanation:

1. Persistent Volume (PV)
Created by the cluster admin or dynamically by Kubernetes.
Represents actual storage (like NFS, AWS EBS, GCE PD, etc.).
Lives independently of any pod.
2. Persistent Volume Claim (PVC)
A request for storage by a user or application.
Specifies how much storage is needed and access mode (e.g., ReadWriteOnce).
Think of it like asking for a USB drive with specific size and features.
3. Binding
Kubernetes matches a PVC to a suitable PV.
Once matched, the PV is bound to the PVC and can’t be used by another claim.
4. Pod Uses the PVC
The pod mounts the PVC like a volume.
Now the pod can read/write data to the persistent storage.
🔁 Lifecycle Summary
[Admin/User] --> Creates PV  
[App/User] --> Creates PVC  
Kubernetes --> Matches PVC to PV (Binding)  
Pod --> Uses PVC as a Volume  

## 9. StorageClass  
**What Is a Storage Class?**  
A StorageClass in Kubernetes defines how storage is provisioned dynamically. Instead of manually creating a Persistent Volume (PV), you can let Kubernetes automatically create one when a PVC is made.  

Think of it like ordering a custom USB drive online:  
- You specify the size and type.
- The system provisions it for you.
**How It Works**
Storage
Class defines the type of storage (e.g., AWS EBS, GCE PD, etc.).  
PVC refers to the StorageClass by name.  
Kubernetes dynamically creates a PV that matches the PVC request.  

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

**NOTE:** If we don't specify Storage Class, then it flow will be-  


## 10. StatefulSet
## 11. DeamonSet
## 12. Ingress
## 13. Taint and Tolerations
## 14. NodeSelector
## 15. NodeAffinity
## 16. Pod Affinity
## 17. Pod Anti-Affinit