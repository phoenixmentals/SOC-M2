---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# MISP Threat Intelligence

**Introduction**&#x20;

Notre objectif ne se limite pas à la simple collecte et analyse de nos journaux. Nous cherchons une méthode permettant d'enrichir nos logs avec des informations, facilitant ainsi la réactivité des analystes et la détection rapide d'éventuelles activités malveillantes. Un exemple concret serait la capacité des analystes à déterminer si une adresse IP avec laquelle notre site interagit est malveillante. Pour atteindre cet objectif, la solution MISP offre plusieurs avantages :

1. **Enrichissement des logs :** MISP nous permet d'enrichir les logs reçus en y ajoutant des renseignements sur les menaces provenant de divers fournisseurs.
2. **Analyse sélective et stockage :** Nous pouvons analyser et stocker de manière sélective les réponses obtenues, assurant ainsi que seules les données cruciales sont enregistrées.
3. **Automatisation de l'enrichissement :** MISP propose une automatisation du processus d'enrichissement des logs reçus, optimisant ainsi l'efficacité de notre système de détection des menaces.

\
MISP est une solution logicielle open source conçue pour collecter, stocker, distribuer et partager des indicateurs de cybersécurité et des menaces liées à l'analyse d'incidents et à l'analyse de logiciels malveillants. MISP est élaboré par et pour des analystes d'incidents, des professionnels de la sécurité et des technologies de l'information, ou des experts en inversion de logiciels malveillants, afin de soutenir leurs opérations quotidiennes en matière de partage efficace d'informations structurées.

L'objectif de MISP est de favoriser le partage d'informations structurées au sein de la communauté de la sécurité et au-delà. MISP offre des fonctionnalités pour soutenir l'échange d'informations, mais également la consommation de ces informations par les systèmes de détection d'intrusion réseau (NIDS), les systèmes de détection d'intrusion sur les hôtes (HIDS), ainsi que les outils d'analyse de journaux et les systèmes de gestion des informations et des événements de sécurité (SIEM).

Source : [https://github.com/MISP/MISP](https://github.com/MISP/MISP)

**Installation**&#x20;

Il existe plusieurs méthodes pour installer MISP, et divers scripts sont disponibles pour simplifier la tâche. Pour cette installation, j'ai opté pour l'utilisation de Docker.

**Installation de Docker et Docker Compose**

Désinstallation des anciennes versions de Docker, le cas échéant : Avant de poursuivre avec l'installation de MISP, il est recommandé de désinstaller les éventuelles anciennes versions de Docker pour éviter tout conflit

```
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

configuration et installation du dépôt Docker

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

Ajout du repo dans les sources

```bash
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Installation du Package Docker et premier lancement :

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
```

<figure><img src=".gitbook/assets/image (150).png" alt=""><figcaption><p>Docker Installé avec Succès</p></figcaption></figure>

**Build de l'Image Docker de MISP**

<pre class="language-bash"><code class="lang-bash">git clone https://github.com/MISP/misp-docker
<strong>cd misp-docker
</strong>cp template.env .env
</code></pre>

Modification des parametres vim .env

<figure><img src=".gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

**Construction et Premier Démarrage du Conteneur Docker**

Après avoir configuré les paramètres initiaux, procédons à la construction du conteneur Docker en utilisant les commandes suivantes :

```
docker compose build
docker compose up -d
```

Une fois que les conteneurs Docker sont construits avec succès, il est essentiel d'éditer le fichier `docker-compose.yml` pour garantir une exécution correcte. Ce fichier renferme les informations de configuration nécessaires pour l'environnement Docker dans lequel MISP fonctionnera.&#x20;

On s'assure de décommenter le port 80 pour prévenir les connexions non sécurisées. De plus, on modifie la variable MISP\_BASEURL pour refléter l'adresse IP de la machine sur laquelle MISP sera exécuté.

<figure><img src=".gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

Maintenant que nous avons ajusté le port et la variable `MISP_BASEURL`, nous pouvons démarrer l'environnement Docker et lancer notre instance MISP en utilisant la commande suivante :

```
docker-compose up -d 
```

Une fois sur la page d'accueil de MISP et connecté, il est impératif de changer les mots de passe par défaut pour renforcer la sécurité de notre instance.

<figure><img src=".gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

**Les flux**

Par défaut, MISP intègre deux flux qu'il utilise pour télécharger des rapports sur les menaces, des indicateurs de compromission (IoC), etc. Ces flux contiennent l'ensemble des données que MISP stocke.

Les flux intégrés fournissent une source initiale de renseignements sur les menaces et sont cruciaux pour l'enrichissement des données de MISP. Ils sont conçus pour permettre à MISP de rester à jour avec les dernières informations sur les menaces, facilitant ainsi une analyse plus précise et en temps réel des activités malveillantes. Ces flux peuvent inclure des indicateurs tels que des adresses IP malveillantes, des noms de domaine suspects, des signatures de logiciels malveillants, et bien d'autres informations encore.

Il est recommandé de régulièrement mettre à jour ces flux pour garantir que MISP dispose des dernières informations sur les menaces, renforçant ainsi la capacité de la plateforme à détecter et à réagir aux nouvelles menaces de manière proactive.

<figure><img src=".gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

Nous allons devoir importer et activer d'autres flux pour améliorer cet aspect. Pour ce faire, suivez les étapes ci-dessous :

<figure><img src=".gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

Accédez à l'onglet "Sync Actions/Feeds" :

* Dans l'interface utilisateur de MISP, recherchez et accédez à l'onglet intitulé "Sync Actions/Feeds".

Choisissez l'option "Import Feeds from JSON" :

* Dans cet onglet, recherchez l'option qui vous permet d'importer des flux depuis un fichier JSON. Elle pourrait être intitulée "Import Feeds from JSON" ou quelque chose de similaire.

Nous allons coller le contenu du repo suivant dans la zone Feed metadata JSON sur l'image en haut

{% embed url="https://github.com/MISP/MISP/blob/2.4/app/files/feed-metadata/defaults.json" %}

* Une fois le fichier importé, activez les flux correspondants pour qu'ils soient pris en compte par MISP.

Enregistrez les modifications :

* Sauvegardez toutes les modifications apportées à la configuration des flux.

<figure><img src=".gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

**Mise en Place d'une API pour la Récupération des Flux dans MISP**&#x20;

Comme la récupération des flux ne s'effectue pas automatiquement, la mise en place d'une API avec une planification de tâches à l'aide de cron peut simplifier ce processus.

**Création d'une API pour la Récupération des Flux**

Avant de créer l'API pour la récupération des flux dans MISP, commençons par générer une clé d'API .

Dans l'onglet administration>List auth keys>add ath key

<figure><img src=".gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

Une fois la clé générée,on clique  sur "I have noted down my key" .On en aura  besoin pour authentifier les requêtes de notre API

**Creation d'une Tache Cron Journaliere**

<pre class="language-bash"><code class="lang-bash"><strong>crontab -e
</strong><strong>0 1 * * * /usr/bin/curl -XPOST --insecure --header "Authorization: IAfsBAVkxYidIHQ3IpRROQAwlYaliN0X5B6xw4Ye" --header "Accept: application/json" --header "Content-Type: application/json" https://192.168.1.149/feeds/fetchFromAllFeeds
</strong></code></pre>

Configuration d'une tache avec cron  pour automatiser la mise a jour des flux

<figure><img src=".gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

**Integration avec Graylog**

Lorsque des organisations évoluent vers la surveillance de centaines voire de milliers de serveurs, le volume de logs à analyser peut rapidement se transformer en un obstacle majeur. La corrélation des renseignements sur les menaces avec chaque connexion réseau, chaque hachage de processus et chaque fichier téléchargé devient une tâche titanesque, entraînant une perte d'efficacité et augmentant le risque de passer à côté d'indicateurs cruciaux.

L'utilisation astucieuse de l'API robuste de MISP par l'intermédiaire de Graylog offre la possibilité d'automatiser le processus d'enrichissement des journaux, éliminant ainsi la nécessité d'une analyse manuelle fastidieuse. Les avantages de cette intégration résident dans la capacité de Graylog à interroger MISP en temps réel, extrayant des informations pertinentes en fonction des IoCs présents dans les journaux et enrichissant ainsi les données avant leur stockage dans l'indexeur Wazuh.

Pour mettre en place l'intégration entre MISP et Graylog, commençons par créer une clé d'API avec des autorisations en mode "Read Only". Cela garantira que Graylog ne peut effectuer que des opérations de lecture, renforçant ainsi la sécurité.

<figure><img src=".gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

```bash
curl -k -L -H "Accept: application/json" -H "Content-Type: application/json" -H "Authorization: 5A6MgyhegxcPhN5eX0hFbvQqI3P5upB0NGAtNRTu" http://192.168.1.149/attributes/restSearch/value:mb4z3nlfyrcjnoqf.onion
```

<figure><img src=".gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

\
**Contournement du Problème des Certificats Auto-signés dans Graylog**

Lors de l'installation comme j'étais dans un environnement de test, j'ai remarqué que Graylog ne prend pas en charge les certificats auto-signés, ce qui pose un problème majeur. J'ai décidé de documenter cette étape, car je la considère comme cruciale.

Elle est documentée en détails sur cette page web : [https://www.ctechmat.fr/?p=34002](https://www.ctechmat.fr/?p=34002)

Graylog refuse les certificats auto-signés lors des requêtes. Pour contourner ce problème et permettre l'exécution de requêtes, nous allons suivre les étapes suivantes :

1. Création du fichier /etc/ssl/private/openssl-misp.cnf sur le serveur MISP avec les paramètres adaptés au serveur MISP.

{% hint style="info" %}
Ces commandes sont à exécuter sur le serveur MISP
{% endhint %}

```
[req]
distinguished_name = req_distinguished_name
x509_extensions = v3_req
prompt = no
[req_distinguished_name]
C = FR
ST = Occitanie
L = Toulouse
O = Home
OU = Securite
CN = misp.local
[v3_req]
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
IP.1 = 192.168.1.149
DNS.1 = misp.local
```

2. Génération d'un certificat auto-signé à l'aide d'OpenSSL en utilisant les paramètres spécifiés dans le fichier de configuration openssl-misp.cnf.

```bash
sudo openssl req -newkey rsa:4096 -days 365 -nodes -x509 -keyout /etc/ssl/private/misp.local.key -out /etc/ssl/private/misp.local.crt -config /etc/ssl/private/openssl-misp.cnf
```

3\. affichage de la clé du certificat en sur Graylog :

```
openssl s_client -showcerts -connect 192.168.1.149:443
```

<figure><img src=".gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

La commande qu'on vient d'executer va générer du texte, ce qui nous interrresse c'est de copier la section entre "BEGIN CERTIFICATE" et "END CERTIFICATE" du certificat qu'on a obtenu comme sur la capture précédente, pour la coller dans un nouveau fichier /etc/graylog/server/certificates/misp-certificate.pem.&#x20;

Cette étape permettra d'importer la clé correspondante dans le magasin de clés de Java et de redémarrer le serveur Graylog en  exécutant les commande suivantes :

```bash
keytool -importcert -keystore /usr/lib/jvm/java-1.11.0-openjdk-amd64/lib/security/cacerts -alias misp-cert -file /etc/graylog/server/certificates/misp-certificate.pem
systemctl restart graylog-server.service
```

**Cas d'un enrichissement de log avec MISP à l'aide de la fonctionnalité de surveillance de fichiers de Wazuh**

Pour illustrer un exemple de cette intégration, nous allons utiliser la fonctionnalité de surveillance de fichiers de Wazuh. Pour ce faire, nous allons activer la surveillance en temps réel du dossier "Downloads" sur un équipement Windows. Bien que cela puisse être réalisé de manière globale avec Wazuh Manager, nous allons le faire directement sur l'agent pour cet exemple.

On se rend dans le repertoire C:\Program Files (x86)\ossec-agent et on ajoute la ligne ci-après dans la partie \<syscheck>\</syscheck>

```
<directories realtime="yes">C:\Users\Phoenix\Downloads</directories>
```

<figure><img src=".gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

On redemarre l'agent à l'aide des commandes suivantes

```powershell
NET STOP WazuhSvc
NET START WazuhSvc
```

Ensuite, nous nous rendons sur notre serveur Graylog pour poursuivre les configurations

**Création du Data Adapter**

Pour commencer dans Graylog, nous devons d'abord créer un adaptateur de données. L'adaptateur de données est la fonctionalité qui nous permet de configurer la requête API qui sera effectuée, incluant l'URL, les clés d'authentification, les en-têtes, etc.

Voici comment procéder sur ces images:

<figure><img src=".gitbook/assets/image (111).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

**Creation du cache de données**

Créer un cache de données est un autre avantage intéressant de l'utilisation de Graylog. Le caching permet de limiter la charge sur le serveur MISP. Graylog n'invoquera l'API MISP que si la valeur demandée par Graylog n'est pas déjà présente dans le cache.

L'image suivante montre la création du Cache

<figure><img src=".gitbook/assets/image (115).png" alt=""><figcaption></figcaption></figure>

**Création de la table de recherche**&#x20;

La création d'une table de recherche (look up table) est essentielle pour lier une instance d'adaptateur de données et une instance de cache. Elle est nécessaire pour activer l'utilisation effective de la table de recherche dans les extracteurs, les convertisseurs, les fonctions de pipeline et les décorateurs.

<figure><img src=".gitbook/assets/image (116).png" alt=""><figcaption></figcaption></figure>

**Creation de la pipeline**

Avec notre table de recherche configurée, nous devons indiquer à Graylog quand nous voulons invoquer l'API MISP. Cela se fait en créant une règle  pipeline. Dans cet exemple, je vais créer une règle de pipeline pour invoquer l'API MISP lorsqu'un fichier est ajouté un terminal. Je vais utiliser le champ syscheck\_sha256\_after et utiliser sa valeur pour construire une demande API vers MISP. Cela nous permettra de savoir si le fichier ajouté au système a un hash malveillant connu ou non.

<figure><img src=".gitbook/assets/image (117).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (118).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

Ayant terminé toutes les configurations sur Graylog, nous pouvons visualiser un journal déclenché lors de l'ajout d'un logiciel malveillant.

Sur une machine virtuelle dédiée aux tests, j'ai téléchargé un logiciel malveillant depuis [https://bazaar.abuse.ch/sample/](https://bazaar.abuse.ch/sample/). Il a été détecté, et voici un log enrichi avec les informations de MISP.

<figure><img src=".gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

Nous allons construire un lien permettant d'ouvrir MISP depuis Grafana afin d'éviter de copier/coller depuis Graylog vers MISP.

Dans l'onglet "Homes > Connections > Data sources", nous choisissons Wazuh.

<figure><img src=".gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>

On clique sur "Save & Test" pour terminer la création du lien. À ce stade, on peut constater que le log comporte un deuxième lien (bouton "View in MISP").

<figure><img src=".gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

En cliquant sur le bouton "View in MISP", nous sommes redirigés vers MISP sur cet événement spécifique.

<figure><img src=".gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Il est tout à fait possible d'utiliser la même approche pour effectuer du threat hunting sur les adresses IP malveillantes connues de MISP. En intégrant MISP à Graylog et en visualisant les données avec Grafana  on peut créer des liens ou des requêtes qui nous permettent de rapidement accéder aux informations pertinentes sur les adresses IP malveillantes détectées dans nos logs ouévénements de sécurité. Cela peut grandement faciliter le processus de recherche de menaces et d'investigation en fournissant un accès direct aux informations de renseignement sur les menaces..
{% endhint %}
