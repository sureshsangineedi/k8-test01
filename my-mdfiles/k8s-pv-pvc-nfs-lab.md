# 🧩 Kubernetes Storage Practical Lab — PV, PVC, and NFS

## Step 1: Create a Normal Pod (Without Storage)

### Create Pod
```bash
kubectl run test-pod --image=nginx --restart=Never
```

### Go Inside the Pod
```bash
kubectl exec -it test-pod -- /bin/bash
```

### Create a File
```bash
echo "Hello from Nginx Pod" > /usr/share/nginx/html/test.txt
cat /usr/share/nginx/html/test.txt
```

### Delete and Recreate Pod
```bash
kubectl delete pod test-pod
kubectl run test-pod --image=nginx --restart=Never
kubectl exec -it test-pod -- cat /usr/share/nginx/html/test.txt
```
❌ The file is gone — Pods are ephemeral!

---

## Step 2: Create PV and PVC

### Create Local Directory on Node
```bash
sudo mkdir -p /mnt/data
sudo chmod 777 /mnt/data
```

### pv.yaml
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

Apply it:
```bash
kubectl apply -f pv.yaml
```

### pvc.yaml
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

Apply it:
```bash
kubectl apply -f pvc.yaml
kubectl get pv,pvc
```

---

## Step 3: Create Pod with PVC Mounted

### pod-with-pvc.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-pvc
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: storage
  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: my-pvc
```

Apply it:
```bash
kubectl apply -f pod-with-pvc.yaml
```

### Add Data
```bash
kubectl exec -it pod-with-pvc -- /bin/bash
echo "Persistent Data" > /usr/share/nginx/html/persist.txt
cat /usr/share/nginx/html/persist.txt
exit
```

### Delete and Recreate Pod
```bash
kubectl delete pod pod-with-pvc
kubectl apply -f pod-with-pvc.yaml
kubectl exec -it pod-with-pvc -- cat /usr/share/nginx/html/persist.txt
```
✅ Data persists!

---

## Step 4: NFS (Network File System) Example

NFS = Network storage accessible from all nodes.

### nfs-pv.yaml
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    path: /srv/nfs
    server: 192.168.1.10
```

Apply it:
```bash
kubectl apply -f nfs-pv.yaml
```

Use same PVC and Pod definition to store data in NFS.

---

## Summary Table

| Type | Storage Location | Data Survives Pod Delete | Node Dependency |
|------|------------------|--------------------------|-----------------|
| No Volume | Inside Pod filesystem | ❌ No | Temporary |
| hostPath PV | Node local folder | ✅ Yes | Stays on that node |
| NFS PV | Network folder | ✅ Yes | Shared across all nodes |

---

✅ **This lab demonstrates:**
- How Pod storage is lost without PV/PVC  
- How PV and PVC preserve data  
- How NFS enables shared storage across nodes
