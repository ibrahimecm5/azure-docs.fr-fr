---
title: Exemples de modèles Azure Resource Manager - Azure Front Door
description: Apprenez-en davantage sur les exemples de modèles Resource Manager pour Azure Front Door, notamment les modèles pour la création d’une instance Front Door de base et la configuration de la limitation du débit Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/01/2021
ms.author: duau
ms.openlocfilehash: db0bf4783de324af2ab4e5a5ec6bfbaa18ad33cf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449770"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Modèles de déploiement Azure Resource Manager pour Front Door

Le tableau suivant comprend des liens vers des modèles de déploiement Azure Resource Manager pour Azure Front Door.

## <a name="azure-front-door"></a>Azure Front Door

| Modèle | Description |
| ---| ---|
| [Créer une porte d’entrée de base](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-basic)| Crée une configuration de porte d’entrée de base avec un seul backend. |
| [Créer une porte d’entrée avec plusieurs backends et pools de backends, et un routage basé sur les URL](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-multiple-backends)| Crée une porte d’entrée avec l’équilibrage de charge configuré pour plusieurs backends du pool de backends, ainsi qu’entre des pools de backends, basé sur les chemins d’URL. |
| [Intégrer un domaine personnalisé et un certificat TLS managé avec Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain)| Ajoutez un domaine personnalisé à votre instance Front Door et utilisez un certificat TLS managé par Front Door. |
| [Intégrer un domaine personnalisé et un certificat TLS géré par le client avec Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain-customer-certificate)| Ajoutez un domaine personnalisé à votre instance Front Door et utilisez votre propre certificat TLS à l’aide de Key Vault. |
| [Créer une porte d’entrée avec filtrage géographique](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-geo-filtering)| Créer une porte d’entrée qui autorise/bloque le trafic provenant de certains pays et de certaines régions. |
| [Contrôler les sondes d’intégrité pour vos backends sur Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-health-probes)| Mettez à jour votre porte d’entrée pour modifier les paramètres de sonde d’intégrité en changeant le chemin des sondes, ainsi que les intervalles auxquels les sondes sont envoyées. |
| [Créer une porte d’entrée avec une configuration de backend Active/En veille](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-priority-lb)| Crée une porte d’entrée qui montre le routage en fonction de la priorité pour la topologie d’application Actif/En veille, c’est-à-dire qui envoie par défaut tout le trafic au backend principal (priorité la plus élevée) jusqu’à ce qu’il devienne indisponible. |
| [Créer une porte d’entrée avec la mise en cache activée pour certaines routes](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-caching)| Crée une porte d’entrée avec la mise en cache activée pour la configuration de routage définie, ce qui a pour effet de mettre en cache les ressources statiques pour votre charge de travail. |
| [Configurer l’affinité de session pour les noms d’hôte de votre porte d’entrée](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-session-affinity) | Met à jour une porte d’entrée pour activer l’affinité de session pour l’hôte de votre frontend, envoyant ainsi le trafic résultant de la même session utilisateur au même backend. |
| [Configurer Front Door pour le placement en liste verte ou rouge d’adresses IP clientes](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-clientip)| Configure une porte d’entrée pour limiter le trafic de certaines adresses IP avec un contrôle d’accès personnalisé basé sur les adresses IP clientes. |
| [Configurer Front Door pour effectuer une action avec des paramètres HTTP spécifiques](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-http-params)| Configure une porte d’entrée pour autoriser ou bloquer un certain trafic en fonction des paramètres HTTP de la requête entrante, avec des règles personnalisées pour le contrôle d’accès en fonction de paramètres HTTP. |
| [Configurer la limitation du débit de Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-rate-limiting)| Configure une porte d’entrée pour limiter le débit du trafic entrant pour un hôte frontend donné. |
| | |

## <a name="azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (préversion)

| Exemple | Description |
|-|-|
| [Front Door (création rapide)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium/) | Crée un profil Front Door de base incluant un point de terminaison, un groupe d’origine, une origine et une route.  |
| [Ensemble de règles](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rule-set/) | Crée un profil et un ensemble de règles Front Door.  |
| [Stratégie WAF avec l’ensemble de règles managées](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-waf-managed/) | Crée un profil Front Door et une stratégie WAF avec un ensemble de règles managées.  |
| [Stratégie WAF avec une règle personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-waf-custom/) | Crée un profil Front Door et une stratégie WAF avec une règle personnalisée.  |
| [Stratégie WAF avec limite de débit](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rate-limit/) | Crée un profil Front Door et un pare-feu d’applications web avec une règle personnalisée pour limiter le débit.  |
| [Stratégie WAF avec géofiltrage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-geo-filtering/) | Crée un profil Front Door et un pare-feu d’applications web avec une règle personnalisée pour appliquer un géofiltrage.  |
|**Origines App Service**| **Description** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-app-service-public) | Crée une application App Service avec un point de terminaison public et un profil Front Door.  |
| [App Service avec Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-app-service-private-link) | Crée une application App Service avec un point de terminaison privé et un profil Front Door.  |
|**Origines Azure Functions**| **Description** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-function-public/) | Crée une application Azure Functions avec un point de terminaison public et un profil Front Door.  |
| [Azure Functions avec Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-function-private-link) | Crée une application Azure Functions avec un point de terminaison privé et un profil Front Door.  |
|**Origines API Management**| **Description** |
| [Gestion des API (externe)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-api-management-external) | Crée une instance de Gestion des API avec une intégration au réseau virtuel VNet externe et un profil Front Door.  |
|**Origines Stockage**| **Description** |
| [Site web statique de stockage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-storage-static-website) | Crée un compte Stockage Azure et un site web statique avec un point de terminaison public et un profil Front Door.  |
| [Objets blob de stockage avec Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-storage-blobs-private-link) | Crée un compte Stockage Azure et un conteneur d’objets blob avec un point de terminaison privé et un profil Front Door.  |
|**Origines Application Gateway**| **Description** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-application-gateway-public) | Crée une passerelle applicative et un profil Front Door. |
|**Origines Machine virtuelle**| **Description** |
| [Machine virtuelle avec service Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-vm-private-link) | Crée une machine virtuelle et un service Private Link ainsi qu’un profil Front Door. |
| | |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
