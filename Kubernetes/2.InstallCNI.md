# Install CNI
## Install Network CNI ini hanya di Master-Node
```
curl https://raw.githubusercontent.com/projectcalico/calico/v3.28.2/manifests/calico.yaml -O
kubectl apply -f calico.yaml
kubectl get pods -n kube-system | grep calico
watch kubectl get pods -n kube-system -o wide

# verifikasi semua component kube-system sudah running
kubectl get pods -n kube-system

# verifikasi master dan worker node sudah join
kubectl get nodes
```

