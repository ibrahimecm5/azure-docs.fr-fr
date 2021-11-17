---
title: Ajouter une application d’authentification unique basée sur OpenID Connect
description: Découvrez comment ajouter une application d’authentification unique basée sur OpenID Connect dans Azure Active Directory.
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
ms.openlocfilehash: 02bbc4d6b0cc6f346111a8cd0145ef099dd33feb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552011"
---
# <a name="add-an-openid-connect-based-single-sign-on-application"></a>Ajouter une application d’authentification unique basée sur OpenID Connect

Ajoutez une application qui prend en charge l’authentification unique basée sur [OpenID Connect (OIDC)](../develop/active-directory-v2-protocols.md) dans votre locataire Azure Active Directory (Azure AD).

Il est recommandé d’utiliser un environnement de non-production pour tester les étapes de cette page.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’authentification unique basée sur OIDC, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.

## <a name="add-the-application"></a>Ajouter l’application

Quand vous ajoutez une application d’entreprise qui utilise le standard OIDC pour l’authentification unique, vous sélectionnez un bouton de configuration. Quand vous sélectionnez le bouton, vous effectuez le processus d’inscription de l’application.

Pour configurer l’authentification unique basée sur OIDC pour une application :

1. Accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous avec l’un des rôles listés dans les prérequis.
1. Dans le menu de gauche, sélectionnez **Applications d’entreprise**. Le volet **Toutes les applications** s’ouvre et affiche une liste des applications de votre locataire Azure AD. 
1. Dans le volet **Applications d’entreprise**, sélectionnez **Nouvelle application**.
1. Le volet **Parcourir la galerie Azure AD** s’ouvre et affiche des vignettes pour les plateformes cloud, les applications locales et les applications principales. Les applications listées dans la section **Applications principales** ont des icônes indiquant si elles prennent en charge l’authentification unique fédérée et le provisionnement. Recherchez votre application et sélectionnez-la. Dans cet exemple, **SmartSheet** est utilisé.
1. Sélectionnez **S’inscrire**. Connectez-vous avec les informations d’identification du compte d’utilisateur depuis Azure Active Directory. Si vous disposez déjà d’un abonnement à l’application, les détails de l’utilisateur et les informations sur le locataire sont validés. Si l’application ne peut pas vérifier l’utilisateur, elle vous redirige pour que vous vous inscriviez au service d’application.

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/oidc-sso-configuration.png" alt-text="Complétez l’écran de consentement pour une application.":::

1. Sélectionnez **Consentement pour le compte de votre organisation**, puis **Accepter**. L’application est ajoutée à votre locataire et la page d’accueil de l’application s’affiche. Pour en savoir plus sur le consentement de l’utilisateur et de l’administrateur, consultez [Comprendre le consentement de l’utilisateur et de l’administrateur](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la planification d’un déploiement d’authentification unique.
> [!div class="nextstepaction"]
> [Planifier un déploiement de l’authentification unique](plan-sso-deployment.md)
