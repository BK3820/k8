# **Kubernetes Cheatsheet: Scheduling & Resource Management**

### **Approaches:**
- **Using NodeName** - Manually assign a pod to a node before pod creation.
- **Using Binding** - Manually assign a pod to a node after pod creation.
- **NodeSelector** - Assigns pods based on node labels.
- **Node Affinity** - Advanced node selection using labels.
- **Pod Affinity and Pod Anti-Affinity** - Schedule pods together or apart.
- **Taints and Toleration** - Restrict pod placement on tainted nodes.
- **Priority and Preemption** - High-priority pods replace lower-priority ones.
- **Topology** - Distribute pods across zones/nodes.

---

### **1. Using NodeName (Manual Assignment Before Pod Creation)**
#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  nodeName: worker-node-1
  containers:
    - name: nginx
      image: nginx
```
#### **Imperative Command:**
```sh
kubectl run my-pod --image=nginx --overrides='{"spec": {"nodeName": "worker-node-1"}}'
```

---

### **2. Using Binding (Manual Assignment After Pod Creation)**
#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Binding
metadata:
  name: my-pod  # Name of the existing pod
  namespace: default
target:
  apiVersion: v1
  kind: Node
  name: worker-node-1  # Target node
```
#### **Imperative Command:**
```sh
kubectl apply -f binding.yaml
```

---

### **3. NodeSelector (Using Labels of the Node)**
#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-selector
spec:
  nodeSelector:
    disktype: ssd  # Matches the node label
  containers:
    - name: nginx
      image: nginx
```
#### **Imperative Command:**
```sh
kubectl label node worker-node-1 disktype=ssd
```

---

### **4. Node Affinity (Advanced Way Using Node Labels)**
#### **Affinity Types:**
- **requiredDuringSchedulingIgnoredDuringExecution** - Ensures the pod is only scheduled if it matches the defined rules.
- **preferredDuringSchedulingIgnoredDuringExecution** - Tries to schedule the pod on matching nodes but doesn’t enforce it.
- **requiredDuringSchedulingRequiredDuringExecution** - (Upcoming feature) Enforces node constraints both during scheduling and runtime.

#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: node-affinity-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: disktype
                operator: In
                values:
                  - ssd
  containers:
    - name: nginx
      image: nginx
```

---

### **5. Pod Affinity and Pod Anti-Affinity (Schedule Pods Together or Apart)**
#### **Affinity Types:**
- **Pod Affinity** - Ensures that pods are scheduled close together (e.g., same node or same region).
- **Pod Anti-Affinity** - Ensures that pods are spread across different nodes or zones.

#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-affinity-pod
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchLabels:
              app: my-app
          topologyKey: "kubernetes.io/hostname"
  containers:
    - name: nginx
      image: nginx
```

---

### **6. Taints and Tolerations (Restrict Pod Placement on Tainted Nodes)**
#### **Taint Effects:**
- **NoSchedule** - Prevents scheduling of pods on the node.
- **PreferNoSchedule** - Prefers to avoid scheduling but allows it if necessary.
- **NoExecute** - Evicts existing pods from the node.

#### **Imperative Command:**
```sh
kubectl taint nodes worker-node-1 key=value:NoSchedule
```
#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: toleration-pod
spec:
  tolerations:
    - key: "key"
      operator: "Equal"
      value: "value"
      effect: "NoSchedule"
  containers:
    - name: nginx
      image: nginx
```

---

### **7. Priority and Preemption (High-Priority Pods Replace Lower-Priority Ones)**
#### **Preemption Policies:**
- **PreemptLowerPriority** - Removes lower-priority pods to accommodate the high-priority pod.
- **Never** - Prevents the pod from preempting any other pods.

#### **Declarative YAML:**
```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
preemptionPolicy: PreemptLowerPriority
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: priority-pod
spec:
  priorityClassName: high-priority
  containers:
    - name: nginx
      image: nginx
```

---

### **8. Topology (Distribute Pods Across Zones/Nodes)**
#### **Topology Spread Constraints:**
- **maxSkew** - Defines the maximum imbalance allowed between topology domains.
- **topologyKey** - Defines the node label used to spread pods.
- **whenUnsatisfiable** - Defines behavior when scheduling constraints cannot be met (`DoNotSchedule` or `ScheduleAnyway`).

#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: topology-aware-pod
spec:
  topologySpreadConstraints:
    - maxSkew: 1
      topologyKey: "topology.kubernetes.io/zone"
      whenUnsatisfiable: DoNotSchedule
      labelSelector:
        matchLabels:
          app: my-app
  containers:
    - name: nginx
      image: nginx
```

---

### **Summary of Scheduling Approaches:**
| Approach | Description |
|----------|-------------|
| **NodeName** | Assigns a pod to a specific node manually before creation. |
| **Binding** | Assigns a pod to a node after creation using the API. |
| **NodeSelector** | Assigns pods to nodes based on labels. |
| **Node Affinity** | Advanced method using label-based node selection. |
| **Pod Affinity/Anti-Affinity** | Ensures pods are scheduled together or apart based on labels. |
| **Taints & Tolerations** | Restricts pods from running on specific nodes unless tolerated. |
| **Priority & Preemption** | High-priority pods can evict lower-priority pods if needed. |
| **Topology** | Spreads pods evenly across nodes/zones. |

