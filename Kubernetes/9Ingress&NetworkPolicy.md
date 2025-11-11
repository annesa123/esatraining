# Ingress
## Ingress controller installation
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

curl <NODE_PUBLIC_IP>:<NodePort ingress service>

curl -H "Host: kubeapp.com" http://<NODE_PUBLIC_IP>:<NODE-PORT SVC INGRESS NGINX>/home
curl -H "Host: kubeapp.com" http://<NODE_PUBLIC_IP>:<NODE-PORT SVC INGRESS NGINX>/account

vim /etc/hosts
<PUBLIC_IP> kubeapp.com




notepad <C:\Windows\System32\drivers\etc>
# di browser
kubeapp.com:<NODEPORT SVC INGRESS NGINX>/
kubeapp.com:<NODEPORT SVC INGRESS NGINX>/account
