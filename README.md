
### Few exec CMD

```bash
 kubectl exec -it shared-volume-deployment-8677cdd75d-9mfvt -c reader -- sh
 k logs -f shared-volume-deployment-8677cdd75d-9mfvt -c reader
 
 ```

 # [Class Notes](https://projects.100xdevs.com/tracks/kubernetes-part-2/k8s-3-6)
 
# 🚀 Kubernetes Storage Deep Dive — PV, PVC, NFS, Block Storage & MongoDB  
![Kubernetes](https://img.shields.io/badge/Kubernetes-Storage-blue?logo=kubernetes&style=for-the-badge)
![Status](https://img.shields.io/badge/Repo-Active-brightgreen?style=for-the-badge)
![Learning](https://img.shields.io/badge/Learning-Phenomenal-orange?style=for-the-badge)

This repo teaches Kubernetes storage using the **Phenomenix method** — a learning framework to master anything complex:

> **Phenomenix** → *Understand the Components → Connect the Flow → Visualize → Apply → Debug → Master*

This README is designed so you **understand, remember, and actually implement** Kubernetes storage.

---

# 🧠 1. Big Picture (Phenomenix: Visualize First)

## 📘 Kubernetes Storage Concept Diagram
```
                ┌──────────────────────────────┐
                │         Application Pod       │
                │     (MongoDB / Any App)       │
                └──────────────┬───────────────┘
                               │ uses
                               ▼
                ┌──────────────────────────────┐
                │         PVC (nfs-pvc)         │
                └──────────────┬───────────────┘
                   binds to     │
                               ▼
                ┌──────────────────────────────┐
                │      PV (nfs-volume)          │
                └──────────────┬───────────────┘
                     points to  │
                               ▼
                ┌──────────────────────────────┐
                │   STORAGE BACKEND             │
                │   • NFS Droplet               │
                │   • DO Block Storage          │
                │   • emptyDir (ephemeral)      │
                └──────────────────────────────┘
```

---

# 📁 2. Repository Overview (Phenomenix: Understand Components)

```
.
├── nfs-class/                # Manual NFS server setup files
├── pv.yaml                   # Static PV
├── pvc.yaml                  # Static PVC
├── do-storageclass.yaml      # Dynamic PV creation using DO Block Storage
├── empty-volume.yaml         # EmptyDir example
├── manifest-pod.yml          # MongoDB Pod with NFS PVC
└── README.md
```

---

# ⚙️ 3. Commands (Phenomenix: Apply Immediately)

## 🔹 Apply all YAMLs
```sh
kubectl apply -f .
```

---

## 🔹 Create NFS-backed PV + PVC
```sh
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
kubectl get pv,pvc
```

---

## 🔹 Apply DigitalOcean Block Storage Class
```sh
kubectl apply -f do-storageclass.yaml
```

Check storage classes:
```sh
kubectl get storageclass
```

---

## 🔹 Create EmptyDir Pod
```sh
kubectl apply -f empty-volume.yaml
```

---

## 🔹 Create MongoDB Pod Using NFS
```sh
kubectl apply -f manifest-pod.yml
```

Describe Pod:
```sh
kubectl describe pod mongodb
```

Enter Pod:
```sh
kubectl exec -it mongodb -- sh
```

Check MongoDB data:
```sh
ls /data/db
```

---

# 🔥 4. Massive Clarity Section (Phenomenix: Connect the Flow)

### 🎯 **Goal:** Your Pod should store data **outside itself** so restarting the Pod does not delete data.

There are 3 ways to achieve this:

---

## 1️⃣ NFS (Manual) — *You control everything*  
- You create an NFS server in a droplet  
- PV connects to your server  
- PVC binds automatically  
- Pod uses PVC  

**Best for:** large shared storage, multiple Pods accessing same folder.

---

## 2️⃣ DO Block Storage (Dynamic) — *Kubernetes auto-creates PV*  
PVC → StorageClass → DigitalOcean → PV → Pod  

**Best for:** production-grade apps needing auto-scaling storage.

---

## 3️⃣ EmptyDir — *Temporary storage*  
- Deleted when Pod is deleted  
- Good for caching, temp files  

---

# 📦 5. MongoDB Storage Explained (Phenomenix: Master by Real Example)

### Inside `manifest-pod.yml`:
- **mountPath:** `/data/db`
- **volumeName:** `nfs-volume`
- **PVC:** `nfs-pvc`

Meaning → MongoDB writes data into NFS drive mounted at `/data/db`.

Even if the Pod dies:
✔️ Data stays  
✔️ MongoDB boots with old data  
✔️ Persistent real-world behavior  

---

# 🧪 6. Debug Like a Pro (Phenomenix: Debug → Master)

### Check volume mount inside the Pod
```sh
df -h
```

### Check PV/PVC binding issues
```sh
kubectl describe pvc nfs-pvc
```

### Look for NFS connectivity problems
```sh
kubectl describe pv nfs-volume
```

### Logs if MongoDB fails to start
```sh
kubectl logs mongodb
```

---

# 🏁 7. Final Memory Hook (Phenomenix: Retain Forever)

👉 **Pod → PVC → PV → Storage Backend**  
👉 **Pods NEVER talk directly to storage. PVC is the middleman.**  
👉 **PV is the “hard disk,” PVC is the “cable,” Pod is the “computer.”**  
👉 **Dynamic provisioning removes manual PV creation.**  
👉 **NFS is for shared storage; Block Storage is for dedicated disks.**

---

# 📚 References
- Kubernetes Volumes Docs  
- DigitalOcean Block Storage  
- NFS Setup Docs  

---

✨ **Learn once. Never forget. Build real infra.**  
🚀 Happy Kubernetes-ing!
# 🚀 Kubernetes Storage Deep Dive — PV, PVC, NFS, DO Block Storage, EmptyDir & MongoDB  
![Kubernetes](https://img.shields.io/badge/Kubernetes-Storage-blue?logo=kubernetes&style=for-the-badge)
![Status](https://img.shields.io/badge/Repo-Updated-brightgreen?style=for-the-badge)
![Learning](https://img.shields.io/badge/Method-Phenomenix-orange?style=for-the-badge)

This repository demonstrates **EVERY major Kubernetes volume type** using a clean, practical file structure.

Learning style: **Phenomenix**  
> *Understand → Connect → Visualize → Apply → Debug → Master*

---

# 🧠 1. Visual Architecture

```
                ┌──────────────────────────────┐
                │         mongo-pod            │
                │   (Writes to /data/db)       │
                └──────────────┬───────────────┘
                               │ PVC → nfs-pvc
                               ▼
                ┌──────────────────────────────┐
                │            nfs-pv             │
                └──────────────┬───────────────┘
                       NFS      │
                               ▼
                ┌──────────────────────────────┐
                │  NFS Server in Droplet        │
                │ docker-compose.yml → /exports │
                └──────────────────────────────┘
```

Also included:

✔ Dynamic PV using DigitalOcean Block Storage  
✔ EmptyDir shared volume example  
✔ Manual NFS setup via docker-compose  

---

# 📁 2. Repository Structure (Actual)

```
.
├── nfs-class/
│   ├── data/
│   └── docker-compose.yml       # Creates NFS server on DO droplet
│
├── image.png                    # Architecture diagram
│
├── pv.yml                       # NFS PersistentVolume
├── pvc.yml                      # NFS PersistentVolumeClaim
├── pvc-do.yml                   # DO Block Storage PVC (dynamic)
│
├── manifest-pod.yml             # MongoDB pod using NFS
├── manifest-emptyVolume.yml     # emptyDir example with writer/reader
│
└── README.md
```

---

# 📦 3. File-by-File Explanation

---

## **1️⃣ pvc.yml (NFS PVC)**

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Gi
  storageClassName: nfs
```

✔ Binds to `nfs-pv`  
✔ RWMany so multiple pods can read/write  

---

## **2️⃣ pvc-do.yml (DigitalOcean Block Storage PVC)**

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: csi-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 40Gi
  storageClassName: do-block-storage
```

✔ Automatically creates PV  
✔ Uses DigitalOcean CSI driver  

---

## **3️⃣ pv.yml (NFS PV)**

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  storageClassName: nfs
  nfs:
    path: /exports
    server: 64.227.188.228
```

✔ Points to DO droplet  
✔ Path maps to `/exports` inside NFS container  

---

## **4️⃣ manifest-emptyVolume.yml (Shared emptyDir volume)**

Writer writes → Reader reads from the **same ephemeral directory**.

✔ Exists until Pod dies  
✔ Ideal for caching/temp storage  

---

## **5️⃣ manifest-pod.yml (MongoDB Pod using NFS)**

```
volumeMounts:
- mountPath: "/data/db"
  name: nfs-volume

volumes:
- name: nfs-volume
  persistentVolumeClaim:
    claimName: nfs-pvc
```

✔ MongoDB stores data persistently in NFS  
✔ Pod restarts → Data stays  

---

## **6️⃣ docker-compose.yml (Create NFS Server in Droplet)**

Creates an NFS server exporting `/exports`.

```
services:
  nfs-server:
    image: itsthenetwork/nfs-server-alpine
    privileged: true
    environment:
      SHARED_DIRECTORY: /exports
    volumes:
      - ./data:/exports
    ports:
      - "2049:2049"
```

Run:

```sh
docker-compose up -d
```

---

# ⚙️ 4. Commands (Apply Immediately)

## Create NFS PV + PVC
```sh
kubectl apply -f pv.yml
kubectl apply -f pvc.yml
kubectl get pv,pvc
```

## Create DO Block PVC (Dynamic)
```sh
kubectl apply -f pvc-do.yml
```

## Create EmptyDir Deployment
```sh
kubectl apply -f manifest-emptyVolume.yml
```

## Create NFS-backed MongoDB Pod
```sh
kubectl apply -f manifest-pod.yml
```

Inspect:
```sh
kubectl exec -it mongo-pod -- sh
```

---

# 🧪 5. Debug Like a Pro

Check PV/PVC:
```sh
kubectl describe pv nfs-pv
kubectl describe pvc nfs-pvc
```

Check NFS mount inside pod:
```sh
df -h
```

Check Mongo logs:
```sh
kubectl logs mongo-pod
```

---

# 🏁 6. Final Memory Hooks (Phenomenix)

👉 **Pod → PVC → PV → Storage Backend**  
👉 Pod NEVER talks directly to storage  
👉 NFS = shared storage (RWMany)  
👉 DO Block Storage = dynamic provisioning  
👉 emptyDir = ephemeral  

---

# 🎉 Done — You Now Understand Kubernetes Storage Properly  