---
title: Flux d’utilisateurs et stratégies personnalisées dans Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: En savoir plus sur les flux d’utilisateurs intégrés et l’infrastructure de stratégies extensibles personnalisées de Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 73d1649881428047cf0c593335d66d9d3145ec2c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021863"
---
# <a name="user-flows-and-custom-policies-overview"></a>Vue d’ensemble des flux d’utilisateurs et des stratégies personnalisées

Dans Azure AD B2C, vous pouvez définir la logique métier que les utilisateurs suivent pour accéder à votre application. Par exemple, vous pouvez déterminer la séquence des étapes suivies par les utilisateurs lorsqu’ils se connectent, s’inscrivent, modifient un profil ou réinitialisent un mot de passe. À l’issue de la séquence, l’utilisateur acquiert un jeton et accède à votre application. 

Dans Azure AD B2C, il existe deux moyens de fournir des expériences d’utilisateurs d’identités :

* Les **flux utilisateur** sont des stratégies prédéfinies, intégrées et configurables que nous fournissons, afin que vous puissiez créer des expériences d’inscription, de connexion et de modification de stratégie en quelques minutes.

* Les **stratégies personnalisées** vous permettent de créer vos propres parcours utilisateur pour des scénarios d’expérience d’identité complexes.

La capture d’écran suivante montre l’interface utilisateur des paramètres de flux d’utilisateurs et les fichiers de configuration de stratégies personnalisées.

![La capture d’écran montre l’interface utilisateur des paramètres de flux d’utilisateurs et les fichiers de configuration de stratégies personnalisées.](media/user-flow-overview/user-flow-vs-custom-policy.png)

Cet article donne une brève vue d’ensemble des flux d’utilisateurs et des stratégies personnalisées, et vous aide à décider quelle méthode correspond le mieux à vos besoins professionnels.

## <a name="user-flows"></a>Flux d’utilisateurs

Pour configurer les tâches d’identité les plus courantes, le portail Azure inclut plusieurs stratégies configurables et prédéfinies, appelées *flux d’utilisateurs*.

Vous pouvez configurer des paramètres de flux utilisateur comme ceux mentionnés ici, pour contrôler les comportements d’expérience d’identité dans vos applications :

* Types de compte utilisés pour la connexion, comme les comptes de réseaux sociaux de type Facebook, ou les comptes locaux qui utilisent une adresse e-mail et un mot de passe pour cette connexion
* Attributs à collecter auprès du consommateur, comme le prénom, le code postal ou le pays/la région de résidence
* Azure AD Multi-Factor Authentication (MFA)
* Personnalisation de l’interface utilisateur
* Ensemble de revendications dans un jeton que votre application reçoit dès que l’utilisateur a terminé le flux utilisateur
* Gestion des sessions
* ... et bien plus

La plupart des scénarios d’identité courants pour les applications peuvent se définir et s’implémenter efficacement avec les flux d’utilisateurs. Nous vous recommandons d’utiliser les flux d’utilisateurs intégrés, sauf si vous avez des scénarios de parcours utilisateur complexes qui nécessitent toute la flexibilité offerte par les stratégies personnalisées.

## <a name="custom-policies"></a>Stratégies personnalisées

Les stratégies personnalisées sont des fichiers config qui définissent le comportement de votre expérience utilisateur locataire Azure AD B2C. Si les flux d’utilisateurs sont prédéfinis sur le portail Azure AD B2C pour les tâches d’identité les plus courantes, les stratégies personnalisées, elles, peuvent être entièrement modifiés par un développeur d’identité pour effectuer de nombreuses tâches différentes.

Une stratégie personnalisée est entièrement configurable et basée sur des stratégies. Elle orchestre la confiance entre les entités dans les protocoles standards. Par exemple, OpenID Connect, OAuth, SAML et d’autres non standards, par exemple, les échanges de revendications de système à système basés sur un API REST. L’infrastructure crée des expériences conviviales en marque blanche.

La stratégie personnalisée vous donne la possibilité de construire des parcours utilisateur avec toutes sortes de combinaisons d’étapes. Par exemple :

* Fédération avec d’autres fournisseurs d’identités
* Problématiques de l’authentification multifacteur (MFA) interne ou de tiers
* Collecte d’entrées utilisateur
* Intégration à des systèmes externes à l’aide de la communication API REST

Chaque parcours utilisateur est défini par une stratégie. Vous pouvez générer autant de stratégies que vous le souhaitez pour offrir une expérience utilisateur optimale pour votre organisation.

![Schéma montrant un exemple de parcours utilisateur complexe activé par IEF](media/user-flow-overview/custom-policy-diagram.png)

Une stratégie personnalisée est définie par plusieurs fichiers XML qui se font mutuellement référence dans une chaîne hiérarchique. Les éléments XML définissent des aspects de l’expérience d’identité, entre autres : le schéma des revendications, les transformations de revendications, les définitions de contenu, les fournisseurs de revendications, les profils techniques et les étapes d’orchestration du parcours utilisateur.

La puissante flexibilité des stratégies personnalisées représente la solution la mieux adaptée lorsque vous devez créer des scénarios d’identité complexes. Les développeurs qui configurent des stratégies personnalisées doivent définir les relations de confiance dans leurs moindres détails pour inclure les points de terminaison de métadonnées et les définitions exactes des échanges de revendications, et configurer les secrets, les clés et les certificats selon les besoins de chaque fournisseur d’identité.

Apprenez-en davantage sur les stratégies personnalisées avec [Stratégies personnalisées dans Azure Active Directory B2C](custom-policy-overview.md).

## <a name="comparing-user-flows-and-custom-policies"></a>Comparaison des flux d’utilisateur et des stratégies personnalisées

Le table suivante fournit une comparaison détaillée des scénarios que vous pouvez rendre possibles avec les flux d’utilisateurs et les stratégies personnalisées Azure AD B2C.

| Context | Flux d’utilisateurs | Stratégies personnalisées |
|-|-------------------|-----------------|
| Utilisateurs cibles | Tous les développeurs d’applications avec ou sans expertise de l’identité | Professionnels de l’identité, intégrateurs système, consultants et équipes d’identité internes. Ils sont à l’aise avec les flux OpenID Connect et comprennent les fournisseurs d’identité et l’authentification basée sur les revendications. |
| Mode de configuration | Portail Azure avec interface utilisateur (UI) conviviale | Modification directe des fichiers XML, puis chargement sur le portail Azure |
| Personnalisation de l’interface utilisateur | [Personnalisation complète de l’interface utilisateur](customize-ui-with-html.md), y compris HTML, CSS et [JavaScript](javascript-and-page-layout.md).<br><br>[Support multilingue](language-customization.md) avec des chaînes personnalisées. | Identique aux flux utilisateur |
| Personnalisation des attributs | Attributs standard et personnalisés | Identique aux flux utilisateur |
| Gestion des jetons et des sessions | [Personnalisez des jetons](configure-tokens.md) et [le comportement de sessions](session-behavior.md). | Identique aux flux utilisateur |
| Identity Providers | [Fournisseur local prédéfini](identity-provider-local.md) ou [social](add-identity-provider.md), tel que la fédération avec des locataires Azure Active Directory. | OIDC basé sur les normes, OAUTH et SAML  L’authentification est également possible via l’intégration avec les API REST. |
| Tâches d’identité | [Inscrivez-vous ou connectez-vous](add-sign-up-and-sign-in-policy.md)avec des comptes locaux et de nombreux comptes sociaux.<br><br>[Réinitialisation de mots de passe en libre service](add-password-reset-policy.md).<br><br>[Modification de profil](add-profile-editing-policy.md).<br><br>Authentification multifacteur<br><br>Flux de jetons d’accès | Réalisation des mêmes tâches que les flux d’utilisateur à l’aide de fournisseurs d’identité personnalisés ou utilisation des étendues personnalisées<br><br>Provisionnement d’un compte d’utilisateur dans un autre système au moment de l’inscription<br><br>Envoi d’un e-mail de bienvenue avec votre propre fournisseur de service de messagerie<br><br>Utilisation d’un magasin d’utilisateurs en dehors d’Azure AD B2C<br><br>Validation des informations fournies par l’utilisateur avec un système approuvé à l’aide d’une API |

## <a name="application-integration"></a>Intégration d’applications

Vous pouvez créer plusieurs flux d’utilisateurs ou stratégies personnalisées de types différents dans votre locataire et les utiliser dans vos applications selon vos besoins. Les flux d’utilisateurs et les stratégies personnalisées peuvent être réutilisés entre les applications. Cette flexibilité vous permet de définir et de modifier les expériences relatives à l’identité avec peu ou pas de modification du code. 

Lorsqu’un utilisateur veut se connecter à votre application, l’application initie une requête d’autorisation sur un point de terminaison fourni par un flux d’utilisateurs ou une stratégie personnalisée. Le flux d’utilisateur ou la stratégie personnalisée définit et contrôle l’expérience de l’utilisateur. Lorsque qu’un flux d’utilisateurs se termine, Azure AD B2C génère un jeton, puis redirige l’utilisateur vers votre application.

![Application mobile, avec des flèches montrant le flux circulant entre l’application et la page de connexion Azure AD B2C](media/user-flow-overview/app-integration.png)

Plusieurs applications peuvent utiliser le même flux utilisateur ou la même stratégie personnalisée. Une seule application peut utiliser plusieurs flux utilisateur ou stratégies personnalisées.

Par exemple, pour se connecter à une application, l’application utilise le flux utilisateur *d’inscription ou de connexion*. Une fois l’utilisateur connecté, il peut vouloir modifier son profil. Pour modifier le profil, l’application initie une autre requête d’autorisation, cette fois-ci au moyen du flux d’utilisateurs de la *modification de profil*.

Votre application déclenche un flux d’utilisateurs à l’aide d’une requête d’authentification HTTP standard qui inclut un flux d’utilisateurs ou une stratégie personnalisée. La réponse reçue est un [jeton](tokens-overview.md) personnalisé.


## <a name="next-steps"></a>Étapes suivantes

- Pour créer les flux d’utilisateur recommandés, suivez les instructions du [Tutoriel : Créer un flux d’utilisateur](tutorial-create-user-flows.md).
- Pour plus d’informations, consultez [Versions de flux d’utilisateurs dans Azure AD B2C](user-flow-versions.md).
- En savoir plus sur les [ Stratégies personnalisées d’Azure AD B2C](custom-policy-overview.md).
