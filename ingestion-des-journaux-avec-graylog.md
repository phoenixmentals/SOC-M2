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

# Ingestion des journaux avec Graylog

Avec notre serveur de stockage en place, nous avons maintenant besoin d'un outil capable d'envoyer  nos journaux au serveur Wazuh-Indexer , notre choix est Graylog.&#x20;

Graylog recevra les journaux de Wazuh-Manager, des dispositifs réseaux (Suricata) ou de tout service disposant d'une option de transfert de Syslog.

On pourrait se passer de Graylog sur cette partie mais il présente beaucoup d'avantages :

*   Prise en charge d'entrées multiples : Graylog permet d'ouvrir des ports d'écoute pour recevoir nos journaux provenant de divers sources , tels que :

    AWS Cloudtrail, Beats, Gelf Syslog et plus
*   Gestion des index : les index sont les éléments constitutifs de la manière dont Elastic et OpenSearch stockent les données. Des problèmes de performances et des limitations de capacité de disque peuvent survenir si des périodes de rétention d'index ne sont pas définis. par exemple, un index peut contenir des données datant de 6 mois. S'il n y a aucune politique de rétention des index  ils resteront sur le disque, ce qui pourrait affecter la capacité à écrire des journaux .

    C'est dans ce cadre spécifique que Graylog intervient pour pallier à ce problème car il permet de définir de façon précise la durée de stockage des index sur le disque
* &#x20;Normalisation des journaux  : la standardisation des données est essentielle, nous devons nous assurer que les champs de données peu importe leur source (Windows, Linux, Firewall,IPS/IDS) soient écris de la même façon pour faciliter leur représentation graphique dans le futur.
* Enrichissement de l'API : automatiser l'analyse de nos journaux avec des données de menaces est une pièce cruciale de tout  SIEM. Nous pouvons utiliser Graylog pour interagir avec MISP, VirusTotal, etc. afin de détecter toute adresse IP, domaine, hachage de fichier malveillant connu qui apparaît dans nos journaux collectés.
* Perte de données minimales : Malheureusement, des défaillances de notre serveur de stockage peuvent se produire. Heureusement, Graylog peut détecter lorsque le stockage est indisponible et écrire les journaux sur son disque jusqu'à ce que le serveur soit de nouveau en ligne.&#x20;

**Installation**&#x20;

Prérequis

```bash
sudo apt update && sudo apt upgrade
sudo apt install apt-transport-https openjdk-11-jre-headless uuid-runtime pwgen dirmngr gnupg wget
```

MongoDB

Graylog utilise MongoDB pour stocker les données de configuration,  telles que les informations sur les utilisateurs ou les configurations de flux. Il peut être installé sur un serveur dédié ou sur le même hôte que Graylog.

```bash
sudo apt-get install gnupg curl
```

Ajout de la clé PGP de MongoDB :

```bash
sudo wget -qO- https://pgp.mongodb.com/server-6.0.asc | sudo gpg --dearmor --batch --yes -o /usr/share/keyrings/mongodb-server-6.0.gpg
```

Configuration des sources APT pour MongoDB

```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

Installation de MongoDB :

```bash
sudo apt-get update 
sudo apt-get install -y mongodb-org 
sudo systemctl daemon-reload 
sudo systemctl enable mongod.service 
sudo systemctl restart mongod.service
```

Graylog

```bash
# Téléchargement du paquet de dépôt Graylog 5.1
wget https://packages.graylog2.org/repo/packages/graylog-5.1-repository_latest.deb

# Installation du paquet de dépôt Graylog
wget https://packages.graylog2.org/repo/packages/graylog-5.1-repository_latest.deb

# Mise à jour des paquets et installation du serveur Graylog
sudo apt-get update && sudo apt-get install graylog-server

# Création du répertoire pour les certificats Graylog
mkdir /etc/graylog/server/certs

# Copie du fichier cacerts de Java vers le répertoire des certificats Graylog
cp -a /usr/lib/jvm/java-11-openjdk-amd64/lib/security/cacerts /etc/graylog/server/certs/cacerts

# Copie du certificat root-ca.pem vers le répertoire des certificats Graylog
cp root-ca.pem /etc/graylog/server/certs/

# Importation du certificat root-ca.pem dans le keystore Graylog
keytool -importcert -keystore /etc/graylog/server/certs/cacerts -storepass changeit -alias root_ca -file /etc/graylog/server/certs/root-ca.pem

# Génération d'un mot de passe sécurisé
pwgen -N 1 -s 96

# Creation du hash de mot de passe root
echo -n "Enter Password: " && head -1 </dev/stdin | tr -d '\n' | sha256sum | cut -d" " -f1

```

Modification du fichier de Configuration

A cette étape nous avons besoin de modifier le fichier de configuration <mark style="color:blue;">/etc/graylog/server/server.conf</mark> pour l'adapter aux resultats des deux dernieres commandes .

<figure><img src=".gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>

Et nous pouvons démarrer Graylog&#x20;

<figure><img src=".gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>
