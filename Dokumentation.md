# Dokumentation Zum Modul 300 LB2

## Wissenszuwachs

Zu Beginn des Moduls hatte ich noch nie mit Virtualisierung oder Vagrant/Markdown gearbeitet.

Was war mir neu:

* Virtualisierung
* ufw firewall
* Vagrant
* Markdown
* Containers
* Github
* ldap
* reverse Proxy
* SSH Key Agent
* Apache (Löschen des Index Files zeigt dann die Repo)


## Vorarbeit für das Modul

VB Installieren
Vagrant installieren und nutzen
Git Client installieren
SSH-Key generieren für den Host
Visual Studio inclusive Moduls installieren

### SSH-Key

Key erstellen

        ssh-keygen -t rsa -b 4096 -C "Fabian.Oppliger@gmail.com"

Agent aktivieren 

        eval "$(ssh-agent -s)"

SSH Key automatisch dem Agent hinzufügen


    Config SSH Key

        Host *
        AddKeysToAgent yes
        IgnoreUnknown UseKeychain
        UseKeychain yes
        IdentityFile ~/.ssh/id_rsa

in der Bash : ssh-add -k ~/.ssh/id_rsa


SSH Key auslesen für zb Github
        cat ~/.ssh/id_rsa.pub


Dieser wurde dann auf dem erstellten GITHUB- Konto hinterlegt


## Persönlicher Wissenstand Zuvor und Jetzt

Linux:

Zuvor: Mit Linux habe ich bereits ein wenig erfahrung und kannte mich mit den Basics aus.
Jetzt: Mein Wissensstand blieb unverändert bezüglich Linux

Virtualisierung
Zuvor: Virtualisierung war mir zuvor ein Begriff, habe jedoch nochnie zuvor damit gearbeitet.
Jetzt: Ich konnte meine ersten Erfahrungen sammeln und bin davon begeistert. Man kann Ressourcen sparen und ebenfalls sind sind zb Container oder VM viel schneller aufgesetzt.

VirtualBox
Zuvor: Virtualbox habe ich bereits in anderen Moduls eingesetzt.
Jetzt: Seit diesem Modul ist mir auch bewusst, dass es sich hierbei um einen Hypervisor Typ2 handelt. Ich habe ein wenig erfahren was hinter dem Programm steckt.

Markdown
Zuvor: Zuvor habe ich noch nie von Markdown gehört bzw damit gerarbeitet.
Jetzt: Nun konnte ich erste Erfahrungen mit Markdown erarbeiten. Das beste an dieser Dokumentationsart finde ich die Nachvollziehbarkeit. Wenn etwas angepasst wurde sieht man von wem
und im besten Fall ist ein nützlicher Kommentar dazu vorhanden

Wir verwenden Markdown als Dokumentationstool.

Systemsicherheit
Zuvor: Mir waren die Basics der Netzwerksicherheit bekannt.
Jetzt: Mein Wissen hierzu hat sich nicht gross vermehr. Jedoch konnte ich besser nachvollziehen wie die UFW Firewall funktioniert.

Git
Zuvor: Keine Vorkenntnisse vorhanden
Jetzt: Ich habe nun zum erstenmal mit Git gerarbeitet 

Folgende Befehle habe ich kennengelernt:

git clone: ermöglicht es ein Repository  zu Clonen
gitt add: Datei für Upload vorbereiten
git pull: Repository aktuallisieren

git commit: Upload bestätigen mit einem Kommentar
git push: Upload hochladen 


Vagrant
zuvor: Ich habe zuvor noch nie mit Vagrant gearbeitet
Jetzt: Am Afang war es schwer sich in das Thema einzuarbeiten aber mit Beispiel Scripts etc konnte ich Stück für Stück die Struktur eines Vagrant Files nachvollziehen zb mit den Alias etc.

Vagrant kann verwenden werden um jederzeit seine Virtuelle Umgebung aufzusetzen oder zu testen.

Folgende Befehle habe ich kennengelernt:

Vagrant init: Erstellt Vagrant File
Vagrant up: Startet VM und richtet VM nach Vagrant File ein
Vagrant ssh: SSH verbindung auf die VM
Vagrant status: Status anzeige der VM
Vagrant port: Anzeige der Weitergeleiteten Ports von der VM
Vagrant halt: Stoppt laufende VM
Vagrant destroy: löscht die Umgebung
Vagrant destroy -f: löscht VM behält jedoch das File




## Config meines Webservers

#webserver
  config.vm.define "web" do |web|
    web.vm.hostname = "apache"
    web.vm.network "private_network", ip: "192.168.55.101"
    web.vm.box = "ubuntu/xenial64"
    web.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true
    web.vm.synced_folder ".", "/var/www/html" 
  
   web.vm.provider "virtualbox" do |vb|
    vb.name = "apache"
    vb.memory = "512"  
  end

Hier wird Memory, syncfolder für den apacheserver etc definiert.

## installation meines Webservers

sudo apt-get update
sudo apt-get upgrade
sudo apt-get install -y apache2

## User erstellen

sudo useradd fabian
sudo usermod -aG sudo fabian


## Installation meiner Firewall

Firewall:

sudo apt-get install ufw # ufw Firewall installieren)
sudo ufw default deny incoming # default deny inbound
sudo ufw default allow outgoing # default allow outbound
sudo ufw allow 80/tcp # öffnet den port 80 TCP
sudo ufw allow 443/tcp # öffnet den port 443 TCP
sudo ufw allow 22/tcp # öffnet den port 22 TCP für SSH 
echo "y" | sudo ufw enable # Firewall aktivieren mit der ausgabe Y



## Fehlgeschlagener Ldap

config.vm.define "master" do |master|
  master.vm.box = "ubuntu/xenial64"
master.vm.provider "virtualbox" do |vb|
  vb.memory = "256"  
end
  master.vm.hostname = "master"
  master.vm.network "private_network", ip: "192.168.55.102"
  master.vm.network "forwarded_port", guest:80, host:8081, auto_correct: false
  master.vm.synced_folder ".", "/vagrant"  	
master.vm.provision "shell", inline: <<-SHELL
sudo apt-get update
sudo apt-get -y install debconf-utils apache2 nmap
sudo a2enmod cgi	
sudo a2enmod ldap	
sudo a2enmod authnz_ldap
sudo cp /vagrant/rest /vagrant/network /usr/lib/cgi-bin/ && sudo chown www-data /usr/lib/cgi-bin/* && sudo chmod 755 /usr/lib/cgi-bin/*
sudo mkdir -p  /var/www/html/data && sudo chown www-data:www-data /var/www/html/data 
export DEBIAN_FRONTEND=noninteractive
sudo debconf-set-selections <<<'slapd slapd/internal/generated_adminpw password admin'
sudo debconf-set-selections <<<'slapd slapd/password2 password admin'
sudo debconf-set-selections <<<'slapd slapd/internal/adminpw password admin'
sudo debconf-set-selections <<<'slapd slapds/password1 password admin'
sudo apt-get install -y slapd ldap-utils phpldapadmin

sudo sed -i -e's/dc=example,dc=com/dc=nodomain/' /etc/phpldapadmin/config.php
sudo service apache2 restart 
SHELL



## Reflektion LB02

Zuallererst hatte ich extreme Probleme die ganze Thematik zu verstehen. Ich habe mich dann jedoch mit einem Beispiel ( einem vorgegebenen Vagrantfile )auseinander gesetzt 
und konnte dann stück für stück die abhängigkeit und auch Strukturierung der Vagrantfiles verstehen. Ebenfalls ist mir nun die ganze Virtualisierungsthematik deutlich klarer geworden.
Gerne hätte ich noch weiter herum experimentiert aber hierführ hatte ich in LB02 keine Zeit.

Ebenfalls habe ich Markdown kennengelernt inclusive Github. Persönlich finde ich dies eine tolle Kombination und überlege mir dies auch Pirvat zuzulegen da die Administration etc. extrem einfach und nachvollziehbar ist.

Im grossen und ganzen habe ich sehr viel bezüglich Virtualisierung und Markdown gelernt. jedoch waren die anderen Themen bisher eher Auffrischung.