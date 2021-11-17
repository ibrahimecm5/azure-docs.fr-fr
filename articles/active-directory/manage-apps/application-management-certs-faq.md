---
title: Forum aux questions sur les certificats de gestion d’application
titleSuffix: Azure AD
description: Découvrez les réponses du forum aux questions (FAQ) sur la gestion des certificats pour les applications en utilisant Azure Active Directory en tant que fournisseur d’identité (IdP).
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: davidmu
ms.reviewer: sureshja, saumadan
ms.openlocfilehash: e52f06d102c98979005c08f51ef394c29389bbce
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553303"
---
# <a name="application-management-certificates-frequently-asked-questions"></a>Forum aux questions sur les certificats de gestion d’application

Cette page contient les réponses du forum aux questions sur la gestion des certificats pour les applications en utilisant Azure Active Directory (Azure AD) en tant que fournisseur d’identité (IdP).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Existe-t-il un moyen de générer une liste de certificats de signature SAML arrivant à expiration ?

Vous pouvez exporter toutes les inscriptions d’applications avec des secrets et des certificats arrivant à expiration et leurs propriétaires pour les applications spécifiées, de votre annuaire vers un fichier CSV, via des [scripts PowerShell](app-management-powershell-samples.md).

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Où puis-je trouver les informations relatives aux étapes de renouvellement de certificats arrivant bientôt à expiration ?

Vous trouverez la procédure [ici](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Comment personnaliser la date d’expiration des certificats émis par Azure AD ?

Par défaut, Azure AD configure un certificat pour qu’il expire après trois ans quand il est créé automatiquement lors de la configuration de l’authentification unique SAML. Étant donné que vous ne pouvez pas modifier la date d’un certificat après l’avoir enregistré, vous devez créer un nouveau certificat. Pour savoir comme procéder, consultez [Personnaliser la date d’expiration de votre certificat de fédération et activer le nouveau certificat](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate).

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Comment faire pour automatiser les notifications d’expiration des certificats ?

Azure AD enverra un e-mail de notification 60, 30 et 7 jours avant expiration du certificat SAML. Vous pouvez ajouter plusieurs adresses e-mail pour recevoir des notifications.

> [!NOTE]
> Vous pouvez ajouter jusqu’à 5 adresses e-mail à la liste de notification (y compris l’adresse e-mail de l’administrateur qui a ajouté l’application). Si vous avez besoin de notifier un plus grand nombre de personnes, utilisez des e-mails de liste de distribution.

Pour spécifier les e-mails auxquels vous souhaitez que les notifications soient envoyées, consultez [Ajouter des adresses e-mail de notification d’expiration du certificat](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration).

Aucune option ne permet de modifier ou de personnaliser ces notifications par e-mail reçues de `aadnotification@microsoft.com`. Toutefois, vous pouvez exporter des inscriptions d’applications avec des secrets et certificats arrivant à expiration via des [scripts PowerShell](app-management-powershell-samples.md).

## <a name="who-can-update-the-certificates"></a>Qui peut mettre à jour les certificats ?

Le propriétaire de l’application, l’administrateur général ou de l’administrateur de l’application peut mettre à jour les certificats via l’interface du portail Azure, PowerShell ou Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>J’ai besoin de plus de détails sur les options de signature de certificat

Dans Azure AD, vous pouvez configurer les options de signature de certificat et l’algorithme de signature de certificat. Pour en savoir plus, consultez [Options avancées de signature de certificat de jeton SAML pour les applications Azure AD](certificate-signing-options.md).

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Je dois remplacer le certificat pour les applications Proxy d’application Azure AD et j’ai besoin d’instructions supplémentaires

Pour remplacer les certificats pour des applications de proxy d’application Azure AD, consultez [Exemple PowerShell – Remplacer le certificat des applications Proxy d’application](../app-proxy/scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Comment faire pour gérer des certificats pour des domaines personnalisés dans le proxy d’application Azure AD ?

Pour configurer une application locale afin d’utiliser un domaine personnalisé, vous devez disposer d’un domaine personnalisé Azure Active Directory vérifié, d’un certificat PFX pour le domaine personnalisé et d’une application locale à configurer. Pour en savoir plus, consultez [Domaines personnalisés dans le proxy d’application Azure AD](../app-proxy/application-proxy-configure-custom-domain.md).

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Je dois mettre à jour le certificat de signature de jetons côté application. Où puis-je l’obtenir côté Azure AD ?

Vous pouvez renouveler un certificat X.509 SAML. Pour savoir comment précéder, consultez [Certificat de signature SAML](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Qu’est-ce qu’une substitution de clé de signature Azure AD ?

Vous trouverez plus d’informations [ici](../develop/active-directory-signing-key-rollover.md).

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Comment faire pour renouveler un certificat de chiffrement de jeton d’application ?

Pour renouveler un certificat de chiffrement de jeton d’application, consultez [Comment renouveler un certificat de chiffrement de jeton pour une application d’entreprise](howto-saml-token-encryption.md).

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Comment faire pour renouveler un certificat de signature de jeton d’application ?

Pour renouveler un certificat de signature de jeton d’application, consultez [Comment renouveler un certificat de signature de jeton pour une application d’entreprise](manage-certificates-for-federated-single-sign-on.md).

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Comment faire pour mettre à jour Azure AD après avoir modifié mes certificats de fédération ?

Pour mettre à jour Azure AD après avoir modifié vos certificats de fédération, consultez [Renouveler des certificats de fédération pour Microsoft 365 et Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md).
