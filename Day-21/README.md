# 🚪 Kubernetes Ingress – “One Load Balancer, Many Front Doors”  
> Layer-7 (HTTP/HTTPS) traffic router · **Single external LB** → **many internal Services** · Pick your weapon: *nginx, HAProxy, Kong, Istio, Traefik…*

---

## 🔥 20-Second Visual Tour
```mermaid
graph LR
    Ext[Internet User 🔍] -->|https://myapp.com| LB[External LB<br/>cloud-provided]
    LB -->|one IP:port| IC[Ingress Controller<br/>nginx-ingress]
    IC -->|Host + Path| SvcA[Service A<br/>nginx-clusterip]
    IC -->|Host + Path| SvcB[Service B<br/>other-clusterip]
    SvcA --> PodA[Pod(s) 🏎️]
    SvcB --> PodB[Pod(s) 🏎️]
    style IC fill:#326ce5,color:#fff
```

---

## ⚡ TL;DR Cheat-Sheet
| Goal | YAML Snippet |
|------|--------------|
| **Route by host** | `host: myapp.com` |
| **Route by path** | `path: /api` & `pathType: Prefix` |
| **Target service** | `backend.service.name: nginx-clusterip` |
| **TLS (HTTPS)** | `tls.hosts: [myapp.com] + secretName: my-tls` |

---

## 📄 Minimal Ingress (nginx-ingress controller assumed)
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: myapp.com          # Layer-7 host header
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-clusterip   # existing ClusterIP service
            port:
              number: 80
  # tls:                      # uncomment for HTTPS
  # - hosts:
  #   - myapp.com
  #   secretName: my-tls-secret
```

---

## 🚀 Quick Spin-Up
```bash
# 1) install an ingress controller (nginx shown)
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.0/deploy/static/provider/cloud/deploy.yaml

# 2) create dummy ClusterIP services
kubectl create deployment nginx --image=nginx
kubectl expose deploy nginx --port=80 --name=nginx-clusterip

# 3) apply the ingress above
kubectl apply -f ingress.yaml

# 4) wait for external IP
kubectl get svc -n ingress-nginx ingress-nginx-controller
# → paste IP into DNS myapp.com or use local /etc/hosts test
```

---

## 🎯 Popular Ingress Controllers @ a Glance
| Controller | Best For | TCP/UDP L4? | Annotation Power |
|------------|----------|-------------|------------------|
| **ingress-nginx** | simple, lightweight | ❌ (w/ addon) | high |
| **Kong** | API-gateway + plugins | ✅ | very high |
| **Traefik** | auto HTTPS, file-based | ✅ | medium |
| **Istio Gateway** | service-mesh | ✅ | CRD-based |
| **HAProxy** | enterprise, TCP heavy | ✅ | high |

Pick one → install once → route many.

---

## 🔍 Everyday Commands
| Task | Command |
|------|---------|
| **see ingresses** | `kubectl get ingress` |
| **describe rules** | `kubectl describe ing nginx-ingress` |
| **test path** | `curl -H "Host: myapp.com" http://<EXTERNAL-IP>/api` |
| **check controller logs** | `kubectl logs -n ingress-nginx deploy/ingress-nginx-controller` |

---

## 🆘 Debug Map
```bash
# 404 from controller?
kubectl describe ing nginx-ingress | grep -A5 Events

# backend IP empty?
kubectl get endpoints nginx-clusterip

# wrong pathType?
# use Exact, Prefix or ImplementationSpecific
```

---

Copy → paste → `kubectl apply -f ingress.yaml` → **one external IP**, **many internal routes**!  
Full docs: `kubectl explain ingress`  
Pick your controller & happy routing 🚪
```
