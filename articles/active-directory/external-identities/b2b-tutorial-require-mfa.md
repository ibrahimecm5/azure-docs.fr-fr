---
title: Tutoriel - Authentification multifacteur pour B2B - Azure AD
description: Dans ce tutoriel, découvrez comment exiger une authentification multifacteur quand vous utilisez Azure AD B2B pour collaborer avec des utilisateurs externes et des organisations partenaires.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a1be1add489b3b48fc4e0fbeeca34197340e4b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053812"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Tutoriel : Appliquer l’authentification multifacteur pour les utilisateurs invités B2B

Lors d’une collaboration avec des utilisateurs invités B2B externes, il est judicieux de protéger vos applications avec des stratégies d’authentification multifacteur. Les utilisateurs externes doivent donc avoir plus qu’un simple nom d’utilisateur et mot de passe pour accéder à vos ressources. Dans Azure Active Directory (Azure AD), vous pouvez atteindre cet objectif avec une stratégie d’accès conditionnel exigeant une authentification multifacteur pour l’accès. Ces stratégies peuvent être appliquées au niveau du locataire, d’une application ou d’un utilisateur individuel invité, de la même façon qu’elles peuvent être activées pour les membres de votre propre organisation.

Exemple :

![Diagramme montrant un utilisateur invité se connectant aux applications d’une entreprise](media/tutorial-mfa/aad-b2b-mfa-example.png)

1. Un administrateur ou un employé de la société A invite un utilisateur invité à utiliser une application cloud ou locale qui est configurée pour exiger l’authentification multifacteur pour l’accès.
1. L’utilisateur invité se connecte avec sa propre identité professionnelle, scolaire ou sociale.
1. L’utilisateur est invité à effectuer une demande d’authentification multifacteur. 
1. L’utilisateur configure l’authentification multifacteur avec la société A et choisit son option d’authentification multifacteur. L’utilisateur est autorisé à accéder à l’application.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
> - Tester l’expérience de connexion avant la configuration de l’authentification multifacteur.
> - Créer une stratégie d’accès conditionnel qui exige l’authentification multifacteur pour l’accès à une application cloud dans votre environnement. Dans ce tutoriel, nous allons utiliser l’application de gestion Microsoft Azure pour illustrer le processus.
> - Utilisez l’outil What If pour simuler la connexion d’authentification multifacteur.
> - Testez votre stratégie d’accès conditionnel.
> - Supprimez l’utilisateur de test et la stratégie.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre le scénario décrit dans ce didacticiel, vous avez besoin de ce qui suit :

- **Accès à l’édition Azure AD Premium**, qui inclut des fonctionnalités de stratégie d’accès conditionnel. Pour appliquer l’authentification multifacteur, vous devez créer une stratégie d’accès conditionnel Azure AD. Notez que les stratégies d’authentification multifacteur sont toujours appliquées à votre organisation, que le partenaire ait ou non des fonctionnalités d’authentification multifacteur.
- **Un compte e-mail externe valide** que vous pouvez ajouter à votre annuaire de locataires en tant qu’utilisateur invité et vous servir pour la connexion. Si vous ne savez pas comment créer un compte Invité, consultez [Ajouter un utilisateur invité B2B dans le portail Azure](add-users-administrator.md).

## <a name="create-a-test-guest-user-in-azure-ad"></a>Créer un utilisateur invité de test dans Azure AD

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur Azure AD.
1. Dans le portail Azure, sélectionnez **Azure Active Directory**.
1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sélectionnez **Nouvel utilisateur invité**.

    ![Capture d’écran montrant l’endroit où sélectionner l’option Nouvel utilisateur invité](media/tutorial-mfa/tutorial-mfa-user-3.png)

1. Sous **Identité**, entrez l’adresse e-mail de l’utilisateur externe. Vous pouvez éventuellement inclure un nom et un message d’accueil.

    ![Capture d’écran montrant où entrer le message d’invitation adressé à l’invité](media/tutorial-mfa/tutorial-mfa-user-4.png)

1. Sélectionnez **Inviter** pour envoyer automatiquement l’invitation à l’utilisateur invité. Un message **Utilisateur invité avec succès** apparaît.
1. Après avoir envoyé l’invitation, le compte d’utilisateur est automatiquement ajouté au répertoire en tant qu’invité.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>Tester l’expérience de connexion avant la configuration de l’authentification multifacteur

1. Utilisez votre nom d’utilisateur de test et votre mot de passe pour vous connecter à votre [portail Azure](https://portal.azure.com/).
1. Notez que vous pouvez accéder au portail Azure avec seulement vos informations d’identification de connexion. Aucune autre authentification supplémentaire n’est nécessaire.
1. Déconnectez-vous.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>Créer une stratégie d’accès conditionnel exigeant l’authentification multifacteur

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Dans le portail Azure, sélectionnez **Azure Active Directory**.
1. Dans le menu de gauche, sous **Gérer**, sélectionnez **Sécurité**.
1. Sous **Sécurité**, sélectionnez **Accès conditionnel**.
1. Dans la page **Accès conditionnel**, dans la barre d’outils en haut, sélectionnez **Nouvelle stratégie**.
1. Dans la page **Nouveau**, dans la zone de texte **Nom**, tapez **Exiger l’authentification multifacteur pour l’accès au portail B2B**.
1. Dans la section **Affectations**, choisissez le lien sous **Utilisateurs et groupes**.
1. Dans la page **Utilisateurs et groupes**, choisissez **Sélectionner des utilisateurs et groupes**, puis **Tous les utilisateurs invités et externes**.

    ![Capture d’écran montrant la sélection de tous les utilisateurs invités](media/tutorial-mfa/tutorial-mfa-policy-6.png)
1. Dans la section **Affectations**, choisissez le lien sous **Applications ou actions cloud**.
1. Choisissez **Sélectionner les applications**, puis choisissez le lien sous **Sélectionner**.

    ![Capture d’écran montrant la page Applications cloud et l’option Sélectionner](media/tutorial-mfa/tutorial-mfa-policy-10.png)

1.  Dans la page **Sélectionner**, choisissez **Gestion Microsoft Azure**, puis **Sélectionner**.

    ![Capture d’écran mettant en évidence l’option Gestion Microsoft Azure.](media/tutorial-mfa/tutorial-mfa-policy-11.png)

1.  Dans la page **Nouveau**, dans la section **Contrôles d’accès**, choisissez le lien sous **Accorder**.
1.  Dans la page **Accorder**, choisissez **Accorder l’accès**, cochez la case **Exiger une authentification multifacteur**, puis choisissez **Sélectionner**.

    ![Capture d’écran montrant l’option Exiger l’authentification multifacteur](media/tutorial-mfa/tutorial-mfa-policy-13.png)

1.  Sous **Activer une stratégie**, sélectionnez **Activé**.

    ![Capture d’écran dans laquelle l’option Activer la stratégie est activée](media/tutorial-mfa/tutorial-mfa-policy-14.png)

1.  Sélectionnez **Create** (Créer).

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>Utiliser l’option What If pour simuler une connexion

1. Dans la page **Accès conditionnel | Stratégies**, sélectionnez **What If**.

    ![Capture d’écran indiquant où sélectionner l’option What If dans la page Accès conditionnel - Stratégies.](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

1. Sélectionnez le lien sous **Utilisateur**. 
1. Dans la zone de recherche, tapez le nom de votre utilisateur invité de test. Choisissez l’utilisateur dans les résultats de la recherche, puis choisissez **Sélectionner**.

    ![Capture d’écran montrant un utilisateur invité sélectionné](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

1. Sélectionnez le lien sous **Applications cloud, actions ou contenu d’authentification**. . Choisissez **Sélectionner les applications**, puis choisissez le lien sous **Sélectionner**.

    ![Capture d’écran montrant l’application Microsoft Azure Management sélectionnée](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

1. Dans la page **Applications cloud**, dans la liste des applications, choisissez **Gestion Microsoft Azure**, puis **Sélectionner**.
1. Choisissez **What If** et vérifiez que votre nouvelle stratégie apparaît sous **Résultats de l’évaluation** sous l’onglet **Stratégies qui vont s’appliquer**.

    ![Capture d’écran montrant où sélectionner l’option What If](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>Tester votre stratégie d’accès conditionnel

1. Utilisez votre nom d’utilisateur de test et votre mot de passe pour vous connecter à votre [portail Azure](https://portal.azure.com/).
1. Vous devez voir une demande pour des méthodes d’authentification supplémentaires. Notez qu’un certain temps peut être nécessaire pour que la stratégie entre en vigueur.

    ![Capture d’écran montrant le message Plus d’informations requises](media/tutorial-mfa/mfa-required.png)

1. Déconnectez-vous.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez l’utilisateur de test et la stratégie d’accès conditionnel de test.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur Azure AD.
1. Sélectionnez **Azure Active Directory** dans le volet de gauche.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sélectionnez l’utilisateur invité, puis sélectionnez **Supprimer l’utilisateur**.
1. Sélectionnez **Azure Active Directory** dans le volet de gauche.
1. Sous **Sécurité**, sélectionnez **Accès conditionnel**.
1. Dans la liste **Nom de la stratégie**, sélectionnez le menu contextuel (...) pour votre stratégie de test, puis **Supprimer**. Sélectionnez **Oui** pour confirmer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une stratégie d’accès conditionnel exigeant que les utilisateurs invités utilisent l’authentification multifacteur lors de la connexion à une de vos applications cloud. Pour plus d’informations sur l’ajout d’utilisateurs invités pour la collaboration, consultez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le portail Azure](add-users-administrator.md).
