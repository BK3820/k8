# Kubernetes Admission Controllers Cheat Sheet

## **Understanding Admission Controllers**

### **How Requests Flow in Kubernetes**

1. **Request Initiation** → A user submits a request (e.g., creating a pod) via `kubectl`.
2. **Authentication** → Kubernetes verifies the identity of the user using certificates.
3. **Authorization** → Kubernetes checks if the user has the right permissions (RBAC enforcement).
4. **Admission Control** → The request goes through **admission controllers** for additional validation or modification.
5. **Persistence & Execution** → If approved, the request is saved in `etcd` and applied to the cluster.

---

## **Role-Based Access Control (RBAC) vs Admission Controllers**

| Feature           | Role-Based Access Control (RBAC)        | Admission Controllers                              |
| ----------------- | --------------------------------------- | -------------------------------------------------- |
| **Purpose**       | Controls **who** can perform actions    | Controls **how** requests are processed            |
| **Scope**         | API-level access control                | Enforces policies at resource creation/update time |
| **Examples**      | Allowing `developer` to create `pods`   | Preventing use of `latest` tag in images           |
| **Customization** | Configured via `Role` and `RoleBinding` | Enabled/disabled via API server config             |

---

## **Types of Admission Controllers**

### **1. Validating Admission Controllers**
- Validate requests and **reject** them if they don't meet policies.
- Example: `NamespaceExists` ensures a namespace **must exist** before a resource can be created.
- Example: Enforce that no pods run as `root`.

### **2. Mutating Admission Controllers**
- Modify the request **before** it is created.
- Example: `DefaultStorageClass` adds a storage class **automatically** if none is specified.
- Example: `NamespaceAutoProvision` (deprecated) creates a namespace automatically if it doesn't exist.

### **3. Combined Admission Controllers**
- Some controllers **validate and mutate**.
- Example: `PodSecurityPolicy` can **modify** security settings while also **validating** policies.

📌 **Mutating admission controllers run first, followed by validating controllers** to ensure that modifications are considered before validation.

---

## **Common Built-in Admission Controllers**

| **Admission Controller**   | **Purpose**                                                                        |
| -------------------------- | ---------------------------------------------------------------------------------- |
| **AlwaysPullImages**       | Ensures images are always pulled instead of using cached ones                      |
| **DefaultStorageClass**    | Automatically assigns a default storage class to PVCs if none is specified         |
| **EventRateLimit**         | Limits the number of requests an API server can handle to prevent flooding         |
| **NamespaceExists**        | Ensures the requested namespace exists before allowing resource creation           |
| **NamespaceAutoProvision** | Creates a namespace automatically if it does not exist (deprecated)                |
| **NamespaceLifecycle**     | Prevents deletion of critical namespaces (`default`, `kube-system`, `kube-public`) |

---

## **Example: Namespace Exists Admission Controller**

### **Scenario: Creating a Pod in a Non-Existent Namespace**

```sh
kubectl create namespace blue
kubectl apply -f pod.yaml --namespace=blue
```

🔴 **Error: Namespace not found** (if `NamespaceExists` is enabled)

✔️ **Solution:** Enable `NamespaceAutoProvision` (deprecated) or manually create the namespace.

---

## **How to Enable and Disable Admission Controllers**

### **Check Enabled Admission Controllers**

```sh
kube-apiserver --help | grep enable-admission-plugins
```

### **Enable an Admission Controller (Kubeadm Setup)**

1. Edit the API server manifest:

```sh
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

2. Modify the command section to add `--enable-admission-plugins`:

```yaml
- --enable-admission-plugins=NamespaceLifecycle,AlwaysPullImages
```

3. Save and restart Kubernetes:

```sh
systemctl restart kubelet
```

### **Disable an Admission Controller**

Modify `kube-apiserver.yaml` and add:

```yaml
- --disable-admission-plugins=NamespaceLifecycle
```

Then restart the API server.

---

## **Custom Admission Controllers using Webhooks**

### **Webhook-Based Admission Controllers**
- **MutatingAdmissionWebhook** → Used for modifying objects.
- **ValidatingAdmissionWebhook** → Used for validating objects.

### **How it Works**
1. The API server sends an `AdmissionReview` JSON request to the webhook server.
2. The webhook server processes the request and responds with an `AdmissionReview` JSON result.
3. If `allowed=true`, the request is permitted. Otherwise, it is denied.

### **Example: Restricting Image Sources**

```yaml
apiVersion: admissionregistration.k8s.io/v1
kind: ValidatingWebhookConfiguration
metadata:
  name: restrict-public-images
webhooks:
  - name: check-image-source.example.com
    rules:
      - apiGroups: [""]
        apiVersions: ["v1"]
        operations: ["CREATE", "UPDATE"]
        resources: ["pods"]
    clientConfig:
      service:
        name: image-policy
        namespace: kube-system
        path: /validate-image
    admissionReviewVersions: ["v1"]
    sideEffects: None
```

🚀 **Effect:** Blocks pod creation if the container image is from an **external registry** (e.g., Docker Hub).

---

## **Webhook Server Deployment Steps**

1. **Develop a Webhook Server** (e.g., in Go or Python)
2. **Deploy it in Kubernetes as a Service**
3. **Configure a Webhook Admission Controller** to point to the service

### **Example: Python Webhook Server (Pseudo Code)**

```python
from flask import Flask, request, jsonify
app = Flask(__name__)

@app.route("/validate", methods=["POST"])
def validate():
    request_json = request.get_json()
    if request_json["user"] == request_json["object_name"]:
        return jsonify({"allowed": False})
    return jsonify({"allowed": True})

if __name__ == "__main__":
    app.run(port=443, ssl_context=('cert.pem', 'key.pem'))
```

🚀 **Effect:** Rejects requests where the username and object name are identical.

---

## **Summary**

- Admission Controllers **enforce policies beyond RBAC**.
- **Mutating controllers modify requests**, while **validating controllers approve/reject them**.
- Some controllers **do both validation and mutation**.
- Kubernetes supports **custom admission controllers** via webhooks.
- Webhooks allow **custom validation logic** for security and compliance.


