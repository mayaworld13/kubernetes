# Kubeadm Installation Guide with CRI-O runtime

This guide outlines the steps needed to set up a Kubernetes cluster using kubeadm.

## Pre-requisites

- Ubuntu OS 
- sudo privileges
- Internet access
- t2.medium instance type or higher

---

## Setup

1. Update the packages and install packages needed to use the Kubernetes repository


   ```bash
    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl gpg
   ```
2. Download the public signing key for the Kubernetes package repositories.

   ```bash
    curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
   ```
   
 3. Add the appropriate Kubernetes apt repository. Please note that this repository have packages only for Kubernetes 1.30; for other Kubernetes minor versions, you need to change the Kubernetes minor version in the URL to match your desired minor version

    ```bash
    echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
    ```

4. Update the apt packages, install kubelet, kubeadm and kubectl, and enable the kubelet

   ```bash
    sudo apt-get update
    sudo apt-get install -y kubelet kubeadm kubectl
    sudo apt-mark hold kubelet kubeadm kubectl
    sudo systemctl enable --now kubelet
   ```

5. IPv4 forwarding is a networking feature that allows a system to forward packets from one network interface to another. In the context of Kubernetes, enabling IPv4 forwarding is crucial for networking between different containers or pods running on different nodes within a Kubernetes cluster.

   To enable IPv4 forwarding for Kubernetes networking

      ```bash
      cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
      net.ipv4.ip_forward = 1
      EOF
      ```
6. This command loads system-wide settings from the /etc/sysctl.conf file and applies them to the kernel. It's often used after modifying kernel parameters to ensure that changes take effect without requiring a system reboot.
   
   To apply system-wide kernel parameters in Linux

   ```bash
   sudo sysctl --system
   ```


# Installing CRI-O for Kubernetes

### To install CRI-O, a lightweight container runtime, for Kubernetes, follow these steps:

1. **Import the GPG key for the CRI-O repository:**
   
sudo curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/ /" | sudo tee /etc/apt/sources.list.d/cri-o.list
sudo apt-get update -y
sudo apt-get install -y cri-o
sudo systemctl daemon-reload
sudo systemctl enable crio --now
sudo kubeadm config images pull
sudo kubeadm init
sudo chmod 644 /etc/kubernetes/admin.conf
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/calico.yaml
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
