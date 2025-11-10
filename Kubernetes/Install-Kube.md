# Kubernetes Installation
## Lakukan install sshpass
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
