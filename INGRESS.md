# 🚀 Kubernetes Ingress — Complete Notes (ingress.md)

## 1. Ingress Flow Diagram
```
Client
  ↓
External LoadBalancer (Public IP)
  ↓
Ingress Controller (NGINX / Traefik / HAProxy)
  ↓
Ingress Resource (Routing Rules)
  ↓
Service (ClusterIP)
  ↓
Pods (Application Containers)
```

## 2. What Is an Ingress?
Ingress is a Kubernetes API object that manages external access to internal services using HTTP/HTTPS.

### Key Features
- One external IP for many services
- Host-based routing
- Path-based routing
- Centralized traffic control
- Optional TLS termination
- Cleaner microservice architecture

Ingress = Routing rules  
Ingress Controller = The router that enforces rules

## 3. Why Ingress Is Needed
### Without Ingress
- Each service needs its own LoadBalancer or NodePort
- Higher cloud cost
- No centralized routing
- Harder TLS management

### With Ingress
- One LoadBalancer → many services
- Central routing rules
- Easy TLS
- Better architecture

## 4. Host-Based Routing (Domain Routing)
```
web.example.com → web-service
api.example.com → api-service
```

## 5. Path-Based Routing (URL Routing)
```
example.com/api → api-service
example.com/    → frontend-service
```

## 6. Full Ingress YAML Example (Host + Path Routing)
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: complete-ingress
  namespace: default
spec:
  ingressClassName: nginx
  rules:
    - host: app.example.com
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: api-service
                port:
                  number: 8080

          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend-service
                port:
                  number: 80
```

## 7. Debugging Ingress
Commands:
```
kubectl get ingress
kubectl describe ingress <name>
kubectl get svc
kubectl get endpoints
kubectl logs -n ingress-nginx deploy/ingress-nginx-controller
```

DNS:
Ensure domain points to the Ingress Controller's external IP.

## 8. Summary
- Ingress = routing rules for external traffic
- Ingress Controller = router that enforces rules
- Host-based routing = different domains → different services
- Path-based routing = different URL paths → different services
- One LoadBalancer can serve many services

- Ingress is essential for production-grade Kubernetes networking
