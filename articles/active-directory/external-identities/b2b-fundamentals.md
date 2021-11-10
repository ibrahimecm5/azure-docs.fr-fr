---
title: Meilleures pratiques et recommandations relatives à Azure Active Directory B2B
description: Découvrez les meilleures pratiques et recommandations pour l'accès des utilisateurs invités B2B dans Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5f819cbffc04d79ef053d89af3ea865d3dd244c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251107"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Bonnes pratiques relatives à Azure Active Directory B2B
Cet article contient des recommandations et des meilleures pratiques pour la collaboration interentreprises (B2B) dans Azure Active Directory (Azure AD).

> [!IMPORTANT]
> **À partir du 1er novembre 2021**, nous allons commencer à déployer un changement afin d’activer la fonctionnalité de code secret à usage unique envoyé par e-mail pour tous les locataires existants et de l’activer par défaut pour les nouveaux locataires. Dans le cadre de cette modification, Microsoft cessera de créer des comptes et des locataires Azure AD non managés (« viraux ») lors de l’acceptation d’invitation de collaboration B2B. Pour réduire au minimum les perturbations pendant les fêtes de fin d’année et les blocages de déploiements, les modifications seront déployées pour la majorité des locataires en janvier 2022. Il s’agit en effet d’une méthode d’authentification de secours transparente pour les utilisateurs invités. Cependant, si vous ne voulez pas autoriser l’activation automatique de cette fonctionnalité, vous pouvez [la désactiver](one-time-passcode.md#disable-email-one-time-passcode).


## <a name="b2b-recommendations"></a>Recommandations B2B
| Recommandation | Commentaires |
| --- | --- |
| Pour une expérience de connexion optimale, associez-vous aux fournisseurs d’identité | Dans la mesure du possible, associez-vous directement aux fournisseurs d’identité pour permettre aux utilisateurs invités de se connecter à vos applications et ressources partagées sans avoir à créer des comptes Microsoft (MSAs) ou des comptes de Azure AD. Vous pouvez utiliser la fonctionnalité [Fédération de Google](google-federation.md) pour autoriser les utilisateurs invités B2B à se connecter avec leurs comptes Google. Ou, vous pouvez utiliser la [fonction de fournisseur d’identité SAML/WS-Fed (préversion)](direct-federation.md) pour configurer la fédération directe avec toute organisation dont le fournisseur d’identité (IdP) prend en charge le protocole SAML 2.0 ou WS-Fed. |
| Utilisez la fonctionnalité d’envoi d’un code secret à usage unique par e-mail pour les invités B2B qui ne peuvent pas s’authentifier par d’autres moyens | La fonctionnalité [Envoi d’un code secret à usage unique par e-mail](one-time-passcode.md) permet d’authentifier les utilisateurs invités B2B lorsqu’il n’est pas possible de les authentifier par d’autres moyens, comme Azure AD, un compte Microsoft (MSA) ou la fédération Google. Lorsque l’utilisateur invité accepte une invitation ou accède à une ressource partagée, il peut demander un code temporaire, qui est envoyé à son adresse e-mail. Puis, il entre ce code pour se connecter. |
| Ajouter votre marque à votre page de connexion | Vous pouvez personnaliser votre page de connexion afin qu’elle soit plus intuitive pour vos utilisateurs invités B2B. Découvrez comment [ajouter une marque de société aux pages de connexion et du volet d’accès](../fundamentals/customize-branding.md). |
| Ajoutez votre déclaration de confidentialité à l’expérience d’échange d’utilisateurs invités B2B | Vous pouvez ajouter l’URL de la déclaration de confidentialité de votre organisation au processus d’acceptation d’invitation envoyé pour la première fois à l’utilisateur invité. Il devra ainsi donner son consentement à vos conditions de confidentialité pour continuer. Découvrez [comment faire : pour ajouter les informations de confidentialité de votre organisation dans Azure Active Directory](../fundamentals/active-directory-properties-area.md). |
| Utilisez la fonctionnalité d’invitation en bloc (préversion) pour inviter plusieurs utilisateurs invités B2B en même temps | Invitez plusieurs utilisateurs à votre organisation en même temps à l’aide de la fonctionnalité d’aperçu en bloc des invitations dans le Portail Azure. Cette fonctionnalité vous permet de télécharger un fichier CSV pour créer des utilisateurs invités B2B et d’envoyer des invitations en bloc. Consultez le [tutoriel vous expliquant comment inviter des utilisateurs B2B en bloc](tutorial-bulk-invite.md). |
| Appliquer les stratégies d’accès conditionnel pour l’authentification multifacteur (MFA) Azure Active Directory | Nous vous recommandons d’appliquer des stratégies MFA sur les applications que vous souhaitez partager avec les utilisateurs B2B partenaires. De cette façon, l’authentification MFA est appliquée de manière cohérente sur les applications de votre locataire, que l’organisation partenaire utilise ou non l’authentification multifacteur. Consultez [Accès conditionnel pour les utilisateurs de B2B Collaboration](conditional-access.md) |
| Si vous appliquez des stratégies d’accès conditionnel en fonction de l’appareil, utilisez des listes d’exclusion pour autoriser l’accès aux utilisateurs B2B | Si les stratégies d’accès conditionnel en fonction de l’appareil sont activées dans votre organisation, les appareils des utilisateurs invités B2B sont bloqués, car ils ne sont pas gérés par votre organisation. Vous pouvez créer des listes d’exclusion contenant des utilisateurs partenaires spécifiques pour les exclure de la stratégie d’accès conditionnel en fonction de l’appareil. Consultez [Accès conditionnel pour les utilisateurs de B2B Collaboration](conditional-access.md) |
| Utilisez une URL spécifique au locataire lorsque vous fournissez des liens directs à vos utilisateurs invités B2B | Comme alternative à l’e-mail d’invitation, vous pouvez donner à un invité un lien direct à votre application ou à votre portail. Ce lien direct doit être spécifique au client, ce qui signifie qu’il doit inclure un ID de locataire ou un domaine vérifié afin que l’invité puisse être authentifié dans votre locataire, où se trouve l’application partagée. Consultez [Expérience d’échange pour l’utilisateur invité](redemption-experience.md). |
| Lorsque vous développez une application, utilisez UserType pour déterminer l’expérience de l’utilisateur invité  | Si vous développez une application et que vous souhaitez fournir une expérience différente pour les utilisateurs clients et les utilisateurs invités, utilisez la propriété UserType. La revendication UserType n’est pour le moment pas incluse dans le jeton. Les applications doivent utiliser l’API Microsoft Graph pour interroger l’annuaire de l’utilisateur afin d’obtenir son UserType. |
| Modifiez la propriété UserType *uniquement* si la relation de l’utilisateur avec l’organisation change | Bien qu’il soit possible d’utiliser PowerShell pour convertir la propriété UserType d’un utilisateur de membre en invité (et inversement), vous devez modifier cette propriété uniquement si la relation de l’utilisateur avec votre organisation change. Consultez [Propriétés d’un utilisateur invité B2B](user-properties.md).|

## <a name="next-steps"></a>Étapes suivantes

[Gérer le partage B2B](delegate-invitations.md)