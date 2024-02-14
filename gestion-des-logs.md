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

# Gestion des logs

**Indexes**

Un index est la façon dont Wazuh-Indexer stocke nos logs ingérés. Nous devons créer un index qui stockera toutes nos alertes Wazuh.&#x20;

Graylog offre également la possibilité de configurer les paramètres de l'index, tels que le nombre d'unités, de répliques, etc.

Dans l'onglet system on  clique sur indice et "create a new index set"

<figure><img src=".gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

**Préfixe de l'index** : Nom de l'index utilisé pour stocker les données dans notre indexeur Wazuh.

**Stratégie de rotation** : Fréquence de rotation de l'index. Par exemple, notre premier index créé sera et nommé wazuh-alerts-SOCM2\_0. Une fois que cet index aura atteint une taille de 10 Go, Graylog passera à l'index suivant, wazuh-alerts-SOCM2\_1.

**Stratégie de rétention** : Durée pendant laquelle un index restera dans notre indexeur Wazuh. Par exemple, j'ai une stratégie de rotation de 10 Go et une stratégie de rétention de 10. Cela signifie que je conserverai au maximum 100 Go (10 x 10) de données Wazuh Alerts. Une fois les 10 indices atteints, Graylog supprimera le premier index, wazuh-alerts-SOCM2\_0, pour faire de la place à wazuh-alerts-SOCM2\_11. Gardez à l'esprit que ces données sont définitivement supprimées.

**Flux Graylog**

Les flux de Graylog sont un mécanisme qui permet d'acheminer les messages dans des catégories en temps réel au moment même du traitement. Il est possible de définir des règles dans Graylog pour acheminer les messages vers certains flux.

Les flux nous permettent d'acheminer les messages reçus vers l'index approprié. La création de plusieurs index et de plusieurs flux nous permet d'offrir une solution multi couches.

Dans l'onglet Stream on clique sur "create stream"

<figure><img src=".gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

Nous allons rajouter des champs statiques sur nos flux , dans l'onglet System puis Input  et add static Field

<figure><img src=".gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

Cela ajoutera la paire clé-valeur log\_type:wazuh à chaque log ingéré par notre entrée Wazuh Events Fluent Bit et Nous pourrons utiliser ce nom de champ comme une règle pour notre flux afin de rediriger toutes les alertes Wazuh vers un index approprié.

On retourne dans l'onglet Streams et sur notre flux Wazuh on clique sur "more" et manage rules

<figure><img src=".gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

On clique sur Add stream rule et on rempli les champs suivants

<figure><img src=".gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

On crée les règles de notre flux et on demarre le flux.

On peut voir que les logs sont stockés sur notre index&#x20;

<figure><img src=".gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

Avec cette approche on peut definir notre politique de stockage, de retention des logs et router les logs vers des index appropriés, on pourra si on le desire acheminer les logs du firewall, de l'IDS/IPS sur des index qui leur sont uniques.
