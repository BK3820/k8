# Setting Up a Kubernetes Cluster from Scratch

## Prerequisites

### Hardware Requirements:
- **Master Node:** Minimum 2 CPUs, 2 GB RAM
- **Worker Nodes:** Minimum 1 CPU, 2 GB RAM each
- Full network connectivity between nodes
- Unique hostname, MAC address, and product UUID for each node
- Open required ports for Kubernetes communication

### Software Requirements:
- Linux-based OS (Ubuntu, CentOS, etc.)
- SSH access to all nodes
- Disable swap:
  ```bash
  sudo swapoff -a
  sudo sed -i '/ swap / s/^/#/' /etc/fstab
  ```
- Set the hostname on each node:
  ```bash
  sudo hostnamectl set-hostname <hostname>
  ```

## Step 1: Install Container Runtime
Kubernetes supports container runtimes like **containerd, CRI-O, and Docker (with cri-dockerd)**.

### Install `containerd` (Recommended)
```bash
sudo apt-get update
sudo apt-get install -y containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd
```

## Step 2: Install Kubernetes Components
### Add Kubernetes Repositories:
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
```
### Install Kubernetes Components:
```bash
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
Enable `kubelet`:
```bash
sudo systemctl enable --now kubelet
```

## Step 3: Initialize the Control Plane
On the master node, run:
```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

Set up the `kubectl` configuration:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Step 4: Install a Pod Network
Choose a CNI (Container Network Interface) plugin:
- **Calico**:
  ```bash
  kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
  ```
- **Flannel**:
  ```bash
  kubectl apply -f https://github.com/coreos/flannel/raw/master/Documentation/kube-flannel.yml
  ```

Verify the setup:
```bash
kubectl get nodes
kubectl get pods -A
```

## Step 5: Join Worker Nodes
On each worker node, run the command displayed by `kubeadm init`, or retrieve it from the master node:
```bash
kubeadm token create --print-join-command
```
Run the generated command on each worker node:
```bash
sudo kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

Verify worker nodes are added:
```bash
kubectl get nodes
```

## Step 6: Customizing the Cluster
### Enable Scheduling on Control Plane Node (Optional)
```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

### Configure kube-proxy (Optional)
Modify `kube-proxy` settings via:
```yaml
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
mode: "ipvs"
```
Apply the changes:
```bash
kubectl apply -f kube-proxy-config.yaml
```

## Step 7: Verify Cluster Setup
```bash
kubectl cluster-info
kubectl get nodes
kubectl get pods -A
```

## Step 8: Deploy a Sample Application
```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort
kubectl get svc
```
Access the application using the node's IP and NodePort.

## Cleanup (If Needed)
Reset all nodes:
```bash
kubeadm reset
```
Remove the node from the cluster:
```bash
kubectl delete node <node-name>
```

## Conclusion
You have successfully set up a Kubernetes cluster using `kubeadm` from scratch! 🎉

