# M300-Services
Plattformübergreifende Dienste in ein Netzwerk integrieren

## Autor
Marius Huber

# Docker
Docker ist eine Freie Software zur Isolierung von Anwendungen mit Hilfe von Containervirtualisierung. Docker vereinfacht die Bereitstellung von Anwendungen, weil sich Container, die alle nötigen Pakete enthalten, leicht als Dateien transportieren und installieren lassen.
## Befehle
| Befehl            | Funktion                                             |
| -------------     | ---------------------------------------------------- | 
| ```docker pull```      | Holt ein Image. |
| ```docker run```      | Started VM mit dem ausgewähltem Image. |
| ```docker ps```      | Zeigt laufende Maschinen. |
| ```docker version```      | Zeigt die Docker Version von Echo-Client und Server an. |
| ```docker images```        | Listet alle Docker Images auf. |
| ```docker exec```       | Führt einen Befehl in einem laufenden Container aus. |
| ```docker search```    | Durchsucht das Docker Hub nach Images. |
| ```docker attach```      | Hängt etwas an einen laufenden Container an. |
| ```docker commit```   | Erstellt ein neues Image mit den Änderungen, die an einem Container vorgenommen worden sind. |

## Netzwerkplan
![image](https://user-images.githubusercontent.com/50829674/114029048-6bae9380-9879-11eb-960f-7c95fed70dc5.png)

## Webserver erstellen
Hier wird kurz erklärt wie man eine einfache Webseite anhand eines Containers anbietet.
### Dockerfile
```
#
#	Einfache Apache Umgebung
#
FROM ubuntu:14.04
MAINTAINER Marcel mc-b Bernet <marcel.bernet@ch-open.ch>

RUN apt-get update
RUN apt-get -q -y install apache2 

# Konfiguration Apache
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

RUN mkdir -p /var/lock/apache2 /var/run/apache2

EXPOSE 80

VOLUME /var/www/html

CMD /bin/bash -c "source /etc/apache2/envvars && exec /usr/sbin/apache2 -DFOREGROUND"
```
### Container starten
Zuerst Image erstellen:
```
docker build <Pfad vom Dockerfile>
```

Danach Image umbennen, damit es einfacher zu erkennen ist:
``` 
docker tag <Image ID> <Name den man vergeben will>
```

Nun kann man die VM starten (hier wird der Port 8080 weitergeleitet):
```
docker run --rm -d -p 8080:80 -v /web:/var/www/html --name <Container Name> <Image ID>
```

So könnte man im nachhinein auf die Shell zugreifen:
```
docker exec -it Webserver /bin/bash
```

So könnte man auch die Website abändern:
```
docker cp <Pfad vom HTML File auf Notebook> <VM Name>:/var/www/html/
```

Bei mir sieht es nun so aus:
![image](https://user-images.githubusercontent.com/50829674/114038550-42decc00-9882-11eb-8840-0b3d80876160.png)
