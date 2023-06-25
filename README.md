# okd-3-11-example

## Authentication HTpasswd

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
