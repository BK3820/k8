# Kubernetes Monitoring and Logging Cheat Sheet

## **Monitoring in Kubernetes**

### **Why Monitor Kubernetes?**
Monitoring a Kubernetes cluster helps track:
✅ **Node-level metrics** → Number of nodes, health status, CPU, memory, disk, and network utilization.
✅ **Pod-level metrics** → Number of pods, individual pod CPU and memory consumption.
✅ **Cluster performance** → Ensuring efficient resource allocation and troubleshooting.

---

## **Kubernetes Monitoring Solutions**

| **Monitoring Tool**   | **Description** |
|----------------------|----------------|
| **Metrics Server**   | Lightweight, in-memory monitoring tool for Kubernetes. No historical data storage. |
| **Prometheus**       | Open-source monitoring and alerting toolkit. Stores time-series data. |
| **Elastic Stack (ELK)** | Provides centralized logging, monitoring, and analytics. |
| **Datadog/Dynatrace** | Proprietary enterprise solutions for advanced Kubernetes monitoring. |

🔴 **Deprecated Tool:** Heapster (replaced by Metrics Server).

---

## **Metrics Server**

- **Retrieves real-time metrics** from Kubernetes nodes and pods.
- **Uses `kubelet` and `cAdvisor`** to collect resource usage.
- **Does not store historical data** (only keeps metrics in memory).

### **Deploying Metrics Server**

✅ **For Minikube:**
```sh
minikube addons enable metrics-server
```
✅ **For Other Environments:**
```sh
git clone https://github.com/kubernetes-sigs/metrics-server.git
kubectl apply -f metrics-server/deploy/kubernetes/
```

### **Viewing Metrics**

- **Node Metrics:**
```sh
kubectl top node
```
- **Sort Node Metrics by CPU Usage:**
```sh
kubectl top node --sort-by=cpu
```
- **Sort Node Metrics by Memory Usage:**
```sh
kubectl top node --sort-by=memory
```
- **Pod Metrics Across All Namespaces:**
```sh
kubectl top pod --all-namespaces
```

#### **Check Kubernetes Cluster Health**
```sh
kubectl get componentstatuses
```
✅ Shows the status of core Kubernetes components like etcd, controller-manager, and scheduler.

---

## **Logging in Kubernetes**

### **Basic Logging with `kubectl logs`**
Logs in Kubernetes are stored at the **container level** and can be retrieved using `kubectl logs`.

```sh
kubectl logs <pod-name>
```

📌 **For streaming live logs:**
```sh
kubectl logs -f <pod-name>
```

### **Logging in Multi-Container Pods**
If a pod has multiple containers, specify the container name:
```sh
kubectl logs <pod-name> -c <container-name>
```

### **Additional Logging Commands**

- **View Logs for a Pod in a Specific Namespace**
```sh
kubectl logs <pod-name> -n <namespace>
```
- **View Logs from Previous Pod Instance**
```sh
kubectl logs <pod-name> --previous
```
- **Get Logs for All Containers in a Pod**
```sh
kubectl logs <pod-name> --all-containers=true
```
- **Filter Logs Using `grep`**
```sh
kubectl logs <pod-name> | grep "ERROR"
```
- **Check System Logs on Kubernetes Nodes**
```sh
journalctl -u kubelet -f
```
✅ Monitors **Kubelet logs** on the node in real time.

---

## **📊 Prometheus-Specific Commands**
If using **Prometheus for advanced monitoring**, these commands help interact with Prometheus metrics:

- **List All Available Metrics**
```sh
curl http://<prometheus-ip>:9090/api/v1/label/__name__/values
```
✅ Retrieves a list of **all available metrics**.

- **Query a Specific Metric (Example: CPU Usage)**
```sh
curl "http://<prometheus-ip>:9090/api/v1/query?query=node_cpu_seconds_total"
```
✅ Queries CPU usage from Prometheus.

---

## **Docker vs Kubernetes Logging**

| Feature        | Docker Logging (`docker logs`) | Kubernetes Logging (`kubectl logs`) |
|--------------|--------------------------------|--------------------------------|
| Log Storage  | Logs stored per container    | Logs collected at the pod level |
| Live Streaming | `docker logs -f`            | `kubectl logs -f`               |
| Multi-Container | Not needed                  | Must specify `-c <container-name>` |

---

## **Summary**

- **Monitoring Kubernetes** helps track node and pod health.
- **Metrics Server** provides real-time data but does not store logs.
- **Prometheus, Elastic Stack, and Datadog** are recommended for long-term monitoring.
- **`kubectl logs`** retrieves logs from running containers.
- **Use `-f` flag** for live log streaming.
- **Multi-container pods require specifying the container name** when fetching logs.
- **Prometheus queries help retrieve custom metrics for advanced monitoring.**



