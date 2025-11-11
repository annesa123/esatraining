# Network & Services
## ClusterIP
```
cat <<EOF> service-clusterip.yaml
apiVersion: v1
kind: Service
metadata:
  name: kubeapp
spec:
  type: ClusterIP
  selector:
    app: kubeapp
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8000
EOF

kubectl apply -f service-clusterip.yaml

(optional)#cara lain expose deployment
kubectl expose deployment kubeapp --port=80 --target-port=8000

kubectl get svc
kubectl describe svc kubeapp
kubectl get ep
kubectl describe ep
kubectl get pods -o wide

#verifikasi service
kubectl run test -it --rm --image=kubenesia/kubebox -- sh
curl kubeapp
curl <IP SERVICE>
exit
```
# ExternalName
```
cat <<EOF> service-externalname.yaml
apiVersion: v1
kind: Service
metadata:
  name: idn
spec:
  type: ExternalName
  externalName: www.idn.id
EOF

kubectl apply -f service-externalname.yaml
kubectl describe svc idn
kubectl run test -it --rm --image=kubenesia/kubebox -- sh
curl -k https://idn
nslookup idn
exit
```
# NodePort
```
cat <<EOF> service-nodeport.yaml 
apiVersion: v1
kind: Service
metadata:
  name: kubeapp-nodeport
spec:
  type: NodePort
  selector:
    app: kubeapp
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8000
EOF

kubectl apply -f service-nodeport.yaml
kubectl get svc kubeapp-nodeport
kubectl describe svc kubeapp-nodeport

<IP PUBLIC MASTER/WORKER>:<NODE-PORT>
```
# LoadBalancer
```
cat <<EOF> service-loadbalancer.yaml
apiVersion: v1
kind: Service
metadata:
  name: kubeapp-loadbalancer
spec:
  type: LoadBalancer
  selector:
    app: kubeapp
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8000
EOF

kubectl apply -f service-loadbalancer.yaml
kubectl get svc kubeapp-loadbalancer
kubectl describe svc kubeapp-loadbalancer
```
# Install Metallb
```
kubectl edit configmap -n kube-system kube-proxy
>>>
  strictARP: true
>>>

kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.9/config/manifests/metallb-native.yaml

cat <<EOF> firstpool.yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 10.23.3.45-10.23.3.49
EOF

kubectl apply -f firstpool.yaml
kubectl get ipaddresspool

cat <<EOF> L2.yaml
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2
  namespace: metallb-system
spec:
  ipAddressPools:
  - first-pool
EOF

kubectl apply -f L2.yaml
kubectl get L2Advertisement
```


