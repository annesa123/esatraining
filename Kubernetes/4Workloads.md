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
# Static Pod
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

# ReplicaSets
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
# Deployment
```
cat <<EOF> kubeapp.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kubeapp
spec:
  replicas: 5
  selector:
    matchLabels:
      app: kubeapp
      service: a
  template:
    metadata:
      labels:
        app: kubeapp
        service: a
    spec:
      containers:
      - name: kubeapp
        image: kubenesia/kubeapp:1.0.0  
EOF

kubectl apply -f kubeapp.yaml
kubectl get deployment
kubectl get replicaset
kubectl get pods
kubectl get pods --show-labels
```
# scale deployment
```
kubectl scale deployment <name deployment> --replicas=<jumlah replika>
```
# Deployment rolling update (update versi ke 1.1.0)
```
kubectl rollout history deployment kubeapp -n <namespace>
kubectl annotate deployment <NAMA DEPLOYMENT> kubernetes.io/change-cause="<CHANGE CAUSE>"
kubectl set image deployment <NAMA DEPLOYMENT> <NAMA CONTAINER>=<IMAGE BARU> --record
kubectl set image deployment kubeapp kubeapp=kubenesia/kubeapp:1.1.0 --record
```

# Deployment check rollout history
```
kubectl rollout status deployment kubeapp
kubectl rollout history deployment kubeapp -n <namespace>
kubectl annotate deployment <NAMA DEPLOYMENT> kubernetes.io/change-cause="<CHANGE CAUSE>"
kubectl rollout history deployment kubeapp

#Setiap melakukan update / rolling update image, Deployment akan membuatkan replicaset baru
kubectl get rs

```
# Update ke versi dan rollback
```
kubectl set image deployment kubeapp kubeapp=kubenesia/kubeapp:1.2.0 --record

1.0.0 → 1.2.0 → 1.1.0

# Deployment Rollback 

#akan melakukan rollback ke satu versi di sebelumnya
kubectl rollout undo deployment kubeapp
kubectl rollout undo deployment kubeapp --to-revision=<REVISION NUMBER>
```



