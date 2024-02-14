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

# IRIS DFIR Platforme

## Introduction

La plateforme IRIS DFIR est une plateforme d'investigation numérique et de réponse aux incidents conçue pour aider à répondre aux incidents de sécurité.&#x20;

La plateforme propose généralement une gamme de fonctionnalités adaptées à ces tâches, notamment :

**La Collecte de données** : IRIS permet aux utilisateurs de collecter et d'analyser des données provenant de diverses sources telles que les points d'extrémité, le trafic réseau, les journaux, la mémoire, etc. Il prend en charge plusieurs méthodes d'acquisition de données pour recueillir des preuves pertinentes à une enquête.

**L'analyse Forensique** : La plateforme aide à analyser les données collectées pour identifier les indicateurs de compromission (IOC), les activités suspectes et les menaces potentielles pour la sécurité. Cela inclut l'analyse de fichiers, l'analyse de mémoire, l'analyse de registre et d'autres techniques forensiques.

**L'orchestration de la réponse aux incidents** : IRIS offre souvent des capacités d'orchestration de la réponse aux incidents, permettant aux utilisateurs d'automatiser et de rationaliser les actions de réponse aux incidents de sécurité. Cela peut inclure des actions telles que l'isolement des systèmes compromis, le blocage du trafic malveillant .

**La gestion des cas** : Il fournit une interface centralisée pour gérer et suivre les enquêtes, y compris la création de cas, l'attribution, le suivi de la progression, la gestion des preuves et la génération de rapports.

**L'intégration des reinseignements sur les menaces** : L'intégration avec des flux d'intelligence sur les menaces permet à IRIS d'enrichir les données d'enquête avec des informations sur les menaces connues, les signatures de logiciels malveillants et d'autres sources d'intelligence sur les menaces pertinentes.

**Outils de collaboration** : La plateforme comprend souvent des fonctionnalités de collaboration pour faciliter la communication et la collaboration entre les membres de l'équipe impliqués dans le processus de réponse aux incidents. Cela peut inclure des tableaux de bord partagés, des fonctionnalités de chat et des outils d'annotation.

**Conformité et reporting** : IRIS aide les organisations à se conformer aux exigences réglementaires en générant des rapports complets détaillant les conclusions des enquêtes, les actions entreprises et l'état de conformité.



**Installation**

On va utiliser docker pour installer le module,&#x20;

```bash
# Clonage du dépôt iris-web à partir de GitHub
git clone https://github.com/dfir-iris/iris-web.git
cd iris-web

# Passage à la dernière version taguée non-bêta
git checkout v2.4.5

# Copie du fichier d'environnement 
cp .env.model .env

# Construction des conteneurs Docker
docker compose build

# Exécution d'IRIS
docker compose up
```

L'installation s'est bien deroulé et notre plateforme est disponible.

<figure><img src=".gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Il faut maintenant trouver le mot de passe par défaut qui est generé dans les logs d'installation du conteneur  de l'application web.

<figure><img src=".gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

On va taper la commande suivante en prenant soin de remplacer l'ID du conteneur docker&#x20;

```
docker logs ef7c3577643d --follow
```

<figure><img src=".gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

Voici la page d'acceuil de notre plateforme d'investigation numerique et de reponse aux incidents .

<figure><img src=".gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

Il est tout à fait possible de créer des cas manuellement mais ce qui nous intéresse c'est de pouvoir automatiser le processus avec shuffle.

**Integrations**

L'architecture d'IRIS DFIR est conçue avec une approche intégrée, offrant une la possiblité d'integrer  des modules  pour une collaboration fluide avec d'autres outils , comme Cortex et bien d'autres.&#x20;

Cette intégration harmonieuse permet une synergie efficace entre les fonctionnalités d'IRIS DFIR et les capacités complémentaires des plateformes externes. En combinant ces modules, les analystes SOC disposent d'un ensemble robuste d'outils pour détecter, analyser et répondre aux menaces de manière proactive et stratégique. Cette interconnectivité renforce la capacité globale d'IRIS  à relever les défis complexes de la sécurité informatique dans un environnement en constante évolution.

**Cortex**

Pour intégrer IRIS DFIR à Cortex, on procède en clonant le dépôt Git suivant dans le répertoire /opt/iris-modules/, puis en suivant les indications qui y sont fournies.

{% embed url="https://github.com/socfortress/iris-cortexanalyzer-module.git" %}

Ensuite, on accède à l'interface graphique de notre serveur IRIS pour ajouter le module. L'image ci-dessous résume les étapes nécessaires.

<figure><img src=".gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

IRIS propose par défaut différents modules tels que MISP et d'autres encore. Cependant, dans notre cas, nous n'allons pas les utiliser, mais plutôt nous appuyer sur Cortex. L'avantage de Cortex réside dans sa facilité à intégrer plusieurs analyseurs en un seul clic, contrairement à IRIS. Cette fonctionnalité nous permettra, par exemple, de lancer et de modifier notre analyseur configuré sur Cortex directement depuis IRIS.

À cette étape, nous configurons les connexions et les clés API pour accéder à Cortex.

<figure><img src=".gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

D'un autre coté on a génèré une clé API sur Cortex qui va être utilisé par IRIS

<figure><img src=".gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

On peut lancer un test depuis IRIS pour vérifier si l'intégration fonctionne correctement. Pour ce faire, on commence par créer un cas, puis un indicateur de compromission, et on lance une analyse sur cet indicateur de compromission (dans notre cas, une adresse IP).

<figure><img src=".gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Après la création de l'indicateur, nous pouvons analyser notre indicateur de compromission en suivant les étapes indiquées sur l'image suivante.

<figure><img src=".gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

On retourne sur notre IoC et on visualise le rapport de notre analyseur (Virus Total)

<figure><img src=".gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Nous avons achevé l'intégration avec Cortex, ce qui nous permettra d'interroger plusieurs sources directement depuis IRIS.

## Les profils et privilèges utilisateurs

Iris gère les profils et les privilèges des utilisateurs dans la mise en place de la plateforme SOC en suivant plusieurs étapes clés :

1. **Identification des Profils Utilisateurs :** Iris identifie les différents types d'utilisateurs qui auront accès à la plateforme SOC. Cela peut inclure des analystes de sécurité, des administrateurs système, des gestionnaires de niveau supérieur, etc.

<figure><img src=".gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

1. **Définition des Niveaux de Privilèges :** Iris établit des niveaux de privilèges différents pour chaque profil utilisateur. Par exemple, un analyste de sécurité pourrait avoir un accès plus étendu aux données et aux fonctionnalités de la plateforme qu'un technicien de support.
2. **Contrôle d'Accès :** Iris met en place des mécanismes de contrôle d'accès pour garantir que chaque utilisateur n'a accès qu'aux données et aux fonctionnalités qui sont nécessaires à l'exécution de ses tâches spécifiques. Cela inclut la limitation des privilèges en fonction du rôle de chaque utilisateur.
3. **Gestion des Privilèges :** Iris assure une gestion proactive des privilèges en surveillant et en ajustant les niveaux d'accès des utilisateurs au fur et à mesure que leurs rôles évoluent ou changent au sein de l'organisation.
4. **Audit et Suivi :** Iris effectue des audits réguliers pour s'assurer que les profils et les privilèges des utilisateurs sont correctement configurés et respectent les politiques de sécurité de l'organisation. Elle surveille également les activités des utilisateurs pour détecter tout comportement anormal ou toute tentative d'accès non autorisé.

<figure><img src=".gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

En résumé, la gestion des profils et des privilèges des utilisateurs par Iris dans la mise en place de la plateforme SOC permet de garantir un contrôle strict de l'accès aux données et aux fonctionnalités critiques, contribuant ainsi à renforcer la sécurité globale de l'environnement informatique d'une l'organisation.
