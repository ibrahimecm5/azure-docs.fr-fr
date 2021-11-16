---
title: Créer un profil personnalisé dans Azure Automanage pour les machines virtuelles
description: Découvrez comment créer un profil personnalisé dans Azure Automanage et sélectionner vos services et paramètres.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 821ad720645c433b9a487383e5ed9edb5bc21e61
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479183"
---
# <a name="create-a-custom-profile-in-azure-automanage-for-vms"></a>Créer un profil personnalisé dans Azure Automanage pour les machines virtuelles

Azure Automanage pour les meilleures pratiques relatives aux ordinateurs comprend des profils de meilleures pratiques par défaut qui ne peuvent pas être modifiés. Toutefois, si vous avez besoin de davantage de flexibilité, vous pouvez sélectionner et choisir l’ensemble de services et paramètres en créant un profil personnalisé.

Nous prenons en charge l’activation et la désactivation des services. Nous prenons également en charge actuellement la personnalisation de paramètres sur [Sauvegarde Azure](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) et [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avant de commencer.

> [!NOTE]
> Les comptes associés à un essai gratuit n’ont pas accès aux machines virtuelles utilisées dans ce tutoriel. Veuillez passer à un abonnement avec paiement à l’utilisation.

> [!IMPORTANT]
> L’autorisation de RBAC Azure suivante est nécessaire pour activer Automanage pour la première fois sur un abonnement : rôles **Propriétaire** ou **Contributeur**, ainsi que **Administrateur de l’accès utilisateur**.


## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Activer l’Autogestion pour machines virtuelles sur une machine virtuelle existante

1. Dans la barre de recherche, recherchez et sélectionnez **Automanage - Azure machine best practices** (Automanage - Bonnes pratiques pour les machines virtuelles Azure).

2. Sélectionnez l’option **Activer sur la machine virtuelle existante**.

3. Sous **Profil de configuration**, sélectionnez **Profil personnalisé**.

4. Sélectionnez un profil personnalisé existant dans la liste déroulante, ou créez-en un en cliquant sur **Créer**.

5. Dans le panneau **Créer un profil**, entrez les détails suivants :
    1. Nom du profil
    1. Abonnement
    1. Groupe de ressources
    1. Région

    :::image type="content" source="media\virtual-machine-custom-profile\create-custom-profile.png" alt-text="Entrez les détails du profil personnalisé.":::

6. Ajustez le profil avec les services et paramètres souhaités, puis cliquez sur **Créer**.

7. Dans le panneau **Sélectionner les machines** :
    1. Filtrez la liste des machines virtuelles selon votre **abonnement** et votre **groupe de ressources**.
    1. Cochez la case des machines virtuelles que vous souhaitez intégrer.
    1. Cliquez sur le bouton **Sélectionner**.

    :::image type="content" source="media\virtual-machine-custom-profile\existing-vm-select-machine.png" alt-text="Sélectionnez une machine virtuelle existante dans la liste des machines virtuelles disponibles.":::

    > [!NOTE]
    > Cliquez sur **Show ineligible machines** (Afficher les machines non éligibles) pour voir la liste des machines non prises en charge et la raison associée. 

## <a name="disable-automanage-for-vms"></a>Désactiver l’Autogestion pour machines virtuelles

Vous pouvez cesser rapidement d’utiliser l’Autogestion Azure pour machines virtuelles en désactivant la gestion automatique.

:::image type="content" source="media\virtual-machine-custom-profile\disable-step-1.png" alt-text="Désactivation de l’Autogestion sur une machine virtuelle":::

1. Accédez à la page **Automanage – Bonnes pratiques pour les machines Azure**, qui répertorie toutes vos machines virtuelles gérées automatiquement.
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

Pour obtenir les réponses aux questions les plus fréquemment posées, consultez notre FAQ. 

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ)](faq.yml)