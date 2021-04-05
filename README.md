

# cluster management host dependencies
```bash
apt install ansible pdsh
```

# create ansible 'hosts' file from .template
```bash
cp hosts.template hosts
```

# create pdhs 'pdsh_hosts' file from .template
```bash
cp pdsh_hosts.template pdsh_hosts
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

# install deps on all nodes
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
pdsh -w ^pdsh_hosts -R ssh "rm -rf /etc/cni/net.d"
pdsh -w ^pdsh_hosts -R ssh "ip link delete flannel.1; ip link delete cni0"
pdsh -w ^pdsh_hosts -R ssh "ip link delete cni0"
pdsh -w ^pdsh_hosts -R ssh "rm -rf /home/k8s/.kube"
pdsh -w ^pdsh_hosts -R ssh "rm -rf /root/*.log; rm -rf /home/k8s/*.log"

# wipe docker
pdsh -w ^pdsh_hosts -R ssh "apt-get purge docker-ce docker-ce-cli containerd.io; apt autoremove"
pdsh -w ^pdsh_hosts -R ssh "rm -rf /var/lib/docker"

# wipe k8s
pdsh -w ^pdsh_hosts -R ssh "apt-get apt-get purge kubeadm kubectl kubelet kubernetes-cni kube*; apt autoremove"
pdsh -w ^pdsh_hosts -R ssh "rm -rf /etc/cni /etc/kubernetes /var/lib/dockershim /var/lib/etcd /var/lib/kubelet /var/run/kubernetes ~/.kube/*"
pdsh -w ^pdsh_hosts -R ssh "iptables -F && iptables -X; \
iptables -t nat -F && iptables -t nat -X; \
iptables -t raw -F && iptables -t raw -X; \
iptables -t mangle -F && iptables -t mangle -X"


```

# update keyserver with k8s repo keys if needed
```bash
pdsh -w ^pdsh_hosts -R ssh "apt-key adv --keyserver keyserver.ubuntu.com --recv-keys [key from apt update error]"
```

# kubectl alias with completion

```bash
alias k=kubectl
complete -F __start_kubectl k
```