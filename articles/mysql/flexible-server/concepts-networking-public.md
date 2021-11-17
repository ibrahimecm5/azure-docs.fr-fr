---
title: Vue d’ensemble de l’accès réseau public - Serveur flexible Azure Database pour MySQL
description: Découvrir l’option de réseau avec accès public dans l’option de déploiement de serveur flexible pour Azure Database pour MySQL
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: a8d2e1e6ef3f1aa2dcb736336232b1b149eb7b89
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438194"
---
# <a name="public-network-access-for-azure-database-for-mysql---flexible-server"></a>Accès réseau public pour Azure Database pour MySQL – Serveur flexible

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Cet article décrit les options de connectivité publique pour votre serveur. Vous allez découvrir en détail les concepts pour la création d’un serveur flexible Azure Database pour MySQL accessible de façon sécurisée via Internet.

## <a name="public-access-allowed-ip-addresses"></a>Accès public (adresses IP autorisées)

La configuration de l’accès public sur votre serveur flexible permettra d’accéder à ce serveur via un point de terminaison public, c’est-à-dire que le serveur sera accessible via Internet. Le point de terminaison public est une adresse DNS résolvable publiquement. L’expression « adresses IP autorisées » fait référence à une plage d’adresses IP que vous choisissez d’autoriser à accéder à votre serveur. Ces autorisations sont appelées règles de pare-feu.

Les caractéristiques de la méthode d’accès public sont les suivantes :

* Seules les adresses IP que vous autorisez peuvent accéder à votre serveur flexible MySQL. Par défaut, aucune adresse IP n’est autorisée. Vous pouvez ajouter des adresses IP lors de la création du serveur ou par la suite.
* Votre serveur MySQL porte un nom DNS résolvable publiquement.
* Votre serveur flexible ne se trouve pas dans l’un de vos réseaux virtuels Azure.
* Le trafic réseau à destination et en provenance de votre serveur ne passe pas par un réseau privé. Le trafic utilise les chemins Internet généraux.

### <a name="firewall-rules"></a>Règles de pare-feu

L’octroi d’une autorisation à une adresse IP s’appelle une règle de pare-feu. Si une tentative de connexion provient d’une adresse IP que vous n’avez pas autorisée, le client d’origine voit une erreur.

### <a name="allowing-all-azure-ip-addresses"></a>Autorisation de toutes les adresses IP Azure

Si aucune adresse IP sortante fixe n’est disponible pour votre service Azure, vous pouvez envisager d’activer les connexions à partir de toutes les adresses IP du centre de données Azure.

> [!IMPORTANT]
> L’option **Autoriser l’accès public à partir des ressources et services Azure dans Azure** permet de configurer le pare-feu afin d’autoriser toutes les connexions en provenance d’Azure, notamment les connexions à partir des abonnements d’autres clients. Lorsque vous sélectionnez cette option, vérifiez que votre connexion et vos autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.

Découvrez comment activer et gérer l’accès public (adresses IP autorisées) à l’aide du [portail Azure](how-to-manage-firewall-portal.md) ou d’[Azure CLI](how-to-manage-firewall-cli.md).

### <a name="troubleshooting-public-access-issues"></a>Résolution des problèmes d’accès public

Considérez les points suivants quand l’accès au service de serveur Microsoft Azure Database pour MySQL présente un comportement anormal :

* **Les modifications apportées à la liste d’approbation n’ont pas encore pris effet :** jusqu’à cinq minutes peuvent s’écouler avant que les modifications apportées à la configuration du pare-feu du serveur de base de données Azure pour MySQL ne soient effectives.

* **Échec de l’authentification :** Si un utilisateur n’a pas d’autorisations sur le serveur Azure Database pour MySQL ou que le mot de passe est incorrect, la connexion au serveur Azure Database pour MySQL est refusée. La création d’un paramètre de pare-feu permet uniquement aux clients de tenter de se connecter à votre serveur. Chaque client doit quand même fournir les informations d’identification de sécurité nécessaires.

* **Adresse IP de client dynamique :** Si vous avez une connexion Internet avec un adressage IP dynamique et que le pare-feu demeure infranchissable, vous pouvez essayer une des solutions suivantes :

  * Demandez à votre fournisseur de services Internet (ISP) la plage d’adresses IP affectée à vos ordinateurs clients qui accèdent au serveur Azure Database pour MySQL, puis ajoutez cette plage à une règle de pare-feu.
  * Obtenez un adressage IP statique à la place pour vos ordinateurs clients, puis ajoutez l’adresse IP statique en tant que règle de pare-feu.

* **La règle de pare-feu n’est pas disponible pour le format IPv6 :** Les règles de pare-feu doivent être au format IPv4. Si vous spécifiez des règles de pare-feu au format IPv6, l’erreur de validation s’affiche.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment activer l’accès public (adresses IP autorisées) à l’aide du [portail Azure](how-to-manage-firewall-portal.md) ou d’[Azure CLI](how-to-manage-firewall-cli.md)
* Découvrir comment [utiliser le protocole TLS](how-to-connect-tls-ssl.md)
