---
title: Se connecter à un réseau virtuel interne à l’aide de la Gestion des API Azure
description: Découvrez comment installer et configurer la Gestion des API Azure dans un réseau virtuel en mode interne.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/10/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6dc297736403b6124b27f34462ffaee0bfa4cf4a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845776"
---
# <a name="connect-to-a-virtual-network-in-internal-mode-using-azure-api-management"></a>Se connecter à un réseau virtuel en mode interne à l’aide de Gestion des API Azure 
Grâce aux réseaux virtuels (VNET) d’Azure, Gestion des API Azure peut gérer des API inaccessibles sur Internet au moyen de plusieurs technologies VPN permettant d’établir la connexion. Vous pouvez déployer la Gestion des API via le mode [externe](./api-management-using-with-vnet.md) ou interne. Pour connaître les options de connectivité, les conditions requises et les considérations liées aux réseaux virtuels, consultez [Utilisation d’un réseau virtuel avec Gestion des API Azure](virtual-network-concepts.md).

Dans cet article, vous allez apprendre à déployer la Gestion des API en mode VNET interne. Dans ce mode, vous pouvez uniquement afficher les points de terminaison de service suivants dans un réseau virtuel dont vous contrôlez l’accès.
* La passerelle API
* Le portail des développeurs
* La gestion directe
* Git 

> [!NOTE]
> Aucun point de terminaison de service n’est inscrit sur le DNS public. Les points de terminaison de service restent inaccessibles à moins de [configurer le DNS](#dns-configuration) pour le réseau virtuel.

Utilisez la Gestion des API en mode interne pour :

* Rendre les API hébergées dans votre centre de données privé accessibles de l’extérieur en toute sécurité à des tiers à l’aide de connexions Azure VPN ou Azure ExpressRoute.
* Activer les scénarios de cloud hybride en exposant vos API cloud et sur site par le biais d’une passerelle commune.
* Gérer vos API hébergées dans plusieurs localisations géographiques à l’aide d’un seul point de terminaison de passerelle.

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-vnet-internal.png" alt-text="Se connecter à un réseau virtuel interne VNET":::

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prérequis

Certains prérequis varient en fonction de la version (`stv2` ou `stv1`) de la [plateforme de calcul](compute-infrastructure.md) de votre instance Gestion des API. 

> [!TIP]
> Quand vous utilisez le portail pour créer ou mettre à jour la configuration réseau de votre instance Gestion des API, l’instance est hébergée sur la plateforme de calcul `stv2`.

### <a name="stv2"></a>[stv2](#tab/stv2)

+ **Une instance APIM.** Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

* **Un réseau et un sous-réseau virtuels** dans la même région et le même abonnement que votre instance Gestion des API. Le sous-réseau peut contenir d’autres ressources Azure.

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

   > [!NOTE]
   > Lorsque vous déployez un service Gestion des API dans un réseau virtuel interne sur la plateforme `stv2`, il est hébergé derrière un équilibreur de charge interne du [niveau tarifaire Standard](../load-balancer/skus.md) à l’aide de la ressource d’IP publique.

### <a name="stv1"></a>[stv1](#tab/stv1)

+ **Une instance APIM.** Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

* **Un réseau et un sous-réseau virtuels** dans la même région et le même abonnement que votre instance Gestion des API.

    Le sous-réseau doit être dédié aux instances Gestion des API. Toute tentative de déploiement d’une instance de Gestion des API Azure sur un sous-réseau du réseau virtuel Resource Manager qui contient d’autres ressources entraîne l’échec du déploiement.

   > [!NOTE]
   > Lorsque vous déployez un service Gestion des API dans un réseau virtuel interne sur la plateforme `stv1`, il est hébergé derrière un équilibreur de charge interne du [niveau tarifaire De base](../load-balancer/skus.md).

---

## <a name="enable-vnet-connection"></a>Activer la connexion au réseau virtuel

### <a name="enable-vnet-connectivity-using-the-azure-portal-stv2-platform"></a>Activer la connectivité aux réseaux virtuels à l’aide du portail Azure (plateforme `stv2`)

1. Accédez au [portail Azure](https://portal.azure.com) pour rechercher votre instance Gestion des API. Recherchez et sélectionnez **Services Gestion des API**.
1. Choisissez votre instance Gestion des API.
1. Sélectionnez **Réseau virtuel**.
1. Sélectionnez le type d’accès **Interne**.
1. Dans la liste des localisations (régions) où votre service Gestion des API est provisionné : 
    1. Choisissez un **Emplacement**.
    1. Sélectionnez le **Réseau virtuel**, le **Sous-réseau** et l’**Adresse IP**. 
        * La liste des réseaux virtuels contient les réseaux virtuels Resource Manager, disponibles dans vos abonnements Azure, qui sont installés dans la région que vous configurez.
1. Sélectionnez **Appliquer**. La page **Réseau virtuel** de votre instance de Gestion des API est mise à jour avec vos nouveaux choix de réseau virtuel et de sous-réseau.
   :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png" alt-text="Configurer un réseau virtuel interne dans Portail Azure":::
1. Poursuivez la configuration des paramètres de réseau virtuel pour les autres emplacements de votre instance de Gestion des API.
1. Dans la barre de navigation supérieure, sélectionnez **Enregistrer**, puis **Appliquer la configuration réseau**.

    La mise à jour de l’instance Gestion des API peut prendre de 15 à 45 minutes.

Une fois le déploiement réussi, vous devez voir l’adresse IP virtuelle **privée** et l’adresse IP virtuelle **publique** de votre service Gestion des API s’afficher dans le panneau **Vue d’ensemble**. Pour plus d’informations sur les adresses IP, consultez la section [Routage](#routing) dans cet article.

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png" alt-text="Adresses IP publiques et privées dans Portail Azure"::: 

> [!NOTE]
> L’URL de la passerelle n’étant pas inscrite sur le DNS public, la console de test disponible sur le portail Azure ne fonctionnera pas pour un service déployé sur un réseau virtuel **Interne**. À la place, vous devez utiliser la console de test fournie sur le **Portail des développeurs**.

### <a name="enable-connectivity-using-a-resource-manager-template"></a>Activer la connectivité à l’aide d’un modèle Resource Manager

#### <a name="api-version-2021-01-01-preview-stv2-platform"></a>API version 2021-01-01-preview (plateforme `stv2`)

* [Modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet-publicip) Azure Resource Manager

     [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet-publicip%2Fazuredeploy.json)

#### <a name="api-version-2020-12-01-stv1-platform"></a>API version 2020-12-01 (plateforme `stv1`)

* [Modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet) Azure Resource Manager

     [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

### <a name="enable-connectivity-using-azure-powershell-cmdlets-stv1-platform"></a>Activer la connectivité à l’aide de cmdlets Azure PowerShell (plateforme `stv1`)

[Créez](/powershell/module/az.apimanagement/new-azapimanagement) ou [mettez à jour](/powershell/module/az.apimanagement/update-azapimanagementregion) une instance Gestion des API dans un réseau virtuel.

## <a name="dns-configuration"></a>Configuration DNS

En mode VNET externe, Azure gère le DNS. En mode VNET interne, vous devez gérer votre propre DNS. Nous recommandons les actions suivantes :
1. Configurez une [zone privée Azure DNS](../dns/private-dns-overview.md).
1. Liez la zone privée Azure DNS au réseau virtuel dans lequel vous avez déployé votre service Gestion des API. 

Apprenez à [configurer une zone privée dans Azure DNS](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> Le service Gestion des API n’écoute pas les requêtes sur ses adresses IP. Il répond uniquement aux demandes pour le nom d’hôte configuré sur ses points de terminaison de service. Ces points de terminaison sont les suivants :
> * Passerelle API
> * Le portail Azure
> * Le portail des développeurs
> * Point de terminaison de la gestion directe
> * Git

### <a name="access-on-default-host-names"></a>Accès sur les noms d’hôtes par défaut
Lorsque vous créez un service Gestion des API (`contosointernalvnet`, par exemple), les points de terminaison de service suivants sont configurés par défaut :

| Point de terminaison | Configuration du point de terminaison |
| ----- | ----- |
| API Gateway | `contosointernalvnet.azure-api.net` |
| Portail des développeurs | `contosointernalvnet.portal.azure-api.net` |
| Le nouveau portail des développeurs | `contosointernalvnet.developer.azure-api.net` |
| Point de terminaison de la gestion directe | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

Pour accéder à ces points de terminaison de service Gestion des API, vous pouvez créer une machine virtuelle dans un sous-réseau connecté au réseau virtuel dans lequel la Gestion des API est déployée. En supposant que l’adresse IP virtuelle interne de votre service est 10.1.0.5, vous pouvez mapper le fichier hôte comme suit. Sous Windows, ce fichier se trouve sous le chemin `%SystemDrive%\drivers\etc\hosts`. 

| Adresse IP virtuelle interne | Configuration du point de terminaison |
| ----- | ----- |
| 10.1.0.5 | `contosointernalvnet.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.portal.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.developer.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.management.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.scm.azure-api.net` |

Vous pouvez alors accéder à tous les points de terminaison de service à partir de la machine virtuelle que vous avez créée.


### <a name="access-on-custom-domain-names"></a>Accès sur des noms de domaines personnalisés

Si vous ne souhaitez pas accéder au service Gestion des API avec les noms d’hôtes par défaut : 

1. Configurez des [noms de domaines personnalisés](configure-custom-domain.md) pour tous vos points de terminaison de service, comme indiqué dans l’image suivante :

    :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png" alt-text="Configurer un nom de domaine personnalisé":::

2. Créez des enregistrements dans votre serveur DNS pour accéder aux points de terminaison accessibles depuis votre réseau virtuel.

## <a name="routing"></a>Routage

Les adresses IP virtuelles suivantes sont configurées pour une instance Gestion des API dans un réseau virtuel interne. En savoir plus sur les [adresses IP de Gestion des API](api-management-howto-ip-addresses.md).

| Adresse IP virtuelle | Description |
| ----- | ----- |
| **Adresse IP virtuelle privée** | Adresse IP à charge équilibrée à partir de la plage de sous-réseaux de l’instance Gestion des API (DIP), par le biais de laquelle vous pouvez accéder à la passerelle API, au portail des développeurs, à la gestion et aux points de terminaison Git.<br/><br/>Inscrivez cette adresse auprès des serveurs DNS utilisés par le réseau virtuel.     |  
| **Adresse IP virtuelle publique** | Utilisée *uniquement* pour le trafic du plan de contrôle vers le point de terminaison de gestion sur le port 3443. Peut être verrouillée sur l’étiquette de service [GestionApi][ServiceTags]. | 

Les adresses IP privées et publiques à charge équilibrée se trouvent dans le panneau **Vue d’ensemble** sur le portail Azure.

> [!NOTE]
> La ou les adresses IP virtuelles de l’instance Gestion des API sont modifiées quand :
> * Le réseau virtuel est activé ou désactivé. 
> * La Gestion des API passe du mode de réseau virtuel **Externe** au mode **Interne**, ou vice versa.
> * Les paramètres de [redondance de Zone](zone-redundancy.md) sont activés, mis à jour ou désactivés dans une région pour votre instance (référence SKU Premium uniquement).
>
> Vous aurez peut-être besoin de mettre à jour les inscriptions DNS, les règles d’acheminement et les listes de restriction d’IP au sein du réseau virtuel.

### <a name="vip-and-dip-addresses"></a>Adresses IP virtuelles et adresses DIP

Les adresses DIP seront attribuées à chaque machine virtuelle sous-jacente du service et utilisées pour accéder aux ressources *au sein* du réseau virtuel. Une adresse IP virtuelle est utilisée pour accéder aux ressources *situées en dehors* du réseau virtuel. Si une restriction d’IP liste des ressources sécurisées au sein du réseau virtuel, vous devez préciser la plage entière du sous-réseau où le service Gestion des API est déployé pour accorder ou restreindre l’accès à partir du service.

En savoir plus sur la [taille de sous-réseau recommandée](virtual-network-concepts.md#subnet-size).

#### <a name="example"></a> Exemple

Si vous déployez une [unité de capacité](api-management-capacity.md) de Gestion des API dans le niveau Premium dans un réseau virtuel interne, trois adresses IP sont utilisées : une pour l’adresse IP virtuelle privée et une chacune pour les adresses DIP de deux machines virtuelles. Si vous effectuez un scale-out jusqu’à quatre unités, davantage d’adresses IP seront consommées pour les adresses DIP supplémentaires du sous-réseau.  

Si le point de terminaison de destination n’a autorisé qu’un ensemble fixe d’adresses DIP, des échecs de connexion se produiront si vous ajoutez de nouvelles unités à l’avenir. Pour cette raison, et puisque le sous-réseau est entièrement sous votre contrôle, nous recommandons d’autoriser l’ensemble du sous-réseau dans le serveur principal.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

* [Configuration du réseau lors de la configuration de Gestion des API Azure dans un réseau virtuel][Common network configuration problems]
* [Forum aux questions sur les réseaux virtuels](../virtual-network/virtual-networks-faq.md)
* [Création d’un enregistrement A dans DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags

