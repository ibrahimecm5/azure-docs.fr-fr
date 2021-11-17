---
title: Intégrer votre application à un réseau virtuel Azure
description: Intégrez votre application à Azure App Service avec des réseaux virtuels Azure.
author: madsd
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: madsd
ms.openlocfilehash: 2fab79439dd28a889d117bdce5bc4916f3d5b2f8
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892928"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Intégrer votre application à un réseau virtuel Azure

Cet article décrit la fonctionnalité d’intégration au réseau virtuel d’Azure App Service et explique comment la configurer avec des applications dans [App Service](./overview.md). Avec les [réseaux virtuels Azure](../virtual-network/virtual-networks-overview.md) (VNets), vous pouvez placer un grand nombre de vos ressources Azure dans un réseau routable non-Internet. La fonctionnalité d’intégration au réseau virtuel d’App Service permet à vos applications d’accéder à des ressources dans ou via un réseau virtuel. Elle n’autorise pas l’accès privé à vos applications.

App Service propose deux variantes :

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Découvrez comment [activer l’intégration au réseau virtuel](./configure-vnet-integration-enable.md).

## <a name="regional-virtual-network-integration"></a>Intégration du réseau virtuel régional

L’intégration au réseau virtuel régional prend en charge la connexion à un réseau virtuel dans la même région et ne nécessite pas de passerelle. L’utilisation de l’intégration au réseau virtuel régional permet à votre application d’accéder aux :

* ressources du réseau virtuel auquel vous êtes intégré.
* ressources dans les réseaux virtuels appairés au réseau virtuel auquel votre application est intégrée, y compris les connexions de peering mondial.
* Ressources sur des connexions Azure ExpressRoute.
* Services sécurisés par un point de terminaison de service.
* Services avec points de terminaison privés.

Lorsque vous utilisez l’Intégration au réseau virtuel régional, vous pouvez utiliser les fonctionnalités de mise en réseau Azure suivantes :

* **Groupes de sécurité réseau (NSG)**  : Vous pouvez bloquer le trafic sortant avec un groupe de sécurité réseau placé sur votre sous-réseau d’intégration. Les règles de trafic entrant ne s’appliquent pas, car vous ne pouvez pas utiliser l’intégration au réseau virtuel pour fournir un accès entrant à votre application.
* **Tables de routage (Routes définies par l’utilisateur)**  : Vous pouvez placer une table de routage sur le sous-réseau d’intégration pour envoyer le trafic sortant où vous voulez.

### <a name="how-regional-virtual-network-integration-works"></a>Fonctionnement de l’intégration au réseau virtuel régional

Les applications contenues dans App Service sont hébergées dans des rôles de travail. L’intégration au réseau virtuel régional opère en montant des interfaces virtuelles aux rôles de travail avec des adresses du sous-réseau délégué. Comme l’adresse De se trouve dans votre réseau virtuel, elle peut accéder à la plupart des éléments de votre réseau virtuel, comme le ferait une machine virtuelle de votre réseau virtuel. L’implémentation de la mise en réseau est différente de l’exécution d’une machine virtuelle dans votre réseau virtuel. C’est pourquoi certaines fonctionnalités de mise en réseau ne sont pas encore disponibles.

:::image type="content" source="./media/overview-vnet-integration/vnetint-how-regional-works.png" alt-text="Diagramme illustrant le fonctionnement de l’intégration au réseau virtuel régional.":::

Lorsque l’intégration au réseau virtuel régional est activée, votre application effectue des appels sortants via votre réseau virtuel. Les adresses sortantes figurant sur le portail des propriétés de l’application sont toujours les adresses qu’utilise votre application. Toutefois, si votre appel sortant concerne un ordinateur virtuel ou un point de terminaison privé dans le réseau virtuel d’intégration ou le réseau virtuel appairé, l’adresse sortante sera une adresse du sous-réseau d’intégration. L’adresse IP privée assignée à une instance est exposée via la variable d’environnement WEBSITE_PRIVATE_IP.

Quand le routage de l’ensemble du trafic est activé, tout le trafic sortant est envoyé dans votre réseau virtuel. Si le routage de tout le trafic n’est pas activé, seul le trafic privé (RFC1918) et les points de terminaison de service configurés sur le sous-réseau d’intégration seront envoyés dans le réseau virtuel, et le trafic sortant vers l’Internet passera par les mêmes canaux que d’habitude.

La fonctionnalité ne prend en charge qu’une seule interface virtuelle par Worker. Une interface virtuelle par Worker signifie une intégration au réseau virtuel régional par plan App Service. Toutes les applications d’un même plan App Service peuvent utiliser l’intégration au même réseau virtuel. Si vous avez besoin d’une application pour vous connecter à un autre réseau virtuel, vous devez créer un autre plan App Service. L’interface virtuelle utilisée n’est pas une ressource à laquelle les clients peuvent accéder directement.

Compte tenu du mode de fonctionnement de cette technologie, le trafic utilisé avec l’intégration au réseau virtuel n’apparaît pas dans les journaux de flux du groupe de sécurité réseau (NSG) ou d’Azure Network Watcher.

### <a name="subnet-requirements"></a>Configuration requise du sous-réseau

L’intégration au réseau virtuel dépend d’un sous-réseau dédié. Quand vous créez un sous-réseau, le sous-réseau Azure perd cinq IP dès le début. Une seule adresse du sous-réseau d’intégration est utilisée pour chaque instance de plan. Si vous définissez l’échelle de votre application sur quatre instances, quatre adresses sont utilisées.

Lorsque vous en augmentez ou diminuez la taille, l’espace d’adressage requis est doublé pendant une brève période de temps. Cela change les instances prises en charge réelles et disponibles pour une taille de sous-réseau donnée. Le tableau suivant indique à la fois le nombre maximal d’adresses disponibles par bloc CIDR et l’effet sur la mise à l’échelle horizontale.

| Taille de bloc CIDR | Nombre maximal d’adresses disponibles | Mise à l’échelle horizontale maximale (instances)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Part du principe que vous devrez effectuer un scale-up ou un scale-down en taille ou en SKU à un moment donné.

Comme la taille du sous-réseau ne peut pas être modifiée après l’affectation, utilisez un sous-réseau suffisamment grand pour s’adapter à l’échelle que votre application est susceptible d’atteindre. Pour éviter tout problème de capacité du sous-réseau, utilisez un `/26` avec 64 adresses.

Lorsque vous voulez que les applications de votre plan atteignent un réseau virtuel auquel sont déjà connectées des applications d’un autre plan, sélectionnez un sous-réseau différent de celui utilisé par l’intégration au réseau virtuel préexistante.

### <a name="routes"></a>Itinéraires

Il existe deux types de routage à prendre en compte lorsque vous configurez l’intégration au réseau virtuel régional. Le routage des applications définit quel trafic est acheminé de votre application vers le réseau virtuel. Le routage réseau permet de contrôler la manière dont le trafic est acheminé depuis votre réseau virtuel vers l’extérieur.

#### <a name="application-routing"></a>Routage d’applications

Lorsque vous configurez le routage d’applications, vous pouvez acheminer tout le trafic ou seulement le trafic privé (également appelé trafic [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)) vers votre réseau virtuel. Cette configuration s’effectue par le biais du paramètre **Tout acheminer**. Si **Tout acheminer** est désactivé, votre application achemine uniquement le trafic privé vers votre réseau virtuel. Si vous souhaitez acheminer l’ensemble de votre trafic sortant vers votre réseau virtuel, assurez-vous que le paramètre **Tout acheminer** est activé.

> [!NOTE]
> * Lorsque **Tout acheminer** est activé, tout le trafic est soumis aux NSG et UDR appliqués à votre sous-réseau d’intégration. Lorsque l’acheminement de tout le trafic est activé, le trafic sortant est toujours envoyé à partir des adresses répertoriées dans les propriétés de votre application, sauf si vous fournissez des itinéraires qui dirigent le trafic ailleurs.
> * Les conteneurs Windows ne prennent pas en charge le routage des références Key Vault d’App Service ou l’extraction d’images conteneur personnalisées via l’intégration au réseau virtuel.
> * L’intégration au réseau virtuel régional peut utiliser le port 25.

Découvrez [comment configurer le routage d’application](./configure-vnet-integration-routing.md).

Nous vous recommandons d’utiliser le paramètre de configuration **Tout acheminer** pour activer le routage de tout le trafic. L’utilisation du paramètre de configuration vous permet d’auditer le comportement avec une [stratégie intégrée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33228571-70a4-4fa1-8ca1-26d0aba8d6ef). Le paramètre d’application WEBSITE_VNET_ROUTE_ALL existant peut toujours être utilisé et vous pouvez activer tout le routage du trafic avec l’un ou l’autre des paramètres.

#### <a name="network-routing"></a>Routage réseau

Vous pouvez utiliser des tables de routage pour router le trafic sortant depuis votre application vers où vous voulez. Les tables de routage ont un impact sur le trafic de destination. Lorsque **Tout acheminer** est désactivé dans le [routage des applications](#application-routing), seul le trafic privé (RFC1918) est concerné par vos tables de routage. Les destinations courantes peuvent inclure des pare-feu ou des passerelles. Les routes définies sur votre sous-réseau d’intégration n’affectent pas les réponses aux requêtes d’application entrantes.

Pour router tout le trafic sortant localement, vous pouvez utiliser une table de route afin d’envoyer l’intégralité du trafic sortant vers votre passerelle Azure ExpressRoute. Si vous choisissez de router le trafic vers une passerelle, définissez des routes dans le réseau externe pour renvoyer des réponses.

Les routes BGP (Border Gateway Protocol) affectent également le trafic de votre application. Si vous avez des routes BGP provenant par exemple d’une passerelle ExpressRoute, le trafic sortant de votre application est affecté. Semblable aux itinéraires définis par l’utilisateur, les itinéraires BGP ont un impact sur le trafic en fonction des paramètres d’étendue du routage.

### <a name="network-security-groups"></a>Groupes de sécurité réseau

Une application utilisant l’intégration au réseau virtuel régional peut utiliser un [groupe de sécurité réseau](../virtual-network/network-security-groups-overview.md) pour bloquer le trafic sortant vers des ressources dans votre réseau virtuel ou sur Internet. Pour bloquer le trafic vers des adresses publiques, activez [Tout acheminer](#application-routing) vers le réseau virtuel. Lorsque l’option **Tout acheminer** n’est pas activée, les groupes de sécurité réseau sont appliqués uniquement au trafic RFC1918.

Un NSG appliqué à votre sous-réseau d’intégration est en vigueur indépendamment des tables de routage appliquées à votre sous-réseau d’intégration.

Les règles de trafic entrant dans un groupe de sécurité réseau ne s’appliquent pas à votre application, car l’intégration au réseau virtuel n’a d’incidence que sur le trafic sortant depuis votre application. Pour contrôler le trafic entrant vers votre application, utilisez la fonctionnalité Restrictions d’accès.

### <a name="service-endpoints"></a>Points de terminaison de service

L’intégration au réseau virtuel régional vous permet d’atteindre les services Azure sécurisés avec les points de terminaison de service. Pour accéder à un service sécurisé par un point de terminaison de service, procédez comme suit :

1. Configurez l’intégration au réseau virtuel régional avec votre application web pour vous connecter à un sous-réseau spécifique pour l’intégration.
1. Accédez au service de destination et configurez des points de terminaison de service sur le sous-réseau d’intégration.

### <a name="private-endpoints"></a>Instances Private Endpoint

Si vous souhaitez effectuer des appels vers des [points de terminaison privés](./networking/private-endpoint.md), assurez-vous que vos recherches DNS seront résolues sur le point de terminaison privé. Vous pouvez appliquer ce comportement de l’une des façons suivantes :

* Intégrer à des zones privées Azure DNS Si votre réseau virtuel n’a pas de serveur DNS personnalisé, cette intégration est effectuée automatiquement quand les zones sont liées au réseau virtuel.
* Gérer le point de terminaison privé dans le serveur DNS utilisé par votre application. Pour gérer la configuration, vous devez connaître l’adresse IP du point de terminaison privé. Pointez ensuite le point de terminaison que vous essayez d’atteindre vers cette adresse à l’aide d’un enregistrement A.
* Configurez votre propre serveur DNS pour le transfert vers des zones privées Azure DNS.

### <a name="azure-dns-private-zones"></a>Zones privées Azure DNS

Une fois votre application intégrée au réseau virtuel, elle utilise le serveur DNS avec lequel votre réseau virtuel est configuré. Si aucun DNS personnalisé n’est spécifié, il utilise le DNS par défaut Azure et toutes les zones privées liées au réseau virtuel.

### <a name="limitations"></a>Limites

Il existe certaines limitations concernant l’utilisation de l’intégration au réseau virtuel régional :

* Cette fonctionnalité est disponible à partir de toutes les unités d’échelle App Service dans Premium v2 et Premium v3. Elle est également disponible dans Standard, mais uniquement à partir d’unités d’échelle App Service plus récentes. Si vous utilisez une unité d’échelle plus ancienne, vous ne pouvez utiliser la fonctionnalité qu’à partir d’un plan App Service Premium v2. Si vous souhaitez vous assurer de pouvoir utiliser la fonctionnalité dans un plan App Service Standard, créez votre application dans un plan App Service Premium v3. Ces plans ne sont pris en charge que sur les unités d’échelle les plus récentes. Vous pouvez réduire la taille du plan si vous le souhaitez après avoir créé le plan.
* La fonctionnalité ne peut pas être utilisée par des applications de plan Isolé qui se trouvent dans un environnement App Service.
* Vous ne pouvez pas accéder aux ressources sur des connexions de peering avec des réseaux virtuels classiques.
* La fonctionnalité nécessite un sous-réseau inutilisé avec un bloc IPv4 `/28` au moins dans un réseau virtuel Azure Resource Manager.
* L’application et le réseau virtuel doivent se trouver dans la même région.
* Il n’est pas possible de définir des espaces d’adressage IPv6 pour le réseau virtuel d’intégration.
* Le sous-réseau d’intégration peut être utilisé par un seul plan App Service.
* Vous ne pouvez pas supprimer un réseau virtuel avec une application intégrée. Supprimez l’intégration avant de supprimer le réseau virtuel.
* Vous ne pouvez disposer que d’une seule intégration au réseau virtuel régional par plan App Service. Plusieurs applications d’un même plan App Service peuvent utiliser le même réseau virtuel.
* Vous ne pouvez pas changer l’abonnement d’une application ou d’un plan quand une application utilise l’intégration au réseau virtuel régional.

## <a name="gateway-required-virtual-network-integration"></a>Intégration au réseau virtuel avec passerelle obligatoire

L’intégration au réseau virtuel avec passerelle obligatoire prend en charge la connexion à un réseau virtuel d’une autre région ou à un réseau virtuel classique. Intégration au réseau virtuel avec passerelle obligatoire :

* Permet à une application de se connecter à un seul réseau virtuel à la fois.
* Permet d’intégrer jusqu’à cinq réseaux virtuels dans un plan App Service.
* Permet à un même réseau virtuel d’être utilisé par plusieurs applications dans le cadre d’un plan App Service sans impact sur le nombre total d’applications pouvant être utilisées par un plan App Service. Si vous avez six applications qui utilisent le même réseau virtuel dans le cadre d’un plan App Service, cela compte pour un réseau virtuel utilisé.
* Le contrat SLA sur la passerelle peut avoir un impact sur le [contrat SLA](https://azure.microsoft.com/support/legal/sla/) global.
* Permet à vos applications d’utiliser le DNS avec lequel le réseau virtuel est configuré.
* Nécessite une passerelle de réseau virtuel basée sur le routage et configurée avec un VPN point à site SSTP pour pouvoir être connectée à une application.

Vous ne pouvez pas utiliser l’intégration au réseau virtuel avec passerelle obligatoire :

* Avec un réseau virtuel connecté avec ExpressRoute.
* À partir d’une application Linux.
* À partir d’un [conteneur Windows](./quickstart-custom-container.md).
* Pour accéder à des ressources sécurisées par un point de terminaison de service.
* Avec une passerelle de coexistence qui prend en charge à la fois les connexions ExpressRoute et les VPN de point à site ou site à site.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Configurer une passerelle dans votre réseau virtuel Azure

Pour créer une passerelle :

1. [Créez la passerelle VPN et le sous-réseau](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw). Sélectionnez un type de VPN basé sur les routes.

1. [Définissez les adresses de point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool). Si la passerelle n’est pas dans la référence (SKU) de base, c’est que IKEV2 doit être désactivé dans la configuration de point à site et que SSTP doit être sélectionné. L’espace d’adressage de point à site doit se situer dans les blocs d’adresses RFC 1918, à savoir 10.0.0.0/8, 172.16.0.0/12 et 192.168.0.0/16.

Si vous créez la passerelle pour l’utiliser avec l’intégration au réseau virtuel avec passerelle obligatoire, vous n’avez pas besoin de charger un certificat. La création de la passerelle peut prendre 30 minutes. Vous ne pourrez pas intégrer votre application à votre réseau virtuel tant que la passerelle n’est pas créée.

### <a name="how-gateway-required-virtual-network-integration-works"></a>Fonctionnement de l’intégration au réseau virtuel avec passerelle obligatoire

L’intégration au réseau virtuel avec passerelle obligatoire s’appuie sur la technologie VPN de point à site. Les VPN de point à site limitent l’accès réseau à la seule machine virtuelle qui héberge l’application. Les applications sont limitées au seul envoi du trafic vers Internet, via des connexions hybrides ou via l’intégration au réseau virtuel. Lorsque votre application est configurée avec le portail pour utiliser l’intégration au réseau virtuel avec passerelle obligatoire, une négociation complexe est managée en votre nom pour créer et attribuer des certificats sur la passerelle et du côté de l’application. Au final, les Workers utilisés pour héberger vos applications peuvent se connecter directement à la passerelle de réseau virtuel, dans le réseau virtuel sélectionné.

:::image type="content" source="./media/overview-vnet-integration/vnetint-how-gateway-works.png" alt-text="Diagramme illustrant le fonctionnement de l’intégration au réseau virtuel avec passerelle obligatoire.":::

### <a name="access-on-premises-resources"></a>Accès aux ressources locales

Les applications peuvent accéder aux ressources locales en s’intégrant à des réseaux virtuels qui ont des connexions site à site. Si vous utilisez l’intégration au réseau virtuel avec passerelle obligatoire, mettez à jour les routes de votre passerelle VPN locale avec vos blocs d’adresses de point à site. Lors de la configuration initiale de ce VPN site à site, les scripts utilisés pour le configurer doivent définir les routes correctement. Si vous ajoutez des adresses de point à site après avoir créé votre VPN de site à site, vous devez mettre à jour les routes manuellement. La procédure détaillée dépend de la passerelle et n’est pas décrite ici. Vous ne pouvez pas configurer BGP avec une connexion VPN de site à site.

Aucune configuration supplémentaire n’est nécessaire pour permettre à la fonctionnalité d’intégration au réseau virtuel régional d’accéder à vos ressources locales via votre réseau virtuel. Vous devez simplement connecter votre réseau virtuel aux ressources locales à l’aide d’ExpressRoute ou d’un VPN de site à site.

> [!NOTE]
> La fonctionnalité d’intégration au réseau virtuel avec passerelle obligatoire n’intègre pas une application à un réseau virtuel doté d’une passerelle ExpressRoute. Même si la passerelle ExpressRoute est configurée en [mode de coexistence](../expressroute/expressroute-howto-coexist-resource-manager.md), l’intégration au réseau virtuel ne fonctionnera pas. Si vous avez besoin d’accéder à des ressources via une connexion ExpressRoute, utilisez la fonctionnalité d’intégration au réseau virtuel régional ou un [environnement ASE (App Service Environment)](./environment/intro.md), qui s’exécute dans votre réseau virtuel.

### <a name="peering"></a>Peering

Si vous utilisez le peering avec l’intégration au réseau virtuel régional, vous n’avez pas besoin d’effectuer d’autres tâches de configuration.

Si vous utilisez l’intégration au réseau virtuel avec passerelle obligatoire et le peering, vous devez configurer quelques éléments supplémentaires. Pour configurer le peering afin qu’il fonctionne avec votre application :

1. Ajoutez une connexion de peering sur le réseau virtuel auquel votre application se connecte. Lors de l’ajout de la connexion de peering, activez **Autoriser l’accès au réseau virtuel** et sélectionnez **Autoriser le trafic transféré** et **Autoriser le transit par passerelle**.
1. Ajoutez une connexion de peering sur le réseau virtuel à appairer au réseau virtuel auquel vous êtes connecté. Lors de l’ajout de la connexion de peering sur le réseau virtuel de destination, activez **Autoriser l’accès au réseau virtuel** et sélectionnez **Autoriser le trafic transféré** et **Autoriser les passerelles distantes**.
1. Accédez à **Plan App Service** > **Mise en réseau** > **Intégration au réseau virtuel** dans le portail. Sélectionnez le réseau virtuel auquel votre application se connecte. Dans la section Routage, ajoutez la plage d’adresses du réseau virtuel qui est appairé au réseau virtuel auquel votre application est connectée.

## <a name="manage-virtual-network-integration"></a>Gérer l’intégration au réseau virtuel

Les connexions et déconnexions avec un réseau virtuel se font au niveau de l’application. Les opérations qui peuvent affecter l’intégration au réseau virtuel entre plusieurs applications s’effectuent au niveau du plan App Service. Dans le portail, sous l’application > **Mise en réseau** > **Intégration au réseau virtuel**, vous pouvez obtenir des détails sur votre réseau virtuel. Vous pouvez consulter des informations similaires au niveau du plan App Service dans le portail **Plan App Service** > **Mise en réseau** > **Intégration au réseau virtuel**.

La seule opération que vous pouvez effectuer dans la vue d’application de votre instance d’intégration au réseau virtuel consiste à déconnecter votre application du réseau virtuel auquel elle est actuellement connectée. Pour déconnecter votre application d’un réseau virtuel, sélectionnez **Déconnecter**. Votre application est redémarrée quand vous vous déconnectez d’un réseau virtuel. La déconnexion ne change pas votre réseau virtuel. Le sous-réseau ou la passerelle ne sont pas supprimés. Donc, si vous souhaitez supprimer votre réseau virtuel, déconnectez d’abord votre application du réseau virtuel, puis supprimez les ressources qu’il contient, par exemple les passerelles.

L’interface utilisateur de l’intégration au réseau virtuel du plan App Service vous montre toutes les intégrations au réseau virtuel utilisées par les applications de votre plan App Service. Pour afficher les détails de chaque réseau virtuel, sélectionnez le réseau virtuel qui vous intéresse. Vous pouvez effectuer deux actions ici pour l’intégration au réseau virtuel avec passerelle obligatoire :

* **Synchroniser le réseau** : l’opération de synchronisation du réseau concerne seulement la fonctionnalité d’intégration au réseau virtuel avec passerelle obligatoire. L’exécution d’une opération de synchronisation réseau garantit que vos certificats et les informations réseau sont synchronisés. Si vous ajoutez ou modifiez le DNS de votre réseau virtuel, effectuez une opération de synchronisation réseau. Cette opération redémarre toutes les applications qui utilisent ce réseau virtuel. Cette opération ne fonctionne pas si vous utilisez une application et un réseau virtuel appartenant à différents abonnements.
* **Ajouter des routes** : l’ajout de routes achemine le trafic sortant vers votre réseau virtuel.

L’adresse IP privée assignée à l’instance est exposée via la variable d’environnement WEBSITE_PRIVATE_IP. L’interface utilisateur de la console Kudu affiche également la liste des variables d’environnement disponibles pour l’application web. Cette adresse IP est attribuée à partir de la plage d’adresses du sous-réseau intégré. Pour l’intégration au réseau virtuel régional, la valeur de WEBSITE_PRIVATE_IP est une adresse IP issue de la plage d’adresses du sous-réseau délégué. Pour l’intégration au réseau virtuel avec passerelle obligatoire, la valeur est une adresse IP issue de la plage d’adresses du pool d’adresses de point à site configuré sur la passerelle de réseau virtuel. Cette adresse IP sera utilisée par l’application web pour se connecter aux ressources via le réseau virtuel Azure.

> [!NOTE]
> La valeur de WEBSITE_PRIVATE_IP est appelée à changer. Toutefois, il s’agit d’une adresse IP de la plage d’adresses du sous-réseau d’intégration ou de la plage d’adresses de point à site. De ce fait, vous devrez autoriser l’accès depuis toutes les adresses de la plage.
>

### <a name="gateway-required-virtual-network-integration-routing"></a>Routage de l’intégration au réseau virtuel avec passerelle obligatoire

Les routes définies dans votre réseau virtuel sont utilisées pour diriger le trafic dans votre réseau virtuel à partir de votre application. Pour envoyer davantage de trafic sortant vers le réseau virtuel, ajoutez ces blocs d’adresses ici. Cette fonctionnalité n’opère qu’avec l’intégration au réseau virtuel avec passerelle obligatoire. Les tables de route n’ont pas d’incidence sur le trafic de votre application lors de l’utilisation de l’intégration au réseau virtuel avec passerelle obligatoire, comme c’est le cas pour l’intégration au réseau virtuel régional.

### <a name="gateway-required-virtual-network-integration-certificates"></a>Certificats d’intégration au réseau virtuel avec passerelle obligatoire

Quand l’intégration au réseau virtuel avec passerelle obligatoire est activée, un échange de certificats est nécessaire pour garantir la sécurité de la connexion. En plus des certificats, la configuration DNS, les routes et d’autres éléments similaires décrivent le réseau.

Si des certificats ou des informations du réseau sont modifiés, sélectionnez **Synchroniser le réseau**. Lorsque vous sélectionnez **Synchroniser le réseau**, la connectivité entre votre application et votre réseau virtuel est brièvement interrompue. Votre application n’est pas redémarrée, mais la perte de connectivité peut altérer le fonctionnement correct de votre site.

## <a name="pricing-details"></a>Détails de la tarification

L’utilisation de la fonctionnalité d’intégration au réseau virtuel régional ne s’accompagne d’aucuns frais d’utilisation supplémentaires au-delà des frais liés au niveau tarifaire du plan App Service.

Trois types de frais sont appliqués en cas d’utilisation de la fonctionnalité d’intégration au réseau virtuel avec passerelle obligatoire :

* **Frais liés au niveau tarifaire du plan App Service** : vos applications doivent se trouver dans un plan App Service Standard, Premium, Premium v2 ou Premium v3. Pour plus d’informations sur ces frais, consultez [Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/).
* **Coût de transfert des données** : les sorties de données engendrent des coûts, même si le réseau virtuel est dans le même centre de données. Ces coûts sont décrits dans [Détails de tarification des transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).
* **Coûts de la passerelle VPN** : La passerelle de réseau virtuel nécessaire pour la connexion VPN de point à site engendre un coût. Pour plus d’informations, consultez [Tarification Passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/).

## <a name="troubleshooting"></a>Dépannage

> [!NOTE]
> L’intégration au réseau virtuel n’est pas prise en charge pour les scénarios Docker Compose dans App Service.
> Les restrictions d’accès sont ignorées s’il y a un point de terminaison privé.

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]
