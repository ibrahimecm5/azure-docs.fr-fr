---
title: Qu’est-ce que B2B Collaboration dans Azure Active Directory ?
description: Azure AD B2B Collaboration prend en charge l’accès utilisateur invité, qui vous permet de partager des ressources et de collaborer avec des partenaires externes de manière sécurisée.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 10/21/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12d37973813fe2eee4972d2063d40df9e46b9b08
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222473"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Qu’est-ce que l’accès utilisateur invité dans Azure Active Directory B2B ?

Azure AD B2B Collaboration est une fonctionnalité d’Azure Active Directory for External Identities qui vous permet d’inviter des utilisateurs à collaborer avec votre organisation. Azure AD B2B Collaboration vous permet de partager de manière sécurisée les applications et services de votre organisation avec des utilisateurs invités de toute autre organisation, tout en conservant le contrôle sur vos propres données d’entreprise. Collaborez de manière sécurisée avec des partenaires externes issus de petites ou grandes entreprises, même s’ils n’utilisent pas Azure AD ou n’ont pas de service informatique. Un simple processus d’invitation et d’échange d’invitation permet à vos partenaires d’utiliser leurs propres informations d’identification pour accéder aux ressources de votre société. Les développeurs peuvent utiliser les API B2B d’Azure AD pour personnaliser le processus d’invitation ou pour écrire des applications telles que des portails d’inscription libre-service. Pour plus d’informations sur les licences et les tarifs relatifs aux utilisateurs invités, consultez [Tarification d’Azure Active Directory for External Identities](https://azure.microsoft.com/pricing/details/active-directory/external-identities/).  

> [!IMPORTANT]
>
> - **À partir du 12 juillet 2021**, si les clients B2B d’Azure AD configurent de nouvelles intégrations Google pour une utilisation avec l’inscription en libre-service pour leurs applications métier ou personnalisées, l’authentification avec Google Identities ne fonctionne pas tant que les authentifications ne sont pas déplacées vers les vues Web système. [Plus d’informations](google-federation.md#deprecation-of-web-view-sign-in-support)
> - **À partir du 30 septembre 30, 2021**, Google [déprécie la prise en charge de la connexion aux vues web intégrée](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Si vos applications authentifient les utilisateurs avec une vue Web incorporée et que vous utilisez Google Federation avec [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) ou Azure AD B2B pour des [invitations utilisateur externes](google-federation.md) ou une [inscription en libre-service](identity-providers.md), les utilisateurs de Google Gmail ne peuvent pas s’authentifier. [Plus d’informations](google-federation.md#deprecation-of-web-view-sign-in-support)
> - **À partir du 1er novembre 2021**, nous allons commencer à déployer un changement afin d’activer la fonctionnalité de code secret à usage unique envoyé par e-mail pour tous les locataires existants et de l’activer par défaut pour les nouveaux locataires. Dans le cadre de cette modification, Microsoft cessera de créer des comptes et des locataires Azure AD non managés (« viraux ») lors de l’acceptation d’invitation de collaboration B2B. Pour réduire au minimum les perturbations pendant les fêtes de fin d’année et les blocages de déploiements, les modifications seront déployées pour la majorité des locataires en janvier 2022. Il s’agit en effet d’une méthode d’authentification de secours transparente pour les utilisateurs invités. Cependant, si vous ne voulez pas autoriser l’activation automatique de cette fonctionnalité, vous pouvez [la désactiver](one-time-passcode.md#disable-email-one-time-passcode).

## <a name="collaborate-with-any-partner-using-their-identities"></a>Collaborer avec tous les partenaires qui utilisent leur propre identité

Avec Azure AD B2B, vos partenaires utilisent leur propre solution de gestion des identités. Votre organisation n’a donc aucuns frais administratifs externes à payer. Les utilisateurs invités peuvent se connecter à vos applications et services avec leurs propres identités professionnelles, scolaires ou sociales.

- Les partenaires utilisent leurs propres identités et informations d’identification. Azure AD n’est donc pas nécessaire.
- Vous n’avez pas à gérer les comptes externes ni les mots de passe.
- Vous n’avez pas à synchroniser les comptes ni à gérer les cycles de vie des comptes.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Inviter facilement des utilisateurs à partir du portail Azure AD

En tant qu’administrateur, vous pouvez facilement ajouter des utilisateurs invités à votre organisation dans le portail Azure.

- [Créez un utilisateur invité](b2b-quickstart-add-guest-users-portal.md) dans Azure AD, comme vous le feriez pour ajouter un utilisateur.
- Attribuez des utilisateurs invités à des applications ou à des groupes.
- Envoyez un e-mail d’invitation contenant un lien d’échange, ou envoyez un lien direct vers l’application que vous souhaitez partager.

![Capture d’écran montrant la page d’entrée de l’invitation Nouvel utilisateur invité](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Les utilisateurs invités suivent quelques [étapes d’échange](redemption-experience.md) simples pour se connecter.

![Capture d’écran montrant la page Passer en revue les autorisations](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Utiliser des stratégies pour partager vos applications et vos services de manière sécurisée

Vous pouvez utiliser des stratégies d’autorisation pour protéger le contenu de votre entreprise. Les stratégies d’accès conditionnel, telles que l’authentification multifacteur, peuvent être appliquées :

- Au niveau du locataire
- Au niveau de l’application
- Pour certains utilisateurs invités afin de protéger les données et les applications d’entreprise

![Capture d’écran montrant l’option Accès conditionnel](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Déléguer à chaque propriétaire d’application ou de groupe la gestion de ses propres utilisateurs invités

Vous pouvez déléguer la gestion des utilisateurs invités aux propriétaires d’applications, afin qu’ils puissent les ajouter directement aux applications qu’ils souhaitent partager, qu’il s’agisse ou non d’une application Microsoft.

- Les administrateurs configurent la gestion des applications et des groupes libre-service.
- Les utilisateurs qui ne sont pas administrateurs utilisent leur [panneau d’accès](https://myapps.microsoft.com) pour ajouter des utilisateurs invités aux applications ou aux groupes.

![Capture d’écran montrant le panneau d’accès pour un utilisateur invité](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Personnaliser l’expérience d’intégration des utilisateurs invités B2B

Intégrez vos partenaires externes de façon personnalisée en fonction des besoins de votre organisation.

- Utilisez la [gestion des droits d’utilisation Azure AD](../governance/entitlement-management-overview.md) pour configurer des stratégies qui vous aident à [gérer les accès pour les utilisateurs externes](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).
- Utilisez les [API d’invitation B2B Collaboration](/graph/api/resources/invitation) pour personnaliser vos expériences d’intégration.

## <a name="integrate-with-identity-providers"></a>Intégrer avec des fournisseurs d’identité

Azure AD prend en charge les fournisseurs d’identité externes tels que Facebook, les comptes Microsoft, Google ou des fournisseurs d’identité d’entreprise. Vous pouvez configurer une fédération avec des fournisseurs d’identité afin que vos utilisateurs externes puissent se connecter avec leurs comptes sociaux ou d’entreprise existants au lieu de créer un compte uniquement pour votre application. En savoir plus sur [les fournisseurs d’identité pour les identités externes](identity-providers.md).

![Capture d’écran montrant la page Fournisseurs d’identité](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow"></a>Créer un flux d’utilisateurs d’inscription en libre-service

Avec un flux utilisateur d’inscription en libre-service, vous pouvez créer une expérience d’inscription pour des utilisateurs externes qui souhaitent accéder à vos applications. Dans le cadre du processus d’inscription, vous pouvez fournir des options pour différents fournisseurs d’identité de réseaux sociaux ou d’entreprise, et collecter des informations sur l’utilisateur. En savoir plus sur l’[inscription en libre-service et sa configuration](self-service-sign-up-overview.md).

Vous pouvez également utiliser des [connecteurs d’API](api-connectors-overview.md) pour intégrer vos flux d’utilisateur d’inscription en libre-service à des systèmes cloud externes. Vous pouvez vous connecter avec des workflows d’approbation personnalisés, effectuer une vérification de l’identité, valider des informations fournies par l’utilisateur, et bien plus encore.

![Capture d’écran montrant la page des flux utilisateur](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Étapes suivantes

- [Prix des identités externes](external-identities-pricing.md)
- [Ajouter des utilisateurs invités B2B Collaboration dans le portail](add-users-administrator.md)
- [Comprendre le processus d’échange d’invitation](redemption-experience.md)
