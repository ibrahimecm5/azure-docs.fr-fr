---
title: Afficher et supprimer les demandes concernant un package d’accès dans la gestion des droits d’utilisation Azure AD – Azure Active Directory
description: Découvrez comment afficher et supprimer les demandes concernant un package d’accès dans la gestion des droits d’utilisation Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 9/20/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140d648ddde6d520fdb27c6df0152cb9eb61c1fa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051798"
---
# <a name="view-and-remove-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Afficher et supprimer les demandes concernant un package d’accès dans la gestion des droits d’utilisation Azure AD

Dans la gestion des droits d’utilisation Azure AD, vous pouvez voir qui a demandé des packages d’accès, ainsi que la stratégie et l’état associés. Cet article explique comment afficher les demandes d’un package d’accès et supprimer celles qui ne sont plus nécessaires.

## <a name="view-requests"></a>Afficher les requêtes

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités, administrateur d’utilisateurs, propriétaire de catalogue, gestionnaire de package d’accès ou gestionnaire d’attribution de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Demandes**.

1. Cliquez sur une requête spécifique pour voir plus de détails.

    ![Liste des demandes pour un package d’accès](./media/entitlement-management-access-package-requests/requests-list.png)

1. Vous pouvez cliquer sur **Détails de l’historique des demandes** pour déterminer qui a approuvé une demande, quelles étaient les justifications de l’approbation et quand l’accès a été délivré.

Si les demandes de certains de vos utilisateurs sont à l’état de remise partielle ou d’échec, vous pouvez effectuer une nouvelle tentative en utilisant la [fonctionnalité de retraitement](entitlement-management-reprocess-access-package-requests.md).

### <a name="view-assignments-with-microsoft-graph"></a>Afficher les attributions avec Microsoft Graph
Vous pouvez également récupérer les demandes de package d’accès à l’aide de Microsoft Graph.  Un utilisateur doté d’un rôle approprié avec une application disposant de la permission déléguée `EntitlementManagement.Read.All` ou `EntitlementManagement.ReadWrite.All` peut appeler l’API pour [lister les accessPackageAssignmentRequests](/graph/api/accesspackageassignmentrequest-list?view=graph-rest-beta&preserve-view=true). Vous pouvez fournir un filtre pour indiquer un package d’accès spécifique, tel que : `$expand=accessPackage&$filter=accessPackage/id eq '9bbe5f7d-f1e7-4eb1-a586-38cdf6f8b1ea'`. Une application qui dispose de l’autorisation `EntitlementManagement.Read.All` ou `EntitlementManagement.ReadWrite.All` peut également utiliser cette API.

## <a name="remove-request-preview"></a>Supprimer une demande (préversion)

Vous pouvez également supprimer une demande terminée qui n’est plus nécessaire. Pour supprimer une demande :

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Demandes**.

1. Recherchez la demande que vous souhaitez supprimer du package d’accès.

1. Sélectionnez l’option Supprimer.

> [!NOTE]
> Si vous supprimez une demande terminée d’un package d’accès, cela ne supprime pas l’attribution active, mais uniquement les données de la demande. Le demandeur continuera donc à avoir accès. Si vous devez également supprimer une attribution et l’accès qui en résulte de ce package d’accès, dans le menu de gauche, cliquez sur **Attributions**, localisez l’attribution, puis [supprimez l’attribution](entitlement-management-access-package-assignments.md).

### <a name="remove-a-request-with-microsoft-graph"></a>Supprimer une demande avec Microsoft Graph

Vous pouvez également supprimer une demande à l’aide de Microsoft Graph.  Un utilisateur doté d’un rôle approprié avec une application disposant de la permission déléguée `EntitlementManagement.ReadWrite.All` ou une application disposant de cette permission d’application peut appeler l’API pour [supprimer un accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Étapes suivantes

- [Retraiter les demandes d’un package d’accès](entitlement-management-reprocess-access-package-requests.md)
- [Changer les paramètres de demande et d’approbation pour un package d’accès](entitlement-management-access-package-request-policy.md)
- [Afficher, ajouter et supprimer les attributions pour un package d’accès](entitlement-management-access-package-assignments.md)
- [Résoudre les problèmes liés aux demandes](entitlement-management-troubleshoot.md#requests)
