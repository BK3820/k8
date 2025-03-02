# **Kubernetes Autoscaling Overview**

## **Scaling in Kubernetes**
Scaling in Kubernetes applies to two areas:
1. **Scaling Workloads:** Scaling the number of pods within a cluster.
2. **Scaling the Cluster Infrastructure:** Scaling the number of nodes in the cluster.

## **Manual vs. Automated Scaling**
| **Type** | **Horizontal Scaling** | **Vertical Scaling** |
|---------|----------------------|----------------------|
| **Manual Scaling** | `kubectl scale --replicas=N deployment/my-app` | `kubectl edit deployment my-app` (adjust resource limits) |
| **Automated Scaling** | **HPA** (adds/removes pods based on metrics) | **VPA** (adjusts pod resource allocation) |
| **Cluster Scaling** | **Cluster Autoscaler** (adds/removes nodes) | Less common; requires modifying existing nodes |


## **Monitoring Requirements for HPA and VPA**
Both **HPA (Horizontal Pod Autoscaler) and VPA (Vertical Pod Autoscaler) require a monitoring tool** to function properly. They do not deploy a built-in monitoring solution by themselves.

### **1️⃣ Horizontal Pod Autoscaler (HPA)**
- **Requires:** **Metrics Server**
- **Why?** HPA makes scaling decisions based on CPU, memory, or custom metrics, and it retrieves this data from the **Metrics Server**.
- **Installation:** If your Kubernetes cluster does not have a Metrics Server installed, HPA will not function.
  ```sh
  kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
  ```
- **How It Works:**  
  - HPA queries the **Metrics Server** for CPU/memory usage.
  - If CPU/memory exceeds a predefined threshold, HPA increases the number of pod replicas.
  - If usage drops, it reduces the number of pods.

### **Manual Scaling Example:**
```sh
kubectl scale deployment my-app --replicas=5
```

### **HPA Imperative Command:**
```sh
kubectl autoscale deployment my-app --cpu-percent=50 --min=1 --max=10
```

### **HPA Declarative YAML:**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 1
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```


---

### **2️⃣ Vertical Pod Autoscaler (VPA)**

### **VPA Imperative Setup:**
```sh
kubectl apply -f https://github.com/kubernetes/autoscaler/releases/latest/download/vpa.yaml
```

### **VPA Declarative YAML:**
```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-app-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  updatePolicy:
    updateMode: "Auto"
```

### **VPA Operating Modes:**
| **Mode** | **Description** |
|---------|----------------|
| **Off** | Only provides recommendations, no action taken. |
| **Initial** | Adjusts resources only when new pods are created. |
| **Recreate** | Evicts pods and recreates them with new resource values. |
| **Auto** | Adjusts resources automatically and updates existing pods when possible. |

## **VPA Workflow: How It Works**
Vertical Pod Autoscaler consists of three main components:

1. **Recommender** - Monitors pod resource usage and suggests CPU/memory allocations.
2. **Updater** - Detects under/over-provisioned pods and evicts them for resource adjustments.
3. **Admission Controller** - Applies recommendations to new pods during creation.

### **Step-by-Step VPA Workflow:**
1. **Recommender Collects Metrics:**
   - Continuously monitors CPU and memory usage using the Kubernetes Metrics API.
   - Stores historical and live data to determine optimal resource allocation.

2. **Recommender Provides Recommendations:**
   - Suggests **minimum, target, and maximum** values for CPU and memory.
   - Does not take action directly but provides data to other components.

3. **Updater Detects Under/Over-Provisioned Pods:**
   - Compares current resource usage with recommendations.
   - If a pod is under-provisioned (starving for resources) or over-provisioned (wasting resources), it **marks the pod for eviction**.
   - Eviction is **only performed if the mode is set to `Recreate` or `Auto`**.

4. **Updater Evicts Pods (if necessary):**
   - If the mode allows, the updater forcefully evicts pods, which causes Kubernetes to recreate them.

5. **Admission Controller Applies Recommendations:**
   - When a new pod starts (after being evicted or a new deployment is made), the admission controller **modifies the pod spec**.
   - The pod starts with the **new recommended CPU and memory settings**.

### **Visual Summary of VPA Workflow:**
```
[Recommender] ---> Monitors usage and provides recommendations
      ↓
[Updater] ---> Detects under/over-provisioned pods, decides whether to evict
      ↓
[Admission Controller] ---> Applies recommendations to new pods
```

### **Key Points:**
- **Recommender only suggests changes**, it does not enforce them.
- **Updater evicts pods** when needed (only if `Recreate` or `Auto` mode is enabled).
- **Admission Controller ensures** new pods start with correct resource requests.

## **Cluster Autoscaler**
Cluster Autoscaler automatically adds or removes nodes in a Kubernetes cluster based on the presence of pending pods that cannot be scheduled due to resource constraints.

### **Key Points:**
- Works with cloud providers like AWS, Azure, and GCP.
- Ensures that all pending pods get scheduled by provisioning more nodes.
- Removes underutilized nodes if they are not needed.

### **Installation and Example Manifest:**
Instead of a specific manifest file, refer to the official Cluster Autoscaler repository for the latest deployment instructions and configurations:
👉 **[Cluster Autoscaler GitHub Repository](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler)**


