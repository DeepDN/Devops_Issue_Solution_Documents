# Istio Installation Guide

## Prerequisites
- Kubernetes cluster
- kubectl configured

## Download Istio
```bash
# Download latest
curl -L https://istio.io/downloadIstio | sh -

# Move to PATH
cd istio-*
sudo cp bin/istioctl /usr/local/bin/

# Verify
istioctl version
```

## Install Istio
```bash
# Install with default profile
istioctl install --set values.defaultRevision=default

# Verify installation
kubectl get pods -n istio-system

# Enable sidecar injection
kubectl label namespace default istio-injection=enabled
```

## Install Addons
```bash
# Apply sample addons
kubectl apply -f samples/addons

# Check addon pods
kubectl get pods -n istio-system
```

## Access Dashboards
```bash
# Kiali dashboard
istioctl dashboard kiali

# Grafana dashboard
istioctl dashboard grafana

# Jaeger dashboard
istioctl dashboard jaeger

# Prometheus
istioctl dashboard prometheus
```

## Deploy Sample App
```bash
# Deploy bookinfo app
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml

# Create gateway
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml

# Get ingress IP
kubectl get svc istio-ingressgateway -n istio-system
```

## Configuration Profiles
```bash
# List profiles
istioctl profile list

# Install minimal profile
istioctl install --set values.defaultRevision=default --set values.pilot.env.EXTERNAL_ISTIOD=false

# Install demo profile
istioctl install --set values.defaultRevision=default --set values.pilot.traceSampling=100.0
```
