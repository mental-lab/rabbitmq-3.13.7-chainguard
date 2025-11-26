# RabbitMQ 3.13.7 with Chainguard Images

Modified Helm chart for deploying RabbitMQ 3.13.7 using Chainguard's secure container images.

## Option 1: Use This Modified Chart

```bash
# Clone this repository
git clone https://github.com/mental-lab/rabbitmq-3.13.7-chainguard.git
cd rabbitmq-3.13.7-chainguard

# Install RabbitMQ
helm install rabbitmq . --namespace rabbitmq --create-namespace

# Get credentials
echo "Username: user"
echo "Password: $(kubectl get secret --namespace rabbitmq rabbitmq -o jsonpath="{.data.rabbitmq-password}" | base64 -d)"

# Access management UI
kubectl port-forward --namespace rabbitmq svc/rabbitmq 15672:15672
# Visit: http://127.0.0.1:15672
```

## Option 2: Modify Original Chainguard Chart

If you prefer to modify the original Chainguard chart yourself:

```bash
# Pull original Chainguard chart
helm pull oci://cgr.dev/iamguarded-charts/rabbitmq --version 16.0.14 --untar
cd rabbitmq

# Make these changes to Chart.yaml:
# - appVersion: "3.13.7"
# - version: "14.7.0"

# Make these changes to values.yaml:
# - image.repository: "cgr.dev/chainguard/rabbitmq"
# - image.tag: "3.13.7"
# - persistence.mountPath: "/var/lib/rabbitmq"
# - containerSecurityContext.runAsUser: 999
# - containerSecurityContext.runAsGroup: 999
# - containerSecurityContext.readOnlyRootFilesystem: false
# - initContainers: []

# Comment out prepare-plugins-dir init container in templates/statefulset.yaml

# Install
helm install rabbitmq . --namespace rabbitmq --create-namespace
```

## What Changed

- Updated to RabbitMQ 3.13.7 image: `cgr.dev/chainguard/rabbitmq:3.13.7`
- Fixed filesystem paths for standard RabbitMQ structure
- Disabled incompatible init containers
- Adjusted security context for proper permissions
