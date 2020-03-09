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
  

## SSH-Key

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
