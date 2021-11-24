---
title: Prise en charge des zones de disponibilité pour App Service mutualisé public
description: Découvrez comment déployer votre App Service de façon à ce que vos applications soient redondantes dans une zone.
author: seligj95
ms.topic: article
ms.date: 11/16/2021
ms.author: jordanselig
ms.custom: references_regions
ms.openlocfilehash: e0388695708e8ab3c47ea8049038fb95384dabd2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551290"
---
# <a name="availability-zone-support-for-public-multi-tenant-app-service"></a>Prise en charge des zones de disponibilité pour App Service mutualisé public

Microsoft Azure App Service peut être déployé dans des [zones de disponibilité (AZ)](../availability-zones/az-overview.md) pour vous aider à assurer la résilience et la fiabilité de vos charges de travail vitales pour l’entreprise. Cette architecture est également appelée « redondance de zone ».

Une application réside dans un plan App Service (ASP) qui existe dans une seule unité d’échelle. Quand un App Service est configuré pour être redondant dans une zone, la plateforme répartit automatiquement les instances de machine virtuelle dans le plan App Service entre les trois zones de la région sélectionnée. Si une capacité supérieure à trois est spécifiée et que le nombre d’instances est divisible par trois, les instances sont réparties uniformément. Dans le cas contraire, les instances au-delà de 3*N sont réparties entre les une ou deux zones restantes. Pour les services d’application qui ne sont pas configurés pour être redondants dans une zone, les instances de machine virtuelle sont placées dans une seule zone de la région sélectionnée.

## <a name="requirements"></a>Spécifications

La redondance de zone est une propriété du plan App Service. Les exigences/limitations actuelles relatives à l’activation de la redondance de zone sont les suivantes :

- Les systèmes Windows et Linux sont tous deux pris en charge.
- Requiert des plans App Service **Premium v2** ou **Premium v3**.
- Nombre minimal de trois instances.
  - La plateforme appliquera ce nombre minimal en coulisse si vous spécifiez un nombre d’instances inférieur à trois.
- Peut être activé dans n’importe laquelle des régions suivantes :
  - USA Ouest 2
  - USA Ouest 3
  - USA Centre
  - USA Est
  - USA Est 2
  - Centre du Canada
  - Brésil Sud
  - Europe Nord
  - Europe Ouest
  - Allemagne Centre-Ouest
  - France Centre
  - Sud du Royaume-Uni
  - Japon Est
  - Asie Sud-Est
  - Australie Est
- La redondance de zone ne peut être spécifiée que lors de la création d’un **nouveau** plan App service
  - Actuellement, vous ne pouvez pas convertir un plan de App Service préexistant. Pour plus d’informations sur la création d’un plan App Service prenant en charge la redondance de zone, consultez la section suivante.
- La redondance de zone n’est prise en charge que dans la partie plus récente de l’empreinte App Service
  - Actuellement, si vous opérez sur Pv3, il se peut que vous ayez déjà une empreinte prenant en charge la redondance de zone. Dans ce scénario, vous pouvez créer un plan de App Service et spécifier la redondance de zone lors de la création du nouveau plan App Service.
  - Si vous n’utilisez pas Pv3 ou une unité d’échelle prenant en charge la redondance de zone, si vous êtes dans une région non prise en charge ou si vous n’êtes pas sûr, procédez comme suit :
    - Créez un nouveau groupe de ressources dans une région prise en charge.
        - Cela garantit que le plan de contrôle App Service peut trouver une unité d’échelle dans la région sélectionnée, prenant en charge la redondance de zone.
    - Créez un plan App Service (et une application) dans une région de votre choix à l’aide du **nouveau** groupe de ressources.
    - Vérifiez que la propriété zoneRedundant (décrite ci-dessous) a la valeur true lors de la création du nouveau plan App Service
- La création doit être réalisée à l’aide de [modèles Azure Resource Manager (ARM)](../azure-resource-manager/templates/overview.md).

En cas de défaillance d’une zone, la plateforme App Service détecte les instances perdues et tente automatiquement de trouver de nouvelles instances de remplacement. Si vous avez également configuré la mise à l’échelle automatique et si celle-ci décide que davantage d’instances sont nécessaires, la mise à l’échelle automatique adresse également à App Service une demande d’ajout d’instances (le comportement de la mise à l’échelle automatique est indépendant du comportement de la plateforme App Service). Il est important de noter qu’il n’existe aucune garantie que les demandes d’instances supplémentaires dans un scénario de zone défaillante aboutiront, car le remplissage des instances perdues se produit dans la mesure du possible. La solution recommandée consiste à approvisionner vos plans App Service pour tenir compte de la perte d’une zone, comme décrit dans la section suivante de cet article.

Les applications déployées dans un plan App Service activé pour la redondance de zone continuent à s’exécuter et à traiter le trafic même si d’autres zones dans la même région subissent une interruption. Cependant, il se peut que des comportements hors runtime, dont la mise à l’échelle de plan App Service, la création d’application, la configuration d’application et la publication d’application soient affectés par une interruption dans d’autres zones de disponibilité. La redondance de zone pour les plans App Service garantit uniquement un temps d’activité continu des applications déployées.

Lorsque la plateforme App Service alloue des instances à un plan App Service redondant dans une zone, elle utilise [le meilleur équilibrage de zone possible qu’offre les groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing). Un plan App Service est « équilibré » si chaque zone compte le même nombre de machines virtuelles, ou +/- 1 machine virtuelle dans toutes les autres zones que le plan App Service utilise.

## <a name="how-to-deploy-a-zone-redundant-app-service"></a>Comment déployer une App Service redondante dans une zone

Actuellement, vous devez utiliser un modèle ARM pour créer un App Service redondant dans une zone. Une fois le plan App Service créé via un modèle ARM, vous pouvez l’afficher et interagir avec lui via le portail Azure et les outils CLI. Un modèle ARM n’est nécessaire que pour la création initiale du plan App Service.

Les seules modifications nécessaires dans un modèle ARM pour spécifier un App Service redondant dans une zone sont la nouvelle propriété ***zoneRedundant** _ (obligatoire) et, éventuellement, le nombre d’instances du plan App service (_*_capacity_*_) sur la ressource [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms?tabs=json). Si vous ne spécifiez pas de capacité, la capacité par défaut de la plateforme est trois. La propriété _*_zoneRedundant_*_ doit être définie sur _*_true_*_ et la spécification _ *_capacity_** doit être définie en fonction des exigences de charge de travail, mais ne peut en aucun cas être inférieure à trois. Une bonne règle de base pour choisir la capacité consiste à veiller à ce que les instances soient en nombre suffisant pour l’application, de telle sorte que la perte d’une zone d’instances laisse une capacité suffisante pour gérer la charge attendue.

> [!TIP]
> Pour déterminer la capacité d’instance, vous pouvez utiliser le calcul suivant :
>
> Étant donné que la plateforme répartit les machines virtuelles dans 3 zones et que vous devez prendre en compte au moins la défaillance de 1 zone, multipliez le nombre d’instances nécessaires pour un pic de charge de travail par un facteur de zones/(zones-1), soit 3/2. Par exemple, si votre pic de charge de travail typique nécessite 4 instances, vous devez en approvisionner 6 : (2/3 * 6 instances) = 4 instances.
>

L’extrait de code du modèle ARM ci-dessous montre la nouvelle propriété ***zoneRedundant** _ et la spécification _ *_capacity_**.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "your-appserviceplan-name-here",
    "location": "West US 3",
    "sku": {
        "name": "P1v3",
        "tier": "PremiumV3",
        "size": "P1v3",
        "family": "Pv3",
        "capacity": 3
    },
    "kind": "app",
    "properties": {
        "zoneRedundant": true
    }
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrir comment créer et déployer des modèles ARM](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)

> [!div class="nextstepaction"]
> [Modèles de démarrage rapide ARM](https://azure.microsoft.com/resources/templates/)
