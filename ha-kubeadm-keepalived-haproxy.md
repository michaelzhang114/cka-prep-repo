# Set up Highly Available Kubernetes

## Multipass environment
| Node type     | VM name | RAM (GB) | CPU | Storage |
|---------------|---------|----------|-----|---------|
| Load balancer | lb1     | 1        | 2   | 5       |
| Load balancer | lb1     | 1        | 2   | 5       |
| Control plane | cp1     | 2        | 2   | 20      |
| Control plane | cp2     | 2        | 2   | 20      |
| Control plane | cp3     | 2        | 2   | 20      |

```
multipass launch -n lb1 -c 2 -m 1G -d 5G
multipass launch -n lb2 -c 2 -m 1G -d 5G
multipass launch -n cp1 -c 2 -m 2G -d 20G
multipass launch -n cp2 -c 2 -m 2G -d 20G
multipass launch -n cp3 -c 2 -m 2G -d 20G
```

![image](https://user-images.githubusercontent.com/31636398/163896780-cb54a8e2-5e37-4419-98f8-0d6c9e89c06a.png)

VIP: 172.19.29.242

## Set up load balancer nodes

```
sudo su
apt update && apt install -y keepalived haproxy
```

```
systemctl --all list-units | grep haproxy
systemctl --all list-units | grep keepalived
systemctl status haproxy
systemctl status keepalived
```

### keepalived health check script

```
cat >> /etc/keepalived/check_apiserver.sh <<EOF
#!/bin/sh

errorExit() {
    echo "*** $*" 1>&2
    exit 1
}

curl --silent --max-time 2 --insecure https://localhost:6443/ -o /dev/null || errorExit "Error GET https://localhost:6443/"
if ip addr | grep -q 172.19.29.242; then
    curl --silent --max-time 2 --insecure https://172.19.29.242:6443/ -o /dev/null || errorExit "Error GET https://172.19.29.242:6443/"
fi
EOF

chmod +x /etc/keepalived/check_apiserver.sh
```

### keepalived service configuration file

```
cat >> /etc/keepalived/keepalived.conf <<EOF
! /etc/keepalived/keepalived.conf
! Configuration File for keepalived

global_defs {
    router_id LVS_DEVEL
}
vrrp_script check_apiserver {
  script "/etc/keepalived/check_apiserver.sh"
  interval 3
  weight -2
  fall 10
  rise 2
}

vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 100
    authentication {
        auth_type PASS
        auth_pass 42
    }
    virtual_ipaddress {
        172.19.29.242
    }
    track_script {
        check_apiserver
    }
}
EOF
```

```
systemctl enable --now keepalived
systemctl status keepalived
```

### haproxy config

```
cat >> /etc/haproxy/haproxy.cfg <<EOF

# /etc/haproxy/haproxy.cfg
#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
    log /dev/log local0
    log /dev/log local1 notice
    daemon

#---------------------------------------------------------------------
# common defaults that all the 'listen' and 'backend' sections will
# use if not designated in their block
#---------------------------------------------------------------------
defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 1
    timeout http-request    10s
    timeout queue           20s
    timeout connect         5s
    timeout client          20s
    timeout server          20s
    timeout http-keep-alive 10s
    timeout check           10s

#---------------------------------------------------------------------
# apiserver frontend which proxys to the control plane nodes
#---------------------------------------------------------------------
frontend apiserver
    bind *:6443
    mode tcp
    option tcplog
    default_backend apiserver

#---------------------------------------------------------------------
# round robin balancing for apiserver
#---------------------------------------------------------------------
backend apiserver
    option httpchk GET /healthz
    http-check expect status 200
    mode tcp
    option ssl-hello-chk
    balance     roundrobin
        server master1 172.19.31.135:6443 check
        server master2 172.19.19.2:6443 check
        server master3 172.19.21.151:6443 check
        # [...]

EOF
```

```
systemctl enable haproxy && systemctl restart haproxy
```

## Create the cluster

Initialize

```
kubeadm init --control-plane-endpoint="172.19.29.242:6443" --upload-certs --apiserver-advertise-address=172.19.31.135
```

Join other master nodes

Remember to add `--apiserver-advertise-address={NODE IP}`

```
kubeadm join 172.19.29.242:6443 --token napwnm.ttsqxi8by4h8bb6e --discovery-token-ca-cert-hash sha256:3c8da45b2e4ec7314701377354744a5c03b34d14b7d4dffe785322322b86d86d --control-plane --certificate-key 8022f488051fc00c9129fa8640350c0faeee8a44b43746b72a821c979a9dc9d5 --apiserver-advertise-address=172.19.21.151
```
