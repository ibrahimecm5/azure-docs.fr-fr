---
title: Informations de référence de schéma de normalisation DNS de Microsoft Sentinel | Microsoft Docs
description: Cet article décrit le schéma de normalisation DNS de Microsoft Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3289f52d0f6925374aee5656afc797ed0713f962
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521469"
---
# <a name="microsoft-sentinel-dns-normalization-schema-reference-public-preview"></a>Informations de référence de schéma de normalisation DNS de Microsoft Sentinel (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le modèle CIM DNS est utilisé pour décrire les événements signalés par un serveur DNS ou un système de sécurité DNS, et il est utilisé par Microsoft Sentinel pour activer l’analyse indépendante de la source.

Pour plus d’informations, consultez [Normalisation et modèle d’informations SIEM avancé (ASIM)](normalization.md).

> [!IMPORTANT]
> Le schéma de normalisation DNS est actuellement en PRÉVERSION. Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail de production.
>
> Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="schema-overview"></a>Vue d’ensemble du schéma

Le schéma DNS ASIM représente l’activité du protocole DNS, qui peut être enregistrée par un serveur DNS ou par un appareil envoyant des requêtes DNS à un serveur DNS. L’activité du protocole DNS comprend les requêtes DNS, les mises à jour du serveur DNS et les transferts de données en bloc du DNS. Étant donné que le schéma représente une activité de protocole, il est régi par des RFC et des listes de paramètres officiellement attribués, qui sont référencés dans cet article, le cas échéant. Le schéma DNS ne représente pas les événements d’audit du serveur DNS. 

L’activité la plus importante signalée par les serveurs DNS est une requête DNS, pour laquelle le champ `EventType` est défini sur `Query`.   

Les champs les plus importants d’un événement DNS sont les suivants :

- [DnsQuery](#query), qui indique le nom de domaine pour lequel la requête a été émise.
- [SrcIpAddr](#srcipaddr) (avec [IpAddr](#ipaddr) comme alias), qui représente l’adresse IP à partir de laquelle la requête a été générée. 
- [EventResultDetails](#eventresultdetails), qui indique si la requête a abouti et, dans le cas contraire, la raison de cet échec.
- Si disponible, [DnsResponseName](#responsename), qui contient la réponse fournie par le serveur à la requête. L’ASIM n’a pas besoin d’analyser la réponse, et son format varie selon les sources. Pour utiliser ce champ dans du contenu indépendant de la source, recherchez le contenu à l’aide des opérateurs `has` ou `contains`.

Les événements DNS collectés sur l’appareil du client peuvent également inclure des informations sur l’[utilisateur](#user) et le [processus](#process). 

## <a name="guidelines-for-collecting-dns-events"></a>Instructions pour la collecte d’événements DNS

DNS est un protocole unique en ce qu’il peut croiser un grand nombre d’ordinateurs. En outre, étant donné que DNS utilise le protocole UDP, les requêtes et les réponses sont découplées et ne sont pas directement liées les unes aux autres.

L’illustration suivante montre un flux de requête DNS simplifié, y compris quatre segments. Une requête réelle peut être plus complexe, avec plus de segments impliqués.

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="Un flux de requête DNS simplifié.":::

Étant donné que les segments de requête et de réponse ne sont pas directement connectés les uns aux autres dans le flux de requête DNS, la journalisation complète peut entraîner une duplication importante.

Le segment le plus précieux à consigner est la réponse au client, qui fournit les requêtes de nom de domaine, le résultat de la recherche et l’adresse IP du client. Alors que de nombreux systèmes DNS consignent uniquement ce segment, il existe une valeur dans la journalisation des autres parties. Par exemple, une attaque par empoisonnement du cache DNS tire souvent parti des réponses factices d’un serveur en amont.

Si votre source de données prend en charge la journalisation DNS complète et que vous avez choisi de consigner plusieurs segments, vous devez ajuster vos requêtes pour empêcher la duplication des données dans Microsoft Sentinel.

Par exemple, vous pouvez modifier votre requête avec la normalisation suivante :

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>Analyseurs

### <a name="source-agnostic-parsers"></a>Analyseurs indépendants de la source

Pour utiliser les analyseurs indépendants de la source qui unifient tous les analyseurs prêts à l’emploi et garantir que votre analyse s’exécute sur toutes les sources configurées, utilisez les fonctions KQL suivantes comme nom de table dans votre requête :

| Name | Description | Instructions d’utilisation |
| --- | --- | --- |
| **imDNS** | Analyseur d’agrégation qui utilise *union* pour inclure des événements normalisés de toutes les sources DNS. |– Mettez à jour cet analyseur si vous souhaitez ajouter ou supprimer des sources de l’analytique indépendante de la source. <br><br>– Utilisez cette fonction dans vos requêtes indépendantes de la source.|
| **ASimDNS** | Similaire à la fonction `imDns`, mais sans prise en charge des paramètres et, par conséquent, ne force pas le sélecteur d’heure de page **Journaux** à utiliser la value `custom`. |– Mettez à jour cet analyseur si vous souhaitez ajouter ou supprimer des sources de l’analytique indépendante de la source.<br><br>- Utilisez cette fonction dans vos requêtes indépendantes de la source si vous n’envisagez pas d’utiliser de paramètres.|
| **vimDNS\<vendor\>\<product\>** | Les analyseurs spécifiques à la source implémentent la normalisation pour une source spécifique, telle que *imDNSWindowsOMS*. |– Ajoutez un analyseur spécifique à une source lorsqu’il n’existe aucun analyseur de normalisation prêt à l’emploi. Mettez à jour l’analyseur d’agrégation `im` pour inclure la référence à votre nouvel analyseur. <br><br>– Mettez à jour un analyseur spécifique à la source pour résoudre les problèmes d’analyse et de normalisation.<br><br>– Utilisez un analyseur spécifique à la source pour l’analytique spécifique à la source.|
| **ASimDNS\<vendor\>\<product\>** | Les analyseurs spécifiques à la source implémentent la normalisation pour une source spécifique. Contrairement aux fonctions `vim*`, les fonctions `ASimDNS*` ne prennent pas en charge les paramètres. |– Ajoutez un analyseur spécifique à une source lorsqu’il n’existe aucun analyseur de normalisation prêt à l’emploi. Mettez à jour l’analyseur d’agrégation `ASim` pour inclure la référence à votre nouvel analyseur.<br><br>– Mettez à jour un analyseur spécifique à la source pour résoudre les problèmes d’analyse et de normalisation.<br><br>- Utilisez un analyseur spécifique à la source `ASim` pour les requêtes interactives quand vous n’utilisez pas de paramètres.|
| | | |

Les analyseurs peuvent être déployés à partir du [référentiel GitHub de Microsoft Sentinel](https://aka.ms/azsentinelDNS).

### <a name="out-of-the-box-source-specific-parsers"></a>Analyseurs prêts à l’emploi et spécifiques à la source

Microsoft Sentinel fournit les analyseurs DNS suivants, prêts à l’emploi et spécifiques au produit :

| **Nom** | **Description** |
| --- | --- |
|**Serveur DNS Microsoft**  |   **Collecté à l’aide du connecteur DNS et de l’agent Log Analytics** : <br> - `ASimDnsMicrosoftOMS` (standard) <br>- `vimDnsMicrosoftOMS` (paramétré) <br><br>  **Collecté à l’aide de NXlog** : <br> - `ASimDnsMicrosoftNXlog` (standard)<br>- `vimDnsMicrosoftNXlog` (paramétré) |
| **Pare-feu Azure** |- `ASimDnsAzureFirewall` (standard)<br>- `vimDnsAzureFirewall` (paramétré) |
|**Sysmon pour Windows** (événement 22) | **Collecté à l’aide de l’agent Log Analytics ou de l’agent Azure Monitor**, prenant en charge les tables `Event` et `WindowsEvent` : <br>- `ASimDnsMicrosoftSysmon` (standard)<br>- `vimDnsMicrosoftSysmon` (paramétré)  |
|**Cisco Umbrella**  | - `ASimDnsCiscoUmbrella` (standard)<br>- `vimDnsCiscoUmbrella` (paramétré)  |
|**Infoblox NIOS**  |- `ASimDnsInfobloxNIOS` (standard)<br>- `vimDnsInfobloxNIOS` (paramétré) |
| **DNS GCP** |- `ASimDnsGcp` (standard)<br>- `vimDnsGcp` (paramétré) |
| **Événements DNS Corelight Zeek** | - `ASimDnsCorelightZeek` (standard)<br>- `vimDnsCorelightZeek` (paramétré) |
| **zScaler ZIA** |- `AsimDnszScalerZIA` (standard)<br>- `vimDnszScalerZIA` (paramétré)  |
| | |

Les analyseurs peuvent être déployés à partir du [référentiel GitHub de Microsoft Sentinel](https://aka.ms/azsentinelDNS).

### <a name="add-your-own-normalized-parsers"></a>Ajouter vos propres analyseurs normalisés

Lorsque vous implémentez des analyseurs personnalisés pour le modèle d’informations DNS, nommez vos fonctions KQL en utilisant la syntaxe suivante :

- `vimDns<vendor><Product` pour les analyseurs paramétrés
- `ASimDns<vendor><Product` pour les analyseurs standard

### <a name="filtering-parser-parameters"></a>Paramètres de filtrage des analyseurs

Les analyseurs `im` et `vim*` prennent en charge les [paramètres de filtrage](normalization-about-parsers.md#optimized-parsers). Bien que ces analyseurs soient facultatifs, ils peuvent améliorer les performances de vos requêtes.

Les paramètres de filtrage suivants sont disponibles :

| Name     | Type      | Description |
|----------|-----------|-------------|
| **starttime** | DATETIME | Filtrez uniquement les requêtes DNS qui ont été exécutées à ce moment-là ou après. |
| **endtime** | DATETIME | Filtrez uniquement les requêtes DNS dont l’exécution s’est terminée à ce moment-là ou avant. |
| **srcipaddr** | string | Filtrez uniquement les requêtes DNS à partir de cette adresse IP source. |
| **domain_has_any**| dynamique | Filtrez uniquement les requêtes DNS où `domain` (ou `query`) présente l’un des noms de domaine listés, notamment comme partie du domaine de l’événement.
| **responsecodename** | string | Filtrez uniquement les requêtes DNS pour lesquelles le nom du code de réponse correspond à la valeur fournie. <br>Par exemple : `NXDOMAIN` |
| **response_has_ipv4** | string | Filtrez uniquement les requêtes DNS dans lesquelles le champ de réponse commence par l’adresse IP ou le préfixe d’adresse IP fourni. Utilisez ce paramètre lorsque vous souhaitez filtrer sur une seule adresse IP ou un seul préfixe. <br><br>Les résultats ne sont pas retournés pour les sources qui ne fournissent pas de réponse.|
| **response_has_any_prefix** | dynamique| Filtrez uniquement les requêtes DNS dans lesquelles le champ de réponse commence par l’une des adresses IP ou l’un des préfixes d’adresse IP listés. <br><br>Utilisez ce paramètre lorsque vous souhaitez filtrer sur une liste d’adresses IP ou de préfixes. <br><br>Les résultats ne sont pas retournés pour les sources qui ne fournissent pas de réponse. |
| **eventtype**| string | Filtrez uniquement les requêtes DNS du type spécifié. Si aucune valeur n’est spécifiée, seules les requêtes de recherche sont retournées. |
| | | |

Par exemple, pour filtrer uniquement les requêtes DNS à partir du dernier jour qui n’a pas réussi à résoudre le nom de domaine, utilisez :

```kql
imDns (responsecodename = 'NXDOMAIN', starttime = ago(1d), endtime=now())
```

Pour filtrer uniquement les requêtes DNS pour une liste spécifiée de noms de domaine, utilisez :

```kql
let torProxies=dynamic(["tor2web.org", "tor2web.com", "torlink.co",...]);
imDns (domain_has_any = torProxies)
```

## <a name="normalized-content"></a>Contenu normalisé

Pour obtenir la liste complète des règles d’analyse qui utilisent des événements DNS normalisés, consultez la section [Contenu de sécurité des requêtes DNS](normalization-content.md#dns-query-security-content).

## <a name="schema-details"></a>Détails du schéma

Le modèle d’informations DNS est aligné sur le [schéma d’entité DNS OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md).

Pour plus d’informations, consultez les [informations de référence sur les paramètres système du nom de domaine IANA (Internet Assigned Numbers Authority)](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).

### <a name="common-fields"></a>Champs communs

Les champs communs à tous les schémas sont décrits dans la [vue d’ensemble du schéma ASIM](normalization-about-schemas.md#common). Les champs suivants contiennent des instructions spécifiques pour les événements DNS :

| **Champ** | **Classe** | **Type**  | **Description** |
| --- | --- | --- | --- |
| **EventType** | Obligatoire | Énuméré | Indique l’opération signalée par l’enregistrement. <br><br> Pour les enregistrements DNS, cette valeur serait le [code d’opération DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Exemple : `lookup`|
| **EventSubType** | Facultatif | Énuméré | **requête** ou **réponse**. <br><br>Pour la plupart des sources, [seules les réponses sont consignées](#guidelines-for-collecting-dns-events) et par conséquent, la valeur est souvent **response**.  |
| <a name=eventresultdetails></a>**EventResultDetails** | Alias | | Raison ou détails du résultat rapporté dans le champ **_EventResult_**. <br><br> Met en alias le champ [ResponseCodeName](#responsecodename).|
| **EventSchemaVersion** | Obligatoire | Chaîne | La version du schéma documenté ici est **0.1.3**. |
| **EventSchema** | Obligatoire | Chaîne | Le nom du schéma documenté ici est **Dns**. |
| Champs **Dvc**| -      | -    | Pour les événements DNS, les champs d’appareil font référence au système qui signale l’événement DNS. |
| | | | |


### <a name="dns-specific-fields"></a>Champs spécifiques à DNS

Les champs ci-dessous sont spécifiques aux événements DNS, bien que de nombreux champs soient similaires à ceux d’autres schémas et suivent donc la même convention d’affectation de noms.

| **Champ** | **Classe** | **Type** | **Remarques** |
| --- | --- | --- | --- |
| <a name="src"></a>**Src** | Recommandé       | Chaîne     |    Identificateur unique de l’appareil source. <br><br>Ce champ peut prendre l’alias des champs [SrcDvcId](#srcdvcid), [SrcHostname](#srchostname) ou [SrcIpAddr](#srcipaddr). <br><br>Exemple : `192.168.12.1`       |
| <a name="srcipaddr"></a>**SrcIpAddr** | Recommandé | Adresse IP | L’adresse IP du client qui envoie la requête DNS. Pour une requête DNS récursive, cette valeur correspond généralement à l’appareil de création de rapports et, dans la plupart des cas, à `127.0.0.1`. <br><br>Exemple : `192.168.12.1` |
| **SrcPortNumber** | Facultatif | Integer | Port source de la requête DNS.<br><br>Exemple : `54312` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | Alias de [SrcIpAddr](#srcipaddr) |
| **SrcGeoCountry** | Facultatif | Pays ou région | Pays associé à l’adresse IP source.<br><br>Exemple : `USA` |
| **SrcGeoRegion** | Facultatif | Région | Région au sein d’un pays associé à l’adresse IP source.<br><br>Exemple : `Vermont` |
| **SrcGeoCity** | Facultatif | City | Ville associée à l’adresse IP source.<br><br>Exemple : `Burlington` |
| **SrcGeoLatitude** | Facultatif | Latitude | Latitude de la coordonnée géographique associée à l’adresse IP source.<br><br>Exemple : `44.475833` |
| **SrcGeoLongitude** | Facultatif | Longitude | Longitude de la coordonnée géographique associée à l’adresse IP source.<br><br>Exemple : `73.211944` |
| **SrcRiskLevel** | Facultatif | Integer | Niveau de risque associé à la source. La valeur doit être ajustée dans une plage de `0` à `100`, `0` étant un risque bénin et `100` étant un risque élevé.<br><br>Exemple : `90` |
| <a name="srchostname"></a>**SrcHostname** | Recommandé | Chaîne | Nom d’hôte de l’appareil source, à l’exception des informations de domaine. Si aucun nom de périphérique n’est disponible, stockez l’adresse IP pertinente dans ce champ. Cette valeur est obligatoire si [SrcIpAddr](#srcipaddr) est spécifié.<br><br>Exemple : `DESKTOP-1282V4D` |
| **Nom d’hôte** | Alias | | Alias de [SrcHostname](#srchostname) |
|<a name="srcdomain"></a> **SrcDomain** | Recommandé | Chaîne | Domaine de l’appareil source.<br><br>Exemple : `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Recommandé | Énuméré | Type de [SrcDomain](#srcdomain), s’il est connu. Les valeurs possibles incluent :<br>- `Windows`(par exemple :`contoso`)<br>- `FQDN`(par exemple :`microsoft.com`)<br><br>Obligatoire si [SrcDomain](#srcdomain) est utilisé. |
| **SrcFQDN** | Facultatif | String | Nom d’hôte de l’appareil source, y compris les informations de domaine, le cas échéant. <br><br>**Remarque** : ce champ prend en charge à la fois le format FQDN traditionnel et le format Windows domain\hostname. Le champ [SrcDomainType](#srcdomaintype) reflète le format utilisé. <br><br>Exemple : `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Facultatif | Chaîne | ID de l’appareil source comme indiqué dans l’enregistrement.<br><br>Par exemple : `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Facultatif | Énuméré | Type de [SrcDvcId](#srcdvcid), s’il est connu. Les valeurs possibles incluent :<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si plusieurs ID sont disponibles, utilisez le premier de la liste ci-dessus, et stockez les autres à l’aide des noms de champ **SrcDvcAzureResourceId** et **SrcDvcMDEid**, respectivement.<br><br>**Remarque**: ce champ est obligatoire si le champ [SrcDvcld](#srcdvcid) est utilisé. |
| **SrcDeviceType** | Facultatif | Énuméré | Type de l’appareil source. Les valeurs possibles incluent :<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | Facultatif | String | Représentation unique, alphanumérique et lisible par l’ordinateur de l’utilisateur source. Les formats et types pris en charge sont :<br>-  **SID** (Windows) : `S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID** (Linux) : `4578`<br>-  **AADID** (Azure Active Directory) : `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId** : `00urjk4znu3BcncfY0h7`<br>-  **AWSId** : `72643944673`<br><br>Stockez le type ID dans le champ [SrcUserIdType](#srcuseridtype). <br><br>Si d’autres ID sont disponibles, nous vous recommandons de normaliser les noms de champs respectivement en **SrcUserSid**, **SrcUserUid**, **SrcUserAadId**, **SrcUserOktaId** et **UserAwsId**. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Facultatif | Énuméré | Type de l’ID stocké dans le champ [SrcUserId](#srcuserid). Les valeurs prises en charge incluent : `SID`, `UIS`, `AADID`, `OktaId` et `AWSId`. |
| <a name="srcusername"></a>**SrcUsername** | Facultatif | String | Nom de l’utilisateur source, y compris les informations de domaine, le cas échéant. Utilisez l'un des formats suivants et dans l'ordre de priorité suivant :<br>- **Upn/E-mail** : `johndow@contoso.com`<br>- **Windows** : `Contoso\johndow`<br>- **DN** : `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple** : `johndow`. Utilisez le formulaire simple uniquement si les informations de domaine ne sont pas disponibles.<br><br>Stockez le type Nom d’utilisateur dans le champ [SrcUsernameType](#srcusernametype). Si d’autres ID sont disponibles, nous vous conseillons de normaliser les noms des champs pour **SrcUserUpn**, **SrcUserWindows** et **SrcUserDn**.<br><br>Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : `AlbertE` |
| <a name="user"></a>**Utilisateur** | Alias | | Alias de [SrcUsername](#srcusername) |
| <a name="srcusernametype"></a>**SrcUsernameType** | Facultatif | Énuméré | Spécifie le type du nom d’utilisateur stocké dans le champ [SrcUsername](#srcusername). Les valeurs prises en charge sont `UPN`, `Windows`, `DN` et `Simple`. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : `Windows` |
| **SrcUserType** | Facultatif | Énuméré | Type d’intervenant. Les valeurs autorisées sont les suivantes :<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Remarque** : La valeur peut être fournie dans l’enregistrement source avec des termes différents, qui doivent être normalisés avec ces valeurs. Stockez la valeur d’origine dans le champ [EventOriginalUserType](#eventoriginalusertype). |
| <a name="eventoriginalusertype"></a>**SrcOriginalUserType** | Facultatif | String | Le type d'utilisateur source original, s'il est fourni par la source. |
| **SrcUserDomain** | Facultatif | String | Ce champ est mis en œuvre uniquement à des fins de compatibilité descendante. ASIM requiert que les informations de domaine, si elles sont disponibles, fassent partie du champ [SrcUsername](#srcusername) . |
| <a name="srcprocessname"></a>**SrcProcessName**              | Facultatif     | String     |   Nom de fichier du processus qui lance la requête DNS. Ce nom est généralement considéré comme le nom du processus.  <br><br>Exemple : `C:\Windows\explorer.exe`  |
| <a name="process"></a>**Processus**        | Alias        |            | Alias de [SrcProcessName](#srcprocessname) <br><br>Exemple : `C:\Windows\System32\rundll32.exe`|
| **SrcProcessId**| Obligatoire    | Chaîne        | ID de processus (PID) du processus qui lance la requête DNS.<br><br>Exemple : `48610176`           <br><br>**Remarque** : Le type est défini en tant que *chaîne* pour la prise en charge de différents systèmes. Cependant, sur Windows et Linux cette valeur doit être numérique. <br><br>Si vous utilisez un ordinateur Windows ou Linux et avez utilisé un type différent, assurez-vous de convertir les valeurs. Par exemple, si vous avez utilisé une valeur hexadécimale, convertissez-la en valeur décimale.    |
| **SrcProcessGuid**              | Facultatif     | String     |  Identificateur unique généré (GUID) du processus qui lance la requête DNS.   <br><br> Exemple : `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| <a name="dst"></a>**Dst** | Recommandé       | Chaîne     |    Identificateur unique du serveur qui reçoit la requête DNS. <br><br>Ce champ peut prendre l’alias des champs [DstDvcId](#dstdvcid), [DstHostname](#dsthostname) ou [DstIpAddr](#dstipaddr). <br><br>Exemple : `192.168.12.1`       |
| <a name="dstipaddr"></a>**DstIpAddr** | Facultatif | Adresse IP | Adresse IP du serveur qui reçoit la requête DNS. Pour une requête DNS régulière, cette valeur correspond généralement à l’appareil de création de rapports et, dans la plupart des cas, à `127.0.0.1`.<br><br>Exemple : `127.0.0.1` |
| **DstGeoCountry** | Facultatif | Pays ou région | Pays associé à l’adresse IP de destination. Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).<br><br>Exemple : `USA` |
| **DstGeoRegion** | Facultatif | Région | Région, ou état, au sein d’un pays associé à l’adresse IP de destination Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).<br><br>Exemple : `Vermont` |
| **DstGeoCity** | Facultatif | City | Ville associée à l’adresse IP de destination. Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).<br><br>Exemple : `Burlington` |
| **DstGeoLatitude** | Facultatif | Latitude | Latitude de la coordonnée géographique associée à l’adresse IP de destination. Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).<br><br>Exemple : `44.475833` |
| **DstGeoLongitude** | Facultatif | Longitude | Longitude de la coordonnée géographique associée à l’adresse IP de destination. Pour plus d’informations, consultez [Types logiques](normalization-about-schemas.md#logical-types).<br><br>Exemple : `73.211944` |
| **DstcRiskLevel** | Facultatif | Integer | Niveau de risque associé à la destination. La valeur doit être ajustée dans une plage de 0 à 100, 0 étant un risque bénin et 100 étant un risque élevé.<br><br>Exemple : `90` |
| **DstPortNumber** | Facultatif | Integer  | Numéro du port de destination.<br><br>Exemple : `53` |
| <a name="dsthostname"></a>**DstHostname** | Facultatif | Chaîne | Nom d’hôte de l’appareil de destination, à l’exception des informations de domaine. Si aucun nom de périphérique n’est disponible, stockez l’adresse IP pertinente dans ce champ.<br><br>Exemple : `DESKTOP-1282V4D`<br><br>**Remarque**: cette valeur est obligatoire si [DstIpAddr](#dstipaddr) est spécifié. |
| <a name="dstdomain"></a>**DstDomain** | Facultatif | Chaîne | Domaine de l’appareil de destination.<br><br>Exemple : `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | Facultatif | Énuméré | Type de [DstDomain](#dstdomain), s’il est connu. Les valeurs possibles incluent :<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>Obligatoire si [DstDomain](#dstdomain) est utilisé. |
| **DstFQDN** | Facultatif | String | Nom d’hôte de l’appareil de destination, y compris les informations de domaine, le cas échéant. <br><br>Exemple : `Contoso\DESKTOP-1282V4D` <br><br>**Remarque** : ce champ prend en charge à la fois le format FQDN traditionnel et le format Windows domain\hostname. Le champ [DstDomainType](#dstdomaintype) reflète le format utilisé.   |
| <a name="dstdvcid"></a>**DstDvcId** | Facultatif | String | ID de l’appareil de destination comme indiqué dans l’enregistrement.<br><br>Exemple : `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | Facultatif | Énuméré | Type de [DstDvcId](#dstdvcid), s’il est connu. Les valeurs possibles incluent :<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>Si plusieurs ID sont disponibles, utilisez le premier de la liste ci-dessus, et stockez les autres à l’aide des noms de champs  **DvcAzureResourceId** et **DvcMDEid**, respectivement.<br><br>Obligatoire si **DstDeviceId** est utilisé.|
| **DstDeviceType** | Facultatif | Énuméré | Type de l’appareil de destination. Les valeurs possibles incluent :<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name=query></a>**DnsQuery** | Obligatoire | FQDN | Le domaine qui doit être résolu. <br><br>**Remarque** : Certaines sources envoient cette requête dans des formats différents. Par exemple, dans le protocole DNS lui-même, la requête comprend un point ( **.** ) à la fin, qui doit être supprimé.<br><br>Bien que le protocole DNS autorise plusieurs requêtes dans une seule requête, ce scénario est rare, voire même inexistant. Si la requête comporte plusieurs requêtes, stockez la première dans ce champ, puis conservez éventuellement le reste dans le champ [AdditionalFields](normalization-about-schemas.md#additionalfields).<br><br>Exemple : `www.malicious.com` |
| **Domaine** | Alias | | Prendre l’alias [Requête](#query). |
| **DnsQueryType** | Facultatif | Integer | Ce champ peut contenir des [codes de type d’enregistrement de ressource DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Exemple : `28`|
| **DnsQueryTypeName** | Recommandé | Énuméré | Ce champ peut contenir des noms de [type d’enregistrement de ressource DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Remarque** : IANA ne définit pas la casse des valeurs, donc Analytics doit normaliser la casse en fonction des besoins. Si la source fournit uniquement un code de type de requête numérique et non un nom de type de requête, l’analyseur doit inclure une table de recherche pour enrichir cette valeur.<br><br>Exemple : `AAAA`|
| <a name=responsename></a>**DnsResponseName** | Facultatif | Chaîne | Contenu de la réponse, tel qu’il est inclus dans l’enregistrement.<br> <br> Les données de réponse DNS sont incohérentes entre les appareils de création de rapports, sont complexes à analyser et ont moins de valeur pour l’analyse indépendante de la source. Par conséquent, le modèle CIM ne nécessite pas d’analyse ni de normalisation, et Microsoft Sentinel utilise une fonction auxiliaire pour fournir des informations de réponse. Pour plus d’informations, consultez [Gestion des réponses DNS](#handling-dns-response).|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Obligatoire | Énuméré | Le [code de réponse DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Remarque** : IANA ne définit pas la casse des valeurs, donc l’analyse doit normaliser la casse. Si la source fournit uniquement un code de réponse et non un nom de code de réponse, l’analyseur doit inclure une table de recherche pour enrichir cette valeur. <br><br> Si cet enregistrement représente une requête et non une réponse, affectez la valeur **NA**. <br><br>Exemple : `NXDOMAIN` |
| **DnsResponseCode** | Facultatif | Integer | Le [code de réponse numérique DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Exemple : `3`|
| **TransactionIdHex** | Recommandé | Chaîne | ID de transaction hexadécimal unique DNS. |
| **NetworkProtocol** | Facultatif | Énuméré | Protocole de transport utilisé par l’événement de résolution de réseau. La valeur peut être **UDP** ou **TCP**, et est couramment définie sur **UDP** pour DNS. <br><br>Exemple : `UDP`|
| **DnsQueryClass** | Facultatif | Integer | L’[ID de classe DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>En pratique, seule la classe **IN** (ID 1) est utilisée, réduisant ainsi la valeur de ce champ.|
| **DnsQueryClassName** | Facultatif | Chaîne | Le [nom de la classe DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>En pratique, seule la classe **IN** (ID 1) est utilisée, réduisant ainsi la valeur de ce champ. <br><br>Exemple : `IN`|
| <a name=flags></a>**DnsFlags** | Facultatif | Liste de chaînes | Le champ d’indicateurs, tel que fourni par l’appareil de création de rapports. Si les informations d’indicateur sont fournies dans plusieurs champs, concaténez-les avec une virgule comme séparateur. <br><br>Étant donné que les indicateurs DNS sont complexes à analyser et sont moins souvent utilisés par l’analytique, l’analyse et la normalisation ne sont pas nécessaires, et Microsoft Sentinel utilise une fonction auxiliaire pour fournir des informations d’indicateurs. Pour plus d’informations, consultez [Gestion des réponses DNS](#handling-dns-response). <br><br>Exemple : `["DR"]`|
| <a name=UrlCategory></a>**UrlCategory** |  Facultatif | Chaîne | Une source d’événement DNS peut également rechercher la catégorie des domaines demandés. Ce champ est appelé **_UrlCategory_** pour s’aligner sur le schéma de réseau de Microsoft Sentinel. <br><br>**_DomainCategory_** est ajouté en tant qu’alias qui s’adapte à DNS. <br><br>Exemple : `Educational \\ Phishing` |
| **DomainCategory** | Facultatif | Alias | Alias pour [UrlCategory](#UrlCategory). |
| **ThreatCategory** | Facultatif | Chaîne | Si une source d’événement DNS fournit également la sécurité DNS, elle peut également évaluer l’événement DNS. Par exemple, elle peut rechercher l’adresse IP ou le domaine dans une base de données de renseignement sur les menaces et peut affecter le domaine ou l’adresse IP avec une catégorie de menace. |
| **EventSeverity** | Facultatif | Chaîne | Si une source d’événement DNS fournit également la sécurité DNS, elle peut également évaluer l’événement DNS. Par exemple, elle peut rechercher l’adresse IP ou le domaine dans une base de données de renseignement sur les menaces, et affecter une gravité en fonction de l’évaluation. <br><br>Exemple : `Informational`|
| <a name="dnsnetworkduration"></a>**DnsNetworkDuration** | Facultatif | Integer | Durée, en millisecondes, pour que la requête DNS s’achève.<br><br>Exemple : `1500` |
| **Durée** | Alias | | Alias [DnsNetworkDuration](#dnsnetworkduration) |
| **DnsFlagsAuthenticated** | Facultatif | Booléen | L’indicateur DNS `AD`, qui est lié à DNSSEC, signale dans une réponse que toutes les données incluses dans les sections de réponse et d’autorité de la réponse ont été vérifiées par le serveur en fonction des stratégies de ce serveur. Pour plus d’informations, consultez la [RFC 3655 Section 6.1](https://tools.ietf.org/html/rfc3655#section-6.1).    |
| **DnsFlagsAuthoritative** | Facultatif | Booléen | L’indicateur DNS `AA` signale si la réponse du serveur faisait autorité    |
| **DnsFlagsCheckingDisabled** | Facultatif | Booléen | L’indicateur DNS `CD`, qui est lié à DNSSEC, signale dans une requête que les données non vérifiées sont acceptables pour le système qui envoie la requête. Pour plus d’informations, consultez la [RFC 3655 Section 6.1](https://tools.ietf.org/html/rfc3655#section-6.1).   |
| **DnsFlagsRecursionAvailable** | Facultatif | Booléen | L’indicateur DNS `RA` signale dans une réponse que ce serveur prend en charge les requêtes récursives.   |
| **DnsFlagsRecursionDesired** | Facultatif | Booléen | L’indicateur DNS `RD` signale dans une demande que ce client souhaite que le serveur utilise des requêtes récursives.   |
| **DnsFlagsTruncates** | Facultatif | Booléen | L’indicateur DNS `TC` signale qu’une réponse a été tronquée, car elle a dépassé la taille de réponse maximale.  |
| **DnsFlagsZ** | Facultatif | Booléen | L’indicateur DNS `Z` est déprécié, ce qui peut être signalé par des systèmes DNS plus anciens.  |
|<a name="dnssessionid"></a>**DnsSessionId** | Facultatif | string | Identificateur de session DNS signalé par le périphérique de création de rapport. <br><br>Exemple : `EB4BFA28-2EAD-4EF7-BC8A-51DF4FDF5B55` |
| **SessionId** | Alias | Chaîne | Alias [DnsSessionId](#dnssessionid) |
| | | | |

### <a name="deprecated-aliases"></a>Alias déconseillés

Les champs suivants sont des alias qui, bien que dépréciés, sont conservés à des fins de compatibilité descendante. Ils seront supprimés du schéma le 31 décembre 2021.

- Query (alias vers DnsQuery)
- QueryType (alias vers DnsQueryType)
- QueryTypeName (alias vers DnsQueryTypeName)
- ResponseName (alias vers DnsReasponseName)
- ResponseCodeName (alias vers DnsResponseCodeName)
- ResponseCode (alias vers DnsResponseCode)
- QueryClass (alias vers DnsQueryClass)
- QueryClassName (alias vers DnsQueryClassName)
- Flags (alias vers DnsFlags)

### <a name="schema-updates"></a>Mises à jour du schéma

Voici les modifications apportées à la version 0.1.2 du schéma :
- Ajout du champ `EventSchema` : actuellement facultatif, mais devient obligatoire le 1er janvier 2022.
- Ajout d’un champ dédié aux indicateurs qui vient s’ajouter au champ combiné **[Indicateurs](#flags)**  : `DnsFlagsAuthoritative`, `DnsFlagsCheckingDisabled`, `DnsFlagsRecursionAvailable`, `DnsFlagsRecursionDesired`, `DnsFlagsTruncates` et `DnsFlagsZ`.

Voici les modifications apportées à la version 0.1.3 du schéma :
- Le schéma documente désormais explicitement les champs `Src*`, `Dst*`, `Process*` et `User*`.
- Ajout de champs `Dvc*` supplémentaires pour correspondre à la définition la plus récente des champs communs. 
- Ajout de `Src` et `Dst` en tant qu’alias d’un identifiant principal pour les systèmes source et de destination.
- Ajout du champ facultatif `DnsNetworkDuration` et de `Duration`, son alias.
- Ajout des champs facultatifs Geo Location et Risk Level.

## <a name="handling-dns-response"></a>Gestion de la réponse DNS

Dans la plupart des cas, les événements DNS consignés n’incluent pas les informations de réponse, qui peuvent être volumineuses et détaillées. Si votre enregistrement inclut plus d’informations de réponse, stockez-les dans le champ [ResponseName](#responsename) comme elles apparaissent dans l’enregistrement.

Vous pouvez également fournir une fonction KQL supplémentaire appelée `_imDNS<vendor>Response_`, qui prend la réponse non analysée comme entrée et retourne une valeur dynamique avec la structure suivante :

```kql
[
    {
        "part": "answer"
        "query": "yahoo.com."
        "TTL": 1782
        "Class": "IN"
        "Type": "A"
        "Response": "74.6.231.21"
    }
    {
        "part": "authority"
        "query": "yahoo.com."
        "TTL": 113066
        "Class": "IN"
        "Type": "NS"
        "Response": "ns5.yahoo.com"
    }
    ...
]
```

Les champs de chaque dictionnaire dans la valeur dynamique correspondent aux champs de chaque réponse DNS. L’entrée `part` doit inclure `answer`, `authority` ou `additional` pour refléter la partie dans la réponse à laquelle le dictionnaire appartient.

> [!TIP]
> Pour garantir des performances optimales, appelez la fonction `imDNS<vendor>Response` uniquement lorsque cela est nécessaire, et uniquement après un filtrage initial pour garantir de meilleures performances.
>

## <a name="handling-dns-flags"></a>Gestion des indicateurs DNS

L’analyse et la normalisation ne sont pas requises pour les données d’indicateur. À la place, stockez les données d’indicateur fournies par l’appareil dans le champ [Indicateurs](#flags). Si la détermination de la valeur des indicateurs individuels est directe, vous pouvez également utiliser les champs d’indicateurs dédiés. 

Vous pouvez également fournir une fonction KQL supplémentaire appelée `_imDNS<vendor>Flags_`, qui prend la réponse non analysée, ou des champs d’indicateurs dédiés, comme entrée et retourne une liste dynamique, avec des valeurs booléennes qui représentent chaque indicateur dans l’ordre suivant :

- Authentifié (AD)
- Faisant autorité (AA)
- Vérification désactivée (CD)
- Récurrence disponible (RA)
- Récurrence souhaitée (RD)
- Tronqué (TC)
- Z

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Normalisation dans Microsoft Sentinel](normalization.md)
- [Informations de référence sur le schéma de normalisation de l’authentification Microsoft Sentinel (préversion publique)](authentication-normalization-schema.md)
- [Informations de référence de schéma de normalisation des données Microsoft Sentinel](normalization-schema.md)
- [Informations de référence du schéma de normalisation des événements de fichier de Microsoft Sentinel (préversion publique)](file-event-normalization-schema.md)
- [Informations de référence du schéma de normalisation des événements de processus de Microsoft Sentinel](process-events-normalization-schema.md)
- [Informations de référence de schéma de normalisation d’événement du registre Microsoft Sentinel (préversion publique)](registry-event-normalization-schema.md)