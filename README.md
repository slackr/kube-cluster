# dependencies

```bash
apt install ansible
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