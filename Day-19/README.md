# 📋 Kubernetes ConfigMaps – “Hot-Swap Configuration Without Rebuilding Images”  
> Decouple **environment-specific** stuff from **container images** · Mount as **files** OR inject as **env-vars** · Change on-the-fly, zero rebuild

---

## 🔥 10-Second Visual
```mermaid
graph TD
    CM[ConfigMap 📋<br/>outside the image]
    CM -->|envFrom| Pod1[Pod 🏎️<br/>MY_ENV_VAR="MYVALUE"]
    CM -->|volumeMount| Pod2[Pod 🏎️<br/>/etc/config/conf.yml]
    IMG[Image 🐳<br/>unchanged!]
    Pod1 --> IMG
    Pod2 --> IMG
    style CM fill:#4ecdc4,color:#000
```

---

## ⚡ Two-Line Taste Test
```bash
# 1) create the maps (manifests below)
kubectl apply -f cm.yaml

# 2) launch a pod that uses BOTH styles
kubectl apply -f pod-demo.yaml

# 3) peek inside
kubectl exec -it configmap-example -- sh -c 'echo $NAME; cat /etc/config/conf.yml'
```

---

## 📄 cm.yaml – Property-Style vs File-Style
```yaml
# PROPERTY-LIKE (env vars)
apiVersion: v1
kind: ConfigMap
metadata:
  name: property-like-keys
data:
  NAME: YourAppName
  VERSION: "1.0.0"
  AUTHOR: YourName
---
# FILE-LIKE (mountable files)
apiVersion: v1
kind: ConfigMap
data:
  conf.yml: |
    name: YourAppName
    version: 1.0.0
    author: YourName
metadata:
  name: file-like-keys
```

---

## 📄 pod-demo.yaml – consume BOTH
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-example
spec:
  containers:
  - name: nginx
    image: nginx:1.26.0
    envFrom:                          # ← 1) inject ALL keys as env
    - configMapRef:
        name: property-like-keys
    volumeMounts:                     # ← 2) mount as files
    - name: config-vol
      mountPath: /etc/config
      readOnly: true
  volumes:
  - name: config-vol
    configMap:
      name: file-like-keys
```

---

## 🔍 Quick Commands
| Task | One-Liner |
|------|-----------|
| **create from literal** | `kubectl create cm my-cm --from-literal=key=value` |
| **create from file** | `kubectl create cm my-cm --from-file=conf.yml` |
| **see keys/values** | `kubectl get cm my-cm -o yaml` |
| **edit live** | `kubectl edit cm my-cm` *(pod picks up in ~1 min if mounted)* |
| **delete** | `kubectl delete cm my-cm` |

---

## 🏎️ Hot-Reload Behaviour
| Mount Style | Reload Without Pod Restart? | How |
|-------------|-----------------------------|-----|
| **envFrom / valueFrom** | ❌ | Pod must be restarted |
| **volumeMount** | ✅ | kubelet updates files in ~1 min |

---

## 🎯 Real-World Uses
| Use-Case | Mount As | Sample Content |
|----------|----------|----------------|
| **app.conf** | file | server port, debug flags |
| **feature-flags** | env | `NEW_UI=true` |
| **db connection string** | env | `DB_HOST=prod-db` |
| **nginx.conf** | file | upstream servers |
| **spring bootstrap** | file | profile names |

---

## 🆘 Debug in 3 Commands
```bash
# vars not injected?
kubectl exec -it configmap-example -- env | grep NAME

# files missing?
kubectl exec -it configmap-example -- ls -l /etc/config/

# wrong keys?
kubectl describe cm property-like-keys
```

---

Copy → paste → `kubectl apply -f cm.yaml` → configuration lives **outside** the image!  
Full docs: `kubectl explain configmap`
```
