# 🧱 WI — Kennismaken met Kubernetes (na het opzetten van je eerste cluster)

## 🎯 Doel
Je hebt een Kubernetes-cluster opgezet (bijvoorbeeld met `kubeadm` of Minikube).  
In deze oefening leer je:
- Hoe je de **status van het cluster** bekijkt  
- Hoe je een **simpele app** start (deployment)  
- En **waarom Kubernetes handiger is dan oude IT-systemen** met losse VM’s of servers.

---

## 🧩 Wat heb je nodig
- Een **werkend cluster** (minimaal 1 control-plane + 1 worker)
- Toegang tot de terminal met `kubectl`
- Basiskennis Linux (cd, ls, sudo)

---

## 🪄 Stap 1 — Check je cluster
Controleer of alles goed draait:

```bash
kubectl get nodes
✅ Je zou iets moeten zien als:

pgsql
```bash
NAME     STATUS   ROLES           AGE   VERSION
cp-1     Ready    control-plane   5m    v1.34.0
wkr-1    Ready    <none>          3m    v1.34.0
```
👉 Dit betekent dat je cluster online en klaar voor gebruik is.

🚀 Stap 2 — Maak een simpele app (Deployment)
We gaan een kleine webserver starten. Typ:

```bash

kubectl create deployment hello-app --image=nginx
```
Check of hij draait:

```bash
kubectl get pods
```
Als je “Running” ziet, is alles goed! 🎉

🌐 Stap 3 — Maak de app bereikbaar
Maak de webserver bereikbaar in het cluster:

```bash
Copy code
kubectl expose deployment hello-app --type=NodePort --port=80
```
Controleer de service:
```bash
kubectl get svc
```
Je ziet iets als:

pgsql
```bash
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
hello-app    NodePort    10.99.14.12    <none>        80:30080/TCP   1m
```
👉 Dat betekent: je app draait op poort 30080.
Ga in je browser naar:

cpp
```bash
http://<ip-van-je-node>:30080
```
en je ziet de nginx startpagina!

🔁 Stap 4 — Schaal je app (meer pods)
Probeer nu de app te vermenigvuldigen:

```bash
kubectl scale deployment hello-app --replicas=3
kubectl get pods
```
Je ziet nu 3 pods draaien ✨
Kubernetes regelt automatisch dat deze pods op verschillende nodes kunnen komen — zonder dat jij iets hoeft te configureren.

🧹 Stap 5 — Opruimen
```bash
kubectl delete svc hello-app
kubectl delete deployment hello-app
```

Cluster is weer schoon ✅

💡 Waarom Kubernetes ?
oude IT vs K8s
Oude manier (VM’s / losse servers)	--> Nieuwe manier (Kubernetes)
Elke app draait op zijn eigen server of VM --> Meerdere apps delen veilig hetzelfde cluster
Veel handmatig werk bij updates	 --> Automatisch bijwerken via deployments
Problemen = handmatig herstarten	 --> Pods herstellen zichzelf automatisch
Moeilijk schalen bij drukte  -->	Snel schalen met één commando
Configuratie verspreid over meerdere systemen -->	Alles staat centraal en declaratief (YAML)
Minder efficiënt gebruik van resources	 --> Containers delen CPU/RAM slim

Kortom:

Kubernetes laat je meerdere kleine apps slim beheren, in plaats van één grote logge server per app.

🧠 Samenvatting
kubectl get nodes → Checkt of je cluster leeft

kubectl create deployment → Start een app

kubectl expose → Maakt de app bereikbaar

kubectl scale → Meer of minder pods

Kubernetes = sneller, veiliger en schaalbaar

🏁 Extra uitdaging
Probeer een andere image te gebruiken, zoals:

```bash
kubectl create deployment hello-python --image=python:3.11-slim
en ontdek zelf hoe makkelijk het is om nieuwe apps te starten 🚀
```

📘 Tip: Wil je snappen hoe de onderdelen samenwerken?
Bekijk eens: kubectl get all — dan zie je pods, deployments, services in één overzicht.






