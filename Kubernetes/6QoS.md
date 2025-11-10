# resource management
```
cat <<EOF> resouce.yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource
spec:
  containers:
  - name: resource
    image: nginx
    resources:
      requests:
        memory: "128Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "250m"
EOF
kubectl apply -f resource.yml
kubectl get pod
```
