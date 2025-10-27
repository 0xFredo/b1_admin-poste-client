# B1 — Administration Poste Client

## TP 1
### 1. Installation et configuration d’un serveur web

***a. Installer Apache, lancer le et vérifier son statut.***

    sudo apt update
On exécute cette commande pour mettre à jour la liste des paquets. `apt` (à exécuter en tant qu'admin donc `sudo`) est l'outil de gestion des paquets et qui nous servira par la suite pour installer Apache.

    sudo apt install apache2
    sudo systemctl status apache2
On installe Apache avec `apt` et on vérifié son statut avec `systemctl status` (à exécuter aussi en tant qu'admin).

![Screen1](/TP/TP1/Screen1.png)

***b. Configurer le serveur pour servir une page web simple.***

    sudo nano /var/www/html/TP1-1.html
On utilise `nano` pour créer et éditer un nouveau fichier HTML dans le répertoire des fichiers Apache (en tant qu'admin car le dossier est protégé). On colle le contenu de la page HTML dans le fichier, on enregistre et on quitte.

![Screen2](/TP/TP1/Screen2.png)

***c. Vérifier que le serveur est accessible localement via un navigateur.***

Dans notre système, on ouvre le navigateur de notre choix et on tape `localhost/` puis le nom du fichier que l'on vient de créer (ici `TP1-1.html`) : la page s'affiche, le serveur est donc accessible localement via un navigateur.

![Screen3](/TP/TP1/Screen3.png)

***d. Modifier la configuration pour que le serveur réponde sur un port non standard (ex: 8080).***

    sudo nano /etc/apache2/ports.conf
On utilise ici aussi l'outil `nano` (toujours en mode admin car dossier protégé) pour modifier la directive Listen (celle qui indique au serveur quels ports écouter) et y ajouter le port 8080.

(On ajoute la ligne `Listen 8080` ce qui dit à Apache d’écouter sur le port 8080 en plus du port 80.)

![Screen4](/TP/TP1/Screen4.png)

    sudo nano /etc/apache2/sites-available/000-default.conf
On utilise également `nano` pour aussi modifier le fichier de configuration VirtualHost et éditer la ligne de celui-ci pour utiliser le port 8080 (on a modifié `*:80` en `*:8080`)...

![Screen4bis](/TP/TP1/Screen4bis.png)

    sudo systemctl restart apache2
On utilise `systemctl` (toujours avec `sudo`) pour redémarrer le service `apache2` et appliquer les changements apportés à la configuration.

En ouvrant `localhost:8080/` dans le navigateur (et non `localhost` tout simplement car on spécifie le nouveau port `:8080`) on voit que cela fonctionne et que le nouveau port est actif.

![Screen4bis'](/TP/TP1/Screen4bis'.png)

### 2. Configuration du pare-feu avec UFW

***a. Configurer un pare-feu pour n’autoriser que les connexions sur le port du serveur web (ex : 8080) et SSH (port 22).***



***b. Bloquer toutes les autres connexions entrantes.***



***c. Tester les règles en essayant de se connecter via d’autres ports.***



### 3. Sécurisation des connexions SSH

***a. Configurer votre vm pour accepter que les connexions via clefs ssh***



***b. Désactiver le login de l’utilisateur root sur ssh***

