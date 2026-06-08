# 🌐 Kubernetes Service – NodePort

---

## 🎯 What is NodePort Service?

NodePort is a Service type used to expose an application running in Kubernetes to external users.

Since Pods run inside the cluster and cannot be accessed directly, NodePort allows external access by opening a specific port on every node.

---

## 🧠 Core Concept

Instead of accessing Pods directly:

    ❌ User → Pod

You use:

    ✅ User → NodeIP:Port → Service → Pod

NodePort creates a bridge between external users and internal Pods.

---

## ⚙️ How It Works

When a NodePort Service is created:

- Kubernetes selects Pods using labels  
- A port (range: 30000–32767) is opened on every node  
- Requests to that port are routed to the Service  
- Service forwards traffic to available Pods  

---

## 📊 Flow Diagram

    🌍 External User
           ↓
    Node IP : NodePort
           ↓
       🌐 Service
           ↓
      -----------------
      |      |       |
    📦 Pod1 📦 Pod2 📦 Pod3
           ↓
        🐳 Application

---

## 📌 Example

Assume:

- Node IP = 192.168.1.10  
- NodePort = 30007  

Access application:

    http://192.168.1.10:30007

---

## 🔁 Load Balancing

The Service distributes traffic automatically:

    Request 1 → Pod1  
    Request 2 → Pod2  
    Request 3 → Pod3  

---

## 📄 NodePort YAML

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-nodeport

spec:
  type: NodePort

  selector:
    app: nginx

  ports:
  - port: 80
    targetPort: 80
    nodePort: 30007
