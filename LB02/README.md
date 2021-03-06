# M300-Services
Plattformübergreifende Dienste in ein Netzwerk integrieren

## Autor
Marius Huber

## Inhaltsverzeichnis
* [Docker](#Docker)
  * [Befehle](#Befehle)
  * [Netzwerkplan](#Netzwerkplan)
  * [Webserver erstellen](#Webserver-erstellen)
  * [Service Überwachung](#Service-Überwachung)
  * [Container Sicherheit](#Container-Sicherheit)
* [Kubernetes](#Kubernetes)
  * [Deployment erstellen](#Deployment-erstellen)
  * [Service](#Service)

## Docker
Docker ist eine Freie Software zur Isolierung von Anwendungen mit Hilfe von Containervirtualisierung. Docker vereinfacht die Bereitstellung von Anwendungen, weil sich Container, die alle nötigen Pakete enthalten, leicht als Dateien transportieren und installieren lassen.
### Befehle
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
| ```docker stop```   | Haltet die gewünschte Maschine an. |

### Netzwerkplan
![image](https://user-images.githubusercontent.com/50829674/114029048-6bae9380-9879-11eb-960f-7c95fed70dc5.png)

### Webserver erstellen
Hier wird kurz erklärt wie man eine einfache Webseite anhand eines Containers anbietet.
#### Dockerfile
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
#### Container starten
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

### Service Überwachung
Dieser Service ist sehr einfach einzurichten:
```
run -d --name cadvisor -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys:ro -v /var/lib/docker/:/var/lib/docker:ro -p 8080:8080 google/cadvisor:latest
```
Somit kann man nun im Browser mit "localhost:8080" auf den Service zugreifen.
![image](https://user-images.githubusercontent.com/50829674/114884203-de81b680-9e05-11eb-8290-29f6a1a5670b.png)

### Container Sicherheit
Hiermit kann der normale User keine sudo Befehle ausführen.
Das Dockerfile muss folgendes beinhalten:
```
RUN useradd -ms /bin/bash NeuerUserName

USER NeuerUserName

WORKDIR /homeNeuerUserName
```
![image](https://user-images.githubusercontent.com/50829674/115451071-049bc200-a21d-11eb-907e-d176d7878084.png)


#### Read-Only
Wenn man den Docker mit der Option read-only startet, können keine Änderungen am Dateisystem vorgenommen werden (auch mit sudo nicht):
```
docker run --read-only -d -t --name NameDesContainer Image
```
![image](https://user-images.githubusercontent.com/50829674/115451231-37de5100-a21d-11eb-8788-c11ac290e481.png)

## Kubernetes
### Deployment erstellen
Zuerst muss eine yaml Datei erstellt werden:
```
touch deployment.yaml
```
Die Datei soll folgendes beinhalten:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kubdeployment
  labels:
    app: kub
spec:
  replicas: 5
  selector:
    matchLabels:
      app: kub
  template:
    metadata:
      labels:
        app: kub
    spec:
      containers:
      - name: kub-webserver
        image: webserver
        imagePullPolicy: Always
        ports:
        - containerPort: 80
```
Jetzt muss das yaml file noch applied werden, damit die Container erstellt werden. Dafür muss man folgendes eingeben:
```
kubectl apply -f deployment.yaml
```

Mit folgendem Command kann man nun die Pods anzeigen:
```
kubectl get pods
```
![image](https://user-images.githubusercontent.com/50829674/115450699-8c350100-a21c-11eb-8dce-9a94200022d4.png)

### Service

Zuerst ein loadbalance.yaml file erstellen mit folgendem Inhalt:
```
apiVersion: v1
kind: Service
metadata:
  name: kubservice
  annotations:
    service.beta.kubernetes.io/linode-loadbalancer-throttle: "4"
  labels:
    app: kubservice
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: kub
  sessionAffinity: None
```
Nun muss man den Service ausführen:
```
kubectl apply -f loadbalance.yaml
```
Und so kann man ihn anzeigen:
```
kubectl get services
```
![image](https://user-images.githubusercontent.com/50829674/115452982-652bfe80-a21f-11eb-8f74-81c274b46669.png)

Nun kann man auf die Website zugreifen:
![image](https://user-images.githubusercontent.com/50829674/115610250-e3080c80-a2e8-11eb-886b-63622684c381.png)



