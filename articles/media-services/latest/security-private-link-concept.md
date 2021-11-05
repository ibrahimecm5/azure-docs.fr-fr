---
title: Vue d’ensemble de l’utilisation des liaisons privées avec Azure Media Services
description: Cet article propose une vue d’ensemble de l’utilisation des liens privés avec Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 10d27003cfa5300e097c2328deb14c0d9c1f7e93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096700"
---
# <a name="overview-of-using-azure-private-link-with-azure-media-services"></a>Vue d’ensemble de l’utilisation d’Azure Private Link avec Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article propose une vue d’ensemble de l’utilisation des liens privés avec Azure Media Services.

## <a name="when-to-use-private-link-with-media-services"></a>Quand utiliser Private Link avec Media Services

Private Link permet d’accéder à Media Services à partir de réseaux privés. Lorsqu’elles sont utilisées avec les contrôles d’accès réseau fournis par Media Services, les liaisons privées peuvent permettre l’utilisation de Media Services sans exposer les points de terminaison à l’internet public.

## <a name="azure-private-endpoint-and-azure-private-link"></a>Point de terminaison privé Azure et Azure Private Link

Un [point de terminaison privé](/private-link/private-endpoint-overview) est une interface réseau qui utilise une adresse IP privée de votre réseau virtuel.  Cette interface réseau vous connecte de manière privée et sécurisée à un service via Azure Private Link.

Les points de terminaison Media Services sont accessibles à partir d’un réseau virtuel à l’aide de points de terminaison privés. Il est également possible d’accéder aux points de terminaison privés à partir de réseaux virtuels homologués ou d’autres réseaux connectés au réseau virtuel à l’aide d’Express Route ou VPN.

[Azure Private Link](/private-link/) permet d’accéder aux points de terminaison privés Media Services de votre réseau virtuel sans les exposer à l’Internet public. Il achemine le trafic sur le réseau principal de Microsoft.

## <a name="restricting-access"></a>Restriction de l’accès

> [!Important]
> La création d’un point de terminaison privé ne désactive **PAS** implicitement l’accès Internet à celui-ci.

L’accès Internet aux points de terminaison dans le compte Media Services peut être restreint de l’une des deux manières suivantes :

- restriction de l’accès à toutes les ressources au sein du compte Media Services
- restriction de l’accès séparément pour chaque ressource à l’aide de la liste d'adresses IP autorisées.

## <a name="media-services-endpoints"></a>Points de terminaison de Media Services

| Point de terminaison                    | Description                                                               | Prend en charge la liaison privée | Contrôle d’accès à Internet |
| --------------------------- | ------------------------------------------------------------------------- | --------------------- | ----------------------- |
| Point de terminaison de streaming          | Le serveur d’origine pour la diffusion vidéo en continue et les médias de formatage en TLS et DASH | Oui                   | Liste verte d’adresses IP            |
| Point de terminaison de streaming avec CDN | Diffuser des médias vers de nombreux visionneuses                                              | Non                    | Géré par CDN          |
| Remise de clés                | Fournit des clés de contenu multimédia et des licences DRM aux visionneuses de médias             | Oui                   | Liste verte d’adresses IP            |
| événement en temps réel                  | Ingère le contenu multimédia pour le streaming en direct                                  | Oui                   | Liste verte d’adresses IP            |

> [!NOTE]
> Les comptes Media Services créés avec des versions d’API antérieures à 2020-05-01 ont également un point de terminaison pour le point de terminaison de l’API RESTv2 héritée (dépréciation en attente).  Ce point de terminaison ne prend pas en charge les liaisons privées.

## <a name="other-private-link-enabled-azure-services"></a>Autres services Azure activés pour les liaisons privées

| Service                | Intégration de Media Services                      | Documentation sur les liaisons privées |
| ---------------------- | ----------------------------------------------- | -------------------------- |
| Stockage Azure          | Utilisé pour stocker les médias                             | [Utiliser des points de terminaison privés pour Stockage Azure](/storage/common/storage-private-endpoints) |
| Azure Key Vault        | Utilisé pour stocker des [clés managées par le client](security-customer-managed-keys-portal-tutorial.md)             | [Configurer les paramètres de mise en réseau Azure Key Vault](/key-vault/general/how-to-azure-key-vault-network-security) |
| Azure Resource Manager | Fournit l’accès aux API Media Services          | [Utiliser une API REST pour créer une liaison privée pour la gestion de ressources Azure](/azure-resource-manager/management/create-private-link-access-rest) |
| Event Grid             | Fournit des [notifications sur des événements Media Services](./monitoring/job-state-events-cli-how-to.md) | [Configurer des points de terminaison privés pour les rubriques ou les domaines Azure Event Grid](/event-grid/configure-private-endpoints)  |

## <a name="private-endpoints-are-created-on-the-media-services-account"></a>Les points de terminaison privés sont créés sur le compte Media Services

Les points de terminaison privés pour la livraison de clés, les points de terminaison de Streaming et les événements en direct sont créés sur le compte Media Services au lieu d’être créés individuellement.

Une adresse IP privée est créée pour chaque point de terminaison de Streaming ou événement en direct dans le compte Media Services lorsqu’une ressource de point de terminaison privée Media Services est créée. Par exemple, si vous avez deux points de terminaison de streaming démarrés, un seul point de terminaison privé doit être créé pour connecter les deux points de terminaison de streaming à un réseau virtuel. Les ressources peuvent être connectées à plusieurs réseaux virtuels en même temps.

L’accès Internet au compte de Media Services doit être restreint, pour toutes les ressources au sein du compte ou séparément pour chaque ressource.

## <a name="private-link-pricing"></a>Tarification de Private Link
Pour plus d’informations sur les tarifs, consultez [Tarification d’Azure Private Link](https://azure.microsoft.com/pricing/details/private-link)

## <a name="private-link-how-tos-and-faqs"></a>Procédures Private Link et FAQ

- [Créer un compte Media Services et de stockage avec une liaison privée à l’aide d’un modèle Azure Resource Management](security-private-link-arm-how-to.md)
- [Créer une liaison privée pour un point de terminaison de streaming](security-private-link-streaming-endpoint-how-to.md)