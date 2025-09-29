```markdown
# 🚀 kubectl Cheat-Sheet in 60s

## 🔥 One-Liners
```bash
alias k=kubectl
k get pods -A                          # every pod everywhere
k get pods -o wide                     # pod + node + ip
k get pods -l app=nginx                # filter by label
k logs -f deploy/nginx                 # tail deployment logs
k exec -it pod-name -- bash            # jump inside
k port-forward pod-name 8080:80        # localhost:8080 → pod:80
k explain pod.spec.containers.image    # docs in your terminal
```

## 🏃‍♂️ Fast Navigation
```bash
kubectx docker-desktop && kubens dev   # context + ns in 1 go
k config get-contexts                  # see all clusters
k config current-context               # where am i?
```

## 🆘 Debug Like a Pro
```bash
k debug -it pod-name --image=busybox   # instant debug sidecar
k top pods                             # cpu/mem live
k get events --sort-by='.lastTimestamp' # recent issues
```

Copy → paste → profit.  
Full deck: `kubectl --help`
```
