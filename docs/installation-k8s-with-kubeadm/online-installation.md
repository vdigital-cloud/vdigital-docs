---
layout: default
title: Cài đặt K8s bằng Kubeadm (Online)
parent: Cài đặt K8s bằng Kubeadm
nav_order: 5
---
# Cài đặt K8s bằng Kubeadm (Online)
{: .no_toc }

## Mục Lục
{: .no_toc .text-delta }

1. TOC
{:toc}

---
## Chuẩn bị trước khi cài đặt
<div class="code-example" markdown="1">
```
sudo swapoff -a 
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld
```
</div>
---

## Cài đặt Docker
<div class="code-example" markdown="1">
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
systemctl start docker
systemctl enable docker
```
</div>
---

## Cài đặt Kubelet, Kubeadm, Kubectl
<div class="code-example" markdown="1">
```
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF
```
</div>
<div class="code-example" markdown="1">
```
sudo yum install -y kubelet-1.24.6 kubeadm-1.24.6 kubectl-1.24.6 --disableexcludes=kubernetes
```
</div>
---

## Pre-init controlplane
<div class="code-example" markdown="1">
```
rm /etc/containerd/config.toml
systemctl restart containerd
echo "1" > /proc/sys/net/bridge/bridge-nf-call-iptables
```
</div>
---

## Init Controlplane
<div class="code-example" markdown="1">
```
sudo kubeadm init --control-plane-endpoint "<mgmt-ip>:6443" --upload-certs --kubernetes-version 1.24.6
```
</div>

Câu lệnh trên sẽ tự động tạo ra đoạn mã cho cả master và worker node có thể kết nối vào cụm k8s. Copy tokens vào các node có trong mạng.z 

---

## Applying Network (Calico)
<div class="code-example" markdown="1">
```
curl https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml -O
kubectl apply -f calico.yaml
```
</div>