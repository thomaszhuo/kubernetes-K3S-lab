##
_Lightweight_ Kubernetes menggunakan Container runtime Docker.
##
10.10.10.77 kube-master-1

10.10.10.81 kube-worker-1

10.10.10.82 kube-worker-2

##
Install terlebih dahulu K3s, dengan command berikut;
```bash
@kube-master-1:~# curl -sfL https://get.k3s.io | sh -s server --docker
```
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
@kube-master-1:~# mkdir .kube
```          
```bash
@kube-master-1:~# cp /etc/rancher/k3s/k3s.yaml .kube/config
```
```bash
@kube-master-1:~# chown $USER:$GROUP .kube/config
```
```bash
@kube-master-1:~# export KUBECONFIG=~.kube/config
```

sesuaikan IP server
```bash
@kube-master-1:~# cat /.kube/config
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

_catatan : untuk melihat node-token_
```bash
@kube-master-1:~# cat /var/lib/rancher/k3s/server/node-token
```

untuk gabung ke cluster master
```bash
@kube-worker-1:~# curl -sfL https://get.k3s.io | K3S_TOKEN="node_token_anda" sh -s - agent --docker --server https://10.10.10.77:6443
```
ulangi untuk worker 2
```bash
@kube-worker-2:~# curl -sfL https://get.k3s.io | K3S_TOKEN="node_token_anda" sh -s - agent --docker --server https://10.10.10.77:6443
```

berikut hasil untuk mengecek apakah node worker sudah bergabung dengan node master

@kube-master-1:~# kubectl get nodes
NAME            STATUS   ROLES                  AGE   VERSION
kube-worker-1   Ready    <none>                 10m   v1.29.5+k3s1
kube-worker-2   Ready    <none>                 21s   v1.29.5+k3s1
kube-master-1   Ready    control-plane,master   18m   v1.29.5+k3s1

@kube-master-1:~# kubectl get nodes -o wide
NAME            STATUS   ROLES                  AGE   VERSION        INTERNAL-IP   EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION       CONTAINER-RUNTIME
kube-worker-1   Ready    <none>                 10m   v1.29.5+k3s1   10.10.10.81   <none>        Ubuntu 22.04 LTS   5.15.0-25-generic    docker://24.0.7
kube-worker-2   Ready    <none>                 25s   v1.29.5+k3s1   10.10.10.82   <none>        Ubuntu 22.04 LTS   5.15.0-25-generic    docker://24.0.7
kube-master-1   Ready    control-plane,master   18m   v1.29.5+k3s1   10.10.10.77   <none>        Ubuntu 22.04 LTS   5.15.0-112-generic   docker://24.0.7




##
cek status service master
-> systemctl status k3s.service

cek status service worker
-> systemctl status k3s-agent.service
##
