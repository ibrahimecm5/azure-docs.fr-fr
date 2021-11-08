---
title: Gérer votre locataire Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Découvrez comment gérer votre locataire Azure Active Directory B2C. Découvrez les fonctionnalités Azure AD qui sont prises en charge dans Azure AD B2C, ainsi que l’utilisation des rôles administrateur pour gérer des ressources, et la procédure d’ajout de comptes professionnels et d’utilisateurs invités à votre locataire Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/25/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: c0713bd45f344ce6dc994cde61cb275f9fdef3a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036330"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Gérer votre locataire Azure Active Directory B2C

Dans Azure Active Directory B2C (Azure AD B2C), un locataire représente votre répertoire d’utilisateurs consommateurs. Chaque locataire Azure AD B2C est distinct et séparé des autres locataires Azure AD B2C. Un locataire Azure AD B2C n’est pas un locataire Azure Active Directory ; d’ailleurs vous disposez peut-être déjà de ce dernier. Dans cet article, vous allez apprendre à gérer votre locataire Azure AD B2C.

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas encore créé votre propre [locataire Azure AD B2C](tutorial-create-tenant.md), créez-en un maintenant. Vous pouvez utiliser un locataire Azure AD B2C existant.
- Comprendre les [comptes d’utilisateur dans Azure AD B2C](user-overview.md).
- Comprendre les [rôles utilisateur pour contrôler l’accès aux ressources](roles-resource-access-control.md).


## <a name="add-an-administrator-work-account"></a>Ajouter un administrateur (compte professionnel)

Pour créer un compte d’administration, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec des autorisations d’administrateur général ou d’administrateur de rôle privilégié.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sélectionnez **Nouvel utilisateur**.
1. Sélectionnez **Créer un utilisateur** (vous pouvez en créer plusieurs à la fois en sélectionnant **Je souhaite créer des utilisateurs en bloc**).
1. Sur la page **Utilisateur**, entrez les informations pour cet utilisateur :

  
   - **Nom d’utilisateur**. *Requis*. Nom d’utilisateur du nouvel utilisateur. Par exemple : `mary@contoso.com`.
     Le domaine qui fait partie du nom d’utilisateur doit utiliser le nom de domaine par défaut initial, *\<tenant name>.onmicrosoft.com*, ou votre [domaine personnalisé](custom-domain.md), par exemple `contoso.com`.
   - **Nom**. *Requis*. Prénom et nom du nouvel utilisateur. Par exemple, *Mary Parker*.
   - **Groupes**. *Facultatif*. Vous pouvez ajouter l’utilisateur à un ou plusieurs groupes existants. Vous pouvez également ajouter l’utilisateur à des groupes ultérieurement. 
   - **Rôle d’annuaire** : si vous devez attribuer des autorisations d’administration Azure AD à l’utilisateur, vous pouvez les ajouter à un rôle Azure AD. Vous pouvez attribuer à l'utilisateur le rôle d'Administrateur général, ou lui confier un ou plusieurs des rôles d'administrateur limités dans Azure AD. Pour plus d’informations sur l’attribution de rôles, consultez [Utiliser des rôles pour contrôler l’accès aux ressources](roles-resource-access-control.md).
   - **Informations sur l’emploi** : Vous pouvez ajouter ici des informations supplémentaires sur l’utilisateur, ou le faire ultérieurement. 

1. Copiez le mot de passe généré automatiquement fourni dans le champ **Mot de passe**. Vous devrez fournir ce mot de passe à l’utilisateur pour qu’il se connecte la première fois.
1. Sélectionnez **Create** (Créer).

L’utilisateur est créé et ajouté à votre locataire Azure AD B2C. Il est préférable d’avoir au moins un compte professionnel natif pour votre locataire Azure AD B2C affecté au rôle d’administrateur général. Ce compte peut être considéré comme un *compte de secours*.

## <a name="invite-an-administrator-guest-account"></a>Inviter un administrateur (compte invité)

Vous pouvez également inviter un nouvel utilisateur invité à gérer votre locataire. Le compte invité est l’option privilégiée lorsque votre organisation possède également Azure AD, car le cycle de vie de cette identité peut être géré en externe. 

Pour inviter un utilisateur, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec des autorisations d’administrateur général ou d’administrateur de rôle privilégié.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sélectionnez **Nouveau compte Invité**.
1. Sur la page **Utilisateur**, entrez les informations pour cet utilisateur :

   - **Nom**. *Requis*. Prénom et nom du nouvel utilisateur. Par exemple, *Mary Parker*.
   - **Adresse e-mail**. *Requis*. Adresse e-mail de l’utilisateur que vous souhaitez inviter, qui doit correspondre à un compte Microsoft. Par exemple : `mary@contoso.com`.   
   - **Message personnel** : vous ajoutez un message personnel qui sera inclus dans l’e-mail d’invitation.
   - **Groupes**. *Facultatif*. Vous pouvez ajouter l’utilisateur à un ou plusieurs groupes existants. Vous pouvez également ajouter l’utilisateur à des groupes ultérieurement.
   - **Rôle d’annuaire** : si vous devez attribuer des autorisations d’administration Azure AD à l’utilisateur, vous pouvez les ajouter à un rôle Azure AD. Vous pouvez attribuer à l'utilisateur le rôle d'Administrateur général, ou lui confier un ou plusieurs des rôles d'administrateur limités dans Azure AD. Pour plus d’informations sur l’attribution de rôles, consultez [Utiliser des rôles pour contrôler l’accès aux ressources](roles-resource-access-control.md).
   - **Informations sur l’emploi** : Vous pouvez ajouter ici des informations supplémentaires sur l’utilisateur, ou le faire ultérieurement.

1. Sélectionnez **Créer**.

Un e-mail d’invitation est envoyé à l’utilisateur. L’utilisateur doit accepter l’invitation pour pouvoir se connecter.

### <a name="resend-the-invitation-email"></a>Renvoyer l’e-mail d’invitation

Si l’invité n’a pas reçu l’e-mail d’invitation ou si l’invitation a expiré, vous pouvez renvoyer l’invitation. Comme alternative à l’e-mail d’invitation, vous pouvez donner un lien direct à un invité pour qu’il accepte l’invitation. Pour renvoyer l’invitation et recevoir le lien direct :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Recherchez et sélectionnez l’utilisateur auquel vous souhaitez renvoyer l’invitation.
1. Sur la page **Utilisateur | Profil**, sous **Identité**, sélectionnez **(Gérer)** .
        ![Capture d’écran montrant comment renvoyer un e-mail d’invitation à un compte Invité.](./media/tenant-management/guest-account-resend-invite.png)

1. Pour **Renvoyer une invitation ?** , sélectionnez **Oui**. Lorsque **Voulez-vous vraiment renvoyer une invitation ?** s’affiche, sélectionnez **Oui**.
1. Azure AD B2C envoie l’invitation. Vous pouvez également copier l’URL de l’invitation et la fournir directement à l’invité.
    
    ![Capture d’écran montrant comment recevoir l’URL de l’invitation.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Ajouter une attribution de rôle

Vous pouvez attribuer un rôle lorsque vous [créez un utilisateur](#add-an-administrator-work-account) ou que vous [invitez un utilisateur invité](#invite-an-administrator-guest-account). Vous pouvez ajouter, modifier ou supprimer un rôle pour un utilisateur :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec des autorisations d’administrateur général ou d’administrateur de rôle privilégié.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sélectionnez l’utilisateur dont vous souhaitez modifier les rôles. Ensuite, sélectionnez **Rôles affectés**.
1. Sélectionnez **Ajouter des affectations**, le rôle à attribuer (par exemple, *Administrateur d’application*), puis choisissez **Ajouter**.

## <a name="remove-a-role-assignment"></a>Supprimer une attribution de rôle

Si vous devez supprimer une attribution de rôle d’un utilisateur, procédez comme suit :

1. Sélectionnez **Azure AD B2C**, sélectionnez **Utilisateurs**, puis recherchez et sélectionnez l’utilisateur.
1. Sélectionnez **Rôles attribués**. Sélectionnez le rôle que vous souhaitez supprimer, tel qu’*Administrateur d’application*, puis sélectionnez **Supprimer l’attribution**.

## <a name="review-administrator-account-role-assignments"></a>Vérifier les attributions de rôle de compte administrateur

Dans le cadre d’un processus d’audit, vous examinez généralement les utilisateurs qui sont attribués à des rôles spécifiques dans le répertoire Azure AD B2C. Procédez comme suit pour vérifier quels utilisateurs sont actuellement affectés à des rôles privilégiés.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec des autorisations d’administrateur général ou d’administrateur de rôle privilégié.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Rôles et administrateurs**.
1. Sélectionnez un rôle, comme **Administrateur général**. La page de **Rôle | Affectations** répertorie les utilisateurs disposant de ce rôle.

## <a name="delete-an-administrator-account"></a>Supprimer un compte administrateur

Pour supprimer un utilisateur existant, vous devez disposer d’une attribution de rôle *Administrateur général*. Les administrateurs généraux peuvent supprimer n’importe quel utilisateur, notamment les autres administrateurs. Les *administrateurs d’utilisateurs* peuvent supprimer n’importe quel utilisateur non-administrateur.

1. Dans votre répertoire Azure AD B2C, sélectionnez **Utilisateurs**, puis sélectionnez l'utilisateur que vous souhaitez supprimer.
1. Sélectionnez **Supprimer**, puis **Oui** pour confirmer la suppression.

L’utilisateur est supprimé et n’apparaît plus sur la page **Utilisateurs : Tous les utilisateurs**. L’utilisateur est affiché sur la page **Utilisateurs supprimés** pendant 30 jours et peut être restauré durant cette période. Pour plus d’informations sur la restauration d’un utilisateur, consultez [Restaurer ou supprimer un utilisateur supprimé à l’aide d’Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="protect-administrative-accounts"></a>Protéger des comptes d’administration

Il est recommandé de protéger tous les comptes Administrateur avec l’authentification multifacteur (MFA, Multifactor Authentication) pour plus de sécurité. MFA est un processus de vérification d’identité lors de la connexion qui invite l’utilisateur à fournir une plus grande forme d’identification, telle qu’un code de vérification sur son appareil mobile ou une requête dans son application Microsoft Authenticator.

![Méthodes d’authentification utilisées dans la capture d’écran de connexion](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

Vous pouvez activer des [paramètres de sécurité par défaut Azure AD](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) pour forcer tous les comptes d’administration à utiliser MFA.

## <a name="get-your-tenant-name"></a>Récupérer votre nom de locataire

Pour récupérer votre nom de locataire Azure AD B2C, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Dans la **Vue d’ensemble**, copiez le **Nom de domaine**.

![Capture d’écran montrant comment récupérer le nom de locataire Azure AD B2C.](./media/tenant-management/get-azure-ad-b2c-tenant-name.png)  

## <a name="get-your-tenant-id"></a>Récupérer votre ID de locataire

Pour récupérer votre ID de locataire Azure AD B2C, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le Portail Azure, recherchez et sélectionnez **Azure Active Directory**.
1. Dans la **vue d’ensemble**, copiez l’**ID de locataire**.

![Capture d’écran montrant comment récupérer l’ID de locataire Azure AD B2C.](./media/tenant-management/get-azure-ad-b2c-tenant-id.png)  

## <a name="next-steps"></a>Étapes suivantes

- [Nettoyer les ressources et supprimer le locataire](tutorial-delete-tenant.md)
