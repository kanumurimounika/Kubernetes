# Kubernetes RBAC — Complete Notes (Roles, ClusterRoles, Bindings, Examples)

## 1. What is RBAC?
RBAC (Role-Based Access Control) defines:
- WHO → User / Group / ServiceAccount
- CAN DO WHAT → verbs (get, list, create, delete…)
- ON WHICH RESOURCES → pods, deployments, secrets…

RBAC = Kubernetes authorization system.

## 2. RBAC Flow Diagram
User / ServiceAccount  
        ↓  
RoleBinding / ClusterRoleBinding  
        ↓  
Role / ClusterRole  
        ↓  
Allowed Verbs on Resources  

## 3. Role (Namespace Scoped)
A **Role** gives permissions inside ONE namespace.

Example: Allow reading pods in dev namespace.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: dev
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
```

## 4. RoleBinding (Assign Role → User/SA)
RoleBinding connects a Role to a user/group/service account **within a namespace**.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: dev
subjects:
  - kind: ServiceAccount
    name: app-sa
    namespace: dev
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

## 5. ClusterRole (Cluster Scoped)
ClusterRole gives permissions across the **entire cluster**.

Used for:
- Nodes  
- PersistentVolumes  
- Namespaces  
- Cluster-wide resources  
- Reusable permissions across namespaces  

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: node-reader
rules:
  - apiGroups: [""]
    resources: ["nodes"]
    verbs: ["get", "list", "watch"]
```

## 6. ClusterRoleBinding (Assign ClusterRole → User/SA)
ClusterRoleBinding connects a ClusterRole to a subject **cluster-wide**.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: node-read-binding
subjects:
  - kind: User
    name: devops@example.com
roleRef:
  kind: ClusterRole
  name: node-reader
  apiGroup: rbac.authorization.k8s.io
```

## 7. ServiceAccount (Identity for Pods)
Pods use **ServiceAccounts** to authenticate to the API server.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-sa
  namespace: dev
```

## 8. FULL RBAC EXAMPLE (Role + RoleBinding + ServiceAccount)
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-sa
  namespace: dev
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: configmap-editor
  namespace: dev
rules:
  - apiGroups: [""]
    resources: ["configmaps"]
    verbs: ["get", "list", "create", "update"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: configmap-editor-binding
  namespace: dev
subjects:
  - kind: ServiceAccount
    name: app-sa
    namespace: dev
roleRef:
  kind: Role
  name: configmap-editor
  apiGroup: rbac.authorization.k8s.io
```

## 9. RBAC Verbs (What actions can be allowed?)
- get → read one resource  
- list → read all resources  
- watch → watch changes  
- create → create new resources  
- update → modify existing resources  
- patch → partial update  
- delete → delete resources  

Full access:
```
verbs: ["*"]
```

## 10. API Groups (VERY IMPORTANT)
| Resource Type | API Group |
|---------------|-----------|
| Pods, Services, ConfigMaps | "" (core) |
| Deployments, ReplicaSets | apps |
| Ingress | networking.k8s.io |
| Roles, Bindings | rbac.authorization.k8s.io |
| CRDs | apiextensions.k8s.io |

Example:
```yaml
apiGroups: ["apps"]
resources: ["deployments"]
verbs: ["get", "list"]
```

## 11. Debugging RBAC
Check permissions:
```
kubectl auth can-i get pods --as mounika
kubectl auth can-i create deployments --as system:serviceaccount:dev:app-sa
```

List RBAC objects:
```
kubectl get roles -A
kubectl get rolebindings -A
kubectl get clusterroles
kubectl get clusterrolebindings
```

## 12. Summary
- Role → namespace permissions  
- ClusterRole → cluster-wide permissions  
- RoleBinding → assign Role to user/SA in namespace  
- ClusterRoleBinding → assign ClusterRole cluster-wide  
- ServiceAccount → identity used by Pods  
- RBAC controls WHO can do WHAT on WHICH resources  
