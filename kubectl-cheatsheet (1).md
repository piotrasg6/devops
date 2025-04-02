# Kubectl Command Cheat Sheet

## Cluster Information
```bash
# Display cluster information
kubectl cluster-info

# Check API resources available in the cluster
kubectl api-resources

# View kubectl configuration
kubectl config view

# Check the version of kubectl and the Kubernetes server
kubectl version
```

## Context and Namespace Management
```bash
# List available contexts
kubectl config get-contexts

# Switch to a different context
kubectl config use-context <context-name>

# Create a new namespace
kubectl create namespace <namespace-name>

# Set the default namespace for kubectl commands
kubectl config set-context --current --namespace=<namespace-name>

# List all namespaces
kubectl get namespaces
```

## Resource Management

### Viewing Resources
```bash
# List all resources in current namespace
kubectl get all

# List specific resource type (pods, services, deployments, etc.)
kubectl get <resource-type>

# List resources with more details
kubectl get <resource-type> -o wide

# List resources in all namespaces
kubectl get <resource-type> --all-namespaces

# Get detailed information about a specific resource
kubectl describe <resource-type> <resource-name>

# List resources with custom columns
kubectl get <resource-type> -o custom-columns=NAME:.metadata.name,STATUS:.status.phase

# Output resource in YAML format
kubectl get <resource-type> <resource-name> -o yaml
```

### Creating and Updating Resources
```bash
# Create resources from a YAML file
kubectl apply -f <filename.yaml>

# Create resources from a directory of YAML files
kubectl apply -f <directory>/

# Create resources from a URL
kubectl apply -f https://raw.githubusercontent.com/example/file.yaml

# Edit a resource directly
kubectl edit <resource-type> <resource-name>

# Update a specific field using patch
kubectl patch <resource-type> <resource-name> -p '{"spec":{"replicas":3}}'
```

### Deleting Resources
```bash
# Delete a specific resource
kubectl delete <resource-type> <resource-name>

# Delete all resources of a type
kubectl delete <resource-type> --all

# Delete resources using a YAML file
kubectl delete -f <filename.yaml>

# Force delete a pod without waiting for graceful termination
kubectl delete pod <pod-name> --grace-period=0 --force
```

## Pod Operations
```bash
# List all pods
kubectl get pods

# Get pod logs
kubectl logs <pod-name>

# Get logs from a specific container in a pod
kubectl logs <pod-name> -c <container-name>

# Stream pod logs with follow option
kubectl logs <pod-name> -f

# Execute a command in a pod
kubectl exec <pod-name> -- <command>

# Get an interactive shell in a pod
kubectl exec -it <pod-name> -- /bin/bash

# Copy files to/from a pod
kubectl cp <pod-name>:/path/to/file /local/path  # from pod to local
kubectl cp /local/path <pod-name>:/path/to/file  # from local to pod

# Get pod resource usage
kubectl top pod <pod-name>
```

## Deployment Operations
```bash
# Create a deployment
kubectl create deployment <name> --image=<image-name>

# Scale a deployment
kubectl scale deployment <name> --replicas=<count>

# Rollout status
kubectl rollout status deployment <name>

# Rollout history
kubectl rollout history deployment <name>

# Rollback to previous revision
kubectl rollout undo deployment <name>

# Rollback to specific revision
kubectl rollout undo deployment <name> --to-revision=<revision-number>

# Pause/resume a rollout
kubectl rollout pause deployment <name>
kubectl rollout resume deployment <name>

# Restart a deployment (rolling restart)
kubectl rollout restart deployment <name>
```

## Service Operations
```bash
# Expose a deployment as a service
kubectl expose deployment <name> --port=<port> --target-port=<target-port> --type=<type>

# Forward a local port to a pod
kubectl port-forward <pod-name> <local-port>:<pod-port>

# Forward to a service
kubectl port-forward svc/<service-name> <local-port>:<service-port>
```

## ConfigMaps and Secrets
```bash
# Create a ConfigMap
kubectl create configmap <name> --from-file=<path/to/file>
kubectl create configmap <name> --from-literal=key1=value1 --from-literal=key2=value2

# Create a Secret
kubectl create secret generic <name> --from-file=<path/to/file>
kubectl create secret generic <name> --from-literal=key1=value1

# Create TLS secret
kubectl create secret tls <name> --cert=<path/to/cert> --key=<path/to/key>
```

## Troubleshooting
```bash
# Debug with a temporary pod
kubectl run debug-pod --rm -it --image=busybox -- /bin/sh

# Check event logs
kubectl get events --sort-by=.metadata.creationTimestamp

# Show cluster resource usage
kubectl top nodes
kubectl top pods

# Explain resource fields and structure
kubectl explain <resource-type>
kubectl explain <resource-type>.spec

# Validate YAML without applying
kubectl apply -f <filename.yaml> --dry-run=client

# Check pod connectivity (requires debug container)
kubectl debug -it <pod-name> --image=busybox -- ping <target-ip>
```

## Labels and Annotations
```bash
# Add a label to a resource
kubectl label <resource-type> <resource-name> key=value

# Remove a label
kubectl label <resource-type> <resource-name> key-

# List resources by label selector
kubectl get <resource-type> -l key=value,another-key=another-value

# Add annotation
kubectl annotate <resource-type> <resource-name> key=value
```

## Advanced Commands
```bash
# View resource allocation/utilization
kubectl describe node <node-name> | grep -A 5 "Allocated resources"

# List pods that would be affected by draining a node
kubectl drain <node-name> --ignore-daemonsets --dry-run

# Scale to zero (temporarily)
kubectl scale deployment <name> --replicas=0

# Start a proxy to the Kubernetes API
kubectl proxy --port=8080

# Convert between API versions of YAML manifests
kubectl convert -f <old-file.yaml> --output-version <new-api-version>

# Apply configuration with a specific field manager name
kubectl apply -f <file> --field-manager=<name>

# Diff between running configuration and local file
kubectl diff -f <file.yaml>
```

## Tips and Tricks
- Use `kubectl completion` to enable shell autocompletion
- Create aliases for common commands: `alias k=kubectl`
- Use `kubectl config current-context` to see which cluster you're connected to
- Use JSONPath for custom output: `kubectl get pods -o jsonpath='{.items[*].metadata.name}'`
- Install kubectl plugins with krew: `kubectl krew install <plugin-name>`
- Use `--watch` or `-w` flag to monitor resources in real-time

## Common Resource Types
- `pods` or `po`
- `services` or `svc`
- `deployments` or `deploy`
- `replicasets` or `rs`
- `statefulsets` or `sts`
- `daemonsets` or `ds`
- `configmaps` or `cm`
- `secrets`
- `ingress` or `ing`
- `persistentvolumes` or `pv`
- `persistentvolumeclaims` or `pvc`
- `nodes` or `no`
- `namespaces` or `ns`
