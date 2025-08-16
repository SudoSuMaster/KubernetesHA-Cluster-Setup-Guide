# Rancher Installatie Handleiding

Deze handleiding beschrijft hoe je Rancher installeert op een Kubernetes-cluster met behulp van Helm 
## Vereisten
- Een werkend Kubernetes-cluster
- Helm geïnstalleerd https://helm.sh/docs/intro/install/
- Kubectl geconfigureerd voor het cluster

## Stappen

### Step 1. Voeg de Rancher Helm Repository toe
```bash
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
helm repo update
```

### Step 2. Maak de namespace voor Rancher aan
```bash
kubectl create namespace cattle-system
```

### Step 3. Installeer rancher
```bash
helm install rancher rancher-latest/rancher \
  --namespace cattle-system \
  --set replicas=1 \
  --set ingress.enabled=false \
  --set bootstrapPassword=admin
```

### Step 4: Check Rancher Service
```bash
kubectl get svc -n cattle-system
```

### Step 5: Convert Rancher Service to NodePort
```bash
kubectl patch svc rancher -n cattle-system -p '{"spec": {"type": "NodePort"}}'
kubectl get svc -n cattle-system

```

### Step 6. Toegang tot Rancher in de browser

Open: https://<LB_IP>:<HTTPS_NODEPORT>
Voorbeeld: https://192.168.2.21:32542
Let op: de browser kan een waarschuwing geven over een self-signed certificaat.

Step 7. Haal het bootstrap-wachtwoord op

```bash
kubectl get secret --namespace cattle-system bootstrap-secret -o go-template='{{.data.bootstrapPassword|base64decode}}{{ "\n" }}'
```

