---
title: Galerie de partenaires ISV pour Azure AD B2C
titleSuffix: Azure AD B2C
description: 'Découvrez comment intégrer les solutions de nos partenaires ISV pour adapter l’expérience de vos utilisateurs finaux à vos besoins. Notre réseau de partenaires étend les fonctionnalités de nos solutions : activez MFA, activez l’authentification forte du client, appliquez le contrôle d’accès en fonction du rôle et luttez contre les fraudes grâce à la vérification et la confirmation d’identité.'
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: bf58338bfc16e2e0cea11183ad4fdf3e72961bb3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224914"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Partenaires ISV pour Azure Active Directory B2C

Notre réseau de partenaires ISV étend les fonctionnalités de nos solutions et vous aide ainsi à offrir des expériences fluides aux utilisateurs finaux. Azure AD B2C vous pouvez de vous intégrer avec des partenaires fournisseurs de logiciels indépendants pour activer des méthodes d’authentification multifacteur (MFA), effectuer un contrôle d’accès en fonction du rôle, activer la vérification et la confirmation d’identité, améliorer la sécurité grâce à la détection des bots et la protection contre les fraudes, et répondre aux exigences en matière d’authentification sécurisée des clients de la directive 2 sur les services de paiement (PSD2). Suivez nos exemples de procédures détaillées pour découvrir comment intégrer des applications avec les partenaires fournisseurs de logiciels indépendants. 

Pour être pris en compte dans cet exemple de documentation, envoyez votre demande d’application dans le [portail Microsoft Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx). Pour toute question supplémentaire, envoyez un e-mail à [SaaSApplicationIntegrations@service.microsoft.com](mailto:SaaSApplicationIntegrations@service.microsoft.com).

>[!NOTE]
>Sur le [site de la communauté Azure Active Directory B2C sur GitHub](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/), vous trouverez également des exemples de stratégies personnalisées fournis par la communauté.

## <a name="identity-verification-and-proofing"></a>Vérification des identités

Microsoft travaille en partenariat avec les fournisseurs de logiciels indépendants (ISV) suivants pour la vérification et la confirmation d’identité.

| Partenaire fournisseur de logiciels indépendant | Description et procédures pas à pas d’intégration |
|:-------------------------|:--------------|
|![Capture d’écran d’un logo Experian.](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) est un fournisseur de solutions de vérification et de confirmation d’identité qui effectue des évaluations de risques basées sur les attributs utilisateur pour empêcher les fraudes. |
|![Capture d’écran d’un logo IDology.](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) est un fournisseur de solutions de vérification et de confirmation d’identité, comprenant entre autres des solutions de vérification d’identité, des solutions de prévention des fraudes et des solutions de conformité.|
|![Capture d’écran d’un logo Jumio.](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) est un service de vérification d’ID, qui permet de vérifier l’ID automatisé en temps réel et de protéger les données client. |
| ![Capture d’écran d’un logo LexisNexis.](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) est un fournisseur de solutions de profilage et de validation d’identité qui vérifie l’identification des utilisateurs et fournit des évaluations de risques complètes basées sur l’appareil de l’utilisateur. |
| ![Capture d’écran d’un logo Onfido](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) est un ID de document et une solution de vérification de biométrie faciale qui permet aux entreprises de répondre au exigences *de connaître la clientèle*  et d’identités en temps réel.  |

## <a name="mfa-and-passwordless-authentication"></a>Authentification multifacteur et authentification sans mot de passe

Microsoft travaille en partenariat avec les fournisseurs de logiciels indépendants (ISV) suivants pour l’authentification multifacteur et l’authentification sans mot de passe.

| Partenaire fournisseur de logiciels indépendant | Description et procédures pas à pas d’intégration |
|:-------------------------|:--------------|
| ![Capture d’écran d’un logo BlokSec](./media/partner-gallery/bloksec-logo.png) | [BlokSec](./partner-bloksec.md) est une solution d’authentification sans mot de passe et MFA sans jeton, qui fournit des services en temps réel basés sur le consentement et qui protège les clients contre les cyberattaques axées sur l’identité, telles que l’usurpation de mot de passe, l’hameçonnage et les attaques de l’intercepteur. |
| ![Capture d’écran d’un logo Hypr](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) est un fournisseur d’authentification sans mot de passe qui remplace les mots de passe par des chiffrements à clé publique, éliminant les fraudes, le hameçonnage et la réutilisation des informations d’identification. |
| ![Capture d’écran d’un logo idemia](./media/partner-gallery/idemia-logo.png) | [IDEMIA](./partner-idemia.md) est un fournisseur d’authentification sans mot de passe, qui fournit des services basés sur le consentement en temps réel avec une authentification biométrique comme faceID et une empreinte digitale, éliminant la fraude et la réutilisation des informations d’identification. |
| ![Capture d’écran d’un logo Itsme](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) est une solution d’identification numérique conforme aux normes eiDAS (Electronic Identification, Authentication and Trust Services) qui permet aux utilisateurs de se connecter en toute sécurité sans avoir à utiliser de lecteurs de cartes, de mots de passe, de méthode d’authentification à deux facteurs ni de codes PIN multiples. |
|![Capture d’écran d’un logo Keyless.](./media/partner-gallery/keyless-logo.png) | [Keyless](./partner-keyless.md) est un fournisseur d’authentification sans mot de passe qui propose une authentification sous la forme d’une analyse biométrique faciale et élimine ainsi la fraude, le hameçonnage et la réutilisation des informations d’identification.
| ![Capture d’écran d’un logo Nevis](./media/partner-gallery/nevis-logo.png) | [Nevis](./partner-nevis.md) permet une authentification sans mot de passe et offre une expérience utilisateur final axée sur l’interface mobile, entièrement personnalisée avec l’application Nevis Access pour une authentification forte du client et pour garantir le respect des obligations relatives aux transactions de la directive 2 sur les services de paiement (PSD2). |
| ![Capture d’écran d’un logo nok nok](./media/partner-gallery/nok-nok-logo.png) | [Nok Nok](./partner-nok-nok.md) fournit une authentification sans mot de passe et active l’authentification multifactorielle certifiée FIDO, comme FIDO UAF, FIDO U2F, WebAuthn et FIDO2 pour les applications mobiles et web. Les clients Nok Nok peuvent améliorer leur sécurité tout en équilibrant l’expérience utilisateur.
| ![Capture d’écran d’un logo Trusona](./media/partner-gallery/trusona-logo.png) | L’intégration avec [Trusona](./partner-trusona.md) vous aide à sécuriser vos connexions, et permet l’authentification sans mot de passe, l’authentification multifacteur et l’analyse de licence numérique. |
| ![Capture d’écran d’un logo Twilio](./media/partner-gallery/twilio-logo.png) | [Twilio Verify App](./partner-twilio.md) fournit plusieurs solutions qui permettent l’authentification multifacteur (MFA) par l’envoi par SMS d’un mot de passe à usage unique (OTP), d’un mot de passe à usage unique et durée définie (TOTP) ou de notifications Push, et aident à se conformer aux exigences en matière d’authentification sécurisée des clients de la directive 2 sur les services de paiement (PSD2). |
| ![Capture d’écran d’un logo typingDNA](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) permet une authentification forte du client en analysant le modèle de saisie d’un utilisateur. Il permet aux entreprises d’activer une authentification multifacteur sans assistance et de se conformer aux exigences en matière d’authentification sécurisée des clients de la directive 2 sur les services de paiement (PSD2). |
| ![Capture d’écran d’un logo WhoIAM](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) est une application BRIMS (Branded Identity Management System) qui permet aux organisations de vérifier leur base d’utilisateurs par la voix, les SMS et les e-mails. |

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle 
 
Microsoft travaille en partenariat avec les fournisseurs de logiciels indépendants (ISV) suivants pour le contrôle d’accès en fonction du rôle.

| Partenaire fournisseur de logiciels indépendant | Description et procédures pas à pas d’intégration |
|:-------------------------|:--------------|
| ![Capture d’écran d’un logo n8identity](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) est une plateforme de gouvernance d’identité en tant que service qui fournit une solution pour répondre à la migration des comptes clients et à l’administration CSR (Customer Service Requests) s’exécutant sur Microsoft Azure. |
| ![Capture d’écran d’un logo Saviynt](./media/partner-gallery/saviynt-logo.png) | La plateforme [Saviynt](./partner-Saviynt.md) native Cloud améliore la sécurité, la conformité et la gouvernance grâce à une analytique intelligente et à une intégration entre applications pour rationaliser la modernisation informatique. |

## <a name="secure-hybrid-access-to-on-premises-application"></a>Accès hybride sécurisé à une application locale

Microsoft travaille en partenariat avec les fournisseurs de logiciels indépendants (ISV) suivants pour fournir un accès hybride sécurisé à une application locale. 

| Partenaire fournisseur de logiciels indépendant | Description et procédures pas à pas d’intégration |
|:-------------------------|:--------------|
| ![Capture d’écran d’un logo Datawiza](./media/partner-gallery/datawiza-logo.png) | [Datawiza](./partner-datawiza.md) permet l’authentification unique (SSO) et le contrôle d’accès granulaire pour vos applications, et étend Azure AD B2C pour la protection des applications héritées locales.  |
| ![Capture d’écran d’un logo F5](./media/partner-gallery/f5-logo.png) | [F5](./partner-f5.md) permet aux applications héritées d’exposer en toute sécurité sur internet via la sécurité BIG-IP combinée à la pré-authentification Azure AD B2C, à l’accès conditionnel (CA) et à l’authentification unique.  |
| ![Capture d’écran d’un logo Ping](./media/partner-gallery/ping-logo.png) | [Ping Identity](./partner-ping-identity.md) permet un accès hybride sécurisé aux applications héritées locales sur plusieurs clouds. |
| ![Capture d’écran d’un logo Strata](./media/partner-gallery/strata-logo.png) | [Strata](./partner-strata.md) fournit un accès hybride sécurisé à des applications locales en appliquant des stratégies d’accès cohérentes, en assurant la synchronisation des identités et en simplifiant la transition des applications entre des systèmes d’identité hérités et le contrôle d’accès et l’authentification standard fournis par Azure AD B2C. |
| ![Capture d’écran d’un logo Zscaler](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) fournit un accès sécurisé basé sur des stratégies aux applications privées et aux ressources, sans les risques liés aux coûts, à la charge ou à la sécurité d’un réseau privé virtuel (VPN). |

## <a name="fraud-protection"></a>Protection contre les fraudes

Microsoft travaille en partenariat avec les fournisseurs de logiciels indépendants (ISV) suivants pour la détection et la prévention des fraudes. 

| Partenaire fournisseur de logiciels indépendant | Description et procédures pas à pas d’intégration |
|:-------------------------|:--------------|
| ![Capture d’écran d’un logo Arkose Labs](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) est un fournisseur de solutions de prévention des fraudes qui aide les organisations à se protéger contre les attaques de bots, les attaques de prise de contrôle de comptes et les ouvertures de comptes frauduleuses. |
| ![Capture d’écran d’un logo BioCatch](./media/partner-gallery/biocatch-logo.png) | [BioCatch](./partner-biocatch.md) est un fournisseur de solutions de prévention des fraudes qui analyse les comportements numériques cognitifs et physiques d’un utilisateur afin de générer des informations permettant de distinguer les clients légitimes des cybercriminels. |
| ![Capture d’écran d’un logo Microsoft Dynamics 365](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Microsoft Dynamics 365 Fraud Protection](./partner-dynamics-365-fraud-protection.md) est une solution qui aide les organisations à se protéger contre les ouvertures de comptes frauduleuses par le biais d’une prise d’empreinte des appareils. |

## <a name="web-application-firewall"></a>Pare-feu d’applications web 

Microsoft travaille en partenariat avec les fournisseurs de logiciels indépendants (ISV) suivants pour le pare-feu d’applications web (WAF). 

| Partenaire fournisseur de logiciels indépendant | Description et procédures pas à pas d’intégration |
|:-------------------------|:--------------|
|  ![Capture d’écran d’un logo Akamai](./media/partner-gallery/akamai-logo.png) | [Pare-feu d’applications web Akamai](./partner-akamai.md) permet une manipulation précise du trafic afin de protéger et de sécuriser votre infrastructure d’identité contre les attaques malveillantes.  |
|  ![Capture d’écran du logo Azure WAF](./media/partner-gallery/azure-web-application-firewall-logo.png) | [Azure WAF](./partner-azure-web-application-firewall.md) protège de manière centralisée vos applications web contre les vulnérabilités et attaques courantes.  |
![Capture d’écran du logo Cloudflare](./media/partner-gallery/cloudflare-logo.png) | [Cloudflare](./partner-cloudflare.md) est un fournisseur de WAF qui aide les organisations à se protéger contre les attaques malveillantes visant à exploiter des vulnérabilités telles que SQLi et XSS. |


## <a name="additional-information"></a>Informations supplémentaires

- [Stratégies personnalisées dans Azure AD B2C](./custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

## <a name="next-steps"></a>Étapes suivantes

Sélectionnez un partenaire dans les tableaux mentionnés pour savoir comment intégrer leur solution avec Azure AD B2C.
