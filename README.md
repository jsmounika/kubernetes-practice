# Kubernetes-Practice- Examples
This repository has beginner friendly kubernetes basic components examples for practice
## 1. Pod – 
Smallest Deployable unit in cluster in which container will run.  
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
**Example:** - 
## 4. Services
### a. ClusterIP
### b. NodePort
### c. Headless
## 5. Volumes
### a. EmptyDir
### b. HostPath
### c. ConfigMap
### d. Secret
### e. PersistentVolumeClaim
### f. NFS
### g. CSI
## 6. ConfigMap
## 7. Secrets
## 8. Persistent Volume
## 9. StorageClass
## 10. StatefulSet
## 11. DeamonSet
## 12. Ingress
## 13. Taint and Tolerations
## 14. NodeSelector
## 15. NodeAffinity
## 16. Pod Affinity
## 17. Pod Anti-Affinit