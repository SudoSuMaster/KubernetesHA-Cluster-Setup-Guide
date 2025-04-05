# Rancher Installatie Handleiding

Deze handleiding beschrijft hoe je Rancher installeert op een Kubernetes-cluster met behulp van Helm en cert-manager.

## Vereisten
- Een werkend Kubernetes-cluster
- Helm geïnstalleerd
- Kubectl geconfigureerd voor het cluster

## Stappen

### 1. Voeg de Rancher Helm Repository toe
```bash
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
```

### 2. Maak de namespace voor Rancher aan
```bash
kubectl create namespace cattle-system
```

### 3. Installeer cert-manager
```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.2/cert-manager.crds.yaml

helm repo add jetstack https://charts.jetstack.io
helm repo update

helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.13.2
```

Controleer of de pods draaien:
```bash
kubectl get pods --namespace cert-manager
```

### 4. Installeer Rancher
```bash
helm install rancher rancher-latest/rancher \
  --namespace cattle-system \
  --set hostname=rancher.my.org \
  --set bootstrapPassword=admin
```

Controleer de status van de deployment:
```bash
kubectl -n cattle-system rollout status deploy/rancher
kubectl -n cattle-system get deploy rancher
```


✅ Toevoegen van TLS Certificaat via cert-manager
Je kunt een self-signed certificaat gebruiken voor ontwikkelomgevingen, of een Let's Encrypt certificaat voor productie.

3.1 Voeg een ClusterIssuer toe (SelfSigned voorbeeld)
Maak een bestand selfsigned-clusterissuer.yaml aan:
```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: selfsigned-clusterissuer
spec:
  selfSigned: {}
```

```bash
kubectl apply -f selfsigned-clusterissuer.yaml
```

 (Aangepast): Ingress inclusief TLS-certificaat
Pas je rancher-ingress.yaml aan zodat het certificaat automatisch wordt aangemaakt:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: rancher
  namespace: cattle-system
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    cert-manager.io/cluster-issuer: selfsigned-clusterissuer
spec:
  ingressClassName: nginx
  rules:
  - host: rancher.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: rancher
            port:
              number: 80
  tls:
  - hosts:
    - rancher.local
    secretName: rancher-tls
```
```bash
kubectl get certificate -n cattle-system
kubectl describe certificate rancher-tls -n cattle-system
kubectl get secret rancher-tls -n cattle-system
```





### 5. Maak een Ingress voor Rancher aan
Maak een bestand genaamd `rancher-ingress.yaml` met de volgende inhoud:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: rancher
  namespace: cattle-system
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  ingressClassName: nginx
  rules:
  - host: rancher.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: rancher
            port:
              number: 443
  tls:
  - hosts:
    - rancher.local
```

Pas de ingress toe:
```bash
kubectl apply -f rancher-ingress.yaml
```

Controleer of de ingress correct is aangemaakt:
```bash
kubectl get ingress -n cattle-system
```

### 6. Wijzig de service type naar NodePort
```bash
kubectl patch svc rancher -n cattle-system -p '{"spec": {"type": "NodePort"}}'
```

## Toegang tot Rancher
- Gebruik de opgegeven hostname (zoals `rancher.my.org` of `rancher.local`) om toegang te krijgen tot de Rancher UI.
### 7. find port
```bash
kubectl get svc -n cattle-system

kubectl get nodes -o wide
```

### 8. Acces with http://<node-ip>:<node-port> example: http://192.168.1.100:31000

## Opmerkingen
- Zorg ervoor dat de DNS correct geconfigureerd is voor de gebruikte hostname.
- Voor productieomgevingen wordt het aangeraden om een geldig SSL-certificaat te gebruiken.

---

Veel succes met het installeren van Rancher!

