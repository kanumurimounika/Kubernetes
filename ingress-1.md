# Kubernetes Ingress — Host-Based, Path-Based, TLS Termination, SSL Bridging + Full Example


# 1. Host-Based Routing (Different Domains → Different Services)

Host-based routing means routing traffic based on the domain name.  
Example:
web.example.com → web-service  
api.example.com → api-service  

YAML Example:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: host-based-ingress
spec:
  ingressClassName: nginx
  rules:
    - host: web.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: web-service
                port:
                  number: 80
    - host: api.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: api-service
                port:
                  number: 8080
```

# 2. Path-Based Routing (Different URL Paths → Different Services)

Path-based routing means routing traffic based on URL paths.  
Example:
example.com/api → api-service  
example.com/ → frontend-service  

YAML Example:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: path-based-ingress
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

# 3. TLS Secret (Required for HTTPS)

TLS secret stores the certificate and private key.

Command to create TLS secret:

```yaml
kubectl create secret tls app-tls-secret \
  --cert=path/to/tls.crt \
  --key=path/to/tls.key \
  -n default
```

Secret contains:
- tls.crt → certificate  
- tls.key → private key  


# 4. SSL/TLS Termination (HTTPS → HTTP)

Ingress decrypts HTTPS and sends HTTP to backend services.

Flow:
Client (HTTPS)
 → Ingress Controller (decrypt)
 → Backend Service (HTTP)

YAML Snippet:
```yaml
tls:
  - hosts:
      - app.example.com
    secretName: app-tls-secret
```

# 5. SSL Bridging (Re-Encryption: HTTPS → HTTPS)

Ingress decrypts HTTPS, then re-encrypts and sends HTTPS to backend services.

Flow:
Client (HTTPS)
 → Ingress Controller (decrypt)
 → Ingress Controller (re-encrypt)
 → Backend Service (HTTPS)

YAML Annotation:
```yaml
nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
```


# 6. FULL COMBINED PRODUCTION EXAMPLE (Host + Path + TLS + SSL Bridging)
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: full-production-ingress
  namespace: prod
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
    nginx.ingress.kubernetes.io/proxy-body-size: "20m"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "60"
spec:
  ingressClassName: nginx
  tls:
    - hosts:
        - app.example.com
      secretName: app-tls-secret
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
                  number: 443
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend-service
                port:
                  number: 443
```

7. Summary

- Host-based routing → routes by domain  
- Path-based routing → routes by URL path  
- TLS termination → HTTPS to HTTP  
- TLS secret → stores certificate + key  
- SSL bridging → HTTPS to HTTPS  
- Full example shows production-ready Ingress with TLS + routing  
