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

# Analyse des Besoins de Sécurité

**Introduction**

Le SOC (Centre Opérationnel de Sécurité) proposé vise à répondre aux défis de collecte, d'analyse et de stockage des événements de sécurité au moyen d'une approche Open Source. il se compose de plusieurs éléments clés, chacun jouant un rôle spécifique dans le processus de défense cybernétique.

**Éléments Clés d'un SOC**

**1. Ingestion des Journaux :** Avant que les analystes puissent examiner les journaux de sécurité, nous devons les ingérer. Il est crucial de déterminer quelles sources de journaux seront intégrées, allant des journaux des terminaux aux dispositifs réseau, en passant par les proxy, les pare-feu et les services tiers.&#x20;

**2.La normalisation des journaux** : est primordiale pour faciliter le développement ultérieur de tableaux de bord et des stratégies d'alerte.

* **Graylog :** Mon choix pour l'ingestion de journaux. Il collecte des journaux à partir de diverses sources, gère l'indexation des indices stockés sur Wazuh-Indexer, et facilite la gestion des indices.

**3. Analyse des Journaux :** La collecte des journaux est un point de départ essentiel, mais il est tout aussi important d'analyser les détails (métadonnées) pour construire des alertes et prioriser les événements de sécurité.&#x20;

L'analyse des journaux doit permettre de détecter des comportements malveillants.

* **Wazuh :** Un outil collecte les journaux sur les terminaux qui utilise des règles pour analyser le contenu des journaux et détecter les attaques, il offre également des fonctionnalités telles que la surveillance de l'intégrité des fichiers, et la détection des vulnérabilités.

**4. Stockage :** Collecter et analyser les journaux est essentiel, mais le stockage approprié est tout aussi crucial. il doit permettre de stocker, de rechercher et de visualiser les données, tout en garantissant une haute disponibilité, des performances robustes et une évolutivité.

* **Wazuh-Indexer :** Fork d'OpenSearch, il permet le stockage, la recherche et la visualisation des événements de sécurité collectés. Son API riche en fonctionnalités facilite l'intégration avec d'autres outils comme Grafana .

**5. Visualisation :** Stocker les journaux n'est qu'une partie du puzzle. Il est tout aussi important de fournir aux analystes une manière facile de visualiser, trier et rechercher des menaces potentielles. Les outils de visualisation doivent permettre de créer des tableaux de bord, de rechercher rapidement des données et de lire à partir de multiples stockages de journaux.

* **Grafana :**  choisi pour les visualisations. Il offre une personnalisation complète, une rapidité accrue (comparée à Kibana présent par défaut sur Wazuh), des widgets préconstruits et un support assez large.

**6. Enrichissement en Intelligence :** En plus de l'analyse des journaux, il est nécessaire d'enrichir ces journaux avec des informations pour aider les analystes à repérer rapidement une activité malveillante potentielle.

* **Cisco Talos, MISP :** Ces outils permettent d'enrichir les journaux avec des informations provenant de différentes sources de renseignements sur les menaces. Ils offrent une API riche pour automatiser les recherches d'informations sur les menaces.

**7. Gestion des Cas :** À mesure que l'équipe SOC se développe, une plateforme de gestion des cas est nécessaire pour permettre la collaboration, l'enrichissement et la réponse aux alertes. Des playbooks, tâches et procédures doivent être fournis pour guider les analystes SOC à travers les alertes détectées.

* **IRIS - DFIR/Cortex :** Ces outils fournissent une gestion d'incidents, l'organisation, la corrélation des incidents et l'automatisation de l'analyse forensique.

**8. Automatisation :** Avec l'augmentation de la collecte des journaux, l'automatisation devient essentielle pour des tâches telles que la création de cas, l'analyse des attaques de phishing, la génération de rapports, etc.

* **Shuffle :** Une interprétation Open Source de SOAR (Security Orchestration, Automation, and Response). Il vise à apporter toutes les capacités nécessaires pour transférer des données dans toute l'entreprise avec des applications plug-and-play.

**9. Investigation :** Recevoir des alertes n'est que la moitié du travail. Il est nécessaire de fournir aux analystes SOC la possibilité d'investiguer en profondeur les alertes en interagissant avec les terminaux de manière évolutive et rapide.

* **Velociraptor :** Un outil avancé de réponse aux incidents et de collecte de données numériques. Il améliore la visibilité des points de terminaison et permet une collecte ciblée de preuves numériques.

**10. Simulation des attaques** : La simulation d'attaques revêt une importance cruciale dans le domaine de la cybersécurité, offrant aux organisations une opportunité contrôlée d'évaluer l'efficacité de leurs défenses. En reproduisant des scénarios d'attaques réalistes, les équipes de sécurité peuvent identifier les vulnérabilités potentielles, tester la résilience de leurs systèmes et perfectionner leurs stratégies de défense.

* **Atomic Red Team** : il fournit un ensemble d'outils et de tests permettant de simuler des attaques contrôlées, aidant ainsi le SOC à identifier les failles potentielles et à améliorer la posture de sécurité.

**12. IDS/IPS :** Dans le contexte d'un SOC, les systèmes de détection/prévention d'intrusion (IDS/IPS) sont essentiels pour surveiller en temps réel le trafic réseau et détecter les comportements malveillants

* **Suricata**, en tant que NIDS open source, offre une détection proactive des activités suspectes, renforçant ainsi la sécurité du réseau.



**Conclusion**

Avec une multitude d'outils Open Source, nous pouvons construire notre propre stack SOC de manière économique.&#x20;

La stratégie de déploiement, les configurations seront detailles separement pour chaque outil.
