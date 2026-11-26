# Debugging a CrashLoopBackOff pod

Triage in this order:

```bash
kubectl get pod <pod> -o wide
kubectl describe pod <pod>              # Events at the bottom, Last State, Exit Code
kubectl logs <pod> --previous           # logs of the crashed container, not the restarted one
kubectl get events -n <ns> --sort-by=.lastTimestamp
```

Exit codes worth recognising:

| Code | Usually means |
|---|---|
| 1 | Application error on startup |
| 137 | SIGKILL — very often `OOMKilled` (check `Reason` in `describe`) |
| 139 | Segmentation fault |
| 143 | SIGTERM — graceful shutdown requested |

Then check: wrong image tag, missing ConfigMap/Secret, a liveness probe that kills a slow starter, and memory limits that are too low.
