---
title: Vue d’ensemble des réseaux - Serveur flexible Azure Database pour MySQL
description: Découvrir les options de connectivité et de réseau de l’option de déploiement Serveur flexible pour Azure Database pour MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: 2ae111660b2faa6f5bba17e9f94b17caa2b2c642
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438193"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server"></a>Connectivité et concepts liés aux réseaux pour Azure Database pour MySQL - Serveur flexible

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Cet article présente les concepts permettant de contrôler la connectivité à votre serveur flexible Azure MySQL. Vous allez découvrir en détail les concepts de mise en réseau d’un serveur flexible Azure Database pour MySQL afin de créer un serveur dans Azure et d’y accéder en toute sécurité.

Azure Database pour MySQL – Serveur flexible prend en charge deux méthodes de configuration de la connectivité à vos serveurs :
> [!NOTE]
> Vous ne pourrez pas changer votre option réseau une fois le serveur créé.

   * **Accès privé (intégration au réseau virtuel)** [Accès privé](./concepts-networking-vnet.md) Vous pouvez déployer votre serveur flexible sur votre [réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md). Les réseaux virtuels Azure offrent des communications réseau privées et sécurisées. Les ressources incluses sur un réseau virtuel peuvent communiquer par le biais d’adresses IP privées.
   
   * **Accès public (adresses IP autorisées)** [Accès public](./concepts-networking-public.md) Votre serveur flexible est accessible par le biais d’un point de terminaison public. Le point de terminaison public est une adresse DNS résolvable publiquement. L’expression « adresses IP autorisées » fait référence à une plage d’adresses IP que vous choisissez d’autoriser à accéder à votre serveur. Ces autorisations sont appelées **règles de pare-feu**.

## <a name="choosing-a-networking-option"></a>Choix d’une option réseau

Choisissez l’**accès privé (intégration au réseau virtuel)** si vous souhaitez bénéficier des capacités suivantes :
   * Connexion à partir de ressources Azure du même réseau virtuel ou d’un [réseau virtuel appairé](../../virtual-network/virtual-network-peering-overview.md) à votre serveur flexible
   * Utilisation d’un VPN ou du service ExpressRoute pour vous connecter à partir de ressources non-Azure à votre serveur flexible
   * Aucun point de terminaison public

Choisissez l’**accès public (adresses IP autorisées)** si vous souhaitez bénéficier des capacités suivantes :
   * Connexion à partir de ressources Azure qui ne prennent pas en charge les réseaux virtuels
   * Connexion à partir de ressources situées en dehors d’Azure qui ne sont pas connectées par un VPN ou ExpressRoute 
   * Le serveur flexible a un point de terminaison public.

Les caractéristiques suivantes s’appliquent si vous choisissez d’utiliser l’option d’accès privé ou d’accès public :
* Les connexions à partir d’adresses IP autorisées ont besoin de s’authentifier auprès du serveur MySQL avec des informations d’identification valides.
* Le [chiffrement de la connexion](#tls-and-ssl) est disponible pour votre trafic réseau.
* Le serveur a un nom de domaine complet (FQDN). Pour la propriété du nom d’hôte dans les chaînes de connexion, nous vous recommandons d’utiliser le nom de domaine complet plutôt qu’une adresse IP.
* Les deux options contrôlent l’accès au niveau du serveur, pas au niveau de la base de données ni de la table. Vous pouvez utiliser les propriétés des rôles de MySQL pour contrôler l’accès à la base de données, à la table et à d’autres objets.


### <a name="unsupported-virtual-network-scenarios"></a>Scénarios de réseau virtuel non pris en charge

* Point de terminaison public (ou adresse IP publique ou DNS) : un serveur flexible déployé sur un réseau virtuel ne peut pas avoir de point de terminaison public.
* Une fois le serveur flexible déployé sur un réseau virtuel et un sous-réseau, vous ne pouvez pas le déplacer vers un autre réseau virtuel ou sous-réseau. * Une fois que le serveur flexible est déployé, vous ne pouvez pas déplacer le réseau virtuel utilisé par le serveur flexible dans un autre groupe de ressources ou un autre abonnement.
* Il est impossible d’augmenter la taille d’un sous-réseau (espaces d’adressage) une fois qu’il existe des ressources sur ce sous-réseau.
* Le passage d’un accès public à un accès privé n’est pas autorisé après la création du serveur. La méthode recommandée consiste à utiliser la restauration à un instant dans le passé.

Découvrez comment activer l’accès privé (intégration au réseau virtuel) à l’aide du [portail Azure](how-to-manage-virtual-network-portal.md) ou d’[Azure CLI](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Si vous utilisez le serveur DNS personnalisé, vous devez utiliser un redirecteur DNS pour résoudre le nom de domaine complet du serveur Azure Database pour MySQL – Serveur flexible. Consultez [Résolution de noms utilisant votre propre serveur DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) pour en savoir plus.

## <a name="hostname"></a>Nom d’hôte
Quelle que soit l’option de mise en réseau choisie, nous vous recommandons d’utiliser le FQDN `<servername>.mysql.database.azure.com` dans les chaînes de connexion lorsque vous vous connectez à votre serveur flexible. 

## <a name="tls-and-ssl"></a>TLS et SSL
Le serveur flexible Azure Database pour MySQL prend en charge la connexion de vos applications clientes au serveur MySQL en utilisant le protocole SSL (Secure Sockets Layer) avec le protocole TLS (Transport Layer Security). TLS est un protocole standard qui garantit la sécurité des connexions réseau entre votre serveur de base de données et vos applications clientes, ce qui vous permet de respecter les exigences de conformité.

Le serveur flexible Azure Database pour MySQL prend en charge par défaut les connexions chiffrées à l’aide du protocole TLS 1.2 (Transport Layer Security), donc toutes les connexions entrantes qui utilisent les protocoles TLS 1.0 et TLS 1.1 sont refusées par défaut. Vous pouvez configurer et modifier la configuration de la mise en œuvre de la connexion chiffrée ou de la version TLS sur votre serveur flexible. 

Voici les différentes configurations des paramètres SSL et TLS possibles pour votre serveur flexible :

| Scénario   | Paramètres du serveur      | Description                                    |
|------------|--------------------------------|------------------------------------------------|
|Désactiver SSL (connexions chiffrées) | require_secure_transport = OFF |Si votre application héritée ne prend pas en charge les connexions chiffrées à MySQL Server, vous pouvez désactiver l’application des connexions chiffrées à votre serveur flexible en définissant require_secure_transport = OFF.|
|Appliquer le protocole SSL avec une version TLS < 1.2 | require_secure_transport = ON et tls_version = TLSV1 ou TLSV1.1| Si votre application héritée prend en charge les connexions chiffrées mais requiert la version TLS < 1.2, vous pouvez activer les connexions chiffrées, mais configurer votre serveur flexible pour autoriser les connexions avec la version TLS (v1.0 ou v1.1) prises en charge par votre application|
|Appliquer le protocole SSL avec la version TLS version = 1.2 (configuration par défaut)|require_secure_transport = ON et tls_version = TLSV1.2| Il s’agit de la configuration par défaut recommandée pour le serveur flexible.|
|Appliquer le protocole SSL avec une version TLS = 1.3 (pris en charge avec MySQL v8.0 et versions ultérieures)| require_secure_transport = ON et tls_version = TLSV1.3| Cette fonctionnalité est utile et recommandée pour le développement de nouvelles applications|

> [!Note]
> Les modifications apportées au chiffrement SSL sur le serveur flexible ne sont pas prises en charge. Les suites de chiffrement FIPS sont appliquées par défaut quand tls_version est défini sur TLS version 1.2. Pour les versions TLS autres que la version 1.2, le chiffrement SSL est défini sur les paramètres par défaut qui sont fournis avec l’installation de MySQL Community.

Pour en savoir plus, passez en revue la rubrique sur la [connexion à l’aide du protocole SSL/TLS](how-to-connect-tls-ssl.md). 


## <a name="next-steps"></a>Étapes suivantes
* Découvrir comment activer l’accès privé (intégration au réseau virtuel) à l’aide du [portail Azure](how-to-manage-virtual-network-portal.md) ou d’[Azure CLI](how-to-manage-virtual-network-cli.md)
* Découvrir comment activer l’accès public (adresses IP autorisées) à l’aide du [portail Azure](how-to-manage-firewall-portal.md) ou d’[Azure CLI](how-to-manage-firewall-cli.md)
* Découvrir comment [utiliser le protocole TLS](how-to-connect-tls-ssl.md)
