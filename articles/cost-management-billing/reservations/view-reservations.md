---
title: Autorisations pour afficher et gérer les réservations Azure
description: Découvrez comment afficher et gérer les réservations Azure dans le portail Azure.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: 5ee6972ae409a2912f677c0411daf01aaa96f6f7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464696"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Autorisations pour afficher et gérer les réservations Azure

Cet article explique le fonctionnement des autorisations de réservation et comment les utilisateurs peuvent afficher et gérer les réservations Azure dans le portail Azure et avec Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="who-can-manage-a-reservation-by-default"></a>Qui peut gérer une réservation par défaut

Par défaut, les utilisateurs suivants peuvent voir et gérer des réservations :

- La personne qui achète une réservation et l’administrateur de compte de l’abonnement de facturation utilisé pour acheter la réservation sont ajoutés à l’ordre de réservation.
- Les administrateurs de facturation de l’Accord Entreprise et du Contrat client Microsoft.
- Les utilisateurs disposant d’un accès avec élévation de privilèges pour gérer tous les abonnements et groupes d’administration Azure.
- Un administrateur de réservation pour les réservations dans le locataire (répertoire) Azure Active Directory (Azure AD)
- Un lecteur de réservation dispose d’un accès en lecture seule aux réservations dans son locataire Azure Active Directory (répertoire)

Actuellement, les rôles Administrateur de réservation et Lecteur de réservation ne sont disponibles pour attribution qu’à l’aide de PowerShell. Ils ne peuvent pas être affichés ou attribués dans le portail Azure. Pour plus d’informations, consultez [Accorder l’accès avec PowerShell](#grant-access-with-powershell).

Le cycle de vie des réservations étant indépendant d’un abonnement Azure, la réservation n’est pas une ressource dans le cadre de l’abonnement Azure. Il s’agit plutôt d’une ressource au niveau du locataire avec sa propre autorisation Azure RBAC distincte des abonnements. Les réservations n’héritent pas des autorisations des abonnements après achat.

## <a name="view-and-manage-reservations"></a>Afficher et gérer les réservations

Si vous êtes administrateur de facturation, suivez les étapes ci-dessous pour afficher et gérer toutes les réservations et les transactions de réservation dans le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à **Cost Management and Billing**.
    - Si vous êtes un administrateur d’entreprise, dans le menu de gauche, sélectionnez **Étendues de facturation**, puis, dans la liste des étendues de facturation, sélectionnez-en une.
    - Si vous êtes propriétaire d’un profil de facturation Contrat client Microsoft, dans le menu de gauche, sélectionnez **Profils de facturation**. Dans la liste des profils de facturation, sélectionnez-en un.
1. Dans le menu de gauche, sélectionnez **Produits + services** > **Réservations**.
1. La liste complète des réservations pour votre profil d’inscription ou de facturation d’administrateur d’entreprise s’affiche.
1. Les administrateurs de facturation peuvent prendre possession d’une ou de plusieurs réservations en les sélectionnant, puis en sélectionnant **Accorder l’accès** dans la fenêtre qui s’affiche.

### <a name="add-billing-administrators"></a>Ajouter des administrateurs de facturation

Ajoutez un utilisateur en tant qu’administrateur de facturation à un Contrat Entreprise ou à un Contrat client Microsoft dans le portail Azure.

- Pour un Contrat Entreprise, ajoutez des utilisateurs avec le rôle d’_Administrateur d’entreprise_ qui permet d’afficher et de gérer tous les ordres de réservation qui s’appliquent au Contrat Entreprise. Les administrateurs d’entreprise peuvent afficher et gérer les réservations dans **Gestion des coûts + facturation**.
    - Les utilisateurs dotés du rôle _Administrateur d’entreprise (lecture seule)_ peuvent uniquement afficher la réservation à partir de **Gestion des coûts + facturation**. 
    - Les administrateurs de service et les propriétaires de compte ne peuvent pas afficher les réservations _à moins_ d’être explicitement ajoutés à celles-ci à l’aide du contrôle d’accès (IAM). Pour plus d’informations, consultez [Gestion des rôles Azure Enterprise](../manage/understand-ea-roles.md).
- Pour un Contrat client Microsoft, les utilisateurs détenant le rôle de propriétaire du profil de facturation ou le rôle de contributeur du profil de facturation peuvent gérer l’ensemble des achats de réservation effectués à l’aide du profil de facturation. Les lecteurs de profil de facturation et les gestionnaires de facture peuvent voir toutes les réservations qui sont réglées avec le profil de facturation. Toutefois, ils ne peuvent apporter aucune modification aux réservations.
    Pour plus d’informations, consultez [Rôles et tâches liés au profil de facturation](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Afficher les réservations avec l’accès Azure RBAC

Si vous avez acheté la réservation ou si vous êtes ajouté à une réservation, suivez les étapes ci-dessous pour afficher et gérer les réservations dans le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Tous les services** > **Réservations** pour afficher la liste des réservations auxquelles vous avez accès.

## <a name="manage-subscriptions-and-management-groups-with-elevated-access"></a>Gérer les abonnements et les groupes d’administration dans le cadre d’un accès avec élévation de privilèges

Vous pouvez élever l’[accès pour gérer tous les abonnements et groupes d’administration Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json) d’un utilisateur.

Après avoir élevé l’accès :

1. Accédez à **Toutes les services** > **Réservation** pour afficher toutes les réservations qui se trouvent dans le locataire.
1. Pour apporter des modifications à une réservation, ajoutez vous-même en tant que propriétaire de l’ordre de réservation à l’aide du contrôle d’accès (IAM).

## <a name="grant-access-to-individual-reservations"></a>Accorder l’accès à des réservations individuelles

Les utilisateurs dotés d’un accès propriétaire sur les réservations et les administrateurs de facturation peuvent déléguer la gestion des accès pour un ordre de réservation individuel dans le portail Azure.

Pour permettre à d’autres personnes de gérer des réservations, vous avez le choix entre deux options :

- Déléguer la gestion de l’accès pour un ordre de réservation individuel en attribuant le rôle Propriétaire à un utilisateur au niveau de l’étendue des ressources de l’ordre de réservation. Si vous souhaitez accorder un accès limité, sélectionnez un autre rôle.  
     Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).

- Ajouter un utilisateur en tant qu’administrateur de facturation à un Contrat Entreprise ou à un Contrat client Microsoft :
    - Pour un Contrat Entreprise, ajoutez des utilisateurs avec le rôle d’_Administrateur d’entreprise_ qui permet d’afficher et de gérer tous les ordres de réservation qui s’appliquent au Contrat Entreprise. Les utilisateurs détenant le rôle d’_Administrateur d’entreprise (lecture seule)_ peuvent uniquement afficher la réservation. Les administrateurs de service et les propriétaires de compte ne peuvent pas afficher les réservations _à moins_ d’être explicitement ajoutés à celles-ci à l’aide du contrôle d’accès (IAM). Pour plus d’informations, consultez [Gestion des rôles Azure Enterprise](../manage/understand-ea-roles.md).

        _Les administrateurs d’entreprise peuvent prendre possession d’un ordre de réservation et peuvent ajouter d’autres utilisateurs à une réservation à l’aide du contrôle d’accès (IAM)._
    - Pour un Contrat client Microsoft, les utilisateurs détenant le rôle de propriétaire du profil de facturation ou le rôle de contributeur du profil de facturation peuvent gérer l’ensemble des achats de réservation effectués à l’aide du profil de facturation. Les lecteurs de profil de facturation et les gestionnaires de facture peuvent voir toutes les réservations qui sont réglées avec le profil de facturation. Toutefois, ils ne peuvent apporter aucune modification aux réservations.
    Pour plus d’informations, consultez [Rôles et tâches liés au profil de facturation](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="grant-access-with-powershell"></a>Accorder l’accès avec PowerShell

Les utilisateurs dotés d’un accès propriétaire pour les ordres de réservations, les utilisateurs dotés d’un accès avec élévation des privilèges et [les administrateurs d’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator) peuvent déléguer la gestion des accès pour tous les ordres de réservation auxquelles ils ont accès.

L’accès accordé à l’aide de PowerShell n’est pas indiqué dans le portail Azure. Au lieu de cela, vous utilisez la commande `get-AzRoleAssignment` dans la section suivante pour afficher les rôles attribués.

## <a name="assign-the-owner-role-for-all-reservations"></a>Attribuer le rôle Propriétaire pour toutes les réservations

Utilisez le script Azure PowerShell suivant pour permettre à un utilisateur Azure RBAC d’accéder à tous les ordres de réservations dans son Azure AD locataire (répertoire).

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources
 
Connect-AzAccount -Tenant <TenantId>
 
$response = Invoke-AzRestMethod -Path /providers/Microsoft.Capacity/reservations?api-version=2020-06-01 -Method GET
 
$responseJSON = $response.Content | ConvertFrom-JSON
 
$reservationObjects = $responseJSON.value
 
foreach ($reservation in $reservationObjects)
{
  $reservationOrderId = $reservation.id.substring(0, 84)
  Write-Host "Assigning Owner role assignment to "$reservationOrderId
  New-AzRoleAssignment -Scope $reservationOrderId -ObjectId <ObjectId> -RoleDefinitionName Owner
}
```

Lorsque vous utilisez le script PowerShell pour attribuer le rôle de propriétaire et qu’il s’exécute correctement, aucun message de réussite n’est retourné. Toutefois, vous pouvez vérifier que le rôle a été attribué avec :

```azurepowershell
get-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" |?{$_.RoleDefinitionName -Like "Reservations*"}
```

### <a name="parameters"></a>Paramètres

**-ObjectId**  Azure AD ObjectId de l’utilisateur, du groupe ou du principal du service.
- Tapez : String
- Alias : ID, PrincipalId
- Position : Nommée
- Valeur par défaut : Aucun
- Accepter l’entrée de pipeline : True
- Accepter les caractères génériques : False

**-TenantId** Identificateur unique du locataire.
- Tapez : String
- Position : 5
- Valeur par défaut : Aucun
- Accepter l’entrée de pipeline : False
- Accepter les caractères génériques : False

Les [administrateurs de l'accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator) peuvent ajouter les utilisateurs aux rôles Administrateur de réservation et lecteur de réservation.

## <a name="add-a-reservation-administrator-role-at-the-tenant-level"></a>Ajouter un rôle d’administrateur de réservation au niveau du locataire

Utilisez le script de Azure PowerShell suivant pour ajouter un rôle d’administrateur de réservation au niveau du locataire avec PowerShell.

```azurepowershell
Import-Module Az.Accounts
Import-Module Az.Resources
Connect-AzAccount -Tenant <TenantId>
New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Administrator"
```

### <a name="parameters"></a>Paramètres

**-ObjectId** Azure AD ObjectId de l’utilisateur, du groupe ou du principal du service.
- Tapez : String
- Alias : ID, PrincipalId
- Position : Nommée
- Valeur par défaut : Aucun
- Accepter l’entrée de pipeline : True
- Accepter les caractères génériques : False

**-TenantId** Identificateur unique du locataire.
- Tapez : String
- Position : 5
- Valeur par défaut : Aucun
- Accepter l’entrée de pipeline : False
- Accepter les caractères génériques : False

## <a name="assign-a-reservation-reader-role-at-the-tenant-level"></a>Attribuer un rôle Lecteur de réservation au niveau du locataire

Utilisez le script Azure PowerShell suivant pour attribuer le rôle Lecteur de réservation au niveau du locataire avec PowerShell.

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources

Connect-AzAccount -Tenant <TenantId>

New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Reader"
```

### <a name="parameters"></a>Paramètres

**-ObjectId** Azure AD ObjectId de l’utilisateur, du groupe ou du principal du service.
- Tapez : String
- Alias : ID, PrincipalId
- Position : Nommée
- Valeur par défaut : Aucun
- Accepter l’entrée de pipeline : True
- Accepter les caractères génériques : False

**-TenantId** Identificateur unique du locataire.
- Tapez : String
- Position : 5
- Valeur par défaut : Aucun
- Accepter l’entrée de pipeline : False
- Accepter les caractères génériques : False


## <a name="next-steps"></a>Étapes suivantes

- [Gérer les réservations Azure](manage-reserved-vm-instance.md)
