---
title: Options de mise en réseau d’Azure Functions
description: Vue d’ensemble de toutes les options de mise en réseau disponibles dans Azure Functions.
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: c954f411d14aaa0f33bf4a3423a67ae64204f932
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892845"
---
# <a name="azure-functions-networking-options"></a>Options de mise en réseau d’Azure Functions

Cet article décrit les fonctionnalités de mise en réseau disponibles dans les options d’hébergement pour Azure Functions. Toutes les options de mise en réseau suivantes vous permettent d’accéder à des ressources sans utiliser d’adresses routables sur Internet, ou de restreindre l’accès à Internet à une application de fonction.

Les modèles d’hébergement offrent différents niveaux d’isolement réseau. Le choix de l’option appropriée vous aide à répondre à vos besoins d’isolement réseau.

Vous pouvez héberger des applications de fonction de deux façons :

* Vous avez le choix entre des plans qui s’exécutent sur une infrastructure mutualisée, avec divers niveaux de connectivité au réseau virtuel et diverses options de mise à l’échelle :
    * Le [plan Consommation](consumption-plan.md), qui s’adapte de façon dynamique en réponse à la charge et offre des options d’isolement réseau minimal.
    * Le [plan Premium](functions-premium-plan.md), qui s’adapte de façon dynamique tout en offrant un isolement réseau plus complet.
    * Le plan [Azure App Service](dedicated-plan.md) opère à une échelle fixe et offre un offre isolement réseau similaire au plan Premium.
* Vous pouvez exécuter des fonctions dans un [Azure App Service Environment](../app-service/environment/intro.md). Cette méthode déploie votre fonction dans votre réseau virtuel et offre un contrôle et un isolement réseau complets.

## <a name="matrix-of-networking-features"></a>Matrice de fonctionnalités de mise en réseau

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>Restrictions d’accès entrant

Vous pouvez utiliser des restrictions d’accès pour définir la liste des adresses IP classées par ordre de priorité qui sont autorisées ou non à accéder à votre application. La liste peut inclure des adresses IPv4 et IPv6 ou des sous-réseaux spécifiques de réseau virtuel utilisant des [points de terminaison de service](#use-service-endpoints). Lorsqu’il y a une ou plusieurs entrées, une règle implicite « Tout refuser » se trouve à la fin de la liste. Les restrictions d’adresse IP fonctionnent avec toutes les options d’hébergement de fonction.

Les restrictions d’accès sont disponibles dans les niveaux [Premium](functions-premium-plan.md), [Consommation](consumption-plan.md) et [App service](dedicated-plan.md).

> [!NOTE]
> Une fois les restrictions réseau en place, vous pouvez uniquement déployer à partir de votre réseau virtuel ou après avoir ajouté l’adresse IP de la machine que vous utilisez pour accéder au portail Azure sur la liste des destinataires approuvés. Toutefois, vous pouvez toujours gérer la fonction à l’aide du portail.

Pour en savoir plus, consultez [Restrictions d’accès statique Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-endpoint-connections"></a>Connexions des points de terminaison privés

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

Pour appeler d’autres services qui disposent d’une connexion de point de terminaison privé, par exemple Stockage ou Service Bus, veillez à configurer votre application pour effectuer des [appels sortants vers des points de terminaison privés](#private-endpoints).

### <a name="use-service-endpoints"></a>Utiliser des points de terminaison de service

Grâce aux points de terminaison de service, vous pouvez restreindre l’accès à une sélection de sous-réseaux de réseau virtuel Azure. Pour restreindre l’accès à un sous-réseau spécifique, créez une règle de restriction avec le type **Réseau virtuel**. Vous pouvez ensuite sélectionner l’abonnement, le réseau virtuel et le sous-réseau auxquels vous souhaitez accorder ou refuser l’accès. 

Si les points de terminaison de service ne sont pas encore activés avec Microsoft.Web pour le sous-réseau que vous avez sélectionné, ils seront automatiquement activés, sauf si vous cochez la case **Ignorer les points de terminaison de service Microsoft.Web manquants**. Le scénario dans lequel vous voudriez activer des points de terminaison de service sur l’application, mais pas sur le sous-réseau, dépend principalement du fait que vous disposiez ou non des autorisations pour les activer sur le sous-réseau. 

Si vous avez besoin que quelqu’un d’autre active les points de terminaison de service sur le sous-réseau, cochez la case **Ignorer les points de terminaison de service Microsoft.Web manquants**. Votre application sera configurée pour les points de terminaison de service en prévision de leur activation ultérieure sur le sous-réseau. 

![Capture d’écran du volet « Ajouter une restriction d’adresse IP » avec le type Réseau virtuel sélectionné.](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Vous ne pouvez pas utiliser des points de terminaison de service pour restreindre l’accès aux applications qui s’exécutent dans un environnement App Service Environment. Si votre application se trouve dans un environnement App Service Environment, vous pouvez contrôler l’accès à celle-ci avec des règles d’accès IP. 

Pour apprendre à configurer des points de terminaison de service, consultez [Établir l’accès privé aux sites avec Azure Functions](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Intégration du réseau virtuel

L’intégration de réseau virtuel permet à votre Function App d’accéder aux ressources au sein d’un réseau virtuel.
Azure Functions prend en charge deux types d’intégration de réseau virtuel :

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Dans Azure Functions, l'intégration au réseau virtuel utilise une infrastructure partagée avec les applications Web App Service. Pour en savoir plus sur les deux types d'intégration au réseau virtuel, consultez :

* [Intégration du réseau virtuel régional](../app-service/overview-vnet-integration.md#regional-virtual-network-integration)
* [Intégration au réseau virtuel avec passerelle obligatoire](../app-service/overview-vnet-integration.md#gateway-required-virtual-network-integration)

Pour savoir comment configurer l’intégration d’un réseau virtuel, consultez [Activer l’intégration d’un réseau virtuel](#enable-vnet-integration).

## <a name="enable-vnet-integration"></a>Activer une intégration au réseau virtuel

1. Accédez au panneau **Réseau** dans le portail Function App. Sous **Intégration de réseau virtuel**, sélectionnez **Cliquez ici pour configurer**.

1. Sélectionnez **Ajouter un réseau virtuel**.

    :::image type="content" source="./media/functions-networking-options/vnet-int-function-app.png" alt-text="Sélectionner l’intégration au réseau virtuel":::

1. La liste déroulante contient tous les réseaux virtuels Azure Resource Manager de votre abonnement dans la même région. Sélectionnez le réseau virtuel avec lequel vous souhaitez effectuer l’intégration.

    :::image type="content" source="./media/functions-networking-options/vnet-int-add-vnet-function-app.png" alt-text="Sélectionner le réseau virtuel":::

    * Le plan Premium Functions prend uniquement en charge l’intégration de réseau virtuel régional. Si le réseau virtuel se trouve dans la même région, créez un nouveau sous-réseau ou sélectionnez un sous-réseau préexistant vide.
    * Pour sélectionner un réseau virtuel dans une autre région, vous devez disposer d’une passerelle de réseau virtuel provisionnée dont l’option Point à site est activée. L’intégration de réseau virtuel entre régions est uniquement prise en charge pour les plans dédiés.

Lors de l’intégration, votre application est redémarrée. Une fois l’intégration terminée, vous verrez des informations sur le réseau virtuel auquel vous êtes intégré. Par défaut, l’option Tout router est activée, et tout le trafic est routé vers votre réseau virtuel.

Si vous souhaitez que seul votre trafic privé (trafic [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)) soit routé, suivez les étapes fournies dans la [documentation d’App Service](../app-service/overview-vnet-integration.md#application-routing).

## <a name="regional-virtual-network-integration"></a>Intégration du réseau virtuel régional

L’utilisation de l’intégration au réseau virtuel régional permet à votre application d’accéder aux :

* Ressources d’un réseau virtuel dans la même région que votre application.
* Ressources de réseaux virtuels appairés au réseau virtuel auquel votre application est intégrée.
* Services sécurisés des points de terminaison de service.
* Ressources sur des connexions Azure ExpressRoute.
* Ressources dans le réseau virtuel auquel vous êtes intégré.
* Ressources sur des connexions appairées, notamment des connexions Azure ExpressRoute.
* Instances Private Endpoint 

Lorsque vous utilisez l’intégration au réseau virtuel avec des réseaux virtuels d’une même région, vous pouvez utiliser les fonctionnalités de même en réseau Azure suivantes :

* **Groupes de sécurité réseau (NSG)**  : Vous pouvez bloquer le trafic sortant avec un groupe de sécurité réseau placé sur votre sous-réseau d’intégration. Les règles de trafic entrant ne s’appliquent pas, car vous ne pouvez pas utiliser l’intégration au réseau virtuel pour fournir un accès entrant à votre application.
* **Tables de routage (Routes définies par l’utilisateur)**  : Vous pouvez placer une table de routage sur le sous-réseau d’intégration pour envoyer le trafic sortant où vous voulez.

> [!NOTE]
> Lorsque vous routez tout le trafic sortant vers votre réseau virtuel, il est soumis aux groupes de sécurité réseau et aux routes définies par l’utilisateur appliqués à votre sous-réseau d’intégration. Lorsque le réseau virtuel est intégré, le trafic sortant de votre application de fonction vers des adresses IP publiques est toujours envoyé à partir des adresses listées dans les propriétés de votre application, sauf si vous fournissez des routes qui dirigent le trafic ailleurs.
> 
> L’intégration au réseau virtuel régional n’est pas en mesure d’utiliser le port 25.

Il existe certaines limitations concernant l’utilisation de l’intégration au réseau virtuel avec les réseaux virtuels d’une même région :

* Vous ne pouvez pas accéder à des ressources via des connexions d’appairage mondiales.
* Cette fonctionnalité est disponible à partir de toutes les unités d’échelle App Service dans Premium v2 et Premium v3. Elle est également disponible dans Standard, mais uniquement à partir d’unités d’échelle App Service plus récentes. Si vous utilisez une unité d’échelle plus ancienne, vous ne pouvez utiliser la fonctionnalité qu’à partir d’un plan App Service Premium v2. Si vous souhaitez vous assurer de pouvoir utiliser la fonctionnalité dans un plan App Service Standard, créez votre application dans un plan App Service Premium v3. Ces plans ne sont pris en charge que sur les unités d’échelle les plus récentes. Vous pouvez effectuer un scale-down par la suite si vous le souhaitez.
* Le sous-réseau d’intégration peut être utilisé par un seul plan App Service.
* La fonctionnalité ne peut pas être utilisée par des applications de plan Isolé qui se trouvent dans un environnement App Service.
* La fonctionnalité nécessite un sous-réseau inutilisé /28 ou d’une taille supérieure dans un réseau virtuel Azure Resource Manager.
* L’application et le réseau virtuel doivent être dans la même région.
* Vous ne pouvez pas supprimer un réseau virtuel avec une application intégrée. Supprimez l’intégration avant de supprimer le réseau virtuel.
* Vous ne pouvez disposer que d’une seule intégration au réseau virtuel régional par plan App Service. Plusieurs applications d’un même plan App Service peuvent utiliser le même réseau virtuel.
* Vous ne pouvez pas changer l’abonnement d’une application ou d’un plan quand une application utilise l’intégration au réseau virtuel régional.
* Votre application ne peut pas résoudre les adresses dans les zones privées Azure DNS sans modification de la configuration.

## <a name="subnets"></a>Sous-réseaux

L’intégration au réseau virtuel dépend d’un sous-réseau dédié. Lorsque vous approvisionnez un sous-réseau, le sous-réseau Azure perd cinq adresses IP dès le début. Une seule adresse du sous-réseau d’intégration est utilisée pour chaque instance de plan. Lorsque vous définissez l’échelle de votre application sur quatre instances, quatre adresses sont utilisées. 

Lorsque vous en augmentez ou diminuez la taille, l’espace d’adressage requis est doublé pendant une brève période de temps. Cela affecte les instances prises en charge réelles et disponibles pour une taille de sous-réseau donnée. Le tableau suivant indique à la fois le nombre maximal d’adresses disponibles par bloc CIDR et l’impact de cette mise à l’échelle horizontale :

| Taille de bloc CIDR | Nombre maximal d’adresses disponibles | Mise à l’échelle horizontale maximale (instances)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Part du principe que vous devrez effectuer un scale-up ou un scale-down en taille ou en SKU à un moment donné. 

Comme la taille du sous-réseau ne peut pas être modifiée après l’affectation, utilisez un sous-réseau suffisamment grand pour s’adapter à l’échelle que votre application est susceptible d’atteindre. Pour éviter tout problème de capacité de sous-réseau pour les plans Premium Functions, vous devez utiliser un /24 avec 256 adresses pour Windows et un /26 avec 64 adresses pour Linux. 

Quand vous voulez que vos applications d’un autre plan atteignent un réseau virtuel auquel sont déjà connectées des applications d’un autre plan, sélectionnez un sous-réseau différent de celui utilisé par l’intégration au réseau virtuel préexistante.

La fonctionnalité est entièrement prise en charge pour les applications Windows et Linux, notamment les [conteneurs personnalisés](../app-service/configure-custom-container.md). Tous les comportements sont identiques entre les applications Windows et les applications Linux.

### <a name="service-endpoints"></a>Points de terminaison de service

Afin d’offrir un niveau de sécurité supérieur, vous pouvez restreindre un nombre de services Azure sur un réseau virtuel en utilisant des points de terminaison de service. L’intégration de réseau virtuel régional permet à votre application de fonction d’atteindre les services Azure sécurisés avec des points de terminaison de service. Cette configuration est prise en charge sur tous les [plans](functions-scale.md#networking-features) qui prennent en charge l’intégration du réseau virtuel. Pour accéder à un service sécurisé par point de terminaison de service, vous devez effectuer les opérations suivantes :

1. Configurez l’intégration de réseau virtuel régional avec votre application de fonction pour vous connecter à un sous-réseau spécifique.
1. Accédez au service de destination et configurez des points de terminaison de service sur le sous-réseau d’intégration.

Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="network-security-groups"></a>Groupes de sécurité réseau

Vous pouvez utiliser des groupes de sécurité réseau pour bloquer le trafic entrant et sortant vers des ressources d’un réseau virtuel. Une application utilisant l’intégration de réseau virtuel régional peut utiliser un [groupe de sécurité réseau][VNETnsg] pour bloquer le trafic sortant vers des ressources dans votre réseau virtuel ou sur Internet. Pour bloquer le trafic vers des adresses publiques, vous devez disposer de l’intégration de réseau virtuel avec l’option Tout router activée. Les règles de trafic entrant dans un groupe de sécurité réseau ne s’appliquent pas à votre application, car l’intégration au réseau virtuel n’a d’incidence que sur le trafic sortant depuis votre application.

Pour contrôler le trafic entrant vers votre application, utilisez la fonctionnalité Restrictions d’accès. Un groupe de sécurité réseau appliqué à votre sous-réseau d’intégration est actif quelles que soient les routes appliquées à votre sous-réseau d’intégration. Si votre application de fonction est intégrée à un réseau virtuel avec l’option Tout router activée, et que vous n’avez aucune route affectant le trafic des adresses publiques sur votre sous-réseau d’intégration, l’ensemble du trafic sortant est toujours soumis aux groupes de sécurité réseau affectés à votre sous-réseau d’intégration. Lorsque l’option Tout router n’est pas activée, les groupes de sécurité réseau sont appliqués uniquement au trafic RFC1918.

### <a name="routes"></a>Itinéraires

Vous pouvez utiliser des tables de routage pour router le trafic sortant depuis votre application vers où vous voulez. Par défaut, les tables de routage affectent seulement votre trafic de destination RFC1918. Lorsque l’option Tout router est activée, tous vos appels sortants sont affectés. Lorsque [Tout router](../app-service/overview-vnet-integration.md#application-routing) est désactivé, seul le trafic privé (RFC1918) est concerné par vos tables de routage. Les routes définies sur votre sous-réseau d’intégration n’affectent pas les réponses aux requêtes d’application entrantes. Les destinations courantes peuvent inclure des pare-feu ou des passerelles.

Si vous souhaitez router tout le trafic sortant localement, vous pouvez utiliser une table de route pour envoyer l’intégralité du trafic sortant vers votre passerelle ExpressRoute. Si vous choisissez de router le trafic vers une passerelle, veillez à définir des routes dans le réseau externe pour renvoyer des réponses.

Les routes BGP (Border Gateway Protocol) affectent également le trafic de votre application. Si vous avez des routes BGP provenant par exemple d’une passerelle ExpressRoute, le trafic sortant de votre application est affecté. Par défaut, les routes BGP affectent seulement votre trafic de destination RFC1918. Lorsque votre application de fonction est intégrée à un réseau virtuel avec l’option Tout router activée, tout le trafic sortant peut être affecté par vos routes BGP.

### <a name="azure-dns-private-zones"></a>Zones privées Azure DNS 

Une fois que votre application est intégrée à votre réseau virtuel, elle utilise le même serveur DNS que celui avec lequel votre réseau virtuel est configuré. Par défaut, votre application ne fonctionnera pas avec des zones privées Azure DNS. Pour qu’elle fonctionne avec les zones privées Azure DNS, vous devez ajouter les paramètres d’application suivants :

- `WEBSITE_VNET_ROUTE_ALL` avec la valeur `1`

Ce paramètre envoie l’ensemble des appels sortants de votre application vers votre réseau virtuel, et permet à votre application d’accéder à une zone privée Azure DNS. Avec ces paramètres, votre application peut utiliser Azure DNS en interrogeant la zone privée DNS au niveau du Worker.  

### <a name="private-endpoints"></a>Points de terminaison privés

Si vous souhaitez effectuer des appels vers des [points de terminaison privés][privateendpoints], vous devez vous assurer que vos recherches DNS seront résolues sur le point de terminaison privé. Vous pouvez appliquer ce comportement de l’une des façons suivantes : 

* Intégrer à des zones privées Azure DNS Lorsque votre réseau virtuel n’a pas de serveur DNS personnalisé, cela sera effectué automatiquement.
* Gérer le point de terminaison privé dans le serveur DNS utilisé par votre application. Pour ce faire, vous devez connaître l’adresse du point de terminaison privé, puis pointer le point de terminaison que vous essayez d’atteindre vers cette adresse à l’aide d’un enregistrement A.
* Configurez votre propre serveur DNS pour le transfert vers des [zones privées Azure DNS](#azure-dns-private-zones).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Restreindre votre compte de stockage à un réseau virtuel 

Quand vous créez une application de fonction, vous devez créer un compte de stockage Azure à usage général qui prend en charge le stockage Blob, File d’attente et Table, ou établir un lien vers un compte de ce type. Vous pouvez remplacer ce compte de stockage par un compte sécurisé avec des points de terminaison de service ou privés. 

Cette fonctionnalité est prise en charge par toutes les références SKU prises en charge par le réseau virtuel Windows dans le plan dédié (App Service) et pour le plan Premium. Elle est également pris en charge avec le DNS privé pour les références SKU prises en charge par le réseau virtuel Linux. Le plan de consommation et les plans DNS personnalisés sur Linux ne sont pas pris en charge. Pour savoir comment configurer une fonction avec un compte de stockage limité à un réseau privé, consultez [Restreindre votre compte de stockage à un réseau virtuel](configure-networking-how-to.md#restrict-your-storage-account-to-a-virtual-network).

## <a name="use-key-vault-references"></a>Utiliser des références Key Vault

Vous pouvez utiliser des références Azure Key Vault pour utiliser des secrets d’Azure Key Vault dans votre application Azure Functions, sans avoir à modifier le code. Azure Key Vault est un service qui fournit une gestion centralisée des secrets, avec un contrôle total sur les stratégies d’accès et l’historique d’audit.

Si l’intégration de réseau virtuel est configurée pour l’application, des [références Key Vault](../app-service/app-service-key-vault-references.md) peuvent être utilisées pour récupérer des secrets d’un coffre avec restrictions de réseau.

## <a name="virtual-network-triggers-non-http"></a>Déclencheurs de réseau virtuel (non HTTP)

Vous pouvez utiliser des fonctions de déclencheur non-HTTP à partir d’un réseau virtuel de deux manières :

+ Exécutez votre application de fonction dans un plan Premium, et activez la prise en charge des déclencheurs de réseau virtuel.
+ Exécutez votre application de fonction dans un plan ou environnement App Service.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plan Premium avec déclencheurs de réseau virtuel

En cas d’exécution dans un plan Premium, vous pouvez connecter des fonctions de déclencheur non-HTTP à des services s’exécutant au sein d’un réseau virtuel. Pour ce faire, vous devez activer la prise en charge des déclencheurs de réseau virtuel pour votre application de fonction. Le paramètre de **Surveillance d’échelle en temps réel** se trouve dans le [portail Azure](https://portal.azure.com), sous **Configuration** > **Paramètres d’exécution de la fonction**.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Vous pouvez également activer les déclencheurs de réseau virtuel en utilisant la commande Azure CLI suivante :

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> L’activation des déclencheurs de réseau virtuel peut avoir un impact sur les performances de votre application, car vos instances de plan App Service doivent surveiller vos déclencheurs pour déterminer quand mettre à l’échelle. Cet impact devrait être très réduit très réduit.

Les déclencheurs de réseau virtuel sont pris en charge dans la version 2.x et les versions ultérieures du runtime Functions. Les types de déclencheurs non-HTTP suivants sont pris en charge.

| Extension | Version minimale |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 ou ultérieure |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 ou ultérieure|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 ou ultérieure|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 ou ultérieure|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 ou ultérieure|

> [!IMPORTANT]
> Lorsque vous activez la prise en charge des déclencheurs de réseau virtuel, seuls les types de déclencheurs présentés dans le tableau précédent sont mis à l’échelle de façon dynamique avec votre application. Vous pouvez toujours utiliser les déclencheurs qui ne figurent pas dans le tableau, mais ils ne sont pas mis à l’échelle au-delà de leur nombre d’instances préchauffées. Pour obtenir la liste complète des déclencheurs, consultez [Déclencheurs et liaisons](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Plan App Service et App Service Environment avec déclencheurs de réseau virtuel

Lorsque votre application de fonction s’exécute dans un plan App Service ou un App Service Environment, vous pouvez utiliser des fonctions de déclencheur non-HTTP. Pour le bon déclenchement de vos fonctions, vous devez être connecté à un réseau virtuel, avec accès à la ressource définie dans la connexion de déclenchement.

Supposons, par exemple, que vous souhaitiez configurer Azure Cosmos DB pour accepter le trafic uniquement à partir d’un réseau virtuel. Dans ce cas, vous devez déployer votre application de fonction dans un plan App Service fournissant une intégration de réseau virtuel à ce réseau virtuel. L’intégration permet donc qu’une fonction soit déclenchée par cette ressource Azure Cosmos DB.

## <a name="hybrid-connections"></a>les connexions hybrides

[Connexions hybrides](../azure-relay/relay-hybrid-connections-protocol.md) est une fonctionnalité d’Azure Relay que vous pouvez utiliser pour accéder aux ressources d’application dans d’autres réseaux. Elles permettent d’accéder depuis votre application à un point de terminaison d’application. Vous ne pouvez pas l’utiliser pour accéder à votre application. La fonctionnalité Connexions hybrides est disponible pour les fonctions exécutées sur Windows dans tous les plans, sauf le plan Consommation.

Utilisée dans Azure Functions, chaque connexion hybride correspond à une combinaison d’hôte et de port TCP unique. Cela signifie que le point de terminaison de connexion hybride peut se trouver sur un quelconque système d’exploitation et toute application tant que vous accédez à un port d’écoute TCP. La fonctionnalité Connexions hybrides ne détecte pas et ne prend pas en compte le protocole d’application ou les ressources auxquels vous accédez. Elle fournit simplement un accès réseau.

Pour plus d’informations, consultez la [documentation App Service pour les connexions hybrides](../app-service/app-service-hybrid-connections.md). Ces mêmes étapes de configuration prennent en charge Azure Functions.

>[!IMPORTANT]
> Les connexions hybrides sont uniquement prises en charge dans les plans Windows. Linux n’est pas pris en charge.

## <a name="outbound-ip-restrictions"></a>Restrictions d’adresse IP sortantes

Les restrictions d’adresse IP sortante sont disponibles dans un plan Premium, un plan App Service ou un App Service Environment. Vous pouvez configurer des restrictions sortantes pour le réseau virtuel sur lequel votre Azure App Service Environment est déployé.

Lorsque vous intégrez une application de fonction à un réseau virtuel dans le cadre d'un plan Premium ou App Service, l’application est toujours en mesure de passer des appels sortants vers Internet par défaut. En intégrant votre application de fonction à un réseau virtuel avec l’option Tout router activée, vous forcez l’envoi de tout le trafic sortant vers votre réseau virtuel, où des règles de groupe de sécurité réseau peuvent être utilisées pour restreindre le trafic.

Pour savoir comment contrôler l’adresse IP sortante à l’aide d’un réseau virtuel, consultez [Tutoriel : Contrôler l’adresse IP sortante Azure Functions avec une passerelle NAT de réseau virtuel Azure](functions-how-to-use-nat-gateway.md). 

## <a name="automation"></a>Automatisation
Les API suivantes vous permettent de gérer par programmation les intégrations de réseaux virtuels régionaux :

+ **Azure CLI** : utilisez les commandes [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) pour ajouter, répertorier ou supprimer une intégration de réseau virtuel régional.  
+ **Modèles ARM** : l’intégration d’un réseau virtuel régional peut être activée à l’aide d’un modèle Azure Resource Manager. Pour un exemple complet, consultez [ce modèle de démarrage rapide de Functions](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Dépannage

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la mise en réseau et Azure Functions :

* [Tutoriel pour bien démarrer avec l’intégration de réseau virtuel](./functions-create-vnet.md)
* [Lire les questions fréquentes (FAQ) sur la mise en réseau de Functions](./functions-networking-faq.yml)
* [En savoir plus sur l’intégration de réseau virtuel avec App Service/Functions](../app-service/overview-vnet-integration.md)
* [En savoir plus sur les réseaux virtuels dans Azure](../virtual-network/virtual-networks-overview.md)
* [Activer davantage de fonctionnalités de réseau et plus de contrôle avec Azure App Service Environment](../app-service/environment/intro.md)
* [Se connecter à des ressources locales sans modifier le pare-feu à l’aide des connexions hybrides](../app-service/app-service-hybrid-connections.md)


<!--Links-->
[VNETnsg]: ../virtual-network/network-security-groups-overview.md
[privateendpoints]: ../app-service/networking/private-endpoint.md