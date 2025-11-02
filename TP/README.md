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
On utilise ici aussi l'outil `nano` (toujours en mode admin car dossier protégé) pour modifier la directive Listen (celle qui indique au serveur quels ports écouter) : on ajoute la ligne `Listen 8080` ce qui dit à Apache d’écouter sur le port 8080 en plus du port 80.

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

    sudo ufw default deny incoming
On configure UFW (en tant qu'administrateur) pour refuser toutes les connexions entrantes par défaut (à l'exception des connexion autorisées / liste blanche).

    sudo ufw allow 22/tcp
    sudo ufw allow 8080/tcp 
On ajoute les ports 22 (SSH) et 8080 (localhost) à la liste blanche (à l'aide de l'option `allow`).

    sudo ufw enable
On active le pare-feu pour que sa configuration prenne effet (avec l'optien `enable`): il n'autorise plus que les connexions sur les ports 8080 et 22.

    sudo ufw status verbose
On vérifie le statut du pare-feu pour vérifier qu'il a bel et bien été activé (avec l'argument `verbose` pour avoir des détails en plus sur la configuration).

![Screen5](/TP/TP1/Screen5.png)

***b. Tester les règles en essayant de se connecter via d’autres ports.***

    nc -vz 10.37.129.4 80
En exécutant `nc` (`netcat`) vers notre système en utilisant le port 80 on remarque que la connexion n'aboutit pas...

![Screen6](/TP/TP1/Screen6.png)

    nc -vz 10.37.129.4 8080
    nc -vz 10.37.129.4 22
Cependant, après avoir exécuté la même commande en utilisant les ports 8080 puis 22, on remarque que les deux connexions ont abouti.

Les règles du pare-feu sont donc bien efficaces...

![Screen6bis](/TP/TP1/Screen6bis.png)

### 3. Sécurisation des connexions SSH

***a. Configurer votre vm pour accepter que les connexions via clefs ssh***

**Sur la machine client :**

Les deux prochaines étapes consistent en créer et copier la clé SSH qui nous permettra de nous connecter au serveur SSH, même après avoir désactivé l'authentification par mot de passe...

    ssh-keygen
On utilise cette commande pour générer une paire clés SSH (une publique et une privée, protégées par un mot de passe) pour le client SSH.

    ssh-copy-id utilisateur@ip_du_serveur
On utilise cette commande pour copier la clé publique sur le serveur SSH.

![Screen7](/TP/TP1/Screen7.png)

**Sur le serveur SSH :**

    sudo nano /etc/ssh/sshd_config
On utilise `nano` pour modifier la configuration du serveur SSH de notre VM. Le fichier de configuration étant dans `/etc/ssh/`, il nous faut les permissions d'administrateur donc on utilise une fois de plus `sudo`. On modifie le fichier de configuration comme suivant :

        PasswordAuthentication no
        PubkeyAuthentication yes
        ChallengeResponseAuthentication no
et on enregistre.

    sudo systemctl restart ssh
On utilise `systemctl` (toujours en admin) pour redémarrer le service SSH et ainsi appliquer les changements de configuration.

***b. Désactiver le login de l’utilisateur root sur ssh***

    sudo nano /etc/ssh/sshd_config
On retourne modifier la configuration du serveur SSH. On cherche la ligne `PermitRootLogin` et on change simplement la suite en `no`.

    sudo systemctl restart ssh
Ici aussi on redémarre SSH pour appliquer les changements de config...

## TP 2

### 1. Préparation et configuration du webhook

***Créer un webhook sur un serveur Discord dédié aux alertes de sécurité. Récupérer l’URL du webhook pour l’utiliser dans les scripts d’alerte.***

    https://discord.com/api/webhooks/1432832794622427157/SsUhEnhBIJI9Mb5b1pWZFjjpZgkIhxW8UvMdRIwfxtv-sMqr9Ll2kX75eKkiYRLqyjZn

*L'URL du webhook. Il s'appelle Adrien.*

    curl -H "Content-Type: application/json" \
        -X POST \
        -d "{\"content\": \"Test d'alerte Discord\"}" \
        "https://discord.com/api/webhooks/1432832794622427157/SsUhEnhBIJI9Mb5b1pWZFjjpZgkIhxW8UvMdRIwfxtv-sMqr9Ll2kX75eKkiYRLqyjZn"

On teste le webhook pour être sûr que tout fonctionne. Adrien doit dire "Test d'alerte Discord".

![Capture d'écran du webhook qui fonctionne](/TP/TP2/Screen1.png)

### 2. Surveillance des accès à des fichiers sensibles

***Identifier un fichier sensible (par exemple : `/etc/secret.txt`). Configurer un mécanisme de surveillance pour détecter tout accès en lecture à ce fichier. Utiliser `inotify` pour surveiller le fichier et déclencher un script Bash lorsqu’un accès est détecté. Le script enverra une alerte Discord via le webhook.***

    sudo apt update
    sudo apt install inotify-tools
En tant qu'administrateur, on commence par utiliser `apt` (le gestionnaire de paquets) pour actualiser le catalogue de paquets (`update`) avant d'installer (`install`) `inotify-tools` qui nous sera utile pour le TP.

    nano TP2.sh
On utilise `nano` pour éditer un nouveau fichier `TP2.sh` : il s'agit de notre script.

![Capture d'écran du script](/TP/TP2/Screen2.png)

    chmod +x TP2.sh
On rend notre script exécutable.

    sudo nano /etc/systemd/system/TP2.service
En tant qu'admin, on utilise `nano` pour créer un nouveau fichier service dans le dossier des services. Ce service est en charge d'exécuter notre script au lancement du système pour que la surveillance de notre fichier secret soit permanente.

![Capture d'écran du service](/TP/TP2/Screen3.png)

    sudo systemctl enable TP2.service
    sudo systemctl start TP2.service
On utilise `systemctl` (toujours en mode admin) pour activer (`enable`) et démarrer (`start`) le service (pour qu'il se lance au démarrage).

    cat /etc/secret.txt
On accède au fichier pour vérifier que tout fonctionne bien.

![Capture d'écran de la surveillance OK](/TP/TP2/Screen4.png)
![Capture d'écran de la surveillance OK](/TP/TP2/Screen4bis.png)

*La notification apparait immédiatement à chaque fois...*

### 3. Surveillance des connexions SSH hors des horaires de bureau

***a. Les horaires de bureau sont définis entre 9h00 et 18h00.***



***b. Surveiller les connexions SSH en dehors de ces horaires en analysant les logs du service SSH.***



***c. Configurer un script qui analyse le fichier de log `/var/log/auth.log` ou utilise `journalctl` pour repérer les connexions hors de cette plage horaire.***



***d. Si une connexion est détectée en dehors des heures de bureau, envoyer une alerte Discord via le webhook.***



### 4. Automatisation avec cron pour une surveillance continue

***Configurer cron pour exécuter régulièrement les scripts :***

***a. Le script de surveillance d'accès au fichier doit tourner en arrière-plan en continu.***



***b. Le script de surveillance des connexions SSH doit être exécuté toutes les 5 minutes.***

