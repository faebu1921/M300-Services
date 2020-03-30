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

Zuvor: Mit Linux habe ich bereits ein wenig erfahrung und kannte mich mit den  Basics aus. <br>
Jetzt: Mein Wissensstand blieb unverändert bezüglich Linux <br>

Virtualisierung

Zuvor: Virtualisierung war mir zuvor ein Begriff, habe jedoch nochnie zuvor damit gearbeitet. <br>
Jetzt: Ich konnte meine ersten Erfahrungen sammeln und bin davon begeistert. Man kann Ressourcen sparen und ebenfalls sind sind zb Container oder VM viel schneller aufgesetzt.<br>

VirtualBox

Zuvor: Virtualbox habe ich bereits in anderen Moduls eingesetzt.<br>
Jetzt: Seit diesem Modul ist mir auch bewusst, dass es sich hierbei um einen Hypervisor Typ2 handelt. Ich habe ein wenig erfahren was hinter dem Programm steckt.

Markdown

Zuvor: Zuvor habe ich noch nie von Markdown gehört bzw damit gerarbeitet.<br>
Jetzt: Nun konnte ich erste Erfahrungen mit Markdown erarbeiten. Das beste an dieser Dokumentationsart finde ich die Nachvollziehbarkeit. Wenn etwas angepasst wurde sieht man von wem
und im besten Fall ist ein nützlicher Kommentar dazu vorhanden<br>

Wir verwenden Markdown als Dokumentationstool.

Systemsicherheit

Zuvor: Mir waren die Basics der Netzwerksicherheit bekannt.<br>
Jetzt: Mein Wissen hierzu hat sich nicht gross vermehr. Jedoch konnte ich besser nachvollziehen wie die UFW Firewall funktioniert.<br>

Git

Zuvor: Keine Vorkenntnisse vorhanden<br>
Jetzt: Ich habe nun zum erstenmal mit Git gerarbeitet <br>

Folgende Befehle habe ich kennengelernt:<br>

git clone: ermöglicht es ein Repository  zu Clonen<br>
gitt add: Datei für Upload vorbereiten<br>
git pull: Repository aktuallisieren<br>

git commit: Upload bestätigen mit einem Kommentar<br>
git push: Upload hochladen <br>


Vagrant

zuvor: Ich habe zuvor noch nie mit Vagrant gearbeitet<br>
Jetzt: Am Afang war es schwer sich in das Thema einzuarbeiten aber mit Beispiel Scripts etc konnte ich Stück für Stück die Struktur eines Vagrant Files nachvollziehen zb mit den Alias etc.<br>

Vagrant kann verwenden werden um jederzeit seine Virtuelle Umgebung aufzusetzen oder zu testen.<br>

Folgende Befehle habe ich kennengelernt:<br>

Vagrant init: Erstellt Vagrant File<br>
Vagrant up: Startet VM und richtet VM nach Vagrant File ein<br>
Vagrant ssh: SSH verbindung auf die VM<br>
Vagrant status: Status anzeige der VM<br>
Vagrant port: Anzeige der Weitergeleiteten Ports von der VM<br>
Vagrant halt: Stoppt laufende VM<br>
Vagrant destroy: löscht die Umgebung<br>
Vagrant destroy -f: löscht VM behält jedoch das File <br>



## Config meines Webservers

#webserver
  config.vm.define "web" do |web| <br>
    web.vm.hostname = "apache"<br>
    web.vm.network "private_network", ip: "192.168.55.101"<br>
    web.vm.box = "ubuntu/xenial64"<br>
    web.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true<br>
    web.vm.synced_folder ".", "/var/www/html" <br>
  
   web.vm.provider "virtualbox" do |vb|<br>
    vb.name = "apache"<br>
    vb.memory = "512"  <br>
  end

Hier wird Memory, syncfolder für den apacheserver etc definiert.<br>

## installation meines Webservers

sudo apt-get update<br>
sudo apt-get upgrade<br>
sudo apt-get install -y apache2<br>

## User erstellen

sudo useradd fabian<br>
sudo usermod -aG sudo fabian<br>


## Installation meiner Firewall

Firewall:

sudo apt-get install ufw # ufw Firewall installieren)<br>
sudo ufw default deny incoming # default deny inbound<br>
sudo ufw default allow outgoing # default allow outbound<br>
sudo ufw allow 80/tcp # öffnet den port 80 TCP<br>
sudo ufw allow 443/tcp # öffnet den port 443 TCP<br>
sudo ufw allow 22/tcp # öffnet den port 22 TCP für SSH <br>
echo "y" | sudo ufw enable # Firewall aktivieren mit der ausgabe Y<br>

## Testfälle 


### Test Nr1 

Ersteller des Testfalls: Fabian Oppliger<br>
Tester: Fabian Oppliger<br>
Test wurde am 30.03.2020 durchgeführt<br>

Soll: Apache webinterface über Lokalhost aufrufen<br>
Wie wurde getestet: Vom Localhost per http://127.0.0.1:8080/ auf den Apache zugreifen<br>

Ist: Localhost kann per Webinterface auf den Apache zugreifen.<br>

### Test Nr2
Ersteller des Testfalls: Fabian Oppliger<br>
Tester: Fabian Oppliger<br>
Test wurde am 30.03.2020 durchgeführt<br>

Soll: In der Virtuellen Umgebung sollte "/var/www/html" mit dem Lokalen Ordner C:\Users\Fabian\M300\M300-Services synchronisieren<br>
Wie wurde getestet: Das erzeugte Index.html file wird gelöscht, danach sollte auf dem Weinterface nur eine Directory zu sehen sein.<br>
Ist: Die Ordner synchronisieren, sobald das Index.html file gelöscht wird, wird die aktuelle directory angezeigt<br>

## Fehlgeschlagener Ldap

config.vm.define "master" do |master|<br>
  master.vm.box = "ubuntu/xenial64"<br>
master.vm.provider "virtualbox" do |vb|<br>
  vb.memory = "256"  <br>
end<br>
  master.vm.hostname = "master"<br>
  master.vm.network "private_network", ip: "192.168.55.102"<br>
  master.vm.network "forwarded_port", guest:80, host:8081, auto_correct: false<br>
  master.vm.synced_folder ".", "/vagrant"  	<br>
master.vm.provision "shell", inline: <<-SHELL<br>
sudo apt-get update<br>
sudo apt-get -y install debconf-utils apache2 nmap<br>
sudo a2enmod cgi	<br>
sudo a2enmod ldap	<br>
sudo a2enmod authnz_ldap<br>
sudo cp /vagrant/rest /vagrant/network /usr/lib/cgi-bin/ && sudo chown www-data /usr/lib/cgi-bin/* && sudo chmod 755 /usr/lib/cgi-bin/*<br>
sudo mkdir -p  /var/www/html/data && sudo chown www-data:www-data /var/www/html/data <br>
export DEBIAN_FRONTEND=noninteractive<br>
sudo debconf-set-selections <<<'slapd slapd/internal/generated_adminpw password admin'<br>
sudo debconf-set-selections <<<'slapd slapd/password2 password admin'<br>
sudo debconf-set-selections <<<'slapd slapd/internal/adminpw password admin'<br>
sudo debconf-set-selections <<<'slapd slapds/password1 password admin'<br>
sudo apt-get install -y slapd ldap-utils phpldapadmin<br>

sudo sed -i -e's/dc=example,dc=com/dc=nodomain/' /etc/phpldapadmin/config.php<br>
sudo service apache2 restart <br>
SHELL<br>



## Reflektion LB02

Zuallererst hatte ich extreme Probleme die ganze Thematik zu verstehen. Ich habe mich dann jedoch mit einem Beispiel ( einem vorgegebenen Vagrantfile )auseinander gesetzt 
und konnte dann stück für stück die abhängigkeit und auch Strukturierung der Vagrantfiles verstehen. Ebenfalls ist mir nun die ganze Virtualisierungsthematik deutlich klarer geworden.
Gerne hätte ich noch weiter herum experimentiert aber hierführ hatte ich in LB02 keine Zeit.<br>

Ebenfalls habe ich Markdown kennengelernt inclusive Github. Persönlich finde ich dies eine tolle Kombination und überlege mir dies auch Pirvat zuzulegen da die Administration etc. extrem einfach und nachvollziehbar ist.<br>

Im grossen und ganzen habe ich sehr viel bezüglich Virtualisierung und Markdown gelernt. jedoch waren die anderen Themsen bisher eher Auffrischung.