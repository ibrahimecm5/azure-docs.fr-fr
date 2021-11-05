---
title: Organiser des abonnements en groupes d’administration et attribuer des rôles à des utilisateurs pour Microsoft Defender pour le cloud
description: Découvrez comment organiser vos abonnements Azure en groupes d’administration dans Microsoft Defender pour le cloud, ainsi qu’attribuer des rôles à des utilisateurs au sein de votre organisation
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/11/2021
ms.custom: subject-rbac-steps, ignite-fall-2021
ms.author: memildin
ms.openlocfilehash: 9c53c626685b0035c659687454b7ad1f49acd81d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097867"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>Organiser des abonnements en groupes d’administration et attribuer des rôles à des utilisateurs

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cette page explique comment gérer la position de sécurité de votre organisation à grande échelle en appliquant des stratégies de sécurité à tous les abonnements Azure liés à votre locataire Azure Active Directory.

Pour avoir une visibilité de la sécurité de tous les abonnements liés à un locataire Azure AD, un rôle Azure doté des autorisations de lecture doit être attribué au groupe d’administration racine.

## <a name="organize-your-subscriptions-into-management-groups"></a>Organiser vos abonnements dans des groupes d’administration

### <a name="introduction-to-management-groups"></a>Introduction aux groupes d’administration

Utilisez des groupes d’administration pour gérer efficacement non seulement l’accès, les stratégies et les rapports de **groupes d’abonnements**, mais aussi l’intégralité du domaine Azure en intervenant sur le groupe d’administration racine. Vous pouvez organiser les abonnements en groupes d’administration et y appliquer vos stratégies de gouvernance. Tous les abonnements d’un groupe d’administration héritent automatiquement des stratégies appliquées à ce groupe d’administration. 

Chaque locataire Azure AD reçoit un seul groupe d’administration de niveau supérieur appelé **groupe d’administration racine**. Ce groupe d’administration racine est intégré à la hiérarchie et contient tous les groupes d’administration et abonnements. Il permet d’appliquer des stratégies globales et des attributions de rôles Azure au niveau du répertoire. 

Le groupe d’administration racine est créé automatiquement quand vous effectuez l’une des actions suivantes : 
- Ouvrez **Groupes d’administration** dans le [portail Azure](https://portal.azure.com).
- Créer un groupe d’administration avec un appel d’API.
- Créer un groupe d’administration avec PowerShell. Pour des instructions sur PowerShell, consultez [Créer des groupes d’administration pour gérer les ressources et l’organisation](../governance/management-groups/create-management-group-portal.md).

Les groupes d’administration ne sont pas obligatoires pour intégrer Defender pour le cloud, mais nous vous recommandons vivement d’en créer au moins un pour que le groupe d’administration racine soit créé. Une fois que le groupe est créé, tous les abonnements sous votre locataire Azure AD y sont liés. 

Pour une présentation détaillée des groupes d’administration, consultez l’article [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/overview.md).

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Afficher et créer des groupes d’administration dans le portail Azure

1. À partir du [portail Azure](https://portal.azure.com), utilisez la zone de recherche dans la barre supérieure pour rechercher et ouvrir **Groupes d'administration**.

    :::image type="content" source="./media/management-groups-roles/open-management-groups-service.png" alt-text="Accès à vos groupes d’administration.":::

    La liste de vos groupes d’administration s’affiche.

1. Pour créer un groupe d’administration, sélectionnez **Ajouter un groupe d’administration**, entrez les informations appropriées, puis sélectionnez **Enregistrer**.

    :::image type="content" source="media/management-groups-roles/add-management-group.png" alt-text="Ajout d’un groupe d’administration à Azure.":::

    - L’**ID du groupe d’administration** est l’identificateur unique de l’annuaire utilisé pour envoyer des commandes sur ce groupe d’administration. Cet identificateur n’est pas modifiable après sa création car il est utilisé dans tout le système Azure pour identifier ce groupe. 
    - Le champ du nom d’affichage correspond au nom qui s’affiche dans le portail Azure. Un nom d’affichage distinct est un champ facultatif lors de la création du groupe d’administration. Il peut être modifié à tout moment.  


### <a name="add-subscriptions-to-a-management-group"></a>Ajouter des abonnements à un groupe d’administration
Vous pouvez ajouter des abonnements au groupe d’administration que vous avez créé.

1. À partir du Portail Azure, ouvrez **Groupes d’administration** et sélectionnez le groupe d’administration pour votre abonnement.

    :::image type="content" source="./media/management-groups-roles/management-group-subscriptions.png" alt-text="Sélectionner un groupe d’administration pour votre abonnement.":::

1. Lorsque la page du groupe s’ouvre, sélectionnez **Abonnements**.

1. Dans la page des abonnements, sélectionnez **Ajouter**, choisissez vos abonnements, puis sélectionnez **Enregistrer**. Répétez les étapes pour tous les abonnements concernés.

    :::image type="content" source="./media/management-groups-roles/management-group-add-subscriptions.png" alt-text="Ajout d’un abonnement à un groupe d’administration.":::

   > [!IMPORTANT]
   > Les groupes d’administration peuvent contenir à la fois des abonnements et des groupes d’administration enfants. Lorsque vous attribuez un rôle Azure à un utilisateur dans le groupe d’administration parent, l’accès est hérité par les abonnements du groupe d’administration enfant. Les stratégies définies au niveau du groupe d’administration parent sont aussi héritées par les enfants. 



## <a name="assign-azure-roles-to-other-users"></a>Attribuer des rôles Azure à d’autres utilisateurs

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Attribuer des rôles Azure aux utilisateurs via le portail Azure : 

1. À partir du [portail Azure](https://portal.azure.com), utilisez la zone de recherche dans la barre supérieure pour rechercher et ouvrir **Groupes d'administration**.

    :::image type="content" source="./media/management-groups-roles/open-management-groups-service.png" alt-text="Accès à vos groupes d’administration.":::

    La liste de vos groupes d’administration s’affiche.

1.  Sélectionnez le groupe d’administration approprié.

1. Sélectionnez **Contrôle d’accès (IAM)** , ouvrez l’onglet **Attributions de rôle**, puis sélectionnez **Ajouter** > **Ajouter une attribution de rôle**.

    :::image type="content" source="./media/management-groups-roles/add-user.png" alt-text="Ajout d’un utilisateur à un groupe d’administration.":::

1. Dans la page **Ajouter une attribution de rôle**, sélectionnez le rôle approprié.

    :::image type="content" source="./media/management-groups-roles/add-role-assignment-page.png" alt-text="Page Ajouter une attribution de rôle.":::

1. Dans l’onglet **Membres**, sélectionnez **+ Sélectionner des membres** et attribuez le rôle aux membres appropriés.

1. Dans l’onglet **Passer en revue + affecter**, sélectionnez **Passer en revue + affecter** pour affecter le rôle.


### <a name="assign-azure-roles-to-users-with-powershell"></a>Attribuer des rôles Azure aux utilisateurs à l’aide de PowerShell : 

1. Installez [Azure PowerShell](/powershell/azure/install-az-ps).
2. Exécutez les commandes suivantes : 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Quand vous y êtes invité, connectez-vous avec des informations d’identification d’administrateur général. 

    ![Capture d’écran de l’invite de connexion.](./media/management-groups-roles/azurerm-sign-in.PNG)

4. Accordez les autorisations du rôle de lecteur en exécutant la commande suivante :

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Pour supprimer le rôle, utilisez la commande suivante : 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Supprimer l’accès élevé 

Une fois que les rôles Azure ont été attribués aux utilisateurs, l’administrateur client doit se supprimer du rôle d’administrateur des accès utilisateur.

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).

2. Dans la liste de navigation, sélectionnez **Azure Active Directory**, puis **Propriétés**.

3. Sous **Gestion des accès aux ressources Azure**, définissez le commutateur sur **Non**.

4. Pour enregistrer votre paramètre, sélectionnez **Enregistrer**.



## <a name="next-steps"></a>Étapes suivantes

Sur cette page, vous avez appris comment organiser des abonnements en groupes d’administration et attribuer des rôles à des utilisateurs. Pour plus d’informations, consultez :

- [Autorisations dans Microsoft Defender pour le cloud](permissions.md)
