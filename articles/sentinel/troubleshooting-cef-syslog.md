---
title: Résoudre les problèmes de connexion entre Microsoft Sentinel et un connecteur de données CEF ou Syslog | Microsoft Docs
description: Découvrez comment résoudre les problèmes liés à votre connecteur de données CEF ou Syslog Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 627660330493b2d26c003145fc41dbadaa780052
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517252"
---
# <a name="troubleshoot-your-cef-or-syslog-data-connector"></a>Résoudre les problèmes liés à votre connecteur de données CEF ou Syslog

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article décrit les méthodes courantes de vérification et de dépannage d’un connecteur de données CEF ou Syslog pour Microsoft Sentinel.

Par exemple, si vos journaux ne s’affichent pas dans Microsoft Sentinel, que ce soit dans les tables Syslog ou Common Security Log, votre source de données risque de ne pas se connecter ou il peut y avoir une autre raison pour laquelle vos données ne sont pas ingérées.

D’autres symptômes d’un déploiement de connecteur défaillant inclus soit l’absence des fichiers **security_events.conf** ou **security-omsagent.config.conf** soit l’absence de réponse du serveur rsyslog sur le port 514.

Pour plus d’informations, consultez [Connecter votre solution externe à l’aide du Format d’évènement commun CEF](connect-common-event-format.md) et [Collecter des données à partir des sources Linux à l’aide de Syslog](connect-syslog.md).

> [!NOTE]
> L’agent Log Analytics pour Windows est souvent appelé *Agent de surveillance Microsoft (MMA)* . L’agent Log Analytics pour Linux est souvent appelé *Agent OMS*.
>

> [!TIP]
> Lors du dépannage, nous vous recommandons de suivre les étapes de cet article dans l’ordre dans lequel elles sont présentées pour vérifier et résoudre les problèmes dans votre Collecteur Syslog, votre système d’exploitation ou votre agent OMS.
>
> Si vous avez déployé votre connecteur à l’aide d’une méthode différente de la procédure documentée et que vous rencontrez des problèmes, nous vous recommandons de vider le déploiement et de l’installer à nouveau comme indiqué.
>

## <a name="validate-cef-connectivity"></a>Valider la connectivité CEF

Une fois que vous avez [déployé votre redirecteur de journal ](connect-common-event-format.md) et [configuré votre solution de sécurité pour lui envoyer des messages CEF](./connect-common-event-format.md), suivez les étapes dans cette section pour vérifier la connectivité entre votre solution de sécurité et Microsoft Sentinel.

1. Assurez-vous que vous disposez des prérequis suivants :

    - Vous devez disposer d’autorisations élevées (sudo) sur votre machine de transfert de journaux.

    - **Python 2.7** ou **3** doit être installé sur votre machine de transfert de journaux. Utilisez la commande `python –version` pour vérifier.

    - Vous pouvez avoir besoin de l’ID et de la clé primaire de l’espace de travail à un moment donné dans ce processus. Vous pouvez les trouver dans la ressource d’espace de travail, sous **Gestion des agents**.

1. Dans le menu de navigation de Microsoft Sentinel, ouvrez **Journaux**. Exécutez une requête à l’aide du schéma **CommonSecurityLog** pour voir si vous recevez les journaux de votre solution de sécurité.

    Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans **Log Analytics**.

1. Si vous ne voyez aucun résultat lors de la requête, vérifiez que des événements sont générés à partir de votre solution de sécurité, ou essayez d’en générer certains, et vérifiez qu’ils sont transférés vers la machine de redirection de syslog que vous avez désignée.

1. Exécutez le script suivant sur le redirecteur de journal (en appliquant l’ID d’espace de travail à la place de l’espace réservé) pour vérifier la connectivité entre votre solution de sécurité, le redirecteur de journal et Microsoft Sentinel. Ce script vérifie que le démon écoute les ports appropriés, que le transfert est correctement configuré, et s’assure que rien ne bloque la communication entre le démon et l’agent Log Analytics. Il envoie également des messages fictifs « TestCommonEventFormat » pour vérifier la connectivité de bout en bout. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - Vous verrez peut-être s’afficher un message vous demandant d’exécuter une commande pour corriger un problème de **mappage du champ *Ordinateur***. Pour plus d’informations, consultez l’[explication dans le script de validation](#mapping-command).

    - Vous verrez peut-être s’afficher un message vous demandant d’exécuter une commande pour corriger un problème d’**analyse de journaux de pare-feu Cisco ASA**. Pour plus d’informations, consultez l’[explication dans le script de validation](#parsing-command).

### <a name="cef-validation-script-explained"></a>Explication du script de validation CEF

Le script de validation effectue les vérifications suivantes :

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. Vérifie que le fichier<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe et est valide.

1. Vérifie que le fichier contient le texte suivant :

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Vérifie que l’analyse des événements de pare-feu Cisco ASA est configurée comme prévu à l’aide de la commande suivante :

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>En cas de problème avec l’analyse, le script génère un message d’erreur qui vous indique d’**exécuter manuellement la commande suivante** (en appliquant l’ID de l’espace de travail au lieu de l’espace réservé). La commande va garantir l’analyse correcte et redémarrer l’agent.

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Vérifie que le champ *Ordinateur* de la source syslog est correctement mappé dans l’agent Log Analytics en utilisant la commande suivante :

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>En cas de problème avec le mappage, le script génère un message d’erreur qui vous indique d’**exécuter manuellement la commande suivante** (en appliquant l’ID d’espace de travail à la place de l’espace réservé). La commande va garantir le mappage correct et redémarrer l’agent.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Vérifie s’il existe des améliorations de sécurité sur la machine qui peuvent bloquer le trafic réseau (par exemple un pare-feu hôte).

1. Vérifie que le démon syslog (rsyslog) est correctement configuré pour envoyer les messages (qu’il identifie en tant que CEF) à l’agent Log Analytics sur le port TCP 25226 :

    - Fichier de configuration : `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
        ```

1. Redémarre le démon syslog et l’agent Log Analytics :

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Vérifie que les connexions nécessaires sont établies : TCP 514 pour la réception de données, TCP 25226 pour la communication interne entre le démon syslog et l’agent Log Analytics :

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Vérifie que le démon syslog reçoit des données sur le port 514 et que l’agent reçoit les données sur le port 25226 :

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Envoie des données fictives au port 514 sur localhost. Ces données doivent être observables dans l’espace de travail Microsoft Sentinel en exécutant la requête suivante :

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. Vérifie que le fichier<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existe et est valide.

1. Vérifie que le fichier contient le texte suivant :

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Vérifie que l’analyse des événements de pare-feu Cisco ASA est configurée comme prévu à l’aide de la commande suivante :

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>En cas de problème avec l’analyse, le script génère un message d’erreur qui vous indique d’**exécuter manuellement la commande suivante** (en appliquant l’ID de l’espace de travail au lieu de l’espace réservé). La commande va garantir l’analyse correcte et redémarrer l’agent.

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Vérifie que le champ *Ordinateur* de la source syslog est correctement mappé dans l’agent Log Analytics en utilisant la commande suivante :

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>En cas de problème avec le mappage, le script génère un message d’erreur qui vous indique d’**exécuter manuellement la commande suivante** (en appliquant l’ID d’espace de travail à la place de l’espace réservé). La commande va garantir le mappage correct et redémarrer l’agent.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Vérifie s’il existe des améliorations de sécurité sur la machine qui peuvent bloquer le trafic réseau (par exemple un pare-feu hôte).

1. Vérifie que le démon syslog (syslog-ng) est correctement configuré pour envoyer les messages qu’il identifie en tant que CEF (à l’aide d’une expression régulière) à l’agent Log Analytics sur le port TCP 25226 :

    - Fichier de configuration : `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Redémarre le démon syslog et l’agent Log Analytics :

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Vérifie que les connexions nécessaires sont établies : TCP 514 pour la réception de données, TCP 25226 pour la communication interne entre le démon syslog et l’agent Log Analytics :

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Vérifie que le démon syslog reçoit des données sur le port 514 et que l’agent reçoit les données sur le port 25226 :

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Envoie des données fictives au port 514 sur localhost. Ces données doivent être observables dans l’espace de travail Microsoft Sentinel en exécutant la requête suivante :

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="verify-cef-or-syslog-prerequisites"></a>Vérifier les prérequis pour CEF ou Syslog

Utilisez les sections suivantes pour vérifier les prérequis pour votre connecteur de données CEF ou Syslog.

### <a name="azure-virtual-machine-as-a-syslog-collector"></a>Machine virtuelle Azure en tant que collecteur Syslog

Si vous utilisez une machine virtuelle Azure en tant que collecteur Syslog, vérifiez les éléments suivants :

- Lorsque vous configurez votre connecteur de données Syslog, veillez à désactiver vos [paramètres d’approvisionnement automatique de Microsoft Defender pour le cloud](../security-center/security-center-enable-data-collection.md) pour l’[agent MMA/OMS](connect-windows-security-events.md#connector-options).

    Vous pouvez les réactiver une fois votre connecteur de données entièrement configuré.

- Avant de déployer le [script Python du Connecteur de données au Format d’événement commun](./connect-log-forwarder.md), assurez-vous que votre Machine virtuelle n’est pas déjà connectée à un espace de travail Syslog existant. Vous pouvez trouver ces informations dans la liste des Machines virtuelles de l’espace de travail Log Analytics, où une machine virtuelle connectée à un espace de travail Syslog est répertoriée comme **connectée**.

- Veillez à ce que Microsoft Sentinel soit connecté au bon espace de travail Syslog, avec la solution **SecurityInsights** installée.

    Pour plus d’informations, consultez [Etape 1: Déployer le redirecteur de journaux](./connect-log-forwarder.md).

- Assurez-vous que la taille de votre machine est correcte avec au moins les prérequis minimum. Pour plus d’informations, voir les [prérequis CEF](connect-common-event-format.md#prerequisites).

### <a name="on-premises-or-a-non-azure-virtual-machine"></a>Machine virtuelle locale ou non-Azure

Si vous utilisez une machine locale ou une machine virtuelle non-Azure pour votre connecteur de données, assurez-vous que vous avez exécuté le script d’installation sur une nouvelle installation d’un système d’exploitation Linux pris en charge :

> [!TIP]
> Vous pouvez également trouver ce script à partir de la page du connecteur de données **Common Event Format** dans Microsoft Sentinel.
>

```cli
sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py <WorkspaceId> <Primary Key>
```

### <a name="enable-your-syslog-facility-and-log-severity-collection"></a>Activez votre service Syslog et la collecte de gravité du journal

Le serveur Syslog, rsyslog ou syslog-ng, transfère toutes les données définies dans le fichier de configuration approprié, qui est automatiquement rempli par les paramètres définis dans votre espace de travail Log Analytics.

Veillez à ajouter des informations sur les niveaux de journalisation de gravité et d’installation que vous souhaitez ingérer dans Microsoft Sentinel. Le processus de configuration peut prendre environ 20 minutes.

Pour plus d’informations, consultez [Explication du script de déploiement](./connect-log-forwarder.md#deployment-script-explained) et [Configuration de Syslog dans le portail Azure](../azure-monitor/agents/data-sources-syslog.md).


**Par exemple, pour un serveur rsyslog**, exécutez la commande suivante pour afficher les paramètres actuels de votre transfert Syslog, puis examinez les modifications apportées au fichier de configuration :

```bash
cat /etc/rsyslog.d/95-omsagent.conf
```

Dans ce cas, pour rsyslog, une sortie similaire à ce qui suit doit s’afficher. Le contenu de ce fichier doit refléter ce qui est défini dans la configuration Syslog sur l’écran **configuration du Client de l’espace de travail Log Analytics - paramètres de l’installation Syslog**.


```bash
OMS Syslog collection for workspace c69fa733-da2e-4cf9-8d92-eee3bd23fe81
auth.=alert;auth.=crit;auth.=debug;auth.=emerg;auth.=err;auth.=info;auth.=notice;auth.=warning  @127.0.0.1:25224
authpriv.=alert;authpriv.=crit;authpriv.=debug;authpriv.=emerg;authpriv.=err;authpriv.=info;authpriv.=notice;authpriv.=warning  @127.0.0.1:25224
cron.=alert;cron.=crit;cron.=debug;cron.=emerg;cron.=err;cron.=info;cron.=notice;cron.=warning  @127.0.0.1:25224
local0.=alert;local0.=crit;local0.=debug;local0.=emerg;local0.=err;local0.=info;local0.=notice;local0.=warning  @127.0.0.1:25224
local4.=alert;local4.=crit;local4.=debug;local4.=emerg;local4.=err;local4.=info;local4.=notice;local4.=warning  @127.0.0.1:25224
syslog.=alert;syslog.=crit;syslog.=debug;syslog.=emerg;syslog.=err;syslog.=info;syslog.=notice;syslog.=warning  @127.0.0.1:25224
```


**Pour le transfert CEF, pour un serveur rsyslog**, exécutez la commande suivante pour afficher les paramètres actuels de votre transfert Syslog, puis examinez les modifications apportées au fichier de configuration :

```bash
cat /etc/rsyslog.d/security-config-omsagent.conf
```

Dans ce cas, pour rsyslog, une sortie similaire à ce qui suit doit s’afficher :

```bash
if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
```

## <a name="troubleshoot-operating-system-issues"></a>Résoudre les problèmes liés au système d’exploitation

Cette procédure décrit comment résoudre les problèmes qui sont certainement dérivés de la configuration du système d’exploitation.

**Pour résoudre les problèmes liés au système d’exploitation** :

1. Si vous ne l’avez pas encore fait, vérifiez que vous utilisez un système d’exploitation et une version de Python pris en charge. Pour plus d’informations, consultez les [prérequis CEF](connect-common-event-format.md#prerequisites) et [Configurer votre machine ou appliance Linux](connect-syslog.md#configure-your-linux-machine-or-appliance).

1. Si votre Machine virtuelle se trouve dans Azure, vérifiez que le groupe de sécurité réseau (NSG) autorise la connectivité TCP/UDP entrante à partir de votre client de journal (expéditeur) sur le port 514.

1. Vérifiez que les paquets arrivent dans le Collecteur Syslog. Pour capturer les paquets Syslog arrivant dans le Collecteur Syslog, exécutez :

    ```config
    tcpdump -Ani any port 514 and host <ip_address_of_sender> -vv
    ```

1. Effectuez l'une des opérations suivantes :

    - Si vous ne voyez pas de paquets arrivant, vérifiez les autorisations du groupe de sécurité NSG et le chemin de routage vers le Collecteur Syslog.

    - Si vous voyez des paquets arrivant, vérifiez qu’ils ne sont pas rejetés.

    Si vous voyez des paquets rejetés, vérifiez que les tables IP ne bloquent pas les connexions.

    Pour confirmer que les paquets ne sont pas rejetés, exécutez :

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. Vérifiez si le serveur Syslog traite les journaux. Exécutez :

    ```config
    tail -f /var/log/messages or tail -f /var/log/syslog
    ```

    Les journaux Syslog ou CEF en cours de traitement sont affichés en texte brut.

1. Vérifiez que le serveur rsyslog est à l’écoute sur le port TCP/UDP 514. Exécutez :

    ```config
    netstat -anp | grep syslog
    ```

    Si des journaux CEF ou ASA sont envoyés à votre Collecteur Syslog, vous devriez voir une connexion établie sur le port TCP 25226.

    Par exemple :

    ```config
    0 127.0.0.1:36120 127.0.0.1:25226 ESTABLISHED 1055/rsyslogd
    ```

    Si la connexion est bloquée, il est possible que vous ayez soit une [connexion SELinux bloquée vers l’agent OMS](#selinux-blocking-connection-to-the-oms-agent), soit un [processus de pare-feu bloqué](#blocked-firewall-policy). Utilisez les ensembles d’instructions suivants pour déterminer le problème.

### <a name="selinux-blocking-connection-to-the-oms-agent"></a>SELinux bloquant la connexion à l’agent OMS

Cette procédure décrit comment vérifier si SELinux est actuellement dans un état `permissive` ou s’il bloque une connexion vers l’agent OMS. Cette procédure s’applique lorsque votre système d’exploitation est une distribution à partir de RedHat ou de CentOS.

> [!NOTE]
> La prise en charge de Microsoft Sentinel pour CEF et Syslog comprend uniquement le renforcement des normes FIPS (Federal Information Processing Standard). D’autres méthodes de renforcement de la sécurité, telles que SELinux ou CIS, ne sont pas prises en charge actuellement.
>

1. Exécutez :

    ```config
    sestatus
    ```

    L’état peut afficher l'une des valeurs suivantes :

    - `disabled`. Cette configuration est prise en charge pour votre connexion à Microsoft Sentinel.
    - `permissive`. Cette configuration est prise en charge pour votre connexion à Microsoft Sentinel.
    - `enforced`. Cette configuration n’est pas prise en charge et vous devez soit désactiver l’état, soit lui affecter la valeur `permissive`.

1. Si l’état est défini sur `enforced`, désactivez-le temporairement pour confirmer s’il s’agissait du bloqueur. Exécutez :

    ```config
    setenforce 0
    ```

    > [!NOTE]
    > Cette étape désactive SELinux uniquement jusqu’au redémarrage du serveur. Modifiez la configuration de SELinux pour la conserver désactivée.
    >

1. Pour vérifier si la modification a réussi, exécutez :

    ```
    getenforce
    ```

    L'état `permissive` doit être retourné.

> [!IMPORTANT]
> Cette mise à jour du paramètre est perdue lors du redémarrage du système. Pour actualiser ce paramètre de manière permanente sur `permissive`, modifiez le fichier **/etc/selinux/config**, en modifiant la valeur de `SELINUX` sur `SELINUX=permissive`.
>
> Pour plus d’informations, consultez la [documentation RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_selinux/changing-selinux-states-and-modes_using-selinux).


### <a name="blocked-firewall-policy"></a>Stratégie de pare-feu bloquée

Cette procédure décrit comment vérifier si une stratégie de pare-feu bloque la connexion entre le démon rsyslog et l’agent OMS, et comment la désactiver si nécessaire.


1. Exécutez la commande suivante pour vérifier s’il existe des rejets dans les tables IP, indiquant que le trafic est abandonné par la stratégie de pare-feu :

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. Pour conserver la stratégie de pare-feu activée, créez une règle de stratégie pour autoriser les connexions. Ajoutez des règles en fonction des besoins pour autoriser les ports TCP/UDP 25226 et 25224 via le pare-feu actif.

    Par exemple :

    ```config
    Every 2.0s: iptables -nvL                      rsyslog: Wed Jul  7 15:56:13 2021

    Chain INPUT (policy ACCEPT 6185K packets, 2466M bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain OUTPUT (policy ACCEPT 6792K packets, 6348M bytes)
     pkts bytes target     prot opt in     out     source               destination
    ```

1. Pour créer une règle en fonction des besoins pour autoriser les ports TCP/UDP 25226 et 25224 via le pare-feu actif.

    1. Pour installer l’éditeur de Stratégie de pare-feu, exécutez :

        ```config
        yum install policycoreutils-python
        ```

    1. Ajoutez les règles de pare-feu à la stratégie de pare-feu. Par exemple :

        ```config
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25226  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p udp --dport 25224  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25224  -j ACCEPT
        ```

    1. Vérifiez l’ajout de l’exception. Exécutez :

        ```config
        sudo firewall-cmd --direct --get-rules ipv4 filter INPUT
        ```

    1. Rechargez le pare-feu. Exécutez :

        ```config
        sudo firewall-cmd --reload
        ```

> [!NOTE]
> Pour désactiver le pare-feu, exécutez : `sudo systemctl disable firewalld`
>

## <a name="linux-and-oms-agent-related-issues"></a>Problèmes liés aux agents Linux et OMS

Si les étapes décrites plus haut dans cet article ne résolvent pas votre problème, il se peut que vous rencontriez un problème de connectivité entre l’agent OMS et l’espace de travail Microsoft Sentinel.

Dans ce cas, poursuivez la résolution des problèmes en vérifiant les éléments suivants :

- Vérifiez que vous pouvez voir les paquets arrivant sur le port TCP/UDP 514 sur le collecteur Syslog

- Assurez-vous que vous pouvez voir les journaux écrits dans le fichier journal local, soit **/var/log/messages** ou **/var/log/syslog**

- Vérifiez que vous pouvez voir les paquets de données qui circulent sur le port 25224, 25226 ou les deux

- Assurez-vous que votre machine virtuelle dispose d’une connexion sortante vers le port 443 via TCP ou peut se connecter aux [points de terminaison de Log Analytics](../azure-monitor/agents/log-analytics-agent.md#network-requirements)

- Vérifiez que vous avez accès aux URL requises à partir de votre collecteur Syslog par le biais de votre stratégie de pare-feu. Pour plus d’informations, consultez [Configuration requise du pare-feu de l’agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements).

- Assurez-vous que votre Machine virtuelle Azure est affichée comme connecté dans la liste des machines virtuelles de votre espace de travail.

Exécutez la commande suivante pour déterminer si l’agent communique avec succès avec Azure, ou si l’agent OMS ne peut se connecter à l’espace de travail Log Analytics.

```config
Heartbeat
 | where Computer contains "<computername>"
 | sort by TimeGenerated desc
```

Une entrée de journal est retournée si l’agent communique avec succès. Dans le cas contraire, l’agent OMS peut être bloqué.

## <a name="next-steps"></a>Étapes suivantes

Si les étapes de dépannage décrites dans cet article n’ont pas contribué à résoudre votre problème, ouvrez un ticket de support ou utilisez les ressources de la communauté Microsoft Sentinel. Pour plus d’informations, consultez [Ressources utiles lorsque vous travaillez avec Microsoft Sentinel](resources.md).

Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- Découvrez le [mappage de champs CEF et CommonSecurityLog](cef-name-mapping.md).
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](./detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.
