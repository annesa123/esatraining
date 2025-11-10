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
#Static Pod
```
ssh <IP-WORKER>

cat <<EOF | sudo tee /etc/kubernetes/manifests/pod-static.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-static
spec:
  containers:
  - name: nginx
    image: nginx
EOF
```

#ReplicaSets
```
cat <<EOF> kubeapp-rs.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: kubeapp-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: kubeapp-rs
  template:
    metadata:
      labels:
        app: kubeapp-rs
    spec:
      containers:
      - name: kubeapp
        image: kubenesia/kubeapp:1.0.0
EOF


kubectl apply -f kubeapp-rs.yaml
kubectl get rs
kubectl describe rs <nama replicaset>
kubectl get pods
kubectl edit rs <nama replicaset>
    #ganti angka replicas 3 > 5

#update replicas by kubectl
kubectl scale rs/kubeapp-rs --replicas=<new-replicas>

#replicaset with nodeSelector
cat <<EOF> kubeapp-rs-with-nodeSelector.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: kubeapp-rs-with-nodeselector
spec:
  replicas: 3
  selector:
    matchLabels:
      app: kubeapp-rs
  template:
    metadata:
      labels:
        app: kubeapp-rs
    spec:
      containers:
      - name: kubeapp
        image: kubenesia/kubeapp:1.0.0
      nodeSelector:
        disk: ssd 
EOF

kubectl apply -f kubeapp-rs-with-nodeSelector.yaml
```
