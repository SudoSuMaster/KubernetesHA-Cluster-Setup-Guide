# 🚀 Kubernetes High Availability (HA) Cluster Setup Guide

## 📖 Introduction
This guide walks you through setting up a **Kubernetes High Availability (HA) Cluster** using the **Stacked etcd topology**.

In this setup:
- `etcd` runs alongside the control plane nodes (masters), reducing network overhead and simplifying deployment.
- We'll configure **control plane nodes** and **worker nodes**, set up the **container runtime (containerd)**, and deploy Kubernetes using `kubeadm`.
- For networking, we'll use **Calico**.

### Architecture Overview
![Kubernetes HA Cluster](https://github.com/user-attachments/assets/cd8de135-0012-45bf-b5fb-5ab59eef7530)

More details: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/ha-topology/

---

## ✅ Prerequisites
Ensure the following before starting:

- **3+ Control Plane (Master) nodes**
- **2+ Worker nodes**
- **A Load Balancer** (e.g., HAProxy) for distributing traffic to API servers
- **Ubuntu 22.04+** on all nodes
- **Root or sudo privileges**
- **Network connectivity** between all nodes

---

## ⚙️ Step 1: Setup Load Balancer (HAProxy)

Install and configure HAProxy to distribute API requests across masters.

### Install HAProxy
```sh
sudo apt update && sudo apt install -y haproxy
```

### Configure HAProxy
Edit the config:
```sh
sudo vim /etc/haproxy/haproxy.cfg
```

Add:
```ini
frontend kubernetes-frontend
    bind 192.168.2.10:6443
    mode tcp
    option tcplog
    default_backend kubernetes-backend

backend kubernetes-backend
    mode tcp
    balance roundrobin
    option tcp-check
    server master-1 192.168.2.11:6443 check
    server master-2 192.168.2.12:6443 check
    server master-3 192.168.2.13:6443 check
```

Restart and enable HAProxy:
```sh
sudo systemctl restart haproxy
sudo systemctl enable haproxy
```

Verify:
```sh
nc 192.168.2.10 6443 -v
```

---

## ⚙️ Step 2: Prepare All Nodes (except Load Balancer)

### Disable Swap
```sh
sudo swapoff -a
sudo sed -i '/swap/d' /etc/fstab
```

### Configure Kernel Parameters
```sh
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
sudo sysctl --system
```

### Load Required Kernel Modules
```sh
sudo modprobe overlay
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
```

---

## ⚙️ Step 3: Add Kubernetes Repository
```sh
sudo mkdir -p -m 755 /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key   | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /"   | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

---

## ⚙️ Step 4: Install Kubernetes Components
```sh
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

---

## ⚙️ Step 5: Install & Configure Container Runtime (containerd)
```sh
sudo apt-get install -y containerd
sudo mkdir -p /etc/containerd

containerd config default | sudo tee /etc/containerd/config.toml > /dev/null
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

sudo systemctl restart containerd
sudo systemctl enable containerd
```

Enable kubelet:
```sh
sudo systemctl enable kubelet
```

---

## ⚙️ Step 6: Initialize Kubernetes Control Plane

Run this **on one control plane node**:
```sh
sudo kubeadm init   --control-plane-endpoint "192.168.2.10:6443"   --upload-certs   --pod-network-cidr=10.244.0.0/16
```

Save the **join command** from the output.

---

## ⚙️ Step 7: Configure kubectl on Primary Master
```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

## ⚙️ Step 8: Join Additional Nodes

### Additional Masters
```sh
kubeadm join 192.168.2.10:6443 --token <token>   --discovery-token-ca-cert-hash sha256:<hash>   --control-plane --certificate-key <cert-key>
```

### Worker Nodes
```sh
kubeadm join 192.168.2.10:6443 --token <token>   --discovery-token-ca-cert-hash sha256:<hash>
```

---

## ⚙️ Step 9: Deploy Network Plugin (Calico)

Run on any control plane node:
```sh
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
```

Check status:
```sh
kubectl get nodes
```

If any node is `NotReady`, restart kubelet:
```sh
sudo systemctl restart kubelet
```

---

## ⚙️ Step 10: Verify ETCD Cluster

### Install etcd client
```sh
sudo apt update && sudo apt install -y etcd-client
```

### Check members
```sh
ETCDCTL_API=3 etcdctl member list   --endpoints=https://127.0.0.1:2379   --cacert=/etc/kubernetes/pki/etcd/ca.crt   --cert=/etc/kubernetes/pki/etcd/server.crt   --key=/etc/kubernetes/pki/etcd/server.key
```

---

## 🎉 Conclusion

You now have a **Highly Available Kubernetes Cluster** with multiple control plane and worker nodes running on `containerd` with **Calico networking**.

Check node status:
```sh
kubectl get nodes
```

If any node is stuck in `NotReady`, restart kubelet and check networking.

✨ Congratulations! Your Kubernetes HA cluster is live and production-ready 🚀
