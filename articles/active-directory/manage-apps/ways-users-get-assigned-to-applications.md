---
title: Comprendre comment les utilisateurs sont attribués aux applications
description: Comprendre comment les utilisateurs sont affectés à une application qui utilise Azure Active Directory pour la gestion des identités.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 2df43ef2a3bc70398d1d73702e1402e439538d15
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548289"
---
# <a name="understand-how-users-are-assigned-to-apps"></a>Comprendre comment les utilisateurs sont attribués aux applications

Cet article vous explique comment les utilisateurs sont affectés à une application dans votre client.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Comment les utilisateurs sont-ils affectés à une application dans Azure AD ?

Pour qu’un utilisateur puisse accéder à une application, il doit tout d’abord être affecté à cette application d’une certaine manière. L’affectation peut être effectuée par un administrateur, un délégué de l’entreprise ou, parfois, par l’utilisateur lui-même. Voici une description des méthodes permettant d’affecter des utilisateurs à des applications :

* Un administrateur [affecte un utilisateur](./assign-user-or-group-access-portal.md) directement à l’application
* Un administrateur [affecte un groupe](./assign-user-or-group-access-portal.md) dont l’utilisateur est membre à l’application, y compris :

  * Un groupe synchronisé localement
  * Un groupe de sécurité statique créé dans le cloud
  * Un [groupe de sécurité dynamique](../enterprise-users/groups-dynamic-membership.md) créé dans le cloud
  * Un groupe Microsoft 365 créé dans le cloud
  * Le groupe [Tous les utilisateurs](../fundamentals/active-directory-groups-create-azure-portal.md)
* Un administrateur active l’[accès aux applications en libre-service](./manage-self-service-access.md) pour autoriser un utilisateur à ajouter une application à l’aide de la fonctionnalité **Ajouter une application** de [Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510) **sans approbation de l’entreprise**.
* Un administrateur active l’[accès aux applications en libre-service](./manage-self-service-access.md) pour autoriser un utilisateur à ajouter une application à l’aide de la fonctionnalité **Ajouter une application** de [Mes accès](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510), mais uniquement **après approbation d’un ensemble sélectionné d’approbateurs d’entreprise**.
* Un administrateur active la [gestion des groupes en libre-service](../enterprise-users/groups-self-service-management.md) pour permettre à un utilisateur de joindre un groupe auquel une application est affectée, **sans approbation d’entreprise**
* Un administrateur active la [gestion des groupes en libre-service](../enterprise-users/groups-self-service-management.md) pour permettre à un utilisateur de joindre un groupe auquel une application est affectée, mais uniquement **après approbation d’un ensemble sélectionné d’approbateurs d’entreprise**
* Un administrateur affecte une licence à un utilisateur pour une première application, par exemple [Microsoft 365](https://products.office.com/)
* Un administrateur affecte une licence à un groupe dont l’utilisateur est membre pour une première application, par exemple [Microsoft 365](https://products.office.com/)
* Un [administrateur donne son consentement à une application](../develop/howto-convert-app-to-be-multi-tenant.md) pour être utilisée par tous les utilisateurs, puis un utilisateur se connecte à l’application
* Un utilisateur [donne lui-même son consentement à une application](../develop/howto-convert-app-to-be-multi-tenant.md) en se connectant à l’application

## <a name="next-steps"></a>Étapes suivantes

* [Série de guides de démarrage rapide sur la gestion des applications](view-applications-portal.md)
* [Qu’est-ce que la gestion des applications ?](what-is-application-management.md)
* [Qu’est-ce que l’authentification unique ?](what-is-single-sign-on.md)