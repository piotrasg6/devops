# Helm Command Cheat Sheet

## General Information
```bash
# Check Helm version
helm version

# Get help for any command
helm help [command]

# Show helm environment information
helm env
```

## Repository Management
```bash
# Add a repository
helm repo add [repo-name] [url]

# Update repositories (fetch latest charts)
helm repo update

# List configured repositories
helm repo list

# Remove a repository
helm repo remove [repo-name]

# Search repositories for a chart
helm search repo [keyword]

# Search Helm Hub
helm search hub [keyword]
```

## Chart Management
```bash
# Create a new chart
helm create [chart-name]

# Package a chart into a chart archive
helm package [chart-path]

# Verify a chart meets requirements
helm lint [chart-path]

# Pull a chart from a repository
helm pull [repo/chart]
helm pull [repo/chart] --untar  # Extract after download

# Show chart information
helm show chart [chart]

# Show all information (chart, values, readme)
helm show all [chart]

# Show customizable values
helm show values [chart]

# Download chart dependencies
helm dependency update [chart-path]

# List chart dependencies
helm dependency list [chart-path]
```

## Release Management
```bash
# Install a chart
helm install [release-name] [chart] 

# Install with custom values file
helm install [release-name] [chart] --values [values-file]

# Install with overriding specific values
helm install [release-name] [chart] --set key1=value1,key2=value2

# Install or upgrade if release exists
helm upgrade --install [release-name] [chart]

# Upgrade a release
helm upgrade [release-name] [chart]

# Rollback a release to a previous revision
helm rollback [release-name] [revision]

# List releases
helm list
helm ls                           # Alias for list
helm list --all-namespaces        # List across all namespaces
helm list --namespace [namespace] # List in specific namespace
helm list --uninstalled           # Show uninstalled releases

# Get release information
helm status [release-name]

# Uninstall a release
helm uninstall [release-name]

# Get release history
helm history [release-name]

# Get release values
helm get values [release-name]

# Get release manifest (applied YAML)
helm get manifest [release-name]

# Get release notes
helm get notes [release-name]

# Get all information about a release
helm get all [release-name]
```

## Template & Debugging
```bash
# Render chart templates locally
helm template [release-name] [chart]

# Render with custom values
helm template [release-name] [chart] --values [values-file]

# Validate chart renders correctly
helm install [release-name] [chart] --dry-run

# Debug chart during installation
helm install [release-name] [chart] --debug

# Show computed values during install
helm install [release-name] [chart] --dry-run --debug
```

## Plugin Management
```bash
# List installed plugins
helm plugin list

# Install a plugin
helm plugin install [url/local-path]

# Update plugins
helm plugin update [plugin-name]

# Uninstall a plugin
helm plugin uninstall [plugin-name]
```

## Common Useful Plugins
```bash
# Helm Diff plugin (compare releases/revisions)
helm diff upgrade [release-name] [chart]
helm diff rollback [release-name] [revision]

# Helm Secrets plugin (manage secrets)
helm secrets enc [file]
helm secrets dec [file]
helm secrets install [release-name] [chart] -f [encrypted-values.yaml]

# Helm S3 plugin (S3 chart repository)
helm s3 init s3://[bucket-name]/charts
helm repo add [repo-name] s3://[bucket-name]/charts
```

## Useful Flags
```bash
# Common flags for many commands:
--namespace [namespace]  # Specify namespace
--create-namespace       # Create namespace if it doesn't exist
--kube-context [context] # Specify Kubernetes context
--kubeconfig [path]      # Specify kubeconfig file
--atomic                 # Roll back on failure during upgrade/install
--cleanup-on-fail        # Clean up new resources on failed install/upgrade
--timeout [time]         # Time to wait for operation (e.g. 5m0s)
--wait                   # Wait for resources to be ready before marking release as successful
--debug                  # Enable verbose output
```

## Advanced Usage
```bash
# Work with multiple values files (processed from left to right)
helm install [release-name] [chart] -f values1.yaml -f values2.yaml

# Install from URL
helm install [release-name] https://example.com/charts/mychart-1.0.0.tgz

# Specify chart version
helm install [release-name] [repo/chart] --version [version]

# Generate chart documentation
helm-docs  # Requires helm-docs plugin

# Force resource updates during upgrade
helm upgrade [release-name] [chart] --force

# Skip CRD installation
helm install [release-name] [chart] --skip-crds

# Set release name from chart name
helm install [chart] --generate-name

# Install release in another namespace
helm install [release-name] [chart] --namespace [namespace]

# Show chart download URL without downloading
helm repo add [repo-name] [url] --no-update
```

## OCI Registry Support (Helm 3)
```bash
# Log in to OCI-based registry
helm registry login [registry]

# Log out from OCI-based registry
helm registry logout [registry]

# Pull chart from OCI registry
helm pull oci://[registry]/[chart]

# Push chart to OCI registry
helm push [chart-package] oci://[registry]/
```

## Tips and Tricks
- Use `--atomic` flag for installations to automatically roll back on failure
- Create aliases like `alias h=helm` for faster typing
- Use `helm completion` to enable shell autocompletion
- Structure large deployments using umbrella charts
- Use `helm get values -a [release-name]` to view all current values (including defaults)
- When debugging templating issues, use `helm template` with `--debug` flag
- For CI/CD pipelines, use `--wait` flag to ensure resources are ready
- Use `-o yaml|json` with many commands to get machine-readable output
- Use semver ranges in dependencies (e.g., `>=1.2.3 <2.0.0`)

## Common Chart Repositories
```bash
# Add common repositories
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add stable https://charts.helm.sh/stable
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo add jetstack https://charts.jetstack.io
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

## Helm 2 to Helm 3 Migration
```bash
# Convert Helm 2 release to Helm 3
helm 2to3 convert [release-name]

# Move Helm 2 configuration
helm 2to3 move config

# Clean up Helm 2 data
helm 2to3 cleanup
```
