# 🌐 Kubernetes Service – ClusterIP (Internal Service)

---

## 🎯 What is ClusterIP Service?

ClusterIP is the **default type of Kubernetes Service**.

It is used to expose an application **only inside the Kubernetes cluster**.

This means:
- Pods inside the cluster can access it ✅  
- External users cannot access it ❌  

---

## 🧠 Concept Understanding

When Pods are created, their IP addresses keep changing.

So instead of connecting directly to Pods:

    ❌ Pod → Pod (breaks when IP changes)

We use:

    ✅ Pod → Service (ClusterIP) → Pod

The Service gives a **stable internal IP** and forwards traffic to Pods.

---

## ⚙️ How ClusterIP Works

1. Service is created with a ClusterIP  
2. Service selects Pods using labels  
3. Any request to Service IP is forwarded to matching Pods  

---

## 📊 Flow Diagram

    Pod / App
       ↓
 ClusterIP Service
       ↓
   ---------------
   |      |      |
 📦 Pod1  Pod2  Pod3

---

## 🔑 Label Matching Example

Pods:

- Pod1 → app=nginx  
- Pod2 → app=nginx  
- Pod3 → app=apache  

Service selector:

    app=nginx

Result:
- Pod1 ✅  
- Pod2 ✅  
- Pod3 ❌  

Service connects only to matching Pods.

---

## 📄 ClusterIP YAML Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service

spec:
  type: ClusterIP

  selector:
    app: nginx

  ports:
  - port: 80
    targetPort: 80
