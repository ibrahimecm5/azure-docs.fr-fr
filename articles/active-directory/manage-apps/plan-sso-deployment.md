---
title: Planifier un déploiement de l’authentification unique
description: Planifiez le déploiement de l’authentification unique dans Azure Active Directory.
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
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8f8140362a7b2709aaf1ea635e61a43e84eb503a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546573"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planifier un déploiement de l’authentification unique

Cet article fournit des informations que vous pouvez utiliser pour planifier votre déploiement de l’[authentification unique (SSO)](what-is-single-sign-on.md) dans Azure Active Directory (Azure AD). Lorsque vous planifiez votre déploiement de l’authentification unique avec vos applications dans Azure AD, vous devez prendre en compte les questions suivantes :

- Quels sont les rôles d’administration requis pour la gestion de l’application ?
- Le certificat doit-il être renouvelé ?
- Qui doit être informé des changements liés à l’implémentation de l’authentification unique ?
- Quelles sont les licences nécessaires pour garantir une gestion efficace de l’application ?
- Les comptes d’utilisateur partagés sont-ils utilisés pour accéder à l’application ?
- Ai-je compris les options de déploiement de l’authentification unique ?

## <a name="administrative-roles"></a>Rôles d’administration

Utilisez toujours le rôle ayant le moins de privilèges possible pour accomplir une tâche dans Azure AD. Passez en revue les différents rôles disponibles et choisissez celui qui répond le mieux aux besoins de chaque personnage de l’application. Certains rôles devront peut-être être appliqués temporairement, puis supprimés une fois le déploiement terminé.

| Utilisateur | Rôles | Rôle Azure AD (le cas échéant) |
| ------- | ----- | --------------------------- |
| Administrateur du support technique | Support de niveau 1 | None |
| Administrateur d’identité | Configuration et débogage lorsque les problèmes concernent Azure AD | Administrateur général |
| Administrateur d’application | Attestation de l’utilisateur dans l’application, configuration des utilisateurs disposant d’autorisations | None |
| Administrateurs de l’infrastructure | Propriétaire de la substitution de certificat | Administrateur général |
| Chef d’entreprise/Partie prenante | Attestation de l’utilisateur dans l’application, configuration des utilisateurs disposant d’autorisations | None |

Pour en savoir plus sur les rôles d’administrateur Azure AD, consultez [Rôles intégrés Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

## <a name="certificates"></a>Certificats

Lorsque vous activez l’authentification unique fédérée pour votre application, Azure AD crée un certificat qui est par défaut valide pendant trois ans. Vous pouvez personnaliser la date d’expiration de ce certificat si nécessaire. Vérifiez que vous disposez de processus permettant de renouveler les certificats avant leur expiration. 

Cette durée peut être modifiée dans le portail Azure. Assurez-vous de documenter l’expiration et de choisir comment gérer le renouvellement des certificats. Il est important d’identifier les rôles et les groupes de distribution d’e-mails appropriés qui sont impliqués dans la gestion du cycle de vie du certificat de signature. Les rôles suivants sont recommandés :

- Un propriétaire pour mettre à jour les propriétés utilisateur dans l’application
- Un propriétaire d’astreinte pour le dépannage de l’application
- Une liste de distribution e-mail surveillée de près pour les notifications de modification des certificats

Vous pouvez utiliser les méthodes suivantes pour gérer vos certificats :

- **Substitution automatique des certificats** : La substitution de la clé de signature est prise en charge dans Azure AD. Bien que la substitution de la clé de signature soit la méthode préférée pour la gestion des certificats, toutes les applications ne prennent pas en charge ce scénario.

- **Mise à jour manuelle** : Chaque application a son propre certificat, qui expire à une date définie. Avant que le certificat de l’application n’expire, créez un certificat puis envoyez-le au fournisseur de l’application. Ces informations peuvent être tirées (pull) à partir des métadonnées de fédération. Pour plus d’informations, consultez la section [Métadonnées de fédération](../azuread-dev/azure-ad-federation-metadata.md). 

Configurez un processus vous permettant de gérer le changement de certificat entre Azure AD et votre application. En mettant en place ce processus, vous pouvez éviter ou réduire au minimum une panne due à l’expiration d’un certificat ou à la substitution forcée d’un certificat. Pour plus d’informations, voir [Gérer les certificats pour l’authentification unique fédérée dans Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

## <a name="communications"></a>Communications

La communication est essentielle à la réussite de tout nouveau service. Communiquez de manière proactive avec vos utilisateurs sur la façon dont leur expérience va changer. Indiquez-leur à quel moment cela va se faire et comment il est possible d’obtenir de l’aide en cas de problème. Passez en revue les différentes façons dont les utilisateurs peuvent accéder à leurs applications avec authentification unique, et adaptez vos communications à chaque cas de figure.

Créez votre plan de communication. Veillez à ce que vos utilisateurs sachent qu’un changement est à venir, quand il aura lieu et ce qu’ils doivent faire maintenant. Veillez également à fournir des informations sur la manière de demander de l’aide.

## <a name="licensing"></a>Licences

Assurez-vous que l’application est couverte par les conditions de licence suivantes :

- **Gestion des licences Azure AD** : Pour les applications d’entreprise préintégrées, l’authentification unique est gratuite. Toutefois, le nombre d’objets contenus dans votre répertoire et les fonctionnalités que vous souhaitez déployer peuvent nécessiter des licences supplémentaires. Pour obtenir la liste complète des conditions de licence, consultez la page [Tarification Azure Active Directory](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

- **Licences d’application** : Le type de licence que vous choisissez pour vos applications doit répondre aux besoins de votre entreprise. Contactez le propriétaire de l’application pour déterminer si les utilisateurs affectés à l’application disposent de licences adaptées à leurs rôles dans l’application. Si Azure AD gère l’attribution automatique d’utilisateurs en fonction des rôles, les rôles attribués dans Azure AD doivent s’aligner sur le nombre de licences détenues au sein de l’application. Si le nombre de licences détenues dans l’application est incorrect, cela peut entraîner des erreurs pendant l’approvisionnement ou la mise à jour d’un compte d’utilisateur.

## <a name="shared-accounts"></a>Comptes partagés

Du point de vue de la connexion, les applications avec comptes partagés ne sont pas différentes des applications d’entreprise qui utilisent l’authentification unique par mot de passe pour les utilisateurs individuels. Toutefois, davantage d’étapes sont nécessaires lorsque vous planifiez et configurez une application conçue pour utiliser des comptes partagés.
- Collaborez avec les utilisateurs pour documenter les informations suivantes :
    - Les groupes d’utilisateurs de l’organisation qui utiliseront l’application.
    - Les informations d’identification existantes de l’application qui sont associées à chaque groupe d’utilisateur.
- Pour chaque combinaison groupe d’utilisateurs-informations d’identification, créez un groupe de sécurité localement ou dans le cloud, selon vos besoins.
- Réinitialisez les informations d’identification partagées. Une fois que l’application est déployée dans Azure AD, les utilisateurs n’ont plus besoin du mot de passe du compte partagé. Azure AD stocke le mot de passe, et vous devriez envisager de le configurer pour qu’il soit long et complexe.
- Si l’application le prend en charge, configurez la substitution automatique du mot de passe. De cette façon, même l’administrateur qui a effectué la configuration initiale ne connaît pas le mot de passe du compte partagé.

## <a name="single-sign-on-options"></a>Options d’authentification unique

Vous pouvez configurer une application pour l’authentification unique de plusieurs façons. Le choix d’une méthode d’authentification unique dépend de la façon dont l’application est configurée pour l’authentification.
- Pour l’authentification unique, les applications cloud peuvent utiliser les méthodes suivantes : OpenID Connect, OAuth, SAML, par mot de passe ou liée. L’authentification unique peut également être désactivée.
- Pour l’authentification unique, les applications locales peuvent utiliser les méthodes suivantes : par mot de passe, Authentification Windows intégrée, par en-tête ou liée. Les choix pour les applications locales fonctionnent quand les applications sont configurées pour [Proxy d’application](../app-proxy/what-is-application-proxy.md).

Cet organigramme vous permet de décider quelle méthode d’authentification unique est la plus adaptée à votre situation.

![Organigramme de décision pour la méthode d’authentification unique](./media/plan-sso-deployment/single-sign-on-options.png)
 
Les protocoles d’authentification unique suivants peuvent être utilisés :

- **OpenID Connect et OAuth** : Choisissez OpenID Connect et OAuth 2.0 si l’application à laquelle vous vous connectez les prend en charge. Pour plus d’informations, consultez [Protocoles OAuth 2.0 et OpenID Connect sur la plateforme d’identités Microsoft](../develop/active-directory-v2-protocols.md). Pour connaître les étapes à suivre pour implémenter l’authentification unique OpenID Connect, consultez [Configurer l’authentification unique OIDC pour une application dans Azure Active Directory](add-application-portal-setup-oidc-sso.md).

- **SAML** : Choisissez SAML dans la mesure du possible pour les applications existantes qui n’utilisent pas ni OpenID Connect ni OAuth. Pour plus d’informations, consultez [Protocole SAML d’authentification unique](../develop/single-sign-on-saml-protocol.md). Pour une présentation rapide de l’implémentation de l’authentification unique SAML, consultez [Démarrage rapide : Configurer l’authentification unique SAML pour une application dans Azure Active Directory](add-application-portal-setup-sso.md). 

- **Par mot de passe** : choisissez l’option par mot de passe lorsque l’application dispose d’une page de connexion HTML. L’authentification unique par mot de passe est également connue sous le nom d’archivage des mots de passe. L’authentification unique par mot de passe vous permet de gérer l’accès utilisateur et les mots de passe pour les applications web qui ne prennent pas en charge la fédération d’identité. Elle est également utile lorsque plusieurs utilisateurs doivent partager un seul compte, par exemple les comptes d’applications de réseaux sociaux de votre organisation. 

    L’authentification unique par mot de passe prend en charge les applications qui requièrent plusieurs champs de connexion et qui demandent d’autres champs en plus du nom d’utilisateur et du mot de passe pour la connexion. Vous pouvez personnaliser les étiquettes des champs de nom d’utilisateur et de mot de passe que vos utilisateurs voient dans Mes applications quand ils entrent leurs informations d’identification. Pour plus d’instructions sur l’implémentation de l’authentification unique par mot de passe, consultez [Authentification unique par mot de passe](configure-password-single-sign-on-non-gallery-applications.md).

- **Liée** : Choisissez l’authentification liée quand l’application est configurée pour l’authentification unique dans un autre service de fournisseur d’identité. L’option liée vous permet de configurer l’emplacement cible lorsqu’un utilisateur sélectionne l’application dans les portails de votre organisation. Vous pouvez ajouter un lien vers une application web personnalisée qui utilise actuellement la fédération, par exemple les services de fédération Active Directory (AD FS). 

    Vous pouvez également ajouter des liens vers des pages web spécifiques que vous souhaitez voir apparaître dans les panneaux d’accès de l’utilisateur et vers une application qui ne requiert pas d’authentification. L’option Authentification liée ne fournit pas de fonctionnalité d’authentification via des informations d’identification Azure AD. Pour connaître les étapes à suivre pour implémenter l’authentification unique liée, consultez [Authentification unique liée](configure-linked-sign-on.md).

- **Désactivée** : Choisissez l’authentification unique désactivée lorsque l’application n’est pas prête à être configurée pour l’authentification unique.

- **Authentification Windows intégrée (IWA)**  : Choisissez l’authentification unique IWA pour les applications qui utilisent IWA ou pour les applications prenant en charge les revendications. Pour plus d’informations, consultez [Délégation contrainte Kerberos pour l’authentification unique à vos applications avec Proxy d’application](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md).

- **Par en-tête** : Choisissez l’authentification unique par en-tête quand l’application utilise des en-têtes pour l’authentification. Pour plus d’informations, consultez [Authentification unique par en-tête](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md).

## <a name="next-steps"></a>Étapes suivantes
- [Gérer l’accès aux applications](what-is-access-management.md)
