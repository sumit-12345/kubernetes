Here is a comprehensive list of **DaemonSet** interview questions, ranging from basic to advanced, along with their answers:

---

### **Basic Questions**
#### 1. **What is a DaemonSet in Kubernetes?**
**Answer**:  
A **DaemonSet** is a Kubernetes object that ensures a pod runs on all (or a subset of) nodes in a cluster. It is typically used for node-level tasks like logging, monitoring, and networking.

---

#### 2. **How does a DaemonSet differ from a Deployment?**
**Answer**:
| Feature           | **DaemonSet**                          | **Deployment**                      |
|--------------------|-----------------------------------------|--------------------------------------|
| **Purpose**        | Node-level operations                  | Application-level workloads          |
| **Pod Placement**  | One pod per node                       | Arbitrary placement across cluster   |
| **Scaling**        | Automatic based on node count          | Manual by configuring replicas       |

---

#### 3. **When would you use a DaemonSet?**
**Answer**:  
Use a DaemonSet for tasks that need to run on every node, such as:
- Log collection (e.g., Fluentd, Logstash).
- Metrics gathering (e.g., Prometheus Node Exporter).
- Networking (e.g., kube-proxy, Calico, WeaveNet).
- Security monitoring (e.g., Falco, Sysdig).

---

#### 4. **What happens if you add or remove a node in a cluster with a DaemonSet?**
**Answer**:
- **When a node is added**: A new pod is automatically created on the new node.
- **When a node is removed**: The pod on the removed node is automatically terminated.

---

#### 5. **How do you restrict DaemonSet pods to specific nodes?**
**Answer**:
You can use:
- **Node Selectors**: Match specific labels on nodes.
- **Taints and Tolerations**: Allow pods to tolerate specific conditions on nodes.
- **Node Affinity**: Define advanced placement rules for nodes.

Example:
```yaml
spec:
  template:
    spec:
      nodeSelector:
        disktype: ssd
```

---

### **Intermediate Questions**
#### 6. **Can you run multiple DaemonSets on the same node?**
**Answer**:  
Yes, you can run multiple DaemonSets on the same node. Each DaemonSet manages its own pods independently, and they can coexist as long as there are enough resources.

---

#### 7. **How do you update a DaemonSet without downtime?**
**Answer**:  
You can use the `RollingUpdate` update strategy. Kubernetes will update the pods one by one:
```yaml
spec:
  updateStrategy:
    type: RollingUpdate
```
This ensures minimal disruption during the update process.

---

#### 8. **What is the default update strategy for a DaemonSet?**
**Answer**:  
The default update strategy for a DaemonSet is **RollingUpdate**. It updates pods incrementally to ensure availability.

---

#### 9. **Can DaemonSet pods have a different configuration on each node?**
**Answer**:  
No, all pods created by a DaemonSet share the same configuration as defined in the pod template. However, environment variables or volumes like `hostPath` can allow pods to access node-specific resources.

---

#### 10. **How do you debug a DaemonSet?**
**Answer**:
- Check the DaemonSet status:
  ```bash
  kubectl describe daemonset <daemonset-name>
  ```
- Verify pod status:
  ```bash
  kubectl get pods -o wide
  ```
- Inspect logs:
  ```bash
  kubectl logs <pod-name>
  ```

---

### **Advanced Questions**
#### 11. **What are tolerations in the context of DaemonSets?**
**Answer**:  
Tolerations allow DaemonSet pods to be scheduled on nodes with specific taints. For example:
```yaml
tolerations:
- key: "node-role.kubernetes.io/master"
  operator: "Exists"
  effect: "NoSchedule"
```
This allows the pod to run on master nodes, which are typically tainted to prevent normal workloads.

---

#### 12. **Can you run a DaemonSet on a specific subset of nodes?**
**Answer**:  
Yes, you can use **nodeSelector** or **nodeAffinity**. Example:
```yaml
spec:
  template:
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: kubernetes.io/hostname
                operator: In
                values:
                - node1
                - node2
```

---

#### 13. **What are the common limitations of DaemonSets?**
**Answer**:
- Lack of fine-grained scaling (only one pod per node).
- Cannot directly use Horizontal Pod Autoscaler (HPA).
- Pods must share the same configuration.

---

#### 14. **How do you monitor a DaemonSet's performance?**
**Answer**:
- Use `kubectl` commands to check pod status:
  ```bash
  kubectl get daemonset -o wide
  ```
- Integrate with monitoring tools like Prometheus or Grafana.
- Use `kubectl top pods` to view resource usage.

---

#### 15. **How does DaemonSet handle node failure?**
**Answer**:  
If a node fails:
- The pod on the failed node is lost.
- When the node recovers, the DaemonSet recreates the pod on the node.

---

### **Hands-On/Scenario-Based Questions**
#### 16. **Write a YAML file for a DaemonSet that collects logs using Fluentd.**
**Answer**:
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: logging
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd:v1.16
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
```

---

#### 17. **How would you ensure high availability for DaemonSet pods?**
**Answer**:
- Schedule pods on nodes across multiple availability zones.
- Use anti-affinity rules to prevent pods from running on the same physical hardware.
- Monitor node health and set up alerts for failures.

---

Let me know if you'd like to explore any of these in more detail!