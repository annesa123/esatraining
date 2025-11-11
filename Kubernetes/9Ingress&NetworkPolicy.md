# Ingress
## Ingress controller installation
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.0/deploy/static/provider/baremetal/deploy.yaml

kubectl get all -n ingress-nginx
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
Route by path
kubectl create deployment kubeapp-home --image=nginx --replicas=2
kubectl expose deployment kubeapp-home --port=80
kubectl get svc
kubectl get ep

kubectl create deployment kubeapp-account --image=httpd --replicas=2
kubectl expose deployment kubeapp-account --port=80

kubectl get deploy
kubectl get svc

kubectl run test -it --rm --image=kubenesia/kubebox -- sh
curl kubeapp-home
curl kubeapp-account

cat <<EOF> ingress-fanout.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-fanout
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: kubeapp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: kubeapp-home
            port:
              number: 80
      - path: /account
        pathType: Prefix
        backend:
          service:
            name: kubeapp-account
            port:
              number: 80
EOF
kubectl apply -f ingress-fanout.yaml
kubectl get ingress

kubectl get svc -n ingress-nginx



notepad <C:\Windows\System32\drivers\etc>
# di browser
kubeapp.com:<NODEPORT SVC INGRESS NGINX>/
kubeapp.com:<NODEPORT SVC INGRESS NGINX>/account
```

## Network Policy
```
kubectl get pods -n kube-system

cat <<EOF> netpol-deny-from-other-ns.yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: default
  name: deny-from-other-namespaces
spec:
  podSelector: {}
  ingress:
  - from:
    - podSelector: {}
EOF

kubectl apply -f netpol-deny-from-other-ns.yaml

kubectl get networkpolicy


**# test koneksi dari luar namespace default**
kubectl run test -it -n dev --rm --image=kubenesia/kubebox -- sh
wget -qO- --timeout=5 kubeapp.default
wget: download timed out

**# test koneksi dari dalam namespace default**
kubectl run test -it -n default --rm --image=kubenesia/kubebox -- sh	
wget -qO- --timeout=2 kubeapp
	Hello World!

kubectl delete netpol deny-from-other-namespaces
```

