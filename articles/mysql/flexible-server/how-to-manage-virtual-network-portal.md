---
title: Gérer des réseaux virtuels - Portail Azure - Azure Database pour MySQL - Serveur flexible
description: Créer et gérer des réseaux virtuels dans le serveur flexible Azure Database pour MySQL à l’aide du portail Azure
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 076dc23568c2cc6570da8f7ee1e614a225568dc5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472672"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Créer et gérer des réseaux virtuels dans le serveur flexible Azure Database pour MySQL à l’aide du portail Azure

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Le serveur flexible Azure Database pour MySQL prend en charge deux types de méthodes de connectivité réseau mutuellement exclusives pour se connecter à votre serveur flexible. Les deux options sont les suivantes :

- Accès public (adresses IP autorisées)
- Accès privé (intégration au réseau virtuel)

Dans cet article, nous allons nous concentrer sur la création d’un serveur MySQL avec l’option **Accès privé (intégration au réseau virtuel)** à l’aide du portail Azure. Avec l’option Accès privé (intégration au réseau virtuel), vous pouvez déployer votre serveur flexible sur votre propre [réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md). Les réseaux virtuels Azure offrent des communications réseau privées et sécurisées. Dans un accès privé, les connexions au serveur MySQL sont limitées à votre réseau virtuel. Pour en savoir plus à ce sujet, reportez-vous à [Accès privé (intégration au réseau virtuel)](./concepts-networking-vnet.md#private-access-vnet-integration).

>[!Note]
>Vous pouvez déployer votre serveur flexible dans un réseau virtuel et un sous-réseau au moment de la création du serveur. Une fois le serveur flexible déployé, vous ne pouvez pas le déplacer vers un autre réseau virtuel ou sous-réseau, ni vers l’*accès public (adresses IP autorisées)* .

## <a name="prerequisites"></a>Prérequis

Pour créer un serveur flexible dans un réseau virtuel, vous avez besoin des éléments suivants :

- Un [réseau virtuel](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Le réseau virtuel et le sous-réseau doivent se trouver dans la même région et le même abonnement que votre serveur flexible.

- Pour [déléguer un sous-réseau](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) à **Microsoft.DBforMySQL/flexibleServers**. Cette délégation spécifie que seuls les serveurs flexibles Azure Database pour MySQL peuvent utiliser ce sous-réseau. Aucun autre type de ressource Azure ne peut se trouver dans le sous-réseau délégué.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>Créer un serveur flexible Azure Database pour MySQL dans un réseau virtuel déjà existant

1. Sélectionnez **Créer une ressource** (+) dans l’angle supérieur gauche du portail.
2. Sélectionnez **Bases de données** > **Azure Database pour MySQL**. Vous pouvez également entrer **MySQL** dans la zone de recherche pour localiser le service.
3. Sélectionnez **Serveur flexible** comme option de déploiement.
4. Remplissez le formulaire **Informations de base**
5. Accédez à l’onglet **Réseau**.
6. Dans **Méthode de connectivité**, sélectionnez **Accès privé (intégration au réseau virtuel)** . Accédez à la section **Réseau virtuel**. Vous pouvez sélectionner un *réseau virtuel* et un *sous-réseau* déjà existants qui sont délégués à *Microsoft. DBforMySQL/flexibleServers*, ou en créer un en cliquant sur le lien *Créer un réseau virtuel*.
    > [!Note]
    > Seuls les réseaux virtuels et les sous-réseaux de la même région et du même abonnement seront listés dans la liste déroulante. </br>
    > Le sous-réseau choisi sera délégué à *Microsoft.DBforMySQL/flexibleServers*. Cela signifie que seuls les serveurs flexibles Azure Database pour MySQL peuvent utiliser ce sous-réseau.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="Intégration au réseau virtuel":::

7. Créez une **Zone DNS privée** ou sélectionnez-en une qui existe déjà.
    > [!NOTE]
    > Les noms des zones DNS privées doivent se terminer par `mysql.database.azure.com`. </br>
    > Si vous ne voyez pas l’option permettant de créer une zone DNS privée, entrez le nom du serveur sous l’onglet **De base**.</br>
    > Une fois le serveur flexible déployé sur un réseau virtuel et un sous-réseau, vous ne pouvez pas le déplacer vers un accès public (adresses IP autorisées).</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="dnsconfiguration":::
8. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre serveur flexible.
9. Sélectionnez **Créer** pour approvisionner le serveur. L'approvisionnement peut prendre quelques minutes.

## <a name="next-steps"></a>Étapes suivantes

- [Création et gestion d’un réseau virtuel de serveur flexible Azure Database pour MySQL à l’aide d’Azure CLI](./how-to-manage-virtual-network-cli.md).
- En savoir plus sur la [mise en réseau dans le serveur flexible Azure Database pour MySQL](./concepts-networking.md)
- Apprenez-en plus sur le [réseau virtuel de serveur flexible Azure Database pour MySQL](./concepts-networking-vnet.md#private-access-vnet-integration).
