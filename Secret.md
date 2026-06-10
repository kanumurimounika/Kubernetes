# Kubernetes Secrets — Complete Guide

## 1. What is a Secret?
A Secret is a Kubernetes object used to store **sensitive data**, such as:

- Passwords  
- API keys  
- Tokens  
- Certificates  
- SSH keys  

Secrets prevent exposing sensitive values in Pod specs or container images.

---

## 2. Secret Types

| Type | Description |
|------|-------------|
| **Opaque** | Generic key‑value secrets |
| **kubernetes.io/tls** | TLS certificate + private key |
| **kubernetes.io/basic-auth** | Username/password pairs |
| **kubernetes.io/dockerconfigjson** | Docker registry credentials |
| **ServiceAccount token** | Auto‑generated for ServiceAccounts |

---

## 3. Opaque Secret Example

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
  namespace: dev
type: Opaque
data:
  username: YWRtaW4=
  password: c2VjdXJlMTIz
```

> Values **must be base64 encoded**.

---

## 4. Creating Secrets (CLI)

### **From literal values**
```
kubectl create secret generic db-secret \
  --from-literal=username=admin \
  --from-literal=password=secure123
```

### **From a file**
```
kubectl create secret generic cert-secret --from-file=cert.pem
```

### **From an env file**
```
kubectl create secret generic app-secret --from-env-file=app.env
```

---

## 5. Using Secrets in Pods

### **A. As Environment Variables**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
  namespace: dev
spec:
  containers:
    - name: app
      image: nginx
      env:
        - name: DB_USER
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: username
        - name: DB_PASS
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
```

---

### **B. As Mounted Files**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-file-pod
  namespace: dev
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - name: secret-vol
          mountPath: /etc/secret
  volumes:
    - name: secret-vol
      secret:
        secretName: db-secret
```

Inside the Pod:
```
/etc/secret/username
/etc/secret/password
```

---

## 6. TLS Secret (Certificate + Private Key)

TLS Secrets store:

- **tls.crt** → certificate  
- **tls.key** → private key  

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: tls-secret
  namespace: dev
type: kubernetes.io/tls
data:
  tls.crt: <base64 certificate>
  tls.key: <base64 private key>
```

### Create TLS Secret (CLI)
```
kubectl create secret tls tls-secret \
  --cert=cert.pem \
  --key=key.pem \
  -n dev
```

---

## 7. How Secret Updates Work

| Method | Auto‑update? |
|--------|--------------|
| **Environment variables** | ❌ No (Pod restart required) |
| **Mounted files** | ✔ Yes (updated live) |

---

## 8. Security Best Practices

- Enable **encryption at rest** for Secrets  
- Restrict access using **RBAC**  
- Avoid storing secrets in **environment variables** for highly sensitive data  
- Prefer **mounted files** for certificates and keys  
- **Never commit Secrets to GitHub**  
- Use external secret managers:  
  - HashiCorp Vault  
  - AWS Secrets Manager  
  - Azure Key Vault  
  - GCP Secret Manager  

---

## 9. Debugging Secrets

### View Secret:
```
kubectl get secret db-secret -n dev -o yaml
```

### Decode a value:
```
echo "c2VjdXJlMTIz" | base64 --decode
```

### Check mounted files:
```
kubectl exec -it <pod> -n dev -- ls /etc/secret
```

---

## 10. Summary

- Secrets store **sensitive data**  
- Stored in **etcd** (base64 encoded; can be encrypted)  
- Used as **env vars** or **mounted files**  
- TLS Secrets store **certificate + key**  
- Mounted files auto‑update  
- Protect Secrets with **RBAC + encryption**  

