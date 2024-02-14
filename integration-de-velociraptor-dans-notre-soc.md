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

# Intégration de Velociraptor dans notre SOC

Velociraptor est une plateforme unique et avancée de surveillance des terminaux, d'investigation numérique et de réponse aux incidents. &#x20;

Velociraptor offre une manière puissante et efficace de rechercher des artefacts spécifiques et de surveiller les activités sur des flottes de terminaux.

Voici quelques fonctionalités que fournit Velociprator :&#x20;

* Reconstituer les activités des attaquants grâce à l'analyse criminalistique numérique&#x20;
* Rechercher des preuves de l'existence d'APT&#x20;
* Enquêter sur les logiciels malveillants et autres activités suspectes sur le réseau&#x20;
* Surveiller en permanence les activités suspectes des utilisateurs, telles que les fichiers copiés sur des périphériques USB.&#x20;
* Déterminer si la divulgation d'informations confidentielles a eu lieu en dehors du réseau&#x20;
* Recueillir des données sur les terminaux au fil du temps pour les utiliser dans la chasse aux menaces et les enquêtes futures.&#x20;

Cette section traitera de l'intégration de Velociraptor dans notre SOC pour renforcer nos capacités de réponse aux incidents et de surveillance.

#### Installation du serveur Velociraptor

Nous débutons en déployant le serveur Velociraptor. Tous les terminaux se connecteront au serveur via l'agent Velociraptor. Le serveur Velociraptor fournit une interface Web.

**Téléchargement du binaire**

Nous téléchargeons le binaire depuis le site officiel de Velociraptor tout en nous assurant de la compatibilité  avec notre système d'exploitation.

```
wget https://github.com/Velocidex/velociraptor/releases/download/v0.6.4-2/velociraptor-v0.6.4-2-linux-amd64
```

Après le téléchargement, nous rendons le binaire exécutable en utilisant la commande chmod.

```bash
chmod +x velociraptor-v0.6.4-2-linux-amd64
```

**Générer le fichier de configuration du serveur**

Nous générons le fichier de configuration du serveur en utilisant la commande suivante :

```bash
./velociraptor-v0.6.4-2-linux-amd64 config generate -i
```

<figure><img src=".gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

**Création du package .deb**

À l'aide du fichier de configuration du serveur, nous créons un package .deb qui installera le service Velociraptor Server  Ubuntu 20.04.

```bash
./velociraptor-v0.6.4-2-linux-amd64 --config server.config.yaml debian server --binary velociraptor-v0.6.4-2-linux-amd64
```

**Installation du package .deb et démarrage du service Velociraptor**

Nous installons le nouveau package .deb et démarrons le service Velociraptor.

```bash
dpkg -i velociraptor_0.6.4-2_server.deb
systemctl start velociraptor_server
```

**Accés à l'interface Web**

Lorsqu'on suit par defaut les options d'installation du serveur Velociraptor , l'interface d'ecoute est la loopback.

il faut modifier le fichier /etc/velocirptor/server.config.yaml pour que le serveur puisse ecouter sur une interface specifique

<figure><img src=".gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

Apres ces modifications on peut lancer l'interface graphique du serveur

<figure><img src=".gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>

#### Installation du client Velociraptor

Maintenant que notre serveur est installé et opérationnel, nous configurons les packages d'agent pour qu'ils puissent fonctionner sur nos terminaux.

**Création du package client .deb**

Nous créons notre propre package client préconfiguré avec le contenu du fichier client.config.yaml.

```bash
./velociraptor-v0.6.4-2-linux-amd64 --config client.config.yaml debian client
```

**Transfert et installation du package client sur Linux**

Nous transférons le nouveau package client .deb sur notre terminal linux et l'installons.

```bash
scp velociraptor_0.6.4-2_client.deb phoenix@192.168.1.52:/tmp/
dpkg -i velociraptor_0.6.4-2_client.deb
styemctl status velociraptor_client
```

<figure><img src=".gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>

En retournant sur notre serveur Velociraptor, nous constatons qu'un nouveau client est apparu et enregistré.

<figure><img src=".gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>

#### Conclusion

Velociraptor est une plateforme de réponse aux incidents extrêmement puissante et gratuite qui peut être utilisée pour un certain nombre de tâches. La multitude d'artefacts fournis permet aux utilisateurs de collecter des fichiers, de rechercher des intrusions, de mettre en quarantaine des hôtes et d'exécuter des tâches de remédiation à distance. Déployez Velociraptor dès aujourd'hui pour fournir une plateforme de réponse aux incidents évolutive, gratuite et rapide.
