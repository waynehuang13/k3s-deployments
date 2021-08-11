# Setup homelab services - NFS/MetalLB/cert-manager/kubed/traefik/keycloak
### NFS
https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner
```
helm repo add nfs-subdir-external-provisioner \
    https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
helm install nfs-subdir-external-provisioner \
    nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
    -f nfs-provisioner/lab-values.yaml
```
or
```
kubectl create -f nfs-provisioner/nfs-deployment.yaml
```
### MetalLB
#### https://metallb.universe.tf/installation/
```
helm repo add metallb https://metallb.github.io/metallb
helm install metallb metallb/metallb -f metallb/lab-values.yaml \
    -n metallb-system --create-namespace
```
### cert-manager
https://cert-manager.io/docs/installation/helm/
```
helm repo add jetstack https://charts.jetstack.io/
helm install cert-manager jetstack/cert-manager \
    --namespace cert-manager \
    --create-namespace \
    --version v1.4.3 \
    --set installCRDs=true
```
### appscode/kubed
https://appscode.com/products/kubed/v0.12.0/setup/install/
https://appscode.com/products/kubed/v0.12.0/guides/config-syncer/intra-cluster/

Sync secret/configmap objects between namespaces
```
helm install kubed appscode/kubed \
    --version v0.12.0 \
    --namespace kube-system

# annotate certificate secret
 kubectl annotate secret kairos-ai-prod-tls \
    -n cert-manager kubed.appscode.com/sync="app=kubed"
```
```bash
kubectl label namespace traefik app=kubed
```
### Traefik
https://doc.traefik.io/traefik/getting-started/install-traefik/#use-the-helm-chart
```
helm repo add traefik https://helm.traefik.io/traefik
helm install traefik traefik/traefik \
    -f traefik/lab-values.yaml \
    -n traefik --create-namespace

# Exposing traefik dashboard
kubectl apply -f traefik/traefik-dashboard.yaml

# Basic Auth:
kubectl apply -f traefik/basic-auth.yaml
```
### Keycloak
```
helm repo add codecentric https://codecentric.github.io/helm-charts
helm install keycloak codecentric/keycloak \
    -n keycloak --create-namespace
```