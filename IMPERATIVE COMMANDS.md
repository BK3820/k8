# Kubernetes Imperative Commands Cheat Sheet (CKA 2025)

## **1. Working with Namespaces**
| Task | Command |
|------|---------|
| Create a namespace | `kubectl create namespace <namespace>` |
| Delete a namespace | `kubectl delete namespace <namespace>` |
| View all namespaces | `kubectl get namespaces` |

---

## **2. Pods Management**
| Task | Command |
|------|---------|
| Create a pod | `kubectl run <pod-name> --image=<image-name>` |
| Create a pod with labels | `kubectl run <pod-name> --image=<image-name> --labels="env=prod,app=web"` |
| Expose a pod | `kubectl expose pod <pod-name> --port=<port>` |
| Delete a pod | `kubectl delete pod <pod-name>` |
| View pod details | `kubectl describe pod <pod-name>` |
| List all pods | `kubectl get pods` |
| List pods in a namespace | `kubectl get pods -n <namespace>` |
| Get pod logs | `kubectl logs <pod-name>` |
| Stream logs | `kubectl logs -f <pod-name>` |
| View logs of a specific container | `kubectl logs <pod-name> -c <container-name>` |
| Execute a command in a running pod | `kubectl exec -it <pod-name> -- /bin/sh` |

---

## **3. Deployments Management**
| Task | Command |
|------|---------|
| Create a deployment | `kubectl create deployment <deployment-name> --image=<image-name>` |
| Scale a deployment | `kubectl scale deployment <deployment-name> --replicas=<num>` |
| Update a deployment | `kubectl set image deployment/<deployment-name> <container-name>=<new-image>` |
| Rollback a deployment | `kubectl rollout undo deployment/<deployment-name>` |
| View deployment status | `kubectl rollout status deployment/<deployment-name>` |
| Delete a deployment | `kubectl delete deployment <deployment-name>` |

---

## **4. Services and Networking**
| Task | Command |
|------|---------|
| Expose a deployment as a service | `kubectl expose deployment <deployment-name> --type=NodePort --port=<port>` |
| List services | `kubectl get svc` |
| Delete a service | `kubectl delete svc <service-name>` |
| Get service details | `kubectl describe svc <service-name>` |
| Port-forward a service | `kubectl port-forward svc/<service-name> <local-port>:<service-port>` |

---

## **5. ConfigMaps and Secrets**
| Task | Command |
|------|---------|
| Create a ConfigMap from a file | `kubectl create configmap <config-name> --from-file=<file-name>` |
| Create a ConfigMap from a literal value | `kubectl create configmap <config-name> --from-literal=<key>=<value>` |
| View ConfigMaps | `kubectl get configmaps` |
| Describe a ConfigMap | `kubectl describe configmap <config-name>` |
| Create a Secret from a file | `kubectl create secret generic <secret-name> --from-file=<file-name>` |
| Create a Secret from a literal value | `kubectl create secret generic <secret-name> --from-literal=<key>=<value>` |
| View Secrets | `kubectl get secrets` |
| Describe a Secret | `kubectl describe secret <secret-name>` |

---

## **6. Node Management**
| Task | Command |
|------|---------|
| View all nodes | `kubectl get nodes` |
| View node details | `kubectl describe node <node-name>` |
| Cordon a node (mark as unschedulable) | `kubectl cordon <node-name>` |
| Uncordon a node (mark as schedulable) | `kubectl uncordon <node-name>` |
| Drain a node (evict all pods) | `kubectl drain <node-name> --ignore-daemonsets` |

---

## **7. Persistent Volumes (PV) and Persistent Volume Claims (PVC)**
| Task | Command |
|------|---------|
| List persistent volumes | `kubectl get pv` |
| List persistent volume claims | `kubectl get pvc` |
| Describe a persistent volume | `kubectl describe pv <pv-name>` |
| Describe a persistent volume claim | `kubectl describe pvc <pvc-name>` |
| Delete a persistent volume claim | `kubectl delete pvc <pvc-name>` |

---

## **8. Troubleshooting & Debugging**
| Task | Command |
|------|---------|
| Get cluster information | `kubectl cluster-info` |
| Get events in a namespace | `kubectl get events -n <namespace>` |
| Run a busybox pod for debugging | `kubectl run busybox --image=busybox -it -- /bin/sh` |
| Get logs of a failed pod | `kubectl logs <pod-name>` |
| Check why a pod is not scheduled | `kubectl describe pod <pod-name>` |
| Debug a node issue | `kubectl describe node <node-name>` |

---

## **9. Role-Based Access Control (RBAC)**
| Task | Command |
|------|---------|
| Create a role | `kubectl create role <role-name> --verb=<verbs> --resource=<resource> -n <namespace>` |
| Create a cluster role | `kubectl create clusterrole <role-name> --verb=<verbs> --resource=<resource>` |
| Bind a role to a user | `kubectl create rolebinding <binding-name> --role=<role-name> --user=<user> -n <namespace>` |
| Bind a cluster role to a user | `kubectl create clusterrolebinding <binding-name> --clusterrole=<role-name> --user=<user>` |
| List role bindings | `kubectl get rolebindings -n <namespace>` |
| List cluster role bindings | `kubectl get clusterrolebindings` |

---

## **10. Helm (If Required in the Exam)**
| Task | Command |
|------|---------|
| Install Helm | `curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash` |
| Add a Helm repository | `helm repo add <repo-name> <repo-url>` |
| Install a Helm chart | `helm install <release-name> <chart-name>` |
| List installed Helm releases | `helm list` |
| Upgrade a Helm release | `helm upgrade <release-name> <chart-name>` |
| Uninstall a Helm release | `helm uninstall <release-name>` |

---

