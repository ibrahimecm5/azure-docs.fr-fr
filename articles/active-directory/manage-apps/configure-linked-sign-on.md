---
title: Ajouter l’authentification unique liée à une application
description: Ajoutez l’authentification unique liée à une application dans le Répertoire actif Azure.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: bc50777e503c50ca68955b8324b75c7c6aa66c77
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548479"
---
# <a name="add-linked-single-sign-on-to-an-application"></a>Ajouter l’authentification unique liée à une application

Cet article explique comment configurer l’authentification unique (SSO) liée à votre application dans le Répertoire actif Azure (Azure AD). L’authentification unique liée SSO permet à Azure AD de fournir l’authentification unique à une application qui est déjà configurée pour l’authentification unique dans un autre service. L’option liée vous permet de configurer l’emplacement cible lorsqu’un utilisateur sélectionne l’application dans le portail Mes applications ou Microsoft 365 de votre organisation.

L’option liée SSO ne fournit pas de fonctionnalité d’authentification via Azure AD. L’option définit simplement l’emplacement auquel les utilisateurs sont envoyés lorsqu’ils sélectionnent l’application sur le portail Mes applications ou Microsoft 365.

Voici quelques scénarios courants dans lesquels l’option d’authentification liée SSo est utile :
- Ajouter un lien vers une application web personnalisée qui utilise actuellement la fédération, par exemple les services de fédération Active Directory (AD FS).
- Ajouter des liens profonds vers des pages Web spécifiques que vous souhaitez afficher sur vos pages d’accès d’utilisateurs.
- Ajoutez un lien vers une application qui ne requiert pas d’authentification. L’option liée ne fournit pas de fonctionnalité d’authentification par le biais des informations d’identification Azure AD, mais vous pouvez toujours utiliser les autres fonctionnalités des applications d’entreprise. Par exemple, vous pouvez utiliser les journaux d’audit et ajouter un logo et un nom d’application personnalisés.

## <a name="prerequisites"></a>Prérequis

Pour configurer une option liée SSO dans votre locataire Azure AD, vous devez :
-   Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
-   Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
-   Application qui prend en charge l’authentification unique liée.

## <a name="configure-linked-based-single-sign-on"></a>Configurer l’authentification unique liée

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec le rôle approprié.
2.  Sélectionnez **Répertoire actif Azure**, puis **Applications d’entreprise**.
3.  Recherchez et sélectionnez l’application que vous souhaitez ajouter à l’authentification unique SSO.
4.  Sélectionnez **Authentification unique**, puis sélectionnez **Liée**.
5.  Saisissez l’URL de la page d’authentification de l’application.
6.  Sélectionnez **Enregistrer**. 

## <a name="next-steps"></a>Étapes suivantes

- [Gérer l’accès aux applications](what-is-access-management.md)