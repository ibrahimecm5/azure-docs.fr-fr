---
title: Enrichir des entités avec des données de géolocalisation dans Microsoft Sentinel à l’aide d’une API REST | Microsoft Docs
description: Cet article explique comment enrichir des entités dans Microsoft Sentinel avec des données de géolocalisation via une API REST.
services: sentinel
documentationcenter: na
author: batamig
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
ms.author: bagol
ms.openlocfilehash: cce16ca2f0db3d4f44c4077e4760f599e565bae6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520595"
---
# <a name="enrich-entities-in-microsoft-sentinel-with-geolocation-data-via-rest-api-public-preview"></a>Enrichir des entités dans Microsoft Sentinel avec des données de géolocalisation via une API REST (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article explique comment enrichir des entités dans Microsoft Sentinel avec des données de géolocalisation à l’aide d’une API REST.

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="common-uri-parameters"></a>Paramètres URI communs

Voici les paramètres d’URI communs pour l’API de géolocalisation :




| Nom | Dans | Obligatoire | Type | Description |
|-|-|-|-|-|
| **{subscriptionId}** | path | yes | GUID | ID d’abonnement Azure |
| **{resourceGroupName}** | path | yes | string | Nom du groupe de ressources dans l’abonnement |
| **{api-version}** | query | yes | string | Version du protocole utilisé pour faire cette demande. Depuis le 30 2021 avril, la version de l’API de géolocalisation est *2019-01-01-preview*.|
| **{ipAddress}** | query | yes | string | Adresse IP pour laquelle les informations de géolocalisation sont nécessaires, au format IPv4 ou IPv6.   |
|

## <a name="enrich-ip-address-with-geolocation-information"></a>Enrichir une adresse IP avec des informations de géolocalisation

Cette commande extrait les données de géolocalisation pour une adresse IP donnée.

### <a name="request-uri"></a>URI de demande

| Méthode | URI de requête |
|-|-|
| **GET** | `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.SecurityInsights/enrichment/ip/geodata/?ipaddress={ipAddress}&api-version={api-version}` |
|

### <a name="responses"></a>Réponses

|Code d’état  |Description  |
|---------|---------|
|**200**     |   Succès      |
|**400**     |      Adresse IP non fournie ou de format non valide    |
|**404**     | Données de géolocalisation introuvables pour cette adresse IP         |
|**429**     |      Trop de demandes, réessayer dans le délai spécifié    |
|     |         |

### <a name="fields-returned-in-the-response"></a>Champs retournés dans la réponse

|Nom du champ  |Description  |
|---------|---------|
|**ASN**     |  Numéro de système autonome associé à cette adresse IP       |
|**carrier**     |  Nom de l’opérateur pour cette adresse IP       |
|**city**     |   Ville dans laquelle se trouve cette adresse IP      |
|**cityCf**     | Évaluation de confiance numérique dans le fait que la valeur figurant dans le champ « city » est correcte, sur une échelle de 0 à 100        |
|**continent**     | Continent dans lequel se trouve cette adresse IP        |
|**country**     |Pays dans lequel se trouve cette adresse IP        |
|**countryCf**     |   Évaluation de confiance numérique dans le fait que la valeur figurant dans le champ « country » est correcte, sur une échelle de 0 à 100      |
|**IpAddr**     |   Représentation de chaîne avec point décimal ou deux-points de séparation de l’adresse IP      |
|**ipRoutingType**     |   Description du type de connexion pour cette adresse IP      |
|**latitude**     |     Latitude correspondant à cette adresse IP    |
|**longitude**     |  Latitude correspondant à cette adresse IP       |
|**organization**     |  Nom de l’organisation correspondant à cette adresse IP       |
|**organizationType**     | Type de l’organisation correspondant à cette adresse IP        |
|**region**     |    Région géographique où se trouve cette adresse IP     |
|**state**     |  État dans lequel se trouve cette adresse IP       |
|**stateCf**     | Évaluation de confiance numérique dans le fait que la valeur figurant dans le champ « state » est correcte, sur une échelle de 0 à 100        |
|**stateCode**     |   Nom abrégé de l’État dans lequel dans lequel se trouve cette adresse IP      |
|     |         |


## <a name="throttling-limits-for-the-api"></a>Seuils de limitation pour l’API

Cette API a une limite de 100 appels, par utilisateur, par heure.

### <a name="sample-response"></a>Exemple de réponse

```rest
"body":
{
    "asn": "12345",
    "carrier": "Microsoft",
    "city": "Redmond",
    "cityCf": 90,
    "continent": "north america",
    "country": "united states",
    "countryCf": 99
    "ipAddr": "1.2.3.4",
    "ipRoutingType": "fixed",
    "latitude": "40.2436",
    "longitude": "-100.8891",
    "organization": "Microsoft",
    "organizationType": "tech",
    "region": "western usa",
    "state": "washington",
    "stateCf": null
    "stateCode": "wa"
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- En savoir plus sur les entités :

    - [Informations de référence sur les types d’entités Microsoft Sentinel](entities-reference.md)
    - [Classer et analyser les données à l'aide d'entités dans Microsoft Sentinel](entities-in-azure-sentinel.md)
    - [Mapper des champs de données à des entités dans Microsoft Sentinel](map-data-fields-to-entities.md)

- Explorer d’autres utilisations de l’[API Microsoft Sentinel](/rest/api/securityinsights/)
