# Default Scheduling
```
#buat file pod-myapp.yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
spec:
  containers:
  - name: nginx
    image: nginx

#buat file node-web-app.yaml
apiVersion: v1
kind: Pod
metadata:
  name: node-web-app
spec:
  containers:
  - name: node-web-app
    image: hudani/node-web-app

kubectl apply -f pod-myapp.yaml
kubectl apply -f node-web-app.yaml
kubectl get pods -o wide
```

# NodeSelector
```
#cara nambah label
kubectl label node worker1 disk=hdd
kubectl label node worker2 disk=ssd
kubectl label node <nama-node> <any-key>=<any-value>

#cara verifikasi label
kubectl label node <nama-node> --list
kubectl get nodes --show-labels
kubectl get nodes -l <key1>=<value1>,<key2>=<value2>

#cara hapus label
kubectl label node <nama-node> disk-
kubectl label node <nama-node> <any key>-

cat <<EOF> pod-myapp-ssd.yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-ssd
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disk: ssd
EOF

kubectl apply -f pod-myapp-ssd.yaml
kubectl get nodes -l disk=ssd
kubectl get pods -o wide
```
