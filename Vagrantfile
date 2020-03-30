Vagrant.configure(2) do |config|
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
  config.vm.provision "shell", inline: <<-SHELL
    # Packages vom lokalen Server holen
    # sudo sed -i -e"1i deb {{config.server}}/apt-mirror/mirror/archive.ubuntu.com/ubuntu xenial main restricted" /etc/apt/sources.list 
    #install webserver
    sudo apt-get update
    sudo apt-get -y install apache2 
    #https
    sudo a2ensite default-ssl.conf
    sudo a2enmod ssl
    sudo a2dissite 000-default.conf 
    sudo service apache2 restart 
    #fw
    sudo apt-get install ufw
    sudo ufw default deny incoming
    sudo ufw default allow outgoing
    sudo ufw allow 80/tcp
    sudo ufw allow 443/tcp
    sudo ufw allow ssh
    echo "y" | sudo ufw enable
    #myuser
    sudo useradd fabian
    sudo usermod -aG sudo fabian
        #ssh Key erstellen
        #mkdir .ssh && chmod 700 .ssh
        #ssh-keygen -t rsa -f .ssh/id_rsa -b 4096 -C vagrant@srv-nv -P ''
  SHELL
  end



# LDAP Server
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
# OpenLDAP mit Admin UI - http://localhost:8081/phpldapadmin
export DEBIAN_FRONTEND=noninteractive
sudo debconf-set-selections <<<'slapd slapd/internal/generated_adminpw password admin'
sudo debconf-set-selections <<<'slapd slapd/password2 password admin'
sudo debconf-set-selections <<<'slapd slapd/internal/adminpw password admin'
sudo debconf-set-selections <<<'slapd slapds/password1 password admin'
sudo apt-get install -y slapd ldap-utils phpldapadmin
# Patch Standarddomain
sudo sed -i -e's/dc=example,dc=com/dc=nodomain/' /etc/phpldapadmin/config.php
sudo service apache2 restart 
SHELL
end
end