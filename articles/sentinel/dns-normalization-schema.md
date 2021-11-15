---
title: Informations de référence de schéma de normalisation DNS Azure Sentinel | Microsoft Docs
description: Cet article décrit le schéma de normalisation DNS Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/15/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: bf53669dc4c6ca445ea43535a709804d387e855d
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989581"
---
# <a name="azure-sentinel-dns-normalization-schema-reference-public-preview"></a>Informations de référence de schéma de normalisation DNS Azure Sentinel (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le modèle d’informations DNS est utilisé pour décrire les événements signalés par un serveur DNS ou un système de sécurité DNS, et il est utilisé par Azure Sentinel pour activer l’analytique indépendante de la source.

Pour plus d’informations, consultez [Normalisation et modèle ASIM (Azure Sentinel Information Model)](normalization.md).

> [!IMPORTANT]
> Le schéma de normalisation DNS est actuellement en PRÉVERSION. Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail de production.
>
> Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="guidelines-for-collecting-dns-events"></a>Instructions pour la collecte d’événements DNS

DNS est un protocole unique en ce qu’il peut croiser un grand nombre d’ordinateurs. En outre, étant donné que DNS utilise le protocole UDP, les requêtes et les réponses sont découplées et ne sont pas directement liées les unes aux autres.

L’illustration suivante montre un flux de requête DNS simplifié, y compris quatre segments. Une requête réelle peut être plus complexe, avec plus de segments impliqués.

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="Un flux de requête DNS simplifié.":::

Étant donné que les segments de requête et de réponse ne sont pas directement connectés les uns aux autres dans le flux de requête DNS, la journalisation complète peut entraîner une duplication importante.

Le segment le plus précieux à consigner est la réponse au client, qui fournit les requêtes de nom de domaine, le résultat de la recherche et l’adresse IP du client. Alors que de nombreux systèmes DNS consignent uniquement ce segment, il existe une valeur dans la journalisation des autres parties. Par exemple, une attaque par empoisonnement du cache DNS tire souvent parti des réponses factices d’un serveur en amont.

Si votre source de données prend en charge la journalisation DNS complète et que vous avez choisi de consigner plusieurs segments, vous devez ajuster vos requêtes pour empêcher la duplication des données dans Azure Sentinel.

Par exemple, vous pouvez modifier votre requête avec la normalisation suivante :

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>Analyseurs

### <a name="source-agnostic-parsers"></a>Analyseurs indépendants de la source

Pour utiliser les analyseurs indépendants de la source qui unifient tous les analyseurs intégrés, et vérifier que votre analyse s’exécute sur toutes les sources configurées, utilisez les fonctions KQL suivantes comme nom de table dans votre requête :

| Nom | Description | Instructions d’utilisation |
| --- | --- | --- |
| **imDNS** | Analyseur d’agrégation qui utilise *union* pour inclure des événements normalisés de toutes les sources DNS. |– Mettez à jour cet analyseur si vous souhaitez ajouter ou supprimer des sources de l’analytique indépendante de la source. <br><br>– Utilisez cette fonction dans vos requêtes indépendantes de la source.|
| **ASimDNS** | Similaire à la fonction `imDns`, mais sans prise en charge des paramètres et, par conséquent, ne force pas le sélecteur d’heure de page **Journaux** à utiliser la value `custom`. |– Mettez à jour cet analyseur si vous souhaitez ajouter ou supprimer des sources de l’analytique indépendante de la source.<br><br>- Utilisez cette fonction dans vos requêtes indépendantes de la source si vous n’envisagez pas d’utiliser de paramètres.|
| **vimDNS\<vendor\>\<product\>** | Les analyseurs spécifiques à la source implémentent la normalisation pour une source spécifique, telle que *imDNSWindowsOMS*. |– Ajoutez un analyseur spécifique à la source pour une source lorsqu’il n’existe aucun analyseur de normalisation intégré. Mettez à jour l’analyseur d’agrégation `im` pour inclure la référence à votre nouvel analyseur. <br><br>– Mettez à jour un analyseur spécifique à la source pour résoudre les problèmes d’analyse et de normalisation.<br><br>– Utilisez un analyseur spécifique à la source pour l’analytique spécifique à la source.|
| **ASimDNS\<vendor\>\<product\>** | Les analyseurs spécifiques à la source implémentent la normalisation pour une source spécifique. Contrairement aux fonctions `vim*`, les fonctions `ASimDNS*` ne prennent pas en charge les paramètres. |– Ajoutez un analyseur spécifique à la source pour une source lorsqu’il n’existe aucun analyseur de normalisation intégré. Mettez à jour l’analyseur d’agrégation `ASim` pour inclure la référence à votre nouvel analyseur.<br><br>– Mettez à jour un analyseur spécifique à la source pour résoudre les problèmes d’analyse et de normalisation.<br><br>- Utilisez un analyseur spécifique à la source `ASim` pour les requêtes interactives quand vous n’utilisez pas de paramètres.|
| | | |

Les analyseurs peuvent être déployés à partir du [référentiel GitHub Azure Sentinel](https://aka.ms/azsentinelDNS).

### <a name="built-in-source-specific-parsers"></a>Analyseurs intégrés spécifiques de la source

Azure Sentinel fournit les analyseurs DNS spécifiques aux produits et intégrés suivants :

  - **Serveur DNS Microsoft**, collecté avec l’agent Log Analytics - ASimDnsMicrosoftOMS (standard), vimDnsMicrosoftOMS (paramétré)
  - **Cisco Umbrella** - ASimDnsCiscoUmbrella (standard), vimDnsCiscoUmbrella (paramétré)
  - **Infoblox NIOS** - ASimDnsInfobloxNIOS (standard), vimDnsInfobloxNIOS (paramétré)
  - **GCP DNS** - ASimDnsGcp (standard), vimDnsGcp  (paramétré)
  - **Événements DNS Corelight Zeek** - ASimDnsCorelightZeek (standard), vimDnsCorelightZeek (paramétré)
  - **Sysmon pour Windows** (événement 22) collecté avec l’agent Log Analytics ou l’agent Azure Monitor, prenant en charge les tables Event et WindowsEvent, ASimDnsMicrosoftSysmon (standard), vimDnsMicrosoftSysmon (paramétré)

Les analyseurs peuvent être déployés à partir du [référentiel GitHub Azure Sentinel](https://aka.ms/azsentinelDNS).

### <a name="add-your-own-normalized-parsers"></a>Ajouter vos propres analyseurs normalisés

Quand vous implémentez des analyseurs personnalisés pour le modèle d’informations DNS, nommez vos fonctions KQL avec la syntaxe suivante : `vimDns<vendor><Product` pour les analyseurs paramétrés et `ASimDns<vendor><Product` pour les analyseurs standard.

### <a name="filtering-parser-parameters"></a>Paramètres de filtrage des analyseurs

Les analyseurs `im` et `vim*` prennent en charge les [paramètres de filtrage](normalization-about-parsers.md#optimized-parsers). Bien que ces analyseurs soient facultatifs, ils peuvent améliorer les performances de vos requêtes.

Les paramètres de filtrage suivants sont disponibles :

| Nom     | Type      | Description |
|----------|-----------|-------------|
| **starttime** | DATETIME | Filtrez uniquement les requêtes DNS qui ont été exécutées à ce moment-là ou après. |
| **endtime** | DATETIME | Filtrez uniquement les requêtes DNS dont l’exécution s’est terminée à ce moment-là ou avant. |
| **srcipaddr** | string | Filtrez uniquement les requêtes DNS à partir de cette adresse IP source. |
| **domain_has_any**| dynamique | Filtrez uniquement les requêtes DNS où `domain` (ou `query`) présente l’un des noms de domaine listés, notamment comme partie du domaine de l’événement.
| **responsecodename** | string | Filtrez uniquement les requêtes DNS pour lesquelles le nom du code de réponse correspond à la valeur fournie. Par exemple : NXDOMAIN |
| **response_has_ipv4** | string | Filtrez uniquement les requêtes DNS dans lesquelles le champ de réponse commence par l’adresse IP ou le préfixe d’adresse IP fourni. Utilisez ce paramètre lorsque vous souhaitez filtrer sur une seule adresse IP ou un seul préfixe. Les résultats ne sont pas retournés pour les sources qui ne fournissent pas de réponse.|
| **response_has_any_prefix** | dynamique| Filtrez uniquement les requêtes DNS dans lesquelles le champ de réponse commence par l’une des adresses IP ou l’un des préfixes d’adresse IP listés. Utilisez ce paramètre lorsque vous souhaitez filtrer sur une liste d’adresses IP ou de préfixes. Les résultats ne sont pas retournés pour les sources qui ne fournissent pas de réponse. |
| **eventtype**| string | Filtrez uniquement les requêtes DNS du type spécifié. Si aucune valeur n’est spécifiée, seules les requêtes de recherche sont retournées. |
||||

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

La prise en charge du schéma ASIM DNS comprend également celle des règles analytiques intégrées suivantes avec des analyseurs DNS normalisés. Outre au travers des liens vers le dépôt GitHub Azure Sentinel fournis ci-dessous comme référence, vous pouvez trouver ces règles dans la [galerie de règles Azure Sentinel Analytics](detect-threats-built-in.md). Utilisez les pages GitHub liées pour copier toutes les requêtes de chasse appropriées pour les règles listées.

Les règles d’analytique intégrées suivantes fonctionnent désormais avec les analyseurs DNS normalisés :
 - (Préversion) TI map Domain entity to Dns Event (DNS normalisé)
 - (Préversion) TI map IP entity to DnsEvents (DNS normalisé)
 - [DGA potentielle détecté (ASimDNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_HighNXDomainCount_detection.yaml)
 - [Trop de requêtes DNS NXDOMAIN (DNS normalisé)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [Événements DNS liés aux pools d’exploration (DNS normalisé)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [Événements DNS liés aux proxys ToR (DNS normalisé)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [Domaines Barium connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [Adresses IP Barium connues](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Des vulnérabilités Exchange Server ont divulgué la correspondance IoC de mars 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [Domaines et hachages GALLIUM connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [Known IRIDIUM IP (Adresse IP IRIDIUM connue)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM - Domaine et IOC IP - Mars 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [Domaines/adresses IP de groupe Phosphorus connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [Domaines de groupe STRONTIUM connus – Juillet 2019](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Balise réseau Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [Domaines THALLIUM inclus dans DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [Codes de hachage de programme malveillant Comebacker et Klackring de ZINC connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)


## <a name="schema-details"></a>Détails du schéma

Le modèle d’informations DNS est aligné sur le [schéma d’entité DNS OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md).

Pour plus d’informations, consultez les [informations de référence sur les paramètres système du nom de domaine IANA (Internet Assigned Numbers Authority)](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).

### <a name="log-analytics-fields"></a>Champs Log Analytics

Les champs suivants sont générés par Log Analytics pour chaque enregistrement et vous pouvez les remplacer quand [vous créez un connecteur personnalisé](create-custom-connector.md).

| **Champ** | **Type** | **Description** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | Date/heure | Heure à laquelle l’événement a été généré par l’appareil de création de rapports. |
| **\_ResourceId** | guid | ID de ressource Azure de l’appareil ou du service de création de rapports, ou l’ID de ressource de redirecteur de journal pour les événements transférés avec Syslog, CEF ou WEF. |
| **Type** | String | Table d’origine à partir de laquelle l’enregistrement a été extrait. Ce champ est utile lorsque le même événement peut être reçu via plusieurs canaux vers différentes tables, et ont les mêmes valeurs EventVendor et EventProduct.<br><br>Par exemple, un événement Sysmon peut être collecté dans la table Event ou dans la table WindowsEvent. |
| | | |

> [!NOTE]
> D’autres champs de Log Analytics, moins relatifs à la sécurité, sont documentés avec [Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
> 

### <a name="event-fields"></a>Champs de l’événement

Les champs d’événement sont communs à tous les schémas et décrivent l’activité elle-même et l’appareil de création de rapports.

| **Champ** | **Classe** | **Type**  | **Discussion** |
| --- | --- | --- | --- |
| **EventMessage** | Facultatif | Chaîne | Message général ou description, inclus dans l’enregistrement ou généré depuis l’enregistrement. |
| **EventCount** | Obligatoire | Integer | Nombre d’événements décrits par l’enregistrement. <br><br>Cette valeur est utilisée lorsque la source prend en charge l’agrégation, et un seul enregistrement peut représenter plusieurs événements. <br><br>Pour les autres sources, la valeur doit être **1**. <br><br>Exemple :`1`|
| **EventStartTime** | Obligatoire | Date/heure | Si la source prend en charge l’agrégation et que l’enregistrement représente plusieurs événements, utilisez ce champ pour spécifier l’heure à laquelle le premier événement a été généré. <br><br>Dans d’autres cas, In other cases, mettez en alias le champ [TimeGenerated](#timegenerated). |
| **EventEndTime** | Alias || Alias du champ [TimeGenerated](#timegenerated). |
| **EventType** | Obligatoire | Énuméré | Indique l’opération signalée par l’enregistrement. <br><Br> Pour les enregistrements DNS, cette valeur serait le [code d’opération DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Exemple : `lookup`|
| **EventSubType** | Facultatif | Énuméré | **requête** ou **réponse**. Pour la plupart des sources, [seules les réponses sont consignées](#guidelines-for-collecting-dns-events) et par conséquent, la valeur est souvent **response**.  |
| **EventResult** | Obligatoire | Énuméré | L’une des valeurs suivantes : **Succès**, **Partiel**, **Échec**, **NA** (Non applicable).<br> <br>La valeur peut être fournie dans l’enregistrement source à l’aide de termes différents, qui doivent être normalisés avec ces valeurs. Sinon, la source peut fournir uniquement le champ [EventResultDetails](#eventresultdetails) qui devrait être analysé pour dériver la valeur EventResult.<br> <br>Si cet enregistrement représente une requête et non une réponse, affectez la valeur **NA**. <br><br>Exemple : `Success`|
| <a name=eventresultdetails></a>**EventResultDetails** | Alias | | Raison ou détails du résultat rapporté dans le champ **_EventResult_**. Met en alias le champ [ResponseCodeName](#responsecodename).|
| **EventOriginalUid** | Facultatif | Chaîne | ID unique de l’enregistrement d’origine, s’il est fourni par la source. |
| **EventOriginalType**   | Facultatif    | Chaîne  |  Type ou ID d’événement d’origine, s’il est fourni par la source.<br><br>Exemple : `lookup` |
| <a name ="eventproduct"></a>**EventProduct** | Obligatoire | Chaîne | Produit générant l’événement. Ce champ peut ne pas être disponible dans l’enregistrement source, auquel cas il doit être défini par l’analyseur. <br><br>Exemple : `DNS Server` |
| **EventProductVersion** | Facultatif | Chaîne | Version du produit générant l’événement. Ce champ peut ne pas être disponible dans l’enregistrement source, auquel cas il doit être défini par l’analyseur. <br><br>Exemple : `12.1` |
| **EventVendor** | Obligatoire | Chaîne | Fournisseur du produit générant l’événement. Ce champ peut ne pas être disponible dans l’enregistrement source, auquel cas il doit être défini par l’analyseur.<br><br>Exemple : `Microsoft`|
| **EventSchemaVersion** | Obligatoire | Chaîne | La version du schéma documenté ici est **0.1.2**. |
| **EventSchema** | Obligatoire | Chaîne | Le nom du schéma documenté ici est **Dns**. |
| **EventReportUrl** | Facultatif | Chaîne | URL fournie dans l’événement pour une ressource qui apporte plus d’informations sur l’événement. |
| <a name="dvc"></a>**Dvc** | Obligatoire       | Chaîne     |    Identificateur unique de l’appareil sur lequel l’événement s’est produit. <br><br>Ce champ peut prendre l’alias des champs [DvcId](#dvcid), [DvcHostname](#dvchostname) ou [DvcIpAddr](#dvcipaddr). Pour les sources cloud pour lesquelles il n’y a pas d’appareil apparent, utilisez la même valeur que celle du champ [Event Product](#eventproduct). <br><br>Exemple : `ContosoDc.Contoso.Azure`       |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recommandé | Adresse IP |  Adresse IP de l’appareil sur lequel l’événement de processus s’est produit. <br><br>Exemple : `45.21.42.12` |
| <a name ="dvchostname"></a>**DvcHostname**         | Recommandé | Nom d’hôte   | Nom d’hôte de l’appareil sur lequel l’événement de processus s’est produit. <br><br>Exemple : `ContosoDc.Contoso.Azure`                |
| <a name ="dvcid"></a>**DvcId**               | Facultatif    | Chaîne     | ID unique de l’appareil sur lequel l’événement de processus s’est produit. <br><br>Exemple : `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| <a name=additionalfields></a>**AdditionalFields** | Facultatif | Dynamique | Si votre source fournit d’autres informations à conserver, conservez-les avec les noms de champs d’origine ou créez le champ dynamique **AdditionalFields**, puis ajoutez-y les informations supplémentaires sous forme de paires clé/valeur. |
| | | | |

### <a name="dns-specific-fields"></a>Champs spécifiques à DNS

Les champs ci-dessous sont spécifiques aux événements DNS. Cela dit, beaucoup d’entre eux ont des similitudes dans d’autres schémas et par conséquent, suivent la même convention d’affectation de noms.

| **Champ** | **Classe** | **Type** | **Remarques** |
| --- | --- | --- | --- |
| **SrcIpAddr** | Recommandé | Adresse IP | L’adresse IP du client qui envoie la requête DNS. Pour une requête DNS récursive, cette valeur correspond généralement à l’appareil de création de rapports et, dans la plupart des cas, à `127.0.0.1`.<br><br>Exemple : `192.168.12.1` |
| **SrcPortNumber** | Facultatif | Integer | Port source de la requête DNS.<br><br>Exemple : `54312` |
| **DstIpAddr** | Facultatif | Adresse IP | Adresse IP du serveur qui reçoit la requête DNS. Pour une requête DNS régulière, cette valeur correspond généralement à l’appareil de création de rapports et, dans la plupart des cas, à `127.0.0.1`.<br><br>Exemple : `127.0.0.1` |
| **DstPortNumber** | Facultatif | Integer  | Numéro du port de destination.<br><br>Exemple : `53` |
| **IpAddr** | Alias | | Alias pour SrcIpAddr |
| <a name=query></a>**DnsQuery** | Obligatoire | FQDN | Le domaine qui doit être résolu. <br><br>**Remarque** : Certaines sources envoient cette requête dans des formats différents. Par exemple, dans le protocole DNS lui-même, la requête comprend un point ( **.** ) à la fin, qui doit être supprimé.<br><br>Bien que le protocole DNS autorise plusieurs requêtes dans une seule requête, ce scénario est rare, voire même inexistant. Si la requête comporte plusieurs requêtes, stockez la première dans ce champ, puis conservez éventuellement le reste dans le champ [AdditionalFields](#additionalfields).<br><br>Exemple : `www.malicious.com` |
| **Domaine** | Alias | | Prendre l’alias [Requête](#query). |
| **DnsQueryType** | Facultatif | Integer | Ce champ peut contenir des [codes de type d’enregistrement de ressource DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Exemple : `28`|
| **DnsQueryTypeName** | Recommandé | Énuméré | Ce champ peut contenir des noms de [type d’enregistrement de ressource DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Remarque** : IANA ne définit pas la casse des valeurs, donc Analytics doit normaliser la casse en fonction des besoins. Si la source fournit uniquement un code de type de requête numérique et non un nom de type de requête, l’analyseur doit inclure une table de recherche pour enrichir cette valeur.<br><br>Exemple : `AAAA`|
| <a name=responsename></a>**DnsResponseName** | Facultatif | Chaîne | Contenu de la réponse, tel qu’il est inclus dans l’enregistrement.<br> <br> Les données de réponse DNS sont incohérentes entre les appareils de création de rapports, sont complexes à analyser et ont moins de valeur pour l’analyse indépendante de la source. Par conséquent, le modèle d’information ne nécessite pas d’analyse ni de normalisation, et Azure Sentinel utilise une fonction auxiliaire pour fournir des informations de réponse. Pour plus d’informations, consultez [Gestion des réponses DNS](#handling-dns-response).|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Obligatoire | Énuméré | Le [code de réponse DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Remarque** : IANA ne définit pas la casse des valeurs, donc l’analyse doit normaliser la casse. Si la source fournit uniquement un code de réponse et non un nom de code de réponse, l’analyseur doit inclure une table de recherche pour enrichir cette valeur. <br><br> Si cet enregistrement représente une requête et non une réponse, affectez la valeur **NA**. <br><br>Exemple : `NXDOMAIN` |
| **DnsResponseCode** | Facultatif | Integer | Le [code de réponse numérique DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Exemple : `3`|
| **TransactionIdHex** | Recommandé | Chaîne | ID de transaction hexadécimal unique DNS. |
| **NetworkProtocol** | Facultatif | Énuméré | Protocole de transport utilisé par l’événement de résolution de réseau. La valeur peut être **UDP** ou **TCP**, et est couramment définie sur **UDP** pour DNS. <br><br>Exemple : `UDP`|
| **DnsQueryClass** | Facultatif | Integer | L’[ID de classe DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>En pratique, seule la classe **IN** (ID 1) est utilisée, réduisant ainsi la valeur de ce champ.|
| **DnsQueryClassName** | Facultatif | Chaîne | Le [nom de la classe DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>En pratique, seule la classe **IN** (ID 1) est utilisée, réduisant ainsi la valeur de ce champ. <br><br>Exemple : `IN`|
| <a name=flags></a>**DnsFlags** | Facultatif | Liste de chaînes | Le champ d’indicateurs, tel que fourni par l’appareil de création de rapports. Si les informations d’indicateur sont fournies dans plusieurs champs, concaténez-les avec une virgule comme séparateur. <br><br>Étant donné que les indicateurs DNS sont complexes à analyser et sont moins souvent utilisés par l’analyse, l’analyse et la normalisation ne sont pas nécessaires, et Azure Sentinel utilise une fonction auxiliaire pour fournir des informations d’indicateurs. Pour plus d’informations, consultez [Gestion des réponses DNS](#handling-dns-response). <br><br>Exemple : `["DR"]`|
| <a name=UrlCategory></a>**UrlCategory** |  Facultatif | Chaîne | Une source d’événement DNS peut également rechercher la catégorie des domaines demandés. Ce champ est appelé **_UrlCategory_** pour s’aligner avec le schéma de réseau Azure Sentinel. <br><br>**_DomainCategory_** est ajouté en tant qu’alias qui s’adapte à DNS. <br><br>Exemple : `Educational \\ Phishing` |
| **DomainCategory** | Facultatif | Alias | Alias pour [UrlCategory](#UrlCategory). |
| **ThreatCategory** | Facultatif | Chaîne | Si une source d’événement DNS fournit également la sécurité DNS, elle peut également évaluer l’événement DNS. Par exemple, elle peut rechercher l’adresse IP ou le domaine dans une base de données de renseignement sur les menaces et peut affecter le domaine ou l’adresse IP avec une catégorie de menace. |
| **EventSeverity** | Facultatif | Chaîne | Si une source d’événement DNS fournit également la sécurité DNS, elle peut également évaluer l’événement DNS. Par exemple, elle peut rechercher l’adresse IP ou le domaine dans une base de données de renseignement sur les menaces, et affecter une gravité en fonction de l’évaluation. <br><br>Exemple : `Informational`|
| **DvcAction** | Facultatif | Chaîne | Si une source d’événement DNS fournit également la sécurité DNS, elle peut entreprendre une action sur la requête, par exemple la bloquer. <br><br>Exemple : `Blocked` |
| **DnsFlagsAuthenticated** | Facultatif | Booléen | L’indicateur DNS `AD`, qui est lié à DNSSEC, signale dans une réponse que toutes les données incluses dans les sections de réponse et d’autorité de la réponse ont été vérifiées par le serveur en fonction des stratégies de ce serveur. Pour plus d’informations, consultez la [RFC 3655 Section 6.1](https://tools.ietf.org/html/rfc3655#section-6.1).    |
| **DnsFlagsAuthoritative** | Facultatif | Booléen | L’indicateur DNS `AA` signale si la réponse du serveur faisait autorité    |
| **DnsFlagsCheckingDisabled** | Facultatif | Booléen | L’indicateur DNS `CD`, qui est lié à DNSSEC, signale dans une requête que les données non vérifiées sont acceptables pour le système qui envoie la requête. Pour plus d’informations, consultez la [RFC 3655 Section 6.1](https://tools.ietf.org/html/rfc3655#section-6.1).   |
| **DnsFlagsRecursionAvailable** | Facultatif | Booléen | L’indicateur DNS `RA` signale dans une réponse que ce serveur prend en charge les requêtes récursives.   |
| **DnsFlagsRecursionDesired** | Facultatif | Booléen | L’indicateur DNS `RD` signale dans une demande que ce client souhaite que le serveur utilise des requêtes récursives.   |
| **DnsFlagsTruncates** | Facultatif | Booléen | L’indicateur DNS `TC` signale qu’une réponse a été tronquée, car elle a dépassé la taille de réponse maximale.  |
| **DnsFlagsZ** | Facultatif | Booléen | L’indicateur DNS `Z` est déprécié, ce qui peut être signalé par des systèmes DNS plus anciens.  |
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

### <a name="added-fields"></a>Champs ajoutés

Les champs suivants ont été ajoutés à la version 0.1.2 du schéma :
- EventSchema : actuellement facultatif, mais devient obligatoire le 1er janvier 2022.
- Champ d’indicateur dédié qui augmente le champ des **[indicateurs](#flags)** combinés : `DnsFlagsAuthoritative`, `DnsFlagsCheckingDisabled`, `DnsFlagsRecursionAvailable`, `DnsFlagsRecursionDesired`, `DnsFlagsTruncates` et `DnsFlagsZ`.

### <a name="additional-entities"></a>Entités supplémentaires

Les événements évoluent autour des entités, telles que les utilisateurs, les hôtes, les processus ou les fichiers, et chaque entité peut nécessiter plusieurs champs pour la décrire. Par exemple :

- Un hôte peut avoir un nom et une adresse IP
- Un seul enregistrement peut inclure plusieurs entités du même type, comme un hôte source et un hôte de destination.

Le schéma DNS décrit dans cet article comprend des champs qui décrivent les entités. Si votre source comprend d’autres informations pour décrire les entités, ajoutez d’autres champs basés sur les entités listées dans le tableau suivant pour capturer ces informations.

Pour plus d’informations, consultez [Normalisations dans Azure Sentinel](normalization.md).


| **Entité** | **Fields** | **Type** | **Champs obligatoires** | **Remarques** |
| --- | --- | --- | --- | --- |
| **Actor** | Acteur\* | Utilisateur |  | La plupart des sources d’événements DNS ne fournissent pas d’informations sur l’utilisateur, ce qui n’est généralement pas une partie du protocole DNS. <br><br>Dans certains cas, l’appareil de création de rapports fournit des informations sur l’utilisateur, généralement en résolvant l’adresse IP source en informations utilisateur. Dans ce cas, représentez l’utilisateur comme décrit dans la documentation relative aux entités de schéma. Utilisez **Actor** comme descripteur, dans la mesure où les informations sont basées sur l’adresse IP source. <br><br>**Remarque** : Lorsque qu’**Actor** est utilisé en tant que descripteur d’entité, il n’est pas nécessaire d’ajouter le champ **Utilisateur**. |
| **Appareil source** | Src\* | Appareil | `SrcIpAddr` | Les sources d’événement DNS signalent en général l’adresse IP de la source de la requête, et par conséquent **SrcIpAddr** est obligatoire. <br><br>Si l’appareil de création de rapports fournit plus d’informations sur la source de la requête, ou si vous enrichissez les données, utilisez les instructions d’entité d’appareil pour normaliser à l’aide de **Src** comme préfixe du champ. |
| **Appareil de destination** | Dst\* | Appareil |  | En règle générale, les sources d’événements DNS ne signalent pas d’informations sur la destination de la requête. Si l’appareil de création de rapports fournit des informations sur la destination de la requête, ou si vous enrichissez les données, utilisez les instructions d’entité d’appareil pour normaliser à l’aide de **Dst** comme préfixe. |
| **Appareil de création de rapport** | Dvc\* | Appareil | `Dvc` | La plupart des événements incluent des informations sur l’appareil de création de rapports. Utiliser le préfixe Dvc pour ces champs|
| **Informations sur le processus** | Processus\* | Processus |  | Informations relatives au processus qui a généré la requête DNS sur l’appareil client. |
| | | | | |

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

- [Normalisation dans Azure Sentinel](normalization.md)
- [Informations de référence sur le schéma de normalisation de l’authentification Azure Sentinel (préversion publique)](authentication-normalization-schema.md)
- [Référence de schéma de normalisation des données Azure Sentinel](./network-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation des événements de fichier Azure Sentinel (préversion publique)](file-event-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation des événements de processus Azure Sentinel](process-events-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation des événements du registre Azure Sentinel (préversion publique)](registry-event-normalization-schema.md)
