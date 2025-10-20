1) Wat is Docker (heel kort)

Image = het “recept” (software + instellingen).

Container = het “gerecht” dat draait op jouw laptop/server, gemaakt van een image.


2) Installeren & checken

1. Installeer Docker Desktop (Windows/Mac) of Docker Engine (Linux).


2. Test in je terminal/PowerShell:



docker --version

3) Eerste test: hello-world

docker run hello-world

Docker downloadt de image en start een container.

Klaar? Je ziet een korte uitleg in de terminal.


4) Belangrijkste commando’s (cheatsheet)

docker pull <image:tag>     # image downloaden
docker images               # alle images op je pc
docker run <image>          # start een container
docker ps                   # draaiende containers
docker ps -a                # alle containers (ook gestopte)
docker stop <naam/id>       # container stoppen
docker rm <naam/id>         # container verwijderen
docker rmi <image-id>       # image verwijderen

5) Praktijk: een kleine website draaien

We gebruiken de officiële nginx webserver.

docker pull nginx:alpine
docker run -d -p 8080:80 --name mijnsite nginx:alpine

Open je browser: http://localhost:8080

Stoppen:


docker stop mijnsite
docker rm mijnsite

6) Zelf een image bouwen (met Dockerfile)

We maken een piepkleine website.

1. Maak een map, bv. mijn-website/, met twee bestanden:



index.html

<!doctype html>
<html>
  <head><meta charset="utf-8"><title>Hallo Docker</title></head>
  <body><h1>Werkt!</h1><p>Mijn eerste Docker image 🎉</p></body>
</html>

Dockerfile

# Gebruik een kleine webserver image
FROM nginx:alpine

# Kopieer onze website naar de standaard map van nginx
COPY index.html /usr/share/nginx/html/index.html

2. Bouw de image:



docker build -t mijn-website:1.0 .

3. Start een container:



docker run -d -p 8080:80 --name site mijn-website:1.0

4. Check in je browser: http://localhost:8080


5. Stop/verwijder:



docker stop site
docker rm site

7) Veelgemaakte fouten (en fixes)

Poort al in gebruik → kies andere poort: -p 8081:80

“permission denied” bij build → zorg dat je in de juiste map staat (cd) en dat Dockerfile zo heet (zonder extensie).

Container start en stopt meteen → check logs:

docker logs <containernaam>


8) Mini-opdrachten (voor in de les)

1. Start nginx:alpine op poort 8090 i.p.v. 8080.


2. Pas je index.html aan (andere tekst) en bouw mijn-website:1.1. Start ’m.


3. Laat met docker images zien dat je 1.0 en 1.1 hebt. Verwijder 1.0.


4. Zoek zelf een officiële image (tip: alpine, python, node) en start ’m.



9) Veilig werken

Gebruik bij voorkeur officiële images (van “library” of bekende uitgevers).

Lees altijd even de beschrijving van de image (welke poorten, welke commando’s).
