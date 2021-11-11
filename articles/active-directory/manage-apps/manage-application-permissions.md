---
title: Vérifier les autorisations accordées aux applications
titleSuffix: Azure AD
description: Découvrez comment examiner et gérer les autorisations pour une application dans Azure Active Directory.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 038955a1c7b4a15b2b0ae630c95c2833f32a3eab
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471171"
---
# <a name="review-permissions-granted-to-applications-in-azure-active-directory"></a>Passer en revue les autorisations accordées aux applications dans Azure Active Directory

Dans cet article, vous allez apprendre à passer en revue les autorisations accordées aux applications dans votre locataire Azure Active Directory (Azure AD). Vous devrez peut-être passer en revue les autorisations lorsque vous avez détecté une application malveillante ou si l’application a reçu plus d’autorisations que nécessaire.

Les étapes présentées dans cet article s’appliquent à toutes les applications qui ont été ajoutées à votre locataire Azure Active Directory (Azure AD), avec consentement utilisateur ou administrateur. Pour plus d’informations sur le consentement des applications, consultez [Infrastructure de consentement d’Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prérequis

Pour vérifier les autorisations accordées aux applications, vous devez :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.

Vous pouvez accéder au portail Azure AD pour obtenir des scripts PowerShell contextuels permettant d'appliquer les mesures.

## <a name="review-application-permissions"></a>Passer en revue les autorisations de l’application

Pour passer en revue les autorisations de l’application :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec l’un des rôles listés dans la section des prérequis.
1. Sélectionnez **Azure Active Directory**, puis **Applications d’entreprise**.
1. Sélectionnez l’application dont vous souhaitez restreindre l’accès.
1. Sélectionnez **Autorisations**. Sur la barre de commandes, sélectionnez **Passer en revue les autorisations**.
![Capture d’écran de la fenêtre Passer en revue les autorisations.](./media/manage-application-permissions/review-permissions.png)
1. Donnez la raison pour laquelle vous souhaitez passer en revue les autorisations de l’application en sélectionnant l’une des options listées après la question : **Pourquoi voulez-vous passer en revue les autorisations pour cette application ?**

Chaque option génère des scripts PowerShell qui vous permettent de contrôler l’accès utilisateur à l’application et de passer en revue les autorisations accordées à l’application. Pour plus d’informations sur la façon de contrôler l’accès utilisateur à une application, consultez [Comment supprimer l’accès d’un utilisateur à une application](methods-for-removing-user-access.md)

## <a name="revoke-permissions-using-powershell-commands"></a>Révoquer des autorisations à l’aide de commandes PowerShell

L’utilisation du script PowerShell suivant révoque toutes les autorisations octroyées à cette application.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```

> [!NOTE]
> La révocation de l’autorisation actuellement octroyée n’empêchera pas les utilisateurs de consentir à nouveau à l’application. Si vous souhaitez empêcher les utilisateurs de donner leur consentement, consultez [Configurer la manière dont les utilisateurs consentent aux applications](configure-user-consent.md).

## <a name="invalidate-the-refresh-tokens"></a>Invalider les jetons d’actualisation

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Configurer le workflow du consentement administrateur](configure-admin-consent-workflow.md)
