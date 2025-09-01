# Kubernetes-Practice- Examples
This repository has beginner friendly kubernetes basic components examples for practice
## 1. Pod – 
Smallest Deployable unit in cluster
### a. Single container pod
** Example: ** - jsmounika/kubernetes-practice/1-pods/pod-with-single-container.yaml 
```
apiVersion: v1
kind: Pod
metadata:
  name: simple-nginx-pod
  namespace: simple-k8s-app
  labels:
    app: nginx
    tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    imagePullPolicy: IfNotPresent
    ports:
    - containerPort: 80
```
### b. Multi-container pod
** Example: **
## 2. Deployments
** Example: **
## 3. ReplicaSets
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