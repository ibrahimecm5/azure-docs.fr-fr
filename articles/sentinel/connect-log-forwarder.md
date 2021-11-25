---
title: Déployer un redirecteur de journal pour ingérer des journaux Syslog et CEF dans Microsoft Sentinel | Microsoft Docs
description: Découvrez comment déployer un redirecteur de journal, composé d’un démon Syslog et de l’agent Log Analytics, dans le cadre du processus d’ingestion de journaux Syslog et CEF dans Microsoft Sentinel.
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
ms.openlocfilehash: 6279e4cee368a36d66db5f41a326194fb04fe397
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518581"
---
# <a name="deploy-a-log-forwarder-to-ingest-syslog-and-cef-logs-to-microsoft-sentinel"></a>Déployer un redirecteur de journal pour ingérer des journaux Syslog et CEF dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Pour ingérer des journaux Syslog et CEF dans Microsoft Sentinel, provenant en particulier d’appareils et d’appliances sur lesquels vous ne pouvez pas installer directement l’agent Log Analytics, vous devez désigner et configurer une machine Linux qui collecte les journaux de vos appareils et les transfère à votre espace de travail Microsoft Sentinel. Cet ordinateur peut être une machine physique ou virtuelle dans votre environnement local, une machine virtuelle Azure ou une machine virtuelle dans un autre cloud. 

Cette machine inclut deux composants qui participent à ce processus :

- Un démon Syslog, **syslog** ou **syslog-ng**, qui collecte les messages journaux.
- L’**agent Log Analytics** (également appelé agent OMS) qui transfère les journaux vers Microsoft Sentinel.

À l’aide du lien ci-dessous, vous allez exécuter sur l’ordinateur désigné un script qui effectue les tâches suivantes :

- Installe l’agent Log Analytics pour Linux (également appelé agent OMS) et le configure aux fins suivantes :
    - l’écoute des messages CEF à partir du démon Syslog Linux intégré sur le port TCP 25226
    - l’envoi sécurisé des messages via TLS à votre espace de travail Microsoft Sentinel, où ils sont analysés et enrichis

- Configure le démon Syslog Linux intégré (rsyslog.d/syslog-ng) aux fins suivantes :
    - l’écoute des messages Syslog de vos solutions de sécurité sur le port TCP 514
    - le transfert des seuls messages qu’il identifie comme CEF à l’agent Log Analytics sur localhost à l’aide du port TCP 25226
 
## <a name="prerequisites"></a>Prérequis

Votre machine doit répondre aux exigences suivantes :

- **Matériel (physique/virtuel)**

    - Votre machine Linux doit avoir un minimum de **4 cœurs de processeur et 8 Go de RAM**.

        > [!NOTE]
        > - Un ordinateur redirecteur de journal qui utilise le démon **rsyslog** a une capacité prise en charge pouvant aller **jusqu’à 8 500 événements par seconde (EPS)** collectés.

- **Système d’exploitation**

    - CentOS 7 et 8 (par 6), y compris les versions mineures (64 bits/32 bits)
    - Amazon Linux 2017.09 (64 bits uniquement)
    - Oracle Linux 7 (64 bits/32 bits)
    - Red Hat Enterprise Linux (RHEL) Server 7 et 8 (pas 6), y compris les versions mineures (64 bits/32 bits)
    - Debian GNU/Linux 8 et 9 (64 bits/32 bits)
    - Ubuntu Linux 14.04 LTS et 16.04 LTS (64 bits/32 bits), ainsi que 18.04 LTS (uniquement 64 bits)
    - SuSE Linux Enterprise Server 12, 15 (uniquement 64 bits)

- **Versions du démon**

    - Rsyslog : v8
    - Syslog-ng : 2.1 - 3.22.1

- **Packages**
    - **Python 2.7** ou **3** doit être installé sur la machine Linux.<br>Utilisez la commande `python --version` ou `python3 --version` pour vérifier.

- **Prise en charge de RFC Syslog**
  - RFC Syslog 3164
  - RFC Syslog 5424
 
- **Configuration**
    - Vous devez disposer d’autorisations élevées (sudo) sur votre machine Linux désignée.
    - La machine Linux ne doit être connectée à aucun espace de travail Azure avant l’installation de l’agent Log Analytics.

- **Données**
    - Il se peut que vous ayez besoin de l’**ID d’espace de travail** de l’espace de travail Microsoft Sentinel, ainsi que de la **clé primaire d’espace de travail** à un moment donné au cours de ce processus. Vous pouvez les trouver dans les paramètres de l’espace de travail, sous **Gestion des agents**.

### <a name="security-considerations"></a>Considérations relatives à la sécurité

Veillez à configurer la sécurité de la machine en fonction de la stratégie de sécurité de votre organisation. Par exemple, vous pouvez configurer votre réseau de sorte qu’il s’accorde à la stratégie de sécurité de votre réseau d’entreprise, et modifier les ports et les protocoles dans le démon pour les adapter à vos besoins. Vous pouvez utiliser les instructions suivantes pour améliorer la configuration de la sécurité de votre machine :  [Sécurisation de la machine virtuelle dans Azure](../virtual-machines/security-policy.md), [Bonnes pratiques pour la sécurité réseau](../security/fundamentals/network-best-practices.md).

Si vos appareils envoient des journaux Syslog et CEF sur TLS (par exemple, parce que votre redirecteur de journal se trouve dans le cloud), vous devez configurer le démon Syslog (rsyslog ou syslog-ng) pour qu’il communique dans TLS. Pour plus de détails, consultez la documentation suivante :  
- [Chiffrement du trafic Syslog avec TLS – rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [Chiffrement des messages de journal avec TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="run-the-deployment-script"></a>Exécuter le script de déploiement
 
1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Connecteurs de données**. Sélectionnez le connecteur de votre produit dans la galerie des connecteurs (ou le **Common Event Format (CEF)** si votre produit n’est pas répertorié), puis cliquez sur le bouton **Ouvrir la page du connecteur** dans l’angle inférieur droit. 

1. Sur la page du connecteur, dans les instructions sous **1.2 Installer le collecteur CEF sur la machine Linux**, copiez le lien fourni sous **Exécuter le script suivant pour installer et appliquer le collecteur CEF**.  
Si vous n’avez pas accès à cette page, copiez le lien à partir du texte ci-dessous (copie et collage de l’**ID d’espace de travail** et la **Clé primaire** ci-dessus à la place des espaces réservés) :

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```
1. Collez le lien ou le texte dans la ligne de commande de votre redirecteur de journal, puis exécutez-le.

1. Pendant l’exécution du script, vérifiez que vous ne recevez pas de messages d’erreur ou d’avertissement.
    - Vous pouvez recevoir un message vous indiquant d’exécuter une commande pour corriger un problème avec le mappage du champ *Ordinateur*. Pour plus d’informations, consultez l’[explication dans le script de déploiement](#mapping-command).

1. [Configurer votre appareil pour envoyer des messages CEF](connect-common-event-format.md#configure-your-device).

    > [!NOTE]
    > **Utilisation du même ordinateur pour transférer à la fois des messages Syslog *et* des messages CEF**
    >
    > Si vous envisagez d’utiliser cette machine de transfert de journaux pour transférer des [messages Syslog](connect-syslog.md) ainsi que des messages CEF, afin d’éviter la duplication des événements dans les tables Syslog et CommonSecurityLog :
    >
    > 1. Sur chaque machine source qui envoie des journaux au redirecteur au format CEF, vous devez modifier le fichier config Syslog pour supprimer les fonctionnalités utilisées pour envoyer des messages CEF. De cette façon, les fonctionnalités envoyées en CEF ne sont pas aussi envoyées au format Syslog. Pour obtenir des instructions détaillées sur la procédure à suivre, consultez [Configurer Syslog sur l’agent Linux](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent).
    >
    > 1. Vous devez exécuter la commande suivante sur ces machines pour désactiver la synchronisation de l’agent avec la configuration Syslog dans Microsoft Sentinel. Cela permet de s’assurer que la modification de configuration que vous avez apportée à l’étape précédente n’est pas remplacée.<br>
    > `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="deployment-script-explained"></a>Explication du script de déploiement

Voici une description commande par commande des actions du script de déploiement.

Choisissez un démon Syslog pour afficher la description appropriée.

# <a name="rsyslog-daemon"></a>[démon rsyslog](#tab/rsyslog)

1. **Téléchargement et installation de l’agent Log Analytics :**

    - Télécharge le script d’installation pour l’agent Linux Log Analytics (OMS).

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Installe l’agent Log Analytics/
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Paramétrage de la configuration de l’agent Log Analytics pour écouter sur le port 25226 et transférer les messages CEF vers Microsoft Sentinel :**

    - Télécharge la configuration à partir du dépôt GitHub de l’agent Log Analytics.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Configuration du démon Syslog :**

    - Ouvre le port 514 pour la communication TCP à l’aide du fichier config Syslog `/etc/rsyslog.conf`.

    - Configure le démon pour transférer les messages CEF à l’agent Log Analytics sur le port TCP 25226, en insérant un fichier config spécial `security-config-omsagent.conf` dans le répertoire Syslog `/etc/rsyslog.d/` du démon.

        Contenu du fichier `security-config-omsagent.conf` :

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Redémarrage du démon Syslog et de l’agent Log Analytics :**

    - Redémarre le démon rsyslog.
    
        ```bash
        service rsyslog restart
        ```

    - Redémarre l’agent Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Vérification du mappage du champ *Ordinateur* :**

    - Vérifie que le champ *Ordinateur* de la source syslog est correctement mappé dans l’agent Log Analytics en utilisant la commande suivante : 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>En cas de problème avec le mappage, le script génère un message d’erreur qui vous indique d’**exécuter manuellement la commande suivante** (en appliquant l’ID de l’espace de travail au lieu de l’espace réservé). La commande va garantir le mappage correct et redémarrer l’agent.
    
        ```bash
        sudo sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[démon syslog-ng](#tab/syslogng)

1. **Téléchargement et installation de l’agent Log Analytics :**

    - Télécharge le script d’installation pour l’agent Linux Log Analytics (OMS).

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Installe l’agent Log Analytics/
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Paramétrage de la configuration de l’agent Log Analytics pour écouter sur le port 25226 et transférer les messages CEF vers Microsoft Sentinel :**

    - Télécharge la configuration à partir du dépôt GitHub de l’agent Log Analytics.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Configuration du démon Syslog :**

    - Ouvre le port 514 pour la communication TCP à l’aide du fichier config Syslog `/etc/syslog-ng/syslog-ng.conf`.

    - Configure le démon pour transférer les messages CEF à l’agent Log Analytics sur le port TCP 25226, en insérant un fichier config spécial `security-config-omsagent.conf` dans le répertoire Syslog `/etc/syslog-ng/conf.d/` du démon.

        Contenu du fichier `security-config-omsagent.conf` :

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Redémarrage du démon Syslog et de l’agent Log Analytics :**

    - Redémarre le démon syslog-ng.
    
        ```bash
        service syslog-ng restart
        ```

    - Redémarre l’agent Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Vérification du mappage du champ *Ordinateur* :**

    - Vérifie que le champ *Ordinateur* de la source syslog est correctement mappé dans l’agent Log Analytics en utilisant la commande suivante : 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>En cas de problème avec le mappage, le script génère un message d’erreur qui vous indique d’**exécuter manuellement la commande suivante** (en appliquant l’ID de l’espace de travail au lieu de l’espace réservé). La commande va garantir le mappage correct et redémarrer l’agent.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à déployer l’agent Log Analytics pour connecter des appliances CEF à Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- Découvrez le [mappage de champs CEF et CommonSecurityLog](cef-name-mapping.md).
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](./detect-threats-built-in.md).
