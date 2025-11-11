# ConfigMap and Secret

## create ConfigMap
cat <<EOF> configmap-kubeapp.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kubeapp-config
data:
  MODE: development
  COLOR: blue
  SERVICE: kubeapp
EOF

kubectl apply -f configmap-kubeapp.yaml
kubectl get cm kubeapp-config
kubectl describe cm kubeapp-config

# create Pod with Configmap
cat <<EOF> pod-kubeapp-with-configmap.yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubeapp-with-configmap
spec:
  containers:
  - name: kubeapp
    image: kubenesia/kubeapp
    envFrom:
    - configMapRef:
        name: kubeapp-config
EOF

kubectl apply -f pod-kubeapp-with-configmap.yaml
kubectl exec -it kubeapp-with-configmap -- sh
printenv 
