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

# Dynamic provisioning
## Install Helm di node master
```
sudo apt-get install curl gpg apt-transport-https --yes
curl -fsSL https://packages.buildkite.com/helm-linux/helm-debian/gpgkey | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/helm.gpg] https://packages.buildkite.com/helm-linux/helm-debian/any/ any main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm


# setup nfs provisioner

helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner

helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner --set nfs.server=10.184.0.55 --set nfs.path=/mnt/nfs_share --set storageClass.create=false --set storageClass.provisionerName=kubenesia.com/nfs


# verifikasi nfs provisioner, pastikan running
kubectl get pods -l app=nfs-subdir-external-provisioner
kubectl logs -l app=nfs-subdir-external-provisioner

```

## membuat StorageClass untuk dynamic provisioning
```
cat <<EOF> sc-kubenesia.yaml
allowVolumeExpansion: true
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  labels:
    app: nfs-subdir-external-provisioner
  name: nfs-kubenesia
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
parameters:
  archiveOnDelete: "true"
provisioner: kubenesia.com/nfs
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

kubectl apply -f sc-kubenesia.yaml
kubectl get sc


cat <<EOF> pvc-dynamic.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: nfs-dynamic
spec:
  storageClassName: nfs-kubenesia
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
EOF

kubectl apply -f pvc-dynamic.yaml

kubectl get pvc
```
## Create Pod With PVC Dynamic
```
cat <<EOF> pod-nginx-pvc-dynamic.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pvc-dynamic
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
      claimName: nfs-dynamic
EOF

kubectl apply -f pod-nginx-pvc-dynamic.yaml
kubectl exec -it nginx-pvc-dynamic -- bash
cd /usr/share/nginx/html
touch <nama-from-pvc-dynamic>.yaml

```

