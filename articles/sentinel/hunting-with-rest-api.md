---
title: Gérer les requêtes de chasse et de diffusion en continu dans Microsoft Sentinel à l’aide de l’API REST | Microsoft Docs
description: Cet article explique comment les fonctionnalités de chasse Microsoft Sentinel vous permettent de tirer parti de l’API REST de Log Analytics pour gérer les requêtes de chasse et de diffusion en continu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 0c12c96e5a9cb8fb532cdd8ad462ff3d6a5f0bfc
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520500"
---
# <a name="manage-hunting-and-livestream-queries-in-microsoft-sentinel-using-rest-api"></a>Gérer les requêtes de chasse et de diffusion en continu dans Microsoft Azure Sentinel à l’aide de l’API REST

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel, construit en partie sur Azure Monitor Log Analytics, vous permet d’utiliser l’API REST de Log Analytics pour gérer les requêtes de chasse et de diffusion en continu. Ce document explique les procédures de création et de gestion des requêtes de chasse à l’aide de l’API REST.  Les requêtes créées de cette façon sont affichées dans l’interface utilisateur de Microsoft Sentinel.

Consultez la référence de l’API REST définitive pour plus de détails sur les [API de recherche enregistrées](/rest/api/loganalytics/savedsearches).

## <a name="api-examples"></a>Exemples d'API

Dans les exemples suivants, remplacez ces espaces réservés par l’élément de remplacement prescrit dans le tableau suivant :

| Espace réservé | Remplacer par |
|-|-|
| **{subscriptionId}** | nom de l’abonnement auquel vous appliquez la requête de chasse ou de diffusion en continu. |
| **{resourceGroupName}** | nom du groupe de ressources auquel vous appliquez la requête de chasse ou de diffusion en continu. |
| **{savedSearchId}** | ID unique (GUID) pour chaque requête de chasse. |
| **{WorkspaceName}** | nom de l’espace de travail Log Analytics qui est la cible de la requête. |
| **{DisplayName}** | nom d'affichage de votre choix pour la requête. |
| **{Description}** | description de la requête de chasse ou de diffusion en continu. |
| **{Tactics}** | tactique MITRE ATT&CK pertinente qui s’applique à la requête. |
| **{Query}** | expression de requête pour votre requête. |
|  

### <a name="example-1"></a>Exemple 1

Cet exemple explique la procédure de création ou de mise à jour d’une requête de chasse pour un espace de travail Microsoft Sentinel donné.  Pour une requête de diffusion en continu, remplacez *« Catégorie » : « requêtes de chasse »* par *« Catégorie » : « requêtes de diffusion en continu »* dans le **corps de la demande** : 

#### <a name="request-header"></a>En-tête de requête

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Corps de la demande

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Exemple 2

Cet exemple explique la procédure de suppression d’une requête de chasse ou de diffusion en continu pour un espace de travail Microsoft Sentinel donné :

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Exemple 3

Cet exemple explique la procédure de récupération d’une requête de chasse ou de diffusion en continu pour un espace de travail donné :

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à gérer les requêtes de chasse et de diffusion en continu dans Microsoft Sentinel à l’aide de l’API Log Analytics. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- [Hunt for threats with in Azure Sentinel Preview](hunting.md) (Rechercher des menaces dans Azure Sentinel en préversion)
- [Use notebooks to run automated hunting campaigns](notebooks.md) (Utiliser des notebooks pour exécuter des campagnes de repérage automatisées)
