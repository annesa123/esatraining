# Install metrics server


kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

kubectl get pods -n kube-system
kubectl edit deployment -n kube-system metrics-server

#sejajar dg containers, tambahkan baris berikut
hostNetwork: true

#dibagian argument tambahkan
- --kubelet-insecure-tls



# cara verifikasi metrics-server sudah berjalan dg baik atau tidak

kubectl get pods -n kube-system

#pastikan status nya running dan ready (1/1)

# cek utilisasi CPU / Memory
kubectl top pods
kubectl top nodes

# enable autoscaling on deployment / create HPA object

kubectl autoscale deployment <DEPLOYMENT-NAME> --min=<MIN> --max=<MAX> --cpu-percent=<CPU treshold>

kubectl autoscale deployment kubeapp --min=3 --max=10 --cpu-percent=60

# Check HPA object

kubectl get hpa


# set deployment resource request cpu, agar HPA berjalan dg baik
# resource request = jumlah reservasi resources dari pod ke worker/node

kubectl edit deployment kubeapp



# simulasi 100000 user mengakses deployment Kubeapp secara bersamaan

sudo apt install apache2-utils -y

#terminal 1
kubectl port-forward deployment/kubeapp --address 0.0.0.0 8080:8000

#terminal 2
watch kubectl get hpa

#terminal 3
ab -n 100000 -c 1000 http://<IP PUBLIC MASTER>:8080/
