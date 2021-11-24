---
title: Informations de référence sur le schéma de normalisation de l’authentification Microsoft Sentinel | Microsoft Docs
description: Cet article décrit le schéma de normalisation de l’authentification Microsoft Sentinel.
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
ms.openlocfilehash: 62bf35c4638433cc4e9820f8d0bb7e2592bcebf5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517574"
---
# <a name="microsoft-sentinel-authentication-normalization-schema-reference-public-preview"></a>Informations de référence sur le schéma de normalisation de l’authentification Microsoft Sentinel (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le modèle d’informations de l’authentification est utilisé pour décrire les événements liés à l’authentification des utilisateurs, à la connexion et à la déconnexion. Les événements d’authentification sont envoyés par de nombreux appareils de création de rapports, généralement dans le cadre du flux d’événements en même temps que d’autres événements.

Par exemple, Windows envoie plusieurs événements d’authentification avec d’autres événements d’activité du système d’exploitation. Ainsi, dans la plupart des cas, les événements d’authentification sont stockés dans différentes tables Microsoft Sentinel et sont normalisés avec une fonction KQL, qui filtre également uniquement les événements d’authentification appropriés.

Les événements d’authentification incluent les événements des systèmes qui se concentrent sur l’authentification, tels que les passerelles VPN ou les contrôleurs de domaine, et l’authentification directe sur un système final, tel qu’un ordinateur ou un pare-feu.

Pour plus d’informations sur la normalisation dans Microsoft Sentinel, consultez [Normalisation et modèle ASIM (Advanced SIEM Information Model)](normalization.md).

> [!IMPORTANT]
> Le schéma de normalisation de l’authentification est en préversion. Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail de production.
>
> Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="parsers"></a>Analyseurs

Microsoft Sentinel fournit les analyseurs d’événements d’authentification intégrés suivants, spécifiques aux produits : 

- **Connexions Windows** signalées en tant qu’événements de sécurité (4624, 4625, 4634 et 4647), collectées avec l’agent Log Analytics ou l’agent Azure Monitor.
- **Connexions Windows** signalées par Microsoft 365 Defender pour point de terminaison, collectées avec le connecteur Microsoft 365 Defender.
- **Connexions Azure Active Directory**, collectées avec le connecteur Azure Active Directory. Des analyseurs distincts sont fournis pour les connexions de principaux de service et d’identités managées non interactives standard.
- **Connexions AWS**, collectées avec le connecteur AWS CloudTrail.
- **Authentification Okta**, collectée avec le connecteur Okta.

Pour utiliser l’analyseur indépendant de la source, qui unifie tous les analyseurs listés, vous assurant ainsi d’analyser les données dans toutes les sources configurées, utilisez **imAuthentication** comme nom de table dans votre requête.

Déployez les [analyseurs indépendants de la source et spécifiques de la source](normalization-about-parsers.md) à partir du [référentiel GitHub Microsoft Sentinel](https://aka.ms/AzSentinelAuth).



## <a name="normalized-content"></a>Contenu normalisé

La prise en charge du schéma ASIM de l’authentification comprend également la prise en charge des règles d’analytique intégrées suivantes avec des analyseurs d’authentification normalisés. Outre au travers des liens vers le référentiel GitHub Microsoft Sentinel fournis ci-dessous comme référence, vous pouvez trouver ces règles dans la [galerie de règles Microsoft Sentinel Analytics](detect-threats-built-in.md). Utilisez les pages GitHub liées pour copier toutes les requêtes de chasse appropriées pour les règles listées.

- [Attaque par pulvérisation de mot de passe potentielle (utilise la normalisation de l’authentification)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthPasswordSpray.yaml)
 - [Attaque par force brute contre les informations d’identification de l’utilisateur (utilise la normalisation de l’authentification)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthBruteForce.yaml)
 - [Connexion de l’utilisateur depuis différents pays dans un délai de 3 heures (utilise la normalisation de l’authentification)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthSigninsMultipleCountries.yaml)
 - [Connexions à partir d’adresses IP qui tentent de se connecter à des comptes désactivés (utilise la normalisation de l’authentification)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imSigninAttemptsByIPviaDisabledAccounts.yaml)


Les règles d’analytique d’authentification normalisée sont uniques, car elles détectent les attaques entre les sources. Ainsi, si un utilisateur s’est connecté à des systèmes différents et non liés, à partir de différents pays, Microsoft Sentinel détecte désormais cette menace.

## <a name="schema-details"></a>Détails du schéma

Le modèle d’informations de l’authentification est aligné sur le [schéma d’entité de connexion OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/logon.md).

Dans les tableaux suivants, le terme *Type* fait référence à un type logique. Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).

### <a name="log-analytics-fields"></a>Champs Log Analytics

Les champs suivants sont générés par Log Analytics pour chaque enregistrement et vous pouvez les remplacer quand vous créez un connecteur personnalisé.

|Champ  |Type  |Description  |
|---------|---------|---------|
|<a name ="timegenerated"></a>**TimeGenerated**     |  DATETIME       |Heure à laquelle l’événement a été généré par l’appareil de création de rapports.         |
|**_ResourceId**     | guid        |  ID de ressource Azure de l’appareil ou du service de création de rapports, ou l’ID de ressource de redirecteur de journal pour les événements transférés avec Syslog, CEF ou WEF.       |
| **Type** | String | Table d’origine à partir de laquelle l’enregistrement a été extrait. Ce champ est utile lorsque le même événement peut être reçu via plusieurs canaux vers différentes tables, et ont les mêmes valeurs EventVendor et EventProduct.<br><br>Par exemple, un événement Sysmon peut être collecté dans la table Event ou dans la table WindowsEvent. |
|     |         |         |

> [!NOTE]
> Log Analytics ajoute également d’autres champs qui sont moins pertinents pour les cas d’usage de sécurité. Pour plus d’informations, consultez [Colonnes standard dans les journaux d’Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>

### <a name="event-fields"></a>Champs d'événement

Les champs d’événement sont communs à tous les schémas et décrivent l’activité elle-même et l’appareil de création de rapports.

| Champ               | Classe       | Type       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Facultatif    | Chaîne     |     Message général ou description, inclus dans l’enregistrement ou généré depuis l’enregistrement.   |
| **EventCount**          | Obligatoire   | Integer    |     Nombre d’événements décrits par l’enregistrement. <br><br>Cette valeur est utilisée quand la source prend en charge l’agrégation, et un seul enregistrement peut représenter plusieurs événements. <br><br>Pour les autres sources, affectez à la valeur `1`. <br><br>**Remarque** : Ce champ est inclus pour des raisons de cohérence, mais il n’est généralement pas utilisé pour les événements d’authentification.  |
| **EventStartTime**      | Obligatoire   | Date/heure  |      Si la source prend en charge l’agrégation et que l’enregistrement représente plusieurs événements, ce champ spécifie l’heure à laquelle le premier événement a été généré. Sinon, ce champ associe le champ [TimeGenerated](#timegenerated).<br><br>**Remarque** : Ce champ est inclus pour des raisons de cohérence, mais il n’est généralement pas utilisé pour les événements d’authentification.  |
| **EventEndTime**        | Obligatoire   | Alias      |      Alias du champ [TimeGenerated](#timegenerated).    |
| **EventType**           | Obligatoire   | Énuméré |    Décrit l’opération signalée par l’enregistrement. <br><br>Pour les enregistrements d’authentification, les valeurs prises en charge sont les suivantes : <br>- `Logon` <br>- `Logoff`<br><br>**Remarque** : La valeur peut être fournie dans l’enregistrement source avec des termes différents, qui doivent être normalisés avec ces valeurs. La valeur d’origine doit être stockée dans le champ [EventOriginalType](#eventoriginaltype).|
| <a name ="eventoriginaltype"></a>**EventOriginalType**           | Facultatif   | Chaîne |    Type d’événement, ou ID, tel qu’il est fourni dans l’enregistrement source. <br><br>Exemple : `4625`|
| **EventResult**         | Obligatoire   | Énuméré |  Décrit le résultat de l’événement, normalisé à l’une des valeurs prises en charge suivantes : <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (non applicable) <br><br>**Remarque** : La valeur peut être fournie dans l’enregistrement source avec des termes différents, qui doivent être normalisés avec ces valeurs. <br><br>La source peut également fournir uniquement une valeur pour le champ [EventResultDetails](#eventresultdetails), qui doit être analysé pour obtenir la valeur **EventResult**. |
| <a name ="eventresultdetails"></a>**EventResultDetails**         | Facultatif   | Chaîne |  Une des valeurs suivantes : <br><br>-  `No such user or password`. Cette valeur doit également être utilisée quand l’événement d’origine signale qu’il n’existe pas d’utilisateur de ce type, sans référence à un mot de passe. <br>-   `Incorrect password`<br>-   `Account expired`<br>-  `Password expired`<br>- `User locked`<br>-  `User disabled`<br>-    `Logon violates policy`. Cette valeur doit être utilisée quand l’événement d’origine signale, par exemple : authentification multifacteur requise, ouverture de session en dehors des heures de travail, restrictions d’accès conditionnel ou tentatives trop fréquentes.<br>-  `Session expired`<br>-  `Other`<br><br>**Remarque** : La valeur peut être fournie dans l’enregistrement source avec des termes différents, qui doivent être normalisés avec ces valeurs.|
| **EventSubType**    | Facultatif    | Chaîne     |   Type de connexion, généralement utilisé pour les types d’ouverture de session Windows. <br><br>Exemple : `Interactive`|
| **EventOriginalResultDetails**    | Facultatif    | Chaîne     |  Valeur fournie dans l’enregistrement d’origine pour [EventResultDetails](#eventresultdetails), si elle est fournie par la source.|
| **EventOriginalUid**    | Facultatif    | Chaîne     |   ID unique de l’enregistrement d’origine, s’il est fourni par la source.|
| **EventOriginalType**   | Facultatif    | Chaîne     |   Type ou ID d’événement d’origine, s’il est fourni par la source.<br><br>Exemple : `4624`|
| <a name ="eventproduct"></a>**EventProduct**        | Obligatoire   | Chaîne     |             Produit générant l’événement. <br><br>Exemple : `Windows`<br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.           |
| **EventProductVersion** | Facultatif    | Chaîne     | Version du produit générant l’événement. <br><br>Exemple : `10` <br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.     |
| **EventVendor**         | Obligatoire   | Chaîne     |           Fournisseur du produit générant l’événement. <br><br>Exemple : `Microsoft`  <br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.  |
| **EventSchemaVersion**  | Obligatoire   | Chaîne     |    Version du schéma. La version du schéma documenté ici est `0.1`         |
| **EventReportUrl**      | Facultatif    | Chaîne     | URL fournie dans l’événement pour une ressource qui apporte des informations supplémentaires sur l’événement.|
| <a name ="dvc"></a>**Dvc** | Obligatoire       | Chaîne     |              Identificateur unique de l’appareil sur lequel l’événement s’est produit. <br><br>Ce champ peut prendre l’alias des champs [DvcId](#dvcid), [DvcHostname](#dvchostname) ou [DvcIpAddr](#dvcipaddr). Pour les sources cloud pour lesquelles il n’y a pas d’appareil apparent, utilisez la même valeur que celle du champ [Event Product](#eventproduct).             |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recommandé | Adresse IP |         Adresse IP de l’appareil sur lequel l’événement de processus s’est produit.  <br><br>Exemple : `45.21.42.12`  <br><br>**Remarque** : Si un identificateur est disponible, mais que le type n’est pas connu, n’utilisez pas ce champ. Pour plus d’informations, consultez [Dvc](#dvc).  |
| <a name ="dvchostname"></a>**DvcHostname**         | Recommandé | Nom d’hôte   |               Nom d’hôte de l’appareil sur lequel l’événement de processus s’est produit. <br><br>Exemple : `ContosoDc.Contoso.Azure`      <br><br>**Remarque** : Si un identificateur est disponible, mais que le type n’est pas connu, n’utilisez pas ce champ. Pour plus d’informations, consultez [Dvc](#dvc).          |
| <a name ="dvcid"></a>**DvcId**               | Facultatif    | Chaîne     |  ID unique de l’appareil sur lequel l’événement de processus s’est produit. <br><br>Exemple : `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **AdditionalFields**    | Facultatif    | Dynamique    | Si votre source fournit des informations supplémentaires à conserver, conservez-les avec les noms de champs d’origine ou créez le champ dynamique **AdditionalFields**, puis ajoutez les informations supplémentaires sous forme de paires clé/valeur.    |
| | | | |


## <a name="authentication-specific-fields"></a>Champs propres à l’authentification

Les champs listés dans le tableau ci-dessous sont propres aux événements d’authentification, mais sont similaires aux champs d’autres schémas et suivent des conventions d’affectation de noms similaires.

Les événements d’authentification référencent les entités suivantes :

- **Actor**
- **ActingApp**
- **SrcDvc**
- **TargetUser**
- **TargetApp**
- **TargetDvc**

La relation entre ces entités est mieux illustrée comme suit :

Un intervenant (**Actor**), exécutant une *application agissante* (**ActingApp**) sur un *appareil source* (**SrcDvc**), tente d’authentifier un utilisateur cible (**TargetUser**) auprès d’une *application cible* (**TargetApp**) sur un *appareil cible* (**TargetDvc**).

| Champ          | Classe        | Type       | Description   |
|---------------|--------------|------------|-----------------|
|**LogonMethod** |Facultatif |Chaîne |Méthode utilisée pour effectuer l’authentification. <br><br>Exemple : `Username & Password` |
|**LogonProtocol** |Facultatif |Chaîne |Protocole utilisé pour effectuer l’authentification. <br><br>Exemple : `NTLM` |
| <a name="actoruserid"></a>**ActorUserId**    | Facultatif  | UserId     |   Représentation unique, alphanumérique et lisible par l’ordinateur de l’intervenant. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).  <br><br>Exemple : `S-1-12-1-4141952679-1282074057-627758481-2916039507`    |
| **ActorUserIdType**| Facultatif  | UserIdType     |  Type de l’ID stocké dans le champ [ActorUserId](#actoruserid). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).         |
| <a name="actorusername"></a>**ActorUsername**  | Facultatif    | Nom d’utilisateur     | Nom d’utilisateur de l’intervenant, y compris les informations de domaine, le cas échéant. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : `AlbertE`     |
| **ActorUsernameType**              | Facultatif    | UsernameType |   Spécifie le type du nom d’utilisateur stocké dans le champ [ActorUsername](#actorusername). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity). <br><br>Exemple : `Windows`       |
| **ActorUserType** | Facultatif | Chaîne | Type de l’intervenant. <br><br>Par exemple : `Guest` |
| **ActorSessionId** | Facultatif     | Chaîne     |   ID unique de la session de connexion de l’intervenant.  <br><br>Exemple : `102pTUgC3p8RIqHvzxLCHnFlg`  |
| **ActingAppId** | Facultatif | Chaîne | ID de l’application effectuant l’autorisation pour le compte de l’intervenant, y compris un processus, un navigateur ou un service. <br><br>Par exemple : `0x12ae8` |
| **ActiveAppName** | Facultatif | Chaîne | Nom de l’application effectuant l’autorisation pour le compte de l’intervenant, y compris un processus, un navigateur ou un service. <br><br>Par exemple : `C:\Windows\System32\svchost.exe` |
| **ActingAppType** | Facultatif | Énuméré | Type de l’application agissante. Les valeurs prises en charge sont : <br> <br>- `Process` <br>- `Browser` <br>- `Resource` <br>- `Other` |
| **HttpUserAgent** |   Facultatif    | Chaîne |  Quand l’authentification est effectuée via HTTP ou HTTPS, la valeur de ce champ est l’en-tête HTTP user_agent fourni par l’application agissante lors de l’exécution de l’authentification.<br><br>Par exemple : `Mozilla/5.0 (iPhone; CPU iPhone OS 12_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0 Mobile/15E148 Safari/604.1` |
|<a name="targetuserid"></a> **TargetUserId**   | Facultatif | UserId     | Représentation unique, alphanumérique et lisible par l’ordinateur de l’utilisateur cible. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).            <br><br> Exemple : `00urjk4znu3BcncfY0h7`    |
| **TargetUserIdType**               | Facultatif | UserIdType     | Type de l’identifiant utilisateur stocké dans le champ [TargetUserId](#targetuserid). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).            <br><br> Exemple : `SID`  |
| <a name="targetusername"></a>**TargetUsername** | Facultatif | Nom d’utilisateur     | Nom d’utilisateur de l’utilisateur cible, y compris les informations de domaine, le cas échéant. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).  <br><br>Exemple :   `MarieC`      |
| **TargetUsernameType**             |Facultatif  | UsernameType | Spécifie le type du nom d’utilisateur stocké dans le champ [TargetUsername](#targetusername). Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).          |
| **TargetUserType** | Facultatif | Chaîne | Type de l’utilisateur cible. <br><br>Par exemple : `Member` |
| **TargetSessionId** | Facultatif | Chaîne | Identificateur de la session de connexion de l’utilisateur cible sur l’appareil source. |
| **Utilisateur**           | Alias        |     Chaîne       | Alias de [TargetUsername](#targetusername) ou [TargetUserId](#targetuserid) si [TargetUsername](#targetusername) n’est pas défini. <br><br>Exemple : `CONTOSO\dadmin`     |
|**SrcDvcId** |Facultatif |Chaîne |ID de l’appareil source comme indiqué dans l’enregistrement. <br><br>Par exemple : `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| <a name="srcdvchostname"></a>**SrcDvcHostname** |Facultatif | Nom d’hôte| Nom d’hôte de l’appareil source, y compris les informations de domaine, le cas échéant. Pour plus d’informations, consultez [L’entité appareil](normalization-about-schemas.md#the-device-entity). <br><br>Exemple : `Constoso\DESKTOP-1282V4D`|
| **SrcDvcHostnameType**|Facultatif |HostnameType |Type de [SrcDvcHostname](#srcdvchostname), s’il est connu. Pour plus d’informations, consultez [L’entité appareil](normalization-about-schemas.md#the-device-entity). |
|**SrcDvcType** |Facultatif |Énuméré |Type de l’appareil source. Les valeurs possibles incluent : <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|**SrcDvcIpAddr**|Recommandé |Adresse IP |Adresse IP de l’appareil source. <br><br>Exemple : `185.175.35.214` |
| **SrcDvcOs**|Facultatif |Chaîne |Système d’exploitation de l’appareil source. <br><br>Exemple : `Windows 10` |
|**SrcIsp** | Facultatif|Chaîne |Fournisseur de services Internet (ISP) utilisé par l’appareil source pour se connecter à Internet. <br><br>Exemple : `corpconnect` |
| **SrcGeoCountry**|Facultatif |Pays ou région |Exemple : `Canada` <br><br>Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types). |
| **SrcGeoCity**|Facultatif |City |Exemple : `Montreal` <br><br>Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types). |
|**SrcGeoRegion** | Facultatif|Région | Exemple : `Quebec` <br><br>Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).|
| **SrcGeoLongtitude**|Facultatif |Longitude  | Exemple : `-73.614830` <br><br>Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).|
| **SrcGeoLatitude**|Facultatif |Latitude |Exemple : `45.505918` <br><br>Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types). |
|**TargetAppId** |Facultatif | Chaîne| ID de l’application pour laquelle l’autorisation est requise, souvent affectée par l’appareil de création de rapports. <br><br>Exemple : `89162` |
|<a name="targetappname"></a>**TargetAppName** |Facultatif |Chaîne |Nom de l’application pour laquelle l’autorisation est requise, y compris un service, une URL ou une application SaaS. <br><br>Exemple : `Saleforce` |
| **TargetAppType**|Facultatif |Chaîne |Type de l’application effectuant l’autorisation pour le compte de l’intervenant. Les valeurs prises en charge sont :  <br><br>- `Process` <br>- `Service` <br>- `Resource` <br>- `URL` <br>- `SaaS application` <br>- `Other`|
|**TargetUrl** |Facultatif |Chaîne |URL associée à l’application cible. <br><br>Exemple : `https://console.aws.amazon.com/console/home?fromtb=true&hashArgs=%23&isauthcode=true&nc2=h_ct&src=header-signin&state=hashArgsFromTB_us-east-1_7596bc16c83d260b` |
|**LogonTarget**| Alias| |Alias de [TargetAppName](#targetappname), *URL* ou [TargetDvcHostname](#targetdvchostname), suivant le champ qui décrit le mieux la cible d’authentification. |
|**TargetDvcId** |Facultatif | Chaîne|ID de l’appareil cible comme indiqué dans l’enregistrement. <br><br> Exemple : `2739` |
|<a name="targetdvchostname"></a>**TargetDvcHostname** | Recommandé| Chaîne|Nom d’hôte de l’appareil cible, y compris les informations de domaine, le cas échéant. Pour plus d’informations, consultez [L’entité appareil](normalization-about-schemas.md#the-device-entity). |
| **TargetDvcHostnameType**|Recommandé | Chaîne|Type de [TargetDvcHostname](#targetdvchostname). Pour plus d’informations, consultez [L’entité appareil](normalization-about-schemas.md#the-device-entity). |
|**TargetDvcType** |Facultatif | Énuméré|Type de l’appareil cible. Les valeurs prises en charge sont : <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|<a name="targetdvcipaddr"></a>**TargetDvcIpAddr** |Facultatif | Adresse IP|Adresse IP de l’appareil cible. <br><br>Exemple : `2.2.2.2` |
|**TargetDvc** |Alias | |   Identificateur unique de l’appareil cible. <br><br>Sélectionnez ce champ afin de définir un alias de la valeur la plus appropriée pour la source spécifique : [TargetDvcHostname](#targetdvchostname), [TargetDvcIpAddr](#targetdvcipaddr) ou un autre ID, le cas échéant. |
| **TargetDvcOs**| Facultatif| Chaîne| Système d’exploitation de l’appareil cible. <br><br>Exemple : `Windows 10`|
| **TargetPortNumber**|Facultatif |Integer |Port de l’appareil cible.|
| | | | |




## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Normalisation dans Microsoft Sentinel](normalization.md)
- [Informations de référence de schéma de normalisation du DNS Microsoft Sentinel](dns-normalization-schema.md)
- [Informations de référence de schéma de normalisation d’événement du fichier Microsoft Sentinel (préversion publique)](file-event-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation du réseau Microsoft Sentinel](./network-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation des événements de processus Microsoft Sentinel (préversion publique)](process-events-normalization-schema.md)
