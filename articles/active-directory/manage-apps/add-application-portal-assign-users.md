---
title: 'Démarrage rapide : Créer et affecter un compte d’utilisateur'
titleSuffix: Azure AD
description: Créez un compte d’utilisateur dans votre locataire Azure Active Directory et affectez-le à une application.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 38be83db1c585a3660882ebdcca70a239a620f03
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549144"
---
# <a name="quickstart-create-and-assign-a-user-account"></a>Démarrage rapide : Créer et affecter un compte d’utilisateur

Dans ce guide de démarrage rapide, vous utilisez le Centre d’administration Azure Active Directory pour créer un compte d’utilisateur dans votre locataire Azure Active Directory (Azure AD). Après avoir créé le compte, vous pouvez l’affecter à l’application d’entreprise que vous avez ajoutée à votre locataire.

Il est recommandé d’utiliser un environnement de non-production pour tester les étapes de ce guide de démarrage rapide.

## <a name="prerequisites"></a>Prérequis

Pour créer un compte d’utilisateur et l’affecter à une application d’entreprise, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- Réalisation des étapes décrites dans [Démarrage rapide : Ajouter une application d’entreprise](add-application-portal.md).

## <a name="create-a-user-account"></a>Création d'un compte d'utilisateur

Pour créer un compte d’utilisateur dans votre locataire Azure AD :

1. Accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous avec l’un des rôles listés dans les prérequis.
1. Dans le menu de gauche, sélectionnez **Utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** en haut du volet.

    :::image type="content" source="media/add-application-portal-assign-users/new-user.png" alt-text="Ajoutez un nouveau compte d’utilisateur à votre locataire Azure AD.":::
    
1. Dans le champ **Nom d’utilisateur**, entrez le nom d’utilisateur du compte d’utilisateur. Par exemple : `contosouser1@contoso.com`. Veillez à remplacer `contoso.com` par le nom du domaine de votre locataire.
1. Dans le champ **Nom**, entrez le nom de l’utilisateur du compte. Par exemple : `contosouser1`.
1. Laissez **Générer automatiquement le mot de passe** sélectionné, puis sélectionnez **Afficher le mot de passe**. Notez la valeur affichée dans la zone Mot de passe.
1. Sélectionnez **Create** (Créer).

## <a name="assign-a-user-account-to-an-enterprise-application"></a>Affecter un compte d’utilisateur à une application d’entreprise

Pour affecter un compte d’utilisateur à une application d’entreprise :

1. Dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com), sélectionnez **Applications d’entreprise**, puis recherchez et sélectionnez l’application à laquelle vous souhaitez affecter le compte d’utilisateur. Par exemple, l’application que vous avez créée dans le guide de démarrage rapide précédent appelée **Azure AD SAML Toolkit 1**.
1. Dans le volet de gauche, sélectionnez **Utilisateurs et groupes** et **Ajouter un utilisateur/groupe**.

    :::image type="content" source="media/add-application-portal-assign-users/assign-user.png" alt-text="Affectez le compte d’utilisateur à l’application zn dans votre locataire Azure AD.":::

1. Dans le volet **Ajouter une attribution**, sélectionnez **Aucun sélectionné** sous **Utilisateurs et groupes**.
1. Recherchez et sélectionnez l’utilisateur que vous voulez attribuer à l’application. Par exemple : `contosouser1@contoso.com`.
1. Sélectionnez **Sélectionner**.
1. Dans le volet **Ajouter une attribution**, sélectionnez **Affecter** en bas.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de suivre le prochain guide de démarrage rapide, gardez l’application que vous avez créée. Dans le cas contraire, vous pouvez la supprimer pour nettoyer votre locataire. Pour plus d’informations, consultez [Supprimer une application](delete-application-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer l’authentification unique pour une application d’entreprise.
> [!div class="nextstepaction"]
> [Activer l’authentification unique](add-application-portal-setup-sso.md)
