---
title: Désactiver la manière selon laquelle un utilisateur se connecte
titleSuffix: Azure AD
description: Comment désactiver une application d’entreprise afin qu’aucun utilisateur ne puisse s’y connecter dans Azure Active Directory
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72f0dc346e23d78ac9725b388b99f1a440136672
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548403"
---
# <a name="disable-how-a-user-signs-in-for-an-application"></a>Désactiver la manière selon laquelle un utilisateur se connecte à une application

Dans cet article, vous désactivez la manière selon laquelle un utilisateur se connecte à une application dans Azure Active Directory.

## <a name="prerequisites"></a>Prérequis

Pour désactiver la manière selon laquelle un utilisateur se connecte, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.

## <a name="disable-how-a-user-signs-in"></a>Désactiver la manière selon laquelle un utilisateur se connecte

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre annuaire.
1. Recherchez et sélectionnez **Azure Active Directory**.
1. Sélectionnez **Applications d’entreprise**.
1. Recherchez l’application à laquelle vous souhaitez empêcher un utilisateur de se connecter, puis sélectionnez l’application.
1. Sélectionner **Propriétés**.
1. Sélectionnez **Non** pour **Connexion permise pour les utilisateurs ?** .
1. Sélectionnez **Enregistrer**.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Utiliser Azure AD PowerShell pour désactiver une application non répertoriée

Vérifiez que vous avez installé le module AzureAD (utilisez la commande Install-Module -Name AzureAD). Lorsque vous y êtes invité, installez un module NuGet ou le nouveau module Azure Active Directory V2 PowerShell, tapez O et appuyez sur Entrée.

Si vous connaissez l’AppId d’une application qui n’apparaît pas dans la liste des applications d’entreprise (par exemple, parce que vous avez supprimé l’application ou que le principal du service n’a pas encore été créé, car l’application est en cours de préautorisation par Microsoft), vous pouvez créer manuellement le principal du service pour l’application, puis le désactiver à l’aide de la [cmdlet Azure AD PowerShell](/powershell/module/azuread/New-AzureADServicePrincipal).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Suppression d’une affectation d’utilisateur ou de groupe à partir d’une application d’entreprise](./assign-user-or-group-access-portal.md)
