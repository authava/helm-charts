# AuthGate Helm Charts

This repository contains Helm charts for deploying AuthGate and its components to Kubernetes.

## Available Charts

- [authgate](./authgate/README.md): Deploys the complete AuthGate stack, including the main service, admin UI, and optional dependencies.

## Usage

### Add the Helm Repository

```bash
helm repo add authgate https://authava.github.io/helm-charts
helm repo update
```

### Install a Chart

```bash
# Install with default values
helm install authgate authgate/authgate

# Install with custom values file
helm install authgate authgate/authgate -f values.yaml
```

## Development

### Testing Charts Locally

To test a chart locally:

```bash
# Lint the chart
helm lint ./authgate

# Install dependencies
helm dependency update ./authgate

# Render the templates
helm template ./authgate

# Install the chart in a test namespace
helm install authgate ./authgate --namespace test --create-namespace
```

## License

MIT