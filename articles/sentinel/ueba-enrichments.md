---
title: Informations de référence sur les enrichissements UEBA Microsoft Sentinel | Microsoft Docs
description: Cet article présente les enrichissements d’entité générés par l’analyse du comportement des entités de Microsoft Sentinel.
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: reference
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 345f34d2e0db508b831dca6d7f19b27436448de0
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519816"
---
# <a name="microsoft-sentinel-ueba-enrichments-reference"></a>Informations de référence sur les enrichissements UEBA Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article décrit la table Microsoft Sentinel nommée **BehaviorAnalytics** figurant dans **Journaux** et mentionnée dans les [pages de détails de l’entité](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages). Il précise les détails des champs d’enrichissements d’entités de cette table, dont vous pouvez utiliser le contenu pour orienter et affûter votre examen des incidents de sécurité.

Les trois champs dynamiques suivants de la table BehaviorAnalytics sont décrits dans les [tableaux ci-dessous](#entity-enrichments-dynamic-fields).

Les champs [UsersInsights](#usersinsights-field) et [DevicesInsights](#devicesinsights-field) contiennent des informations sur les entités provenant des sources Active Directory/Azure AD et Microsoft Threat Intelligence.

Le champ [ActivityInsights](#activityinsights-field) contient des informations sur les entités basées sur les profils de comportement produits par l’analyse du comportement des entités de Microsoft Sentinel. 

<a name="baseline-explained"></a>Les activités des utilisateurs sont analysées par rapport à une ligne de base qui est compilée de façon dynamique à chaque utilisation. Chaque activité a sa période de référence définie à partir de laquelle la base de référence dynamique est dérivée. La période de référence est spécifiée dans la colonne [**Ligne de base**](#activityinsights-field) de ce tableau.

> [!NOTE]
> La colonne **Nom de l’enrichissement** de toutes les tables de champs [Enrichissement d’entité](#entity-enrichments-dynamic-fields) présente deux lignes d’informations. 
>
> - La première, en **gras**, est le « nom convivial » de l’enrichissement.
> - La deuxième, *(en italiques et entre parenthèses)* , est le nom de champ de l’enrichissement, tel qu’il est stocké dans le [**tableau d’analyse comportementale**](#behavioranalytics-table).

> [!IMPORTANT]
> Les fonctionnalités indiquées sont disponibles en préversion. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>
## <a name="behavioranalytics-table"></a>Table BehaviorAnalytics

Le tableau suivant décrit les données d’analyse du comportement affichées sur chaque [page de détails de l’entité](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages) dans Microsoft Sentinel.

| Champ                     | Type | Description                                                  |
|---------------------------|------|--------------------------------------------------------------|
| **TenantId**              | string | Numéro d’identification unique du locataire.                             |
| **SourceRecordId**        | string | Numéro d’identification unique de l’événement EBA.                          |
| **TimeGenerated**         | DATETIME | Timestamp de l’occurrence de l’activité.                   |
| **TimeProcessed**         | DATETIME | Timestamp du traitement de l’activité par le moteur EBA. |
| **ActivityType**          | string | Catégorie de haut niveau de l’activité.                        |
| **ActionType**            | string | Nom normalisé de l’activité.                            |
| **UserName**              | string | Nom d’utilisateur de l’utilisateur ayant lancé l’activité.           |
| **UserPrincipalName**     | string | Nom d’utilisateur complet de l’utilisateur ayant lancé l’activité.      |
| **EventSource**           | string | Source de données ayant fourni l’événement d’origine.               |
| **SourceIPAddress**       | string | Adresse IP à partir de laquelle l’activité a été lancée.               |
| **SourceIPLocation** | string | Pays à partir duquel l’activité a été lancée, enrichie à partir de l’adresse IP. |
| **SourceDevice**          | string | Nom d’hôte de l’appareil ayant lancé l’activité.         |
| **DestinationIPAddress**  | string | Adresse IP de la cible de l’activité.                   |
| **DestinationIPLocation** | string | Pays de la cible de l’activité, enrichi à partir de l’adresse IP. |
| **DestinationDevice**     | string | Nom de l’appareil cible.                                  |
| **UsersInsights**         | dynamique | Enrichissements contextuels des utilisateurs impliqués ([voir les détails ci-dessous](#usersinsights-field)). |
| **DevicesInsights**       | dynamique | Enrichissements contextuels des appareils impliqués ([voir les détails ci-dessous](#devicesinsights-field)). |
| **ActivityInsights**      | dynamique | Analyse contextuelle de l’activité basée sur notre profilage ([voir les détails ci-dessous](#activityinsights-field)). |
| **InvestigationPriority** | int | Score d’anomalie, entre 0 et 10 (0 = bénigne, 10 = très anormale).   |



## <a name="entity-enrichments-dynamic-fields"></a>Champs dynamiques des enrichissements d’entités

### <a name="usersinsights-field"></a>Champ UsersInsights

Le tableau suivant décrit les enrichissements qui figurent dans le champ dynamique **UsersInsights** de la table BehaviorAnalytics :

| Nom d’enrichissement | Description | Exemple de valeur |
| --- | --- | --- |
| **Nom complet du compte**<br>*(AccountDisplayName)* | Nom complet du compte de l’utilisateur. | Admin, Hayden Cook |
| **Domaine du compte**<br>*(AccountDomain)* | Nom de domaine du compte de l’utilisateur. |  |
| **ID d’objet du compte**<br>*(AccountObjectID)* | ID de l’objet du compte de l’utilisateur. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Rayon d'impact**<br>*(BlastRadius)* | Le rayon d’impact est calculé en fonction de plusieurs facteurs : la position de l’utilisateur dans l’arborescence de l’organisation et les rôles et autorisations Azure Active Directory de l’utilisateur. | Faible, moyen, élevé |
| **Est un compte dormant**<br>*(IsDormantAccount)* | Le compte n’a pas été utilisé au cours des 180 derniers jours. | True, False |
| **Est administrateur local**<br>*(IsLocalAdmin)* | Le compte dispose de privilèges d’administrateur local. | True, False |
| **Est un nouveau compte**<br>*(IsNewAccount)* | Le compte a été créé au cours des 30 derniers jours. | True, False |
| **ID de sécurité local**<br>*(OnPremisesSID)* | ID de sécurité local de l’utilisateur associé à l’action. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

### <a name="devicesinsights-field"></a>Champ DevicesInsights

Le tableau suivant décrit les enrichissements qui figurent dans le champ dynamique **DevicesInsights** de la table BehaviorAnalytics :

| Nom d’enrichissement | Description | Exemple de valeur |
| --- | --- | --- |
| **Browser**<br>*(Browser)* | Navigateur utilisé dans l’action. | Edge, Chrome |
| **Famille de périphériques**<br>*(DeviceFamily)* | Famille d’appareils utilisée dans l’action. | Windows |
| **Type d’appareil**<br>*(DeviceType)* | Type d’appareil client utilisé dans l’action. | Bureau |
| **ISP**<br>*(ISP)* | Fournisseur de services Internet utilisé dans l’action. |  |
| **Système d’exploitation**<br>*(OperatingSystem)* | Système d’exploitation utilisé dans l’action. | Windows 10 |
| **Description de l’indicateur Renseignement sur les menaces**<br>*(ThreatIntelIndicatorDescription)* | Description de l’indicateur de menace observé résolu à partir de l’adresse IP utilisée dans l’action. | L’hôte est membre de botnet: azorult |
| **Type de l’indicateur Renseignement sur les menaces**<br>*(ThreatIntelIndicatorType)* | Type de l’indicateur de menace résolu à partir de l’adresse IP utilisée dans l’action. | Botnet, C2, CryptoMining, Darknet, Ddos, MaliciousUrl, Malware, Phishing, Proxy, PUA, Watchlist |
| **Agent utilisateur**<br>*(UserAgent)* | Agent utilisateur utilisé dans l’action. | Bibliothèque de client Microsoft Azure Graph 1.0,<br>Swagger-Codegen/1.4.0.0/csharp,<br>EvoSTS |
| **Famille d’agents utilisateur**<br>*(UserAgentFamily)* | Famille d’agents utilisateur utilisée dans l’action. | Chrome, Edge, Firefox |
|

### <a name="activityinsights-field"></a>Champ ActivityInsights

Les tableaux suivants décrivent les enrichissements qui figurent dans le champ dynamique **ActivityInsights** de la table BehaviorAnalytics :

#### <a name="action-performed"></a>Action effectuée

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Action effectuée pour la première fois par l’utilisateur**<br>*(FirstTimeUserPerformedAction)* | 180 | L’action a été effectuée pour la première fois par l’utilisateur. | True, False |
| **Action effectuée de façon inhabituelle par l’utilisateur**<br>*(ActionUncommonlyPerformedByUser)* | 10 | L’action n’est généralement pas effectuée par l’utilisateur. | True, False |
| **Action effectuée de façon inhabituelle parmi les pairs**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | L’action n’est généralement pas effectuée parmi les pairs de l’utilisateur. | True, False |
| **Action effectuée pour la première fois dans le locataire**<br>*(FirstTimeActionPerformedInTenant)* | 180 | L’action a été effectuée pour la première fois par toute personne de l’organisation. | True, False |
| **Action effectuée de façon inhabituelle dans le locataire**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | L’action n’est généralement pas effectuée dans l’organisation. | True, False |
|

#### <a name="app-used"></a>Application utilisée

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Application utilisée pour la première fois par l’utilisateur**<br>*(FirstTimeUserUsedApp)* | 180 | L’application a été utilisée pour la première fois par l’utilisateur. | True, False |
| **Application utilisée de façon inhabituelle par l’utilisateur**<br>*(AppUncommonlyUsedByUser)* | 10 | L’application n’est généralement pas utilisée par l’utilisateur. | True, False |
| **Application utilisée de façon inhabituelle parmi les pairs**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | L’application n’est généralement pas utilisée parmi les pairs de l’utilisateur. | True, False |
| **Application observée pour la première fois dans le locataire**<br>*(FirstTimeAppObservedInTenant)* | 180 | L’application a été observée pour la première fois dans l’organisation. | True, False |
| **Application utilisée de façon inhabituelle dans le locataire**<br>*(AppUncommonlyUsedInTenant)* | 180 | L’application n’est généralement pas utilisée dans l’organisation. | True, False |
| 

#### <a name="browser-used"></a>Navigateur utilisé

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Première connexion de l’utilisateur via le navigateur**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | Le navigateur a été observé pour la première fois par l’utilisateur. | True, False |
| **Navigateur utilisé de façon inhabituelle par l’utilisateur**<br>*(BrowserUncommonlyUsedByUser)* | 10 | Le navigateur n’est généralement pas utilisé par l’utilisateur. | True, False |
| **Navigateur utilisé de façon inhabituelle parmi les pairs**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | Le navigateur n’est généralement pas utilisé parmi les pairs de l’utilisateur. | True, False |
| **Navigateur observé pour la première fois dans le locataire**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | Le navigateur a été observé pour la première fois dans l’organisation. | True, False |
| **Navigateur utilisé de façon inhabituelle dans le locataire**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | Le navigateur n’est généralement pas utilisé dans l’organisation. | True, False |
| 

#### <a name="country-connected-from"></a>Pays à partir duquel la connexion est établie

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Première connexion de l’utilisateur à partir de ce pays**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | L’emplacement géographique, tel que résolu à partir de l’adresse IP, a été utilisé pour la première fois par l’utilisateur pour établir la connexion. | True, False |
| **Pays d’où l’utilisateur s’est connecté de façon inhabituelle**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | L’emplacement géographique, tel que résolu à partir de l’adresse IP, n’est généralement pas utilisé par l’utilisateur pour établir la connexion. | True, False |
| **Pays d’où la connexion est inhabituelle parmi les pairs**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | L’emplacement géographique, tel que résolu à partir de l’adresse IP, n’est généralement pas utilisé parmi les pairs de l’utilisateur pour établir une connexion. | True, False |
| **Première connexion depuis ce pays observée dans le locataire**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | La connexion a été établie pour la première fois depuis ce pays par une personne de l’organisation. | True, False |
| **Pays d’où la connexion est inhabituelle dans le locataire**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | L’emplacement géographique, tel que résolu à partir de l’adresse IP, n’est généralement pas utilisé dans l’organisation pour établir une connexion. | True, False |
| 

#### <a name="device-used-to-connect"></a>Appareil utilisé pour se connecter

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Première connexion de l’utilisateur à partir de l’appareil**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | L’appareil source a été utilisé pour la première fois par l’utilisateur pour se connecter. | True, False |
| **Appareil utilisé de façon inhabituelle par l’utilisateur**<br>*(DeviceUncommonlyUsedByUser)* | 10 | L’appareil n’est généralement pas utilisé par l’utilisateur. | True, False |
| **Appareil utilisé de façon inhabituelle parmi les pairs**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | L’appareil n’est généralement pas utilisé parmi les pairs de l’utilisateur. | True, False |
| **Appareil observé pour la première fois dans le locataire**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | L’appareil a été observé pour la première fois dans l’organisation. | True, False |
| **Appareil utilisé de façon inhabituelle dans le locataire**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | L’appareil n’est généralement pas utilisé dans l’organisation. | True, False |
| 

#### <a name="other-device-related"></a>Autres insights relatifs aux appareils

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Session ouverte pour la première fois par l’utilisateur sur l’appareil**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | L’utilisateur s’est connecté à l’appareil de destination pour la première fois. | True, False |
| **Famille d’appareils utilisée de façon inhabituelle dans le locataire**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | La famille d’appareils n’est généralement pas utilisée dans l’organisation. | True, False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Fournisseur de services Internet utilisé pour la connexion

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Première connexion de l’utilisateur via un ISP**<br>*(FirstTimeUserConnectedViaISP)* | 30 | L’ISP a été observé pour la première fois par l’utilisateur. | True, False |
| **ISP utilisé de façon inhabituelle par l’utilisateur**<br>*(ISPUncommonlyUsedByUser)* | 10 | L’ISP n’est généralement pas utilisé par l’utilisateur. | True, False |
| **ISP utilisé de façon inhabituelle parmi les pairs**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | L’ISP n’est généralement pas utilisé parmi les pairs de l’utilisateur. | True, False |
| **Première connexion via l’ISP dans le locataire**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | L’ISP a été observé pour la première fois dans l’organisation. | True, False |
| **ISP utilisé de façon inhabituelle dans le locataire**<br>*(ISPUncommonlyUsedInTenant)* | 30 | L’ISP n’est généralement pas utilisé dans l’organisation. | True, False |
| 

#### <a name="resource-accessed"></a>Ressource consultée

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Ressource consultée pour la première fois par l’utilisateur**<br>*(FirstTimeUserAccessedResource)* | 180 | La ressource a été consultée pour la première fois par l’utilisateur. | True, False |
| **Ressource consultée de façon inhabituelle par l’utilisateur**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | La ressource n’est généralement pas consultée par l’utilisateur. | True, False |
| **Ressource consultée de façon inhabituelle parmi les pairs**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | La ressource n’est généralement pas consultée parmi les pairs de l’utilisateur. | True, False |
| **Ressource consultée pour la première fois dans le locataire**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | La ressource a été consultée pour la première fois par une personne de l’organisation. | True, False |
| **Ressource consultée de façon inhabituelle dans le locataire**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | La ressource n’est généralement pas consultée dans l’organisation. | True, False |
| 

#### <a name="miscellaneous"></a>Divers

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Action effectuée pour la dernière fois par l’utilisateur**<br>*(LastTimeUserPerformedAction)* | 180 | Dernière fois que l’utilisateur a effectué la même action. | \<Timestamp\> |
| **Une action similaire n’a pas été effectuée dans le passé**<br>*(SimilarActionWasn’tPerformedInThePast)* | 30 | Aucune action dans le même fournisseur de ressources n’a été effectuée par l’utilisateur. | True, False |
| **Emplacement IP source**<br>*(SourceIPLocation)* | *N/A* | Pays résolu à partir de l’adresse IP source de l’action. | [Surrey, Royaume-Uni] |
| **Volume d’opérations inhabituellement élevé**<br>*(UncommonHighVolumeOfOperations)* | 7 | Un utilisateur a effectué une rafale d’opérations similaires au sein du même fournisseur. | True, False |
| **Nombre inhabituel d’échecs de l’accès conditionnel Azure AD**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | Un nombre inhabituel d’utilisateurs n’a pas pu s’authentifier en raison de l’accès conditionnel | True, False |
| **Nombre inhabituel d’appareils ajoutés**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Un utilisateur a ajouté un nombre inhabituel d’appareils. | True, False |
| **Nombre inhabituel d’appareils supprimés**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Un utilisateur a supprimé un nombre inhabituel d’appareils. | True, False |
| **Nombre inhabituel d’utilisateurs ajoutés au groupe**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Un utilisateur a ajouté un nombre d’utilisateurs à un groupe. | True, False |
|


## <a name="identityinfo-table-public-preview"></a>Table IdentityInfo (préversion publique)

Après avoir [activé UEBA](enable-entity-behavior-analytics.md) pour votre espace de travail Microsoft Sentinel, les données de votre instance Azure Active Directory sont synchronisées avec la table **IdentityInfo** dans Log Analytics afin de pouvoir les utiliser dans Microsoft Sentinel. Vous pouvez incorporer des données utilisateur synchronisées à partir de votre Azure AD dans vos règles d’analyse pour améliorer vos analyses en fonction de vos cas d’usage, et réduire les faux positifs.

Alors que la synchronisation initiale peut prendre quelques jours, une fois les données entièrement synchronisées :

- Les modifications apportées à vos profils utilisateur dans Azure AD sont mis à jour dans la table **IdentityInfo** dans un délai de 15 minutes.

- Les informations de groupes et de rôles sont synchronisées entre la table **IdentityInfo** et Azure AD tous les jours.

- Tous les 21 jours, Microsoft Sentinel se resynchronise avec l’ensemble de votre Azure AD pour s’assurer que les anciens enregistrements sont entièrement mis à jour.

- La durée de rétention par défaut dans la table **IdentityInfo** est de 30 jours.


> [!NOTE]
> Actuellement, seuls les rôles intégrés sont pris en charge.
>
> Les données relatives aux groupes supprimés, où un utilisateur a été supprimé d’un groupe, ne sont pas prises en charge actuellement.
>

Le tableau suivant décrit les données d’identité d’utilisateur incluses dans la table **IdentityInfo** de Log Analytics.

| Champ                      | Type     | Description                                                                                                             |
| --------------------------- | -------- | -------------------------------------------------------- |
| **AccountCloudSID**             | string   | Identificateur de sécurité Azure AD du compte.       |
| **AccountCreationTime**         | DATETIME | Date à laquelle le compte d’utilisateur a été créé (UTC).    |
| **AccountDisplayName**          | string   | Nom d’affichage du compte d’utilisateur.           |
| **AccountDomain**               | string   | Nom de domaine du compte d’utilisateur.  |
| **AccountName**                 | string   | Nom d’utilisateur du compte d’utilisateur.      |
| **AccountObjectId**             | string   | ID de l’objet Azure Active Directory pour le compte d’utilisateur.           |
| **AccountSID**                  | string   | Identificateur de sécurité local du compte d’utilisateur.      |
| **AccountTenantId**             | string   | ID de locataire Azure Active Directory du compte d’utilisateur.    |
| **AccountUPN**                  | string   | Nom d’utilisateur principal du compte d’utilisateur.     |
| **AdditionalMailAddresses**     | dynamique  | Adresses e-mail supplémentaires de l’utilisateur.   |
| **AssignedRoles**               | dynamique  | Rôles Azure AD auxquels le compte d’utilisateur est affecté.    |
| **Ville**                        | string   | Ville du compte d’utilisateur.   |
| **Pays**                     | string   | Pays du compte d’utilisateur.   |
| **DeletedDateTime**             | DATETIME | Date et heure de suppression de l’utilisateur.       |
| **Département**                  | string   | Département du compte d’utilisateur.    |
| **GivenName**                   | string   | Prénom du compte d’utilisateur.     |
| **GroupMembership**             | dynamique  | Groupes Azure AD dont le compte d’utilisateur est membre.      |
| **IsAccountEnabled**            | bool     | Indique si le compte d’utilisateur est activé ou non dans Azure AD.    |
| **JobTitle**                    | string   | Poste occupé par le compte d’utilisateur.    |
| **MailAddress**                 | string   | Adresse e-mail principale du compte d’utilisateur.    |
| **Manager**                     | string   | Alias du responsable du compte d’utilisateur.     |
| **OnPremisesDistinguishedName** | string   | Nom unique (DN) Azure AD. Un nom unique est une séquence de noms uniques relatifs (RDN), connectés par des virgules. |
| **Téléphone**                       | string   | Numéro de téléphone du compte d’utilisateur.       |
| **SourceSystem**                | string   | Système d’où proviennent les données utilisateur.   |
| **State**                       | string   | État géographique du compte d’utilisateur.  |
| **StreetAddress**               | string   | Adresse postale du bureau du compte d’utilisateur.    |
| **Surname**                     | string   | Nom de l’utilisateur. compte.      |
| **TenantId**                    | string   |          ID du locataire de l’utilisateur   |
| **TimeGenerated**               | DATETIME | Heure à laquelle l’événement a été généré (UTC).       |
| **Type**                        | string   | Nom de la table.          |
| **UserState**                   | string   | État actuel du compte d’utilisateur dans Azure AD (actif/désactivé/dormant/verrouillé).  |
| **UserStateChangedOn**          | DATETIME | Date de la dernière modification de l’état du compte (UTC).     |
| **UserType**                    | string   | Type d’utilisateur.         |


## <a name="next-steps"></a>Étapes suivantes

Dans ce document, nous avons décrit le schéma de la table d’analyse du comportement des entités Microsoft Sentinel.

- En savoir plus sur [l’analyse du comportement des entités](identify-threats-with-entity-behavior-analytics.md).
- [Mise en pratique d’UEBA](investigate-with-ueba.md) dans les examens.
