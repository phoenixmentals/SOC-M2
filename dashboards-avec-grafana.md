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

# Dashboards avec Grafana

Grafana se positionne comme l'outil de prédilection pour la visualisation des données dans le domaine de la sécurité informatique. Sa remarquable capacité de personnalisation, autorisant l'utilisation de HTML et CSS sur mesure, offre une adaptabilité exceptionnelle aux besoins spécifiques des utilisateurs. Cette flexibilité s'étend à la personnalisation du branding, garantissant une identité visuelle distinctive sur l'ensemble des tableaux de bord. Grafana excelle également dans la gestion de multiples sources de données, simplifiant l'intégration de logs provenant de diverses origines sans nécessité de centralisation.

Dans le contexte de la sécurité, la réactivité revêt une importance cruciale, et Grafana surpasse Kibana en termes de rapidité de réponse pour les visualisations basées sur Elasticsearch. Cette réputation repose sur sa capacité à fournir des données en temps réel de manière efficace, répondant ainsi aux exigences d'un environnement de sécurité dynamique

**Installation**

```
sudo apt-get install -y software-properties-common wget
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
sudo apt-get update
sudo apt-get install grafana
```

Modification du fichier /etc/grafana/grafana.ini pour inclure les paramètres de configuration adaptés à nos besoins. Certains des paramètres modifiés comprennent :

* **Authentification**
* **Certificats**
* **Adresses IP**

Veuillez ajuster ces paramètres en fonction des exigences spécifiques de votre configuration.

<figure><img src=".gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

Dès la finalisation de l'installation, démarrez le service Grafana avec la commande suivante :

```
systemctl start grafana-server
```

**Configuration de Grafana**

Pour configurer Grafana afin qu'il lise les logs stockés dans le Wazuh Indexer, suivez les captures d'écran ci-dessous, illustrant cette étape.

<figure><img src=".gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

Nous allons à présent incorporer des liens aux champs existants dans les logs. Cela nous permettra de créer des liens dynamiques qui seront utilisés dans la chasse aux menaces (Threat Intelligence).

<figure><img src=".gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

Il est confirmé que Grafana s'est correctement connecté à Wazuh-Indexer. Je tiens à souligner que j'avais préalablement créé un utilisateur nommé "Grafana" sur Wazuh-Indexer.

\
À présent, Grafana a accès aux logs stockés sur Wazuh-Indexer. Nous allons continuer en créant des tableaux de bord personnalisés. J'ai choisi de créer plusieurs tableaux de bord en fonction de besoins spécifiques, notamment celui lié aux connexions réseaux.

Le suivi du trafic et des connexions réseau des terminaux est essentiel pour un SOC, cela nous permet de détecter les destinations suspectes. Pour ce faire, nous devons extraire les données des champs des logs provenant de syslog pour une visualisation plus efficace. Le champ qui nous intéresse est "rule\_groups", que nous allons diviser pour récupérer chaque valeur séparément

<figure><img src=".gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

Voici le résultat des champs lorsqu'ils sont separés.

<figure><img src=".gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

Nous pouvons désormais retourner sur Grafana pour créer des tableaux de bord en utilisant ces champs de données.

**Traffic Reseau**

J'ai opté pour l'utilisation du panneau Sankey pour représenter les flux réseau. Cependant, il n'est pas présent par défaut sur Grafana. Pour l'installer, il suffit d'exécuter la commande suivante :&#x20;

```bash
grafana-cli plugins install netsage-sankey-panel
```

La capture ci-dessous illustre les paramètres utilisés pour configurer un tableau de bord dans Grafana, permettant une surveillance détaillée des connexions réseau effectuées par nos terminaux. L'accent a été mis sur la visualisation des adresses IP sources/destinations et des ports de destination, offrant ainsi une vue précise du trafic réseau.

<figure><img src=".gitbook/assets/image (94).png" alt=""><figcaption><p>Flux reseaux avec le pannel Sankey</p></figcaption></figure>

Dans ce tableau de bord, chaque panneau a été soigneusement configuré pour afficher de manière claire et concise les informations cruciales relatives aux connexions réseau. Les adresses IP sources et destinations sont présentées de manière distincte, facilitant ainsi la détection rapide de tout comportement anormal. De plus, les ports de destination sont également mis en évidence, offrant une compréhension approfondie des activités réseau.

Nous allons étendre nos visualisations en ajoutant une carte qui représente la localisation des adresses IP. À cet effet, nous utiliserons un plugin natif de Graylog appelé Geo-Location Processor.

**Geo-Location Processor**

Le plugin Geo-Location Processor analyse tous les messages à la recherche de champs contenant exclusivement une adresse IP et place leurs informations de géolocalisation (coordonnées, code pays ISO et nom de la ville) dans différents champs. Pour configurer ce plugin, on suit les étapes ci-dessous :

1. Accédez à l'onglet **System > Configuration > Plugins > Geo-Location Processor** dans Graylog.
2. Consultez la documentation de Graylog pour obtenir des informations détaillées sur la configuration et le fonctionnement du Geo-Location Processor.

En intégrant cette fonctionnalité, on peut visualiser la localisation géographique des adresses IP sur la carte, renforçant ainsi votre capacité à comprendre l'origine géographique des connexions réseau et à détecter toute activité suspecte.

<figure><img src=".gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

Et ensuite edit configuration, ci dessous les valeurs à mettre dans les champs de données

<figure><img src=".gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

Pour configurer dleplugin Geo-Location Processor dans Graylogon suit les étapes ci-dessous :

1. Activation du Geo-Location Processor en cochant la case correspondante.
2. Sélection de la base de données GeoIP appropriée. J'ai utilisé MaxMind GeoIP.
3. Spécification du chemin d'accès à la base de données GeoIP. Dans notre configuration, le chemin est /etc/graylog/server/GeoLite2-ASN.mmdb.
4. Configuration l'intervalle de rafraîchissement de la base de données. Dans cette configuration, l'intervalle est défini sur 1 minute

Ces paramètres permettent au Geo-Location Processor d'analyser les messages, d'extraire les adresses IP, et de fournir des informations de géolocalisation telles que les coordonnées, le code pays ISO et le nom de la ville, renforçant ainsi notre capacité à visualiser la localisation géographique des adresses IP sur une carte dans Graylog.

\
Pour importer les fichiers nécessaires à la base de données Geo-Location Processor depuis le repo GitHub et créer le Dashboard de la carte dans Grafana, suivez les étapes ci-dessous :

```bash
cd /etc/graylog/server 
wget https://github.com/socfortress/Wazuh-Rules/releases/download/1.0/GeoLite2-City.mmdb 
wget https://github.com/socfortress/Wazuh-Rules/releases/download/1.0/GeoLite2-ASN.mmdb 
systemctl restart graylog-server
```

**Worldmap Panel**

Pour créer le tableau de bord avec le Worldmap Panel dans Grafana, suivez ces étapes :&#x20;

1. Accédez à l'interface d'administration de Grafana.
2. Dans le menu, cliquez sur "Configuration" (en haut à droite) puis sélectionnez "Plugins".
3. Recherchez "Worldmap Panel" dans la section Plugins.
4. Installez le plugin en cliquant sur le bouton correspondant.
5. Une fois le plugin installé, retournez à l'interface principale de Grafana.

on peut toute aussi l'installer via la ligne de commande suivante

```bash
grafana-cli plugins install grafana-worldmap-panel
sudo service grafana-server restart
```

<figure><img src=".gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>

1. Création du nouveau tableau de bord en cliquant sur "+" dans le menu de gauche.
2. Ajout du panneau en cliquant sur "Add Panel" et choisissez "Worldmap Panel".
3. Configuration du panneau en sélectionnant les champs appropriés pour la géolocalisation des adresses IP. Utilisez les champs que le plugin Geo-Location Processor a ajoutés.
4. Sauvegarde le panneau et ajustement des paramètres du tableau de bord selon les besoins.
5. Nommage du tableau de bord et enregistrement.

<figure><img src=".gitbook/assets/image (99).png" alt=""><figcaption><p>Carte avec des connexions </p></figcaption></figure>

**Histogramme des connexions**

\
Pour ajouter un histogramme permettant de surveiller le nombre de connexions effectuées par agent dans Grafana, suivez ces étapes :

1. Accédez à l'onglet "Dashboards" dans Grafana.
2. Cliquez sur "Ajouter" pour créer un nouveau tableau de bord.
3. Ajoutez un panneau en cliquant sur "Ajouter un panneau" et choisissez "Histogramme".
4. Configurez le panneau en ajustant les configurations selon vos préférences. Certains paramètres à prendre en compte pour l'histogramme des connexions par agent peuvent inclure :
   * **Data Source**: Wazuh
   * **Métrique**: Choisissez la métrique qui représente le nombre de connexions par agent.
   * **Group By**: Utilisez le champ approprié pour grouper les données par agent.
   * **Intervalle de temps**: Définissez l'intervalle de temps pour l'histogramme.
   * **Personnalisation graphique**: Ajustez les couleurs, les étiquettes, etc., selon vos préférences.
5. Une fois que vous avez configuré le panneau, cliquez sur "Appliquer" pour enregistrer les modifications.

<figure><img src=".gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

L'histogramme de connexions par agent est maintenant ajouté au tableau de bord, nous permettant de surveiller efficacement le nombre de connexions effectuées par chaque agent sur le réseau

**Monitoring des Processus réseaux**

Pour mettre en place une visualisation des processus invoquant des connexions réseau dans Grafana, suivez ces étapes :

1. Accédez à l'onglet "Dashboards" dans Grafana.
2. Cliquez sur "Ajouter" pour créer un nouveau tableau de bord.
3. Ajoutez un panneau en cliquant sur "Ajouter un panneau" et choisissez "Jauge"  pour visualiser les processus liés aux connexions réseau.&#x20;
4. Configurez le panneau en ajustant les configurations selon vos préférences. Certains paramètres à prendre en compte pour la visualisation des processus réseau peuvent inclure :
   * **Data Source**: Wazuh
   * **Métrique**: Choisissez la métrique qui représente les processus liés aux connexions réseau.
   * **Group By**: Termes
   * **Intervalle de temps**: Définissez l'intervalle de temps pour la visualisation.
   * **Personnalisation graphique**: Ajustez les couleurs, les étiquettes, etc., selon vos préférences.
5. Une fois que vous avez configuré le panneau, cliquez sur "Appliquer" ou "Sauvegarder" pour enregistrer les modifications.

<figure><img src=".gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

Cette visualisation des processus liés aux connexions réseau nous permettra de détecter des processus anormaux qui ne devraient pas normalement établir de connexions, tels que PowerShell ou d'autres applications potentiellement malveillantes. Ajustez les configurations en fonction de vos besoins spécifiques.

**Intégration avec CISCO Talos pour le Threat Hunting**&#x20;

Pour intégrer Cisco Talos Threat Intelligence dans Grafana afin d'investiguer la réputation d'une adresse IP, suivez ces étapes :

1. Accédez à l'onglet "Dashboards" dans Grafana.
2. Cliquez sur "Ajouter" pour créer un nouveau tableau de bord.
3. Ajoutez un panneau en cliquant sur "Ajouter un panneau" et choisissez "Table" pour représenter les informations de réputation d'adresse IP.&#x20;
4. Configurez le panneau en ajustant les configurations selon vos préférences. Certains paramètres à prendre en compte pour la visualisation de la réputation d'adresse IP peuvent inclure :
   * **Data Source**: Wazuh
   * **Métrique**: Choisissez la métrique qui représente les adresses IP à investiguer.
   * **Group By**: Rule group
   * **Intervalle de temps**: Définissez l'intervalle de temps pour la visualisation.
   * **Personnalisation graphique**: Ajustez les couleurs, les étiquettes, etc., selon vos préférences.
   * **Liens externes**: [https://talosintelligence.com/reputation\_center/lookup?search=${\_\_value.text}](https://talosintelligence.com/reputation\_center/lookup?search=${\_\_value.text})

<figure><img src=".gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

Nous avons mis en place un ensemble de tableaux de bord visant à offrir une vue complète de la sécurité de nos actifs. Ces tableaux de bord permettent une surveillance approfondie, allant de la géolocalisation des adresses IP à la réputation des connexions réseau, en passant par la visualisation des processus et la détection des menaces potentielles. Cette initiative renforce notre capacité à réagir proactivement aux événements de sécurité, en exploitant des ressources telles que le Geo-Location Processor et Cisco Talos Threat Intelligence.

Voici le résultat global de cette configuration.

<figure><img src=".gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>
