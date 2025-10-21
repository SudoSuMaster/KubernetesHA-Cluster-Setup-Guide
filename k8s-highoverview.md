Doel van deze les

Na deze les kun je uitleggen:

wat Kubernetes (k8s) doet,

wat een API en een resource zijn,

hoe je in een cluster kunt zien welke resources er bestaan.

🧠 Wat is Kubernetes eigenlijk?

Kubernetes is een systeem dat veel computers laat samenwerken om jouw applicaties (containers) automatisch te starten, stoppen en beheren.
Je hoeft dus niet meer alles handmatig te doen!

Je kunt het zien als een slim besturingssysteem voor meerdere servers.

🧱 De belangrijkste onderdelen van Kubernetes
Onderdeel	Wat het doet	Vergelijking
Control Plane	De “hersenen” van Kubernetes. Beslist wat er moet gebeuren.	Manager
Node (worker)	De “spieren”. Hier draaien je containers echt.	Medewerker
Kubelet	Houdt contact met de control plane en start de containers.	Communicatiemiddel
API Server	De voordeur: alle opdrachten (via kubectl) gaan hierlangs.	Receptie
etcd	De database waar Kubernetes alles opslaat.	Notitieboek
Scheduler	Kiest op welke node een nieuwe container komt.	Planner
Controller Manager	Controleert of alles nog klopt (desired vs actual).	Controleur
🌐 Hoe het werkt (stapsgewijs)

Jij geeft een opdracht, bijvoorbeeld:

kubectl create deployment hello-app --image=nginx


kubectl praat met de API Server → die slaat de opdracht op in etcd (de database).

De Controller Manager ziet dat er een nieuwe Deployment moet komen en vraagt de Scheduler om de Pods te starten op een Node.

De Kubelet op de Node zegt tegen Docker/containerd: “Start deze container.”

Kubernetes blijft dit controleren. Als een Pod uitvalt, start hij zelf een nieuwe.

🔍 Wat is een API?

Een API (Application Programming Interface) is een soort toegangsdeur waarmee programma’s met elkaar praten.
In Kubernetes gebruik jij de API via het commando:
```bash
kubectl
```

Elke keer als jij iets doet met kubectl, praat het met de API Server via het Kubernetes API-systeem.

🧩 Wat is een API Resource?

Een resource is een onderdeel van Kubernetes dat je via de API kunt aanmaken of aanpassen.

Voorbeelden van resources:

Resource	Functie
Pod	Draait één of meerdere containers.
Service	Maakt je Pod bereikbaar via een IP of poort.
Deployment	Zorgt dat er altijd een bepaald aantal Pods draaien.
Namespace	Logische map om dingen te organiseren.
Node	Eén fysieke of virtuele machine.

👉 Alles wat je in Kubernetes doet — of het nu een Pod starten of een Service maken is — is een API resource.

🧰 Wat heb je nodig

Een draaiend Minikube-cluster

kubectl werkt (test met kubectl get nodes)

🪜 Stappenplan
🔹 Stap 1 – Controleer of je cluster werkt
minikube status


Je ziet iets zoals:
```bash
host: Running
kubelet: Running
apiserver: Running
```
🔹 Stap 2 – Bekijk alle beschikbare resources
kubectl api-resources


Je ziet een lijst met:
```bash
NAME         SHORTNAMES   APIVERSION   NAMESPACED   KIND
pods         po           v1           true         Pod
services     svc          v1           true         Service
deployments  deploy       apps/v1      true         Deployment
```

💬 Uitleg:

NAME: de naam die je gebruikt in commando’s

SHORTNAMES: afkorting (bijv. po = pods)

KIND: het type object

NAMESPACED: of het binnen een namespace hoort

🔹 Stap 3 – Bekijk de API-versies
```bash
kubectl api-versions
```

Hiermee zie je alle groepen waarin resources zitten, zoals apps/v1, batch/v1, networking.k8s.io/v1, enz.

🔹 Stap 4 – Meer info over één resource
```bash
kubectl explain pods
```bash

Of specifieker:
```bash
kubectl explain pods.spec.containers
```

Dan legt Kubernetes uit wat elk veld betekent.

🔹 Stap 5 – Extra: bekijk de API live
```bash
kubectl proxy
```


Ga naar je browser:

http://localhost:8001/apis


Je ziet de API van je cluster als een lijst met alle groepen en versies.

🧠 Samenvatting

Kubernetes = een systeem dat containers automatisch beheert.

De API Server is het hart van de communicatie.

Alles in Kubernetes is een resource (Pod, Service, Deployment, enz.).

Met kubectl api-resources kun je ze allemaal zien.

Controllers zorgen ervoor dat de “desired state” altijd klopt met de “actual state”.

💡 Opdracht

Maak een lijst van 3 resources die jij belangrijk vindt.

Beschrijf in 1 zin wat elk doet.


```bash
🧭 Kleine samenvattende tekening (mentaal model)
+---------------------------+
|      YOU (kubectl)        |
+-------------+-------------+
              |
              v
     +--------+---------+
     |    API Server     |  <--- (ontvangt alle opdrachten)
     +--------+---------+
              |
      +-------+-------+
      |     etcd      |  <--- (database met gewenste toestand)
      +---------------+
              |
              v
  +-----------+------------+
  | Controllers & Scheduler |  <--- (regelen en plannen)
  +-----------+------------+
              |
              v
     +--------+--------+
     |     Kubelet      |  <--- (start containers)
     +--------+--------+
              |
              v
     +--------+--------+
     | Container Runtime |  <--- (Docker/containerd)
     +------------------+
```
Wil je dat ik dit omzet naar een .md-versie (Markdown) zodat je het netjes in een GitHub README of lesmap kunt zetten (met emoji’s en codeblokken behouden)?
