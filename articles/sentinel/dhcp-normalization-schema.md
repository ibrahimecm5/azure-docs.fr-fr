---
title: Informations de référence du schéma de normalisation DHCP Microsoft Sentinel | Microsoft Docs
description: Cet article décrit le schéma de normalisation DHCP Microsoft Sentinel.
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
ms.openlocfilehash: f0287e6f116e8d63c0b20aa970974e48a31524c2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521450"
---
# <a name="microsoft-sentinel-dhcp-normalization-schema-reference-public-preview"></a>Informations de référence du schéma de normalisation DHCP Microsoft Sentinel (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le modèle CIM DHCP sert à décrire les événements signalés par un serveur DHCP. Microsoft Sentinel l’utilise pour permettre une analyse indépendante de la source.

Pour plus d’informations, consultez [Normalisation et modèle d’informations SIEM avancé (ASIM)](normalization.md).

> [!IMPORTANT]
> Le schéma de normalisation DHCP est actuellement en PRÉVERSION. Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail de production.
>
> Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="schema-overview"></a>Vue d’ensemble du schéma

Le schéma DHCP ASIM représente l’activité du serveur DHCP, incluant le service des demandes pour l’adresse IP DHCP louée à des systèmes clients et la mise à jour d’un serveur DNS avec les baux accordés.

Les principaux champs dans un événement DHCP sont les champs [SrcIpAddr](#srcipaddr) et [SrcHostname](#srchostname) que le serveur DHCP lie en accordant le bail, qui ont respectivement pour alias les champs [IpAddr](#ipaddr) et [Hostname](#hostname). Le champ [SrcMacAddr](#srcmacaddr) est également important, car il représente l’ordinateur client utilisé quand une adresse IP n’est pas allouée.  

Un serveur DHCP peut rejeter un client, soit en raison de problèmes de sécurité, soit en raison d’une saturation du réseau. Il peut également mettre en quarantaine un client en lui allouant une adresse IP qui le connecte à un réseau limité. Les champs [EventResult](#eventresult), [EventResultDetails](#eventresultdetails) et [DvcAction](#dvcaction) fournissent des informations sur l’action et la réponse du serveur DHCP.

La durée d’un bail est stockée dans le champ [DhcpLeaseDuration](#dhcpleaseduration).

## <a name="schema-details"></a>Détails du schéma

ASIM est aligné avec le projet de [métadonnées des événements de sécurité open source (Open Source Security Events Metadata, OSSEM)](https://github.com/OTRF/OSSEM).

OSSEM n’a pas de schéma DHCP comparable au schéma DHCP ASIM.

### <a name="log-analytics-fields"></a>Champs Log Analytics

Les champs suivants sont générés par Log Analytics pour chaque enregistrement et vous pouvez les remplacer quand [vous créez un connecteur personnalisé](create-custom-connector.md).

| **Champ** | **Type** | **Description** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | Date/heure | Heure à laquelle l’événement a été généré par l’appareil de création de rapports. |
| **\_ResourceId** | guid | ID de ressource Azure de l’appareil ou du service de création de rapports, ou l’ID de ressource de redirecteur de journal pour les événements transférés avec Syslog, CEF ou WEF. |
| **Type** | String | Table d’origine à partir de laquelle l’enregistrement a été extrait. Ce champ est utile lorsque le même événement peut être reçu via plusieurs canaux dans différentes tables, avec les mêmes valeurs [EventVendor](#eventvendor) et [EventProduct](#eventproduct).<br><br>Par exemple, un événement Sysmon peut être collecté dans la table Event ou dans la table WindowsEvent. |
| | | |

> [!NOTE]
> D’autres champs de Log Analytics, moins relatifs à la sécurité, sont documentés avec [Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
> 

### <a name="event-fields"></a>Champs de l’événement

Les champs d’événement sont communs à tous les schémas et décrivent l’activité elle-même et l’appareil de création de rapports.

| **Champ** | **Classe** | **Type**  | **Discussion** |
| --- | --- | --- | --- |
| **EventMessage** | Facultatif | Chaîne | Message général ou description, inclus dans l’enregistrement ou généré depuis l’enregistrement. |
| **EventCount** | Obligatoire | Integer | Nombre d’événements décrits par l’enregistrement.<br><br>Cette valeur est utilisée lorsque la source prend en charge l’agrégation, et un seul enregistrement peut représenter plusieurs événements.<br><br>Pour les autres sources, la valeur doit être `1`.<br><br>Exemple : `1`|
| **EventStartTime** | Obligatoire | Date/heure | Si la source prend en charge l’agrégation et que l’enregistrement représente plusieurs événements, utilisez ce champ pour spécifier l’heure à laquelle le premier événement a été généré. <br><br>Dans d’autres cas, In other cases, mettez en alias le champ [TimeGenerated](#timegenerated). |
| **EventEndTime** | Alias || Alias du champ [TimeGenerated](#timegenerated). |
| **EventType** | Obligatoire | Énuméré | Indique l’opération signalée par l’enregistrement. <br><Br> Les valeurs possibles sont `Assign`, `Renew`, `Release` et `DNS Update`. <br><br>Exemple : `Assign`| 
| <a name="eventresult"></a>**EventResult** | Obligatoire | Énuméré | L’une des valeurs suivantes : `Success`, `Partial`, `Failure`, `NA` (Non applicable).<br> <br>La valeur peut être fournie dans l’enregistrement source à l’aide de termes différents, qui doivent être normalisés avec ces valeurs. Sinon, la source peut fournir uniquement le champ [EventResultDetails](#eventresultdetails) qui devrait être analysé pour dériver la valeur **EventResult**. Quand un client est mis en quarantaine par le serveur DHCP, ce champ doit avoir la valeur `Partial`.<br><br>Exemple : `Success`|
| <a name="eventresultdetails"></a>**EventResultDetails** | Alias | | Raison ou détails du résultat rapporté dans le champ EventResult](#eventresult). <br><Br> Les valeurs possibles sont `Exhausted`, `Quarantined` et `Denied`. <br><br>Exemple : `Exhausted` |
| **EventOriginalResultDetails**    | Facultatif    | Chaîne     |  Valeur fournie dans l’enregistrement d’origine pour [EventResultDetails](#eventresultdetails), si elle est fournie par la source. Pour les journaux du serveur DHCP Windows, stockez la valeur du champ QResult ici. |
| **EventOriginalUid** | Facultatif | Chaîne | ID unique de l’enregistrement d’origine, s’il est fourni par la source. |
| **EventOriginalType**   | Facultatif    | Chaîne  |  Type ou ID d’événement d’origine, s’il est fourni par la source.<br><br>Exemple : `DNS Assign Failed` |
| <a name ="eventproduct"></a>**EventProduct** | Obligatoire | Chaîne | Produit générant l’événement. Ce champ peut ne pas être disponible dans l’enregistrement source, auquel cas il doit être défini par l’analyseur. <br><br>Exemple : `DHCP Server` |
| **EventProductVersion** | Facultatif | Chaîne | Version du produit générant l’événement. Ce champ peut ne pas être disponible dans l’enregistrement source, auquel cas il doit être défini par l’analyseur. <br><br>Exemple : `12.1` |
| <a name="eventvendor"></a>**EventVendor** | Obligatoire | Chaîne | Fournisseur du produit générant l’événement. Ce champ peut ne pas être disponible dans l’enregistrement source, auquel cas il doit être défini par l’analyseur.<br><br>Exemple : `Microsoft`|
| **EventSchemaVersion** | Obligatoire | Chaîne | La version du schéma documenté ici est **0.1.0**. |
| **EventSchema** | Obligatoire | Chaîne | Le nom du schéma documenté ici est **Dhcp**. |
| **EventReportUrl** | Facultatif | Chaîne | URL fournie dans l’événement pour une ressource qui apporte plus d’informations sur l’événement. |
| <a name="dvc"></a>**Dvc** | Alias | Chaîne | Identificateur unique du serveur DHCP.<br><br>Exemple : `ContosoDc.Contoso.Azure`<br><br>Ce champ peut prendre l’alias des champs [DvcFQDN](#dvcfqdn), [Dvcld](#dvcid), [DvcHostname](#dvchostname) ou [DvclpAddr](#dvcipaddr). Pour les sources cloud pour lesquelles il n’y a pas d’appareil apparent, utilisez la même valeur que celle du champ [Event Product](#eventproduct). |
| <a name="dvcipaddr"></a>**DvcIpAddr** | Recommandé | Adresse IP | Adresse IP du serveur DHCP.<br><br>Exemple : `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>**DvcHostname** | Obligatoire | Chaîne | Nom d’hôte du serveur DHCP, sans informations de domaine. Si aucun nom de périphérique n’est disponible, stockez l’adresse IP pertinente dans ce champ.<br><br>Exemple : `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | Recommandé | Chaîne | Domaine du serveur DHCP.<br><br>Exemple : `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | Recommandé | Énuméré | Type de  [DvcDomain](#dvcdomain), s’il est connu. Les valeurs possibles incluent :<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>**Remarque**: ce champ est obligatoire si le champ [DvcDomain](#dvcdomain) est utilisé. |
| <a name="dvcfqdn"></a>**DvcFQDN** | Facultatif | String | Nom d’hôte du serveur DHCP, avec informations de domaine éventuellement disponibles. <br><br> Exemple : `Contoso\DESKTOP-1282V4D`<br><br>**Remarque** : ce champ prend en charge à la fois le format FQDN traditionnel et le format Windows domain\hostname. Le champ [DvcDomainType](#dvcdomaintype) reflète le format utilisé.  |
| <a name="dvcid"></a>**DvcId** | Facultatif | String | ID du serveur DHCP, tel qu’indiqué dans l’enregistrement.<br><br>Exemple : `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | Facultatif | Énuméré | Type de [Dvcld](#dvcid), s’il est connu. Les valeurs possibles incluent :<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si plusieurs ID sont disponibles, utilisez le premier de la liste et stockez les autres à l’aide des noms de champs  **DvcAzureResourceId** et **DvcMDEid** , respectivement.<br><br>**Remarque**: ce champ est obligatoire si le champ [Dvcld](#dvcid) est utilisé. |
| **EventSeverity** | Facultatif | Énuméré | Définissez la valeur sur `Low` si le serveur DHCP a mis en quarantaine le client, et sur `Medium` si le serveur a bloqué le client. Sinon, a la valeur `Informational`. <br><br>Exemple : `Informational`|
| <a name="dvcaction"></a>**DvcAction** | Facultatif | Énuméré | Action entreprise par le serveur DHCP. Les valeurs possibles sont `Allow`, `Deny` et `Quarantine`.<br><br>Exemple : `Deny` |
| <a name="additionalfields"></a>**AdditionalFields** | Facultatif | Dynamique | Si votre source fournit d’autres informations à conserver, conservez-les avec les noms de champs d’origine ou créez le champ dynamique **AdditionalFields**, puis ajoutez-y les informations supplémentaires sous forme de paires clé/valeur. |
| | | | |

### <a name="dhcp-specific-fields"></a>Champs spécifiques de DHCP

Les champs ci-dessous sont spécifiques des événements DHCP, mais sont similaires aux champs d’autres schémas et suivent la même conventions d’affectation de noms.

| **Champ** | **Classe** | **Type** | **Remarques** |
| --- | --- | --- | --- |
| <a name="srcipaddr"></a>**SrcIpAddr** | Obligatoire | Adresse IP | Adresse IP attribuée au client par le serveur DHCP.<br><br>Exemple : `192.168.12.1` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | Alias pour [SrcIpAddr](#srcipaddr) |
| <a name="requestedipaddr"></a>**RequestedIpAddr** | Facultatif | Adresse IP | Adresse IP demandée par le client DHCP, si disponible.<br><br>Exemple : `192.168.12.3` |
| <a name="srchostname"></a>**SrcHostname** | Obligatoire | Chaîne | Nom d’hôte de l’appareil demandant le bail DHCP. Si aucun nom de périphérique n’est disponible, stockez l’adresse IP pertinente dans ce champ.<br><br>Exemple : `DESKTOP-1282V4D` |
| <a name="hostname"></a>**Hostname** | Alias | | Alias pour [SrcHostname](#srchostname) |
| <a name="srcdomain"></a> **SrcDomain** | Recommandé | Chaîne | Domaine de l’appareil source.<br><br>Exemple : `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Recommandé | Énuméré | Type de [SrcDomain](#srcdomain), s’il est connu. Les valeurs possibles incluent :<br>- `Windows`(par exemple :`contoso`)<br>- `FQDN`(par exemple :`microsoft.com`)<br><br>Obligatoire si [SrcDomain](#srcdomain) est utilisé. |
| **SrcFQDN** | Facultatif | String | Nom d’hôte de l’appareil source, y compris les informations de domaine, le cas échéant. <br><br>**Remarque** : ce champ prend en charge à la fois le format FQDN traditionnel et le format Windows domain\hostname. Le champ [SrcDomainType](#srcdomaintype) reflète le format utilisé. <br><br>Exemple : `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Facultatif | Chaîne | ID de l’appareil source comme indiqué dans l’enregistrement.<br><br>Par exemple : `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Facultatif | Énuméré | Type de [SrcDvcId](#srcdvcid), s’il est connu. Les valeurs possibles incluent :<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si plusieurs ID sont disponibles, utilisez le premier de la liste ci-dessus, et stockez les autres à l’aide des noms de champ **SrcDvcAzureResourceId** et **SrcDvcMDEid**, respectivement.<br><br>**Remarque**: ce champ est obligatoire si le champ [SrcDvcld](#srcdvcid) est utilisé. |
| **SrcDeviceType** | Facultatif | Énuméré | Type de l’appareil source. Les valeurs possibles incluent :<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | Facultatif | String | Représentation unique, alphanumérique et lisible par l’ordinateur de l’utilisateur source. Les formats et types pris en charge sont :<br>- **SID** (Windows) : `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux) : `4578`<br>- **AADID** (Azure Active Directory) : `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>- **OktaId** : `00urjk4znu3BcncfY0h7`<br>- **AWSId** : `72643944673`<br><br>Stockez le type ID dans le champ [SrcUserIdType](#srcuseridtype). Si d’autres ID sont disponibles, nous vous recommandons de normaliser les noms de champs respectivement en SrcUserSid, SrcUserUid, SrcUserAadId, SrcUserOktaId et UserAwsId.<br><br>Exemple : `S-1-12` |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Facultatif | Énuméré | Type de l’ID stocké dans le champ [SrcUserId](#srcuserid). Les valeurs prises en charge incluent : `SID`, `UIS`, `AADID`, `OktaId` et `AWSId`. |
| <a name="srcusername"></a>**SrcUsername** | Facultatif | String | Nom de l’utilisateur source, y compris les informations de domaine, le cas échéant. Utilisez l'un des formats suivants et dans l'ordre de priorité suivant :<br>- **Upn/E-mail** : `johndow@contoso.com`<br>- **Windows** : `Contoso\johndow`<br>- **DN** : `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple** : `johndow`. Utilisez le formulaire simple uniquement si les informations de domaine ne sont pas disponibles.<br><br>Stockez le type Nom d’utilisateur dans le champ [SrcUsernameType](#srcusernametype). Si d’autres ID sont disponibles, nous vous conseillons de normaliser les noms des champs pour **SrcUserUpn**, **SrcUserWindows** et **SrcUserDn**.<br><br>Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : `AlbertE` |
| **Nom d’utilisateur** | Alias | | Alias pour [SrcUsername](#srcusername). |
| <a name="srcusernametype"></a>**SrcUsernameType** | Facultatif | Énuméré | Spécifie le type du nom d’utilisateur stocké dans le champ [SrcUsername](#srcusername). Les valeurs prises en charge sont `UPN`, `Windows`, `DN` et `Simple`. Pour plus d’informations, consultez [L’entité utilisateur](normalization-about-schemas.md#the-user-entity).<br><br>Exemple : `Windows` |
| **SrcUserType** | Facultatif | Énuméré | Type d’intervenant. Les valeurs autorisées sont les suivantes :<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Remarque** : La valeur peut être fournie dans l’enregistrement source avec des termes différents, qui doivent être normalisés avec ces valeurs. Stockez la valeur d’origine dans le champ [EventOriginalUserType](#srcoriginalusertype). |
| <a name="srcoriginalusertype"></a>**SrcOriginalUserType** | | | Le type d'utilisateur source original, s'il est fourni par la source. |
| <a name="srcmacaddr"></a>**SrcMacAddr** | Obligatoire | Adresse MAC | Adresse MAC du client demandant un bail DHCP. <br><br>**Remarque** : le serveur DHCP Windows enregistre l’adresse MAC de manière non standard, en omettant les deux-points, qui doivent être insérés par l’analyseur.<br><br>Exemple : `06:10:9f:eb:8f:14` |
| <a name="dhcpleaseduration"></a>**DhcpLeaseDuration** | Facultatif | Integer | Durée du bail accordé à un client, exprimée en secondes. |  
|<a name="dhcpsessionid"></a>**DhcpSessionId** | Facultatif | string | Identificateur de session signalé par le périphérique de création de rapport. Pour le serveur DHCP Windows, défini sur le champ TransactionID. <br><br>Exemple : `2099570186` |
| **SessionId** | Alias | Chaîne | Alias pour [DhcpkSessionId](#dhcpsessionid). |
| <a name="dhcpsessionduration"></a>**DhcpSessionDuration** | Facultatif | Integer | Durée, en millisecondes, de la session DHCP.<br><br>Exemple : `1500` |
| **Durée** | Alias | | Alias pour [DhcpSessionDuration](#dhcpsessionduration). |
| **DhcpSrcDHCId** | Facultatif | String | ID client DHCP, tel que défini par [RFC4701](https://datatracker.ietf.org/doc/html/rfc4701). |
| **DhcpCircuitId** | Facultatif | String | ID de circuit DHCP, tel que défini par [RFC3046](https://datatracker.ietf.org/doc/html/rfc3046). |
| **DhcpSubscriberId** | Facultatif | String | ID d’abonné DHCP, tel que défini par [RFC3993](https://datatracker.ietf.org/doc/html/rfc3993). |
| **DhcpVendorClassId**  | Facultatif | String | ID de classe de fournisseur DHCP, tel que défini par [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925). |
| **DhcpVendorClass**  | Facultatif | String | Classe de fournisseur DHCP, telle que définie par [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925).|
| **DhcpUserClassId**  | Facultatif | String | ID de classe d’utilisateur DHCP, tel que défini par [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004).|
| **DhcpUserClass** | Facultatif | String | Classe d’utilisateur DHCP, telle que définie par [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004).|
| | | | |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- Regardez le [webinaire sur ASIM](https://www.youtube.com/watch?v=WoGD-JeC7ng) ou passez en revue les [diapositives](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Schémas du modèle d’informations SIEM avancé](normalization-about-schemas.md)
- [Analyseurs de modèle d’informations SIEM avancé](normalization-about-parsers.md)
- [Contenu du modèle d’informations SIEM avancé](normalization-content.md)
