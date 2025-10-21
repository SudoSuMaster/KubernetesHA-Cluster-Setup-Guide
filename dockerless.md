---

# 🐳 Docker Images Guide

Een super simpele uitleg over **Docker**, **images** en **containers** — met voorbeelden die je direct kunt proberen.  
Ideaal voor de eerste kennismaking met containertechnologie. 🚀

---

## 📘 Wat is Docker?

- **Image** → het “recept” (software + instellingen).  
- **Container** → het “gerecht” dat draait op jouw laptop, gemaakt van een image.

Docker maakt het makkelijk om software overal op dezelfde manier te draaien.

---

## ⚙️ Installatie & Controle

1. Installeer **Docker Desktop** (Windows/Mac) of **Docker Engine** (Linux).  
2. Test of alles werkt:

```bash
docker --version
```

---

👋 Eerste Test
```bash
docker run hello-world
```
✅ Docker downloadt de image, start een container, en toont een korte uitleg in de terminal.


---

🧾 Belangrijkste Commando’s

Commando	Uitleg
```bash
docker pull <image:tag>	Download een image
docker images	Toon alle images
docker run <image>	Start een container
docker ps	Toon draaiende containers
docker ps -a	Toon ook gestopte containers
docker stop <naam/id>	Stop een container
docker rm <naam/id>	Verwijder een container
docker rmi <image-id>	Verwijder een image
```


---

🌐 Een Kleine Website Draaien

We gebruiken nginx, een kleine webserver.
```bash
docker pull nginx:alpine
docker run -d -p 8080:80 --name mijnsite nginx:alpine

Open je browser en ga naar: http://localhost:8080
```
Stoppen:

docker stop mijnsite
docker rm mijnsite


---

🏗️ Zelf een Image Bouwen

Maak een map, bijvoorbeeld mijn-website/, met deze bestanden:
```html
index.html

<!doctype html>
<html>
  <head><meta charset="utf-8"><title>Hallo Docker</title></head>
  <body><h1>Werkt!</h1><p>Mijn eerste Docker image 🎉</p></body>
</html>
```

```bash
Dockerfile

# Gebruik een kleine webserver image
FROM nginx:alpine

# Kopieer onze website naar de standaard map van nginx
COPY index.html /usr/share/nginx/html/index.html

Bouw en start de container:

docker build -t mijn-website:1.0 .
docker run -d -p 8080:80 --name site mijn-website:1.0

Check in je browser: http://localhost:8080

```
---

⚠️ Veelgemaakte Fouten

Probleem	Oplossing

Poort al in gebruik	Gebruik een andere poort: -p 8081:80
“permission denied” bij build	Zorg dat je in de juiste map staat (cd)
Container stopt meteen	Bekijk logs: docker logs <containernaam>



---

🧩 Mini-opdrachten

1. Start nginx:alpine op poort 8090.


2. Pas je index.html aan en bouw mijn-website:1.1.


3. Laat met docker images zien dat je 1.0 en 1.1 hebt. Verwijder 1.0.


4. Zoek een officiële image (bijv. alpine, python, node) en start ’m.




---

🔒 Veilig Werken

Gebruik alleen officiële images (van Docker Hub).

Lees altijd even de beschrijving: poorten, volumes, commando’s, enz.



---

🧠 Samenvatting

Docker = software in een container, gebouwd van een image,
die overal hetzelfde werkt — superhandig voor ontwikkelaars en studenten!


---

👨‍🏫 Gemaakt voor MBO-1 studenten om stap-voor-stap Docker te leren begrijpen.

---

Wil je dat ik er ook een klein **GitHub-stijl badge-header** (zoals `![Docker](...)`) en een **“Next Steps”** sectie aan toevoeg, zodat het er nóg professioneler uitziet?



