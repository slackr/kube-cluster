# dependencies

```bash
apt install ansible pdsh
```

# if ssh key is password-protected

```bash
ssh-agent bash
ssh-add ~/.ssh/id_rsa
```

# init cluster

```bash
ansible-playbook -i ./hosts ./init.yml
```

# install deps on all

```bash
ansible-playbook -i ./hosts ./deps.yml
```

# setup master 

```bash
ansible-playbook -i ./hosts ./master.yml
```

# verify master is online

```bash
ssh k8s@master_ip
kubectl get nodes
```

# join worker nodes to cluster

```bash
ansible-playbook -i ./hosts ./nodes.yml
```

# verify nodes are online

```bash
ssh k8s@master_ip
kubectl get nodes
```

# reset kube cluster
```bash
pdsh -w ^pdsh_hosts -R ssh "kubeadm reset -f"
pdsh -w ^pdsh_hosts -R ssh "rm -rf /etc/cni/net.d/10-flannel.conflist; ip link delete flannel.1; ip link delete cni0"
pdsh -w ^pdsh_hosts -R ssh "rm -rf /home/k8s/.kube"
pdsh -w ^pdsh_hosts -R ssh "rm -rf /root/*.log; rm -rf /home/k8s/*.log"
pdsh -w ^pdsh_hosts -R ssh "rm -rf /etc/cni/net.d"
```

# kubectl alias with completion

```bash
alias k=kubectl
complete -F __start_kubectl k
```