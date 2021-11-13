---
title: Échelle et hébergement dans Azure Functions
description: Découvrez comment choisir entre le plan Consommation et le plan Premium d’Azure Functions.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9deb2e40c081b776a8dfa5fc5fb96efcfc4c219f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252936"
---
# <a name="azure-functions-hosting-options"></a>Options d’hébergement Azure Functions

Lorsque vous créez une Function App dans Azure, vous devez choisir un plan d’hébergement pour votre application. Trois plans d’hébergement De base sont disponibles pour Azure Functions : [plan Consommation](consumption-plan.md), [plan Premium](functions-premium-plan.md) et [plan dédié (App Service)](dedicated-plan.md). Tous les plans d’hébergement sont en disponibilité générale sur les machines virtuelles Linux et Windows.

Le plan d’hébergement que vous choisissez détermine les comportements suivants :

* La mise à l’échelle de votre Function App.
* Les ressources disponibles pour chaque instance de Function App.
* La prise en charge des fonctionnalités avancées, notamment la connectivité du réseau virtuel Azure.

Cet article présente une comparaison détaillée entre les différents plans d’hébergement, ainsi que l’[hébergement basé sur Kubernetes](functions-kubernetes-keda.md).

> [!NOTE]
> Si vous choisissez d’héberger vos fonctions dans un cluster Kubernetes, utilisez un [cluster Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md). L’hébergement sur un cluster Kubernetes avec Azure Arc est actuellement disponible en préversion. Pour en savoir plus, consultez [App Service, Functions et Logic Apps sur Azure Arc](../app-service/overview-arc-integration.md).  

## <a name="overview-of-plans"></a>Vue d’ensemble des plans

Voici un résumé des avantages des trois principaux plans d’hébergement pour Functions :

| Plan | Avantages |
| --- | --- |  
|**[Plan de consommation](consumption-plan.md)**| Mettez à l’échelle automatiquement et ne payez les ressources de calcul que lorsque vos fonctions sont en cours d’exécution.<br/><br/>Dans le plan de consommation, les instances de l’hôte Functions sont ajoutées et supprimées de façon dynamique en fonction du nombre d’événements entrants.<br/><br/> ✔ Plan d’hébergement par défaut.<br/>✔ Paiement uniquement en cas d’exécution de vos fonctions.<br/>✔ Mise à l’échelle automatique, même pendant les périodes de charge élevée.|  
|**[Plan Premium](functions-premium-plan.md)**|Mise à l’échelle automatique selon la demande à l’aide de Workers préparés qui exécutent des applications sans délai après leur inactivité, exécution sur des instances plus puissantes et connexion à des réseaux virtuels. <br/><br/>Envisagez le plan Premium d’Azure Functions dans les situations suivantes : <br/><br/>✔Vos applications de fonction s’exécutent en continu ou presque.<br/>✔ Vous disposez d’un grand nombre d’exécutions de petite taille et d’une facture d’exécution élevée, mais de peu de Go par seconde dans le plan Consommation.<br/>✔Vous avez besoin de plus d’options de mémoire ou de processeur que celles qui sont proposées dans le plan de consommation.<br/>✔Votre code exige une durée d’exécution supérieure à celle qui est autorisée dans le plan de consommation.<br/>✔ Vous avez besoin de fonctionnalités qui ne sont pas disponibles dans le plan Consommation, notamment la connectivité de réseau virtuel.<br/>✔ Vous souhaitez fournir une image Linux personnalisée sur laquelle exécuter vos fonctions. |  
|**[Plan dédié](dedicated-plan.md)** |Exécutez vos fonctions au sein d’un plan App Service aux [tarifs habituels du plan App Service](https://azure.microsoft.com/pricing/details/app-service/windows/).<br/><br/>Idéal pour les scénarios de longue durée où l’extension [Durable Functions](durable/durable-functions-overview.md) ne peut pas être utilisée. Envisagez un plan App Service dans les situations suivantes :<br/><br/>✔ Vous disposez de machines virtuelles existantes sous-utilisées qui exécutent déjà d’autres instances App Service.<br/>✔ La prédiction de la mise à l’échelle et des coûts est requise.|  

Les tableaux de comparaison de cet article incluent également les options d’hébergement suivantes, qui fournissent la plus grande quantité de contrôle et d’isolation dans laquelle exécuter vos applications de fonction.  

| Option d’hébergement | Détails |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | App Service Environment(ASE) est une fonctionnalité d’App Service qui fournit un environnement totalement isolé et dédié pour l’exécution sécurisée de vos applications App Service à grande échelle.<br/><br/>Les environnements ASE conviennent aux charges de travail d’application qui nécessitent : <br/><br/>✔ Une très grande échelle.<br/>✔ Une isolation totale du calcul et un accès réseau sécurisé.<br/>✔ Utilisation élevée de la mémoire.|  
| **Kubernetes**<br/>([Direct](functions-kubernetes-keda.md) ou<br/>[Azure Arc](../app-service/overview-arc-integration.md)) | Kubernetes offre un environnement totalement isolé et dédié qui s’exécute sur la plateforme Kubernetes.<br/><br/> Kubernetes convient aux charges de travail d’application qui nécessitent : <br/>✔ Des exigences matérielles personnalisées.<br/>✔ Une isolation et un accès réseau sécurisé.<br/>✔ Une possibilité d’exécution dans un environnement hybride ou multi-cloud.<br/>✔ Une exécution en parallèle de services et d’applications Kubernetes existants.|  

Les autres tableaux de cet article comparent les plans sur des fonctionnalités et des comportements différents. Pour une comparaison des coûts entre les plans d’hébergement dynamique (Consommation et Premium), consultez la [page de tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Pour obtenir la tarification des différentes options de plan Dedicated, consultez la [page de tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="operating-systemruntime"></a>Système d'exploitation/runtime

Le tableau suivant indique la prise en charge des systèmes d’exploitation et des langages pour les plans d’hébergement.

| | Linux<sup>1</sup><br/>Code uniquement | Windows<sup>2</sup><br/>Code uniquement | Linux<sup>1,3</sup><br/>Conteneur Docker |
| --- | --- | --- | --- |
| **[Plan de consommation](consumption-plan.md)** | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>Python<br/>TypeScript | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>TypeScript | Aucune prise en charge  |
| **[Plan Premium](functions-premium-plan.md)** | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>Python<br/>TypeScript |.NET Core<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>TypeScript |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python<br/>TypeScript  | 
| **[Plan dédié](dedicated-plan.md)** | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>Python<br/>TypeScript |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>TypeScript |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>Python<br/>TypeScript |
| **[ASE](dedicated-plan.md)** | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>Python<br/>TypeScript |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>TypeScript |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>Python<br/>TypeScript | 
| **[Kubernetes (direct)](functions-kubernetes-keda.md)** | n/a | n/a |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>Python<br/>TypeScript |
| **[Azure Arc (préversion)](../app-service/overview-arc-integration.md)** | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>Python<br/>TypeScript | n/a |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>Python<br/>TypeScript |

<sup>1</sup> Linux est le seul système d’exploitation pris en charge pour votre pile d’exécution Python. <br/>
<sup>2</sup> Windows est le seul système d’exploitation pris en charge pour votre pile d’exécution PowerShell.<br/>
<sup>3</sup> Linux est le seul système d’exploitation pris en charge pour les conteneurs Docker.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Scale

Le tableau suivant compare les comportements de mise à l’échelle des différents plans d’hébergement.

| Plan | Scale-out | Nombre maximal d’instances |
| --- | --- | --- |
| **[Plan de consommation](consumption-plan.md)** | [Basé sur les événements](event-driven-scaling.md). Effectuer un scale-out automatique, même pendant les périodes de charge élevée L’infrastructure Azure Functions met automatiquement à l’échelle les ressources de processeur et de mémoire en ajoutant des instances de l’hôte Functions selon le nombre d’événements déclencheurs entrants. | 200 |
| **[Plan Premium](functions-premium-plan.md)** | [Basé sur les événements](event-driven-scaling.md). Effectuer un scale-out automatique, même pendant les périodes de charge élevée L’infrastructure Azure Functions met automatiquement à l’échelle les ressources processeur et mémoire en ajoutant des instances de l’hôte Functions selon le nombre d’événements en fonction desquels ses fonctions sont déclenchées. |100|
| **[Plan dédié](dedicated-plan.md)** <sup>1</sup> | Manuel/Mise à l’échelle automatique |10-20|
| **[ASE](dedicated-plan.md)** <sup>1</sup> | Manuel/Mise à l’échelle automatique |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Mise à l’échelle automatique basée sur les événements pour les clusters Kubernetes qui utilisent [KEDA](https://keda.sh). | Varie&nbsp;en fonction du&nbsp;cluster&nbsp;&nbsp;|

<sup>1</sup> Pour connaître les limites spécifiques des différentes options du plan App Service, consultez les [limites du plan App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="cold-start-behavior"></a>Comportement du démarrage à froid

| Plan | Détails | 
| -- | -- |
| **[Plan de&nbsp;consommation](consumption-plan.md)** | Les applications peuvent être mises à l’échelle jusqu’à zéro en cas d’inactivité, ce qui signifie que certaines requêtes peuvent présenter une latence supplémentaire au démarrage.  Le plan de consommation offre des optimisations pour réduire le temps de démarrage à froid, notamment en extrayant à partir de fonctions d’espace réservé préparées qui exécutent déjà les processus de langage et d’hôte de fonction. |
| **[Plan Premium](functions-premium-plan.md)** | Les instances perpétuellement chaudes permettent d’éviter les démarrages à froid. |
| **[Plan dédié](dedicated-plan.md)** | En cas d’exécution dans un plan dédié, l’hôte Functions peut s’exécuter en continu, ce qui signifie que le démarrage à froid n’est pas vraiment un problème. |
| **[ASE](dedicated-plan.md)** | En cas d’exécution dans un plan dédié, l’hôte Functions peut s’exécuter en continu, ce qui signifie que le démarrage à froid n’est pas vraiment un problème. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Selon la configuration de KEDA, les applications peuvent être configurées de manière à éviter un démarrage à froid. Si elles sont configurées pour être mises à l’échelle à zéro, les nouveaux événements font l’objet d’un démarrage à froid. 

## <a name="service-limits"></a>Limites du service

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Fonctionnalités réseau

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Facturation

| Plan | Détails |
| --- | --- |
| **[Plan de consommation](consumption-plan.md)** | Ne payez que la durée d’exécution de vos fonctions. La facturation est basée sur le nombre d’exécutions, la durée d’exécution et la mémoire utilisée. |
| **[Plan Premium](functions-premium-plan.md)** | Le plan Premium se base sur le nombre de cœurs-seconde et la mémoire utilisée sur les instances nécessaires et préparées. Au moins une instance par plan doit être chaude en permanence. Ce plan offre la tarification la plus prévisible. |
| **[Plan dédié](dedicated-plan.md)** | Le coût des Function App dans un plan App Service est le même que pour d’autres ressources App Service, par exemple les applications web.|
| **[App Service Environment (ASE)](dedicated-plan.md)** | Un tarif fixe mensuel couvre l’infrastructure d’un ASE et ne change pas avec la taille de l’environnement ASE. Chaque processeur virtuel de plan App Service présente aussi un coût. Toutes les applications hébergées dans un environnement App Service sont dans la référence (SKU) de prix Isolée. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Vous ne payez que le coût de votre cluster Kubernetes et aucune facturation supplémentaire pour Functions. Votre application de fonction s’exécute comme une charge de travail d’application sur votre cluster, tout comme une application normale. |

## <a name="next-steps"></a>Étapes suivantes

+ [Technologies de déploiement dans Azure Functions](functions-deployment-technologies.md) 
+ [Guide du développeur Azure Functions](functions-reference.md)
