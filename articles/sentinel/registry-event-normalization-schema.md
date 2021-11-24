---
title: Référence de schéma de normalisation d’événement du registre Microsoft Sentinel | Microsoft Docs
description: Cet article décrit le schéma de normalisation des événements de registre Microsoft Sentinel.
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
ms.openlocfilehash: 4a22c8b2f207d8d6d2019f417bfcb025dcca9fa1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523920"
---
# <a name="microsoft-sentinel-registry-event-normalization-schema-reference-public-preview"></a>Informations de référence de schéma de normalisation d’événement du registre Microsoft Sentinel (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le schéma d’événement du registre est utilisé pour décrire l’activité Windows de la création, de la modification ou de la suppression d’entités du Registre Windows.

Les événements de registre sont spécifiques aux systèmes Windows, mais ils sont signalés par différents systèmes qui surveillent Windows, tels que les systèmes de PEPT (détection et réponse des points de terminaison), Sysmon ou Windows lui-même.

Pour plus d’informations sur la normalisation dans Microsoft Sentinel, consultez [Normalisation et modèle ASIM (Advanced SIEM Information Model)](normalization.md).

> [!IMPORTANT]
> Le schéma de normalisation des événements du Registre est actuellement en version préliminaire. Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail de production.
>
> Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="parsers"></a>Analyseurs

Microsoft Sentinel fournit les analyseurs d’événements de registre intégrés suivants, spécifiques aux produits :

- **Mise à jour du registre des événements de sécurité (événement 4657**), collectée à l’aide de l’Agent Log Analytics ou de l’Agent Azure Monitor
- **Événements de surveillance du Registre sysmon (événements 12, 13 et 14)** , collectés à l’aide de l’Agent Log Analytics ou de l’Agent Azure Monitor
- **Microsoft 365 Defender pour les événements de registre des points de terminaison**

Pour utiliser l’analyseur source-agnostique qui unifie tous les analyseurs intégrés et vérifier que votre analyse s’exécute sur toutes les sources configurées, utilisez **imRegistry** comme nom de table dans votre requête.

Déployez les [analyseurs indépendants de la source et spécifiques de la source](normalization-about-parsers.md) à partir du [référentiel GitHub Microsoft Sentinel](https://aka.ms/AzSentinelRegistry).

### <a name="add-your-own-normalized-parsers"></a>Ajouter vos propres analyseurs normalisés

Lorsque vous implémentez des analyseurs personnalisés pour le modèle d’informations sur les événements du Registre, nommez vos fonctions KQL à l’aide de la syntaxe suivante : `imRegistry<vendor><Product>`.

Ajoutez vos fonctions KQL aux `imRegistry` analyseurs source-agnostique pour vous assurer que tout contenu utilisant le modèle d’événement du Registre utilise également votre nouvel analyseur.

## <a name="normalized-content"></a>Contenu normalisé

Microsoft Sentinel fournit la requête de chasse [Clé de registre persistante Via IFEO](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml). Cette requête fonctionne sur toutes les données d’activité du Registre normalisées à l’aide du modèle d’informations Advanced SIEM.

Pour plus d’informations, consultez [Repérer les menaces avec Microsoft Sentinel](hunting.md).

## <a name="schema-details"></a>Détails du schéma

Le modèle d’informations sur les événements du Registre est aligné avec le [schéma d’entité du Registre OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/registry.md).

### <a name="log-analytics-fields"></a>Champs Log Analytics


Les champs suivants sont générés par Log Analytics pour chaque enregistrement et peuvent être remplacés lors de la création d’un connecteur personnalisé.

| Champ         | Type     | Discussion      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | DATETIME | Heure à laquelle l’événement a été généré par l’appareil de création de rapports.|
| **_ResourceId**   | guid     | ID de ressource Azure de l’appareil ou du service de création de rapports, ou l’ID de ressource de redirecteur de journal pour les événements transférés avec Syslog, CEF ou WEF. |
| **Type** | String | Table d’origine à partir de laquelle l’enregistrement a été extrait. Ce champ est utile lorsque le même événement peut être reçu via plusieurs canaux vers différentes tables, et ont les mêmes valeurs EventVendor et EventProduct.<br><br>Par exemple, un événement Sysmon peut être collecté dans la table Event ou dans la table WindowsEvent. |


> [!NOTE]
> Log Analytics ajoute également d’autres champs qui sont moins pertinents pour les cas d’usage de sécurité. Pour plus d’informations, consultez [Colonnes standard dans les journaux d’Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>

### <a name="event-fields"></a>Champs de l’événement


Les champs d’événement sont communs à tous les schémas et décrivent l’activité elle-même et l’appareil de création de rapports.

| Champ               | Classe       | Type       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Facultatif    | Chaîne     |     Message général ou description, inclus dans l’enregistrement ou généré depuis l’enregistrement.   |
| **EventCount**          | Obligatoire   | Integer    |     Nombre d’événements décrits par l’enregistrement. <br><br>Cette valeur est utilisée quand la source prend en charge l’agrégation, et un seul enregistrement peut représenter plusieurs événements. <br><br>Pour les autres sources, affectez à la valeur `1`.   |
| **EventStartTime**      | Obligatoire   | Date/heure  |      Si la source prend en charge l’agrégation et que l’enregistrement représente plusieurs événements, ce champ spécifie l’heure à laquelle le premier événement a été généré. <br><br>Sinon, ce champ associe le champ [TimeGenerated](#timegenerated). |
| **EventEndTime**        | Obligatoire   | Alias      |      Alias du champ [TimeGenerated](#timegenerated).    |
| **EventType**           | Obligatoire   | Énuméré |    Décrit l’opération signalée par l’enregistrement. <br><br>Pour les enregistrements de Registre, les valeurs prises en charge sont les suivantes : <br>- `RegistryKeyCreated` <br>- `RegistryKeyDeleted`<br>- `RegistryKeyRenamed` <br>- `RegistryValueDeleted` <br>- `RegistryValueSet`|
| **EventOriginalUid**    | Facultatif    | Chaîne     |   ID unique de l’enregistrement d’origine, s’il est fourni par la source.<br><br>Exemple : `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Facultatif    | Chaîne     |   Type ou ID d’événement d’origine, s’il est fourni par la source.<br><br>Exemple : `4657`|
| <a name ="eventproduct"></a>**EventProduct**        | Obligatoire   | Chaîne     |             Produit générant l’événement. <br><br>Exemple : `Sysmon`<br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.           |
| **EventProductVersion** | Facultatif    | Chaîne     | Version du produit générant l’événement. <br><br>Exemple : `12.1`      |
| **EventVendor**         | Obligatoire   | Chaîne     |           Fournisseur du produit générant l’événement. <br><br>Exemple : `Microsoft`  <br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.  |
| **EventSchemaVersion**  | Obligatoire   | Chaîne     |    Version du schéma. La version du schéma documenté ici est `0.1`         |
| **EventReportUrl**      | Facultatif    | Chaîne     | URL fournie dans l’événement pour une ressource qui apporte des informations supplémentaires sur l’événement.|
| **Dvc** | Obligatoire       | Chaîne     |               Identificateur unique de l’appareil sur lequel l’événement s’est produit. <br><br>Ce champ peut prendre l’alias des champs [DvcId](#dvcid), [DvcHostname](#dvchostname) ou [DvcIpAddr](#dvcipaddr). Pour les sources cloud, pour lesquelles il n’y a pas d’appareil apparent, utilisez la même valeur que celle du champ [EventProduct](#eventproduct).         |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recommandé | Adresse IP |         Adresse IP de l’appareil sur lequel l’événement de registre s’est produit.  <br><br>Exemple : `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | Recommandé | Nom d’hôte   |               Nom d’hôte de l’appareil sur lequel l’événement de registre s’est produit. <br><br>Exemple : `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | Facultatif    | Chaîne     |  ID unique de l’appareil sur lequel l’événement de registre s’est produit. <br><br>Exemple : `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | Facultatif    | MAC        |   MAC de l’appareil sur lequel l’événement de registre s’est produit.  <br><br>Exemple : `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Facultatif    | Chaîne     |         Système d’exploitation en cours d’exécution sur l’appareil sur lequel l’événement de registre s’est produit.    <br><br>Exemple : `Windows`    |
| **DvcOsVersion**        | Facultatif    | Chaîne     |   Version du système d’exploitation de l’appareil sur lequel l’événement de registre s’est produit. <br><br>Exemple : `10` |
| **AdditionalFields**    | Facultatif    | Dynamique    | Si votre source fournit des informations supplémentaires à conserver, conservez-les avec les noms de champs d’origine ou créez le champ dynamique **AdditionalFields**, puis ajoutez-y les informations supplémentaires sous forme de paires clé/valeur.    |


### <a name="registry-event-specific-fields"></a>Champs spécifiques à l’événement du Registre

Les champs répertoriés dans le tableau ci-dessous sont spécifiques aux événements de Registre, mais sont similaires aux champs d’autres schémas et suivent des conventions d’affectation de noms similaires.

Pour plus d’informations, consultez [Structure du Registre](/windows/win32/sysinfo/structure-of-the-registry) dans la documentation Windows.

| Champ          | Classe        | Type       | Description   |
|---------------|--------------|------------|-----------------|
|<a name="registrykey"></a>**RegistryKey**     |     Obligatoire    |   Chaîne      |La clé de registre associée à l’opération, normalisée aux conventions de nommage de clé racine standard. Pour plus d’informations, consultez [Clés racines](#root-keys).<br><br>Les clés de Registre sont similaires aux dossiers dans les systèmes de fichiers. <br><br>Par exemple : `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`        |
|**RegistryValue**     |    Recommandé     |  Chaîne       |Valeur de registre associée à l’opération. Les valeurs de Registre sont similaires aux fichiers dans les systèmes de fichiers. <br><br>Par exemple : `Path`        |
|<a name="registryvaluetype"></a>**RegistryValueType**     |    Recommandé     |    Chaîne     | Type de valeur de Registre normalisé au format standard. Pour plus d’informations, consultez [Types valeur](#value-types).<br><br>Par exemple : `Reg_Expand_Sz`        |
|**RegistryValueData**     | Recommandé       |      Chaîne   |  Données stockées dans la valeur de registre. <br><br>Exemple : `C:\Windows\system32;C:\Windows;`       |
|<a name="registrypreviouskey"></a>**RegistryPreviousKey**     |  Recommandé       |   Chaîne      |  Pour les opérations qui modifient le registre, la clé de registre d’origine, normalisée au nom de clé racine standard. Pour plus d’informations, consultez [Clés racines](#root-keys). <br><br>**Remarque** : si l’opération a modifié d’autres champs, comme la valeur, mais que la clé reste la même, la [RegistryPreviousKey](#registrypreviouskey) aura la même valeur que la [RegistryKey](#registrykey).<br><br>Exemple : `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`       |
|<a name="registrypreviousvalue"></a>**RegistryPreviousValue**     | Recommandé        | Chaîne        | Pour les opérations qui modifient le registre, le type de valeur d’origine, normalisé au format standard. Pour plus d’informations, consultez [Types valeur](#value-types). <br><br>Si le type n’a pas été modifié, ce champ a la même valeur que le champ [RegistryValueType](#registryvaluetype).  <br><br>Exemple : `Path`       |
|**RegistryPreviousValueType**     | Recommandé        |   Chaîne      |Pour les opérations qui modifient le registre, le type de valeur d’origine. <br><br>Si le type n’a pas été modifié, ce champ a la même valeur que le champ [RegistryValueType](#registryvaluetype), normalisé au format standard. Pour plus d’informations, consultez [Types valeur](#value-types).<br><br>Exemple : `Reg_Expand_Sz`         |
|**RegistryPreviousValueData**     | Recommandé        |   Chaîne      |Pour les opérations qui modifient le registre, les données de registre d’origine. <br><br>Exemple : `C:\Windows\system32;C:\Windows;`         |
|**Utilisateur** | Alias | |Alias du champ [ActorUsername](#actorusername). <br><br>Exemple : `CONTOSO\ dadmin` |
|**Processus**     |  Alias       |         |  Alias du champ [ActingProcessName](#actingprocessname).<br><br>Exemple : `C:\Windows\System32\rundll32.exe`       |
| <a name="actorusername"></a>**ActorUsername**  | Obligatoire    | Chaîne     | Nom d’utilisateur de l’utilisateur qui a lancé l’événement. <br><br>Exemple : `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Obligatoire    | Énuméré |   Spécifie le type du nom d’utilisateur stocké dans le champ [ActorUsername](#actorusername). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity). <br><br>Exemple : `Windows`       |
| <a name="actoruserid"></a>**ActorUserId**    | Recommandé  | Chaîne     |   ID unique de l’Intervenant. L’ID spécifique dépend du système qui génère l’événement. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).  <br><br>Exemple : `S-1-5-18`    |
| **ActorUserIdType**| Recommandé  | Chaîne     |  Type de l’ID stocké dans le champ [ActorUserId](#actoruserid). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity). <br><br>Exemple : `SID`         |
| **ActorSessionId** | Facultatif     | Chaîne     |   ID unique de la session de connexion de l’Intervenant.  <br><br>Exemple : `999`<br><br>**Remarque** : le type est défini en tant que *chaîne* pour la prise en charge de différents systèmes. Cependant, sur Windows cette valeur doit être numérique. Si vous utilisez un ordinateur Windows et que la source envoie un type différent, assurez-vous de convertir la valeur. Par exemple, si la source envoie une valeur hexadécimale, convertissez-la en valeur décimale.   |
| <a name="actingprocessname"></a>**ActingProcessName**              | Facultatif     | Chaîne     |   Nom de fichier du fichier image du processus en cours. Ce nom est généralement considéré comme le nom du processus.  <br><br>Exemple : `C:\Windows\explorer.exe`  |
| **ActingProcessId**| Obligatoire    | Chaîne        | ID de processus (PID) du processus agissant.<br><br>Exemple : `48610176`           <br><br>**Remarque** : Le type est défini en tant que *chaîne* pour la prise en charge de différents systèmes. Cependant, sur Windows et Linux cette valeur doit être numérique. <br><br>Si vous utilisez un ordinateur Windows ou Linux et avez utilisé un type différent, assurez-vous de convertir les valeurs. Par exemple, si vous avez utilisé une valeur hexadécimale, convertissez-la en valeur décimale.    |
| **ActingProcessGuid**              | Facultatif     | Chaîne     |  Identificateur unique (GUID) généré du processus en cours d’action.   <br><br> Exemple : `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ParentProcessName**              | Facultatif     | Chaîne     |  Nom de fichier du fichier image du processus parent. Cette valeur est généralement considérée comme le nom du processus.    <br><br>Exemple : `C:\Windows\explorer.exe` |
| **ParentProcessId**| Obligatoire    | Chaîne    | ID de processus (PID) du processus parent.   <br><br>     Exemple : `48610176`    |
| **ParentProcessGuid**              | Facultatif     | Chaîne     |  Identificateur unique (GUID) généré du processus parent.     <br><br> Exemple : `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |



### <a name="root-keys"></a>Clés racines

Différentes sources représentent des préfixes de clé de registre utilisant différentes représentations. Pour les champs [RegistryKey](#registrykey) et [RegistryPreviousKey](#registrypreviouskey), utilisez les préfixes normalisés suivants :

|Préfixe de clé normalisée  |Autres représentations communes  |
|---------|---------|
|**HKEY_LOCAL_MACHINE**     | `HKLM`, `\REGISTRY\MACHINE`        |
|**HKEY_USERS**     | `HKU`, `\REGISTRY\USER`        |


### <a name="value-types"></a>Types de valeur

Différentes sources représentent des types de valeur de registre utilisant différentes représentations. Pour les champs [RegistryValueType](#registryvaluetype) et [RegistryPreviousValueType](#registrypreviousvalue), utilisez les préfixes normalisés suivants :


|Préfixe de clé normalisée  |Autres représentations communes  |
|---------|---------|
|  **Reg_None**   |    `None`, `%%1872`     |
|  **Reg_Sz**   |     `String`, `%%1873`    |
| **Reg_Expand_Sz**    | `ExpandString`, `%%1874`        |
|  **Reg_Binary**   |   `Binary`, `%%1875`      |
| **Reg_DWord**    |    `Dword`, `%%1876`     |
|  **Reg_Multi_Sz**   |  `MultiString`, `%%1879`       |
|  **Reg_QWord**   |    `Qword`, `%%1883`     |



## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Normalisation dans Microsoft Sentinel](normalization.md)
- [Informations de référence sur le schéma de normalisation de l’authentification Microsoft Sentinel (préversion publique)](authentication-normalization-schema.md)
- [Informations de référence de schéma de normalisation du DNS Microsoft Sentinel](dns-normalization-schema.md)
- [Informations de référence de schéma de normalisation d’événement du fichier Microsoft Sentinel (préversion publique)](file-event-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation du réseau Microsoft Sentinel](./network-normalization-schema.md)
