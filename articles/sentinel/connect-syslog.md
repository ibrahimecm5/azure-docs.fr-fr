---
title: Connecter des données Syslog à Microsoft Sentinel | Microsoft Docs
description: Connectez une machine ou appliance prenant en charge Syslog à Microsoft Sentinel en utilisant un agent sur une machine Linux entre l’appliance et Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2342c2b3959ecb4ff9c174e769ce27f06ff36483
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524547"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Collecter des données de sources Linux à l’aide de Syslog

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Syslog** est un protocole de journalisation des événements commun à Linux. Vous pouvez utiliser le démon Syslog intégré aux appareils et appliances Linux pour collecter les événements locaux des types que vous spécifiez, et pour les envoyer à Microsoft Sentinel à l’aide de l'**agent Log Analytics pour Linux** (anciennement appelé l’agent OMS).

Cet article explique comment connecter vos sources de données à Microsoft Sentinel à l’aide de Syslog. Pour plus d’informations sur les connecteurs pris en charge pour cette méthode, consultez [Informations de référence sur les connecteurs de données](data-connectors-reference.md).

## <a name="architecture"></a>Architecture

Lorsque l’agent Log Analytics est installé sur votre machine virtuelle ou appliance, le script d’installation configure le démon Syslog local pour qu’il transfère des messages à l’agent sur le port UDP 25224. Après la réception des messages, l’agent les envoie à votre espace de travail Log Analytics via le protocole HTTPS, où ils sont ingérés dans la table Syslog dans **Microsoft Sentinel > Journaux**.

Pour en savoir plus, voir [Sources de données Syslog dans Azure Monitor](../azure-monitor/agents/data-sources-syslog.md).

:::image type="content" source="media/connect-syslog/syslog-diagram.png" alt-text="Ce diagramme montre le flux de données des sources Syslog vers l’espace de travail Microsoft Sentinel, où l’agent Log Analytics est installé directement sur l’appareil de la source de données.":::

Pour certains types d’appareils qui n’autorisent pas l’installation locale de l’agent Log Analytics, l’agent peut être installé à la place sur un redirecteur de journal basé sur Linux dédié. L’appareil d’origine doit être configuré pour envoyer des événements Syslog au démon Syslog à ce redirecteur au lieu du démon local. Le démon Syslog du redirecteur envoie des événements à l’agent Log Analytics via le protocole UDP. Si ce redirecteur Linux est supposé collecter un volume élevé d’événements Syslog, son démon Syslog envoie des événements à l’agent via le protocole TCP à la place. Dans les deux cas, l’agent envoie ensuite les événements depuis cet emplacement vers votre espace de travail Log Analytics dans Microsoft Sentinel.

:::image type="content" source="media/connect-syslog/syslog-forwarder-diagram.png" alt-text="Ce diagramme montre le flux de données des sources Syslog vers l’espace de travail Microsoft Sentinel, où l’agent Log Analytics est installé sur un appareil de transfert de journal distinct.":::

> [!NOTE]
> - Si votre appliance prend en charge **CEF (Common Event format) plutôt que Syslog**, un jeu de données plus complet est collecté et les données sont analysées au niveau de la collection. Vous devez choisir cette option et suivre les instructions fournies dans [Obtenir des journaux au format CEF à partir de votre appareil ou de votre appliance dans Microsoft Sentinel](connect-common-event-format.md).
>
> - Log Analytics prend en charge la collecte de messages envoyés par les démons **rsyslog** ou **syslog-ng**, où rsyslog est le démon par défaut. Le démon Syslog par défaut sur la version 5 de Red Hat Enterprise Linux (RHEL), CentOS et Oracle Linux (**sysklog**) ne *prend pas en charge* la collecte des événements Syslog. Pour collecter les données syslog avec cette version de ces distributions, le démon rsyslog doit être installé et configuré à la place de sysklog.

La configuration de la collection Syslog s’exécute en trois étapes :

- **Configurer votre appareil ou appliance Linux**. Cela fait référence à l’appareil sur lequel l’agent Log Analytics sera installé, qu’il s’agisse du même appareil que celui à l’origine des événements ou d’un collecteur de journaux qui les transfère.

- **Configurer les paramètres de journalisation de votre application** correspondant à l’emplacement du démon Syslog qui enverra des événements à l’agent.

- **Configurer l’agent Log Analytics lui-même**. Cette opération s’effectue à partir de Microsoft Sentinel, et la configuration est envoyée à tous les agents installés.

## <a name="configure-your-linux-machine-or-appliance"></a>Configurer votre machine ou appliance Linux

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie des connecteurs, sélectionnez **Syslog**, puis sélectionnez **Ouvrir la page du connecteur**.

    Si votre type d’appareil figure dans la **Galerie de connecteurs de données** Microsoft Sentinel, choisissez le connecteur de votre appareil au lieu du connecteur Syslog générique. S’il existe des instructions supplémentaires ou spéciales pour votre type d’appareil, vous les verrez, ainsi que du contenu personnalisé comme des classeurs et des modèles de règle d’analyse, sur la page connecteur de votre appareil.

1. Installez l’agent Linux. Sous **Choisissez l’emplacement d’installation de l’agent :**

    |Type de machine  |Instructions  |
    |---------|---------|
    |**Pour une machine virtuelle Linux Azure**     |    1. Développez **Installer l’agent sur une machine virtuelle Linux Azure**. <br><br>2. Sélectionnez le lien **Télécharger et installer l’agent pour les machines virtuelles Linux Azure >** .<br><br>3. Dans le panneau **Machines virtuelles**, sélectionnez une machine virtuelle sur laquelle installer l’agent, puis choisissez **Connecter**. Répétez cette étape pour chaque machine virtuelle à laquelle vous souhaitez vous connecter.     |
    |**Pour toute autre machine Linux**     |     1. Développez **Installer l’agent sur une machine Linux non Azure**. <br><br>2. Sélectionnez le lien **Télécharger et installer l’agent pour les machines Linux non Azure >** .<br><br>3. Dans le panneau **Gestion des agents**, sélectionnez l’onglet **Serveurs Linux**, puis copiez la commande pour **Télécharger et intégrer l’agent pour Linux** et exécutez-la sur votre machine Linux.<br><br>        Si vous souhaitez conserver une copie locale du fichier d’installation de l’agent Linux, sélectionnez le lien **Télécharger l’agent Linux** au-dessus de la commande « Télécharger et intégrer l’agent ». |
    |     |         |

   > [!NOTE]
   > Veillez à configurer les paramètres de sécurité pour ces appareils conformément à la stratégie de sécurité de votre organisation. Par exemple, vous pouvez configurer les paramètres réseau de sorte qu’ils soient conformes à la stratégie de sécurité réseau de votre organisation, puis modifier les ports et les protocoles dans le démon pour les adapter aux exigences de sécurité.

### <a name="using-the-same-machine-to-forward-both-plain-syslog-and-cef-messages"></a>Utilisation du même ordinateur pour transférer à la fois des messages Syslog *et* des messages CEF

Vous pouvez utiliser votre [machine de transfert de journaux CEF](connect-log-forwarder.md) existant pour collecter et transférer des journaux à partir de sources Syslog ordinaires également. Toutefois, vous devez effectuer les étapes suivantes pour éviter d’envoyer des événements dans les deux formats à Microsoft Sentinel, car cela entraînera une duplication des événements.

Si vous avez déjà configuré la [collecte de données à partir de vos sources CEF](connect-common-event-format.md) et que vous avez configuré l’agent Log Analytics :

1. Sur chaque machine qui envoie des journaux au format CEF, vous devez modifier le fichier config Syslog pour supprimer les fonctionnalités utilisées pour envoyer des messages CEF. De cette façon, les fonctionnalités envoyées en CEF ne sont pas aussi envoyées au format Syslog. Pour obtenir des instructions détaillées sur la procédure à suivre, consultez [Configurer Syslog sur l’agent Linux](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent).

1. Vous devez exécuter la commande suivante sur ces machines pour désactiver la synchronisation de l’agent avec la configuration Syslog dans Microsoft Sentinel. Cela permet de s’assurer que la modification de configuration que vous avez apportée à l’étape précédente n’est pas remplacée.

    ```c
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
    ```

## <a name="configure-your-devices-logging-settings"></a>Configurer les paramètres de journalisation de votre appareil

De nombreux types d’appareils ont des connecteur de données propres qui s’affichent dans la galerie **Connecteurs de données**. Certains de ces connecteurs impliquent des instructions supplémentaires spéciales pour configurer correctement la collecte des journaux dans Microsoft Sentinel. Ces instructions peuvent inclure l’implémentation d’un analyseur basé sur une fonction Kusto.

Tous les connecteurs répertoriés dans la galerie affichent des instructions spécifiques sur leurs pages de connecteur respectives dans le portail, ainsi que dans leurs sections de la page [Informations de référence sur les connecteurs de données Microsoft Sentinel](data-connectors-reference.md).


## <a name="configure-the-log-analytics-agent"></a>Configurer l’agent Log Analytics

1. En bas du panneau du connecteur Syslog, sélectionnez le lien **Ouvrir la configuration des agents de votre espace de travail >** .

1. Dans le panneau **Agents configuration** (Configuration des agents), sélectionnez l’onglet **Syslog**. Ajoutez ensuite les installations du connecteur à collecter. Sélectionnez **Add facility** (Ajouter une installation), puis choisissez une installation dans la liste déroulante.

    - Ajoutez les installations que votre appliance Syslog inclut dans ses en-têtes de journal.

    - Si vous souhaitez utiliser la détection de connexion SSH anormale avec les données que vous collectez, ajoutez **auth** et **authpriv**. Pour plus de détails, voir la [section suivante](#configure-the-syslog-connector-for-anomalous-ssh-login-detection).

1. Une fois que vous avez ajouté toutes les fonctionnalités que vous souhaitez surveiller, désactivez les cases à cocher correspondant aux gravités que vous ne souhaitez pas collecter. Par défaut, elles sont toutes marquées.

1. Sélectionnez **Appliquer**.

1. Sur votre machine virtuelle ou appliance, assurez-vous d’envoyer les fonctionnalités que vous avez spécifiées.

## <a name="find-your-data"></a>Recherche de données

1. Pour interroger les données du journal Syslog dans **Journaux**, entrez `Syslog` dans la fenêtre de requête.

1. Vous pouvez utiliser les paramètres de requête décrits dans [Utilisation de fonctions dans les requêtes de journal Azure Monitor](../azure-monitor/logs/functions.md) pour analyser vos messages Syslog. Vous pouvez ensuite enregistrer la requête sous la forme d’une nouvelle fonction de Log Analytics et l’utiliser comme nouveau type de données.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configurer le connecteur Syslog pour la détection de connexion SSH anormale

> [!IMPORTANT]
> La détection de connexion SSH anormale est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Microsoft Sentinel peut appliquer du Machine Learning aux données Syslog pour identifier une activité de connexion SSH (Secure Shell) anormale. Il s’agit entre autres des scénarios suivants :

- Voyage impossible : lorsque deux événements de connexion réussis se produisent à partir de deux emplacements où il est impossible de se trouver dans l’intervalle de temps des deux événements de connexion.

- Emplacement inattendu : l’emplacement à partir duquel un événement de connexion réussi s’est produit est suspect. Par exemple, l’emplacement n’a pas été observé récemment.
 
Cette détection nécessite une configuration spécifique du connecteur de données Syslog : 

1. Pour l’étape 2 de la section [Configurer l’agent Log Analytics](#configure-the-log-analytics-agent) ci-dessus, vérifiez que **auth** et **authpriv** sont sélectionnés en tant qu’installations à superviser, et que toutes les gravités sont sélectionnées. 

2. Laissez suffisamment de temps pour la collecte des informations Syslog. Ensuite, accédez à **Microsoft Sentinel - Logs**, puis copiez et collez la requête suivante :
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    Modifiez l’**Intervalle de temps** si nécessaire, puis sélectionnez **Exécuter**.
    
    Si le résultat obtenu est égal à zéro, vérifiez la configuration du connecteur et que les ordinateurs analysés ont une activité de connexion réussie pour la période que vous avez spécifiée pour votre requête.
    
    Si le résultat est supérieur à zéro, vos données Syslog sont adaptées à la détection de connexion SSH anormale. Vous activez cette détection à partir de **Analytique** >  **Modèles de règle** > **Détection de connexion SSH anormale (préversion)** .

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances locales Syslog à Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.
