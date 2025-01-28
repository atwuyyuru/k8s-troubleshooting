# Kubernetes Most Common Error Encounters

Kubernetes is a powerful and complex system, so there are several common errors that users might encounter. Here’s a rundown of typical Kubernetes errors and some troubleshooting methods:

## 1. Pods Not Starting / Container Crash Loops

**Error**: Pod is stuck in the `CrashLoopBackOff` or `Pending` state.

**Troubleshooting**:

- **Check Pod logs**:
    ```bash
    kubectl logs <pod-name> -n <namespace>
    ```

- **Describe Pod**:
    ```bash
    kubectl describe pod <pod-name> -n <namespace>
    ```
    This will show events related to the pod, such as failed scheduling or image pull issues.

- **Check for resource limits**: Ensure the pod is not exceeding memory or CPU limits.

- **Check container images**: Verify that the image exists in the container registry and that the container is configured correctly.

- **Check node resources**: Sometimes pods won't start due to insufficient resources. Check node resource availability:
    ```bash
    kubectl describe node <node-name>
    ```

## 2. Pod is Stuck in Pending State

**Error**: The pod is scheduled but not running.

**Troubleshooting**:

- **Check Scheduler logs**: It could be a scheduling issue (e.g., not enough resources in the cluster).
- **Check node status**: Ensure that the nodes are not over-utilized.
    ```bash
    kubectl describe nodes
    ```

- **Check PersistentVolume (PV) and PersistentVolumeClaim (PVC)**: If your pod is using PVCs, ensure the volume is correctly bound.

- **Check network policies**: Verify if network policies are preventing pod communication.

## 3. Service Not Accessible / Pod Not Reachable

**Error**: Applications in a pod are not accessible via the Kubernetes service.

**Troubleshooting**:

- **Check Service configuration**: Ensure the service is properly exposing the pod’s ports.
    ```bash
    kubectl describe svc <service-name> -n <namespace>
    ```

- **Check Pod’s network connectivity**: Make sure pods are running and have correct IPs.
    ```bash
    kubectl get pod -o wide
    ```

- **Check ingress/egress rules**: Review network policies or any firewall rules blocking communication.

- **Check DNS resolution**: Kubernetes relies on DNS, so ensure the DNS resolution is working.
    ```bash
    kubectl exec -it <pod-name> -- nslookup <service-name>
    ```

## 4. Image Pull Error

**Error**: Kubernetes fails to pull the container image, leading to a pod being stuck in Pending.

**Troubleshooting**:

- **Check Image Name and Tag**: Ensure the image name and tag are correct in the deployment.
- **Check Image Pull Secrets**: If the image is hosted on a private registry, ensure the correct `imagePullSecrets` are configured in your deployment.
- **Check Registry Authentication**: Make sure Kubernetes can authenticate to the container registry if needed.
    ```bash
    kubectl describe pod <pod-name> -n <namespace>
    ```

## 5. Resource Limit/Quota Errors

**Error**: Kubernetes is unable to allocate resources (memory, CPU) to the pod.

**Troubleshooting**:

- **Check Resource Requests and Limits**: Ensure the pod is not over-committing resources.
    ```bash
    kubectl describe pod <pod-name>
    ```

- **Check Resource Quotas**: Ensure the namespace has not exceeded resource quotas. Check for resource usage:
    ```bash
    kubectl describe quota -n <namespace>
    ```

## 6. Node Failures / Node Not Ready

**Error**: The node goes into a NotReady state.

**Troubleshooting**:

- **Check Node status**: If a node is not ready, you can inspect it:
    ```bash
    kubectl describe node <node-name>
    ```

- **Check kubelet logs on the node**: Look at the logs for issues like connectivity problems or misconfigurations.
- **Check resource utilization**: High resource usage (memory, CPU, disk) can cause node instability.

## 7. kubectl Not Working / Command Fails

**Error**: kubectl commands fail or return errors.

**Troubleshooting**:

- **Check Kubeconfig file**: Make sure the kubeconfig file is correctly set up and points to the right cluster.
    ```bash
    echo $KUBECONFIG
    kubectl config view
    ```

- **Check Kubernetes Cluster Health**:
    ```bash
    kubectl cluster-info
    ```

- **Ensure Kubelet is Running**: The kubelet on the nodes might be down.
    ```bash
    systemctl status kubelet
    ```

## 8. Ingress Issues

**Error**: Ingress routes are not working or show 404s.

**Troubleshooting**:

- **Check Ingress resource**: Ensure the Ingress resource is defined correctly and points to the correct service.
    ```bash
    kubectl describe ingress <ingress-name> -n <namespace>
    ```

- **Check Controller logs**: If you’re using an Ingress controller (e.g., NGINX), inspect its logs to find any issues.
    ```bash
    kubectl logs <ingress-controller-pod> -n <namespace>
    ```

## 9. RBAC (Role-Based Access Control) Errors

**Error**: Users or service accounts are unable to perform certain actions.

**Troubleshooting**:

- **Check RBAC Permissions**: Ensure that the appropriate roles and role bindings are created for the user or service account.
    ```bash
    kubectl describe rolebinding <rolebinding-name> -n <namespace>
    ```

- **Check Service Account**: Make sure the service account is being used correctly in your resources (e.g., in deployments or pods).

## 10. High Latency or Performance Issues

**Error**: The application is slow or unresponsive.

**Troubleshooting**:

- **Check Resource Usage**: Inspect pod, node, and cluster-level metrics to see if resources are under heavy load.
    ```bash
    kubectl top pod
    kubectl top node
    ```

- **Check for Network Issues**: Latency can be network-related, so check for network performance or issues with ingress controllers.

- **Check Pod Logs**: High latency might also be related to application-level issues, so inspecting logs is critical.

## General Best Practices:

- Use Liveness and Readiness Probes: These can help Kubernetes determine if your containers are healthy and properly running.
- Monitor with Prometheus/Grafana: Set up monitoring to detect issues early.
- Use `kubectl get events` to check for events related to the cluster resources.

By systematically working through these common error scenarios, you should be able to isolate and resolve most Kubernetes issues.
