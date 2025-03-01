# Kubernetes Deployments and Services Cheat Sheet

## **1. Deployments**

### **Creating a Deployment**
| Task | Command |
|------|---------|
| Create a deployment | <span style="color:blue;">`kubectl create deployment <deployment-name> --image=<image-name>`</span> |
| Create a deployment with replicas | <span style="color:blue;">`kubectl create deployment <deployment-name> --image=<image-name> --replicas=<num>`</span> |
| Apply a deployment YAML file | <span style="color:blue;">`kubectl apply -f <deployment.yaml>`</span> |
| List deployments | <span style="color:blue;">`kubectl get deployments`</span> |
| Describe a deployment | <span style="color:blue;">`kubectl describe deployment <deployment-name>`</span> |

### **Updating a Deployment**
| Task | Command |
|------|---------|
| Update a deployment image | <span style="color:blue;">`kubectl set image deployment/<deployment-name> <container-name>=<new-image>`</span> |
| View rollout status | <span style="color:blue;">`kubectl rollout status deployment/<deployment-name>`</span> |
| Rollback a deployment | <span style="color:blue;">`kubectl rollout undo deployment/<deployment-name>`</span> |

### **Scaling a Deployment**
| Task | Command |
|------|---------|
| Scale a deployment | <span style="color:blue;">`kubectl scale deployment <deployment-name> --replicas=<num>`</span> |
| View updated replicas | <span style="color:blue;">`kubectl get deployments`</span> |

### **Deleting a Deployment**
| Task | Command |
|------|---------|
| Delete a deployment | <span style="color:blue;">`kubectl delete deployment <deployment-name>`</span> |

---

## **2. Services (SVCs)**

### **Creating a Service**
| Task | Command |
|------|---------|
| Expose a deployment as a service | <span style="color:blue;">`kubectl expose deployment <deployment-name> --type=NodePort --port=<port>`</span> |
| Create a service YAML file | <span style="color:blue;">`kubectl apply -f <service.yaml>`</span> |
| List services | <span style="color:blue;">`kubectl get svc`</span> |
| Describe a service | <span style="color:blue;">`kubectl describe svc <service-name>`</span> |

### **Types of Services**
| Service Type | Description |
|-------------|------------|
| ClusterIP | Exposes service only inside the cluster (default) |
| NodePort | Exposes service on a static port on each node |
| LoadBalancer | Creates an external load balancer for the service |
| ExternalName | Maps a service to an external DNS name |

### **Managing a Service**
| Task | Command |
|------|---------|
| Get service details | <span style="color:blue;">`kubectl get svc <service-name>`</span> |
| Delete a service | <span style="color:blue;">`kubectl delete svc <service-name>`</span> |
| Port-forward a service | <span style="color:blue;">`kubectl port-forward svc/<service-name> <local-port>:<service-port>`</span> |

---

## **3. Example YAML for Deployment and Service**

### **Deployment YAML Example**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx
        ports:
        - containerPort: 80
```

### **NodePort Service YAML Example**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

### **ClusterIP Service YAML Example**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-clusterip-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```



