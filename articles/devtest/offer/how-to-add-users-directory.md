---
title: Ajouter des utilisateurs à votre locataire d’annuaire de développement dev/test Azure
description: Guide pratique pour ajouter des utilisateurs à votre abonnement de crédit Azure et gérer leur accès avec des contrôles basés sur les rôles.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 14ded977ab78e8fbf6baf70d1c59e4aa632927b1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096533"
---
# <a name="add-users-to-your-azure-credit-subscription"></a>Ajouter des utilisateurs à votre abonnement à crédits Azure  

Pour gérer les ressources d’abonnement et y accéder, vous devez être utilisateur dans l’annuaire. Dans les abonnements eux-mêmes, la hiérarchie est contrôlée par les locations Azure Active Directory (Azure AD). Azure AD est un protocole LDAP (Lightweight Directory Protocol) qui stocke les noms de compte et les mots de passe.  

Avant d’ajouter des utilisateurs, vous devez déterminer votre hiérarchie métier et le niveau d’accès dont ils ont besoin au sein de votre abonnement.  

## <a name="why-do-i-need-to-add-users"></a>Pourquoi dois-je ajouter des utilisateurs ?

L’étape 1 consiste à déterminer si vous devez ajouter un nouvel utilisateur à votre abonnement. Voici quelques exemples de raisons pour lesquelles vous auriez besoin d’ajouter de nouveaux utilisateurs :  

- Travail sur un projet et nécessité de donner à l’équipe informatique l’accès lui permettant de superviser la sécurité  
- Travail sur de nouveaux protocoles d’API qui pourraient être bénéfiques pour un autre membre d’une organisation  
- Nécessité d’accorder l’accès au niveau de l’abonnement pour qu’ils puissent accéder à tous les groupes de ressources  
- Au niveau métier, un seul groupe de ressources doit se connecter à un ordinateur, mais ils n’ont pas besoin d’accéder à l’abonnement  
- Offre une meilleure visibilité et une plus grande transparence pour les projets sur lesquels vous travaillez tout en isolant certaines parties du travail, si nécessaire.  
- Ajouter un consultant en tant qu’utilisateur ou au sein d’un groupe à des fins de contribution  
- Vous souhaitez collaborer avec une personne pour tester et superviser la pré-production  

## <a name="where-do-i-add-users-and-their-roles-within-my-subscription"></a>Où puis-je ajouter des utilisateurs et leurs rôles au sein de mon abonnement ?

Dans Azure AD, la gestion de l’accès est une fonction critique. Le [contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md) \(Azure RBAC\) est le système d’autorisation qui offre une gestion précise de l’accès aux ressources Azure.  

Une fois que vous avez déterminé que vous devez ajouter un utilisateur, vous devez comprendre où vous l’ajoutez et les ressources auxquelles il doit accéder. L’ensemble des ressources auxquelles un utilisateur peut accéder est appelé étendue.  

Si le projet et les tâches sur lesquels vous travaillez doivent être supervisés par l’équipe informatique pour activer les protocoles de sécurité, vous devez avoir un rôle d’administrateur au sein du groupe d’administration pour bénéficier d’un accès complet et des autorisations au sein de l’abonnement.  

![Capture d’écran des niveaux d’accès dans Azure.](media/how-to-add-users-directory/access-management.png "Gestion des rôles avec des groupes d’administration dans Azure.")

Toutefois, si vous collaborez avec un autre développeur ou un collègue, il se peut qu’ils n’aient besoin d’accéder qu’au niveau de la ressource ou du groupe de ressources.  

Consultez la [vue d’ensemble d’Azure RBAC](../../role-based-access-control/overview.md) pour mieux comprendre le fonctionnement d’Azure RBAC et son rôle dans votre abonnement.  

Comment ajouter des utilisateurs ou supprimer des utilisateurs avec Azure Active Directory  

- [Ajouter ou supprimer des utilisateurs - Azure Active Directory | Microsoft Docs](../../active-directory/fundamentals/add-users-azure-active-directory.md)  

- [Procédure d’attribution d’un rôle Azure - Azure RBAC | Microsoft Docs](../../role-based-access-control/role-assignments-steps.md)  
