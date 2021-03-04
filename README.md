# M300-Services
M300

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
`vagrant up`

### Webserver VM
Zuerst ordner anlegen in der die VM sein soll und eine Datei "Vagrantfile" erstellen (ohne Dateiendung).
In das Vagrant folgenden inhalt schreiben: <br>
```
Vagrant.configure(2) do |config|
    config.vm.box = "ubuntu/xenial64"
    config.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true
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
