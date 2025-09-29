# 🚀 Gateway API – “Ingress 2.0 with Super-Powers”  
> Evolution of Ingress · **Native TCP/UDP** (layers 4 & 7) · **Advanced routing** · **Role-separated** Gateway + Routes · *Not an “API-Gateway” product* 🚪

---

## 🔥 20-Second Visual Journey
```mermaid
graph TD
    Ext[Internet User 🔍] -->|L4/L7 traffic| LB[External LoadBalancer]
    LB -->|single IP:port| GWC[Gateway Controller<br/>Kong / NGINX / Istio]
    GWC -->|reads| GW[Gateway Object<br/>listener:80, protocol:HTTP]
    GW -->|parent ref| HTTPRoute[HTTPRoute<br/>path: /, backend: nginx]
    HTTPRoute --> SvcA[Service A<br/>nginx-clusterip]
    SvcA --> PodsA[Pod(s) 🏎️]
    style GW fill:#ffcc02,stroke:#333
    style HTTPRoute fill:#326ce5,color:#fff
```

---

## 🆚 Ingress vs Gateway API
| Feature | Ingress | Gateway API |
|---------|---------|-------------|
| **Layer 4 (TCP/UDP)** | ❌ (needs CRDs) | ✅ first-class |
| **Role separation** | single object | Gateway + Route (RBAC-friendly) |
| **HTTP header match** | basic | advanced (regex, multiple) |
| **Backend weights** | annotations only | native `weight:` |
| **Status feedback** | limited | precise `conditions` |
| **Extensibility** | annotations | policy attachment |

---

## ⚡ Minimal Hands-On (Kong Gateway Controller shown)
```bash
# 1) install Kong Gateway + CRDs
kubectl apply -f https://bit.ly/kong-ingress-dbless

# 2) create a Gateway listener
kubectl apply -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: kong
spec:
  gatewayClassName: kong
  listeners:
  - name: http
    protocol: HTTP
    port: 80
EOF

# 3) dummy backend
kubectl create deployment nginx --image=nginx
kubectl expose deploy nginx --port=80 --name=nginx-clusterip

# 4) HTTPRoute: parentRefs + backendRefs
kubectl apply -f gateway-route.yaml   # ← next slide
```

---

## 📄 gateway-route.yaml – HTTP + PathPrefix
```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: echo
spec:
  parentRefs:          # ← attach to Gateway
  - name: kong
    sectionName: http
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: nginx-clusterip
      kind: Service
      port: 80
      # weight: 100     # traffic split ready
```

---

## 🎯 TCP Route (Layer 4) in 10 Lines
```yaml
apiVersion: gateway.networking.k8s.io/v1alpha2
kind: TCPRoute
metadata:
  name: tcp-echo
spec:
  parentRefs:
  - name: kong
    sectionName: tcp
  rules:
  - backendRefs:
    - name: tcp-echo-service
      port: 9000
```

---

## 🔍 Everyday Commands
| Task | Command |
|------|---------|
| **see gateways** | `kubectl get gateway` |
| **see routes** | `kubectl get httproute,tcproute,udproute` |
| **check attached parents** | `kubectl describe httproute echo` |
| **controller logs** | `kubectl logs -n kong deploy/kong-gateway` |

---

## 🏎️ Popular Gateway Controllers
| Controller | GatewayClass | L4 TCP/UDP | Extra |
|------------|--------------|------------|-------|
| **Kong** | `kong` | ✅ | plugins, rate-limit |
| **NGINX** | `nginx` | ✅ | WAF, OIDC |
| **Istio** | `istio` | ✅ | service-mesh integration |
| **Envoy-Gateway** | `envoy-gateway` | ✅ | lightweight, CNCF |

Pick **one** → install → set `gatewayClassName:` in your Gateway object.

---

## 🆘 Debug Map
```bash
# route not admitted?
kubectl get httproute echo -o jsonpath='{.status.conditions[*].reason}'

# no gateway class?
kubectl get gatewayclass

# listener conflict?
kubectl describe gateway kong
```

---

Copy → paste → `kubectl apply -f gateway-route.yaml` → **native L4/L7 routing** with **clean RBAC** roles!  
Specs: `kubectl explain gateway`, `kubectl explain httproute`  
Welcome to the **next-gen** traffic door 🚪
```
