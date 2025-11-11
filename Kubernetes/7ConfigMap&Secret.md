# ConfigMap and Secret

## create ConfigMap
```
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
```

## create Pod with Configmap
```
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
```
## Using ConfigMap as file
```
cat <<EOF> configmap-nginx.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: configmap-nginx
data:
  index.html: |
    <h1> ini web kita belajar </h1>
    <h2> ajldjflaj <h2>
EOF

kubectl apply -f configmap-nginx.yaml
kubectl get cm
kubectl describe cm/configmap-nginx

cat <<EOF> pod-kubeapp-with-nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-web
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - mountPath: /usr/share/nginx/html #mount index.html to /usr/share/nginx/html
      name: nginx-mounts
  volumes:
  - name: nginx-mounts
    configMap:
      name: configmap-nginx
      items:
      - key: index.html
        path: index.html
EOF

kubectl apply -f pod-kubeapp-with-nginx.yaml
kubectl port-forward pod/nginx-web --address 0.0.0.0 1234:80

kubectl exec -it nginx-web -- /bin/bash
cd /usr/share/nginx/html
ls
cat index.html
```
# Secret 
## Secret Creation
```
cat <<EOF> kubeapp.env
DATABASE_HOST=mysqlserver
DATABASE_PORT=3306
DATABASE_NAME=db
DATABASE_USERNAME=username
DATABASE_PASSWORD=P1ssw0rdGoB222
EOF

kubectl create secret generic kubeapp-creds --from-file=kubeapp.env
kubectl get secret kubeapp-creds
kubectl describe secret kubeapp-creds
kubectl get secret kubeapp-creds -o yaml
kubectl get secret kubeapp-creds \
 -o jsonpath='{.data.kubeapp\.env}' \
 | base64 --decode
echo -n “<BASE64 ENCODING>” | base64 --decode	
```
## Using Secret as environment variable
```
cat <<EOF> pod-secret.yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubeapp-secret
spec:
  containers:
  - name: kubeapp
    image: kubenesia/kubeapp
    volumeMounts:
    - name: kubeapp-creds-volume
      mountPath: /app/.env
      subPath: .env
      readOnly: true
  volumes:
  - name: kubeapp-creds-volume
    secret:
      secretName: kubeapp-creds
      items:
      - key: kubeapp.env
        path: .env
EOF

kubectl apply -f pod-secret.yaml
kubectl exec -it kubeapp-secret -- ls -la
kubectl exec -it kubeapp-secret -- cat .env
kubectl exec -it kubeapp-secret -- sh
cat /app/.env
```
  


