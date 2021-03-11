# M300-Services
Plattformübergreifende Dienste in ein Netzwerk integrieren

## Autor
Marius Huber

## Github
Git ist eine freie Software zur verteilten Versionsverwaltung von Dateien, die durch Linus Torvalds initiiert wurde.

### File bearbeiten
Als erstes muss man das Verzeichnis Klonen:
```
git clone https://github.com/c7910m/M300-Services/edit/main/README.md
```
Es wird in das Verzeichnis kopiert, indem man sich momentan in der Shell befindet.

Nun kann man das heruntergeladene File bearbeiten.

Danach kann man das File wieder hochladen:
```
git commit
git push
```


## Markdown
Markdown bearbeiten in Code:
https://code.visualstudio.com/docs/languages/markdown

Markdown wird zum darstellen einer Datei gebraucht in einem "Humanen Stil".
<br>

\# H1 Übertitel 1 <br>
\## H2 Übertitel 2 <br>
\### H3 Übertitel 3 <br>
\#### H4 Übertitel 4 <br>
\##### H5 Übertitel 5 <br>
\###### H6 Übertitel 6 <br>
<br>
\Alternatively, for H1 and H2, an underline-ish style: <br>

<p> \Alt-H1 <br>
\====== </p>
<br>
<p> \Alt-H2 <br>
\------ </p>
<br>
https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf

## Vagrant
Vagrant ist eine freie Ruby-Anwendung zum Erstellen und Verwalten virtueller Maschinen.[2] Vagrant ermöglicht einfache Softwareverteilung (englisch Deployment) insbesondere in der Software- und Webentwicklung und dient als Wrapper zwischen Virtualisierungssoftware wie VirtualBox, KVM/QEMU, VMware und Hyper-V und Software-Configuration-Management-Anwendungen beziehungsweise Systemkonfigurationswerkzeugen wie Chef, Saltstack und Puppet.

### Befehle
VM Starten: <br>
```
vagrant up
```

| Befehl            | Funktion                                             |
| -------------     | ---------------------------------------------------- | 
| vagrant init      | Initialisiert im aktuellen Verzeichnis eine Vagrant-Umgebung und erstellt, falls nicht vorhanden, ein Vagrantfile. |
| vagrant up        | Erzeugt und Konfiguriert eine neue Virtuelle Maschine, basierend auf dem Vagrantfile. |
| vagrant ssh       | Baut eine SSH-Verbindung zur gewünschten VM auf. |
| vagrant status    | Zeigt den aktuellen Status der VM an. |
| vagrant port      | Zeigt die Weitergeleiteten Ports der VM an. |
| vagrant halt      | Stoppt die laufende Virtuelle Maschine. |
| vagrant destroy   | Stoppt die Virtuelle Maschine und zerstört sie. |

### Webserver VM
Zuerst ordner anlegen in der die VM sein soll und eine Datei "Vagrantfile" erstellen (ohne Dateiendung).
In das Vagrant folgenden inhalt schreiben: <br>
```
Vagrant.configure(2) do |config|
    config.vm.box = "ubuntu/xenial64"
    config.vm.network "forwarded_port", guest:80, host:100, auto_correct: true
    config.vm.synced_folder ".", "/var/www/html"  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "512"  
  end
  config.vm.provision "shell", inline: <<-SHELL
    # Packages vom lokalen Server holen
    # sudo sed -i -e"1i deb {{config.server}}/apt-mirror/mirror/archive.ubuntu.com/ubuntu xenial main restricted" /etc/apt/sources.list 
    sudo apt-get update
    sudo apt-get -y install apache2 
  SHELL
  end
```
Nun muss man nur noch in einer Shell in das Verzeichnis gehen und "`vagrant up`" eintippen.
