# Kubernetes File Locations Cheat Sheet

## **Key Kubernetes File Locations and Their Purpose**

### **1. Kubernetes Configuration Files**
| File Path | Description | Used For |
|-----------|-------------|----------|
| `/etc/kubernetes/admin.conf` | Kubeconfig file for cluster administration (used by `kubectl`). | Cluster authentication, user access |
| `/etc/kubernetes/kubelet.conf` | Kubeconfig for Kubelet to communicate with API server. | Node authentication, Kubelet connection |
| `/etc/kubernetes/scheduler.conf` | Kubeconfig for the scheduler. | Scheduler configuration, admission controller plugins |
| `/etc/kubernetes/controller-manager.conf` | Kubeconfig for the controller manager. | Controller settings, leader election |
| `$HOME/.kube/config` | Default user-specific kubeconfig file (used by `kubectl`). | User authentication, context switching |

### **2. Kubernetes Static Pod Manifests (Kubeadm Based Setup)**
| File Path | Description | Used For |
|-----------|-------------|----------|
| `/etc/kubernetes/manifests/kube-apiserver.yaml` | API server pod manifest (managed by Kubelet). | Admission controllers, authentication, API extensions |
| `/etc/kubernetes/manifests/kube-controller-manager.yaml` | Controller Manager pod manifest. | Pod scheduling, node lifecycle management |
| `/etc/kubernetes/manifests/kube-scheduler.yaml` | Scheduler pod manifest. | Scheduler policies, admission controller plugins |
| `/etc/kubernetes/manifests/etcd.yaml` | etcd pod manifest. | Cluster data storage, etcd quorum |

### **3. Kubelet and Node Configuration**
| File Path | Description | Used For |
|-----------|-------------|----------|
| `/var/lib/kubelet/config.yaml` | Kubelet configuration file. | Node resource limits, eviction settings |
| `/etc/systemd/system/kubelet.service.d/10-kubeadm.conf` | Systemd service configuration for the Kubelet. | Kubelet startup options, node registration |
| `/var/lib/kubelet/pod-resources` | Stores information about pod resource allocations. | CPU/memory allocation, node resources |

### **4. Kubernetes Networking and CNI Configuration**
| File Path | Description | Used For |
|-----------|-------------|----------|
| `/etc/cni/net.d/` | Directory containing CNI network configuration files. | Network plugin configuration, pod networking |
| `/opt/cni/bin/` | Directory for CNI plugins. | CNI binaries, network policies |
| `/var/lib/kube-proxy/config.conf` | kube-proxy configuration file. | Network proxy settings, cluster IP management |

### **5. Kubernetes Logs and Debugging**
| File Path | Description | Used For |
|-----------|-------------|----------|
| `/var/log/kube-apiserver.log` | Logs for the API server. | API requests, authentication failures |
| `/var/log/kube-scheduler.log` | Logs for the scheduler. | Scheduling decisions, pod placement |
| `/var/log/kube-controller-manager.log` | Logs for the controller manager. | Controller actions, resource scaling |
| `/var/log/etcd.log` | Logs for etcd database. | Cluster state, database transactions |
| `/var/log/kubelet.log` | Logs for the Kubelet service (on worker nodes). | Node issues, pod lifecycle events |
| `/var/log/kube-proxy.log` | Logs for kube-proxy service. | Network connectivity, service routing |

### **6. Kubernetes Certificates**
| File Path | Description | Used For |
|-----------|-------------|----------|
| `/etc/kubernetes/pki/ca.crt` | Kubernetes cluster root certificate authority. | Cluster authentication, TLS security |
| `/etc/kubernetes/pki/apiserver.crt` | API server certificate. | Secure API communications |
| `/etc/kubernetes/pki/apiserver.key` | API server private key. | Secure API authentication |
| `/etc/kubernetes/pki/front-proxy-ca.crt` | Front proxy CA certificate. | Proxy authentication |
| `/etc/kubernetes/pki/etcd/ca.crt` | etcd CA certificate. | Secure etcd communication |

### **7. Persistent Storage Locations**
| File Path | Description | Used For |
|-----------|-------------|----------|
| `/var/lib/etcd` | etcd database storage (critical for Kubernetes state persistence). | Cluster data storage, etcd quorum |
| `/var/lib/kubelet` | Directory where Kubelet stores pod and container state information. | Pod state tracking, container logs |
| `/mnt/data/` | Default directory where persistent volumes (PVs) are mounted. | Persistent storage for applications |

### **8. Kubernetes Add-ons and Custom Manifests**
| File Path | Description | Used For |
|-----------|-------------|----------|
| `/etc/kubernetes/addons/` | Directory for add-on manifests like DNS, ingress, etc. | Add-on deployments, DNS configurations |
| `/etc/kubernetes/dashboard/` | Directory for Kubernetes Dashboard YAML files. | Web-based cluster UI deployment |
| `/etc/kubernetes/metrics-server/` | Metrics Server deployment files. | Real-time monitoring, resource tracking |

---

## **Useful Commands to Debug and Access These Files**

### **View Kubernetes Configuration Files**
```sh
cat /etc/kubernetes/admin.conf
kubectl config view --kubeconfig=/etc/kubernetes/admin.conf
```

### **Check Static Pod Manifests**
```sh
ls -l /etc/kubernetes/manifests/
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

### **View Kubelet Configuration**
```sh
cat /var/lib/kubelet/config.yaml
```

### **Check Kubernetes Logs**
```sh
tail -f /var/log/kube-apiserver.log
journalctl -u kubelet -f
```

### **List Kubernetes Certificates**
```sh
ls -l /etc/kubernetes/pki/
```

### **Check Persistent Storage Paths**
```sh
du -sh /var/lib/etcd/
du -sh /var/lib/kubelet/
```

---

## **Summary**
- **Kubernetes configuration files** are stored in `/etc/kubernetes/`.
- **Static pod manifests** for control plane components are in `/etc/kubernetes/manifests/`.
- **Logs for Kubernetes components** are stored in `/var/log/`.
- **Certificates for secure communication** are stored in `/etc/kubernetes/pki/`.
- **Persistent storage for Kubernetes** is found in `/var/lib/etcd/` and `/var/lib/kubelet/`.



