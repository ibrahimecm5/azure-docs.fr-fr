---
title: Vue d’ensemble de l’accès réseau privé - Serveur flexible Azure Database pour MySQL
description: Découvrir l’option de réseau avec accès privé dans l’option de déploiement de serveur flexible pour Azure Database pour MySQL
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: 961e1fbf4bc87355ce036fb9a5af7a1cd85c4e76
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472710"
---
# <a name="private-network-access-for-azure-database-for-mysql---flexible-server"></a>Accès réseau privé pour Azure Database pour MySQL – Serveur flexible

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Cet article décrit l’option de connectivité privée pour le serveur flexible Azure MySQL. Vous allez découvrir les détails des concepts de réseau virtuel d’un serveur flexible Azure Database pour MySQL afin de créer un serveur de façon sécurisée dans Azure.


## <a name="private-access-vnet-integration"></a>Accès privé (intégration au réseau virtuel)

[Réseau virtuel Microsoft Azure (VNet)](../../virtual-network/virtual-networks-overview.md) est le bloc de construction fondamental pour votre réseau privé dans Azure. L’intégration de réseau virtuel (VNet) à Azure Database pour MySQL - Serveur flexible offre les avantages d’Azure en matière de sécurité et d’isolement du réseau.

L’intégration de réseau virtuel pour un serveur flexible Azure Database pour MySQL vous permet de restreindre l’accès au serveur seulement à votre infrastructure de réseau virtuel. Votre réseau virtuel peut inclure toutes vos ressources d’application et de base de données dans un même réseau virtuel ou peut s’étendre sur différents réseaux virtuels dans des régions identiques ou différentes. Une connectivité fluide entre les différents réseaux virtuels peut être établie via un [appairage](../../virtual-network/virtual-network-peering-overview.md), qui utilise l’infrastructure principale privée à faible latence et bande passante élevée de Microsoft. Les réseaux virtuels apparaissent comme un seul réseau à des fins de connectivité.

Azure Database pour MySQL - Serveur flexible prend en charge la connectivité client depuis :

* Des réseaux virtuels au sein de la même région Azure. (Réseaux virtuels appairés localement)
* Des réseaux virtuels entre des régions Azure. (Réseaux virtuels appairés globalement)

Les sous-réseaux vous permettent de segmenter le réseau virtuel en un ou plusieurs sous-réseaux, et d’allouer une partie de l’espace d’adressage du réseau virtuel sur lequel vous pouvez ensuite déployer des ressources Azure. Azure Database pour MySQL - Serveur flexible nécessite un [sous-réseau délégué](../../virtual-network/subnet-delegation-overview.md). Un sous-réseau délégué est un identificateur explicite indiquant qu’un sous-réseau peut héberger seulement des serveurs flexibles Azure Database pour MySQL. En déléguant le sous-réseau, le service obtient des autorisations explicites pour créer des ressources spécifiques au service dans le sous-réseau afin de gérer de façon fluide votre serveur flexible Azure Database pour MySQL.

Azure Database pour MySQL - Serveur flexible s’intègre à des [zones DNS privées](../../dns/private-dns-privatednszone.md) Azure pour fournir un service DNS fiable et sécurisé, et pour résoudre les noms de domaine dans un réseau virtuel sans nécessiter l’ajout d’une solution DNS personnalisée. Vous pouvez lier une zone DNS privée à un ou plusieurs réseaux virtuels en créant des [liaisons de réseau virtuel](../../dns/private-dns-virtual-network-links.md).


:::image type="content" source="./media/concepts-networking/vnet-diagram.png" alt-text="Réseau virtuel de serveur flexible MySQL":::

Dans le diagramme ci-dessus :

1. Les serveurs flexibles sont injectés dans un sous-réseau délégué ; 10.0.1.0/24 du réseau virtuel **VNet-1**.
2. Les applications qui sont déployées sur des sous-réseaux différents au sein du même réseau virtuel peuvent accéder directement aux serveurs flexibles.
3. Les applications qui sont déployées sur un autre réseau virtuel (**VNet-2**) n’ont pas d’accès direct aux serveurs flexibles. Vous devez effectuer un [appairage de réseaux virtuels de zone DNS privée](#private-dns-zone-and-vnet-peering) pour pouvoir accéder au serveur flexible.

## <a name="virtual-network-concepts"></a>Concepts de réseau virtuel

Voici quelques concepts à connaître quand vous utilisez des réseaux virtuels avec des serveurs flexibles MySQL.

* **Réseau virtuel** -

   Un réseau virtuel Azure contient un espace d’adressage IP privé configuré pour votre utilisation. Pour plus d’informations sur les réseaux virtuels Azure, consultez [Vue d’ensemble de Réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md).

    Votre réseau virtuel doit se trouver dans la même région Azure que votre serveur flexible.

* **Sous-réseau délégué** -

   Un réseau virtuel contient des sous-réseaux. Les sous-réseaux vous permettent de segmenter votre réseau virtuel en espaces d’adressage plus petits. Les ressources Azure sont déployées sur des sous-réseaux spécifiques au sein d’un réseau virtuel.

   Votre serveur flexible MySQL doit se trouver dans un sous-réseau **délégué** à l’usage de serveur flexible MySQL uniquement. Cette délégation spécifie que seuls les serveurs flexibles Azure Database pour MySQL peuvent utiliser ce sous-réseau. Aucun autre type de ressource Azure ne peut se trouver dans le sous-réseau délégué. Pour déléguer un sous-réseau, affectez à sa propriété de délégation la valeur Microsoft.DBforMySQL/flexibleServers.

* **Groupes de sécurité réseau (NSG)**

   Les règles de sécurité dans les groupes de sécurité réseau permettent de filtrer le type de trafic réseau qui peut circuler vers et depuis les interfaces réseau et les sous-réseaux de réseau virtuel. Pour plus d’informations, consultez [Vue d’ensemble des groupes de sécurité réseau](../../virtual-network/network-security-groups-overview.md).

* **Intégration d’une zone DNS privée** -

   L’intégration de zones DNS privées Azure permet de résoudre le DNS privé au sein du réseau virtuel actuel ou de tout réseau virtuel appairé dans la région où la zone DNS privée est liée.

* **Peering de réseau virtuel**

   L'appairage de réseaux virtuels vous permet de connecter en toute transparence deux ou plusieurs réseaux virtuels dans Azure. Les réseaux virtuels appairés apparaissent comme un seul réseau à des fins de connectivité. Le trafic entre les machines virtuelles des réseaux virtuels appairés utilise l'infrastructure principale de Microsoft. Le trafic entre l’application cliente et le serveur flexible dans les réseaux virtuels appairés est routé via le réseau privé de Microsoft uniquement et est dédié uniquement à ce réseau.

## <a name="using-private-dns-zone"></a>Utilisation d’une zone DNS privée

* Si vous utilisez le portail Azure ou Azure CLI pour créer des serveurs flexibles avec un réseau virtuel, une nouvelle zone DNS privée est provisionnée automatiquement pour chaque serveur dans votre abonnement en utilisant le nom du serveur fourni. Si vous voulez configurer votre propre zone DNS privée pour l’utiliser avec le serveur flexible, consultez la documentation [Vue d’ensemble du DNS privé](../../dns/private-dns-overview.md).
* Si vous utilisez l’API Azure, un modèle Azure Resource Manager (modèle ARM) ou Terraform, créez des zones DNS privées qui se terminent par `mysql.database.azure.com` et utilisez-les lors de la configuration de serveurs flexibles avec un accès privé. Pour plus d’informations, consultez la [Vue d’ensemble des zones DNS privées](../../dns/private-dns-overview.md).

   > [!IMPORTANT]
   > Les noms des zones DNS privées doivent se terminer par `mysql.database.azure.com`.

Découvrez comment créer un serveur flexible avec accès privé (intégration au réseau virtuel) dans le [portail Azure](how-to-manage-virtual-network-portal.md) ou [Azure CLI](how-to-manage-virtual-network-cli.md).

## <a name="integration-with-custom-dns-server"></a>Intégration avec un serveur DNS personnalisé

Si vous utilisez le serveur DNS personnalisé, vous devez utiliser un redirecteur DNS pour résoudre le nom de domaine complet du serveur Azure Database pour MySQL – Serveur flexible. L’adresse IP du redirecteur doit être [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md). Le serveur DNS personnalisé doit se trouver à l’intérieur du réseau virtuel ou être accessible via le paramètre de serveur DNS du réseau virtuel. Consultez [Résolution de noms utilisant votre propre serveur DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) pour en savoir plus.

## <a name="private-dns-zone-and-vnet-peering"></a>Zone DNS privée et appairage de réseaux virtuels

Les paramètres de zone DNS privée et l’appairage de réseaux virtuels sont indépendants les uns des autres. Pour plus d’informations sur la création et l’utilisation de zones DNS privées, consultez la section [Utilisation d’une zone DNS privée](concepts-networking-vnet.md#using-private-dns-zone).

Si vous voulez vous connecter au serveur flexible depuis un client provisionné dans un autre réseau virtuel de la même région ou d’une autre région, vous devez lier la zone DNS privée au réseau virtuel. Consultez la documentation [Lier le réseau virtuel](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network).

> [!NOTE]
> Les noms de zone DNS privée qui se terminent par `mysql.database.azure.com` peuvent seulement être liés.

## <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>Connexion à partir d’un serveur local vers un serveur flexible dans un réseau virtuel à l’aide d’ExpressRoute ou d’un réseau VPN

Pour les charges de travail nécessitant l’accès à un serveur flexible dans un réseau virtuel à partir d’un réseau local, vous aurez besoin [d’ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) ou d’un réseau [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) et d’un réseau virtuel [connecté localement](/azure/architecture/reference-architectures/hybrid-networking/). Une fois cette configuration en place, vous aurez besoin d’un redirecteur DNS pour résoudre le nom de serveur flexible si vous souhaitez effectuer la connexion à partir d’une application cliente (comme MySQL Workbench) exécutée sur un réseau virtuel local. Ce redirecteur DNS est chargé de résoudre toutes les requêtes DNS via un redirecteur au niveau du serveur vers le service DNS fourni par Azure [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

Pour la configurer correctement, vous avez besoin des ressources suivantes :

* Réseau local
* Serveur flexible MySQL provisionné avec accès privé (intégration au réseau virtuel)
* Réseau virtuel [connecté à un site local](/azure/architecture/reference-architectures/hybrid-networking/)
* Utiliser le redirecteur DNS [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) déployé dans Azure

Vous pouvez ensuite utiliser le nom de domaine complet (FQDN) pour vous connecter à un serveur flexible à partir de l’application cliente dans un réseau virtuel appairé ou un réseau local.

## <a name="unsupported-virtual-network-scenarios"></a>Scénarios de réseau virtuel non pris en charge

* Point de terminaison public (ou adresse IP publique ou DNS) : un serveur flexible déployé sur un réseau virtuel ne peut pas avoir de point de terminaison public.
* Une fois le serveur flexible déployé sur un réseau virtuel et un sous-réseau, vous ne pouvez pas le déplacer vers un autre réseau virtuel ou sous-réseau. Vous ne pouvez pas déplacer le réseau virtuel vers un autre groupe de ressources ou abonnement.
* Il est impossible d’augmenter la taille d’un sous-réseau (espaces d’adressage) une fois qu’il existe des ressources sur ce sous-réseau.
* Le serveur flexible ne prend pas en charge Private Link. Au lieu de cela, il utilise l’injection de réseau virtuel pour rendre le serveur flexible disponible au sein d’un réseau virtuel.

> [!NOTE]
> Si vous utilisez le serveur DNS personnalisé, vous devez utiliser un redirecteur DNS pour résoudre le nom de domaine complet du serveur Azure Database pour MySQL – Serveur flexible. Consultez [Résolution de noms utilisant votre propre serveur DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) pour en savoir plus.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment activer l’accès privé (intégration au réseau virtuel) à l’aide du [portail Azure](how-to-manage-virtual-network-portal.md) ou d’[Azure CLI](how-to-manage-virtual-network-cli.md)
* Découvrir comment [utiliser le protocole TLS](how-to-connect-tls-ssl.md)
