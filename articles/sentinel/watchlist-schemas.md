---
title: Schémas pour les modèles Watchlist Microsoft Sentinel | Microsoft Docs
description: En savoir plus sur les schémas utilisés dans chaque modèle Watchlist intégré dans Microsoft Sentinel.
author: batamig
ms.author: bagold
ms.service: microsoft-sentinel
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: 0d322f7412e32de0c668c3dd82341da81eb12738
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516890"
---
# <a name="microsoft-sentinel-built-in-watchlist-template-schemas-public-preview"></a>Schémas de modèle Watchlist Microsoft Sentinel intégrés (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article détaille les schémas utilisés dans chaque modèle Watchlist intégré fourni par Microsoft Sentinel. Pour plus d’informations, consultez [créer un nouveau Watchlist à l’aide d’un modèle (version préliminaire publique)](watchlists.md#create-a-new-watchlist-using-a-template-public-preview).

> [!IMPORTANT]
> Les modèles Microsoft Sentinel Watchlist sont actuellement en préversion. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>


## <a name="high-value-assets"></a>Ressources de valeur élevée

La Watchlist des Ressources de valeur élevée répertorie les appareils, les ressources et les autres ressources qui ont une valeur critique dans l’organisation, et comprend les champs suivants :

| Nom du champ | Format                              | Exemple                                                                                                                                | Obligatoire/facultatif |
| ---------- | ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| **Type de ressource** | Chaîne                              | `Device`, `Azure resource`, `AWS resource`, `URL`, `SPO`, `File share`, `Other`                                                                      | Obligatoire          |
| **ID de la ressource**   | Chaîne, selon le type de ressource | `/subscriptions/d1d8779d-38d7-4f06-91db-9cbc8de0176f/resourceGroups/SOC-Purview/providers/Microsoft.Storage/storageAccounts/purviewadls` | Obligatoire          |
| **Nom de la ressource** | String                              | `Microsoft.Storage/storageAccounts/purviewadls`                                                                                          | Facultatif           |
| **Nom de domaine complet de la ressource** | FQDN                                | `Finance-SRv.local.microsoft.com`                                                                                                        | Obligatoire          |
| **Adresse IP** | IP                                  | `1.1.1.1`                                                                                                                                | Facultatif           |
| **Balises**       | List                                | `["SAW user","Blue Ocean team"] `                                                                                                        | Facultatif           |
| | | | |

## <a name="vip-users"></a>Utilisateurs d’adresses IP virtuelles

La Watchlist Utilisateurs d’adresses IP virtuelles répertorie les comptes utilisateurs des employés ayant une valeur à fort impact au sein de l’organisation, et comprend les valeurs suivantes :

| Nom du champ          | Format | Exemple                                             | Obligatoire/facultatif |
| ------------------- | ------ | --------------------------------------------------- | ------------------ |
| **Identificateur d’utilisateur**     | Identificateur d’utilisateur    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | Facultatif           |
| **ID d’objet AAD de l’utilisateur**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | Facultatif           |
| **Sid local de l’utilisateur**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Facultatif           |
| **Nom d'utilisateur principal** | UPN    | `JeffL@seccxp.ninja`                                  | Obligatoire          |
| **Balises**                | List   | `["SAW user","Blue Ocean team"]`                      | Facultatif           |
| | | | |

## <a name="network-mapping"></a>Mappage réseau

La Watchlist du mappage réseau répertorie les sous-réseaux IP et leurs contextes d’organisation respectifs, et comprend les champs suivants :

| Nom du champ | Format       | Exemple                      | Obligatoire/facultatif |
| ---------- | ------------ | ---------------------------- | ------------------ |
| **Sous-réseau IP**  | Plage de sous-réseau |` 198.51.100.0/24 - 198….../22` | Obligatoire          |
| **Nom de la plage** | String       | `DMZ`                          | Facultatif           |
| **Balises**       | List         | `["Example","Example"]`        | Facultatif           |
| | | | |

## <a name="terminated-employees"></a>Employés arrêtés

La watchlist Employés terminés répertorie les comptes d’utilisateurs des employés qui ont été ou qui sont sur le point d’être terminés, et comprend les champs suivants :

| Nom du champ          | Format                                                                          | Exemple                              | Obligatoire/facultatif |
| ------------------- | ------------------------------------------------------------------------------- | ------------------------------------ | ------------------ |
| **Identificateur d’utilisateur**     | Identificateur d’utilisateur                                                                             | `52322ec8-6ebf-11eb-9439-0242ac130002` | Facultatif           |
| **ID d’objet AAD de l’utilisateur**  | SID                                                                             | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e` | Facultatif           |
| **Sid local de l’utilisateur**    | SID                                                                             | `S-1-12-1-4141952679-1282074057-123`   | Facultatif           |
| **Nom d'utilisateur principal** | UPN                                                                             | `JeffL@seccxp.ninja`                  | Obligatoire          |
| **UserState**           | Chaîne <br><br>Nous vous recommandons d’utiliser soit `Notified` soit`Terminated` | `Terminated`                           | Obligatoire          |
| **Date de notification**  | Horodatage - jour                                                                 | `01.12.20`                             | Facultatif           |
| **Date de fin**    | Horodatage - jour                                                                 | `01.01.21`                            | Obligatoire          |
| **Balises**                | List                                                                            | `["SAW user","Amba Wolfs team"]`       | Facultatif           |
| | | | |


## <a name="identity-correlation"></a>Corrélation d’identité

La Watchlist de corrélation des identités répertorie les comptes d’utilisateurs associés qui appartiennent à la même personne et comprend les champs suivants :

| Nom du champ                       | Format  | Exemple                                             | Obligatoire/facultatif |
| -------------------------------- | ------- | --------------------------------------------------- | ------------------ |
| **Identificateur d’utilisateur**                  | Identificateur d’utilisateur     | `52322ec8-6ebf-11eb-9439-0242ac130002`                | Facultatif           |
| **ID d’objet AAD de l’utilisateur**               | SID     | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | Facultatif           |
| **Sid local de l’utilisateur**                 | SID     | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Facultatif           |
| **Nom d'utilisateur principal**              | UPN     | `JeffL@seccxp.ninja`                                  | Obligatoire          |
| **ID d’employé**                      | String  | `8234123`                                             | Facultatif           |
| **Courrier électronique**                            | Courrier   | `JeffL@seccxp.ninja`                                  | Facultatif           |
| **ID de Compte privilégié associé** | UID/SID | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Facultatif           |
| **Compte privilégié associé**    | UPN     | `Admin@seccxp.ninja`                                  | Facultatif           |
| **Balises**                             | List    | `["SAW user","Amba Wolfs team"]`                      | Facultatif           |
| | | | |

## <a name="service-accounts"></a>Comptes de service

La watchlist de Comptes de service répertorie les comptes de service et leurs propriétaires, et comprend les champs suivants :

| Nom du champ                | Format | Exemple                                             | Obligatoire/facultatif |
| ------------------------- | ------ | --------------------------------------------------- | ------------------ |
| **Identificateur du service**        | Identificateur d’utilisateur    | `1111-112123-12312312-123123123`                      | Facultatif           |
| **ID d’objet AAD du service**     | SID    | `11123-123123-123123-123123`                          | Facultatif           |
| **SID de service local**       | SID    | `S-1-12-1-3123123-123213123-12312312-2916039507`      | Facultatif           |
| **Nom du principal de service**    | UPN    | `myserviceprin@contoso.com`                           | Obligatoire          |
| **Identificateur de l'utilisateur propriétaire**     | Identificateur d’utilisateur    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | Facultatif           |
| **ID d’objet AAD de l’utilisateur propriétaire**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | Facultatif           |
| **Sid local de l’utilisateur propriétaire**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Facultatif           |
| **Nom principal de l’utilisateur propriétaire** | UPN    | `JeffL@seccxp.ninja`                                  | Obligatoire          |
| **Balises**                      | List   | `["Automation Account","GitHub Account"]`             | Facultatif           |
| | | | |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Utilisation des watchlists Microsoft Sentinel](watchlists.md).
