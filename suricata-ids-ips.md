# Suricata IDS/IPS

**Introduction**

L'objectif de mon projet est d'intégrer Suricata, un système de détection et de prévention d'intrusions en temps réel (IDS/IPS) open source et multiplateforme à mon SOC. Suricata est spécifiquement conçu pour surveiller le trafic réseau et détecter les activités suspectes ou malveillantes, telles que les attaques par exploitation de vulnérabilités, les tentatives d'intrusion, les scans de ports, etc.

En ce qui concerne la gestion des informations, Suricata génère quatre fichiers de logs distincts :

1. **suricata.log** : contient les messages de démarrage de Suricata.
2. **stats.log** : offre des statistiques sur le trafic réseau observé.
3. **fast.log** : répertorie les activités suspectes découvertes par Suricata.
4. **eve.json** : présente le trafic de votre réseau local ainsi que les activités suspectes au format JSON, offrant une vue détaillée des événements détectés.

**Installation**

**Prérequis**

Pour commencer l'intégration de Suricata dans notre environnement,on s'assure de disposer des éléments suivants :

1.  **Installation des outils essentiels :** Pour installer les outils nécessaires, on exécute la commande suivante :

    ```
    sudo apt install gnupg2 software-properties-common curl wget git unzip -y
    ```
2.  **Ajout du référentiel Suricata :** Ajoutons le référentiel Suricata à notre système en exécutant la commande :

    ```bash
    sudo add-apt-repository ppa:oisf/suricata-stable --yes
    ```
3.  **Mise à jour des référentiels :** Après avoir ajouté le référentiel Suricata, on met à jour la liste des paquets disponibles en exécutant :

    ```sql
    sudo apt update
    ```
4.  **Vérification du paquet Suricata :** Avant d'installer Suricata, vérifions les détails du paquet à l'aide de la commande :

    ```bash
    sudo apt-cache policy suricata
    ```
5.  **Installation de Suricata :**&#x20;

    ```
    apt install suricata jq
    ```

Une fois Suricata installé, on configure ses paramètres pour qu'il corresponde à notre environnement. Pour ce faire, on doit modifier le fichier de configuration principal de Suricata, situé à `/etc/suricata/suricata.yaml`, et ajuster certaines valeurs telles que l'adresse du réseau à surveiller et la carte réseau du serveur IDS.

<figure><img src=".gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (122).png" alt=""><figcaption></figcaption></figure>

une fois l'installation de Suricata terminée, on peut mettre à jour sa configuration et vérifier que tout est correctement configuré. Voici les étapes :

1.  **Mise à jour de la configuration de Suricata :** Pour mettre à jour la configuration de Suricata, on exécute la commande suivante :

    ```sql
    suricata-update
    ```
2.  **Vérification de la configuration de Suricata :** Pour vérifier la configuration de Suricata, on utilise la commande suivante :

    ```r
    suricata -T -c /etc/suricata/suricata.yaml -v
    ```

Cette commande va tester la validité de la configuration en spécifiant le chemin vers le fichier de configuration principal de Suricata (suricata.yaml). Elle vérifie également la syntaxe et la cohérence des paramètres définis dans ce fichier.

<figure><img src=".gitbook/assets/image (123).png" alt=""><figcaption></figcaption></figure>

*   **Ajout de nouvelles règles :** On peut ajouter de nouvelles règles provenant de sources spécifiques. Voici comment procéder :

    *   Mettre à jour la liste des sources disponibles :

        ```sql
        sudo suricata-update update-sources
        ```
    *   Vérifier les sources activées :

        ```sql
        sudo suricata-update list-enabled-sources
        ```



    <figure><img src=".gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

    *   Activer une source de règles spécifique, par exemple, la source de règles oisf/trafficid :

        ```bash
        sudo suricata-update enable-source oisf/trafficid
        ```

Avant de continuer, voici quelques notions importantes à retenir :

1.  **Activation du démarrage de Suricata au démarrage du système :** Pour activer le démarrage automatique de Suricata au démarrage du système, on exécute la commande suivante :

    ```bash
    sudo systemctl enable --now suricata
    ```
2.  **Vérification du statut de Suricata :** Pour vérifier le statut de Suricata et s'assurer qu'il fonctionne correctement, on utilise la commande :

    ```lua
    sudo systemctl status suricata
    ```

#### Mettre à jour les règles

Voici les instructions pour mettre à jour les règles de Suricata et pour automatiser cette mise à jour, ainsi qu'un test pour vérifier si Suricata peut détecter une intrusion :

```
sudo suricata-update update-sources
sudo systemctl restart suricata.service
```

**Mis a jour automatique des regles**

On peut automatiser la mise à jour des règles pour réduire la charge de travail en créant des tâches planifiées à l'aide de cron. Pour ce faire, on édite le fichier des planifications en utilisant la commande :

```bash
crontab -e
```

Ensuite, on insère la ligne suivante à la fin du fichier pour programmer la mise à jour automatique des règles :

```bash
37 1 * * * sudo suricata-update --disable-conf=/etc/suricata/disable.conf &amp;&amp; sudo systemctl restart suricata.service
```

Cette ligne indique à cron de mettre à jour les règles de Suricata tous les jours à 1h37 du matin. Assurez-vous que le fichier des règles à désactiver (disable.conf) est correctement spécifié.

**Test de suricata**&#x20;

Pour tester Suricata et vérifier s'il peut détecter une intrusion, on utilise un terminal et on lance la commande suivante :

```
curl http://testmynids.org/uid/index.html
```

<figure><img src=".gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

D'un autre coté on regarde le fichier des logs pour voir si notre IDS peut voir cette intrusion

<div align="center" data-full-width="false">

<figure><img src=".gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

</div>

Integration avec Graylog

Pour intégrer Suricata à Graylog et pouvoir collecter ses journaux (logs), c'est assez facile. Nous allons nous dervir des fichiers de journaux . Nous allons utiliser Fluent-Bit pour envoyer le contenu du fichier `/var/log/eve.json` à Graylog pour l'ingestion et l'analyse.

Voici ma configuration du fichier `fluent-bit.conf` pour le transfert du fichier.

<figure><img src=".gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

Sur notre serveur Graylog, nous allons créer un Input, comme nous l'avons fait avec le serveur Wazuh, mais cette fois, nous allons changer le type d'input et utiliser Raw/TCP.&#x20;

Deuxieme point pour nous est de séparer les entrées des logs de notre IDS et des logs  de nos terminaux pour une meilleure gestion.

Le fichier eve.json de Suricata envoie le trafic réseau local ainsi que les activités suspectes au format JSON.

<figure><img src=".gitbook/assets/image (67).png" alt=""><figcaption><p>Capture des logs de l'IDS</p></figcaption></figure>

Nous constatons que les journaux parviennent au serveur Graylog sans problème. Avant de continuer, nous allons ajouter un champ statique à tous nos journaux. Ce champ nous sera utile ultérieurement lors de la création des flux, comme mot-clé.

<figure><img src=".gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

À présent, il faut créer un index pour les journaux Suricata. Pour une meilleure gestion de stockage et une bonne clarté , je préfère créer un deuxième index des des journaux de mon IDS/IPS pour . La procédure à suivre est simplifiée sur l'image suivante.

<figure><img src=".gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

À cette étape, nous passons à la création des flux qui nous permettront de router les journaux ayant le champ "suricata" vers l'index Suricata. La capture suivante montre la marche à suivre.

<figure><img src=".gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

Ayant terminé ces étapes, on peut voir sur l'image suivante que l'index Suricata a bien été créé et que nous pouvons stocker les journaux relatifs dessus.

<figure><img src=".gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

Par la suite, on pourrait créer des tableaux de bord à l'aide de Grafana pour visualiser le trafic capturé par notre IDS.

