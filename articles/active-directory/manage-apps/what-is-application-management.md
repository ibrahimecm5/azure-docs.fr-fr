---
title: Qu’est-ce que la gestion des applications ?
description: Vue d’ensemble de la gestion du cycle de vie d’une application dans Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/22/2021
ms.author: davidmu
ms.reviewer: sureshja, napuri
ms.openlocfilehash: 3b1e814def005283fb08ab7eeb3c23a8a38e3ee2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257389"
---
# <a name="what-is-application-management-in-azure-active-directory"></a>Qu’est-ce que la gestion des applications dans Azure Active Directory ?

La gestion des applications dans Azure Active Directory (Azure AD) est le processus de création, de configuration, de gestion et de supervision des applications dans le cloud. Quand une [application](../develop/app-objects-and-service-principals.md) est inscrite dans un locataire Azure AD, les utilisateurs qui lui ont été affectés peuvent y accéder sans problème. De nombreux types d’applications peuvent être inscrits dans Azure AD. Pour plus d’informations, consultez [Types d’application pour la plateforme d’identités Microsoft](../develop/v2-app-types.md).

Dans cet article, vous allez apprendre les aspects importants de la gestion du cycle de vie d’une application :

- **Développer, ajouter ou se connecter** – Vous pouvez utiliser des chemins différents selon que vous développez votre propre application, à l’aide d’une application préintégrée, ou que vous vous connectez à une application locale.
- **Gérer l’accès** – L’accès peut être géré avec l’authentification unique (SSO), en affectant des ressources, en définissant la manière dont l’accès est accordé et consenti, et en utilisant le provisionnement automatique.
- **Configurer les propriétés** – Configurez les conditions requises pour se connecter à l’application et la façon dont l’application est représentée dans les portails utilisateur.
- **Sécuriser l’application** – Gérez la configuration des autorisations, l’authentification multifacteur (MFA), l’accès conditionnel, les jetons et les certificats.
- **Gouverner et superviser** – Gérez l’activité d’interaction et de révision à l’aide de la gestion des droits d’utilisation et des ressources de rapport et de supervision.
- **Nettoyer** – Lorsque votre application n’est plus nécessaire, nettoyez votre locataire en supprimant l’application et son accès.

## <a name="develop-add-or-connect"></a>Développer, ajouter ou se connecter

Vous pouvez gérer les applications dans Azure AD de plusieurs manières. La manière la plus simple de commencer à gérer une application consiste à utiliser une application préintégrée à partir de la galerie Azure AD. Développer votre propre application et l’inscrire à Azure AD est une possibilité, ou vous pouvez continuer à utiliser une application locale.

L’image suivante montre comment ces applications interagissent avec Azure AD.

:::image type="content" source="media/what-is-application-management/app-management-overview.png" alt-text="Diagramme montrant comment vos propres applications développées, applications préintégrées et applications locales peuvent être utilisées comme applications d’entreprise.":::

### <a name="pre-integrated-applications"></a>Applications pré-intégrées

De nombreuses applications sont déjà préintégrées (montrées comme « applications cloud » dans l’image ci-dessus) et peuvent être configurées avec un minimum d’effort. Chaque application de la galerie Azure AD contient un article qui vous présente les étapes nécessaires à la [configuration de l’application](../saas-apps/tutorial-list.md). Pour obtenir un exemple simple de la façon dont une application peut être ajoutée à votre locataire Azure AD à partir de la galerie, consultez [Démarrage rapide : Ajouter une application d’entreprise](add-application-portal.md).

### <a name="your-own-applications"></a>Vos propres applications

Si vous développez votre propre application métier, vous pouvez l’inscrire à Azure AD pour tirer parti des fonctionnalités de sécurité que fournit le locataire. Vous pouvez inscrire votre application dans **Inscriptions d’applications**, ou vous pouvez l’inscrire à l’aide du lien **Créer votre propre application** lors de l’ajout d’une nouvelle application dans **Applications d’entreprise**. Déterminez comment l’[authentification](../develop/authentication-vs-authorization.md) est implémentée dans votre application pour l’intégrer à Azure AD. 

Si vous souhaitez que votre application soit disponible dans la galerie, vous pouvez [envoyer une demande pour l’y ajouter](../develop/v2-howto-app-gallery-listing.md).


### <a name="on-premises-applications"></a>Applications locales

Si vous souhaitez continuer à utiliser une application locale tout en tirant parti de ce qu’offre Azure AD, connectez-la à Azure AD avec un [Proxy d’application Azure AD](../app-proxy/application-proxy.md). Le Proxy d’application peut être implémenté lorsque vous souhaitez publier des applications locales en externe. Les utilisateurs distants qui ont besoin d’accéder aux applications internes peuvent alors y accéder de manière sécurisée.

## <a name="manage-access"></a>Gérer l’accès

Pour [gérer l’accès](what-is-access-management.md) d’une application, vous devez répondre aux questions suivantes :

- Comment l’accès est-il accordé et consenti pour l’application ?
- L’application prend-elle en charge l’authentification unique ?
- Quels utilisateurs, groupes et propriétaires doivent être affectés à l’application ? 
- Existe-t-il d’autres fournisseurs d’identité qui prennent en charge l’application ?
- Sera-t-il utile d’automatiser le provisionnement des identités et des rôles utilisateur ?

### <a name="access-and-consent"></a>Accès et consentement

Vous pouvez [gérer les paramètres de consentement utilisateur](configure-user-consent.md) pour choisir si les utilisateurs peuvent autoriser une application ou un service à accéder aux profils utilisateur et aux données de l’organisation. Lorsque l’accès est accordé aux applications, les utilisateurs peuvent se connecter aux applications intégrées à Azure AD, et l’application peut accéder aux données de votre organisation pour fournir des expériences riches pilotées par les données.

Souvent, les utilisateurs ne peuvent pas donner leur consentement pour les autorisations que demande l’application. Configurez le [workflow du consentement administrateur](configure-admin-consent-workflow.md) pour permettre aux utilisateurs de fournir une justification et demander à un administrateur de vérifier et d’approuver une application.

En tant qu’administrateur, vous pouvez [accorder un consentement administrateur au niveau locataire](grant-admin-consent.md) à une application. Le consentement administrateur au niveau locataire est nécessaire lorsqu’une application demande des autorisations que les utilisateurs standard ne sont pas autorisés à accorder, et permet aux organisations d’implémenter leurs propres processus de révision. Vérifiez toujours attentivement les autorisations demandées par l’application avant d’accorder un consentement. Lorsqu’une application a reçu le consentement administrateur au niveau locataire, tous les utilisateurs peuvent se connecter à l’application, sauf si elle a été configurée pour exiger une affectation d’utilisateur.

### <a name="single-sign-on"></a>Authentification unique

Envisagez d’implémenter l’authentification unique dans votre application. Vous pouvez configurer manuellement la plupart des applications pour l’authentification unique. Les options les plus connues dans Azure AD sont l’[authentification unique basée sur SAML et l’authentification unique basée sur OpenID Connect](../develop/active-directory-v2-protocols.md). Avant de commencer, veillez à bien comprendre les conditions requises de l’authentification unique et comment [planifier un déploiement](plan-sso-deployment.md). Pour obtenir un exemple simple de la configuration de l’authentification unique basée sur SAML pour une application d’entreprise dans votre locataire Azure AD, consultez [Démarrage rapide : Activer l’authentification unique pour une application d’entreprise](add-application-portal-setup-sso.md).

### <a name="user-group-and-owner-assignment"></a>Affectation d’utilisateurs, de groupes et de propriétaires

Par défaut, tous les utilisateurs peuvent accéder à vos applications d’entreprise sans y être affectés. Toutefois, si vous souhaitez affecter l’application à un ensemble d’utilisateurs, votre application nécessite une affectation d’utilisateur. Pour obtenir un exemple simple de création et d’affectation d’un compte d’utilisateur à une application, consultez [Démarrage rapide : Créer et affecter un compte d’utilisateur](add-application-portal-assign-users.md). 

Si cette possibilité est incluse dans votre abonnement, [affectez des groupes à une application](assign-user-or-group-access-portal.md) afin de pouvoir déléguer la gestion des accès en cours au propriétaire du groupe. 

L’[affectation de propriétaires](assign-app-owners.md) est un moyen simple d’accorder la possibilité de gérer tous les aspects de la configuration Azure AD pour une application. En tant que propriétaire, un utilisateur peut gérer la configuration spécifique à l’organisation de l’application.

### <a name="automate-provisioning"></a>Automatiser le provisionnement

Le [provisionnement d’application](../app-provisioning/user-provisioning.md) désigne la création automatique d’identités et de rôles utilisateur dans les applications auxquelles les utilisateurs ont besoin d’accéder. En plus de créer des identités utilisateur, l’approvisionnement automatique comprend la maintenance et la suppression d’identités utilisateur en cas de modification de l’état ou des rôles.

### <a name="identity-providers"></a>Fournisseurs d’identité

Avez-vous un fournisseur d’identité avec lequel vous voulez qu’Azure AD interagisse ? La [Découverte de domaine d’accueil](home-realm-discovery-policy.md) fournit une configuration qui permet à Azure AD d’identifier le fournisseur d’identité avec lequel l’utilisateur doit s’authentifier lorsqu’il se connecte.

### <a name="user-portals"></a>Portails utilisateur

Azure AD offre des moyens personnalisables de déployer des applications pour les utilisateurs de votre organisation. Par exemple, le [portail Mes applications ou le lanceur d’applications Microsoft 365](end-user-experiences.md). Mes applications fournit aux utilisateurs un endroit pour commencer à travailler et pour trouver toutes les applications auxquelles ils ont accès. En tant qu’administrateur d’une application, vous devez [planifier la manière dont les utilisateurs de votre organisation utiliseront Mes applications](my-apps-deployment-plan.md).

## <a name="configure-properties"></a>Configurer les propriétés

Lorsque vous ajoutez une application à votre locataire Azure AD, vous avez la possibilité de [configurer des propriétés](add-application-portal-configure.md) qui affectent le mode de connexion des utilisateurs. Vous pouvez activer ou désactiver la possibilité de se connecter et l’affectation d’utilisateurs peut être demandée. Vous pouvez également déterminer la visibilité de l’application, le logo qui représente l’application, ainsi que tout ce qui peut la concerner.

## <a name="secure-the-application"></a>Sécuriser l’application

Plusieurs méthodes sont disponibles pour vous aider à sécuriser vos applications d’entreprise. Par exemple, vous pouvez [restreindre l’accès du locataire](tenant-restrictions.md), [gérer la visibilité, les données et l’analytique](cloud-app-security.md), et éventuellement fournir un [accès hybride](secure-hybrid-access.md). Le maintien de la sécurité de vos applications d’entreprise implique aussi la gestion de la configuration des autorisations, de l’authentification multifacteur, de l’accès conditionnel, des jetons et des certificats.

### <a name="permissions"></a>Autorisations

Il est important de les réviser régulièrement et, si nécessaire, de [gérer les autorisations accordées à une application ou à un service](manage-application-permissions.md). Veillez à autoriser uniquement l’accès approprié à vos applications en évaluant régulièrement si des activités suspectes existent.

Les [classifications d’autorisations](configure-permission-classifications.md) vous permettent d’identifier l’effet des différentes autorisations en fonction des stratégies et des évaluations des risques de votre organisation. Par exemple, vous pouvez utiliser des classifications d’autorisations dans des stratégies de consentement afin d’identifier les autorisations que les utilisateurs peuvent accorder.

### <a name="multifactor-authentication-and-conditional-access"></a>Authentification multifacteur et accès conditionnel

Azure AD MFA permet de protéger l’accès aux données et aux applications en fournissant une autre couche de sécurité via une deuxième forme d’authentification. De nombreuses méthodes peuvent être utilisées pour une authentification à deux facteurs. Avant de commencer, [planifiez le déploiement de MFA pour votre application](../authentication/howto-mfa-getstarted.md) dans votre organisation.

Les organisations peuvent activer MFA avec l’[accès conditionnel](../conditional-access/overview.md) pour que la solution réponde à leurs besoins spécifiques. Les stratégies d’accès conditionnel permettent aux administrateurs d’affecter des contrôles à des [applications, des actions ou un contexte d’authentification](../conditional-access/concept-conditional-access-cloud-apps.md) spécifiques.

### <a name="tokens-and-certificates"></a>Jetons et certificats

Les différents types de jetons de sécurité sont utilisés dans un workflow d’authentification dans Azure AD selon le protocole utilisé. Par exemple, les [jetons SAML](../develop/reference-saml-tokens.md) sont utilisés pour le protocole SAML, tandis que les [jetons d’ID](../develop/id-tokens.md) et les [jetons d’accès](../develop/access-tokens.md) sont utilisés pour le protocole OpenID Connect. Les jetons sont signés avec le certificat unique qui est généré dans Azure AD et par des algorithmes standard spécifiques. 

Vous pouvez fournir davantage de sécurité en [chiffrant le jeton](howto-saml-token-encryption.md). Vous pouvez également gérer les informations d’un jeton, notamment les [rôles autorisés](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour l’application.

Azure AD utilise l’[algorithme SHA-256](certificate-signing-options.md) par défaut pour signer la réponse SAML. Utilisez SHA-256, sauf si l’application requiert SHA-1. Établissez un processus de [gestion de la durée de vie du certificat](manage-certificates-for-federated-single-sign-on.md). La durée de vie maximale d’un certificat de signature est de trois ans. Pour éviter ou réduire au minimum l’interruption due à l’expiration d’un certificat, utilisez des rôles et des listes de distribution d’e-mails pour vous assurer que les notifications de modifications liées aux certificats sont étroitement supervisées. 

## <a name="govern-and-monitor"></a>Gouverner et superviser

La [gestion des droits d’utilisation](../governance/entitlement-management-scenarios.md) dans Azure AD vous permet de gérer l’interaction entre les applications et les administrateurs, les propriétaires de catalogues, les gestionnaires de packages d’accès, les approbateurs et les demandeurs.

Votre solution de reporting et de supervision Azure AD dépend de vos exigences juridiques, sécuritaires et opérationnelles, ainsi que de votre environnement et de vos processus existants. Plusieurs journaux sont conservés dans Azure AD et vous devez [planifier un déploiement de reporting et de supervision](../reports-monitoring/plan-monitoring-and-reporting.md) pour garantir la meilleure expérience possible pour votre application.

## <a name="clean-up"></a>Nettoyer

Vous pouvez nettoyer l’accès aux applications. Par exemple, [supprimer l’accès d’un utilisateur](methods-for-removing-user-access.md). Vous pouvez aussi [désactiver la manière dont un utilisateur se connecte](disable-user-sign-in-portal.md). Enfin, vous pouvez supprimer l’application si l’organisation n’en a plus besoin. Pour obtenir un exemple simple de suppression d’une application d’entreprise de votre locataire Azure AD, consultez [Démarrage rapide : Supprimer une application d’entreprise](delete-application-portal.md).

## <a name="next-steps"></a>Étapes suivantes

- Commencez par ajouter votre première application d’entreprise avec [Démarrage rapide : Ajouter une application d’entreprise](add-application-portal.md).