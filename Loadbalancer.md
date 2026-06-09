# 🌐 Kubernetes Service – LoadBalancer (Deep Notes)

---

## 🎯 What is LoadBalancer Service?

LoadBalancer is a Service type used to expose applications to the internet using a **public IP address**.

It is mainly used in cloud environments like AWS, Azure, and GCP for production workloads.

---

## 🧠 Core Concept

Instead of accessing application using Node IP and port:

    ❌ User → NodeIP:Port

LoadBalancer provides:

    ✅ User → Public IP → Service → Pods

It gives a clean and production-ready way to access applications.

---

## ⚙️ How It Actually Works (Important)

When you create a LoadBalancer Service:

- Kubernetes talks to cloud provider  
- Cloud creates an external load balancer  
- A public IP is assigned  
- All incoming traffic is routed through this load balancer  

---

## 🔥 Internal Flow (Deep Understanding)

LoadBalancer internally uses multiple layers:

    User
      ↓
Cloud Load Balancer (Public IP)
      ↓
Node (NodePort internally)
      ↓
ClusterIP Service
      ↓
Pods

👉 Even though you see only LoadBalancer, internally it also uses NodePort and ClusterIP.

---

## 📊 Flow Diagram

    🌍 User
       ↓
 Public IP (Cloud Load Balancer)
       ↓
   Node (Port Routing)
       ↓
     🌐 Service
       ↓
  -----------------------
  |        |           |
 📦 Pod1  Pod2      Pod3
       ↓
    🐳 Application

---

## 📌 Real Example

Assume:

    External IP = 18.100.25.10

User accesses:

    http://18.100.25.10

---

## 🔁 Traffic Distribution

Load balancing happens at two levels:

1. Cloud Load Balancer → distributes across nodes  
2. Kubernetes Service → distributes across Pods  

Example:

    Request1 → Node1 → Pod1  
    Request2 → Node2 → Pod2  
    Request3 → Node1 → Pod3  

---

## 📄 YAML Example

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-lb

spec:
  type: LoadBalancer

  selector:
    app: nginx

  ports:
  - port: 80
    targetPort: 80
