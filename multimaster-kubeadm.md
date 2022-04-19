# Set up a multi-master Kubernetes environment locally using Kubeadm

`sudo su`

`apt-get update`

### kubeadm, kubelet, and kubectl

Install packages needed to use the Kubernetes `apt` repo
`sudo apt-get install -y apt-transport-https ca-certificates curl`

Download the Google Cloud public signing key
`sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg`

Add the Kubernetes `apt` repository
`echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list`

Update `apt` package index, install kubelet, kubeadm and kubectl, and pin their version

```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

check version
`kubectl version --client && kubeadm version`

check that swap is disabled: `sudo swapon --show`

turn off swap using

```bash
sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
swapoff -a
```

Load `br_netfilter` and `overlay` modules. Then turn the 3 things on (set to 1).

```bash
modprobe overlay
modprobe br_netfilter

tee /etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sysctl --system
```
