# Kubernetes Ingress Setup

Deze gids beschrijft hoe je **NGINX Ingress** installeert en een demo-applicatie blootstelt via een **Ingress resource** in Kubernetes.

## 📌 Installatie van Helm
Helm is nodig om de Ingress Controller te installeren.
```sh
curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

## 📌 Installatie van NGINX Ingress Controller
Voeg de repository toe en installeer de Ingress Controller.
```sh
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install my-ingress ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace
```

## 📌 Controleer of de Ingress Controller draait
```sh
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

## 📌 Maak een demo-applicatie
Creëer een Nginx-deployment en een ClusterIP-service.
```sh
kubectl create deployment demo --image=nginx
kubectl expose deployment demo --port=80 --target-port=80 --type=ClusterIP
```

## 📌 Maak een Ingress Resource
Maak een bestand **demo-ingress.yaml** met de volgende inhoud:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: demo.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: demo
            port:
              number: 80
```

Pas de Ingress resource toe:
```sh
kubectl apply -f demo-ingress.yaml
```

## 📌 Maak de service extern bereikbaar (optioneel)
Verander de service van **ClusterIP** naar **NodePort**:
```sh
kubectl patch svc demo -p '{"spec": {"type": "NodePort"}}'
```

## 📌 Controleer de services
```sh
kubectl get svc
```

## 📌 Toegang tot de applicatie
1. **Als je een Ingress gebruikt**, voeg het volgende toe aan je **hosts-bestand** (`/etc/hosts` op Linux/macOS of `C:\Windows\System32\drivers\etc\hosts` op Windows):
   ```
   <NODE-IP>  demo.local
   ```
2. Open je browser en ga naar:
   ```
   http://demo.local
   ```

✅ **Yippie** 

