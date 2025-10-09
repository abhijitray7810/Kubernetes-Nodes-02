# 🧩 Kubernetes Namespace and Pod Management

This guide demonstrates how to work with **Kubernetes namespaces** and **pods**, including creating, viewing, and deleting them.

---

## 🏷️ 1. View All Namespaces

```bash
kubectl get namespace
# or
kubectl get ns
````

**Output:**

```
NAME                 STATUS   AGE
default              Active   20h
kube-node-lease      Active   20h
kube-public          Active   20h
kube-system          Active   20h
local-path-storage   Active   20h
```

---

## 📦 2. View All Pods in Default Namespace

```bash
kubectl get pods
```

**Output:**

```
No resources found in default namespace.
```

---

## ⚙️ 3. View Pods in kube-system Namespace

```bash
kubectl get pods -n kube-system
```

**Sample Output:**

```
NAME                                              READY   STATUS        RESTARTS      AGE
coredns-674b8bbfcf-r2n2q                          1/1     Running       0             5m45s
etcd-abhi-kube-control-plane                      1/1     Running       0             20h
kindnet-6x5zd                                     1/1     Running       1 (12m ago)   20h
kube-apiserver-abhi-kube-control-plane            1/1     Running       0             20h
...
```

---

## 🌐 4. Create a New Namespace

```bash
kubectl create ns nginx
# or
kubectl create namespace nginx
```

**Output:**

```
namespace/nginx created
```

---

## 🧾 5. Verify the Namespace

```bash
kubectl get ns
```

**Output:**

```
NAME                 STATUS   AGE
default              Active   20h
kube-node-lease      Active   20h
kube-public          Active   20h
kube-system          Active   20h
local-path-storage   Active   20h
nginx                Active   47s
```

---

## 🚀 6. Run an NGINX Pod in Default Namespace

```bash
kubectl run nginx --image=nginx
```

**Output:**

```
pod/nginx created
```

---

## 🔍 7. Verify the Pod

```bash
kubectl get pods
```

**Output:**

```
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          14m
```

---

## ❌ 8. Delete the Pod from Default Namespace

```bash
kubectl delete pod nginx
```

**Output:**

```
pod "nginx" deleted
```

---

## 🌍 9. Run an NGINX Pod in the NGINX Namespace

```bash
kubectl run nginx --image=nginx -n nginx
```

**Output:**

```
pod/nginx created
```

---

## ✅ 10. Verify Pod in NGINX Namespace

```bash
kubectl get pods -n nginx
```

**Output:**

```
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          119s
```

---

## 🗑️ 11. Delete Pod from NGINX Namespace

```bash
kubectl delete pod nginx -n nginx
```

**Output:**

```
pod "nginx" deleted
```

---

## 🧹 12. Delete the Namespace

```bash
kubectl delete ns nginx
```

**Output:**

```
namespace "nginx" deleted
```

---

## 🧠 Summary

| Action           | Command                                   | Description                     |
| :--------------- | :---------------------------------------- | :------------------------------ |
| View namespaces  | `kubectl get ns`                          | Lists all namespaces            |
| Create namespace | `kubectl create ns <name>`                | Creates a new namespace         |
| Run pod          | `kubectl run <pod> --image=<image>`       | Creates a pod                   |
| View pods        | `kubectl get pods -n <namespace>`         | Lists pods in a namespace       |
| Delete pod       | `kubectl delete pod <pod> -n <namespace>` | Deletes a pod                   |
| Delete namespace | `kubectl delete ns <name>`                | Removes namespace and resources |

---

📘 **Author:** Abhijit Ray
🕒 **Date:** October 2025
🔗 **Purpose:** Practice Kubernetes Namespace & Pod Management

```

---

Would you like me to format it with emojis and section dividers for a **GitHub README aesthetic** (modern developer-style)?
```
