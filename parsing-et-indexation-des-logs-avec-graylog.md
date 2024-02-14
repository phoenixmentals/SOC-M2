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

# Parsing et Indexation des Logs avec Graylog

Lors de la collecte des logs  nous avons crée une  entrée de flux sur Graylog et configuré Fluent Bit sur Wazuh-Manager pour transférer le contenu du  fichier `/var/ossec/logs/alerts/alerts.json` vers Graylog.&#x20;

Sans cette étape, Graylog ne recevrait pas nos logs de sécurité et aucune donnée ne serait stockée dans Wazuh-Indexer.

En examinant notre entrée Graylog, nous constatons la réception des logs de Wazuh-Server.

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption><p>Logs non Parsés sur Graylog</p></figcaption></figure>

Nous constatons que les logs sont bruts et non parsés, cela nous empeche de creer des dashboards, de détecter des attaques et par conséquent d'y repondre.

Pour remédier à ca nous allons utiliser une fonctionnalité de Graylog (Graylog Ecxtractors) pour parser les logs.

{% hint style="info" %}
[https://archivedocs.graylog.org/en/latest/pages/extractors.html](https://archivedocs.graylog.org/en/latest/pages/extractors.html)
{% endhint %}

**Dans notre cas nous allons utiliser un  Extracteur JSON parceque Wazuh ecrit les logs au format json**

L'avantage de l'extracteur JSON est qu'il va parser les logs automatiquement et Graylog se chargera du reste !

**Configuration de l'Extracteur JSON**&#x20;

Nous cliquons dans le message et ensuite sur create extractor pour selectioner notre type d'extracteur qui est le JSON

<figure><img src=".gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Ensuite nous passons aux configurations de l'extracteur,en faisant attention aux separateurs comme indiqué sur la capture suivante.

<figure><img src=".gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Voici un apercu des logs formatés et lisibles&#x20;

<figure><img src=".gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>
