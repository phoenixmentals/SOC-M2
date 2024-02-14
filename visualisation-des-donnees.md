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

# Visualisation des données

## Wazuh Dashboard

Interface web flexible et intuitive, il nous permet d'extraire, d'analyser et de visualiser les données de sécurité. Il fournit des tableaux de bord prêts à l'emploi qui  permettent de naviguer en toute transparence dans l'interface utilisateur, toutefois mon objectif est d'utiliser Wazuh-Dashboard pour pouvoir interagir avec l'API de Wazuh facilement.

Pour les Dashboards  j' utiliserais Grafana pour ses multiples avantages que je vais citer plus tard.

### Installation

```
curl -sO https://packages.wazuh.com/4.7/config.yml
```

**Prérequis**

```
apt-get install debconf adduser procps
apt-get install gnupg apt-transport-https
apt-get install debhelper tar curl libcap2-bin
```

**Import de la clé GPG**

<pre><code>curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.<a data-footnote-ref href="#user-content-fn-1">gpg </a>--import &#x26;&#x26; chmod 644 /usr/share/keyrings/wazuh.gpg
</code></pre>

**Ajout du répertoire**

```
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```

**Mis à jour du pack et installation de Wazuh Dashboard**

```
apt-get update
apt-get -y install wazuh-dashboard
```

**Configuration**

Pour configurer Wazuh-Dashboard il faut modifier le fichier&#x20;

<mark style="color:blue;">**/etc/wazuh-dashboard/opensearch\_dashboards.yml**</mark> plus specifiquement les parametres suivants :&#x20;

<mark style="color:red;">server.host :</mark> paramètre spécifiant l'interface du serveur,  Pour permettre aux utilisateurs distants de se connecter

<mark style="color:red;">opensearch.hosts :</mark> les URL des instances d'indexation de Wazuh à utiliser pour toutes les requêtes.&#x20;

**Déploiement des certificats**&#x20;

Pour commencer nous allons ajouter une variable d'environnement (l'hostname  de notre serveur wazuh dashboard), et poursuivre par les configurations.

> NODE\_NAME=dashboards.phoenix.local

```bash
mkdir /etc/wazuh-dashboard/certs
tar -xf ./wazuh-certificates.tar -C /etc/wazuh-dashboard/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./root-ca.pem
mv -n /etc/wazuh-dashboard/certs/$NODE_NAME.pem /etc/wazuh-dashboard/certs/dashboard.pem
mv -n /etc/wazuh-dashboard/certs/$NODE_NAME-key.pem /etc/wazuh-dashboard/certs/dashboard-key.pem
chmod 500 /etc/wazuh-dashboard/certs
chmod 400 /etc/wazuh-dashboard/certs/*
chown -R wazuh-dashboard:wazuh-dashboard /etc/wazuh-dashboard/certs
```

**Démarrage du service**

```bash
systemctl daemon-reload
systemctl enable wazuh-dashboard
systemctl start wazuh-dashboard
```

on Modifie le fichier <mark style="color:blue;">**/usr/share/wazuh-dashboard/data/wazuh/config/wazuh.yml**</mark> et remplace la valeur url par l'adresse IP de notre  serveur Wazuh.

**Sécurisation de notre installation**

Sur votre nœud Wazuh dashboard, nous exécutons la commande suivante pour mettre à jour le mot de passe kibanaserver dans le keystore Wazuh dashboard

```
echo <kibanaserverpassword> | /usr/share/wazuh-dashboard/bin/opensearch-dashboards-keystore --allow-root add -f --stdin opensearch.password
```

Et on redémarre le service pour prendre en compte ces dernières modifications.

```
systemctl restart wazuh-dashboard
```

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption><p>Wazuh Dashboard</p></figcaption></figure>

[^1]: 
