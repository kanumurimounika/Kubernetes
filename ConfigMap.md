## 1. What is a ConfigMap?
A ConfigMap stores **non‑secret configuration data** for Pods and Deployments.  
It keeps configuration separate from container images and allows updates without rebuilding.

ConfigMaps are namespaced objects and store data as:
- Key‑value pairs  
- JSON/YAML/text files  
- Entire configuration directories  

---

## 2. Why ConfigMaps Exist (Real Purpose)
- Avoid hardcoding config inside images  
- Share config across multiple Pods  
- Update config without rebuilding images  
- Store JSON/YAML/text files for applications  
- Inject config as environment variables or mounted files  

---

## 3. ConfigMap Structure (Key‑Value + File Data)
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: dev
data:
  APP_MODE: "production"
  APP_PORT: "8080"
  config.json: |
    {
      "timeout": 30,
      "retry": 5
    }
```

---

## 4. Using ConfigMap as Environment Variables
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: env-pod
  namespace: dev
spec:
  containers:
    - name: app
      image: nginx
      envFrom:
        - configMapRef:
            name: app-config
```

Inside container:
```
APP_MODE=production
APP_PORT=8080
```

---

## 5. Using ConfigMap as Mounted Files
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: file-pod
  namespace: dev
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - name: cfg
          mountPath: /etc/config
  volumes:
    - name: cfg
      configMap:
        name: app-config
```

Inside Pod:
```
/etc/config/APP_MODE
/etc/config/APP_PORT
/etc/config/config.json
```

---

## 6. ConfigMap + Deployment (Real Production Example)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
  namespace: dev
spec:
  replicas: 2
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
        - name: app
          image: nginx
          envFrom:
            - configMapRef:
                name: app-config
          volumeMounts:
            - name: cfg
              mountPath: /etc/config
      volumes:
        - name: cfg
          configMap:
            name: app-config
```

---

## 7. How ConfigMap Updates Work
### Environment variables:
- ❌ Do NOT auto-update  
- ✔ Require Pod restart  

### Mounted files:
- ✔ Auto-update inside Pod (kubelet refreshes them)  
- ❌ Application must reload them  

---

## 8. Create ConfigMaps (CLI)
From literal values:
```
kubectl create configmap app-config --from-literal=mode=prod --from-literal=debug=false
```

From a file:
```
kubectl create configmap app-config --from-file=config.json
```

From a directory:
```
kubectl create configmap app-config --from-file=./config-dir/
```

---

## 9. Debugging ConfigMaps
View ConfigMap:
```
kubectl get configmap app-config -n dev -o yaml
```

Check mounted files:
```
kubectl exec -it <pod> -n dev -- ls /etc/config
```

Check environment variables:
```
kubectl exec -it <pod> -n dev -- env
```

---

## 10. Summary
- ConfigMap stores **non-secret configuration**  
- Used as **environment variables** or **mounted files**  
- Mounted files auto-update; env vars do not  
- Perfect for JSON/YAML/text configs  
- Essential for 12‑factor applications  
