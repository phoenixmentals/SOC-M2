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

# Configuration des agents

**Introduction**

L'installation de l'Agent Wazuh est simple, voici lzs etapes sur la manière dont l'Agent Wazuh communique avec Wazuh Manager.

<figure><img src=".gitbook/assets/Wazuh Manager.png" alt=""><figcaption></figcaption></figure>

1. **Enregistrement :**
   * Lorsque l'Agent Wazuh démarre pour la première fois, il initie la communication avec le Gestionnaire Wazuh.
   * Le Gestionnaire et l'Agent s'authentifient mutuellement pour établir une connexion sécurisée.
   * Si une authentification est nécessaire (utilisation d'un mot de passe ou d'une authentification basée sur un certificat), le Gestionnaire vérifie l'identité de l'Agent.
   * Une fois vérifié, le Gestionnaire génère une clé client (une clé symétrique) partagée de manière sécurisée avec l'Agent.
2. **Transfert de journaux :**
   * Avec une clé client valide en place, l'Agent Wazuh commence à transférer les journaux vers le Gestionnaire Wazuh.
   * Les journaux sont chiffrés à l'aide de la clé client partagée pour assurer la confidentialité pendant la transmission.
   * Le transfert de journaux se fait généralement via TCP sur le port 1514.
3. **Ports d'écoute sur le Gestionnaire Wazuh :**
   * Le Gestionnaire Wazuh écoute les connexions entrantes des Agents sur des ports spécifiques.&#x20;
   * Le port par défaut pour les messages de journaux entrants est  le 1514.

**Installation des agents**

Pour installer les agents on va utiliser l'interface graphique de Wazuh et générer, Wazuh nous permet de générer des commandes (PowerShell et Bash) à exécuter sur les terminaux pour installer automatiquement les agents.

Ci-dessous les captures illustrant la procédure.

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

A la fin de cette étape il ne reste plus qu' à executer le script powershell generé par Wazuh  et à demarrer le service sur la machine dont nous voulons collecter les journaux

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.7.1-1.msi -OutFile ${env.tmp}\wazuh-agent; msiexec.exe /i ${env.tmp}\wazuh-agent /q WAZUH_MANAGER='192.168.1.152' WAZUH_REGISTRATION_PASSWORD='agent' WAZUH_AGENT_GROUP='Windows' WAZUH_AGENT_NAME='My_Windows' WAZUH_REGISTRATION_SERVER='192.168.1.152' 
```

La plupart du temps il faut ajouter une règle sur le pare-feu (Local ou Réseau) pour autoriser le Traffic entre l'agent et le serveur.
