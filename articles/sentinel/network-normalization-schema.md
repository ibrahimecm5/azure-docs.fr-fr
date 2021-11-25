---
title: Référence de schéma de normalisation du réseau Microsoft Sentinel (préversion publique) | Microsoft Docs
description: Cet article présente le schéma de normalisation du réseau Microsoft Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: df994e2be047e2829ea56423b85eee32e63c0154
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520415"
---
# <a name="microsoft-sentinel-network-session-normalization-schema-reference-public-preview"></a>Référence de schéma de normalisation de la session réseau Microsoft Sentinel (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le schéma de normalisation de la session réseau permet de décrire une activité du réseau IP. Cela comprend les connexions réseau et les sessions réseau. Ces événements sont signalés, par exemple, par les systèmes d’exploitation, les routeurs, les pare-feu, les systèmes de prévention des intrusions et les passerelles de sécurité Web.

Pour plus d’informations sur la normalisation dans Microsoft Sentinel, consultez [Normalisation et modèle ASIM (Advanced SIEM Information Model)](normalization.md).

> [!IMPORTANT]
> Cet article décrit la version 0,2 du schéma de normalisation du réseau, où la [version 0,1](normalization-schema-v1.md) a été publiée avant que Asim ne soit disponible et ne s’aligne pas sur Asim à plusieurs endroits. Pour plus d’informations, consultez [différences entre les versions de schéma de normalisation réseau](normalization-schema-v1.md#changes) . 
>

> [!IMPORTANT]
> Le schéma de normalisation Réseau est actuellement en PRÉVERSION. Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail de production.
>
> Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="support-for-common-network-telemetry-sources"></a>Prise en charge des sources de télémétrie courantes du réseau

Le schéma de normalisation du réseau peut représenter n’importe quelle session réseau IP, mais il est spécifiquement conçu pour assurer la prise en charge des types de sources courants, notamment :

- Netflow
- Pare-feux
- Systèmes de prévention des intrusions
- Serveurs Web
- Passerelles de sécurité Web

Les sections suivantes fournissent des conseils sur la normalisation et l’utilisation du schéma pour les différents types de sources. Chaque type de source peut :
- Prendre en charge des champs supplémentaires à partir des listes de champs auxiliaires : [champs d’appareil intermédiaires](#Intermediary), [champs de session http](#http-session-fields)et [champs d’inspection](#inspection-fields). Certains champs peuvent être obligatoires uniquement dans le contexte du type de source spécifique.
- Autorisez les valeurs spécifiques du type source aux champs d’événement courants tels que `EventType` et `EventResult`.
- La prise en charge, en plus de l'analyseur `imNetworkSession`, est également l'analyseur`imWebSession` ou `inNetworkNotable`, ou les deux.

### <a name="netflow-log-sources"></a>Sources de journaux NetFlow

| Tâche | Description|
| --- | --- |
| **Normaliser les événements NetFlow** | Pour normaliser les événements réseau, mappez-les à des [champs de session réseau](#network-session-fields). La télémétrie NetFlow prend en charge l’agrégation, et la `EventCount` valeur de champ doit refléter cela et être définie sur la valeur des *flux* de Netflow. |
| **Utiliser les événements NetFlow** | Les événements Netflow sont exposés dans le cadre de l’analyseur source-agnostique [imNetworkSession](#use-parsers) . Lorsque vous créez une requête d’agrégation, veillez à prendre en compte la `EventCount` valeur du champ, qui ne peut pas toujours être définie sur `1`. |
| | |

### <a name="firewall-log-sources"></a>Sources du journal du pare-feu

| Tâche | Description |
| --- | --- |
| **Normaliser les événements de pare-feu** | Pour normaliser les événements à partir de pare-feu, mappez les événements pertinents aux champs [événement](#event-fields), [session réseau](#network-session-fields)et inspection de la [session](#inspection-fields) . Filtrez ces événements et ajoutez-les à l’analyseur source-agnostique [inNetworkNotables](#use-parsers) . |
| **Utiliser des événements de pare-feu** | Les événements Netflow sont exposés dans le cadre de l’analyseur source-agnostique [imNetworkSession](#use-parsers) . Les événements pertinents, identifiés par les moteurs d’inspection du pare-feu, sont également exposés dans le cadre des analyseurs source-agnostique [inNetworkNotables](#use-parsers). |
| | |

### <a name="intrusion-prevention-systems-ips-log-sources"></a>Sources du journal des systèmes de prévention des intrusions (IPS)

| Tâche | Description |
| --- | --- |
| **Normaliser les événements IPS** | Pour normaliser les événements à partir de systèmes de prévention des intrusions, mappez les champs d' [événement](#event-fields), les [champs de session réseau](#network-session-fields)et les [champs d’inspection de session](#inspection-fields). Veillez à inclure votre analyseur spécifique à la source dans les analyseurs source-agnostique [imNetworkSession](#use-parsers) et [inNetworkNotables](#use-parsers) . |
| **Utiliser des événements IPS** | Les événements Netflow sont exposés dans le cadre de l’analyseur source-agnostique [imNetworkSession](#use-parsers) et [inNetworkNotables](#use-parsers). |
| | |

### <a name="web-servers"></a>Serveurs Web

| Tâche | Description |
| --- | --- |
| **Normaliser les événements des serveurs Web** | Pour normaliser les événements à partir d’un serveur Web, mappez les champs d' [événement](#event-fields), les [champs de session réseau](#network-session-fields)et les [champs de session http](#http-session-fields). Veillez à définir la valeur`EventType` sur `HTTP Session` et suivez les instructions spécifiques à la session http pour les champs `EventResult` et `EventResultDetails`. <br><br>Veillez à inclure votre analyseur spécifique à la source dans les analyseurs source-agnostique [imNetworkSession](#use-parsers) et [imWebSession](#use-parsers). |
| **Utiliser des événements de serveur Web** | Les événements Web Server sont exposés dans le cadre de l’analyseur source-agnostique [imNetworkSession](#use-parsers) . Toutefois, pour utiliser des champs spécifiques à HTTP, utilisez l’analyseur [imWebSession](#use-parsers) . |
| | |

### <a name="web-security-gateways"></a>Passerelles de sécurité Web

| Tâche | Description |
| --- | --- |
| **Normaliser les événements des passerelles de sécurité Web** | Pour normaliser les événements à partir d’une passerelle de serveur Web, mappez les champs d' [événement](#event-fields), les [champs de session réseau](#network-session-fields), les [champs de session http](#http-session-fields), les [champs d’inspection de session](#inspection-fields) et éventuellement les champs intermédiaires. <br><br>Veillez à définir la valeur`EventType` sur `HTTP` et suivez les instructions spécifiques à la session http pour les champs `EventResult` et `EventResultDetails`. <br><br>Veillez à inclure votre analyseur spécifique à la source dans les analyseurs source-agnostique [imNetworkSession](#use-parsers) et [imWebSession](#use-parsers) Filtrez également tous les événements détectés par le moteur d’inspection et ajoutez-les à l’analyseur source-agnostique [inNetworkNotables](#use-parsers) . |
| **Utiliser les événements des passerelles de sécurité Web** | Les événements Web Server sont exposés dans le cadre de l’analyseur source-agnostique [imNetworkSession](#use-parsers) . <br><br>- Pour utiliser des champs spécifiques à HTTP, utilisez l’analyseur [imWebSession](#use-parsers) .<br>-Pour analyser les sessions détectées, utilisez l’analyseur source-agnostique [inNetworkNotables](#use-parsers) . |
| | |


## <a name="use-parsers"></a>Utiliser les analyseurs

Pour utiliser l’analyseur source-agnostique qui unifie tous les analyseurs intégrés et vérifier que votre analyse s’exécute sur toutes les sources configurées, utilisez l’un des analyseurs suivants :


- **imNetworkSession**, pour toutes les sessions réseau
- **imWebSession**, pour les sessions HTTP, généralement signalées par des serveurs Web, des proxys Web et des passerelles de sécurité Web
- **inNetworkNotables**, pour les sessions détectées par un moteur de détection, généralement suspectes. Les événements notables sont généralement signalés par les systèmes de prévention d’intrusion, les pare-feu et les passerelles de sécurité Web.

Déployez les [analyseurs indépendants de la source et spécifiques de la source](normalization-about-parsers.md) à partir du [référentiel GitHub Microsoft Sentinel](https://aka.ms/AzSentinelNetworkSession).

### <a name="built-in-source-specific-parsers"></a>Analyseurs intégrés spécifiques de la source

Microsoft Sentinel fournit les analyseurs d’événements de Network Session suivants, propres aux produits :

- Analyseurs spécifiques de la source :
  - **Microsoft 365 Defender pour les points de terminaison** -vimNetworkSessionMicrosoft365Defender
  - **Microsoft Defender pour IoT-Points de terminaison (MD4IoT)** -vimNetworkSessionMD4IoT
  - **Microsoft Sysmon pour Linux** -vimNetworkSessionSysmonLinux
  - **Pare-feu WindowsEvent**- L’activité du pare-feu Windows, collectée à l’aide d’événements Windows 515x, collectée à l’aide de l’agent Log Analytics ou de l’agent Azure Monitor dans l’événement ou la table WindowsEvent, vimNetworkSessionMicrosoftWindowsEventFirewall 

Les analyseurs peuvent être déployés à partir du [référentiel GitHub de Microsoft Sentinel](https://aka.ms/AzSentinelNetworkSession).

### <a name="add-your-own-normalized-parsers"></a>Ajouter vos propres analyseurs normalisés

Quand vous implémentez des analyseurs personnalisés pour le modèle d’informations sur Network Session, nommez vos fonctions KQL avec la syntaxe suivante : `imNetworkSession<vendor><Product>`. Cette fonction doit mapper tous les champs pertinents pour la source.

Ajoutez votre fonction KQL aux analyseurs agnostiques sources appropriés, selon les besoins, en fonction de leurs sources de journal. Pour plus d’informations, consultez :

- [Sources de journaux NetFlow](#netflow-log-sources)
- [Sources de journaux pare-feu](#firewall-log-sources)
- [Sources du journal des systèmes de prévention des intrusions (IPS)](#intrusion-prevention-systems-ips-log-sources)
- [Serveurs Web](#web-servers)
- [Passerelles de sécurité Web](#web-security-gateways)

## <a name="schema-details"></a>Détails du schéma

Le modèle d’informations sur les sessions réseau est aligné est le [schéma d’entité réseau OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/network.md).

Pour se conformer aux meilleures pratiques du secteur, le schéma de session réseau utilise les descripteurs **Src** et **Dst** pour identifier les périphériques source et de destination de la session réseau, sans inclure le jeton **Dvc** dans le nom de champ.

Par exemple, le nom d’hôte de l’appareil source et l’adresse IP sont nommés respectivement **SrcHostname** et **SrcIpAddr**, et non **Hostname *DVC*** de SRC et de **src *DVC* IpAddr**. Le préfixe **Dvc** est utilisé uniquement pour le périphérique de création de rapports ou intermédiaire, le cas échéant.

Les champs qui décrivent l’utilisateur et l’application associés aux appareils source et de destination utilisent également les descripteurs **Src** et **Dst** .

D’autres schémas ASIM utilisent généralement **Target** au lieu de l'**heure d’été**.

### <a name="log-analytics-fields"></a>Champs Log Analytics

Les champs suivants sont générés par Log Analytics pour chaque enregistrement et peuvent être remplacés lors de la [création d’un connecteur personnalisé](create-custom-connector.md).


| Champ | Type | Discussion |
|-------|------|------------|
| <a name="timegenerated"></a>**TimeGenerated** | DATETIME | Heure à laquelle l’événement a été généré par l’appareil de création de rapports. |
| **\_ResourceId** | guid | L’ID de ressource Azure de l’appareil ou du service de création de rapports, ou l’ID de ressource de redirecteur de journal pour les événements transférés à l’aide de Syslog, CEF ou WEF. |
| **Type** | String | La table d'origine à partir de laquelle l'enregistrement a été récupéré. Ce champ est utile lorsque le même événement peut être reçu via plusieurs canaux vers différentes tables, mais avec les mêmes valeurs `EventVendor` et `EventProduct`. <br><br>Par exemple, un événement sysmon peut être collecté dans la table d’**événements** ou dans la table **SecurityEvent**. |
| | | |

> [!NOTE]
> Log Analytics ajoute également d’autres champs qui sont moins pertinents pour les cas d’usage de sécurité. Pour plus d’informations, consultez [Colonnes standard dans les journaux d’Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>

### <a name="event-fields"></a>Champs de l’événement

Les champs d’événement sont communs à tous les schémas et décrivent l’activité elle-même et l’appareil de création de rapports.

| Champ | Classe | Type | Description |
|-------|-------|------|-------------|
| **EventMessage** | Facultatif | Chaîne | Message général ou description, inclus dans l’enregistrement ou généré depuis l’enregistrement. |
| **EventCount** | Obligatoire | Integer | Nombre d’événements décrits par l’enregistrement. <br><br>Cette valeur est utilisée quand la source prend en charge l’agrégation, et un seul enregistrement peut représenter plusieurs événements. <br><br>**Remarque**: les sources NetFlow prennent en charge l’agrégation et le champ **EventCount** doit être défini sur la valeur du champ **FLUX** NetFlow. Pour les autres sources, la valeur est généralement définie sur `1`. |
| **EventStartTime** | Obligatoire | Date/heure | Si la source prend en charge l’agrégation et que l’enregistrement représente plusieurs événements, ce champ spécifie l’heure à laquelle le premier événement a été généré. Sinon, ce champ associe le champ [TimeGenerated](#timegenerated). |
| **EventEndTime** | Obligatoire | Alias | Alias du champ [TimeGenerated](#timegenerated). |
| **EventType** | Obligatoire | Énuméré | Décrit l’opération signalée par l’enregistrement.<br><br> Pour les enregistrements Network Sessions, les valeurs prises en charge sont les suivantes :<br>- `NetworkConnection`<br>- `NetworkSession`<br>- `HTTPsession` |
| **EventSubType** | Facultatif | String | Description supplémentaire du type, le cas échéant. <br> Pour les enregistrements Network Sessions, les valeurs prises en charge sont les suivantes :<br>- `Start`<br>- `End` |
| **EventResult** | Obligatoire | Énuméré | Décrit le résultat de l’événement, normalisé à l’une des valeurs prises en charge suivantes : <br> - `Success` <br> - `Partial` <br> - `Failure` <br> - `NA` (non applicable) <br><br>Pour une session HTTP, `Success` est défini en tant que code d’état inférieur à `400` , et `Failure` est défini en tant que code d’état supérieur à `400` . Pour obtenir la liste des codes d’état HTTP, reportez-vous à [W3 org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).<br><br>La source peut fournir uniquement une valeur pour le champ [EventResultDetails](#eventresultdetails), qui doit être analysé pour obtenir la valeur **EventResult**. |
| <a name="eventresultdetails"></a>**EventResultDetails** | Facultatif | String | Pour les sessions HTTP, la valeur doit être le code d’état HTTP. <br><br>**Remarque** : La valeur peut être fournie dans l’enregistrement source avec des termes différents, qui doivent être normalisés avec ces valeurs. La valeur d’origine doit être stockée dans le champ **EventOriginalResultDetails**.|
| **EventOriginalResultDetails**    | Facultatif    | Chaîne     |  Valeur fournie dans l’enregistrement d’origine pour [EventResultDetails](#eventresultdetails), si elle est fournie par la source.|
| **EventSeverity** | Obligatoire | Énuméré | La gravité de l’événement, s’il représente une menace détectée ou une alerte. Les valeurs possibles incluent `Informational`, `Low`, `Medium` et`High`. <br><br>Si l’événement ne représente pas une menace, utilisez la valeur`Informational`.<br><br>**Remarque** : La valeur peut être fournie dans l’enregistrement source avec des termes différents, qui doivent être normalisés avec ces valeurs. Stockez la valeur d’origine dans le champ [EventOriginalSeverity](#eventoriginalseverity) . |
| <a name="eventoriginalseverity"></a>**EventOriginalSeverity** | Facultatif | String | Valeur de gravité d’origine fournie dans l’enregistrement source. |
| **EventOriginalUid** | Facultatif | Chaîne | ID unique de l’enregistrement d’origine, s’il est fourni par la source.<br><br>Exemple : `69f37748-ddcd-4331-bf0f-b137f1ea83b` |
| **EventOriginalType** | Facultatif | Chaîne | Type ou ID d’événement d’origine, s’il est fourni par la source. <br><br>Exemple : `5031` |
| <a name="eventproduct"></a>**EventProduct** | Obligatoire | Chaîne | Produit générant l’événement.<br><br>Exemple : `Sysmon`<br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur. |
| **EventProductVersion** | Facultatif | Chaîne | Version du produit générant l’événement.<br><br>Exemple : `12.1` |
| **EventVendor** | Obligatoire | Chaîne | Fournisseur du produit générant l’événement.<br><br>Exemple : `Microsoft`<br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur. |
| **EventSchema** | Obligatoire | Chaîne | Nom du schéma. La version du schéma documenté ici est `NetworkSession`. |
| **EventSchemaVersion** | Obligatoire | Chaîne | Version du schéma. La version du schéma documenté ici est `0.2`. |
| **EventReportUrl** | Facultatif | Chaîne | URL fournie dans l’événement pour une ressource qui apporte des informations supplémentaires sur l’événement. |
| **Dvc** | Alias | Chaîne | Identificateur unique de l’appareil de création de rapports ou intermédiaire.<br><br>Exemple : `ContosoDc.Contoso.Azure`<br><br>Ce champ peut prendre l’alias des champs [DvcFQDN](#dvcfqdn), [Dvcld](#dvcid), [DvcHostname](#dvchostname) ou [DvclpAddr](#dvcipaddr). Pour les sources cloud pour lesquelles il n’y a pas d’appareil apparent, utilisez la même valeur que celle du champ [Event Product](#eventproduct). |
| <a name="dvcipaddr"></a>**DvcIpAddr** | Recommandé | Adresse IP | Adresse IP de l’appareil de création de rapports ou intermédiaire.<br><br>Exemple : `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>**DvcHostname** | Obligatoire | Chaîne | Nom d’hôte du périphérique de création de rapports ou intermédiaire, à l’exception des informations de domaine. Si aucun nom de périphérique n’est disponible, stockez l’adresse IP pertinente dans ce champ.<br><br>Exemple : `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | Recommandé | Chaîne | Domaine de l’appareil de création de rapports ou intermédiaire.<br><br>Exemple : `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | Recommandé | Énuméré | Type de  [DvcDomain](#dvcdomain), s’il est connu. Les valeurs possibles incluent :<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>**Remarque**: ce champ est obligatoire si le champ [DvcDomain](#dvcdomain) est utilisé. |
| <a name="dvcfqdn"></a>**DvcFQDN** | Facultatif | String | Nom d’hôte du périphérique de création de rapports ou intermédiaire, incluant des informations de domaine lorsqu’elles sont disponibles. <br><br> Exemple : `Contoso\DESKTOP-1282V4D`<br><br>**Remarque** : ce champ prend en charge à la fois le format FQDN traditionnel et le format Windows domain\hostname. Le champ [DvcDomainType](#dvcdomaintype) reflète le format utilisé.  |
| <a name="dvcid"></a>**DvcId** | Facultatif | String | L'identifiant du dispositif de reporting ou du dispositif intermédiaire tel qu'il figure dans l'enregistrement.<br><br>Exemple : `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | Facultatif | Énuméré | Type de [Dvcld](#dvcid), s’il est connu. Les valeurs possibles incluent :<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si plusieurs ID sont disponibles, utilisez le premier de la liste et stockez les autres à l’aide des noms de champs  **DvcAzureResourceId** et **DvcMDEid** , respectivement.<br><br>**Remarque**: ce champ est obligatoire si le champ [Dvcld](#dvcid) est utilisé. |
| **AdditionalFields** | Facultatif | Dynamique | Si votre source fournit des informations supplémentaires à conserver, conservez-les avec les noms de champs d’origine ou créez le champ dynamique **AdditionalFields**, puis ajoutez-y les informations supplémentaires sous forme de paires clé/valeur. |
| | | | |

### <a name="network-session-fields"></a>Champs de session réseau

Les champs suivants sont communs à l’ensemble de la journalisation de l’activité des sessions réseau :

| Champ | Classe | Type | Description |
|-------|-------|------|-------------|
|<a name="dstipaddr"></a> **DstIpAddr** | Recommandé | Adresse IP | L'adresse IP de la destination de la connexion ou de la session. <br><br>Exemple : `2001:db8::ff00:42:8329`<br><br>**Remarque**: cette valeur est obligatoire si [DstHostname](#dsthostname) est spécifié. |
| <a name="dstportnumber"></a>**DstPortNumber** | Facultatif | Integer | Port IP de destination.<br><br>Exemple : `443` |
| <a name="dsthostname"></a>**DstHostname** | Recommandé | Chaîne | Nom d’hôte de l’appareil de destination, à l’exception des informations de domaine. Si aucun nom de périphérique n’est disponible, stockez l’adresse IP pertinente dans ce champ.<br><br>Exemple : `DESKTOP-1282V4D`<br><br>**Remarque**: cette valeur est obligatoire si [DstIpAddr](#dstipaddr) est spécifié. |
| **Nom d’hôte** | Alias | | Alias de [DstHostname](#dsthostname) |
| <a name="dstdomain"></a>**DstDomain** | Recommandé | Chaîne | Domaine de l’appareil de destination.<br><br>Exemple : `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | Recommandé | Énuméré | Type de [DstDomain](#dstdomain), s’il est connu. Les valeurs possibles incluent :<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>Obligatoire si [DstDomain](#dstdomain) est utilisé. |
| **DstFQDN** | Facultatif | String | Nom d’hôte de l’appareil de destination, y compris les informations de domaine, le cas échéant. <br><br>Exemple : `Contoso\DESKTOP-1282V4D` <br><br>**Remarque** : ce champ prend en charge à la fois le format FQDN traditionnel et le format Windows domain\hostname. Le champ [DstDomainType](#dstdomaintype) reflète le format utilisé.   |
| <a name="dstdvcid"></a>**DstDvcId** | Facultatif | String | ID de l’appareil de destination comme indiqué dans l’enregistrement.<br><br>Exemple : `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | Facultatif | Énuméré | Type de [DstDvcId](#dstdvcid), s’il est connu. Les valeurs possibles incluent :<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>Si plusieurs ID sont disponibles, utilisez le premier de la liste ci-dessus, et stockez les autres à l’aide des noms de champs  **DvcAzureResourceId** et **DvcMDEid**, respectivement.<br><br>Obligatoire si **DstDeviceId** est utilisé.|
| **DstDeviceType** | Facultatif | Énuméré | Type de l’appareil de destination. Les valeurs possibles incluent :<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="dstuserid"></a>**DstUserId** | Facultatif | String | Représentation unique, alphanumérique et lisible par l’ordinateur de l’utilisateur de destination. <br><br>Les types et formats pris en charge comprennent les suivants :<br>- **SID** (Windows) : `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux) : `4578`<br>-  **AADID** (Azure Active Directory) : `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId** : `00urjk4znu3BcncfY0h7`<br>-  **AWSId** : `72643944673`<br><br>Stockez le type ID dans le champ [DstUserIdType](#dstuseridtype). Si d’autres ID sont disponibles, nous vous recommandons de normaliser les noms de champs à **DstUserSid**, **DstUserUid**, **DstUserAADID**, **DstUserOktaId** et **UserAwsId**, respectivement. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : `S-1-12` |
| <a name="dstuseridtype"></a>**DstUserIdType** | Facultatif | Énuméré | Type de l’ID stocké dans le champ [DstUserId](#dstuserid). <br><br>Les valeurs prises en charge sont `SID`, `UIS`, `AADID`, `OktaId` et `AWSId`. |
| <a name="dstusername"></a>**DstUsername** | Facultatif | String | Nom d’utilisateur de destination, y compris les informations de domaine, le cas échéant. <br><br>Utilisez l'un des formats suivants et dans l'ordre de priorité suivant :<br>- **Upn/E-mail** : `johndow@contoso.com`<br>- **Windows** : `Contoso\johndow`<br>- **DN** : `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple** : `johndow`. Utilisez le formulaire simple uniquement si les informations de domaine ne sont pas disponibles.<br><br>Stockez le type Nom d’utilisateur dans le champ [DstUsernameType](#dstusernametype). Si d’autres ID sont disponibles, nous vous recommandons de normaliser les noms de champs à **DstUserUpn**, **DstUserWindows** et **DstUserDn**. Pour plus d’informations, consultez [l’entité User](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : `AlbertE` |
| **Utilisateur** | Alias | | Alias de [DstUsername](#dstusername) |
| <a name="dstusernametype"></a>**DstUsernameType** | Facultatif | Énuméré | Spécifie le type du nom d’utilisateur stocké dans le champ [DstUsername](#dstusername). Les valeurs prises en charge incluent : `UPN`, `Windows`, `DN` et `Simple`. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : `Windows` |
| **DstUserType** | Facultatif | Énuméré | Type d’intervenant. Les valeurs prises en charge sont :<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Remarque** : La valeur peut être fournie dans l’enregistrement source avec des termes différents, qui doivent être normalisés avec ces valeurs. Stockez la valeur d’origine dans le champ [DstOriginalUserType](#dstoriginalusertype) . |
| <a name="dstoriginalusertype"></a>**DstOriginalUserType** | Facultatif | String | Le type d'utilisateur de destination original, s'il est fourni par la source. |
| **DstUserDomain** | Facultatif | String | Ce champ est mis en œuvre uniquement à des fins de compatibilité descendante. ASIM requiert que les informations de domaine, si elles sont disponibles, fassent partie du champ [DstUsername](#dstusername) . |
| <a name="dstappname"></a>**DstAppName** | Facultatif | String | Nom de l’application de destination.<br><br>Exemple : `Facebook` |
| <a name="dstappid"></a>**DstAppId** | Facultatif | String | L'identifiant de l'application de destination, tel que signalé par le dispositif de reporting.<br><br>Exemple : `124` |
| **DstAppType** | Facultatif | Chaîne | Type de l’application effectuant l’autorisation pour le compte de l’intervenant. Les valeurs prises en charge sont :<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `URL`<br>- `SaaS application`<br>- `Other`<br><br>Ce champ est obligatoire si [DstAppName](#dstappname) ou [DstAppId](#dstappid) sont utilisés. |
| **DstZone** | Facultatif | String | Zone réseau de la destination, telle que définie par le périphérique de création de rapport.<br><br>Exemple : `Dmz` |
| **DstInterfaceName** | Facultatif | String | Interface réseau que l’appareil de destination utilise pour la connexion ou la session.<br><br>Exemple : `Microsoft Hyper-V Network Adapter` |
| **DstInterfaceGuid** | Facultatif | String | GUID de l’interface réseau utilisée sur le périphérique de destination.<br><br>Exemple :<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **DstMacAddr** | Facultatif | String | Adresse MAC de l’interface réseau que l’appareil de destination utilise pour la connexion ou la session.<br><br>Exemple : `06:10:9f:eb:8f:14` |
| **DstGeoCountry** | Facultatif | Pays ou région | Pays associé à l’adresse IP de destination. Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).<br><br>Exemple : `USA` |
| **DstGeoRegion** | Facultatif | Région | Région, ou état, au sein d’un pays associé à l’adresse IP de destination Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).<br><br>Exemple : `Vermont` |
| **DstGeoCity** | Facultatif | City | Ville associée à l’adresse IP de destination. Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).<br><br>Exemple : `Burlington` |
| **DstGeoLatitude** | Facultatif | Latitude | Latitude de la coordonnée géographique associée à l’adresse IP de destination. Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).<br><br>Exemple : `44.475833` |
| **DstGeoLongitude** | Facultatif | Longitude | Longitude de la coordonnée géographique associée à l’adresse IP de destination. Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).<br><br>Exemple : `73.211944` |
| <a name="srcipaddr"></a>**SrcIpAddr** | Recommandé | Adresse IP | Adresse IP d’origine de la connexion ou de la session. Cette valeur est obligatoire si **SrcHostname** est spécifié.<br><br>Exemple : `77.138.103.108` |
| **IpAddr** | Alias | | Alias de [SrcIpAddr](#srcipaddr) |
| **SrcPortNumber** | Facultatif | Integer | Port IP d’où provient la connexion. Peut ne pas être pertinent pour une session comprenant plusieurs connexions.<br><br>Exemple : `2335` |
| **SrcHostname** | Recommandé | Chaîne | Nom d’hôte de l’appareil source, à l’exception des informations de domaine. Si aucun nom de périphérique n’est disponible, stockez l’adresse IP pertinente dans ce champ. Cette valeur est obligatoire si [SrcIpAddr](#srcipaddr) est spécifié.<br><br>Exemple : `DESKTOP-1282V4D` |
|<a name="srcdomain"></a> **SrcDomain** | Recommandé | Chaîne | Domaine de l’appareil source.<br><br>Exemple : `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Recommandé | Énuméré | Type de [SrcDomain](#srcdomain), s’il est connu. Les valeurs possibles incluent :<br>- `Windows`(par exemple :`contoso`)<br>- `FQDN`(par exemple :`microsoft.com`)<br><br>Obligatoire si [SrcDomain](#srcdomain) est utilisé. |
| **SrcFQDN** | Facultatif | String | Nom d’hôte de l’appareil source, y compris les informations de domaine, le cas échéant. <br><br>**Remarque** : ce champ prend en charge à la fois le format FQDN traditionnel et le format Windows domain\hostname. Le champ [SrcDomainType](#srcdomaintype) reflète le format utilisé. <br><br>Exemple : `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Facultatif | Chaîne | ID de l’appareil source comme indiqué dans l’enregistrement.<br><br>Par exemple : `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Facultatif | Énuméré | Type de [SrcDvcId](#srcdvcid), s’il est connu. Les valeurs possibles incluent :<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si plusieurs ID sont disponibles, utilisez le premier de la liste ci-dessus, et stockez les autres à l’aide des noms de champ **SrcDvcAzureResourceId** et **SrcDvcMDEid**, respectivement.<br><br>**Remarque**: ce champ est obligatoire si le champ [SrcDvcld](#srcdvcid) est utilisé. |
| **SrcDeviceType** | Facultatif | Énuméré | Type de l’appareil source. Les valeurs possibles incluent :<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | Facultatif | String | Représentation unique, alphanumérique et lisible par l’ordinateur de l’utilisateur source. Les formats et types pris en charge sont :<br>-  **SID** (Windows) : `S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID** (Linux) : `4578`<br>-  **AADID** (Azure Active Directory) : `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId** : `00urjk4znu3BcncfY0h7`<br>-  **AWSId** : `72643944673`<br><br>Stockez le type ID dans le champ [SrcUserIdType](#srcuseridtype). Si d’autres ID sont disponibles, nous vous recommandons de normaliser les noms de champs à SrcUserSid, SrcUserUid, SrcUserAadId, SrcUserOktaId et UserAwsId, respectivement. Pour plus d’informations, consultez l’entité User.<br><br>Exemple : S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Facultatif | Énuméré | Type de l’ID stocké dans le champ [SrcUserId](#srcuserid). Les valeurs prises en charge incluent : `SID`, `UIS`, `AADID`, `OktaId` et `AWSId`. |
| <a name="srcusername"></a>**SrcUsername** | Facultatif | String | Nom de l’utilisateur source, y compris les informations de domaine, le cas échéant. Utilisez l'un des formats suivants et dans l'ordre de priorité suivant :<br>- **Upn/E-mail** : `johndow@contoso.com`<br>- **Windows** : `Contoso\johndow`<br>- **DN** : `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple** : `johndow`. Utilisez le formulaire simple uniquement si les informations de domaine ne sont pas disponibles.<br><br>Stockez le type Nom d’utilisateur dans le champ [SrcUsernameType](#srcusernametype). Si d’autres ID sont disponibles, nous vous conseillons de normaliser les noms des champs pour **SrcUserUpn**, **SrcUserWindows** et **SrcUserDn**.<br><br>Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : `AlbertE` |
| <a name="srcusernametype"></a>**SrcUsernameType** | Facultatif | Énuméré | Spécifie le type du nom d’utilisateur stocké dans le champ [SrcUsername](#srcusername). Les valeurs prises en charge sont `UPN`, `Windows`, `DN` et `Simple`. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : `Windows` |
| **SrcUserType** | Facultatif | Énuméré | Type d’intervenant. Les valeurs autorisées sont les suivantes :<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Remarque** : La valeur peut être fournie dans l’enregistrement source avec des termes différents, qui doivent être normalisés avec ces valeurs. Stockez la valeur d’origine dans le champ [EventOriginalSeverity](#eventoriginalseverity) . |
| **SrcOriginalUserType** | | | Le type d'utilisateur source original, s'il est fourni par la source. |
| **SrcUserDomain** | Facultatif | String | Ce champ est mis en œuvre uniquement à des fins de compatibilité descendante. ASIM requiert que les informations de domaine, si elles sont disponibles, fassent partie du champ [SrcUsername](#srcusername) . |
| <a name="srcappname"></a>**SrcAppName** | Facultatif | String | Fournir le nom de l’application source. <br><br>Exemple : `filezilla.exe` |
| <a name="srcappid"></a>**SrcAppId** | Facultatif | String | L'identifiant de l'application de destination, tel que signalé par le dispositif de reporting.<br><br>Exemple : `124` |
| **SrcAppType** | Facultatif | String | Type de l’application source. Les valeurs prises en charge sont :<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `Other`<br><br>Ce champ est obligatoire si [SrcAppName](#srcappname) ou [SrcAppId](#srcappid) sont utilisés. |
| **SrcZone** | Facultatif | String | Zone réseau de la source, telle que définie par le périphérique de création de rapport.<br><br>Exemple : `Internet` |
| **SrcIntefaceName** | Facultatif | String | Interface réseau que l’appareil source utilise pour la connexion ou la session. <br><br>Exemple : `eth01` |
| **SrcInterfaceGuid** | Facultatif | String | GUID de l’interface réseau utilisée sur le périphérique source.<br><br>Exemple :<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **SrcMacAddr** | Facultatif | String | Adresse MAC de l’interface réseau d’où provient la connexion ou la session.<br><br>Exemple : `06:10:9f:eb:8f:14` |
| **SrcGeoCountry** | Facultatif | Pays ou région | Pays associé à l’adresse IP source.<br><br>Exemple : `USA` |
| **SrcGeoRegion** | Facultatif | Région | Région au sein d’un pays associé à l’adresse IP source.<br><br>Exemple : `Vermont` |
| **SrcGeoCity** | Facultatif | City | Ville associée à l’adresse IP source.<br><br>Exemple : `Burlington` |
| **SrcGeoLatitude** | Facultatif | Latitude | Latitude de la coordonnée géographique associée à l’adresse IP source.<br><br>Exemple : `44.475833` |
| **SrcGeoLongitude** | Facultatif | Longitude | Longitude de la coordonnée géographique associée à l’adresse IP source.<br><br>Exemple : `73.211944` |
| **NetworkApplicationProtocol** | Facultatif | String | Protocole de couche Application utilisé par la connexion ou la session. Si la valeur [DstPortNumber](#dstportnumber) est fournie, nous vous recommandons d’inclure également  **NetworkApplicationProtocol** . Si la valeur n’est pas disponible à partir de la source, dérivez la valeur de la valeur [DstPortNumber](#dstportnumber) .<br><br>Exemple : `HTTP` |
| **NetworkProtocol** | Facultatif | Énuméré | Protocole IP utilisé par la connexion ou la session, comme indiqué dans l' [attribution de protocole IANA](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml). En général, `TCP`,`UDP` et `ICMP`.<br><br>Exemple : `TCP` |
| **NetworkDirection** | Facultatif | Énuméré | Direction de la connexion ou de la session, vers l’intérieur ou l’extérieur de l’organisation. Les valeurs prises en charge sont : `Inbound`, `Outbound`, `Listen`. `Listen` indique qu’un appareil a commencé à accepter des connexions réseau, mais qu’il n’est pas réellement connecté.|
| <a name="networkduration"></a>**NetworkDuration** | Facultatif | Integer | Durée, en millisecondes, de la session ou de la connexion réseau<br><br>Exemple : `1500` |
| **Durée** | Alias | | Alias [NetworkDuration](#networkduration) |
| **NetworkIcmpCode** | Facultatif | Integer | Pour un message ICMP, la valeur numérique du type de message ICMP tel que décrit dans la [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) pour les connexions réseau IPv4, ou dans la [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443) pour les connexions réseau IPv6. Si une valeur [NetworkIcmpType](#networkicmptype) est fournie, ce champ est obligatoire. Si la valeur n’est pas disponible à partir de la source, dérivez la valeur de la valeur [NetworkIcmpType](#networkicmptype) .<br><br>Exemple : `34` |
|<a name="networkicmptype"></a> **NetworkIcmpType** | Facultatif | String | Pour un message ICMP, la représentation du texte du type de message ICMP tel que décrit dans la [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) pour les connexions réseau IPv4, ou dans la [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443) pour les connexions réseau IPv6.<br><br>Exemple : `Destination Unreachable` |
| **DstBytes** | Recommandé | Integer | Nombre d’octets envoyés de la destination à la source pour la connexion ou la session. Si l’événement est agrégé, **DstBytes** doit être la somme de toutes les sessions agrégées.<br><br>Exemple : `32455` |
| **SrcBytes** | Recommandé | Integer | Nombre d’octets envoyés de la source à la destination pour la connexion ou la session. Si l’événement est agrégé, **SrcBytes** doit être la somme de toutes les sessions agrégées.<br><br>Exemple : `46536` |
| **NetworkBytes** | Facultatif | Integer | Nombre d’octets envoyés dans les deux sens. Si les valeurs **BytesReceived** et **BytesSent** existent, la valeur **BytesTotal** doit être égale à leur somme. Si l’événement est agrégé, **NetworkBytes** doit être la somme de toutes les sessions agrégées.<br><br>Exemple : `78991` |
| **DstPackets** | Facultatif | Integer | Nombre de paquets envoyés de la destination à la source pour la connexion ou la session. La signification d’un paquet est définie par le périphérique de création de rapports. Si l’événement est agrégé, **DstPackets** doit être la somme de toutes les sessions agrégées.<br><br>Exemple : `446` |
| **SrcPackets** | Facultatif | Integer | Nombre de paquets envoyés de la source à la destination pour la connexion ou la session. La signification d’un paquet est définie par le périphérique de création de rapport. Si l’événement est agrégé, **SrcPackets** doit être la somme de toutes les sessions agrégées.<br><br>Exemple : `6478` |
| **NetworkPackets** | Facultatif | Integer | Nombre de paquets envoyés dans les deux sens. Si les valeurs **PacketsReceived** et **PacketsSent** existent, la valeur **BytesTotal** doit être égale à leur somme. La signification d’un paquet est définie par le périphérique de création de rapport. Si l’événement est agrégé, **NetworkPackets** doit être la somme de toutes les sessions agrégées.<br><br>Exemple : `6924` |
|<a name="networksessionid"></a>**NetworkSessionId** | Facultatif | string | Identificateur de session signalé par le périphérique de création de rapport. <br><br>Exemple : `172\_12\_53\_32\_4322\_\_123\_64\_207\_1\_80` |
| **SessionId** | Alias | Chaîne | Alias [NetworkSessionId](#networksessionid) |
| | | | |

### <a name="intermediary-device-fields"></a><a name="Intermediary"></a>Champs d’appareil intermédiaire

Les champs suivants sont utiles si l’enregistrement contient des informations sur un périphérique intermédiaire, tel qu’un pare-feu ou un proxy, qui relaie la session réseau.

| Champ | Classe | Type | Description |
| --- | --- | --- | --- |
| **DstNatIpAddr** | Facultatif | Adresse IP | Si elle est signalée par un périphérique NAT intermédiaire, adresse IP utilisée par le périphérique NAT pour la communication avec la source.<br><br>Exemple : `2::1` |
| **DstNatPortNumber** | Facultatif | Integer | S’il est signalé par un périphérique NAT intermédiaire tel qu’un pare-feu, le port utilisé par le périphérique NAT pour la communication avec la source.<br><br>Exemple : `443` |
| **SrcNatIpAddr** | Facultatif | Adresse IP | Si elle est signalée par un périphérique NAT intermédiaire tel qu’un pare-feu, adresse IP utilisée par le périphérique NAT pour la communication avec la destination.<br><br>Exemple : `4.3.2.1` |
| **SrcNatPortNumber** | Facultatif | Integer | S’il est signalé par un périphérique NAT intermédiaire tel qu’un pare-feu, le port utilisé par le périphérique NAT pour la communication avec la destination.<br><br>Exemple : `345` |
| **DvcInboundInterface** | Facultatif | String | En cas de signalement par un périphérique intermédiaire, interface réseau utilisée par le périphérique NAT pour la connexion au périphérique source.<br><br>Exemple : `eth0` |
| **DvcOutboundInterface** | Facultatif | String | En cas de signalement par un périphérique intermédiaire, interface réseau utilisée par le périphérique NAT pour la connexion au périphérique de destination.<br><br>Exemple : `Ethernet adapter Ethernet 4e` |
| | | | |

### <a name="http-session-fields"></a><a name="http-session-fields"></a>Champs de session HTTP

Une session HTTP est une session réseau qui utilise le protocole HTTP. Ces sessions sont souvent signalées par des serveurs Web, des proxys Web et des passerelles de sécurité Web. Voici des champs supplémentaires spécifiques aux sessions HTTP :

| Champ | Classe | Type | Description |
| --- | --- | --- | --- |
| **Url** | Recommandé | Chaîne | Pour les sessions réseau HTTP/HTTPs, URL de la requête HTTP complète, y compris les paramètres. Ce champ est obligatoire lorsque l’événement représente une session HTTP.<br><br>Exemple : `https://contoso.com/fo/?k=v&amp;q=u#f` |
| **UrlCategory** | Facultatif | String | Regroupement défini d’une URL ou de la partie domaine de l’URL. La catégorie est généralement fournie par les passerelles de sécurité Web et est basée sur le contenu du site vers lequel pointe l’URL.<br><br>Exemple : moteurs de recherche, adultes, Actualités, publicité et domaines parqués. |
| **UrlOriginal** | Facultatif | String | Valeur d’origine de l’URL, lorsque l’URL a été modifiée par le périphérique de création de rapports et que les deux valeurs sont fournies. |
| **HttpVersion** | Facultatif | String | Version de requête HTTP pour les connexions réseau HTTP/HTTPS.<br><br>Exemple : `2.0` |
| **HttpRequestMethod** | Recommandé | Énuméré | Méthode HTTP pour les sessions réseau HTTP/HTTPS. Les valeurs sont définies dans [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4) et [RFC 5789](https://datatracker.ietf.org/doc/html/rfc5789#section-2), et incluent `GET` , `HEAD` , `POST` , `PUT` , `DELETE` , `CONNECT` , `OPTIONS` , `TRACE` et `PATCH` .<br><br>Exemple : `GET` |
| **HttpStatusCode** | Alias | | Code d’état HTTP pour les sessions réseau HTTP/HTTPS. Alias de[EventResultDetails](#eventresultdetails). |
| <a name="httpcontenttype"></a>**HttpContentType** | Facultatif | String | En-tête de type de contenu de réponse HTTP pour les sessions réseau HTTP/HTTPS. <br><br>**Remarque**: le champ **HttpContentType** peut inclure à la fois le format de contenu et des paramètres supplémentaires, tels que l’encodage utilisé pour obtenir le format réel.<br><br> Exemple : `text/html; charset=ISO-8859-4` |
| **HttpContentFormat** | Facultatif | String | Partie du format de contenu de [HttpContentType](#httpcontenttype) <br><br> Exemple : `text/html` |
| **HttpReferrer** | Facultatif | String | En-tête de référent HTTP pour les sessions réseau HTTP/HTTPS.<br><br>**Remarque**: Asim, synchronisé avec OSSEM, utilise l’orthographe correcte pour *referrer* et non l’en-tête HTTP d’origine.<br><br>Exemple : `https://developer.mozilla.org/docs` |
| <a name="httpuseragent"></a>**HttpUserAgent** | Facultatif | String | En-tête de l’agent utilisateur HTTP pour les sessions réseau HTTP/HTTPS.<br><br>Exemple :<br> `Mozilla/5.0`(Windows NT 10,0 ; WOW64)<br>`AppleWebKit/537.36`(KHTML, comme Gecko)<br>`Chrome/83.0.4103.97 Safari/537.36` |
| **UserAgent** | Alias | | Alias de [HttpUserAgent](#httpuseragent) |
| **HttpRequestXff** | Facultatif | Adresse IP | En-tête HTTP X-Forwarded-for pour les sessions réseau HTTP/HTTPS.<br><br>Exemple : `120.12.41.1` |
| **HttpRequestTime** | Facultatif | Integer | Durée, en millisecondes, de l’envoi de la demande au serveur, le cas échéant.<br><br>Exemple : `700` |
| **HttpResponseTime** | Facultatif | Integer | Durée, en millisecondes, nécessaire à la réception d’une réponse sur le serveur, le cas échéant.<br><br>Exemple : `800` |
| **FileName** | Facultatif | String | Pour les chargements HTTP, nom du fichier téléchargé. |
| **FileMD5** | Facultatif | MD5 | Pour les téléchargements HTTP, le hachage MD5 du fichier téléchargé.<br><br>Exemple : `75a599802f1fa166cdadb360960b1dd0` |
| **FileSHA1** | Facultatif | SHA1 | Pour les téléchargements HTTP, le hachage SHA1 du fichier téléchargé.<br><br>Exemple :<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
| **FileSHA256** | Facultatif | SHA256 | Pour les téléchargements HTTP, le hachage SHA256 du fichier téléchargé.<br><br>Exemple :<br>`e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **FileSHA512** | Facultatif | SHA512 | Pour les téléchargements HTTP, le hachage SHA512 du fichier téléchargé. |
| **FileSize** | Facultatif | Integer | Pour les chargements HTTP, taille en octets du fichier téléchargé. |
| **FileContentType** | Facultatif | String | Pour les chargements HTTP, type de contenu du fichier téléchargé. |
| | | | |

En outre, les champs de schéma de mise en réseau standard suivants présentent des indications supplémentaires lorsqu’ils sont utilisés pour une session HTTP :

- **EventType** doit être une &quot; Session HTTP&quot;
- **EventResultDetails** doit être défini sur le code d’état HTTP.
- **EventResult** doit être en &quot;Success&quot; pour les codes d’état HTTP inférieurs à 400 et &quot;Failure&quot; dans le cas contraire.

### <a name="inspection-fields"></a><a name="inspection-fields"></a>Champs d’inspection

Les champs suivants sont utilisés pour représenter l’inspection qu’un périphérique de sécurité, tel qu’un pare-feu, une IPS ou une passerelle de sécurité Web a effectué :

| Champ | Classe | Type | Description |
| --- | --- | --- | --- |
| **NetworkRuleName** | Facultatif | String | Nom ou ID de la règle en vertu de laquelle [DvcAction](#dvcaction) a été décidé<br><br> Exemple : `AnyAnyDrop` |
| **NetworkRuleNumber** | Facultatif | Integer | Numéro de la règle en vertu de laquelle [DvcAction](#dvcaction) a été décidé<br><br>Exemple : `23` |
| **Règle** | Obligatoire | Chaîne | `NetworkRuleName` ou `NetworkRuleNumber` |
| <a name="dvcaction"></a>**DvcAction** | Facultatif | Énuméré | L’action prise sur la session réseau. Les valeurs prises en charge sont les suivantes :<br>- `Allow`<br>- `Deny`<br>- `Drop`<br>- `Drop ICMP`<br>- `Reset`<br>- `Reset Source`<br>- `Reset Destination`<br>- `Encrypt`<br>- `Decrypt`<br>- `VPNroute`<br><br>**Remarque** : La valeur peut être fournie dans l’enregistrement source avec des termes différents, qui doivent être normalisés avec ces valeurs. La valeur d’origine doit être stockée dans le champ [DvcOriginalAction](#dvcoriginalaction).<br><br>Exemple : `drop` |
| <a name="dvcoriginalaction"></a>**DvcOriginalAction** | Facultatif | String | Le [DvcAction](#dvcaction) d’origine, tel que fourni par l’appareil de reporting. |
| **ThreatId** | Facultatif | String | ID de la menace ou programme malveillant identifié dans la session réseau.<br><br>Exemple : `Tr.124` |
| **ThreatName** | Facultatif | String | Nom de la menace ou des programmes malveillants identifiés dans la session réseau.<br><br>Exemple : `EICAR Test File` |
| **ThreatCategory** | Facultatif | String | Catégorie de la menace ou programme malveillant identifié dans la session réseau.<br><br>Exemple : `Trojan` |
| **ThreatRiskLevel** | Facultatif | Integer | Niveau de risque associé à la Session Le niveau doit être un nombre compris entre **0** et **100**.<br><br>**Remarque** : La valeur peut être fournie dans l'enregistrement source en utilisant une échelle différente, qui doit être normalisée à cette échelle. La valeur d’origine doit être stockée dans le champ [ThreatRiskLevelOriginal](#threatriskleveloriginal). |
| <a name="threatriskleveloriginal"></a>**ThreatRiskLevelOriginal** | Facultatif | String | Niveau de risque signalé par le périphérique de reporting. |
| | | | |

### <a name="other-fields"></a>Autres champs

Si l’événement est signalé par l’un des points de terminaison de la session réseau, il peut inclure des informations sur le processus qui a initié ou a mis fin à la session. Dans ce cas, le [schéma d’événement du processus ASIM](process-events-normalization-schema.md) permet de normaliser ces informations.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Normalisation dans Microsoft Sentinel](normalization.md)
- [Informations de référence sur le schéma de normalisation de l’authentification Microsoft Sentinel (préversion publique)](authentication-normalization-schema.md)
- [Informations de référence de schéma de normalisation d’événement du fichier Microsoft Sentinel (préversion publique)](file-event-normalization-schema.md)
- [Informations de référence de schéma de normalisation du DNS Microsoft Sentinel](dns-normalization-schema.md)
- [Informations de référence du schéma de normalisation des événements de processus de Microsoft Sentinel](process-events-normalization-schema.md)
- [Informations de référence de schéma de normalisation d’événement du registre Microsoft Sentinel (préversion publique)](registry-event-normalization-schema.md)
