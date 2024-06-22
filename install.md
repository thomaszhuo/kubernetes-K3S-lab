##
10.10.10.77 kube-master-1

10.10.10.81 kube-worker-1

10.10.10.82 kube-worker-2

##

```bash
@kube-master-1:~# curl -sfL https://get.k3s.io | sh -s server --docker
```
@kube-master-1:~# systemctl status k3s.service
● k3s.service - Lightweight Kubernetes
     Loaded: loaded (/etc/systemd/system/k3s.service; enabled; vendor preset: enabled)
     Active: active (running)
   Main PID: 723 (k3s-server)
      Tasks: 13
     Memory: 463.7M
        CPU: 19.009s
     CGroup: /system.slice/k3s.service
             └─723 "/usr/local/bin/k3s server" "" "" "" "" "" "" "" "" ""
```bash
@kube-master-1:~# cp /etc/rancher/k3s/k3s.yaml /.kube/config
```
```bash
@kube-master-1:~# chown $USER:$GROUP /.kube/config
```
```bash
@kube-master-1:~# export KUBECONFIG=~/.kube/config
```

```bash
root@kube-master-1:~# cat /.kube/config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: censored
    server: https://10.10.10.77:6443
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: censored
    client-key-data: censored
```

catatan : node-token default
```bash
root@kube-master-1:~# cat /var/lib/rancher/k3s/server/node-token
```

Untuk gabung ke Cluster Master
```bash
@kube-worker-1:~# curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="agent" K3S_TOKEN="<token cluster master kamu>" sh -s - --server https://10.10.10.77:6443
```
output kube-worker-1 telah bergabung

@kube-master-1:~# kubectl get nodes
NAME            STATUS   ROLES                  AGE     VERSION
kube-worker-1   Ready    <none>                 5m32s   v1.29.5+k3s1
kube-master-1   Ready    control-plane,master   21m     v1.29.5+k3s1

ulangi untuk worker 2
```bash
@kube-worker-2:~# curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="agent" K3S_TOKEN="<token cluster master kamu>" sh -s - --server https://10.10.10.77:6443
```
output kube-worker-2 telah bergabung
@kube-master-1:~# kubectl get nodes
NAME            STATUS   ROLES                  AGE    VERSION
kube-worker-1   Ready    <none>                 10m    v1.29.5+k3s1
kube-master-1   Ready    control-plane,master   26m    v1.29.5+k3s1
kube-worker-2   Ready    <none>                 107s   v1.29.5+k3s1
##
cek status service master
-> systemctl status k3s.service


cek status worker
-> systemctl status k3s-agent.service
##
