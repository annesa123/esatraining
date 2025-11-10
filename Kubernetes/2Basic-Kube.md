# Basic Config
## Deploy sample app
```
kubectl run node-web-app --image=<image>
kubectl get pods
kubectl port-forward pod/node-web-app --address 0.0.0.0 8112:8080
```

