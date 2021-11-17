---
title: Masquer une application d’entreprise
titleSuffix: Azure AD
description: Guide pratique pour masquer une application d’entreprise à l’utilisateur dans les portails d’accès Azure Active Directory ou les lanceurs Microsoft 365.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80469924b9a4bbc53ac076751026071be2955ad
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550738"
---
# <a name="hide-an-enterprise-application"></a>Masquer une application d’entreprise

Découvrez comment masquer des applications d’entreprise dans Azure Active Directory. Lorsqu’une application est masquée, les utilisateurs ont toujours les autorisations à celle-ci.

## <a name="prerequisites"></a>Prérequis

- Des privilèges d’administrateur d’application sont nécessaires pour masquer une application dans le portail Mes applications et le lanceur Microsoft 365.

- Les privilèges Administrateur général sont nécessaires pour masquer toutes les applications Microsoft 365.

## <a name="hide-an-application-from-the-end-user"></a>Masquer une application à l’utilisateur final

Effectuez les étapes suivantes pour masquer une application dans le portail Mes applications et le lanceur d’applications Microsoft 365.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre annuaire.
1. Sélectionnez **Azure Active Directory**.
1. Sélectionnez **Applications d’entreprise**.
1. Sous **Type d’Application**, sélectionnez **Applications d’entreprise** si cette option n’est pas encore sélectionnée.
1. Recherchez l’application à masquer, puis sélectionnez-la.
1. Sélectionnez **Non** en réponse à la question **Visible par les utilisateurs ?** .
1. Sélectionnez **Enregistrer**.

> [!NOTE]
> Ces instructions s’appliquent uniquement aux applications d’entreprise.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Utiliser Azure AD PowerShell pour masquer une application

Pour masquer une application dans le portail Mes applications, vous pouvez ajouter manuellement l’étiquette HideApp au principal de service de l’application concernée. Exécutez les commandes [AzureAD PowerShell](/powershell/module/azuread/#service_principals) suivantes pour définir la propriété **Visible par les utilisateurs ?** de l’application sur **Non**.

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-my-apps-portal"></a>Masquer des applications Microsoft 365 dans le portail Mes applications

Effectuez les étapes suivantes pour masquer toutes les applications Microsoft 365 dans le portail Mes applications. Les applications sont toujours visibles dans le portail Office 365.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre annuaire.
1. Sélectionnez **Azure Active Directory**.
1. Sélectionnez **Utilisateurs**.
1. Sélectionnez **Paramètres utilisateur**.
1. Sous **Applications d’entreprise**, sélectionnez **Gérer la façon dont les utilisateurs finaux lancent et affichent leurs applications**.
1. Pour **Les utilisateurs peuvent voir uniquement les applications Office 365 dans le portail Office 365**, cliquez sur **Oui**.
1. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- [Suppression d’une affectation d’utilisateur ou de groupe à partir d’une application d’entreprise](./assign-user-or-group-access-portal.md)
