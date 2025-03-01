# Kubernetes Scheduler Profiles Cheat Sheet

## **Kubernetes Scheduling Process**

![Kubernetes Scheduler Flow](/images/scheduler.png)

1. **Pod Creation** → Pod enters **Scheduling Queue**.
2. **Sorting Phase** → Pods are **sorted by priority**.
3. **Filtering Phase** → Nodes that **can't run the pod** are **filtered out**.
4. **Scoring Phase** → Remaining nodes are **scored** based on available resources.
5. **Binding Phase** → Pod is **assigned to the node with the highest score**.

---

## **Scheduler Phases and Plugins**

### **1. Scheduling Queue (Sorting Phase)**

- **Priority Sort Plugin** → Sorts pods based on their **priority class**.

#### **Example: Define a PriorityClass**

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000  # Higher value = higher priority
```

---

### **2. Filtering Phase**

Filters out nodes that **cannot** run the pod.

#### **Plugins Used in Filtering:**

- **NodeResourcesFit** → Ensures node has enough CPU & Memory.
- **NodeName** → Filters nodes if `nodeName` is specified in pod spec.
- **NodeUnschedulable** → Filters nodes marked as **cordoned or drained**.

#### **Example: Cordoning a Node (Unschedulable)**

```sh
kubectl cordon worker-node-1
```

---

### **3. Scoring Phase**

Assigns scores to remaining nodes based on different criteria.

#### **Plugins Used in Scoring:**

- **NodeResourcesFit** → Scores nodes based on available resources.
- **ImageLocality** → Prefers nodes **already having the container image**.

#### **Example: Image Locality Plugin Behavior**

- If Node A **already has the image**, it gets a **higher score**.
- If no nodes have the image, the pod is placed on **any node**.

---

### **4. Binding Phase**

The pod is assigned to the node with the highest score.

#### **Plugins Used in Binding:**

- **DefaultBinder** → Binds pod to the chosen node.

---

## **Scheduler Customization Using Extension Points**

- **Pre-Filter** → Runs before filtering nodes.
- **Filter** → Filters out unsuitable nodes.
- **Post-Filter** → Runs after filtering.
- **Pre-Score** → Runs before scoring nodes.
- **Score** → Scores remaining nodes.
- **Reserve** → Reserves a node before binding.
- **Permit** → Approves scheduling before binding.
- **Pre-Bind** → Runs just before binding.
- **Bind** → Binds the pod to a node.
- **Post-Bind** → Runs after binding.

---

## **Multiple Scheduler Profiles (Introduced in Kubernetes 1.18)**

Instead of running **multiple scheduler binaries**, you can configure multiple **scheduler profiles** within a **single scheduler**.

### **Example: Define Multiple Scheduler Profiles**

```yaml
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
  - schedulerName: my-scheduler-1
    plugins:
      filter:
        disabled:
          - name: NodeResourcesFit  # Disabling a plugin
  - schedulerName: my-scheduler-2
    plugins:
      preScore:
        disabled:
          - name: ImageLocality
```

#### **Use Cases for Multiple Scheduler Profiles:**

✅ **Workload Segmentation:** Assign different workloads to different scheduler profiles.
✅ **Custom Scheduling Policies:** Disable or enable plugins for specific scheduling behaviors.
✅ **High Availability:** Avoid scheduling conflicts by assigning specific profiles to workloads.

#### **Example: Running Pods with Different Scheduler Profiles**

**Run a Pod using `my-scheduler-1`:**
```sh
kubectl run my-pod-1 --image=nginx --scheduler-name=my-scheduler-1
```

**Run a Pod using `my-scheduler-2`:**
```sh
kubectl run my-pod-2 --image=nginx --scheduler-name=my-scheduler-2
```

**Declarative Method:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-3
spec:
  schedulerName: my-scheduler-1
  containers:
    - name: nginx
      image: nginx
```

Apply it with:
```sh
kubectl apply -f my-pod-3.yaml
```

Each profile **acts as a separate scheduler**, but all run within the **same scheduler process**.

#### **Key Benefits of Scheduler Profiles:**

✅ Avoids **race conditions** between multiple schedulers.  
✅ Allows customization **without running separate binaries**.  
✅ Supports **plugin enable/disable per profile**.

---

## **Commands for Scheduler Management**

| **Task**                            | **Command**                                                        |
| ----------------------------------- | ------------------------------------------------------------------ |
| List all schedulers                 | `kubectl get pods -n kube-system | grep scheduler` |
| Check scheduler logs                | `kubectl logs -n kube-system kube-scheduler-master`                |
| Run a pod with a specific scheduler | `kubectl run my-pod --image=nginx --scheduler-name=my-scheduler-1` |
| Describe scheduler profiles         | `kubectl describe kube-scheduler`                                  |

---

## **Conclusion**

- **Kubernetes Scheduler** uses **plugins** at different **extension points** to make scheduling decisions.
- **Multiple Scheduler Profiles** allow running different scheduling behaviors **within a single scheduler binary**.
- You can **enable/disable plugins** and create **custom scheduling logic** by modifying the scheduler configuration.



