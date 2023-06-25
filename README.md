# okd-3-11-example

## Ansible

### Inventory

```yaml
all:
  children:
    OSEv3:
      vars:
        ansible_ssh_user: root
        ansible_user: root
        debug_level: 2
        internal_console_port: 8443
        openshift_deployment_type: origin
        openshift_disable_check: docker_storage,memory_availability,disk_availability
        openshift_master_api_port: 8443
        openshift_master_cluster_hostname: console.okd3-prod.seems.local
        openshift_master_cluster_public_hostname: console.okd3-prod.seems.local
        openshift_master_console_port: 8443
        openshift_master_default_subdomain: apps.okd3-prod.seems.local
        openshift_master_htpasswd_file: /etc/origin/master/htpasswd
        openshift_master_htpasswd_users:
          okdadmin: $apr1$XbI1zlV3$FuiA0g8tUfbYmZDeZpyRs/
          # password: dmlAjICyfrYXCsEH3NOoeeZMBkbo9G0JJy70z4etiO1dlCoo
        openshift_master_identity_providers:
          - challenge: 'true'
            kind: HTPasswdPasswordIdentityProvider
            login: 'true'
            name: htpasswd_auth
      children:
        etcd:
          hosts:
            okd3-master[0:2].seems.local:
              openshift_node_group_name: node-config-master
        lb:
          hosts:
            okd3-lb0.seems.local:
        masters:
          hosts:
            okd3-master[0:2].seems.local: { }
        nfs:
          hosts:
            okd3-lb0.seems.local: { }
        nodes:
          hosts:
            okd3-infra[0:1].seems.local:
              openshift_node_group_name: node-config-infra
            okd3-master[0:2].seems.local: { }
            okd3-node[0:1].seems.local:
              openshift_node_group_name: node-config-compute
```

### Run

```bash
# Prepare
ansible-playbook -i hosts.yaml \
  openshift-ansible/playbooks/prerequisites.yml

# Deploy
ansible-playbook -i hosts.yaml \
  openshift-ansible/playbooks/deploy_cluster.yml

# Uninstall
ansible-playbook -i hosts.yaml \
  openshift-ansible/playbooks/adhoc/uninstall.yml
```


## Authentication

### HTpasswd

```yaml
credentials:
 - name: okdadmin
   password: dmlAjICyfrYXCsEH3NOoeeZMBkbo9G0JJy70z4etiO1dlCoo
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
