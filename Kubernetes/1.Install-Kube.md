# Kubernetes Installation
## Lakukan install sshpass all node
```
sudo apt-get update
sudo apt-get install sshpass -y
```
## Provisoning Ansible only master-node
Clone Ansible Kubeam
```
git clone https://github.com/husnialhamdani/kubeadm-ansible.git
cd kubeadm-ansible
```
Install Ansible on ubuntu
```
sudo apt-get update
sudo apt-get install ansible -y
```
test and run playbook
```
export ANSIBLE_HOST_KEY_CHECKING=False
ansible -i inventory.ini kube_nodes -m ping
ansible-playbook -i inventory.ini kubeadm_playbook.yml

```
## Initiate Mster/Control Plan
```
sudo sysctl -w net.ipv4.ip_forward=1
sudo kubeadm init

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl get pods -n kube-system
kubectl get nodes

# Regenerate join token
kubeadm token create --print-join-command
```


