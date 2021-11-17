---
title: 'Démarrage rapide : Ajouter une application d’entreprise'
description: Ajoutez une application d’entreprise dans Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: f3d9cd36b821b1dca5311c266b84e259eab005c0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546953"
---
# <a name="quickstart-add-an-enterprise-application"></a>Démarrage rapide : Ajouter une application d’entreprise

Dans ce guide de démarrage rapide, vous utilisez le Centre d’administration Azure Active Directory pour ajouter une application d’entreprise à votre locataire Azure Active Directory (Azure AD). Azure AD a une galerie qui contient des milliers d’applications d’entreprise qui ont été préintégrées. De nombreuses applications utilisées par votre organisation sont probablement déjà dans la galerie. Ce guide de démarrage rapide utilise l’application appelée **Azure AD SAML Toolkit** comme exemple, mais les concepts s’appliquent à la plupart des [applications d’entreprise de la galerie](../saas-apps/tutorial-list.md).

Il est recommandé d’utiliser un environnement de non-production pour tester les étapes de ce guide de démarrage rapide.

## <a name="prerequisites"></a>Prérequis

Pour ajouter une application d’entreprise à votre locataire Azure AD, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.

## <a name="add-an-enterprise-application"></a>Ajouter une application d’entreprise

Pour ajouter une application d’entreprise à votre locataire :

1. Accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous avec l’un des rôles listés dans les prérequis.
1. Dans le menu de gauche, sélectionnez **Applications d’entreprise**. Le volet **Toutes les applications** s’ouvre et affiche une liste des applications de votre locataire Azure AD.
1. Dans le volet **Applications d’entreprise**, sélectionnez **Nouvelle application**.
1. Le volet **Parcourir la galerie Azure AD** s’ouvre et affiche des vignettes pour les plateformes cloud, les applications locales et les applications principales. Les applications listées dans la section **Applications principales** ont des icônes indiquant si elles prennent en charge l’authentification unique fédérée et le provisionnement. Recherchez votre application et sélectionnez-la. Dans ce guide de démarrage rapide, **Azure AD SAML Toolkit** est utilisé.

    :::image type="content" source="media/add-application-portal/browse-gallery.png" alt-text="Parcourez la galerie d’applications d’entreprise pour trouver l’application que vous souhaitez ajouter.":::

1. Entrez un nom que vous voulez utiliser pour reconnaître l’instance de l’application. Par exemple : `Azure AD SAML Toolkit 1`.
1. Sélectionnez **Create** (Créer).

Si vous choisissez d’installer une application qui utilise l’authentification unique basée sur OpenID Connect, au lieu de voir un bouton **Créer**, vous voyez un bouton qui vous redirige vers la page de connexion ou d’inscription de l’application, selon que vous avez déjà un compte ou pas. Pour plus d’informations, consultez [Ajouter une application d’authentification unique basée sur OpenID Connect](add-application-portal-setup-oidc-sso.md). Après la connexion, l’application est ajoutée à votre locataire.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de suivre le prochain guide de démarrage rapide, gardez l’application d’entreprise que vous avez créée. Dans le cas contraire, vous pouvez la supprimer pour nettoyer votre locataire. Pour plus d’informations, consultez [Supprimer une application](delete-application-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer un compte d’utilisateur et l’affecter à l’application d’entreprise que vous avez ajoutée.
> [!div class="nextstepaction"]
> [Créer et affecter un compte d’utilisateur](add-application-portal-assign-users.md)
