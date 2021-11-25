---
title: Informations de référence sur le schéma de normalisation des événements de processus Microsoft Sentinel | Microsoft Docs
description: Cet article décrit le schéma de normalisation des événements de processus Microsoft Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: cff85330635a5c6f9501f6064d36fa623159df22
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522818"
---
# <a name="microsoft-sentinel-process-event-normalization-schema-reference-public-preview"></a>Informations de référence sur le schéma de normalisation des événements de processus Microsoft Sentinel (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le schéma de normalisation des événements de processus est utilisé pour décrire l’activité du système d’exploitation consistant à exécuter et à arrêter un processus. Ces événements sont signalés par les systèmes d’exploitation et les systèmes de sécurité, tels que les systèmes de détection de points de terminaison et de réponse.

Un processus, tel que défini par OSSEM, est un objet de gestion et d’autonomie qui représente une instance en cours d’exécution d’un programme. Alors que les processus eux-mêmes ne s’exécutent pas, ils gèrent les threads qui exécutent du code.

Pour plus d’informations sur la normalisation dans Microsoft Sentinel, consultez [Normalisation et modèle ASIM (Advanced SIEM Information Model)](normalization.md).

> [!IMPORTANT]
> Le schéma de normalisation des événements de processus est actuellement disponible en préversion. Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail de production.
>
> Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="parsers"></a>Analyseurs

Microsoft Sentinel fournit les analyseurs d’événements de processus intégrés suivants, propres aux produits :

- **Création du processus des événements de sécurité (événement 4688**), collecté à l’aide de l’Agent Log Analytics ou de l’Agent Azure Monitor
- **Arrêt du processus des événements de sécurité (événement 4689**), collecté à l’aide de l’Agent Log Analytics ou de l’Agent Azure Monitor
- **Création du processus sysmon (événement 1**), collecté à l’aide de l’Agent Log Analytics ou de l’Agent Azure Monitor
- **Arrêt du processus sysmon (événement 5**), collecté à l’aide de l’Agent Log Analytics ou de l’Agent Azure Monitor
- **Création de processus Microsoft 365 Defender pour points de terminaison**

Pour utiliser les analyseurs indépendants de la source qui unifient tous les analyseurs listés et vous assurer que vous analysez toutes les sources configurées, utilisez les noms de tables suivants dans vos requêtes :

- **imProcessCreate**, pour les requêtes qui requièrent des informations de création de processus. Ces requêtes sont le cas le plus courant.
- **imProcessTerminate**, pour les requêtes qui requièrent des informations d’arrêt de processus.
- **imProcessEvents**, pour les requêtes qui nécessitent à la fois des informations de création et d’arrêt de processus. Dans de tels cas, le champ `EventType` vous permet de faire la distinction entre les événements et il est défini sur `ProcessCreate` ou `ProcessTerminate`, respectivement. Les événements d’arrêt de processus incluent généralement beaucoup moins d’informations que les événements de création de processus.

Déployez les [analyseurs indépendants de la source et spécifiques de la source](normalization-about-parsers.md) à partir du [référentiel GitHub Microsoft Sentinel](https://aka.ms/AzSentinelProcessEvents).

## <a name="add-your-own-normalized-parsers"></a>Ajouter vos propres analyseurs normalisés

Lors de l'implémentation d’analyseurs personnalisés pour le modèle d’informations d’[événement de processus](normalization-about-schemas.md#the-process-entity), nommez vos fonctions KQL à l’aide de la syntaxe suivante : `imProcess<Type><vendor><Product>`, où `Type` est `Create`, `Terminate` ou `Event` si l’analyseur implémente à la fois des événements de création et de fin.

Ajoutez vos fonctions KQL aux analyseurs indépendants de la source `imProcess<Type>` et `imProcess` pour vous assurer que tout contenu utilisant le modèle d’[événement de processus](normalization-about-schemas.md#the-process-entity) utilise également votre nouvel analyseur.

## <a name="normalized-content-for-process-activity-data"></a>Contenu normalisé pour les données d’activité de processus

Le contenu Microsoft Sentinel suivant fonctionne avec toutes les activités de processus normalisées à l’aide du modèle d’informations SIEM avancé :

- **Règles analytiques** :

   - [Utilisation probable de l’outil de rapprochement AdFind (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_AdFind_Usage.yaml)
   - [Lignes de commande du processus Windows encodées en Base64 (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_base64_encoded_pefile.yaml)
   - [Programme malveillant dans la corbeille (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_malware_in_recyclebin.yaml)
   - [NOBELIUM – Exécution suspecte rundll32.exe de vbscript (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_NOBELIUM_SuspiciousRundll32Exec.yaml)
   - [Processus enfants SolarWinds suspects SUNBURST (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_SolarWinds_SUNBURST_Process-IOCs.yaml)

   Pour plus d’informations, consultez [Créer des règles analytiques personnalisées pour détecter des menaces](detect-threats-custom.md).

- **Requêtes Hunting** :
    - [Décomposition du résumé quotidien de script cscript (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_cscript_summary.yaml)
    - [Énumération des utilisateurs et des groupes (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_enumeration_user_and_group.yaml)
    - [Ajout du composant logiciel enfichable PowerShell Exchange (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ExchangePowerShellSnapin.yaml)
    - [Hôte exportant la boîte aux lettres et supprimant l’exportation (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_HostExportingMailboxAndRemovingExport.yaml)
    - [Utilisation d’Invoke-PowerShellTcpOneLine (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Invoke-PowerShellTcpOneLine.yaml)
    - [Interpréteur de commandes TCP inverse Nishang dans Base64 (Normalized Process Events)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_NishangReverseTCPShellBase64.yaml)
    - [Résumé des utilisateurs créés à l’aide de commutateurs de ligne de commande non communs/non documentés (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_persistence_create_account.yaml)
    - [Téléchargement Powercat (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_PowerCatDownload.yaml)
    - [Téléchargements PowerShell (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_powershell_downloads.yaml)
    - [Entropie pour les processus d’un hôte donné (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ProcessEntropy.yaml)
    - [Inventaire SolarWinds (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_SolarWindsInventory.yaml)
    - [Énumération suspecte à l’aide de l’outil Adfind (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Suspicious_enumeration_using_adfind.yaml)
    - [Arrêt/redémarrage du système Windows (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Windows%20System%20Shutdown-Reboot(T1529).yaml)
    - [Certutil (LOLBins et LOLScripts, événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Certutil-LOLBins.yaml)
    - [Rundll32 (LOLBins et LOLScripts, événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/inProcess_SignedBinaryProxyExecutionRundll32.yaml)
    - [Processus inhabituels – 5 % inférieurs (événements de processus normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_uncommon_processes.yaml)
    - [Obfuscation Unicode dans la ligne de commande](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/UnicodeObfuscationInCommandLine.yaml)


    Pour plus d’informations, consultez [Repérer les menaces avec Microsoft Sentinel](hunting.md).

## <a name="schema-details"></a>Détails du schéma

Le modèle d’informations sur les événements de processus est aligné sur le [schéma d’entité de processus OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/process.md).

### <a name="log-analytics-fields"></a>Champs Log Analytics

Les champs suivants sont générés par Log Analytics pour chaque enregistrement et peuvent être remplacés lors de la création d’un connecteur personnalisé.

| Champ         | Type     | Discussion      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | DATETIME | Heure à laquelle l’événement a été généré par l’appareil de création de rapports.|
| **_ResourceId**   | guid     | ID de ressource Azure de l’appareil ou du service de création de rapports, ou l’ID de ressource de redirecteur de journal pour les événements transférés avec Syslog, CEF ou WEF. |
| **Type** | String | Table d’origine à partir de laquelle l’enregistrement a été extrait. Ce champ est utile lorsque le même événement peut être reçu via plusieurs canaux vers différentes tables, et ont les mêmes valeurs EventVendor et EventProduct.<br><br>Par exemple, un événement Sysmon peut être collecté dans la table Event ou dans la table WindowsEvent. |
| | | |

> [!NOTE]
> Log Analytics ajoute également d’autres champs qui sont moins pertinents pour les cas d’usage de sécurité. Pour plus d’informations, consultez [Colonnes standard dans les journaux d’Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>

## <a name="event-fields"></a>Champs de l’événement

Les champs d’événement sont communs à tous les schémas et décrivent l’activité elle-même et l’appareil de création de rapports.

| Champ               | Classe       | Type       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Facultatif    | Chaîne     |     Message général ou description, inclus dans l’enregistrement ou généré depuis l’enregistrement.   |
| **EventCount**          | Obligatoire   | Integer    |     Nombre d’événements décrits par l’enregistrement. <br><br>Cette valeur est utilisée quand la source prend en charge l’agrégation, et un seul enregistrement peut représenter plusieurs événements. <br><br>Pour les autres sources, affectez à la valeur `1`.   |
| **EventStartTime**      | Obligatoire   | Date/heure  |      Si la source prend en charge l’agrégation et que l’enregistrement représente plusieurs événements, ce champ spécifie l’heure à laquelle le premier événement a été généré. Sinon, ce champ associe le champ [TimeGenerated](#timegenerated). |
| **EventEndTime**        | Obligatoire   | Alias      |      Alias du champ [TimeGenerated](#timegenerated).    |
| **EventType**           | Obligatoire   | Énuméré |    Décrit l’opération signalée par l’enregistrement. <br><br>Pour les enregistrements de processus, les valeurs prises en charge sont les suivantes : <br>- `ProcessCreated` <br>- `ProcessTerminated` |
| **EventResult**         | Obligatoire   | Énuméré |  Décrit le résultat de l’événement, normalisé à l’une des valeurs prises en charge suivantes : <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (non applicable) <br><br>La source peut fournir uniquement une valeur pour le champ **EventResultDetails**, qui doit être analysé pour obtenir la valeur **EventResult**.<br><br>**Remarque** : les événements de processus ne font généralement état que des réussites. |
| **EventOriginalUid**    | Facultatif    | Chaîne     |   ID unique de l’enregistrement d’origine, s’il est fourni par la source.<br><br>Exemple : `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Facultatif    | Chaîne     |   Type ou ID d’événement d’origine, s’il est fourni par la source.<br><br>Exemple : `4688`|
| <a name ="eventproduct"></a>**EventProduct**        | Obligatoire   | Chaîne     |             Produit générant l’événement. <br><br>Exemple : `Sysmon`<br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.           |
| **EventProductVersion** | Facultatif    | Chaîne     | Version du produit générant l’événement. <br><br>Exemple : `12.1`      |
| **EventVendor**         | Obligatoire   | Chaîne     |           Fournisseur du produit générant l’événement. <br><br>Exemple : `Microsoft`  <br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.  |
| **EventSchemaVersion**  | Obligatoire   | Chaîne     |    Version du schéma. La version du schéma documenté ici est `0.1`         |
| **EventReportUrl**      | Facultatif    | Chaîne     | URL fournie dans l’événement pour une ressource qui apporte des informations supplémentaires sur l’événement.|
| **Dvc** | Obligatoire       | Chaîne     |                Identificateur unique de l’appareil sur lequel l’événement s’est produit. <br><br>Ce champ peut prendre l’alias des champs [DvcId](#dvcid), [DvcHostname](#dvchostname) ou [DvcIpAddr](#dvcipaddr). Pour les sources cloud pour lesquelles il n’y a pas d’appareil apparent, utilisez la même valeur que celle du champ [Event Product](#eventproduct).            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recommandé | Adresse IP |         Adresse IP de l’appareil sur lequel l’événement de processus s’est produit.  <br><br>Exemple : `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | Recommandé | Nom d’hôte   |               Nom d’hôte de l’appareil sur lequel l’événement de processus s’est produit. <br><br>Exemple : `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | Facultatif    | Chaîne     |  ID unique de l’appareil sur lequel l’événement de processus s’est produit. <br><br>Exemple : `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | Facultatif    | MAC        |   MAC de l’appareil sur lequel l’événement de processus s’est produit.  <br><br>Exemple : `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Facultatif    | Chaîne     |         Système d’exploitation en cours d’exécution sur l’appareil sur lequel l’événement de processus s’est produit.    <br><br>Exemple : `Windows`    |
| **DvcOsVersion**        | Facultatif    | Chaîne     |   Version du système d’exploitation de l’appareil sur lequel l’événement de processus s’est produit. <br><br>Exemple : `10` |
| **AdditionalFields**    | Facultatif    | Dynamique    | Si votre source fournit des informations supplémentaires à conserver, conservez-les avec les noms de champs d’origine ou créez le champ dynamique **AdditionalFields**, puis ajoutez-y les informations supplémentaires sous forme de paires clé/valeur.    |
| | | | |

### <a name="process-event-specific-fields"></a>Champs propres à l’événement de processus

Les champs listés dans le tableau ci-dessous sont propres aux événements de processus, mais sont similaires aux champs d’autres schémas et suivent des conventions d’affectation de noms similaires.

Le schéma d’événement de processus fait référence aux entités suivantes, qui sont le centre de l’activité de création et d’arrêt de processus :

- **Actor**. Utilisateur à l’origine de la création ou de l’arrêt du processus.
- **ActingProcess**. Processus utilisé par l’intervenant pour initier la création ou l’arrêt du processus.
- **TargetProcess**. Nouveau processus.
- **TargetUser**. Utilisateur dont les informations d’identification sont utilisées pour créer le nouveau processus.
- **ParentProcess**. Processus qui a initié le processus d’intervenant.

| Champ          | Classe        | Type       | Description   |
|---------------|--------------|------------|-----------------|
| **Utilisateur**           | Alias        |            | Alias du [TargetUsername](#targetusername). <br><br>Exemple : `CONTOSO\dadmin`     |
| **Processus**        | Alias        |            | Alias du [TargetProcessName](#targetprocessname) <br><br>Exemple : `C:\Windows\System32\rundll32.exe`|
| **CommandLine**    | Alias        |            |     Alias du [TargetProcessCommandLine](#targetprocesscommandline)  |
| **Hash**           | Alias        |            |       Alias du meilleur hachage disponible. |
| <a name="actorusername"></a>**ActorUsername**  | Obligatoire    | Chaîne     | Nom d’utilisateur de l’utilisateur qui a lancé l’événement. <br><br>Exemple : `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Obligatoire    | Énuméré |   Spécifie le type du nom d’utilisateur stocké dans le champ [ActorUsername](#actorusername). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity). <br><br>Exemple : `Windows`       |
| <a name="actoruserid"></a>**ActorUserId**    | Recommandé  | Chaîne     |   ID unique de l’Intervenant. L’ID spécifique dépend du système qui génère l’événement. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).  <br><br>Exemple : `S-1-5-18`    |
| **ActorUserIdType**| Recommandé  | Chaîne     |  Type de l’ID stocké dans le champ [ActorUserId](#actoruserid). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity). <br><br>Exemple : `SID`         |
| **ActorSessionId** | Facultatif     | Chaîne     |   ID unique de la session de connexion de l’Intervenant.  <br><br>Exemple : `999`<br><br>**Remarque** : le type est défini en tant que *chaîne* pour la prise en charge de différents systèmes. Cependant, sur Windows, cette valeur doit être numérique. <br><br>Si vous utilisez un ordinateur Windows et avez utilisé un type différent, assurez-vous de convertir les valeurs. Par exemple, si vous avez utilisé une valeur hexadécimale, convertissez-la en valeur décimale.   |
| **ActingProcessCommandLine**       | Facultatif     | Chaîne     |   Ligne de commande utilisée pour exécuter le processus agissant. <br><br>Exemple : `"choco.exe" -v`    |
| **ActingProcessName**              | Facultatif     | string     |   Nom du processus agissant. Ce nom est généralement dérivé de l’image ou du fichier exécutable utilisé pour définir le code et les données initiaux mappés dans l’espace d’adressage virtuel du processus.<br><br>Exemple : `C:\Windows\explorer.exe`  |
| **ActingProcessFileCompany**       | Facultatif     | Chaîne     |           Société qui a créé le fichier d’image de processus agissant.  <br><br> Exemple : `Microsoft`    |
| **ActingProcessFileDescription**   | Facultatif     | Chaîne     |  Description incorporée dans les informations de version du fichier image du processus agissant. <br><br>Exemple : `Notepad++ : a free (GPL) source code editor` |
| **ActingProcessFileProduct**       | Facultatif     | Chaîne     |Nom de produit issu des informations de version dans le fichier image du processus agissant. <br><br> Exemple : `Notepad++`           |
| **ActingProcessFileVersion**       | Facultatif     | Chaîne     |               Version de produit issue des informations de version dans le fichier image du processus agissant. <br><br>Exemple : `7.9.5.0`   |
| **ActingProcessFileInternalName**  | Facultatif     | Chaîne     |      Nom de fichier interne de produit issu des informations de version dans le fichier image du processus agissant. |
| **ActingProcessFileOriginalName** | Facultatif     | Chaîne     |Nom de fichier d’origine de produit issu des informations de version dans le fichier image du processus agissant.       <br><br> Exemple : `Notepad++.exe` |
| **ActingProcessIsHidden**          | Facultatif     | Booléen    |      Indique si le processus agissant est en mode masqué.  |
| **ActingProcessInjectedAddress**   | Facultatif     | Chaîne     |      Adresse mémoire dans laquelle le processus agissant responsable est stocké.           |
| **ActingProcessId**| Obligatoire    | Chaîne        | ID de processus (PID) du processus agissant.<br><br>Exemple : `48610176`           <br><br>**Remarque** : Le type est défini en tant que *chaîne* pour la prise en charge de différents systèmes. Cependant, sur Windows et Linux cette valeur doit être numérique. <br><br>Si vous utilisez un ordinateur Windows ou Linux et avez utilisé un type différent, assurez-vous de convertir les valeurs. Par exemple, si vous avez utilisé une valeur hexadécimale, convertissez-la en valeur décimale.    |
| **ActingProcessGuid**              | Facultatif     | string     |  Identificateur unique (GUID) généré du processus agissant. Permet d’identifier le processus entre les systèmes.  <br><br> Exemple : `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ActingProcessIntegrityLevel**    | Facultatif     | Chaîne     |       Chaque processus a un niveau d’intégrité représenté dans son jeton. Les niveaux d’intégrité déterminent le niveau de protection ou l’accès au processus. <br><br> Windows définit les niveaux d’intégrité suivants : **low**, **medium**, **high** et **system**. Les utilisateurs Standard reçoivent un niveau d’intégrité moyen (**medium**) et les utilisateurs avec élévation de privilège reçoivent un niveau d’intégrité élevé (**high**). <br><br> Pour plus d’informations, consultez [Contrôle d’intégrité obligatoire - Applications Win32](/windows/win32/secauthz/mandatory-integrity-control). |
| **ActingProcessMD5**               | Facultatif     | Chaîne     |Hachage MD5 du fichier image du processus agissant.  <br><br>Exemple : `75a599802f1fa166cdadb360960b1dd0`|
| **ActingProcessSHA1**              | Facultatif     | SHA1       | Hachage SHA-1 du fichier image du processus agissant.             <br><br>  Exemple : `d55c5a4df19b46db8c54c801c4665d3338acdab0`  |
| **ActingProcessSHA256**            | Facultatif     | SHA256     | Hachage SHA-256 du fichier image du processus agissant.    <br><br> Exemple : <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`   |
| **ActingProcessSHA512**            | Facultatif     | SHA521     |       Hachage SHA-512 du fichier image du processus agissant.       |
| **ActingProcessIMPHASH**           | Facultatif     | Chaîne     |       Le hachage d’importation de toutes les dll de bibliothèque utilisées par le processus agissant.    |
| **ActingProcessCreationTime**      | Facultatif     | DateTime   |       Date et heure du début du processus agissant. |
| **ActingProcessTokenElevation**    | Facultatif     | Chaîne     | Jeton indiquant la présence ou l’absence de l’élévation des privilèges de l’utilisateur Access Control (UAC) appliquée au processus agissant.   <br><br>Exemple : `None`|
| **ActingProcessFileSize**          | Facultatif     | Long       |      Taille du fichier qui a exécuté le processus agissant.   |
| **ParentProcessName**              | Facultatif     | string     | Nom du processus parent. Ce nom est généralement dérivé de l’image ou du fichier exécutable utilisé pour définir le code et les données initiaux mappés dans l’espace d’adressage virtuel du processus.<br><br>Exemple : `C:\Windows\explorer.exe` |
| **ParentProcessFileCompany**       | Facultatif     | Chaîne     |Nom de la société qui a créé le fichier d’image de processus parent.            <br><br>    Exemple : `Microsoft`   |
| **ParentProcessFileDescription**   | Facultatif     | Chaîne     |  Description des informations de version dans le fichier image du processus parent.    <br><br>Exemple : `Notepad++ : a free (GPL) source code editor`|
| **ParentProcessFileProduct**       | Facultatif     | Chaîne     |Nom de produit issu des informations de version dans le fichier image du processus parent.    <br><br>  Exemple : `Notepad++`  |
| **ParentProcessFileVersion**       | Facultatif     | Chaîne     | Version de produit issue des informations de version dans le fichier image du processus parent.    <br><br> Exemple : `7.9.5.0` |
| **ParentProcessIsHidden**          | Facultatif     | Booléen    |   Indique si le processus parent est en mode masqué.  |
| **ParentProcessInjectedAddress**   | Facultatif     | Chaîne     |    Adresse mémoire dans laquelle le processus parent responsable est stocké.           |
| **ParentProcessId**| Obligatoire    | Chaîne    | ID de processus (PID) du processus parent.   <br><br>     Exemple : `48610176`    |
| **ParentProcessGuid**              | Facultatif     | Chaîne     |  Identificateur unique (GUID) généré du processus parent.  Permet d’identifier le processus entre les systèmes.    <br><br> Exemple : `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |
| **ParentProcessIntegrityLevel**    | Facultatif     | Chaîne     |   Chaque processus a un niveau d’intégrité représenté dans son jeton. Les niveaux d’intégrité déterminent le niveau de protection ou l’accès au processus. <br><br> Windows définit les niveaux d’intégrité suivants : **low**, **medium**, **high** et **system**. Les utilisateurs Standard reçoivent un niveau d’intégrité moyen (**medium**) et les utilisateurs avec élévation de privilège reçoivent un niveau d’intégrité élevé (**high**). <br><br> Pour plus d’informations, consultez [Contrôle d’intégrité obligatoire - Applications Win32](/windows/win32/secauthz/mandatory-integrity-control). |
| **ParentProcessMD5**               | Facultatif     | MD5        | Hachage MD5 du fichier image du processus parent.  <br><br>Exemple : `75a599802f1fa166cdadb360960b1dd0`|
| **ParentProcessSHA1**              | Facultatif     | SHA1       | Hachage SHA-1 du fichier image du processus parent.       <br><br> Exemple : `d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| **ParentProcessSHA256**            | Facultatif     | SHA256     |Hachage SHA-256 du fichier image du processus parent.      <br><br>  Exemple : <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **ParentProcessSHA512**            | Facultatif     | SHA512     |    Hachage SHA-512 du fichier image du processus parent.       |
| **ParentProcessIMPHASH**           | Facultatif     | Chaîne     |    Le hachage d’importation de toutes les dll de bibliothèque utilisées par le processus parent.    |
| **ParentProcessTokenElevation**    | Facultatif     | Chaîne     |Jeton indiquant la présence ou l’absence de l’élévation des privilèges de l’utilisateur Access Control (UAC) appliquée au processus parent.     <br><br>  Exemple : `None` |
| **ParentProcessCreationTime**      | Facultatif    | DateTime   |    Date et heure du début du processus parent. |
| <a name="targetusername"></a>**TargetUsername** | Obligatoire pour les événements de création de processus. | Chaîne     | Nom d’utilisateur de l’utilisateur cible.  <br><br>Exemple :   `CONTOSO\WIN-GG82ULGC9GO$`      |
| **TargetUsernameType**             | Obligatoire pour les événements de création de processus.   | Énuméré | Spécifie le type du nom d’utilisateur stocké dans le champ [TargetUsername](#targetusername). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).          <br><br>  Exemple : `Windows`        |
|<a name="targetuserid"></a> **TargetUserId**   | Recommandé | Chaîne     | ID unique de l’utilisateur cible. L’ID spécifique dépend du système qui génère l’événement. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).            <br><br> Exemple : `S-1-5-18`    |
| **TargetUserIdType**               | Recommandé | Chaîne     | Type de l’identifiant utilisateur stocké dans le champ [TargetUserId](#targetuserid). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).            <br><br> Exemple : `SID`  |
| **TargetUserSessionId**            | Facultatif     | Chaîne     |ID unique de la session de connexion de l’utilisateur cible. <br><br>Exemple : `999`          <br><br>**Remarque** : le type est défini en tant que *chaîne* pour la prise en charge de différents systèmes. Cependant, sur Windows, cette valeur doit être numérique. <br><br>Si vous utilisez un ordinateur Windows ou Linux et avez utilisé un type différent, assurez-vous de convertir les valeurs. Par exemple, si vous avez utilisé une valeur hexadécimale, convertissez-la en valeur décimale.     |
| <a name="targetprocessname"></a>**TargetProcessName**              | Obligatoire    | string     |Nom du processus cible. Ce nom est généralement dérivé de l’image ou du fichier exécutable utilisé pour définir le code et les données initiaux mappés dans l’espace d’adressage virtuel du processus.   <br><br>     Exemple : `C:\Windows\explorer.exe`     |
| **TargetProcessFileCompany**       | Facultatif     | Chaîne     |Nom de la société qui a créé le fichier d’image de processus cible.   <br><br>   Exemple : `Microsoft` |
| **TargetProcessFileDescription**   | Facultatif     | Chaîne     | Description des informations de version dans le fichier image du processus cible.   <br><br>Exemple : `Notepad++ : a free (GPL) source code editor` |
| **TargetProcessFileProduct**       | Facultatif     | Chaîne     |Nom de produit issu des informations de version dans le fichier image du processus cible.  <br><br>  Exemple : `Notepad++`  |
| **TargetProcessFileSize**          | Facultatif     | Chaîne     |    Taille du fichier qui a exécuté le processus responsable de l’événement. |
| **TargetProcessFileVersion**       | Facultatif     | Chaîne     |Version de produit issue des informations de version dans le fichier image du processus cible.   <br><br>  Exemple : `7.9.5.0` |
| **TargetProcessFileInternalName**  |    Facultatif          | Chaîne  |   Nom de fichier interne de produit issu des informations de version dans le fichier image du processus cible. |
| **TargetProcessFileOriginalName** |       Facultatif       | Chaîne   |   Nom de fichier d’origine de produit issu des informations de version dans le fichier image du processus cible. |
| **TargetProcessIsHidden**          | Facultatif     | Booléen    |   Indique si le processus cible est en mode masqué.  |
| **TargetProcessInjectedAddress**   | Facultatif     | Chaîne     |    Adresse mémoire dans laquelle le processus cible responsable est stocké.           |
| **TargetProcessMD5**               | Facultatif     | MD5        | Hachage MD5 du fichier image du processus cible.   <br><br> Exemple : `75a599802f1fa166cdadb360960b1dd0`|
| **TargetProcessSHA1**              | Facultatif     | SHA1       | Hachage SHA-1 du fichier image du processus cible.       <br><br>  Exemple : `d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| **TargetProcessSHA256**            | Facultatif     | SHA256     | Hachage SHA-256 du fichier image du processus cible.      <br><br>  Exemple : <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **TargetProcessSHA512**            | Facultatif     | SHA512     |   Hachage SHA-512 du fichier image du processus cible.       |
| **TargetProcessIMPHASH**           | Facultatif     | Chaîne     |    Le hachage d’importation de toutes les dll de bibliothèque utilisées par le processus cible.    |
| <a name="targetprocesscommandline"></a> **TargetProcessCommandLine**       | Obligatoire    | Chaîne     | Ligne de commande utilisée pour exécuter le processus cible.   <br><br> Exemple : `"choco.exe" -v`  |
| <a name="targetprocesscurrentdirectory"></a> **TargetProcessCurrentDirectory**       | Facultatif    | Chaîne     | Répertoire actif dans lequel le processus cible est exécuté.  <br><br> Exemple : `c:\windows\system32`  |
| **TargetProcessCreationTime**      | Obligatoire    | DateTime   |    Version de produit issue des informations de version dans le fichier image du processus cible.   |
| **TargetProcessId**| Obligatoire    | Chaîne     |  ID de processus (PID) du processus cible.     <br><br>Exemple : `48610176`<br><br>**Remarque** : Le type est défini en tant que *chaîne* pour la prise en charge de différents systèmes. Cependant, sur Windows et Linux cette valeur doit être numérique. <br><br>Si vous utilisez un ordinateur Windows ou Linux et avez utilisé un type différent, assurez-vous de convertir les valeurs. Par exemple, si vous avez utilisé une valeur hexadécimale, convertissez-la en valeur décimale.         |
| **TargetProcessGuid**              | Facultatif    | Chaîne     |Identificateur unique (GUID) généré du processus cible. Permet d’identifier le processus entre les systèmes.   <br><br>  Exemple : `EF3BD0BD-2B74-60C5-AF5C-010000001E00`  |
| **TargetProcessIntegrityLevel**    | Facultatif    | Chaîne     |   Chaque processus a un niveau d’intégrité représenté dans son jeton. Les niveaux d’intégrité déterminent le niveau de protection ou l’accès au processus. <br><br> Windows définit les niveaux d’intégrité suivants : **low**, **medium**, **high** et **system**. Les utilisateurs Standard reçoivent un niveau d’intégrité moyen (**medium**) et les utilisateurs avec élévation de privilège reçoivent un niveau d’intégrité élevé (**high**). <br><br> Pour plus d’informations, consultez [Contrôle d’intégrité obligatoire - Applications Win32](/windows/win32/secauthz/mandatory-integrity-control). |
| **TargetProcessTokenElevation**    | Facultatif    | Chaîne     |Type de jeton indiquant la présence ou l’absence de l’élévation des privilèges de l’utilisateur Access Control (UAC) appliquée au processus qui a été créé ou arrêté.   <br><br>    Exemple : `None`     |
| | | | |



## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Normalisation dans Microsoft Sentinel](normalization.md)
- [Informations de référence sur le schéma de normalisation de l’authentification Microsoft Sentinel (préversion publique)](authentication-normalization-schema.md)
- [Informations de référence de schéma de normalisation du DNS Microsoft Sentinel](dns-normalization-schema.md)
- [Informations de référence de schéma de normalisation d’événement du fichier Microsoft Sentinel (préversion publique)](file-event-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation du réseau Microsoft Sentinel](./network-normalization-schema.md)
- [Informations de référence de schéma de normalisation d’événement du registre Microsoft Sentinel (préversion publique)](registry-event-normalization-schema.md)
