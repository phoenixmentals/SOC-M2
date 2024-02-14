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

# Intégration de Cortex dans notre SOC

**Introduction**

Nous allons explorer l'intégration de Cortex dans notre SOC afin d'améliorer notre capacité à détecter, répondre et remédier aux menaces de manière efficace et efficiente. Cortex est une plateforme de sécurité automatisée conçue pour renforcer la posture de sécurité des organisations en fournissant des fonctionnalités avancées d'analyse et de réponse aux incidents.

### Objectifs de l'intégration

Notre objectif principal en intégrant Cortex est de centraliser et de rationaliser nos opérations de sécurité. En tirant parti des fonctionnalités avancées de Cortex, nous cherchons à :

* Automatiser les tâches répétitives et chronophages liées à la détection et à l'analyse des menaces.
* Améliorer la visibilité sur notre environnement de sécurité en consolidant les données provenant de différentes sources.
* Renforcer notre capacité à détecter les activités malveillantes et à répondre rapidement aux incidents de sécurité.

### Architecture de l'intégration

L'intégration de Cortex dans notre SOC repose sur une architecture flexible et évolutive. Nous adoptons une approche modulaire pour permettre l'ajout et la configuration de différents modules et outils selon nos besoins spécifiques. L'architecture comprend les composants suivants :

* **Cortex Core Engine**: Le moteur central de Cortex qui gère l'exécution des analyses et des actions.
* **Analyzers**: Des modules d'analyse spécialisés dans la détection de différentes catégories de menaces, tels que les indicateurs de compromission (IOC), les logiciels malveillants, etc.
* **Responders**: Des modules permettant d'effectuer des actions en réponse aux incidents détectés, comme l'isolation de machines, la modification de règles de pare-feu, etc.
* **Interfaces utilisateur**: Des interfaces conviviales pour la gestion et la surveillance des opérations de sécurité.

**Installation**

Cortex nécessite Elasticsearch pour stocker et indexer ses données, avec une compatibilité exclusive avec la version 7.x. Je vais utiliser Docker pour cette installation pour de multiples raisons, dont la simplicité. Cela nous permettra d'éviter de déployer un nœud Elasticsearch uniquement pour Cortex.

1. On cree un fichier  [docker-compose.yml](https://raw.githubusercontent.com/TheHive-Project/Cortex/master/docker/cortex/docker-compose.yml) avec le contenu suivant&#x20;

```
version: "2"
services:
  elasticsearch:
    image: elasticsearch:7.9.1
    environment:
      - http.host=0.0.0.0
      - discovery.type=single-node
      - script.allowed_types=inline
      - thread_pool.search.queue_size=100000
      - thread_pool.write.queue_size=10000
    volumes:
      - /usr/share/elasticsearch/data
  cortex:
    image: thehiveproject/cortex:3.1.1
    environment:
      - job_directory=${job_directory}
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ${job_directory}:${job_directory}
    depends_on:
      - elasticsearch
    ports:
      - "0.0.0.0:9001:9001"
```

2. On cree un fichier .env avec le contenu suivant&#x20;

```
job_directory=/tmp/cortex-jobs
```

On met les deux fichiers dans le même répertoire et on lance la commande docker-compose up. On peut voir sur la capture suivante que notre serveur écoute sur le port 9001.

```
docker-compose up
```

La capture suivante indique que notre serveur est à l'écoute sur le port 9001.

<figure><img src=".gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

On ouvre le navigateur et on commence les configurations de base

<figure><img src=".gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

Premièrement on cree notre organisation ensuite on crée un utilisateur à qui on attribuedes droits basés sur les roles

<figure><img src=".gitbook/assets/image (108).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

**Intégration avec MISP**

Intégrer Cortex à MISP est assez simple. Pour ce faire, on doit générer une clé API dans MISP. On peut utiliser la même clé API que celle utilisée par Graylog, bien que cela soit déconseillé en production.

<figure><img src=".gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

Intégration avec Virus total

VirusTotal est un service en ligne gratuit qui permet d'analyser les fichiers et les URL à la recherche de logiciels malveillants et d'autres menaces potentielles. Il utilise une variété de moteurs antivirus et d'outils de détection pour examiner les fichiers téléchargés et les adresses web afin de détecter les signes de programmes malveillants, de chevaux de Troie, de virus, de vers et d'autres types de logiciels indésirables

Pour intégrer Cortex à VirusTotal, il faut se rendre sur le site officiel [https://www.virustotal.com/](https://www.virustotal.com/), créer un compte, puis demander une clé API à utiliser avec Cortex.

<figure><img src=".gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

Ensuite, sur Cortex, il faut se rendre dans la section Organisation > Configurations des Analyseurs et renseigner la clé générée par VirusTotal dans la partie encadrée en rouge sur l'image.

<figure><img src=".gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

Il faut passer à l'onglet "Analyseurs" pour activer les analyseurs que l'on vient de configurer. L'image suivante illustre la marche à suivre.

<figure><img src=".gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

Il convient d'activer plusieurs analyseurs tels que AbuseIPDB, Malware Bazaar, etc., en fonction des besoins spécifiques. Pour illustrer les capacités de Cortex, nous allons procéder à une analyse d'une adresse IP à titre d'exemple.

<figure><img src=".gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

On constate que les tâches d'analyse ont été effectuées avec succès.

<figure><img src=".gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

Voici le rapport fournit sous format JSON par Virus Total à propos de l'adresse analysé

<figure><img src=".gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

L'intégration de Cortex dans notre SOC  offre des avantages significatifs pour notre posture de sécurité. En reliant Cortex à VirusTotal et en activant divers analyseurs tels que MISP, nous avons élargi notre capacité d'analyse et de réponse aux menaces. Cette intégration permet une évaluation plus approfondie des indicateurs de compromission (IOC) et des adresses IP suspectes, renforçant ainsi notre capacité à identifier, analyser et contrer les menaces de manière proactive.
