Below is a ready-to-paste README.md that turns your hand-written Kubernetes cheat-sheet into a clean, visual study note.  
Save it as `README.md` inside the repo that holds the JPG (or copy the markdown into any existing repo).

```markdown
# 📗 Kubernetes Quick-Cheat: Deployment vs ReplicaSet

> A single-page recap that fits in your pocket (or your brain).  
> Originally scribbled on paper → snapped → now digitised for quick revision.

![cheat-sheet](WhatsApp%20Image%202025-09-30%20at%2000.28.35_73a94f64.jpg)

---

## 🎯 TL;DR – what you must remember

| Object      | Purpose | Built-in Rollout/Rollback | Typical `apiVersion` | Field you *must* set |
|-------------|---------|---------------------------|----------------------|----------------------|
| **Deployment** | Declarative updates & zero-downtime rollouts | ✅ | `apps/v1` | `strategy` (defaults to RollingUpdate) |
| **ReplicaSet** | Guarantees a fixed number of pod replicas | ❌ | `apps/v1` | `replicas` |

---

## ⚡ One-liner memory hook
> “**Deployment** is the *conductor*; **ReplicaSet** is the *section leader*.”  
> You almost **never** create ReplicaSets by hand—Deployments do it for you.

---

## 🛠️ Minimal templates

### 1. Deployment (recommended)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-minimal
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.26.0
```

### 2. ReplicaSet (educational only)
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-minimal
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.26.0
```

---

## 🔍 Common exam gotchas
1. `apiVersion` for both is **apps/v1** (not `v1`).  
2. `selector.matchLabels` must **equal** `template.metadata.labels`.  
3. Only **Deployment** has `strategy`, `revisionHistoryLimit`, `progressDeadlineSeconds`.  
4. Rolling updates **create new ReplicaSets**; old ones are scaled to 0 (not deleted immediately).

---

## 📚 Want deeper dives?
- [Official Docs – Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Official Docs – ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- Katacoda playground: https://www.katacoda.com/courses/kubernetes

---

<p align="center">
  <i>Happy kube-ing! 🚢</i>
</p>
```

Drop the file in your repo, commit, and you’ve got a lightweight revision card that’s always one `git clone` away.
