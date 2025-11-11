# etcd backup 
```
sudo apt-get install etcd-client

# before backup

kubectl create ns before-backup

# execute backup
sudo ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key snapshot save snapshot.db

ls /etc/kubernetes/pki/etcd/

sudo ETCDCTL_API=3 etcdctl --write-out=table snapshot status snapshot.db

sudo ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key get / --prefix --keys-only

sudo ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key member list

kubectl get all -n default
```


# delete namespace and modify 
```
kubectl delete  ns before-backup
etcd restore
sudo cp /etc/kubernetes/manifests/etcd.yaml /home/ubuntu/

sudo ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --name=master --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key --data-dir /var/lib/etcd-from-backup --initial-cluster=master=https://127.0.0.1:2380 --initial-cluster-token etcd-cluster-1 --initial-advertise-peer-urls=https://127.0.0.1:2380 snapshot restore snapshot.db --skip-hash-check=true


# edit manifest (blue: add, green: modify)

sudo vim /etc/kubernetes/manifests/etcd.yaml

    - --data-dir=/var/lib/etcd-from-backup
    - --initial-cluster-token=etcd-cluster-1

    volumeMounts:
    - mountPath: /var/lib/etcd-from-backup
      name: etcd-data


  - hostPath:
      path: /var/lib/etcd-from-backup
      type: DirectoryOrCreate
    name: etcd-data


# verify restore

kubectl get ns before-backup
```
