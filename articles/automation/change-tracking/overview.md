---
title: Présentation de la fonctionnalité Suivi des modifications et inventaire dans Azure Automation
description: Cet article présente la fonctionnalité Change Tracking and Inventory, qui permet d’identifier les modifications apportées aux logiciels et aux services Microsoft de votre environnement.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/18/2021
ms.topic: conceptual
ms.openlocfilehash: 073fd4918cec0a1ec1dc679285b62fd63fe078d3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325730"
---
# <a name="change-tracking-and-inventory-overview"></a>Présentation de la fonctionnalité Suivi des modifications et inventaire

Cet article présente Suivi des modifications et inventaire dans Azure Automation. Cette fonctionnalité effectue le suivi des modifications apportées aux machines virtuelles hébergées sur Azure, locales et d’autres environnements cloud pour vous aider à identifier les problèmes opérationnels et environnementaux liés aux logiciels gérés par le gestionnaire de package de distribution. Les éléments suivis par Suivi des modifications et inventaire sont les suivants :

- Logiciels Windows
- Logiciel Linux (packages)
- Fichiers Windows et Linux
- Clés de Registre Windows
- Services Windows
- Démons Linux

> [!NOTE]
> Pour suivre les modifications de propriétés Azure Resource Manager, consultez l’[historique des modifications](../../governance/resource-graph/how-to/get-resource-changes.md) d’Azure Resource Graph.

Suivi des modifications et inventaire utilise [Supervision de l’intégrité des fichiers (FIM) dans Microsoft Defender pour le cloud](../../security-center/security-center-file-integrity-monitoring.md) pour examiner les fichiers de système d’exploitation et d’application, ainsi que le Registre Windows. Bien que FIM surveille ces entités, Suivi des modifications et inventaire suit en mode natif :

- Modifications de logiciel
- Services Windows
- Démons Linux

L’activation des complètes de Suivi des modifications et inventaire peut entraîner des frais supplémentaires. Avant de continuer, passez en revue [Tarification d’Automation](https://azure.microsoft.com/pricing/details/automation/) et [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Suivi des modifications et inventaire transfère les données vers des journaux Azure Monitor, et les données collectées sont stockées dans un espace de travail Log Analytics. La fonctionnalité FIM (Supervision de l’intégrité des fichiers) est disponible uniquement lorsque **Microsoft Defender pour les serveurs** est activé. Pour en savoir plus, consultez la [tarification](../../security-center/security-center-pricing.md) de Microsoft Defender pour le cloud. FIM charge des données dans le même espace de travail Log Analytics que celui créé pour stocker des données à partir de Suivi des modifications et inventaire. Nous vous recommandons de surveiller votre espace de travail Log Analytics lié pour effectuer le suivi de votre utilisation exacte. Pour plus d'informations sur l'analyse de l'utilisation des données des journaux d'activité Azure Monitor, consultez [Gérer l'utilisation et les coûts](../../azure-monitor/logs/manage-cost-storage.md).

Les machines connectées à l’espace de travail Log Analytics utilisent l’[agent Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) pour collecter des données sur les modifications apportées aux logiciels installés, aux services Windows, au registre et aux fichiers Windows, ainsi qu’aux démons Linux sur les serveurs analysés. Lorsque des données sont disponibles, l’agent les envoie aux journaux Azure Monitor pour traitement. Les journaux Azure Monitor appliquent une logique aux données reçues, les enregistrent et les rendent disponibles pour analyse.

> [!NOTE]
> Le suivi des modifications et inventaire vous demande d’établir une liaison entre un espace de travail Log Analytics et votre compte Automation. Pour obtenir la liste définitive des régions prises en charge, consultez [Mappages Azure Workspace](../how-to/region-mappings.md). Les mappages de région n’empêchent pas de gérer les machines virtuelles dans une autre région depuis votre compte Automation.

En tant que fournisseur de services, vous avez peut-être intégré les locataires de plusieurs clients à [Azure Lighthouse](../../lighthouse/overview.md). Azure Lighthouse vous permet d’effectuer des opérations à grande échelle sur plusieurs locataires Azure Active Directory (Azure AD) à la fois, améliorant ainsi l’efficacité des tâches de gestion telles que Suivi des modifications et inventaire parmi les locataires dont vous êtes responsable. La tâche Suivi des modifications et inventaire peut gérer des machines dans plusieurs abonnements au sein d’un même locataire ou entre plusieurs locataires à l’aide de la [gestion des ressources déléguée Azure](../../lighthouse/concepts/architecture.md).

## <a name="current-limitations"></a>Limites actuelles

Suivi des modifications et inventaire ne prend pas en charge ou a les limitations suivantes :

- Récursivité pour le suivi du Registre Windows
- Systèmes de fichiers réseau
- Méthodes d'installation différentes
- *Fichiers **.exe** stockés sur Windows
- La colonne **Taille maximale des fichiers** et ses valeurs ne sont pas utilisées dans l’implémentation actuelle.
- Si vous effectuez le suivi des modifications de fichiers, la taille de fichier est limitée à 5 Mo. 
- Si vous essayez de collecter plus de 2500 fichiers dans le cycle de collecte de 30 minutes, les performances de Change Tracking and Inventory peuvent être dégradées.
- Si le trafic réseau est élevé, l’affichage des enregistrements de modifications peut prendre jusqu’à six heures.
- Si vous modifiez une configuration quand une machine ou un serveur est arrêté, ce matériel risque de publier des modifications appartenant à la configuration précédente.
- Les mises à jour de correctif logiciel ne sont pas collectées sur les machines Windows 2016 Core RS3.
- Les démons Linux peuvent indiquer un état modifié même si aucune modification n’est intervenue. Ce problème est dû à la façon dont les données `SvcRunLevels` dans la table [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) d’Azure Monitor sont écrites.

## <a name="limits"></a>Limites

Pour les limites qui s’appliquent à Suivi des modifications et inventaire, consultez [Limites du service Azure Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#change-tracking-and-inventory).

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Suivi des modifications et inventaire est pris en charge sur tous les systèmes d’exploitation qui répondent aux exigences de l’agent Log Analytics. Pour obtenir la liste des versions de système d’exploitation Windows et Linux que l’agent Log Analytics prend actuellement en charge, consultez [Systèmes d’exploitation pris en charge](../../azure-monitor/agents/agents-overview.md#supported-operating-systems).

Pour comprendre les exigences client du protocole TLS 1.2, consultez [TLS 1.2 pour Azure Automation](../automation-managing-data.md#tls-12-for-azure-automation).

### <a name="python-requirement"></a>Exigence relative à Python

Le Suivi des modifications et inventaire prend uniquement en charge Python2. Si votre machine utilise une distribution qui n’inclut pas Python 2 par défaut, vous devez l’installer. Les exemples de commandes suivants installent Python 2 sur différentes distributions.

- Red Hat, CentOS, Oracle : `yum install -y python2`
- Ubuntu, Debian : `apt-get install -y python2`
- SUSE : `zypper install -y python2`

L’exécutable python2 doit avoir un alias pour *python*.

## <a name="network-requirements"></a>Configuration requise pour le réseau

Pour plus d’informations sur les ports, URL et autres informations réseaux nécessaires pour le Suivi des modifications et inventaire, consultez [Configuration réseau d’Azure Automation](../automation-network-configuration.md#update-management-and-change-tracking-and-inventory).

## <a name="enable-change-tracking-and-inventory"></a>Activer Change Tracking et Inventory

Vous pouvez activer Suivi des modifications et inventaire en procédant de l’une des façons suivantes :

- À partir de votre [compte Automation](enable-from-automation-account.md) pour une ou plusieurs machines Azure et non-Azure.

- Manuellement pour les machines non-Azure, notamment les machines ou les serveurs inscrits auprès de [serveurs activés par Azure Arc](../../azure-arc/servers/overview.md). Pour les machines hybrides, nous vous recommandons d’installer l’agent Log Analytics pour Windows en connectant d’abord votre ordinateur à des [serveurs compatibles avec Azure Arc](../../azure-arc/servers/overview.md), puis en utilisant Azure Policy pour affecter la stratégie intégrée [Déployer l’agent Log Analytics sur des machines Azure Arc *Linux* ou *Windows*](../../governance/policy/samples/built-in-policies.md#monitoring). Si vous envisagez de surveiller également les machines avec Azure Monitor pour machines virtuelles, utilisez plutôt l’initiative [Activer Azure Monitor pour machines virtuelles](../../governance/policy/samples/built-in-initiatives.md#monitoring).

- Pour une machine virtuelle Azure unique à partir de la [page Machine virtuelle](enable-from-vm.md) dans le Portail Azure. Ce scénario est disponible pour les machines virtuelles Linux et Windows.

- Pour [plusieurs machines virtuelles Azure](enable-from-portal.md), sélectionnez-les dans la page Machines virtuelles du Portail Azure.

## <a name="tracking-file-changes"></a>Suivi des modifications de fichiers

Pour suivre les modifications apportées à des fichiers sur Windows et Linux, Suivi des modifications et inventaire utilise des hachages MD5 des fichiers. La fonctionnalité utilise les hachages pour détecter si des modifications ont été apportées depuis le dernier inventaire.

## <a name="tracking-file-content-changes"></a>Suivi des modifications de contenu de fichier

Change Tracking and Inventory vous permet d’afficher le contenu d’un fichier Windows ou Linux. Pour chaque modification apportée à un fichier, Suivi des modifications et inventaire stocke le contenu du fichier dans un [compte Stockage Azure](../../storage/common/storage-account-create.md). Quand vous effectuez le suivi d’un fichier, vous pouvez afficher son contenu avant ou après une modification. Le contenu du fichier peut être affiché en ligne ou côte à côte.

![Afficher les modifications d’un fichier](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Suivi des clés de Registre

Change Tracking and Inventory autorise la supervision des modifications apportées aux clés de Registre Windows. La supervision vous permet d’identifier les points d’extensibilité où du code tiers et des logiciels malveillants peuvent être activés. Le tableau suivant liste les clés de Registre préconfigurées (mais non activées). Pour effectuer le suivi de ces clés de Registre, vous devez les activer.

> [!div class="mx-tdBreakAll"]
> |Clé de Registre | Objectif |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Surveille les scripts qui s’exécutent au démarrage.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Surveille les scripts qui s’exécutent à l’arrêt.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Supervise les clés qui sont chargées avant que l’utilisateur se connecte à son compte Windows. La clé est utilisée pour les applications 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Surveille les modifications apportées aux paramètres d’application.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Supervise les gestionnaires de menu contextuel qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Supervise les gestionnaires de raccordement de copie qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Surveille l’enregistrement du gestionnaire des icônes de recouvrement.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Supervise l’inscription du gestionnaire de superposition d’image sur une icône pour les applications 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisé pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisée pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation pour les applications 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Surveille les nouvelles extensions Internet Explorer, notamment les menus d’outils personnalisés et les boutons de barre d’outils personnalisés.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Supervise les nouvelles extensions Internet Explorer, telles que les menus d’outils et boutons de barre d’outils personnalisés pour les applications 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Supervise les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc. Analogue à la section [pilotes] du fichier **system.ini**.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Supervise les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc pour les applications 32 bits s’exécutant sur des ordinateurs 64 bits. Analogue à la section [pilotes] du fichier **system.ini**.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Supervise la liste des DLL système connues ou couramment utilisées. La supervision empêche quiconque d’exploiter des autorisations faibles de répertoire d’application en déposant des versions de type cheval de Troie des DLL système.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Supervise la liste des packages capables de recevoir des notifications d’événements de la part de **winlogon.exe**, le modèle de prise en charge de l’ouverture de session interactive de Windows.

## <a name="recursion-support"></a>Prise en charge de la récursivité

Suivi des modifications et inventaire prend en charge la récursivité, qui vous permet de spécifier des caractères génériques pour simplifier le suivi parmi les répertoires. La récursivité fournit également des variables d’environnement pour vous permettre d’effectuer le suivi des fichiers dans des environnements avec des noms de lecteurs multiples ou dynamiques. La liste suivante indique les informations courantes que vous devez connaître lors de la configuration de la récursivité :

- Les caractères génériques sont requis pour effectuer le suivi de plusieurs fichiers.

- Les caractères génériques ne peuvent être utilisés que dans le dernier segment d’un chemin de fichier, par exemple **c:\dossier\\fichier** _ ou _ */etc/* .conf**.

- Si le chemin d’une variable d’environnement n’est pas valide, la validation réussit, mais ce chemin échoue lors de l’exécution.

- Nous vous conseillons d’éviter les chemins généraux lors de la définition du chemin, car dans ce cas un trop grand nombre de dossiers peuvent être parcourus.

## <a name="change-tracking-and-inventory-data-collection"></a>Collecte de données dans Suivi des modifications et inventaire

Le tableau suivant indique la fréquence de collecte des données pour les types de modifications pris en charge par Suivi des modifications et inventaire. Pour chaque type, la capture instantanée de données de l’état actuel est également actualisée au moins toutes les 24 heures.

| **Modifier de type** | **Fréquence** |
| --- | --- |
| Registre Windows | 50 minutes |
| Fichier Windows | 30 minutes |
| Fichier Linux | 15 minutes |
| Services Windows | 10 secondes à 30 minutes</br> Valeur par défaut : 30 minutes |
| Démons Linux | 5 minutes |
| Logiciels Windows | 30 minutes |
| Logiciels Linux | 5 minutes |

Le tableau suivant montre les limites des éléments suivis par machine pour Suivi des modifications et inventaire.

| **Ressource** | **Limite** |
|---|---|---|
|Fichier|500|
|Registre|250|
|Logiciels Windows (correctifs non compris) |250|
|Packages Linux|1250|
|Services|250|
|Démons|250|

La consommation moyenne de données Log Analytics d’une machine utilisant Suivi des modifications et inventaire est d’environ 40 Mo par mois, selon votre environnement. Avec la fonctionnalité Utilisation et estimation des coûts de l’espace de travail Log Analytics, vous pouvez afficher les données ingérées par Change Tracking and Inventory dans un graphique d’utilisation. Utilisez cette vue pour évaluer votre utilisation des données et déterminer la manière dont elle se répercute sur votre facture. Voir [Comprendre votre utilisation et estimer les coûts](../../azure-monitor/logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="windows-services-data"></a>Données services Windows

Pour les services Windows, la fréquence de collecte par défaut est de 30 minutes. Vous pouvez configurer la fréquence à l’aide d’un curseur sous l’onglet **Services Windows** sous **Modifier les paramètres**.

![Curseur des services Windows](./media/overview/windowservices.png)

Pour optimiser les performances, l’agent Log Analytics effectue uniquement le suivi des modifications. Quand un seuil élevé est défini, certaines modifications peuvent être omises si le service revient à son état d’origine. Le fait de définir une fréquence moins élevée vous permet d’intercepter les modifications susceptibles d’être omises.

> [!NOTE]
> Même si l’agent peut enregistrer les modifications toutes les 10 secondes, les données mettent quelques minutes à s’afficher dans le portail Azure. Les modifications apportées pendant ce laps de temps continuent à être suivies et enregistrées.

## <a name="support-for-alerts-on-configuration-state"></a>Prise en charge des alertes relatives à l’état de configuration

L’une des principales caractéristiques de Suivi des modifications et inventaire est sa capacité à alerter en cas de modifications apportées à l’état de la configuration de votre environnement hybride. De nombreuses actions utiles peuvent être déclenchées en réponse à des alertes. Par exemple, les actions sur des fonctions Azure, des runbooks Automation, des webhooks et autres. L’alerte en cas de modification apportée au fichier **c:\windows\system32\drivers\etc\hosts** pour une machine est une bonne application des alertes pour Change Tracking and Inventory. Il existe de nombreux autres scénarios d’alerte, notamment les scénarios de requête définis dans le tableau suivant.

|Requête  |Description  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|Utile pour le suivi des modifications apportées aux fichiers critiques du système.|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Utile pour le suivi des modifications apportées aux fichiers de configuration de clés.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|Utile pour le suivi des modifications apportées aux services critiques du système|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState!= "Running"|Utile pour le suivi des modifications apportées aux services critiques du système|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|Utile pour les environnements nécessitant des configurations logicielles verrouillées.|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion!= "8.0.11081.0"|Utile pour voir sur quelles machines une version obsolète ou non conforme d’un logiciel est installée. Cette requête indique le dernier état de configuration signalé, mais n’indique pas les modifications.|
|ConfigurationChange <br>&#124; où RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Utile pour le suivi des modifications apportées aux clés antivirus essentielles.|
|ConfigurationChange <br>&#124; où RegistryKey contient @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Utile pour le suivi des modifications apportées aux paramètres de pare-feu.|

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment activer la fonctionnalité dans un compte Automation, consultez [Activer la fonctionnalité Suivi des modifications et inventaire dans un compte Automation](enable-from-automation-account.md).

- Pour activer la fonctionnalité depuis le portail Azure, consultez [Activer la fonctionnalité Change Tracking and Inventory sur le portail Azure](enable-from-portal.md).

- Pour activer la fonctionnalité sur un runbook, consultez [Activer la fonctionnalité Suivi des modifications et inventaire sur un runbook](enable-from-runbook.md).

- Pour activer la fonctionnalité sur une machine virtuelle, consultez [Activer la fonctionnalité Suivi des modifications et inventaire sur une machine virtuelle](enable-from-vm.md).
