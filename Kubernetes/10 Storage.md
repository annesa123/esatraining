# Storage
## Static provisioning
```
sudo apt-get install nfs-common -y


cat <<EOF> pv-nfs.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: test-pv
spec:
  storageClassName: ""
  capacity:
    storage: 1Gi
  accessModes:
   - ReadWriteMany
  nfs:
    # change this
    server: 10.184.0.55
    path: /mnt/nfs_share
EOF



kubectl apply -f pv-nfs.yaml

kubectl get pv
kubectl describe pv test-pv

cat <<EOF> pvc-nfs.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-claim
spec:
  storageClassName: ""
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi

EOF

kubectl apply -f pvc-nfs.yaml
kubectl get pvc
kubectl describe pvc nfs-claim

```
## Mount volume on Pod
```
cat <<EOF> pod-nginx-pvc.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pvc
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: nfs
      mountPath: /usr/share/nginx/html
  volumes:
  - name: nfs
    persistentVolumeClaim:
      claimName: nfs-claim
EOF

kubectl apply -f pod-nginx-pvc.yaml
kubectl get pods nginx-pvc
kubectl describe pods nginx-pvc


kubectl exec -it nginx-pvc -- bash
cd /usr/share/nginx/html/
touch <NAMA>.txt
```
