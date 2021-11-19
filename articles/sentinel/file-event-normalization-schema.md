---
title: Informations de référence sur le schéma de normalisation des événements de fichier Microsoft Sentinel | Microsoft Docs
description: Cet article décrit le schéma de normalisation des événements de fichier Microsoft Sentinel.
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
ms.openlocfilehash: 1e5e0752459362dbb3cea63b9adb5f453f40d2fd
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524471"
---
# <a name="microsoft-sentinel-file-event-normalization-schema-reference-public-preview"></a>Informations de référence de schéma de normalisation d’événement du fichier Microsoft Sentinel (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le schéma de normalisation des événements de fichier est utilisé pour décrire les activités de fichier, telles que la création, la modification ou la suppression de fichiers ou de documents. Ces événements sont signalés par les systèmes d’exploitation, les systèmes de stockage de fichiers tels qu’Azure Files et les systèmes de gestion de documents tels que Microsoft SharePoint.

Pour plus d’informations sur la normalisation dans Microsoft Sentinel, consultez [Normalisation et modèle ASIM (Advanced SIEM Information Model)](normalization.md).

> [!IMPORTANT]
> Le schéma de normalisation des événements de fichier est en préversion. Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail de production.
>
> Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="parsers"></a>Analyseurs

Microsoft Sentinel fournit les analyseurs d’événements de fichier intégrés suivants, spécifiques aux produits :

- **Événements d’activité de fichier sysmon (événements 11, 23 et 26)** , collectés à l’aide de l’agent Log Analytics ou de l’agent Azure Monitor.
- **Événements Microsoft Office 365 SharePoint et OneDrive**, collectés à l’aide du connecteur d’activité Office.
- **Microsoft 365 Defender pour les événements de points de terminaison**
- **Stockage Azure**, notamment le Stockage Blob, le Stockage Fichier, le Stockage File d’attente et le Stockage Table.

Pour utiliser l’analyseur indépendant de la source qui unifie tous les analyseurs intégrés et vérifier que votre analyse s’exécute sur toutes les sources configurées, utilisez imFileEvent comme nom de table dans votre requête.

Déployez les [analyseurs indépendants de la source et spécifiques de la source](normalization-about-parsers.md) à partir du [référentiel GitHub Microsoft Sentinel](https://aka.ms/AzSentinelFileEvent).

## <a name="add-your-own-normalized-parsers"></a>Ajouter vos propres analyseurs normalisés


Quand vous implémentez des analyseurs personnalisés pour le modèle d’informations sur les événements de fichier, nommez vos fonctions KQL avec la syntaxe suivante : `imFileEvent<vendor><Product`.

Ajoutez vos fonctions KQL à l’analyseur `imFileEvent` indépendant de la source pour vous assurer que tout contenu utilisant le modèle d’événement de fichier utilise également votre nouvel analyseur.

## <a name="normalized-content-for-file-activity-data"></a>Contenu normalisé pour les données d’activité de fichier

La prise en charge du schéma ASIM des activités de fichier comprend également la prise en charge des règles d’analytique intégrées suivantes avec des analyseurs d’activité de fichier normalisés. Outre au travers des liens vers le référentiel GitHub Microsoft Sentinel fournis ci-dessous comme référence, vous pouvez trouver ces règles dans la [galerie de règles Microsoft Sentinel Analytics](detect-threats-built-in.md). Utilisez les pages GitHub liées pour copier toutes les requêtes de chasse appropriées pour les règles listées.


- [Hachages de porte dérobée SUNBURST et SUPERNOVA (événements de fichier normalisés)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [Des vulnérabilités Exchange Server ont divulgué la correspondance IoC de mars 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [Service de messagerie unifiée HAFNIUM écrivant un fichier suspect](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM – Domaine, hachage et IOC IP – Mai 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [Création du fichier journal SUNSPOT ](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [Hachages de programme malveillant Comebacker et Klackring de ZINC connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

Pour plus d’informations, consultez [Créer des règles d’analytique personnalisées pour détecter des menaces](detect-threats-custom.md).


## <a name="schema-details"></a>Détails du schéma

Le modèle d’informations sur les événements de fichier est aligné sur le [schéma d’entité de processus OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/file.md).

### <a name="log-analytics-fields"></a>Champs Log Analytics

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

| Champ               | Classe       | Type       |  Description       |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Facultatif    | Chaîne     |     Message général ou description, inclus dans l’enregistrement ou généré depuis l’enregistrement.   |
| **EventCount**          | Obligatoire   | Integer    |     Nombre d’événements décrits par l’enregistrement. <br><br>Cette valeur est utilisée quand la source prend en charge l’agrégation, et un seul enregistrement peut représenter plusieurs événements. <br><br>Pour les autres sources, affectez à la valeur `1`.   |
| **EventStartTime**      | Obligatoire   | Date/heure  |      Si la source prend en charge l’agrégation et que l’enregistrement représente plusieurs événements, ce champ spécifie l’heure à laquelle le premier événement a été généré. Sinon, ce champ associe le champ [TimeGenerated](#timegenerated). |
| **EventEndTime**        | Obligatoire   | Alias      |      Alias du champ [TimeGenerated](#timegenerated).    |
| **EventType**           | Obligatoire   | Énuméré |    Décrit l’opération signalée par l’enregistrement. <br><br>Pour les enregistrements de fichier, les valeurs prises en charge sont les suivantes : <br><br>- `FileCreated`<br>- `FileModified`<br>- `FileDeleted`<br>- `FileRenamed`<br>- `FileCopied`<br>- `FileMoved`<br>- `FolderCreated`<br>- `FolderDeleted` |
| **EventResult**         | Obligatoire   | Énuméré |  Décrit le résultat de l’événement, normalisé à l’une des valeurs prises en charge suivantes : <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (non applicable) <br><br>La source peut fournir uniquement une valeur pour le champ [EventOriginalResultDetails](#eventoriginalresultdetails), qui doit être analysé pour obtenir la valeur **EventResult**. |
| <a name="eventoriginalresultdetails"></a>**EventOriginalResultDetails**  | Facultatif    | Chaîne     | Décrit le résultat de l’événement. <br><br>**Remarque** : Cette valeur n’est pas normalisée et est destinée à la valeur d’origine fournie par la source de données. Il n’existe aucun champ normalisé associé, tel qu’*EventResultDetails*, pour le schéma de normalisation des événements de fichier. |
| **EventOriginalUid**    | Facultatif    | Chaîne     | ID unique de l’enregistrement d’origine, s’il est fourni par la source.<br><br>Exemple : `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Facultatif    | Chaîne     | Type ou ID d’événement d’origine, s’il est fourni par la source.<br><br>Exemple : `4663`|
| <a name ="eventproduct"></a>**EventProduct**       | Obligatoire   | Chaîne     | Produit générant l’événement. <br><br>Exemple : `Sysmon`<br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.           |
| **EventProductVersion** | Facultatif    | Chaîne     | Version du produit générant l’événement. <br><br>Exemple : `12.1`<br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.           |
| **EventVendor**         | Obligatoire   | Chaîne     | Fournisseur du produit générant l’événement. <br><br>Exemple : `Microsoft`  <br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.  |
| **EventSchemaVersion**  | Obligatoire   | Chaîne     | Version du schéma. La version du schéma documenté ici est `0.1`         |
| **EventReportUrl**      | Facultatif    | Chaîne     | URL fournie dans l’événement pour une ressource qui apporte des informations supplémentaires sur l’événement.|
| **Dvc**                 | Alias       | Chaîne     | Identificateur unique de l’appareil sur lequel l’événement s’est produit. <br><br>Par exemple : `ContosoDc.Contoso.Azure`<br><br>Ce champ peut prendre l’alias des champs [DvcId](#dvcid), [DvcHostname](#dvchostname) ou [DvcIpAddr](#dvcipaddr). Pour les sources cloud, pour lesquelles il n’y a pas d’appareil apparent, utilisez la même valeur que celle du champ [EventProduct](#eventproduct).            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**   | Recommandé | Adresse IP | Adresse IP de l’appareil sur lequel l’événement de fichier s’est produit.  <br><br>Exemple : `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**    | Recommandé | Nom d’hôte   | Nom d’hôte de l’appareil sur lequel l’événement de fichier s’est produit. <br><br>Exemple : `ContosoDc.Contoso.Azure` |
| <a name ="dvcid"></a>**DvcId**  | Facultatif    | Chaîne     |  ID unique de l’appareil sur lequel l’événement de fichier s’est produit. <br><br>Exemple : `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | Facultatif    | MAC        |   MAC de l’appareil sur lequel l’événement de fichier s’est produit.  <br><br>Exemple : `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Facultatif    | Chaîne     |         Système d’exploitation en cours d’exécution sur l’appareil sur lequel l’événement de fichier s’est produit.    <br><br>Exemple : `Windows`    |
| **DvcOsVersion**        | Facultatif    | Chaîne     |   Version du système d’exploitation de l’appareil sur lequel l’événement de fichier s’est produit. <br><br>Exemple : `10` |
| **AdditionalFields**    | Facultatif    | Dynamique    | Si votre source fournit des informations supplémentaires à conserver, conservez-les avec les noms de champs d’origine ou créez le champ dynamique **AdditionalFields**, puis ajoutez-y les informations supplémentaires sous forme de paires clé/valeur.    |
| | | | |



### <a name="file-event-specific-fields"></a>Champs propres à l’événement de fichier

Les champs listés dans le tableau ci-dessous sont propres aux événements de fichier, mais sont similaires aux champs d’autres schémas et suivent des conventions d’affectation de noms similaires.

Le schéma d’événement de fichier référence les entités suivantes, qui sont au centre des activités de fichier :

- **Actor**. Utilisateur ayant lancé l’activité de fichier
- **ActingProcess**. Processus utilisé par l’intervenant (Actor) pour lancer l’activité de fichier
- **TargetFile**. Fichier sur lequel l’opération a été effectuée
- **Fichier source (SrcFile)** . Stocke les informations de fichier avant l’opération.

La relation entre ces entités est mieux illustrée comme suit : un **intervenant** effectue une opération de fichier avec un **processus agissant**, qui modifie le **fichier source** en **fichier cible**. 

Par exemple : `JohnDoe` (**intervenant**) utilise `Windows File Explorer` (**processus agissant**) pour renommer `new.doc` (**fichier source**) en `old.doc` (**fichier cible**).

| Champ          | Classe        | Type       | Description   |
|---------------|--------------|------------|-----------------|
| **ActingProcessCommandLine** |Facultatif  |Chaîne  | Ligne de commande utilisée pour exécuter le processus agissant. <br><br>Exemple : `"choco.exe" -v` |
|**ActingProcessGuid** |Facultatif     | Chaîne     |  Identificateur unique (GUID) généré du processus agissant. Permet d’identifier le processus entre les systèmes.  <br><br> Exemple : `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ActingProcessId**| Obligatoire    | Chaîne        | ID de processus (PID) du processus agissant.<br><br>Exemple : `48610176`           <br><br>**Remarque** : Le type est défini en tant que *chaîne* pour la prise en charge de différents systèmes. Cependant, sur Windows et Linux cette valeur doit être numérique. <br><br>Si vous utilisez un ordinateur Windows ou Linux et avez utilisé un type différent, assurez-vous de convertir les valeurs. Par exemple, si vous avez utilisé une valeur hexadécimale, convertissez-la en valeur décimale.    |
| <a name="actingprocessname"></a>**ActingProcessName**              | Facultatif     | Chaîne     |   Nom du processus agissant. Ce nom est généralement dérivé de l’image ou du fichier exécutable utilisé pour définir le code et les données initiaux mappés dans l’espace d’adressage virtuel du processus.<br><br>Exemple : `C:\Windows\explorer.exe`  |
|**Processus**| Alias| | Alias de [ActingProcessName](#actingprocessname)|
| <a name="actoruserid"></a>**ActorUserId**    | Recommandé  | Chaîne     |   ID unique de l’**intervenant**. L’ID spécifique dépend du système qui génère l’événement. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).  <br><br>Exemple : `S-1-5-18`    |
| **ActorUserIdType**| Recommandé  | Chaîne     |  Type de l’ID stocké dans le champ [ActorUserId](#actoruserid). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity). <br><br>Exemple : `SID`         |
| <a name="actorusername"></a>**ActorUsername**  | Obligatoire    | Chaîne     | Nom d’utilisateur de l’utilisateur qui a lancé l’événement. <br><br>Exemple : `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Obligatoire    | Énuméré |   Spécifie le type du nom d’utilisateur stocké dans le champ [ActorUsername](#actorusername). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity). <br><br>Exemple : `Windows`       |
|**Utilisateur** | Alias| | Alias du champ [ActorUsername](#actorusername). <br><br>Exemple : `CONTOSO\dadmin`|
| **ActorUserType**|Facultatif | Énuméré| Type d’**intervenant**. Les valeurs prises en charge sont : <br><br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other` <br><br>**Remarque** : La source peut fournir uniquement une valeur pour le champ [ActorOriginalUserType](#actororiginalusertype), qui doit être analysé pour obtenir la valeur **ActorUserType**.|
|<a name="actororiginalusertype"></a>**ActorOriginalUserType** |Facultatif |Chaîne | Type d’utilisateur **Actor**, tel que fourni par l’appareil de création de rapports. |
|**HttpUserAgent** |Facultatif | Chaîne |Quand l’opération est lancée par un système distant avec le protocole HTTP ou HTTPS, l’agent utilisateur est utilisé.<br><br>Par exemple :<br>`Mozilla/5.0 (Windows NT 10.0; Win64; x64)`<br>`AppleWebKit/537.36 (KHTML, like Gecko)`<br>` Chrome/42.0.2311.135`<br>`Safari/537.36 Edge/12.246`|
| **NetworkApplicationProtocol**| Facultatif|Chaîne | Quand l’opération est lancée par un système distant, cette valeur est le protocole de la couche application utilisé dans le modèle OSI. <br><br>Même si ce champ n’est pas énuméré et que toute valeur est acceptée, les valeurs préférables sont les suivantes : `HTTP`, `HTTPS`, `SMB`,`FTP` et `SSH`<br><br>Exemple : `SMB`|
|**SrcIpAddr** |Recommandé |Adresse IP | Quand l’opération est lancée par un système distant, il s’agit de l’adresse IP de ce système.<br><br>Exemple : `185.175.35.214`|
| **SrcFileCreationTime**|Facultatif |Date/Heure |Heure à laquelle le fichier source a été créé. |
|**SrcFileDirectory** | Facultatif| Chaîne| Dossier ou emplacement du fichier source. Ce champ doit être similaire au champ [SrcFilePath](#srcfilepath), sans l’élément final. <br><br>**Remarque** : Un analyseur peut fournir cette valeur si la valeur est disponible dans la source du journal et n’a pas besoin d’être extraite du chemin complet.|
| **SrcFileExtension**|Facultatif | Chaîne|Extension du fichier source. <br><br>**Remarque** : Un analyseur peut fournir cette valeur si la valeur est disponible dans la source du journal et n’a pas besoin d’être extraite du chemin complet.|
|**SrcFileMimeType** |Facultatif |Énuméré |    Type MIME ou Media du fichier source. Les valeurs prises en charge sont listées dans le dépôt [Types Media IANA](https://www.iana.org/assignments/media-types/media-types.xhtml). |
|**SrcFileName** |Facultatif |Chaîne | Nom du fichier source, sans chemin ou emplacement, mais avec une extension, le cas échéant. Ce champ doit être similaire au dernier élément du champ [SrcFilePath](#srcfilepath). <br><br>**Remarque** : Un analyseur peut fournir cette valeur si la valeur est disponible dans la source du journal et n’a pas besoin d’être extraite du chemin complet.|
| <a name="srcfilepath"></a>**SrcFilePath**| Obligatoire|Chaîne |Chemin complet et normalisé du fichier source, notamment le dossier ou l’emplacement, le nom de fichier et l’extension. <br><br>Pour plus d’informations, consultez [Structure du chemin](#path-structure).<br><br>Exemple : `/etc/init.d/networking` |
|**SrcFilePathType** |Obligatoire | Énuméré| Type de [SrcFilePath](#srcfilepath). Pour plus d’informations, consultez [Structure du chemin](#path-structure).|
|**SrcFileMD5**|Facultatif |MD5 | Hachage MD5 du fichier source. <br><br>Exemple :           `75a599802f1fa166cdadb360960b1dd0` |
|**SrcFileSHA1**|Facultatif |SHA1 |Hachage SHA-1 du fichier source.<br><br>Exemple :<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
|**SrcFileSHA256** | Facultatif|SHA256 |Hachage SHA-256 du fichier source. <br><br>Exemple :<br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`|
|**SrcFileSHA512** |Facultatif | SHA512|Hachage SHA-512 du fichier source. |
|**SrcFileSize**| Facultatif|Integer | Taille du fichier source en octets.|
|**TargetFileCreationTime** | Facultatif|Date/Heure |Heure à laquelle le fichier cible a été créé. |
|**TargetFileDirectory** | Facultatif|Chaîne |Dossier ou emplacement du fichier cible. Ce champ doit être similaire au champ [TargetFilePath](#targetfilepath), sans l’élément final. <br><br>**Remarque** : Un analyseur peut fournir cette valeur si la valeur est disponible dans la source du journal et n’a pas besoin d’être extraite du chemin complet.|
|**TargetFileExtension** |Facultatif |Chaîne | Extension du fichier cible.<br><br>**Remarque** : Un analyseur peut fournir cette valeur si la valeur est disponible dans la source du journal et n’a pas besoin d’être extraite du chemin complet.|
| **TargetFileMimeType**|Facultatif | Énuméré| Type MIME ou Media du fichier cible. Les valeurs autorisées sont listées dans le dépôt [Types Media IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|
| **TargetFileName**|Facultatif |Chaîne |Nom du fichier cible, sans chemin ou emplacement, mais avec une extension, le cas échéant. Ce champ doit être similaire à l’élément final du champ [TargetFilePath](#targetfilepath).<br><br>**Remarque** : Un analyseur peut fournir cette valeur si la valeur est disponible dans la source du journal et n’a pas besoin d’être extraite du chemin complet.|
|<a name="targetfilepath"></a>**TargetFilePath** | Obligatoire| Chaîne| Chemin complet et normalisé du fichier cible, notamment le dossier ou l’emplacement, le nom de fichier et l’extension. Pour plus d’informations, consultez [Structure du chemin](#path-structure). <br><br>**Remarque** : Si l’enregistrement n’inclut pas d’informations sur le dossier ou l’emplacement, stockez le nom de fichier uniquement ici. <br><br>Exemple : `C:\Windows\System32\notepad.exe`|
|**TargetFilePathType** | Obligatoire|Énuméré | Type de [TargetFilePath](#targetfilepath). Pour plus d’informations, consultez [Structure du chemin](#path-structure).    |
|**FilePath** |Alias | | Alias du champ [TargetFilePath](#targetfilepath).|
| **TargetFileMD5**| Facultatif| MD5|Hachage MD5 du fichier cible. <br><br>Exemple : `75a599802f1fa166cdadb360960b1dd0` |
|**TargetFileSHA1** |Facultatif |SHA1 |Hachage SHA-1 du fichier cible. <br><br>Exemple :<br> `d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0`|
|**TargetFileSHA256** | Facultatif|SHA256 |Hachage SHA-256 du fichier cible. <br><br>Exemple :<br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`  |
| **TargetFileSHA512**| Facultatif| SHA512|Hachage SHA-512 du fichier source. |
|**Hash**|Alias | |Alias du meilleur hachage disponible du fichier cible. |
|**TargetFileSize** |Facultatif | Integer|Taille du fichier cible en octets. |
| **TargetUrl**|Facultatif | Chaîne|Quand l’opération est lancée avec HTTP ou HTTPS, l’URL utilisée. <br><br>Exemple : `https://onedrive.live.com/?authkey=...` |
| | | | |



## <a name="path-structure"></a>Structure du chemin

Le chemin doit être normalisée afin de correspondre à un des formats suivants. Le format dans lequel la valeur est normalisée est reflété dans le champ **FilePathType** respectif.

|Type  |Exemple  |Notes  |
|---------|---------|---------|
|**Rattaché à Windows**     |   `C:\Windows\System32\notepad.exe`      |      Étant donné que les noms de chemin Windows ne respectent pas la casse, ce type implique que la valeur ne respecte pas la casse.   |
|**Partage Windows**     |      `\\Documents\My Shapes\Favorites.vssx`   | Étant donné que les noms de chemin Windows ne respectent pas la casse, ce type implique que la valeur ne respecte pas la casse.        |
|**Unix**     |  `/etc/init.d/networking`       |     Étant donné que les noms de chemin UNIX respectent la casse, ce type implique que la valeur respecte la casse.  <br><br>- Utilisez ce type pour AWS S3. Concaténez les noms de compartiment et de clé pour créer le chemin. <br><br>- Utilisez ce type pour les clés d’objet de stockage d’objets blob Azure.   |
|**URL**     |  `https://1drv.ms/p/s!Av04S_*********we`       | Utilisez ce type quand le chemin du fichier est disponible en tant qu’URL. Les URL ne sont pas limitées à *http* ou *https*, et toute valeur, y compris une valeur FTP, est valide. |
|     |         |         |



## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Normalisation dans Microsoft Sentinel](normalization.md)
- [Informations de référence sur le schéma de normalisation de l’authentification Microsoft Sentinel (préversion publique)](authentication-normalization-schema.md)
- [Informations de référence de schéma de normalisation du DNS Microsoft Sentinel](dns-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation du réseau Microsoft Sentinel](./network-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation des événements de processus Microsoft Sentinel (préversion publique)](process-events-normalization-schema.md)
- [Informations de référence de schéma de normalisation d’événement du registre Microsoft Sentinel (préversion publique)](registry-event-normalization-schema.md)
