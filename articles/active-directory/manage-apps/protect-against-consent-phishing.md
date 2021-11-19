---
title: Protéger contre le hameçonnage de consentement
titleSuffix: Azure AD
description: Apprenez comment atténuer les attaques par hameçonnage de consentement basé sur l’application à l’aide de Azure AD.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: tilarso
ms.openlocfilehash: cad0102bf76cd63553049045ea55e0e7fb8f16d0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550681"
---
# <a name="protecting-against-consent-phishing"></a>Protéger contre le hameçonnage de consentement

La productivité n’est plus limitée aux réseaux privés et le travail a été déplacé vers les services Cloud. Alors que les applications Cloud permettent aux employés d’être productifs à distance, les attaquants peuvent également utiliser des attaques basées sur les applications pour accéder à des données d’entreprise précieuses. Vous connaissez peut-être les attaques qui se concentrent sur les utilisateurs, telles que le hameçonnage par e-mail ou la compromission des informations d’identification. Le ***hameçonnage de consentement*** est un autre vecteur de menace à connaître.
Cet article explore ce qu’est le hameçonnage de consentement, ce que Microsoft fait pour vous protéger et les étapes que les organisations peuvent prendre pour rester en sécurité.

## <a name="what-is-consent-phishing"></a>Qu’est-ce que le hameçonnage de consentement ?

Les attaques par hameçonnage de consentement incitent les utilisateurs à accorder des autorisations à des applications cloud malveillantes. Ces applications malveillantes peuvent alors accéder aux services et aux données cloud légitimes des utilisateurs. Contrairement à la compromission des informations d’identification, les *acteurs des menaces* qui effectuent du hameçonnage de consentement ciblent les utilisateurs qui peuvent accorder directement l’accès à leurs données personnelles ou organisationnelles. L’écran de consentement affiche toutes les autorisations que l’application reçoit. Étant donné que l’application est hébergée par un fournisseur légitime (tel que la plateforme d’identité de Microsoft), les utilisateurs non suspects acceptent les termes ou tapent sur « *Accepter* », ce qui octroient à une application malveillante les autorisations demandées aux données de l’utilisateur ou de l’organisation.

:::image type="content" source="./media/protect-consent-phishing/permissions-requested.png" alt-text="Capture d’écran montrant la fenêtre autorisations demandées nécessitant le consentement de l’utilisateur.":::

*Exemple d’une application OAuth qui demande l’accès à un large éventail d’autorisations.*

## <a name="mitigating-consent-phishing-attacks-using-azure-ad"></a>Atténuation des attaques par hameçonnage de consentement avec Azure AD

Les administrateurs, les utilisateurs ou les chercheurs en sécurité Microsoft peuvent signaler des applications OAuth qui semblent se comporter de façon suspecte. Une application signalée sera examinée par Microsoft pour déterminer si l’application viole les conditions d’accès. Si une violation est confirmée, Azure AD désactivera l’application et empêchera toute utilisation ultérieure sur l’ensemble des services Microsoft.

Lorsque Azure AD désactive une application OAuth, quelques événements se produisent :
- L’application malveillante et les principaux de service associés sont placés dans un état totalement désactivé. Toutes les nouvelles demandes de jeton ou demandes de jetons d’actualisation seront refusées, mais les jetons d’accès existants seront toujours valides jusqu’à leur expiration.
- Nous présentons l’état désactivé par le biais d’une propriété exposée appelée *disabledByMicrosoftStatus* sur les types de ressources [application](/graph/api/resources/application?view=graph-rest-1.0&preserve-view=true) et [principal de service](/graph/api/resources/serviceprincipal?view=graph-rest-1.0&preserve-view=true) associés dans Microsoft Graph.
- Les administrateurs généraux qui ont pu avoir un utilisateur de l’organisation qui a consenti à une application avant la désactivation par Microsoft doivent reçoivent un e-mail reflétant l’action entreprise et les mesures recommandées qu’ils peuvent entreprendre pour examiner et améliorer leur position de sécurité.

## <a name="recommended-response-and-remediation"></a>Réponse et correction recommandées

Si votre organisation a été affectée par une application désactivée par Microsoft, nous vous recommandons de suivre ces étapes immédiates pour sécuriser votre environnement :

1. Examinez l’activité de l’application désactivée, notamment :
    - Les autorisations déléguées ou autorisations d’application demandées par l’application.
    - Les journaux d’audit Azure AD pour l’activité de l’application et de l’activité de connexion pour les utilisateurs autorisés à utiliser l’application.
1. Passez en revue et mettez en œuvre les [conseils sur la défense contre les octrois de consentement illicites](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) dans les produits cloud Microsoft, y compris l’audit des autorisations et le consentement pour l’application désactivée ou toute autre application suspecte détectée lors de la révision.
1. Implémentez les meilleures pratiques pour renforcer la protection contre le hameçonnage de consentement, décrites ci-dessous.


## <a name="best-practices-for-hardening-against-consent-phishing-attacks"></a>Meilleures pratiques pour renforcer la protection contre les attaques par hameçonnage de consentement

Chez Microsoft, nous souhaitons placer les administrateurs dans le contrôle en fournissant les Insights et les fonctionnalités appropriées pour contrôler la façon dont les applications sont autorisées et utilisées dans les organisations. Bien que les attaquants ne se reposent jamais, il existe des étapes que les organisations peuvent prendre pour améliorer leur sécurité. Certaines bonnes pratiques à suivre sont les suivantes :

* Apprenez à votre organisation le fonctionnement de l’infrastructure de nos autorisations et consentements
    - Comprenez les données et les autorisations demandées par une application et comprenez le fonctionnement des  [autorisations et du consentement](../develop/v2-permissions-and-consent.md) au sein de notre plateforme.
    - Veillez à ce que les administrateurs sachent comment  [gérer et évaluer les demandes de consentement](./manage-consent-requests.md).
    - Effectuer l’[audit des applications et des autorisations accordées](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions)  au sein de votre organisation pour vous assurer que les applications utilisées n’accèdent qu’aux données dont elles ont besoin et qu’elles adhèrent aux principes de privilège moindre.
* Savoir comment identifier et bloquer les stratégies d’hameçonnage de consentement courantes
    - Vérifiez les erreurs orthographiques et grammaticales. Si un e-mail ou l’écran de consentement de l’application contient des erreurs orthographiques et grammaticales, il est probable qu’il s’agisse d’une application suspecte. Dans ce cas, vous pouvez le rapportez directement sur l’[invite de consentement](../develop/application-consent-experience.md#building-blocks-of-the-consent-prompt) avec le lien « *Le rapporter ici* » et Microsoft examinera s’il s’agit d’une application malveillante et la désactivera le cas échéant.
    - Ne vous fiez pas aux noms d’application et aux URL de domaine comme source d’authenticité. Les attaquants aiment usurper les noms d’application et de domaine qui semblent provenir d’un service ou d’entreprises légitimes, pour que vous donniez votre consentement à une application malveillante. Au lieu de cela, validez la source de l’URL de domaine et utilisez des applications à partir d'[éditeurs vérifiés](../develop/publisher-verification-overview.md) lorsque cela est possible.
    - Bloquez les [e-mails de hameçonnage de consentement à l’aide de Microsoft Defender pour Office 365](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies#impersonation-settings-in-anti-phishing-policies-in-microsoft-defender-for-office-365) en protégeant contre les campagnes de hameçonnage où un attaquant usurpe l’identité d’un utilisateur connu de votre organisation.
    - Configurez les stratégies Microsoft Defender for Cloud Apps, telles que les [stratégies d’activité](/cloud-app-security/user-activity-policies), la [détection d’anomalies](/cloud-app-security/anomaly-detection-policy)et les [stratégies d’application OAuth](/cloud-app-security/app-permission-policy)  pour faciliter la gestion et prendre des mesures sur l’activité anormale des applications dans votre organisation.
    - Examinez et recherchez les attaques par hameçonnage de consentement en suivant les conseils relatifs à la [recherche avancée avec Microsoft 365 Defender](/microsoft-365/security/defender/advanced-hunting-overview).
* Autoriser l’accès aux applications de confiance et protégez-vous contre celles auxquelles vous ne faites pas confiance
    - Utilisez des applications qui ont été vérifiées par l’éditeur. La [vérification de l’éditeur](../develop/publisher-verification-overview.md) permet aux administrateurs et aux utilisateurs finaux de s'assurer de l'authenticité des développeurs d'applications à l’aide d’un processus de vérification pris en charge par Microsoft.
    - [Configurez des paramètres de consentement de l'utilisateur](./configure-user-consent.md?tabs=azure-portal) en permettant aux utilisateurs de donner leur consentement uniquement à des applications spécifiques fiables, telles que les applications développées par votre organisation ou par des éditeurs vérifiés.
    - Créez des stratégies de [gouvernance des applications](/microsoft-365/compliance/app-governance-manage-app-governance) proactives pour surveiller le comportement des applications tierces sur la plateforme Microsoft 365 pour résoudre les comportements d’application suspects courants.

## <a name="next-steps"></a>Étapes suivantes

* [Examen d’octroi de consentement d’application](/security/compass/incident-response-playbook-app-consent)
* [Gestion de l’accès aux applications](./what-is-access-management.md)
* [Limiter les opérations de consentement de l’utilisateur dans Azure AD](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations)
