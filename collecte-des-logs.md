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

# Collecte des Logs

## Wazuh Manager&#x20;

Dans un environnement comportant des milliers de terminaux, il devient impraticable de se connecter individuellement à chaque terminal pour analyser ses journaux.&#x20;

Nous avons maintenant besoin d'un EDR qui enregistrera les activités et les événements se produisant sur les terminaux , ce qui nous donnera la visibilité nécessaire pour découvrir des incidents qui, autrement, resteraient invisibles.&#x20;

Une solution EDR doit fournir une visibilité continue et complète de ce qui se passe sur les postes de travail en temps réel.

Elle est composée de deux éléments clés :&#x20;

* **Agent** : Collecte les journaux sur les terminaux et les transmet au serveur
* **Serveur** : Reçoit les journaux et analyse les activités malveillantes.

Grâce à Wazuh Manager, nous avons la capacité de collecter les journaux de nos terminaux, de les centraliser et ensuite de les stocker.

Wazuh Manager agit comme le système central qui analyse les données reçues de tous les agents enregistrés, déclenchant des alertes en cas de correspondance avec une règle spécifiée.&#x20;

Par exemple, il peut détecter une intrusion, un fichier modifié, une configuration non conforme à la politique, la possibilité d'un rootkit, entre autres scénarios. En plus de son rôle en tant que gestionnaire, il fonctionne également comme un agent sur la machine locale, ce qui signifie qu'il possède toutes les fonctionnalités d'un agent. De plus, le gestionnaire peut transmettre les alertes qu'il déclenche via Syslog, e-mails ou des API externes intégrées.

### **Fonctionnalités**

* Analyse des données du journal&#x20;
* Surveillance de l'intégrité des fichiers&#x20;
* Détection des vulnérabilités&#x20;
* CIS Benchmark Assessment
* Conformité réglementaire

### Installation de Wazuh Manager

**Prérequis**

```bash
apt-get install gnupg apt-transport-https
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```

**Installation des paquets**

```bash
apt-get update
apt-get -y install wazuh-manager
systemctl daemon-reload
systemctl enable wazuh-manager
systemctl start wazuh-manager
```

**Fonctionnement**

Les agents situés sur les hôtes collectent les journaux locaux et les transmettent à Wazuh Manager.

Wazuh Manager analyse à son tour les journaux reçus à l'aide de ses règles préétablies.&#x20;

En cas de corrélation avec une règle spécifique, les journaux sont enregistrés dans le fichier <mark style="color:blue;">**/var/ossec/logs/alerts/alerts.json**</mark>

Notre objectif  est de pouvoir acheminer le contenu du fichier "<mark style="color:blue;">**/var/ossec/logs/alerts/alerts.json**</mark>" vers notre serveur Graylog afin de réaliser le parsing et la normalisation des logs. Pour accomplir cette tâche, nous allons nous servir de  **Fluent Bit**.

### **Fluent Bit**

Fluent Bit est un système de collecte et de traitement de logs (journaux) open source et léger. Il est conçu pour être performant, extensible et facile à intégrer dans divers environnements.&#x20;

il nous permettra de récupérer les logs depuis le fichier <mark style="color:blue;">**/var/ossec/logs/alerts/alerts.json**</mark>, de les transformer au besoin, puis de les acheminer vers Graylog pour une analyse ultérieure.

Ce processus garantit une gestion centralisée et efficace des journaux, avec la possibilité d'utiliser des outils tels que Graylog pour une analyse approfondie et une interprétation simplifiée des données collectées.

**Installation et Configuration de Fluent Bit**

```bash
curl https://raw.githubusercontent.com/fluent/fluent-bit/master/install.sh | sh
```

Pour configurer Fluent Bit pour collecter le fichier `alerts.json` et l'envoyer à Graylog, nous devons modifier le fichier <mark style="color:blue;">/etc/fluent-bit/fluent-bit.conf</mark> et utiliser les entrées suivants :&#x20;

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Et on démarre le service&#x20;

```bash
systemctl enable fluent-bit
systemctl start fluent-bit
```



### Sécurisation et configuration de Wazuh Manager&#x20;

Pour sécuriser notre installation de Wazuh Manager, nous allons en premier lieu exiger des agents un enregistrement avec mot de passe, de cette façon seuls les agents connaissant le mot de passe pourront s'enregistrer auprès de notre serveur Wazuh-Manager

### Enregistrement avec mot de passe&#x20;

1. Modification du fichier <mark style="color:blue;">`/var/ossec/etc/ossec.conf`</mark>&#x20;

<figure><img src=".gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>

2.  Création du fichier <mark style="color:blue;">`/var/ossec/etc/authd.pass`</mark> et ajout de mot de passe&#x20;



    ```
    echo "<motdepasse>" > /var/ossec/etc/authd.pass
    ```
3. Changement des permissions et du proprietaires sur le fichier <mark style="color:blue;">`/var/ossec/etc/authd.pass`</mark>

### Détection des vulnérabilités avec Wazuh

En deuxième lieu nous allons activer l'audit des vulnérabilités sur les terminaux, pour ce faire nous allons modifier le fichier <mark style="color:blue;">`/var/ossec/etc/ossec.conf`</mark> en rajoutant le bloc de code à l'adresse suivante dans la partie <mark style="color:red;">\<vulnerability-detector>     \</vulnerability-detector> .</mark>

{% embed url="https://gist.githubusercontent.com/taylorwalton/4dc30a634bb938122aeac8820e67166e/raw/62c0de0b3eaaeb202458b0dd9eeae1ee2065d54a/vuln_detection%20ossec.conf" %}

### Configuration centralisée des agents

Enfin Wazuh Manager permet de configurer les agents de façon centralisée, dans notre cas nous allons grouper les configurations des agents selon le Système d'exploitation (**Windows et Linux**)

#### Windows

Sur Windows nous allons modifier le fichier <mark style="color:blue;">`/var/ossec/etc/ossec.conf`</mark>en rajoutant le bloc de code à l'adresse suivante dans la partie <mark style="color:red;">\<agent\_config>          \</agent\_config></mark>

{% embed url="https://gist.githubusercontent.com/taylorwalton/db7035a2bd248c7ce0960c6a4ea1510b/raw/8589f43fd66106f9a52530e62b37c751eb6fac7c/windows%20agent%20group" %}

#### Linux

Sur Linux nous allons modifier le fichier <mark style="color:blue;">`/var/ossec/etc/ossec.conf`</mark>en rajoutant le bloc de code à l'adresse suivante dans la partie <mark style="color:red;">\<agent\_config>          \</agent\_config></mark>

{% embed url="https://gist.githubusercontent.com/taylorwalton/93868507d72b3d40ad2e1318d5983e77/raw/be4f6457c5f60dd04d098cac57754422beef88de/linux%20agent%20group" %}

**Transfert des journaux à Graylog**

Avec Wazuh Server  en place, la prochaine étape est d'envoyer  les alertes Wazuh à Graylog pour qu'il puisse les traiter et écrire les journaux dans Wazuh-Indexer pour le stockage et la recherche.

Nous allons procéder comme suit :&#x20;

1. Ouverture de session GUI Graylog
2. Navigation dans l'onglet "System"
3. Input
4. Raw/Plaintext TCP&#x20;
5. Launch New Input&#x20;

Et definition des paramètres sur la capture suivante

<figure><img src=".gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>
