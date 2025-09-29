# 🏁 Kubernetes Jobs – “Run-to-Completion Batch Racers”  
> Adds **completions tracking** · **Runs once, finishes, done** · **Never restarts on success**

---

## ⚡ 15-Second Demo
```bash
kubectl apply -f job.yaml
kubectl logs -l job-name=echo-date-minimal -f
# → current timestamp, pod exits, job recorded as complete ✅
```

---

## 📄 job.yaml – copy & paste
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: echo-date-minimal
spec:
  backoffLimit: 1          # retry once on failure
  template:
    spec:
      restartPolicy: Never # mandatory for Jobs
      containers:
      - name: echo
        image: busybox:1.36.1
        command: ["date"]
```

---

## 🔍 Anatomy in 1 Table
| Field | Meaning | Batch Racing Analogy |
|-------|---------|----------------------|
| `spec.template.spec.restartPolicy` | **Must be `Never` or `OnFailure`** | Car crosses finish line → race over |
| `spec.backoffLimit` | Max retries per pod | Pit-crew fixes allowed |
| `spec.completions` | How many pods must succeed (default 1) | Number of laps to log |
| `spec.parallelism` | Pods running in parallel | Cars on track at once |

---

## 🎮 Quick Experiments
```bash
# 1) watch the race live
kubectl get pods -l job-name=echo-date-minimal --watch

# 2) inspect final result
kubectl describe job echo-date-minimal

# 3) see completion counter
kubectl get job echo-date-minimal -o jsonpath='{.status.succeeded}'
```

---

## 🏎️ When to Use Jobs vs Deployments
| Workload | Kubernetes Object | Restart on Success? |
|----------|-------------------|---------------------|
| Web server | Deployment | ✅ forever |
| DB migration | Job | ❌ never |
| Nightly report | CronJob | ❌ never |
| CI build step | Job | ❌ never |

---

## 🆘 Fail-Fast Checklist
```bash
# job stuck? → backoffLimit reached
kubectl get job echo-date-minimal -o jsonpath='{.status.failed}'

# wrong restartPolicy? → job rejected
kubectl explain job.spec.template.spec.restartPolicy

# image crash-looping? → logs
kubectl logs -l job-name=echo-date-minimal --tail=50
```

---

Done! Job ran, logged the date, **completed**, **never restarted**.  
Full spec: `kubectl explain job`
```
