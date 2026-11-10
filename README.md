# ZABBIX POUR LA SUPERVISON

**Objectif :** Ce projet a pour but de mettre sur pied un serveur de supervision afin de suivre l'ensemble de notre infrastructure (réseau, système, cloud, applications). 


### I- PERIMETRE 
Ce projet ce structure en trois partie. Une pour la supervision système, une pour réseau et une pour les applications. Même comme, à la fin il s'agit juste d'une supervision système quelle soit faite via des agents (passifs ou actifs) ou via le protocole snmp (simple network management protocol) avec des OID(Object Identifier ou identifiant d'objet) en fonction de la plateforme (Linux, cisco, fortigate, mikrotik, windows, etc) sur laquelle nous effectuons la surveillance. 

<p align="center">
  <img src="./images/zabbix.png" width="100%">
</p>


### II- PREREQUIS
Bien que ce readme a pour but de donner des indications sur comment le reproduire, ainsi que les commentaires dans les fichiers yaml (.yml) qui vise à faciliter la compréhension. Il est important que vous ayez des notions en **containeurisation, orchestration de container, langage yml, les notions autours de la supervision (agents, scrapping, vérifications, etc)**. Il faut aussi avoir installé et configuré sur une **VM/VPS/VDS** Linux le necessaire suivant :
- Docker et Docker compose ;



### III- MISE EN PLACE DU SERVEUR ZABBIX


Pour mettre en marche nos outils :
1) Modifier l'adresse IP définit dans nos fichiers de configuration et remplacer par l'IP de votre server.

2) Démarrage de zabbix serveur 
```powershell
# Passer en mode superutilisateur
- sudo -s

# Installation et démarrage de prometheus et du nécessaire (exporteurs)
- docker compose -f compose-zabbix-server.yml up -d

# Vérifier que les conteneurs (mysql, zabbix-server, zabb)
- docker container ls 

# Pour recreer nos conteneurs si vous vous êtes trompés dans l'ordre d'exécution
- docker compose -f compose-zabbix-server.yml up -d --force-recreate
```

3) Installation Zabbix Agent 2 nativement sur notre serveur Zabbix (Ubuntu Server) 
```powershell
# Téléchargement de tous les composants de Zabbix (server, frontend, agent)
- wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb

# Depackatage du dépôt 
- sudo dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb

# Mise à jour du dépôt 
- sudo apt update

# Installation uniquement de l'agent 2
- sudo apt install zabbix-agent2 -y
```
4) Configuration de l'agent  
```powershell
# Editons le fichier de configuration de l'interface
- sudo nano /etc/zabbix/zabbix_agent2.conf

# Nous allons modifier ce fichier en fonction du mode que nous voulons pour notre agent (passif ou actif) :
```

**Pour le mode passif nous allons modifier les lignes suivantes dans le fichier :**
```powershell
- Server=IP_server_zabbix
- ServerActive=127.0.0.1
- Hostname=Zabbix server

- # Timeout
- Timeout=10
- # Niveau de journalisation
- DebugLevel=3

- # Port d'écoute local uniquement
- ListenIP=127.0.0.1
- ListenPort=10050
```
Si nous voulons configurer notre agent en mode actif, nous allons configurer *ServerActive=IP_server_zabbix* et allons commenter *Server=IP_server_zabbix*, nous avons donc :
**Pour le mode actif nous allons modifier les lignes suivantes dans le fichier :**
```powershell
- # Server=IP_server_zabbix
- ServerActive=IP_server_zabbix
- Hostname=Zabbix server
```

```powershell



"
# Identité de l'hôte
Hostname=ubuntu-zabbix-server

# Configuration du mode passif
Server=

# Mode actif
# ServerActive=IP_server_zabbix






"

#
- sudo systemctl enable zabbix-agent2

- sudo systemctl restart zabbix-agent2

- sudo systemctl status zabbix-agent2
```


5) Accéder à l'interface graphique de zabbix :
    - Pour l'interface web de Zabbix : http://votre_ipv4:80. Le Username par défaut est **Admin** et le password **zabbix**. Il est important de changer ces informations quand nous voudrions passer en production. 
    

### III- MISE EN PLACE DE L'AGENT
#### A) Déploiement de l'agent sur un hôte Linux
Pour déployer notre agent sur un hôte Linux à surveiller, il faut :
1) Creer un dossier de l'agent et copier le docker compose file
```powershell
# création du dossier de l'agent
mkdir -p /opt/agent

# Entrons dans le dossier de l'agent
cd /opt/agent-zabbix

# création du docker compose file de l'agent
nano compose.yml 

# Une fois le fichier ouvert avec nano, nous allons copier et coller le contenu de ./compose-agent-linux.yml dans ce fichier compose.yml. Nous allons donc sauvegarder.

- sudo -s
```

2) Démarrage de l'agent zabbix
```powershell
# Passer en mode superutilisateur
- sudo -s

# Installation et démarrage de l'agent en étant toujours dans /opt/agent-zabbix
- docker compose  up -d

# Vérifier que le conteneur a bien démarré
- docker container ls 

# Pour recréer notre conteneurs 
docker compose  up -d --force-recreate
```

3) Modifier le fichier de configuration de l'agent
```powershell
# Passer en mode superutilisateur
- sudo -s

# Installation et démarrage de l'agent en étant toujours dans /opt/agent-zabbix
- docker compose  up -d

# Vérifier que le conteneur a bien démarré
- docker container ls 

# Pour recréer notre conteneurs 
docker compose  up -d --force-recreate
```
#### B) Déploiement de l'agent sur Windows


    
**NB :** Il faudra exécuter les commandes ci-dessus dans le terminal en étant à la racine du projet. 

```powershell
#cd /opt/zabbix

# sudo chmod 600 .env
```
