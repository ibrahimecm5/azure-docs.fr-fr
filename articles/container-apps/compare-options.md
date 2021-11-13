---
title: Comparaison des applications conteneur à d’autres options de conteneur Azure
description: Découvrez quand utiliser Azure Container Apps et en quoi il diffère d’autres options de conteneur, notamment Azure Container Instances, Azure App Services, Azure Functions et Azure Kubernetes Service.
services: app-service
author: jeffhollan
ms.service: app-service
ms.topic: quickstart
ms.date: 11/03/2021
ms.author: jehollan
ms.custom: ignite-fall-2021
ms.openlocfilehash: c14080dc27ecb8755ba786acea8f9964676c09ec
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576431"
---
# <a name="comparing-container-apps-with-other-azure-container-options"></a>Comparaison des applications conteneur à d’autres options de conteneur Azure

Les équipes disposent de nombreuses options pour créer et déployer des applications conteneurisées cloud natives sur Azure. Cet article vous aide à comprendre les scénarios et les cas d’utilisation les mieux adaptés à Azure Container Apps et en quoi il diffère d’autres options de conteneur sur Azure, notamment :  
- [Azure Container Instances](#azure-container-instances)
- [Azure App Services](#azure-app-services)
- [Azure Functions](#azure-functions)
- [Azure Kubernetes Service](#azure-kubernetes-service)
- [Azure Spring Cloud](#azure-spring-cloud)

Aucune solution ne convient parfaitement à chaque cas d’utilisation et chaque équipe. L’explication suivante vous fournit des recommandations générales comme point de départ pour rechercher la solution la mieux adaptée à votre équipe et à vos exigences.

> [!IMPORTANT]
> Azure Container Apps est actuellement en préversion publique, alors que ces autres options sont en disponibilité générale (GA).


## <a name="container-option-comparisons"></a>Comparaison des options de conteneur

### <a name="azure-container-apps"></a>Azure Container Apps
Azure Container Apps vous permet de créer des microservices serverless basés sur des conteneurs. Les fonctionnalités distinctives de Container Apps sont notamment :

* Optimisé pour l’exécution de conteneurs à usage général, en particulier pour les applications qui couvrent de nombreux microservices déployés dans des conteneurs.
* Repose sur Kubernetes et des technologies open source comme [Dapr](https://dapr.io/), [KEDA](https://keda.sh/) et [envoy](https://www.envoyproxy.io/).
* Prend en charge les applications de type Kubernetes et les microservices avec des fonctionnalités comme la [découverte du service](connect-apps.md) et la [répartition du trafic](revisions.md).
* Autorise les architectures d’application pilotées par les événements en prenant en charge la mise à l’échelle en fonction du trafic et en tirant des données à partir de [sources d’événements comme les files d’attente](scale-app.md), y compris la [mise à l’échelle sur 0](scale-app.md).
* Prend en charge les processus de longue durée et peut exécuter des [tâches en arrière-plan](background-processing.md).
* Toutes les applications conteneur sont compatibles Kubernetes.

Azure Container Apps ne fournit pas d’accès direct aux API Kubernetes sous-jacentes. Si vous avez besoin d’accéder aux API Kubernetes et au plan de contrôle, vous devez utiliser [Azure Kubernetes Service](../aks/intro-kubernetes.md). Toutefois, pour créer des applications de type Kubernetes sans accès direct à l’ensemble des API Kubernetes natives et à la gestion des clusters, Container Apps fournit une expérience complètement managée s’inspirant des bonnes pratiques. Pour ces raisons, de nombreuses équipes peuvent préférer utiliser Azure Container Apps pour commencer à créer des microservices de conteneur.

### <a name="azure-app-services"></a>Azure App Services
Azure App Services fournit un hébergement complètement managé pour les applications web, notamment les sites web et les API web. Ces applications web peuvent être déployées avec du code ou des conteneurs. Azure App Services est optimisé pour les applications web. Azure App Services est intégré à d’autres services Azure, notamment Azure Container Apps ou Azure Functions. Si vous créer une application web, en particulier si vous utilisiez une autre solution d’hébergement web, Azure App Services offre la meilleure expérience.

### <a name="azure-container-instances"></a>Azure Container Instances
Azure Container Instances (ACI) fournit un seul pod de conteneurs isolés Hyper-V à la demande. Il peut être considéré comme une option « de base » de niveau inférieur par rapport à Container Apps. Les concepts comme la mise à l’échelle, l’équilibrage de charge et les certificats ne sont pas fournis avec les conteneurs ACI. Par exemple, pour une mise à l’échelle vers 5 instances de conteneur, vous créez cinq instances de conteneur distinctes. Azure Container Apps fournit de nombreux concepts propres aux applications en plus des conteneurs, notamment les certificats, les révisions, la mise à l’échelle et les environnements. Les utilisateurs interagissent souvent avec Azure Container Instances en utilisant d’autres services. Par exemple, Azure Kubernetes Service peut superposer l’orchestration et la mise à l’échelle sur ACI en utilisant des [nœuds virtuels](../aks/virtual-nodes.md). Si vous avez besoin d’un composant avec moins d’options et qui ne correspond pas aux scénarios auxquels convient Azure Container Apps, Azure Container Instances est une option idéale.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
Azure Kubernetes Service fournit une option Kubernetes complètement managée dans Azure. Il prend en charge l’accès direct à l’API Kubernetes et exécute toutes les charges de travail Kubernetes. Les équipes qui ont besoin d’une version sécurisée et complètement managée de Kubernetes dans Azure peuvent choisir Azure Kubernetes Service sans hésiter.

### <a name="azure-functions"></a>Azure Functions
Azure Functions est une solution FaaS (functions as a service) serverless. Il est optimisé pour l’exécution de fonctions pilotées par les événements à partir du modèle de programmation Functions. Il partage de nombreuses caractéristiques avec Azure Container Apps autour de la mise à l’échelle et de l’intégration des événements, mais il est optimisé pour les fonctions éphémères déployées sous forme de code ou de conteneurs. Le modèle de programmation Azure Functions offre des avantages en termes de productivité pour les équipes qui recherchent un déclenchement à partir d’événements et une liaison à d’autres sources de données. Quand vous créez des fonctions de type FaaS, Azure Functions est l’option idéale. Le modèle de programmation Azure Functions est disponible sous forme d’image conteneur de base, ce qui le rend portable vers d’autres plateformes de calcul basées sur les conteneurs et permet aux équipes de réutiliser le code quand les exigences environnementales changent.

### <a name="azure-spring-cloud"></a>Azure Spring Cloud
Azure Spring Cloud permet de déployer facilement des applications de microservices Spring Boot sur Azure, et sans aucune modification du code. Le service gère l’infrastructure des applications Spring Cloud, ce qui permet aux développeurs de se concentrer sur leur code. Azure Spring Cloud assure la gestion du cycle de vie en utilisant des outils complets, tels que la supervision et les diagnostics, la gestion des configurations, la découverte de services, l’intégration CI/CD, les déploiements bleus-verts, etc. Si votre équipe ou votre organisation utilise majoritairement Spring, Azure Spring Cloud est idéal.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer votre première application conteneur](get-started.md)