---
title: Comprendre et utiliser les étendues Azure Virtual Network Manager
description: Découvrez les étendues Azure Virtual Network Manager et leurs effets sur la gestion des réseaux virtuels.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 9a5a78fc77a2b0f0891bfdd2922ad1cd771aba42
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322897"
---
# <a name="understand-and-work-with-azure-virtual-network-manager-preview-scopes"></a>Comprendre et utiliser les étendues Azure Virtual Network Manager (préversion)

Dans cet article, vous allez découvrir comment Azure Virtual Network Manager utilise le concept d’*étendue* pour permettre aux groupes d’administration ou aux abonnements d’utiliser certaines de ses fonctionnalités. Vous allez également découvrir la *hiérarchie* et la manière dont vos utilisateurs peuvent en être affectés quand vous utilisez Virtual Network Manager. 

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="network-manager"></a>Network Manager

*Network Manager* est l’objet de premier niveau des ressources enfants comme les *groupes réseau*, les *configurations* et les *règles*. 

* **Groupes réseau** - sous-ensemble de l’étendue globale à laquelle des stratégies d’administration de sécurité ou de connectivité spécifiques peuvent être appliquées.

* **Configurations** - Azure Virtual Network Manager fournit deux types de configurations : une configuration de connectivité et une configuration de sécurité. Les configurations de connectivité vous permettent de créer des topologies réseau, tandis que les configurations de sécurité vous permettent de créer un ensemble de règles que vous pouvez appliquer sur des réseaux virtuels.

* Règles - Une collection de règles est un ensemble de règles de sécurité réseau qui peuvent autoriser ou refuser le trafic réseau au niveau global de vos réseaux virtuels. 

> [!NOTE]
> Azure Virtual Network Manager demande que toutes les ressources enfants soient supprimées avant de pouvoir être supprimées.
>

## <a name="scope"></a>Étendue

Une *étendue* dans Azure Virtual Network Manager est un ensemble de ressources où des fonctionnalités peuvent être appliquées. Lorsque vous spécifiez une étendue, vous limitez l’accès à ceux pour lesquels Network Manager peut gérer les ressources. La valeur de l’étendue peut être au niveau du groupe d’administration ou au niveau de l’abonnement. Consultez [Groupes d’administration Azure](../governance/management-groups/overview.md) pour apprendre à gérer votre hiérarchie de ressources. Lorsque vous sélectionnez un groupe d’administration en tant qu’étendue, toutes les ressources enfants sont comprises dans l’étendue. 

> [!NOTE]
> Vous ne pouvez pas créer plusieurs instances Azure Virtual Network Manager avec une étendue qui chevauche la même hiérarchie et les mêmes fonctionnalités sélectionnées.
> 

## <a name="features"></a>Fonctionnalités

Les fonctionnalités sont les accès aux étendues que vous autorisez Azure Virtual Network Manager à gérer. Azure Virtual Network Manager présente actuellement deux étendues de fonctionnalités, qui sont *Connectivité* et *Administration de la sécurité*. Vous pouvez activer les deux étendues de fonctionnalités sur la même instance Virtual Network Manager. Pour plus d’informations sur chaque fonctionnalité, consultez [Connectivité](concept-connectivity-configuration.md) et [Administration de la sécurité](concept-security-admins.md).

> [!NOTE]
> Les fonctionnalités sont activées uniquement pendant le déploiement d’Azure Virtual Network Manager. Si vous déployez une instance Virtual Network Manager avec une seule étendue de fonctionnalités, vous devrez redéployer une nouvelle instance Azure Virtual Network Manager pour activer les deux fonctionnalités.
>

## <a name="hierarchy"></a>Hierarchy

Azure Virtual Network Manager permet de gérer vos ressources réseau dans une hiérarchie. Une hiérarchie signifie que plusieurs instances de Virtual Network Manager peuvent gérer des étendues qui se chevauchent et que les configurations de chaque instance de Virtual Network Manager peuvent également se chevaucher. Par exemple, vous pouvez avoir le [groupe d’administration](../governance/management-groups/overview.md) de premier niveau d’une instance Virtual Network Manager et un groupe d’administration enfant comme étendue pour une autre instance de Virtual Network Manager. En cas de conflit de configuration entre différentes instances de Virtual Network Manager qui contient la même ressource, la configuration de l’instance de Virtual Network Manager dont l’étendue est la plus élevée est celle qui est appliquée.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à créer une instance [Azure Virtual Network Manager](create-virtual-network-manager-portal.md).
- Découvrez les [groupes réseau](concept-network-groups.md).
