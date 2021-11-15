---
title: 'Démarrage rapide : Activer l’Autogestion Azure pour machines virtuelles dans le portail Azure'
description: Découvrez comment activer rapidement l’Autogestion pour une machine virtuelle nouvelle ou existante dans le portail Azure.
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom: mode-portal
ms.openlocfilehash: ab9a6f5ff85fe0b5b2d1df4d2a3f64bb4cb21207
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465674"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Démarrage rapide : Activer l’Autogestion Azure pour machines virtuelles dans le portail Azure

Utilisez l’Autogestion Azure dans le portail Azure afin d’activer la gestion automatique sur une machine virtuelle nouvelle ou existante.


## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avant de commencer.

> [!NOTE]
> Les comptes associés à un essai gratuit n’ont pas accès aux machines virtuelles utilisées dans ce tutoriel. Veuillez passer à un abonnement avec paiement à l’utilisation.

> [!IMPORTANT]
> Vous devez disposer du rôle **Contributeur** sur le groupe de ressources contenant vos machines virtuelles pour activer Automanage. Si vous activez Automanage pour la première fois sur un abonnement, vous devez disposer des autorisations suivantes sur votre abonnement : rôles **Propriétaire** ou **Contributeur** ainsi que les rôles **Administrateur de l’accès utilisateur**.


## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com)

## <a name="enable-automanage-on-existing-machines"></a>Activer Automanage sur les machines existantes

1. Dans la barre de recherche, recherchez et sélectionnez **Automanage - Azure machine best practices** (Automanage - Bonnes pratiques pour les machines virtuelles Azure).

2. Sélectionnez l’option **Activer sur la machine virtuelle existante**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Activation sur la machine virtuelle existante":::

4. Sous **Profil de configuration**, sélectionnez votre type de profil : **Meilleures pratiques Azure – Production**, **Meilleures pratiques Azure – Dev/Test** ou [**Profil personnalisé**](virtual-machines-custom-profile.md).

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Sélectionnez les environnements.":::

   Cliquez sur **Afficher les profils de meilleure pratique** pour voir les différences entre les environnements.
    1. Sélectionnez un environnement dans la liste déroulante : *Dev/Test* à des fins de test ou *Production* pour la production.
    1. Cliquez sur le bouton **OK** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Parcourir l’environnement de production.":::

5. Dans le panneau **Sélectionner les machines** :
    1. Filtrez la liste sur votre **abonnement** et votre **groupe de ressources**.
    1. Cochez la case des machines virtuelles que vous souhaitez intégrer.
    1. Cliquez sur le bouton **Sélectionner**.
    > [!NOTE]
    > Vous pouvez sélectionner des machines virtuelles Azure et des serveurs avec Azure Arc.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Sélectionnez une machine virtuelle existante dans la liste des machines virtuelles disponibles.":::


6. Cliquez sur le bouton **Activer**.


## <a name="disable-automanage-for-vms"></a>Désactiver l’Autogestion pour machines virtuelles

Vous pouvez cesser rapidement d’utiliser l’Autogestion Azure pour machines virtuelles en désactivant la gestion automatique.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Désactivation de l’Autogestion sur une machine virtuelle":::

1. Accédez à la page **Autogestion – Bonnes pratiques pour les machines virtuelles** qui liste toutes vos machines virtuelles qui sont gérées automatiquement.
1. Cochez la case des machines virtuelles que vous souhaitez désactiver.
1. Cliquez sur le bouton **Désactiver la gestion automatique**.
1. Lisez attentivement le message dans la fenêtre contextuelle qui s’affiche avant d’accepter de **Désactiver**.


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez créé un nouveau groupe de ressources pour essayer l’Autogestion Azure pour machines virtuelles, et que vous n’en avez plus besoin, vous pouvez le supprimer. La suppression du groupe supprime également la machine virtuelle, ainsi que toutes les autres ressources se trouvant dans le groupe.

L’Autogestion Azure crée des groupes de ressources par défaut dans lesquels stocker les ressources. Vérifiez les groupes de ressources qui suivent la convention de nommage « DefaultResourceGroupRegionName » ou « AzureBackupRGRegionName » afin de nettoyer toutes les ressources.

1. Sélectionnez le **groupe de ressources**.
1. Dans la page du groupe de ressources, sélectionnez **Supprimer**.
1. Lorsque vous y êtes invité, confirmez le nom du groupe de ressources, puis sélectionnez **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez activé l’Autogestion Azure pour machines virtuelles.

Découvrez la manière dont vous pouvez créer et appliquer des profils personnalisés lorsque vous activez Automanage sur votre machine virtuelle.

> [!div class="nextstepaction"]
> [Profils personnalisés Azure Automanage](virtual-machines-custom-profile.md)
