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

# Sysmon et Packetbeat

Par défaut, l'Agent Wazuh recueille les journaux Windows et système. C'est assez basique, ce qui  ne fournit pas une vision suffisante de l' activité sur les terminaux.&#x20;

Notre solution EDR doit recueillir des informations telles que les connexions réseau, les lancements de processus, les commandes exécutées, etc., pour que nous puissions détecter de manière précise toute activité malveillante.

Avec notre agent Wazuh on pourra récolter les journaux d'autres services tels que Sysmon et Packetbeat, afin de recueillir davantage d'informations sur nos terminaux.&#x20;

Il nous suffit tout simplement de diriger notre Agent Wazuh vers ces entrées du canaux, et il se chargera du reste !



**Sysmon**

_System Monitor_ (_Sysmon_) est un service système Windows et le pilote de périphérique permanent entre les redémarrages du système pour surveiller et journaliser l’activité du système dans le journal des événements Windows une fois installé sur un système. Il fournit des informations détaillées sur les créations de processus, les connexions réseau et les modifications apportées à l’heure de création de fichier.

source : [https://learn.microsoft.com/fr-fr/sysinternals/downloads/sysmon](https://learn.microsoft.com/fr-fr/sysinternals/downloads/sysmon)

**Installation**

Le script PowerShell suivant permet d'installer Sysmon de facon automatisé sur Windows. [https://gist.githubusercontent.com/taylorwalton/22b3ef3f624edd494ebf640aba56120c/raw/cee2383e91aea2b850bd87c118f20fc010955cf8/sysmon\_install.ps1](https://gist.githubusercontent.com/taylorwalton/22b3ef3f624edd494ebf640aba56120c/raw/cee2383e91aea2b850bd87c118f20fc010955cf8/sysmon\_install.ps1)

Sysmon écrit les évènements sur <mark style="color:blue;">Microsoft-Windows-Sysmon/Operational</mark> dans <mark style="color:blue;">Applications and Services Logs</mark>

Pour que Wazuh puisse recueillir les journaux de Sysmon il faut indiquer le canal approprié dans la configuration du groupe concerné, dans notre cas c'est sur Windows.

On va ajouter le bloc Suivant dans la configuration.

```
<localfile> 
<location>Microsoft-Windows-Sysmon/Operational</location> 
<log_format>eventchannel</log_format> 
</localfile>
```

**Packetbeat**

Packetbeat est un analyseur de paquets réseau en temps réel que vous pouvez utiliser avec Elasticsearch pour créer un système de surveillance d'applications et d'analyse de performances. Packetbeat complète la plateforme Beats en offrant une visibilité entre les serveurs de votre réseau.

Source : [https://www.elastic.co/guide/en/beats/packetbeat/current/packetbeat-overview.html](https://www.elastic.co/guide/en/beats/packetbeat/current/packetbeat-overview.html)

**Installation**

Le script Bash suivant permet d'installer PacketBeat de façon automatisé sur Linux.&#x20;

Packetbit écrit les évènements sur /tmp/packetbeat/packetbeat.

Pour que Wazuh puisse recueillir les journaux de PacketBeat il faut indiquer le canal approprié dans la configuration du groupe concerné, dans notre cas c'est sur Linux.

On va ajouter le bloc Suivant dans la configuration.

```
<localfile>
<log_format>json</log_format>
<location>/tmp/packetbeat/packetbeat</location>
</localfile>
```

Avec ces deux outils en place nous pouvons récolter des journaux plus spécifiques et pertinentes.
