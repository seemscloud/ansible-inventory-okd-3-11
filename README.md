# Ansible Inventory OKD 3.11

## Ansible

### Run

```bash
# Prepare
ansible-playbook -i inventories/hosts.yaml \
  openshift-ansible/playbooks/prerequisites.yml

# Deploy
ansible-playbook -i inventories/hosts.yaml \
  openshift-ansible/playbooks/deploy_cluster.yml

# Uninstall
ansible-playbook -i inventories/hosts.yaml \
  openshift-ansible/playbooks/adhoc/uninstall.yml
```

## Certificates Redeploy

### Full

```bash
ansible-playbook \
  -i inventory/hosts.yaml \
  openshift-ansible/playbooks/redeploy-certificates.yml
```

### Partial

```bash
ansible-playbook \
  -i inventory/hosts.yaml \
  openshift-ansible/playbooks/openshift-etcd/redeploy-ca.yml
```

```bash
ansible-playbook \
  -i inventory/hosts.yaml \
  openshift-ansible/playbooks/openshift-master/redeploy-certificates.yml

ansible-playbook \
  -i inventory/hosts.yaml \
  openshift-ansible/playbooks/openshift-master/redeploy-openshift-ca.yml
```

### Access

```bash
ansible-playbook \
  -i inventory/hosts.yaml \
  openshift-ansible/playbooks/openshift-master/redeploy-named-certificates.yml

ansible-playbook \
  -i inventory/hosts.yaml \
  openshift-ansible/playbooks/openshift-hosted/redeploy-router-certificates.yml
```

## DNS (`seems.local`)
 
### Forward

```ini
okd3-master0              IN    A      10.100.100.10
okd3-master1              IN    A      10.100.100.11
okd3-master2              IN    A      10.100.100.12
okd3-infra0               IN    A      10.100.100.13
okd3-infra1               IN    A      10.100.100.14
okd3-lb0                  IN    A      10.100.100.15
okd3-node0                IN    A      10.100.100.17 
okd3-node1                IN    A      10.100.100.18

apps.okd3-prod            IN    A      10.100.100.13
apps.okd3-prod            IN    A      10.100.100.14
*.apps.okd3-prod          IN    A      10.100.100.13
*.apps.okd3-prod          IN    A      10.100.100.14

console.okd3-prod         IN    A      10.100.100.15
```

### Reverse

```ini
10                       IN    PTR    okd3-master0.seems.local.
11                       IN    PTR    okd3-master1.seems.local.
12                       IN    PTR    okd3-master2.seems.local.
13                       IN    PTR    okd3-infra0.seems.local.
14                       IN    PTR    okd3-infra1.seems.local.
15                       IN    PTR    okd3-lb0.seems.local.
17                       IN    PTR    okd3-node0.seems.local.
18                       IN    PTR    okd3-node1.seems.local.
```

## Authentication

### HTpasswd Manually add User

```bash
# Get Hash
htpasswd -n okdadmin

# Verify Hash with Password
htpasswd -vb /etc/origin/master/htpasswd okdadmin dmlAjICyfrYXCsEH3NOoeeZMBkbo9G0JJy70z4etiO1dlCoo

# Save File to `/etc/origin/master/htpasswd`
cat > /etc/origin/master/htpasswd << "EndOfMessage"
okdadmin:$apr1$gt/uN/qF$jqqiPEDS3auIXcC0bAyME1
EndOfMessage

# Manuall Re
master-restart api
master-restart controllers
```

### Login

```bash
oc login https://console.okd3-prod.seems.local:8443 \
  --username okdadmin \
  --password dmlAjICyfrYXCsEH3NOoeeZMBkbo9G0JJy70z4etiO1dlCoo \
  --insecure-skip-tls-verify=true \
  --loglevel=10
```

### Using Kube Admin

```bash
oc --config /etc/origin/master/admin.kubeconfig get pods --all-namespaces

kubectl --kubeconfig /etc/origin/master/admin.kubeconfig get pods --all-namespaces
```
