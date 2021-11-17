---
title: Affecter des propriétaires d’application d’entreprise
titleSuffix: Azure AD
description: Affecter des propriétaires aux applications dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: saipradeepb23
manager: celesteDG
ms.service: active-directory
ms.workload: identity
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 08/03/2021
ms.author: saibandaru
ms.openlocfilehash: 2eb7d911dae8e0ebf61a2d50ae692b0af2dcf1ca
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555448"
---
# <a name="assign-enterprise-application-owners"></a>Affecter des propriétaires d’application d’entreprise

L'assignation de propriétaires est un moyen simple d'octroyer la possibilité de gérer tous les aspects de la configuration Azure AD pour un enregistrement d'application spécifique ou une application d'entreprise. En tant que propriétaire, un utilisateur peut gérer la configuration spécifique à l'organisation de l'application d'entreprise, telle que la configuration de la connexion unique, l’approvisionnement et les affectations d'utilisateurs. Un propriétaire peut également ajouter ou supprimer des propriétaires. Contrairement aux administrateurs généraux, les propriétaires ne peuvent gérer que les applications d’entreprise qu’ils possèdent.

Seuls les utilisateurs peuvent être propriétaires d’applications d’entreprise. Les groupes ne peuvent pas être affectés en tant que propriétaires. Les propriétaires peuvent ajouter des informations d’identification à une application et utiliser ces informations d’identification pour emprunter l’identité de l’application. L'application peut disposer de plus d'autorisations que le propriétaire, et constituerait donc une élévation de privilèges par rapport à ce à quoi le propriétaire a accès en tant qu'utilisateur ou principal du service. 

Un propriétaire d’application peut potentiellement créer ou mettre à jour des utilisateurs ou d’autres objets en usurpant l’identité de l’application, en fonction des autorisations de l’application. Les propriétaires d’applications ont les mêmes autorisations que les administrateurs d’applications limités à une application individuelle. Pour plus d’informations, consultez [Rôles intégrés Azure AD](../roles/permissions-reference.md#application-administrator). 

## <a name="assign-an-owner"></a>Affecter un propriétaire

Pour affecter un propriétaire à une application d’entreprise :

1. Connectez-vous à votre [organisation Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) avec un compte éligible pour le rôle **Administrateur d’application** ou **Administrateur d’application cloud** pour l’organisation.
2. Sélectionnez **Applications d’entreprise**, puis sélectionnez l’application à laquelle vous voulez ajouter un propriétaire.
3. Sélectionnez **Propriétaires**, puis sélectionnez **Ajouter** pour obtenir une liste de comptes utilisateur parmi lesquels vous pouvez choisir un propriétaire.
4. Recherchez et sélectionnez le compte d’utilisateur que vous souhaitez utiliser comme propriétaire de l’application.
5. Cliquez sur **Sélectionner** pour ajouter le compte d’utilisateur que vous choisissez comme propriétaire de l’application.

> [!NOTE]
> Si le paramètre utilisateur **Restreindre l’accès au portail d’administration Azure AD** est défini sur `Yes`, les utilisateurs qui ne sont pas administrateurs ne pourront pas utiliser le portail pour gérer les applications dont ils sont propriétaires. Pour plus d’informations sur les actions qui peuvent être effectuées sur des applications d’entreprise détenues, consultez [Propriétaires d’applications d’entreprise](../fundamentals/users-default-permissions.md#owned-enterprise-applications). 

## <a name="next-steps"></a>Étapes suivantes

- [Déléguer des autorisations d’inscription d’application dans Azure Active Directory](../roles/delegate-app-roles.md)
