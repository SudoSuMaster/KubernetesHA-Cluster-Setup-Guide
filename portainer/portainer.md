# Installatie en Configuratie van Portainer op Kubernetes

Dit document beschrijft de stappen om Portainer in een Kubernetes-cluster te installeren en toegankelijk te maken via een browser. Daarnaast wordt uitgelegd wat Persistent Volumes (PV) en Persistent Volume Claims (PVC) zijn.

## 📌 Voorwaarden
Zorg ervoor dat je aan de volgende voorwaarden voldoet voordat je begint:
- Een werkend Kubernetes-cluster (minstens één master- en één worker-node)
- Helm geïnstalleerd op je systeem
- Toegang tot je cluster via `kubectl`

## 🛠 Stap 1: Controleer de StorageClass
Portainer vereist een **Persistent Volume (PV)** om zijn data op te slaan. Kubernetes gebruikt hiervoor een **StorageClass**.

Om te controleren of er een standaard StorageClass beschikbaar is, voer je uit:
```sh
kubectl get sc
```
Je zou een StorageClass moeten zien met `(default)` erachter. Als er geen standaard StorageClass is, kun je er een aanmaken met het volgende commando:
```sh
kubectl patch storageclass <storage-class-name> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
Vervang `<storage-class-name>` door de naam van je StorageClass.

## 📂 Stap 2: Wat zijn PV en PVC?
- **Persistent Volume (PV):** Een opslagbron in Kubernetes die buiten de levenscyclus van een pod blijft bestaan.
- **Persistent Volume Claim (PVC):** Een aanvraag voor opslag door een pod. De PVC zoekt een passend PV binnen de StorageClass en bindt deze aan de pod.

Je kunt beschikbare PV's en PVC's controleren met:
```sh
kubectl get pv
kubectl get pvc
```

## 🚀 Stap 3: Installeren van Portainer met Helm
Voer de volgende commando's uit om Portainer te installeren:
```sh
helm repo add portainer https://portainer.github.io/k8s/
helm repo update
helm install --create-namespace -n portainer portainer portainer/portainer \
    --set enterpriseEdition.enabled=true \
    --set enterpriseEdition.image.tag=lts \
    --set tls.force=true
```
Dit installeert Portainer in de `portainer` namespace.

## 🔍 Stap 4: Controleer de installatie
Controleer of Portainer correct is geïnstalleerd:
```sh
kubectl get pods -n portainer
kubectl get svc -n portainer
```
Je zou een service moeten zien met een NodePort, bijvoorbeeld:
```
NAME        TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
portainer   NodePort   10.96.171.0   <none>        9000:30777/TCP,9443:30779/TCP  10m
```

## 🌍 Stap 5: Toegang tot Portainer
Gebruik de volgende commando's om het IP-adres en de poort van de Portainer UI te vinden:
```sh
export NODE_IP=$(kubectl get nodes -o jsonpath="{.items[0].status.addresses[0].address}")
export NODE_PORT=$(kubectl get --namespace portainer -o jsonpath="{.spec.ports[1].nodePort}" services portainer)
echo "Portainer URL: https://$NODE_IP:$NODE_PORT"
```
Open de URL in je browser:
```
https://<NODE_IP>:<NODE_PORT>
```
Bijvoorbeeld: `https://192.168.2.11:30779`

## ✅ Stap 6: Inloggen en Configureren
1. Open de URL in een browser.
2. Stel een beheerderswachtwoord in.
3. Kies "Kubernetes" als beheerde omgeving.
4. Configureer de opslag en andere instellingen naar wens.

## 📦 Extra: Persistent Storage instellen met PVC
Om ervoor te zorgen dat Portainer zijn data behoudt bij herstarts, voeg een PersistentVolumeClaim (PVC) toe:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: portainer-pvc
  namespace: portainer
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```
Toepassen met:
```sh
kubectl apply -f portainer-pvc.yaml
```

## 🎉 Conclusie
Je hebt nu Portainer geïnstalleerd en toegankelijk gemaakt binnen je Kubernetes-cluster! ✅

### ℹ️ Hulp nodig?
Voer `kubectl logs -n portainer <portainer-pod-name>` uit om logs te bekijken als je problemen hebt.

---

Dit document biedt een volledige werkinstructie om Portainer correct in te stellen en toegankelijk te maken. Laat het weten als je extra uitleg of uitbreidingen nodig hebt! 🚀

