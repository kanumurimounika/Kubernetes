# ⚙️ Kubernetes Deployments & ReplicaSets

---

## 🎯 What is Deployment?

> ✅ A Deployment is a Kubernetes object used to **manage Pods automatically**

It ensures:
- Required number of Pods are running  
- Failed Pods are recreated  
- Applications are updated without downtime  

---

## 🧠 Core Flow

    👨‍💻 User
       ↓
    ⚙️ Deployment
       ↓
    📊 ReplicaSet
       ↓
    📦 Pods
       ↓
    🐳 Containers

---

## ❗ Why Deployment?

Without Deployment:
- ❌ No auto-healing  
- ❌ No scaling  
- ❌ No updates  

✅ Deployment provides:
- 🔄 Auto-healing  
- 📈 Scaling  
- 🔁 Rolling updates  

---

## 🧩 Components Flow

    ⚙️ Deployment
          ↓
    📊 ReplicaSet
          ↓
    📦 Pods
          ↓
    🐳 Containers

---

<details>
<summary>📖 Pod vs ReplicaSet vs Deployment (Click)</summary>

### 📦 Pod
- Smallest unit  
- Runs container  

---

### 📊 ReplicaSet
- Maintains number of Pods  
- Ensures replicas  

---

### ⚙️ Deployment
- Manages ReplicaSet  
- Handles updates & scaling  

</details>

---

## 🧩 ReplicaSet Concept

> ✅ Ensures required number of Pods are always running  

Example:

    replicas = 2

👉 Always keeps 2 Pods running  

---

## 🔁 Auto-Healing

    Pod fails ❌
       ↓
    ReplicaSet detects
       ↓
    New Pod created ✅

---

## 📈 Scaling

Increase Pods:

    replicas: 2 → 4

👉 2 new Pods created automatically  

---

## 🔄 Rolling Updates

> ✅ Update application without downtime  

Example:

    v1 → v2

Flow:

    Old Pods ↓
    New Pods ↑ gradually

---

## ⚙️ Working Flow

    Step 1 → Deployment created
    Step 2 → ReplicaSet created
    Step 3 → Pods created
    Step 4 → Containers running

---

## 📄 Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment

spec:
  replicas: 2

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx-container
        image: nginx
        ports:
        - containerPort: 80
