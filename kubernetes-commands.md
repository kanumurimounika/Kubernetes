# ⚙️ Kubernetes Commands – Pods & Deployments

---

## 🚀 Create Resources

### Apply YAML (Pod / Deployment)

    kubectl apply -f file.yaml

---

## 📃 List Resources

### List Pods

    kubectl get pods

---

### List Deployments

    kubectl get deployments

---

### List All Resources

    kubectl get all

---

### Detailed Output

    kubectl get pods -o wide
    kubectl get deployments -o wide

---

## 🔍 Describe (Detailed Info)

### Pod

    kubectl describe pod pod-name

---

### Deployment

    kubectl describe deployment deployment-name

---

## 🪵 Logs

### Pod Logs

    kubectl logs pod-name

---

### Deployment Logs

    kubectl logs deployment-name

---

## 🔁 Watch Live (Real-time)

### Pods

    kubectl get pods -w

---

### Deployments

    kubectl get deployments -w

---

## ❌ Delete Resources

### Using YAML

    kubectl delete -f file.yaml

---

### Delete Pod

    kubectl delete pod pod-name

---

### Delete Deployment

    kubectl delete deployment deployment-name

---

## 🔄 Update Resource

Re-apply YAML after changes:

    kubectl apply -f file.yaml

---

## 📈 Scale Deployment

    kubectl scale deployment deployment-name --replicas=3

---

## 🔄 Rolling Update (Deployment)

Update image:

    kubectl set image deployment/deployment-name container-name=new-image

---

## ⏪ Rollback Deployment

    kubectl rollout undo deployment deployment-name

---

## 📦 Exec into Pod

    kubectl exec -it pod-name -- /bin/bash

---

## ⚡ Quick Workflow

    Apply → kubectl apply -f file.yaml  
    List → kubectl get pods / deployments  
    Watch → kubectl get pods -w  
    Logs → kubectl logs name  
    Scale → kubectl scale deployment  
    Delete → kubectl delete  

---

## 🎯 One-Line Summary

> ✅ kubectl commands are used to create, monitor, scale, and manage Pods and Deployments in Kubernetes
``
