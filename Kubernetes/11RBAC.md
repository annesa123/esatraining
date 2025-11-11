#RBAC

# create user certificate
openssl genrsa -out nugraha.key 2048
openssl req -new -key nugraha.key -out nugraha.csr -subj "/CN=nugraha/O=development"

#CN: username
#O: namespace

sudo openssl x509 -req -in nugraha.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out nugraha.crt -days 45

#verify
openssl x509 -in nugraha.crt -text -noout

# 3 file: private, csr, public (signed certificate)

# create context / kubeconfig credentials

kubectl config set-credentials nugraha --client-certificate=nugraha.crt --client-key=nugraha.key
kubectl config set-context nugraha-ctx --cluster=kubernetes --namespace=dev --user=nugraha
kubectl config get-contexts
kubectl config use-context nugraha-ctx
kubectl get nodes
	Error from server (Forbidden): nodes is forbidden: User "nugraha" cannot list resource "nodes" in API group "" at the cluster scope

#melihat configuration context
sudo cat ~/.kube/config

# create Role (membuat role/permission dg resource yg akan diizinkan beserta verbs/operasi yg bisa dilakukan)

#kembali menggunakan user admin
kubectl config use-context kubernetes-admin@kubernetes

cat <<EOF> role-developer.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
  namespace: dev
rules:
- apiGroups: ["", "apps"]
  resources: ["deployments", "replicasets", "pods", "services"]
  verbs: ["list", "get", "watch", "create", "update", "patch", "delete"]
EOF

kubectl apply -f role-developer.yaml
kubectl get role -n dev developer
kubectl describe role -n dev developer

#cara cek api group
kubectl api-resources

