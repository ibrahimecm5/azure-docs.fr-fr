---
title: Configurer des classifications d’autorisations
titleSuffix: Azure AD
description: Découvrez comment gérer les classifications d’autorisations déléguées.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 7981bdf26d3bafe301667e02af3fecdf53931021
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548441"
---
# <a name="configure-permission-classifications"></a>Configurer des classifications d’autorisations

Dans cet article, vous allez apprendre à configurer les classifications des autorisations dans Azure Active Directory (Azure AD). Les classifications d’autorisations vous permettent d’identifier l’impact des différentes autorisations en fonction des stratégies et des évaluations des risques de votre organisation. Par exemple, vous pouvez utiliser des classifications d’autorisations dans des stratégies de consentement afin d’identifier les autorisations que les utilisateurs peuvent accorder.

Actuellement, seule la classification d’autorisation ayant un « Faible impact » est prise en charge. Seules des autorisations déléguées qui ne nécessitent pas de consentement de l’administrateur peuvent être classifiées comme ayant un « Faible impact ».

Les autorisations minimales requises pour effectuer une connexion de base (`openid`, `profile`, `email`, `User.Read` et `offline_access`) sont toutes des autorisations déléguées sur Microsoft Graph. Avec ces autorisations, une application peut lire les détails complets du profil de l’utilisateur connecté, et maintenir cet accès même quand l’utilisateur n’utilise plus l’application.

## <a name="prerequisites"></a>Prérequis

Pour configurer des classifications d’autorisations, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.

## <a name="manage-permission-classifications"></a>Gérer des classifications d’autorisations

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour classifier les autorisations à l’aide du Portail Azure, procédez comme suit :

1. Se connecter au [Portail Azure](https://portal.azure.com) en tant qu’[administrateur général](../roles/permissions-reference.md#global-administrator), [administrateur d’application](../roles/permissions-reference.md#application-administrator) ou [administrateur d’application cloud](../roles/permissions-reference.md#cloud-application-administrator)
1. Sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Consentement et autorisations** > **Classifications d’autorisations**.
1. Choisissez **Ajouter des autorisations** pour classifier une autorisation autrement que comme ayant un « Faible impact ».
1. Sélectionnez l’API, puis les autorisations déléguées.

Dans cet exemple, nous avons classifié l’ensemble minimal d’autorisations requises pour l’authentification unique :

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Classifications d’autorisations":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vous pouvez utiliser le dernier module Azure AD PowerShell en préversion, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview), pour classifier les autorisations. Les classifications des autorisations sont configurées sur l’objet **ServicePrincipal** de l’API qui publie les autorisations.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Répertorier les classifications d’autorisations actuelles pour une API

1. Récupérez l’objet **ServicePrincipal** pour l’API. Ici, nous récupérons l’objet ServicePrincipal pour l’API Microsoft Graph :

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Lisez les classifications d’autorisations déléguées pour l’API :

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Classifier une autorisation comme ayant un « Faible impact »

1. Récupérez l’objet **ServicePrincipal** pour l’API. Ici, nous récupérons l’objet ServicePrincipal pour l’API Microsoft Graph :

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Recherchez l’autorisation déléguée que vous souhaitez classifier :

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Définissez la classification d’autorisation à l’aide du nom et de l’ID de l’autorisation :

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Supprimer une classification d’autorisation déléguée

1. Récupérez l’objet **ServicePrincipal** pour l’API. Ici, nous récupérons l’objet ServicePrincipal pour l’API Microsoft Graph :

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Recherchez la classification d’autorisation déléguée à supprimer :

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Supprimez la classification d’autorisation :

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

- Consultez [Autorisations et consentement dans la plateforme d’identités Microsoft](../develop/v2-permissions-and-consent.md)
