# README : Configuration d'OpenLDAP avec DNS sur un Serveur Linux



## Étape 1: Installation du serveur DNS (bind)
- Installez le package dns :
    ```
    sudo apt-get update
    sudo apt-get install bind9
    ```
- Ouvrez le fichier named.conf.local à l’aide de votre éditeur de texte préféré :
    ```
    sudo nano /etc/bind/named.conf.local
    ```

## Étape 2 : Configuration du DNS
- Ajoutez les détails de votre domaine à named.conf.local :
    ```
    zone "projectsec.tn" {
          type master;
          file "/etc/bind/db.projectsec.tn";
    };
    ```
- Créez le fichier du domaine :
    ```
    sudo cp /etc/bind/db.local /etc/bind/db.projectsec.tn
    ```
- Configurez ce fichier avec vos détails de domaine.

- Répétez ce processus avec le fichier db.reverse.tn pour la représentation de l'adresse IP.


## Étape 3 : Test du Serveur DNS
- Utilisez la commande `dig` pour tester la résolution DNS:
    ```
    dig @localhost projectsec.tn
    ```
- La commande doit renvoyer une réponse avec l'adresse IP que vous avez configurée.

## Étape 4 : Test OpenLDAP
- Utilisez la commande `ldapsearch` pour tester la configuration de l'OpenLDAP :
   ```
   ldapsearch -x -H ldap://localhost -b dc=projectsec,dc=tn -LLL "(uid=hamza)"
   ```
- 

