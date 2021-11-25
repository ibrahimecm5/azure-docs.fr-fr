---
title: Schémas ASIM (Advanced SIEM Information Model) | Microsoft Docs
description: Cet article explique les schémas ASIM (Advanced SIEM Information Model) et comment ils aident ASIM à normaliser les données provenant de nombreuses sources différentes dans une présentation uniforme
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
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5a315d02b29e113958c1bf2e9004e33b9021caa7
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520310"
---
# <a name="advanced-siem-information-model-asim-schemas-public-preview"></a>Schémas du modèle ASIM (Azure SIEM Information Model) (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Un schéma [ASIM](normalization.md) est un ensemble de champs qui représentent une activité. L’utilisation des champs d’un schéma normalisé dans une requête garantit que la requête fonctionnera avec toutes les sources normalisées.

Les références de schéma présentent les champs qui composent chaque schéma. ASIM définit actuellement les schémas suivants :

 - [Session réseau](normalization-schema.md)
 - [Activité DNS](dns-normalization-schema.md)
 - [Activité DHCP](dhcp-normalization-schema.md)
 - [Événement de processus](process-events-normalization-schema.md)
 - [Événement d’authentification](authentication-normalization-schema.md)
 - [Événement du Registre](registry-event-normalization-schema.md)
 - [Activité de fichier](file-event-normalization-schema.md)

> [!IMPORTANT]
> ASIM n'est actuellement disponible qu'en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="schema-concepts"></a>Concepts de schéma

Les concepts suivants permettent de comprendre les documents de référence de schéma et d’étendre le schéma de manière normalisée dans le cas où vos données incluent des informations que le schéma ne couvre pas.


|Concept  |Description  |
|---------|---------|
|**Noms de champs**     |   Les noms de champs sont au cœur de chaque schéma. Les noms de champs appartiennent aux groupes suivants : <br><br>- Champs communs à tous les schémas <br>- Champs spécifiques à un schéma <br>- Champs qui représentent des entités, telles que des utilisateurs, qui prennent part au schéma. Les champs qui représentent des entités [sont similaires dans tous les schémas](#entities). <br><br>Lorsque des sources ont des champs qui ne sont pas présentés dans le schéma documenté, ils sont normalisés pour maintenir la cohérence. Si les champs supplémentaires représentent une entité, ils sont normalisés en fonction des recommandations des champs d’entité. Dans le cas contraire, les schémas s’efforcent de maintenir la cohérence entre tous les schémas.<br><br> Par exemple, alors que les journaux d’activité du serveur DNS ne fournissent pas d’informations utilisateur, les journaux d’activité DNS d’un point de terminaison peuvent inclure des informations utilisateur, qui peuvent être normalisées conformément aux instructions de l’entité utilisateur.      |
|**Types de champ**     |  Chaque champ de schéma a un type. L’espace de travail Log Analytics possède un ensemble limité de types de données. Par conséquent, Microsoft Sentinel utilise un type logique pour de nombreux champs de schéma, ce que Log Analytics n’impose pas, mais qui est requis pour la compatibilité des schémas. Les types de champs logiques garantissent que les valeurs et les noms de champs sont cohérents entre les sources.  <br><br>Pour plus d’informations, consultez [Types logiques](#logical-types).     |
|**Classe des champs**     |Les champs peuvent avoir plusieurs classes, qui définissent le moment où les champs doivent être implémentés par un analyseur : <br><br>Les champs -    **obligatoires** doivent apparaître dans chaque analyseur. Si votre source ne fournit pas d’informations pour cette valeur, ou si les données ne peuvent pas être ajoutées par ailleurs, elle ne prend pas en charge la plupart des éléments de contenu qui font référence au schéma normalisé.<br>Les champs -  **recommandés** doivent être normalisés s’ils sont disponibles. Toutefois, ils peuvent ne pas être disponibles dans chaque source, et tout élément de contenu qui fait référence à ce schéma normalisé doit prendre en compte la disponibilité. <br>Les champs -  **facultatifs**, s’ils sont disponibles, peuvent être normalisés ou laissés dans leur forme d’origine. En règle générale, un analyseur minimal ne les normalise pas pour des raisons de performances.    |
|**Entités**     | Les événements évoluent autour des entités, telles que les utilisateurs, les hôtes, les processus ou les fichiers, et chaque entité peut nécessiter plusieurs champs pour la décrire. Par exemple, un hôte peut avoir un nom et une adresse IP. <br><br>Un seul enregistrement peut inclure plusieurs entités du même type, comme un hôte source et un hôte de destination. <br><br>Le modèle ASIM définit la manière de décrire les entités de manière cohérente, et les entités permettent d’étendre les schémas. <br><br>Par exemple, si le schéma de session réseau n’inclut pas d’informations sur les processus, certaines sources d’événements fournissent des informations sur les processus qui peuvent être ajoutés. Pour plus d’informations, consultez [Entités](#entities). |
|**Alias**     |  Dans certains cas, les différents utilisateurs s’attendent à ce qu’un champ ait des noms différents. Par exemple, dans la terminologie DNS, il s’agit d’un champ nommé `query`, alors que plus généralement, il contient un nom de domaine. Les alias résolvent ce problème d’ambiguïté en autorisant plusieurs noms pour une valeur spécifiée. La classe alias est la même que celle du champ qu’elle associe.<br><br>Notez que Log Analytics ne remplace pas l’alias. Pour implémenter des alias, les analyseurs créent une copie de la valeur d’origine à l’aide de l'opérateur `extend`.        |
| | |

## <a name="logical-types"></a>Types logiques

Chaque champ de schéma a un type. Certains intègrent des types de Azure Log Analytics, tels que `string`, `int`, `datetime` ou `dynamic`. D’autres champs ont un type logique qui représente la façon dont les valeurs de champ doivent être normalisées.

|Type de données  |Type physique  |Format et valeur  |
|---------|---------|---------|
|**Booléen**     |   Bool      |    Utilisez le type de données `bool`KQL intégré plutôt qu’une représentation des valeurs booléennes numériques ou sous forme de chaîne.     |
|**Énuméré**     |  Chaîne       |   Liste de valeurs définies explicitement pour le champ. La définition de schéma répertorie les valeurs acceptées.      |
|**Date/Heure**     |  Selon la fonctionnalité de méthode d’ingestion, utilisez l’une des représentations physiques suivantes par ordre décroissant des priorités : <br><br>- Type de DateHeure intégré de Log Analytics <br>- Champ de type entier utilisant une représentation numérique de DateHeure de Log Analytics. <br>- Champ de type chaîne utilisant une représentation numérique de DateHeure de Log Analytics <br>- Champ de chaîne stockant un [format de date/heure de Log Analytics](/azure/data-explorer/kusto/query/scalar-data-types/datetime) pris en charge.       |  La [représentation de date et d’heure de Log Analytics](/azure/kusto/query/scalar-data-types/datetime) est similaire, mais différente de celle d’Unix. Pour plus d'informations, consultez les [instructions de conversion](/azure/kusto/query/datetime-timespan-arithmetic). <br><br>**Remarque** : le cas échéant, l’heure doit être ajustée au fuseau horaire. |
|**Adresse MAC**    |  String       | Notation hexadécimale à deux-points        |
|**Adresse IP**     |Chaîne         |    Les schémas Microsoft Sentinel n’ont pas d’adresses IPv4 et IPv6 distinctes. Tout champ d’adresse IP peut inclure une adresse IPv4 ou IPv6, comme suit : <br><br>- **IPv4** dans une notation décimale à points<br>- **IPv6** en notation 8 hextets, permettant les formes abrégées<br><br>Par exemple :<br>- **IPv4** :`192.168.10.10` <br>- **IPv6** : `FEDC:BA98:7654:3210:FEDC:BA98:7654:3210`<br>- **Formulaire court IPv6** : `1080::8:800:200C:417A`     |
|**FQDN**        |   string      |    Un nom de domaine complet à l’aide d’une notation par points, par exemple `docs.microsoft.com` |
|**Pays**     |   String      |    Chaîne utilisant [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html) conformément à la priorité suivante : <br><br> - Codes alpha-2, tels que `US` pour les États-Unis <br> - Codes alpha-3, tels que `USA` pour les États-Unis <br>- Nom court<br><br>La liste de code se trouve sur le [site Web International Standards Organization (ISO)](https://www.iso.org/obp/ui/#search)|
|**Région**     | String        |   Nom de subdivision du pays selon la norme ISO 3166-2<br><br>La liste de code se trouve sur le [site Web International Standards Organization (ISO)](https://www.iso.org/obp/ui/#search)|
|**Ville**     |  String       |         |
|**Longitude**     | Double        |  Représentation de coordonnée ISO 6709 (décimale signée)       |
|**Latitude**     | Double        |    Représentation de coordonnée ISO 6709 (décimale signée)     |
|**MD5**     |    Chaîne     |  32 caractères hexadécimaux       |
|**SHA1**     |   Chaîne      | 40 caractères hexadécimaux        |
|**SHA256**     | Chaîne        |  64 caractères hexadécimaux       |
|**SHA512**     |   Chaîne      |  128 caractères hexadécimaux       |
| | | |

## <a name="common-fields"></a><a name="common"></a>Champs communs

Certains champs sont communs à tous les schémas ASIM. Veuillez noter que chaque schéma peut ajouter des directives pour l'utilisation de certains des champs communs dans le contexte du schéma spécifique. Par exemple, les valeurs autorisées pour le champ **EventType** peuvent varier selon le schéma, tout comme la valeur du champ **EventSchemaVersion**.

Les champs suivants sont générés par Log Analytics pour chaque enregistrement et peuvent être remplacés lors de la [création d’un connecteur personnalisé](create-custom-connector.md).

| Champ         | Type     | Discussion      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | DATETIME | Heure à laquelle l’événement a été généré par l’appareil de création de rapports.|
| **_ResourceId**   | guid     | ID de ressource Azure de l’appareil ou du service de création de rapports, ou l’ID de ressource de redirecteur de journal pour les événements transférés avec Syslog, CEF ou WEF. |
| **Type** | String | Table d’origine à partir de laquelle l’enregistrement a été extrait. Ce champ est utile lorsque le même événement peut être reçu via plusieurs canaux dans différentes tables, avec les mêmes valeurs [EventVendor](#eventvendor) et [EventProduct](#eventproduct).<br><br>Par exemple, un événement Sysmon peut être collecté soit vers le tableau `Event`, soit vers le tableau `WindowsEvent`. |
| | | |

> [!NOTE]
> Log Analytics ajoute également d’autres champs qui sont moins pertinents pour les cas d’usage de sécurité. Pour plus d’informations, consultez [Colonnes standard dans les journaux d’Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>


Les champs suivants sont définis par l'ASIM pour tous les schémas :

| Champ               | Classe       | Type       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Facultatif    | Chaîne     |     Message général ou description, inclus dans l’enregistrement ou généré depuis l’enregistrement.   |
| **EventCount**          | Obligatoire   | Integer    |     Nombre d’événements décrits par l’enregistrement. <br><br>Cette valeur est utilisée quand la source prend en charge l’agrégation, et un seul enregistrement peut représenter plusieurs événements. <br><br>Pour les autres sources, affectez à la valeur `1`.   |
| **EventStartTime**      | Obligatoire   | Date/heure  |      Si la source prend en charge l’agrégation et que l’enregistrement représente plusieurs événements, ce champ spécifie l’heure à laquelle le premier événement a été généré. <br><br>Sinon, ce champ associe le champ [TimeGenerated](#timegenerated). |
| **EventEndTime**        | Obligatoire   | Alias      |      Alias au champ [TimeGenerated](#timegenerated).    |
|  <a name="eventtype"></a>**EventType**           | Obligatoire   | Énuméré |    Décrit l’opération signalée par l’enregistrement. Chaque schéma documente la liste des valeurs valides pour ce champ. |
| **EventSubType** | Facultatif | Énuméré | Décrit une subdivision de l’opération rapportée dans le champ [EventType](#eventtype). Chaque schéma documente la liste des valeurs valides pour ce champ. |
| <a name="eventresult"></a>**EventResult** | Obligatoire | Énuméré | L’une des valeurs suivantes : **Succès**, **Partiel**, **Échec**, **NA** (Non applicable).<br> <br>La valeur peut être fournie dans l’enregistrement source à l’aide de termes différents, qui doivent être normalisés avec ces valeurs. Sinon, la source peut fournir uniquement le champ [EventResultDetails](#eventresultdetails) qui devrait être analysé pour dériver la valeur EventResult.<br><br>Exemple : `Success`|
| <a name="eventresultdetails"></a>**EventResultDetails** | Obligatoire | Alias | Raison ou détails du résultat rapporté dans le champ [**EventResult**](#eventresult). Chaque schéma documente la liste des valeurs valides pour ce champ.<br><br>Exemple : `NXDOMAIN`|
| **EventOriginalUid**    | Facultatif    | Chaîne     |   ID unique de l’enregistrement d’origine, s’il est fourni par la source.<br><br>Exemple : `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Facultatif    | Chaîne     |   Type ou ID d’événement d’origine, s’il est fourni par la source. Par exemple, ce champ sera utilisé pour stocker l’ID d’événement d’origine Windows.<br><br>Exemple : `4624`|
| <a name="eventproduct"></a>**EventProduct**        | Obligatoire   | Chaîne     |             Produit générant l’événement. <br><br>Exemple : `Sysmon`<br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.           |
| **EventProductVersion** | Facultatif    | Chaîne     | Version du produit générant l’événement. <br><br>Exemple : `12.1`      |
| <a name="eventvendor"></a>**EventVendor**         | Obligatoire   | Chaîne     |           Fournisseur du produit générant l’événement. <br><br>Exemple : `Microsoft`  <br><br>**Remarque** : Ce champ n’est peut-être pas disponible dans l’enregistrement source. Dans ce cas, ce champ doit être défini par l’analyseur.  |
| **EventSchemaVersion**  | Obligatoire   | Chaîne     | Version du schéma. Chaque schéma documente sa version en cours.         |
| **EventReportUrl**      | Facultatif    | Chaîne     | URL fournie dans l’événement pour une ressource qui apporte des informations supplémentaires sur l’événement.|
| <a name="dvc"></a>**Dvc** | Obligatoire       | Chaîne     | Un identifiant unique du dispositif sur lequel l'événement s'est produit ou qui a signalé l'événement, selon le schéma. <br><br>Ce champ peut prendre l’alias des champs [DvcFQDN](#dvcfqdn), [Dvcld](#dvcid), [DvcHostname](#dvchostname) ou [DvclpAddr](#dvcipaddr). Pour les sources cloud pour lesquelles il n’y a pas d’appareil apparent, utilisez la même valeur que celle du champ [Event Product](#eventproduct).            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recommandé | Adresse IP | L'adresse IP du dispositif sur lequel l'événement s'est produit ou qui a signalé l'événement, selon le schéma. <br><br>Exemple : `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | Recommandé | Nom d’hôte   | Le nom d'hôte du dispositif sur lequel l'événement s'est produit ou qui a signalé l'événement, selon le schéma. <br><br>Exemple : `ContosoDc.Contoso.Azure`               |
| <a name="dvcdomain"></a>**DvcDomain** | Recommandé | Chaîne | Le domaine du dispositif sur lequel l'événement s'est produit ou qui a signalé l'événement, selon le schéma.<br><br>Exemple : `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | Recommandé | Énuméré | Type de  [DvcDomain](#dvcdomain), s’il est connu. Les valeurs possibles incluent :<br>- `Windows`, telles que pour `contoso\mypc`<br>- `FQDN`, telles que pour `docs.microsoft.com`<br><br>**Remarque**: ce champ est obligatoire si le champ [DvcDomain](#dvcdomain) est utilisé. |
| <a name="dvcfqdn"></a>**DvcFQDN** | Facultatif | String | Le nom d'hôte du dispositif sur lequel l'événement s'est produit ou qui a signalé l'événement, selon le schéma. <br><br> Exemple : `Contoso\DESKTOP-1282V4D`<br><br>**Remarque** : ce champ prend en charge à la fois le format FQDN traditionnel et le format Windows domain\hostname. Le champ [DvcDomainType](#dvcdomaintype) reflète le format utilisé.  |
| <a name ="dvcid"></a>**DvcId**               | Facultatif    | String     | L'ID unique du dispositif sur lequel l'événement s'est produit ou qui a signalé l'événement, selon le schéma. <br><br>Exemple : `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcIdType** | Facultatif | Énuméré | Type de [Dvcld](#dvcid), s’il est connu. Les valeurs possibles incluent :<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si plusieurs ID sont disponibles, utilisez le premier de la liste et stockez les autres à l’aide des noms de champs  **DvcAzureResourceId** et **DvcMDEid** , respectivement.<br><br>**Remarque**: ce champ est obligatoire si le champ [Dvcld](#dvcid) est utilisé. |
| **DvcMacAddr**          | Facultatif    | MAC        |   Adresse MAC de l’appareil sur lequel l’événement s’est produit.  <br><br>Exemple : `00:1B:44:11:3A:B7`       |
| **DvcZone** | Facultatif | String | Le réseau sur lequel l'événement s'est produit ou qui a signalé l'événement, selon le schéma. La zone est définie par le dispositif de reporting.<br><br>Exemple : `Dmz` |
| **DvcOs**               | Facultatif    | Chaîne     |         Système d’exploitation en cours d’exécution sur l’appareil sur lequel l’événement s’est produit.    <br><br>Exemple : `Windows`    |
| **DvcOsVersion**        | Facultatif    | Chaîne     |   Version du système d’exploitation de l’appareil sur lequel l’événement s’est produit. <br><br>Exemple : `10` |
| **DvcAction** | Facultatif | String | Pour les systèmes de sécurité déclarés, l'action prise par le système, le cas échéant. <br><br>Exemple : `Blocked` |
| <a name="additionalfields"></a>**AdditionalFields**    | Facultatif    | Dynamique    | Si votre source fournit des informations supplémentaires à conserver, conservez-les avec les noms de champs d’origine ou créez le champ dynamique **AdditionalFields**, puis ajoutez-y les informations supplémentaires sous forme de paires clé/valeur.    |
| | | | |

> [!NOTE]
> Log Analytics ajoute également d’autres champs qui sont moins pertinents pour les cas d’usage de sécurité. Pour plus d’informations, consultez [Colonnes standard dans les journaux d’Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>


## <a name="entities"></a>Entities

Les événements évoluent autour des entités, telles que les utilisateurs, les hôtes, les processus ou les fichiers. La représentation d’entité permet à plusieurs entités du même type de faire partie d’un seul enregistrement et prend en charge plusieurs attributs pour les mêmes entités.

Pour activer la fonctionnalité d’entité, la représentation d’entité présente les indications suivantes :

|Instruction  |Description  |
|---------|---------|
|**Descripteurs et alias**     | Étant donné qu’un seul événement comprend souvent plusieurs entités du même type, comme les hôtes source et de destination, les *descripteurs* sont utilisés comme préfixes pour identifier tous les champs associés à une entité spécifique. <br><br>Pour maintenir la normalisation, l'Advanced SIEM Information Model utilise un petit ensemble de descripteurs standard, en choisissant les plus appropriés pour le rôle spécifique des entités.  <br><br>Si une seule entité d’un type est pertinente pour un événement, il n’est pas nécessaire d’utiliser un descripteur. En outre, un ensemble de champs sans descripteur crée un alias pour l’entité la plus utilisée de chaque type.  |
|**Identificateurs et types**     | Un schéma normalisé autorise plusieurs identificateurs pour chaque entité, qui sont censés coexister dans les événements. Si l’événement source a d’autres identificateurs d’entité qui ne peuvent pas être mappés au schéma normalisé, conservez-les dans le formulaire source ou utilisez le champ dynamique `AdditionalFields`. <br><br>Pour conserver les informations de type pour les identificateurs, stockez le type, le cas échéant, dans un champ avec le même nom et un suffixe de `Type`. Par exemple : `UserIdType`.         |
|**Attributs**     |   Les entités ont souvent d’autres attributs qui ne servent pas d’identificateur et peuvent également être qualifiés avec un descripteur. Par exemple, si l’utilisateur source possède des informations de domaine, le champ normalisé est `SrcUserDomain`.      |
| | |

Chaque schéma définit explicitement les entités et les champs d’entité centraux. Les directives suivantes vous permettent de comprendre les champs centraux du schéma, ainsi que la manière d'étendre les schémas de manière normalisée en utilisant d'autres entités ou champs d'entité qui ne sont pas explicitement définis dans le schéma.

### <a name="the-user-entity"></a>L'entité Utilisateur

Les descripteurs utilisés pour un utilisateur sont **Acteur**, **Utilisateur cible** et **Utilisateur mis à jour**, comme décrit dans les scénarios suivants :

|Activité  |Scénario complet  |Scénario d’entité unique utilisé pour l’alias  |
|---------|---------|---------|
|**Create User** (Créer un utilisateur)     |  Un **Acteur** a créé ou modifié un **Utilisateur cible**       |  L’**Utilisateur** (cible) a été créé.       |
|**Modification d’une planification**     |   Un **Acteur** a renommé un **Utilisateur cible** en **Utilisateur mis à jour**. L’**Utilisateur mis à jour** ne dispose généralement pas de toutes les informations associées à un utilisateur et a des chevauchements avec l’**Utilisateur cible**.      |         |
|**Connexion réseau**     |    Processus s’exécutant comme **Acteur** sur l’hôte source, communiquant avec un processus s’exécutant comme **Utilisateur cible** sur l’hôte de destination     |         |
|**Requête DNS**     | Un **Acteur** a initié une requête DNS        |         |
|**Connexion**     |    Un **Acteur** s’est connecté à un système en tant qu’**Utilisateur cible**.     |Un utilisateur (cible) s’est connecté         |
|**Création de processus**     |   Un **Acteur** (l’utilisateur associé au processus d’initiation) a initié la création du processus. Le processus a créé des exécutions sous les informations de connexion d’un **Utilisateur cible** (l’utilisateur lié au processus cible).      |  Le processus créé s’exécute sous les informations de connexion d’un **Utilisateur** (cible).       |
|**Courrier électronique**     |     Un **Acteur** envoie un e-mail à un **Utilisateur cible**    |         |
| | | |

Le tableau suivant décrit les identificateurs pris en charge pour un utilisateur :

|Champ normalisé  |Type  |Format et types pris en charge  |
|---------|---------|---------|
|**UserId**     |    Chaîne     |   Représentation unique, alphanumérique et lisible par l’ordinateur d’un utilisateur dans un système. <br><br>Les formats et types pris en charge sont :<br>    - **SID** (Windows) : `S-1-5-21-1377283216-344919071-3415362939-500`<br>    -  **UID** (Linux) : `4578`<br>    -    **AADID** (Azure Active Directory) : `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>    - **OktaId** : `00urjk4znu3BcncfY0h7`<br>    - **AWSId** : `72643944673`<br><br>    Stockez le type ID dans le champ `UserIdType`. Si d’autres ID sont disponibles, nous vous conseillons de normaliser les noms des champs pour `UserSid`, `UserUid`, `UserAADID`, `UserOktaId` et `UserAwsId`, respectivement.       |
|**Nom d’utilisateur**     |  Chaîne       |   Nom d’utilisateur, y compris les informations de domaine, le cas échéant, dans l’un des formats suivants et dans l’ordre de priorité suivant : <br> -   **Upn/E-mail** : `johndow@contoso.com` <br>  -    **Windows** : `Contoso\johndow` <br> -   **DN** : `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM` <br>  - **Simple** : `johndow`. Utilisez ce formulaire uniquement si les informations de domaine ne sont pas disponibles. <br><br> Stockez le type Nom de domaine dans le champ `UsernameType`.    |
| | | |


### <a name="the-process-entity"></a>Entité Processus

Les descripteurs utilisés pour un utilisateur sont `Acting Process`, `Target Process` et `Parent Process`, comme décrit dans les scénarios suivants :

- **Connexion réseau**. Un **Processus agissant** a initié une connexion réseau pour communiquer avec un **Processus cible** sur un système distant.
- **Requête DNS**.  Un **Processus agissant** a initié une requête DNS
- **Connexion**.  Un **Processus agissant** a initié une connexion à un système distant qui a exécuté un **Processus cible** en son nom.
- **Création de processus**. Un **Processus agissant** a initié une création de **Processus cible**. Le **Processus parent** est le parent du processus agissant.

Le tableau suivant décrit les identificateurs pris en charge pour les processus :

|Champ normalisé  |Type  |Format et types pris en charge  |
|---------|---------|---------|
|**Id**     |    Chaîne     |   ID de processus attribué par le système d’exploitation.      |
|**Guid**     |  Chaîne       |   GUID de processus attribué par le système d’exploitation. L’identificateur unique (GUID) est couramment unique entre les redémarrages du système, tandis que l’ID est souvent réutilisé.   |
|**Chemin d’accès**     |    Chaîne     |   Chemin complet du processus, y compris le nom de répertoire et de fichier.       |
|**Nom**     |  Alias       |  Le nom du processus est un alias du chemin d’accès.   |
| | | |


Pour plus d'informations, voir la [référence du schéma de normalisation des événements de processus Microsoft Sentinel (préversion publique)](process-events-normalization-schema.md).

### <a name="the-device-entity"></a>Entité de l’appareil

Les schémas de normalisation essaient de suivre l’intuition de l’utilisateur autant que possible et, par conséquent, gèrent les appareils de différentes manières, en fonction du scénario :

- Lorsque le contexte d’événement implique un appareil source et cible, les descripteurs `Src` et `Target` sont utilisés. Dans ces cas-là, le descripteur `Dvc` est utilisé pour l’appareil de création de rapports.

- Pour les événements à un seul appareil, tels que les événements de système d’exploitation local, le descripteur `Dvc` est utilisé.

- Si un autre appareil de passerelle est référencé dans l’événement et que la valeur est différente de celle de l’appareil de création de rapports, le descripteur `Gateway` est utilisé.

Les instructions de gestion des appareils sont expliquées plus en détail ci-dessous :

- **Connexion réseau**. Une connexion avait été établie d’un **appareil source** (`Src`) vers un **appareil cible** (`Target`). La connexion avait été signalée par un **appareil** (de création de rapports) (`Dvc`).
- **Connexion de réseau en proxy**. Une connexion avait été établie d’un **appareil source** (`Src`) vers un **appareil cible** (`Target`) via un **appareil de passerelle** (`Gateway`). Un **appareil** (de création de rapports) a signalé la connexion.
- **Requête DNS**.  Une requête DNS avait été initiée d’un **appareil source** (`Src`).
- **Connexion**   Une connexion avait été initiée d’un **appareil source** (`Src`) vers un système distant sur un **appareil cible** (`Target`).
- **Processus**   Un processus avait été initié sur un **appareil** (`Dvc`).

Le tableau suivant décrit les identificateurs pris en charge pour des utilisateurs :

|Champ normalisé  |Type  |Format et types pris en charge  |
|---------|---------|---------|
|**Nom d’hôte**     |    Chaîne     |        |
|**FQDN**     |  Chaîne       |   Nom de domaine complet   |
|**IpAddr**     |    Adresse IP     |   Alors que les appareils peuvent avoir plusieurs adresses IP, les événements ont généralement une seule adresse IP d’identification. L’exception est un appareil de passerelle qui peut avoir deux adresses IP pertinentes. Pour un appareil de passerelle, utilisez `UpstreamIpAddr` et `DownstreamIpAddr`.      |
|**HostId**     |  Chaîne       |     |
| | | |


> [!NOTE]
> `Domain` est un attribut standard d’un appareil, mais n’est pas un identificateur complet.
>

Pour plus d'informations, voir la [référence du schéma de normalisation de l'authentification de Microsoft Sentinel (préversion publique)](authentication-normalization-schema.md).

### <a name="sample-entity-mapping"></a>Exemple de mappage d’entité

Cette section utilise [l'événement Windows 4624](/windows/security/threat-protection/auditing/event-4624) comme exemple pour décrire comment les données de l'événement sont normalisées pour Microsoft Sentinel.

Cet événement comporte les entités suivantes :

|Terminologie Microsoft  |Préfixe du champ d’événement d’origine |Préfixe du champ ASIM  |Description  |
|---------|---------|---------|---------|
|**Subject**     | `Subject`        |   `Actor`      |  Utilisateur qui a signalé des informations sur une connexion réussie.      |
|**Nouvelle connexion**     |    `Target`     |  `TargetUser`       |  Utilisateur pour lequel la connexion a été effectuée.       |
|**Processus**     |    -     |     `ActingProcess`    |   Processus qui a tenté de se connecter.      |
|**Informations sur le réseau**     |   -      |   `Src`      |     Ordinateur à partir duquel une tentative de connexion a été effectuée.    |
| | | | |


Sur la base de ces entités, l’[événement Windows 4624](/windows/security/threat-protection/auditing/event-4624) est normalisé comme suit (certains champs sont facultatifs) :

|Champ normalisé  |Champ d'origine  |Valeur dans l’exemple  |Notes  |
|---------|---------|---------|---------|
|**ActorUserId**     |  SubjectUserSid       |  S-1-5-18        |        |
|**ActorUserIdType**     |  -       | SID        |         |
|**ActorUserName**     |   SubjectDomainName\ SubjectUserName      |  WORKGROUP\WIN-GG82ULGC9GO$       |  Construit en concaténant les deux champs       |
|**ActorUserNameType**     |   -      |   Windows      |         |
|**ActorSessionId**     | SubjectLogonId        |  0x3e7       |         |
|**TargetUserId**     |   TargetUserSid      |    S-1-5-21-1377283216-344919071-3415362939-500     |         |
|**UserId**     |    TargetUserSid     |   alias      |         |
|**TargetUserIdType**     |   -      |    SID     |         |
|**TargetUserName**     | TargetDomainName\ TargerUserName        |   Administrator\WIN-GG82ULGC9GO$      |   Construit en concaténant les deux champs      |
|**Nom d’utilisateur**     |  TargetDomainName\ TargerUserName       |  alias       |         |
|**TargetUserNameType**     | -        |  Windows       |         |
|**TargetSessionId**     |   TargetLogonId      |  0x8dcdc       |         |
|**ActingProcessName**     |  ProcessName       |  C:\\Windows\\System32\\svchost.exe       |         |
|**ActingProcessId**     |    ProcessId     |     0x44c    |         |
|**SrcHostname**     |    WorkstationName     | Windows        |         |
|**SrcIpAddr**     |  IpAddress       |     127.0.0.1    |         |
|**SrcPortNumber**     |  IpPort       |  0       |         |
|**TargetHostname**     |   Computer      |  WIN-GG82ULGC9GO           |         |
|**Nom d’hôte**     |     Computer    |     Alias    |         |
| | | | |


## <a name="next-steps"></a>Étapes suivantes

Cet article donne un aperçu de la normalisation dans Microsoft Sentinel et de l'Advanced SIEM Information Model.

Pour plus d'informations, consultez les pages suivantes :
- [Aperçu de l'Advanced SIEM Information Model](normalization.md)
- [Analyseurs Advanced SIEM Information Model](normalization-about-parsers.md)
- [Contenu de l’Advanced SIEM Information Model](normalization-content.md)