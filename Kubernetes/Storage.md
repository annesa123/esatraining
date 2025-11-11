# Storage
## Static provisioning
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
