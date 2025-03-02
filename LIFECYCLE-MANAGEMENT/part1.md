# **Kubernetes Cheatsheet: Application Lifecycle Management**

## **Application Lifecycle Approaches:**
- **Rolling Updates and Rollbacks** - Gradual updates with rollback support.
- **Configuring Applications** - Define commands, arguments, environment variables.
- **ConfigMaps** - Manage non-sensitive configuration data.
- **Secrets** - Store sensitive configuration data.
- **Scaling Applications** - Adjust the number of running pods.
- **Multi-Container Pods** - Design patterns for running multiple containers in a pod.
- **InitContainers** - Pre-start containers to initialize resources.


---

### **1. Rolling Updates and Rollbacks**
#### **Imperative Commands:**
```sh
kubectl set image deployment/my-app my-container=nginx:latest
kubectl rollout status deployment/my-app
kubectl rollout undo deployment/my-app
```
#### **Declarative YAML:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:latest
```

---

### **2. Commands and Arguments**
#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: command-demo
spec:
  containers:
    - name: busybox
      image: busybox
      command: ["echo"]
      args: ["Hello, Kubernetes!"]
```

---

### **3. ConfigMaps**
#### **Imperative Commands:**
```sh
kubectl create configmap my-config --from-literal=key1=value1
kubectl create configmap my-config --from-file=config.properties
```
#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  key1: "value1"
  config-file: |
    property1=valueA
    property2=valueB
```

---

### **4. Secrets**
#### **Imperative Commands:**
```sh
kubectl create secret generic my-secret --from-literal=password=mysecurepassword
kubectl create secret generic my-secret --from-file=secret.properties
```
#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  password: bXlzZWN1cmVwYXNzd29yZA==  # Base64 encoded value
  secret-file: |
    U2VjcmV0RGF0YT1Tb21lVmFsdWU=
```

---

### **5. Scaling Applications**
#### **Imperative Commands:**
```sh
kubectl scale deployment my-app --replicas=5
```
#### **Declarative YAML:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 5
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: nginx
          image: nginx
```

---

### **6. Multi-Container Pods**
#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
    - name: main-app
      image: nginx
    - name: sidecar-logger
      image: busybox
      command: ["sh", "-c", "while true; do echo Hello from Sidecar; sleep 10; done"]
```

---

### **7. InitContainers**
#### **Declarative YAML:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-container-demo
spec:
  initContainers:
    - name: init-script
      image: busybox
      command: ["sh", "-c", "echo Initializing..."]
  containers:
    - name: main-app
      image: nginx
```
---

### **Summary of Application Lifecycle Approaches:**
| Approach | Description |
|----------|-------------|
| **Rolling Updates and Rollbacks** | Enables updating applications with rollback capability. |
| **Configuring Applications** | Uses commands, arguments, and environment variables. |
| **ConfigMaps** | Stores non-sensitive application configuration. |
| **Secrets** | Stores sensitive data securely in Kubernetes. |
| **Scaling Applications** | Manually increases or decreases pod replicas. |
| **Multi-Container Pods** | Supports multiple containers within a single pod. |
| **InitContainers** | Runs initialization steps before main containers start. |



