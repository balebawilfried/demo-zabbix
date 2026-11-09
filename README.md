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
docker compose -f compose-zabbix-server.yml up -d --force-recreate
```


6) Accéder à l'interface graphique de zabbix :
    - Pour l'interface web de Zabbix : http://votre_ipv4:80. Le Username par défaut est **Admin** et le password **zabbix**. Il est important de changer ces informations quand nous voudrions passer en production. 
    




    
**NB :** Il faudra exécuter les commandes ci-dessus dans le terminal en étant à la racine du projet. D'autres part, il important de modifier les informations de configuration du serveur SMTP présentes ici (**./alertmanager/*.yml**) avec les vôtres sinon vous n'allez pas reçevoir les notification de alertmanager.

### IV- CONFIGURATION DE GRAFANA
Afin de pouvoir ajouter/créer les dashboards, il est important d'ajouter les **sources de données** de prometheus et loki avant de créer les dashboard qui seront connectés à ces dernières. Au moment de l'ajout de la source de données, étant donné que ces éléments appartiennent au même réseau docker. Il faudra respectivement pour urls de connexion choisir **http://prometheus:9090** pour la source de données prometheus et **http://loki:3100**  pour la source de données loki. 





**NB :** A chaque modification d'un fichier .yml, nous avons besoin de faire un **docker compose up -d**. 

**Si vous souhaitez utiliser ce travail pour déployer plus tard en production, il est important de clairement définir la version de nos outils dans le fichier de variable d'environnement du projet .env**
