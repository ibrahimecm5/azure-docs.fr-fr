---
title: Découvrir l’état actuel de la collaboration externe avec Azure Active Directory
description: En savoir plus sur les méthodes permettant de découvrir l’état actuel de votre collaboration.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f3874b4be6cd9f84a98a8e218f5faa983499ab5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300924"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>Découvrir l’état actuel de la collaboration externe avec votre organisation

Avant de découvrir l’état actuel de votre collaboration externe, vous devez [déterminer la posture de sécurité souhaitée](1-secure-access-posture.md). Vous allez considérer les besoins de votre organisation en matière de contrôle centralisé et délégué, ainsi que toutes les cibles de gouvernance, de réglementation et de conformité pertinentes.

Les personnes de votre organisation collaborent probablement déjà avec des utilisateurs d’autres organisations. La collaboration peut s’effectuer via des fonctionnalités dans des applications de productivité comme Microsoft 365, par e-mails ou en partageant des ressources avec des utilisateurs externes. Les piliers de votre plan de gouvernance s’affichent à mesure que vous découvrez :

* Les utilisateurs qui lancent la collaboration externe.
* Les utilisateurs externes et les organisations avec lesquelles vous collaborez.
* L’accès accordé aux utilisateurs externes.

## <a name="users-initiating-external-collaboration"></a>Les utilisateurs lançant la collaboration externe

Les utilisateurs qui lancent une collaboration externe peuvent mieux comprendre les applications les plus pertinentes pour la collaboration externe et quand cet accès doit se terminer. La compréhension de ces utilisateurs peut vous aider à déterminer qui doit recevoir une permission déléguée pour inviter des utilisateurs externes, créer des packages d’accès et effectuer des révisions d’accès.

Pour rechercher les utilisateurs qui collaborent actuellement, consultez le [journal d’audit Microsoft 365 pour partager et accéder aux activités de requêtes](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities). Vous pouvez également consulter le [journal d’audit Azure AD pour plus d’informations sur les personnes qui ont invité des utilisateurs B2B](../external-identities/auditing-and-reporting.md) à votre répertoire.

## <a name="find-current-collaboration-partners"></a>Rechercher des partenaires de collaboration actuels

Les utilisateurs externes peuvent être des [utilisateurs B2B Azure AD](../external-identities/what-is-b2b.md) (préférable) avec des informations d’identification gérées par un partenaire ou des utilisateurs externes avec des informations d’identification configurées localement. Ces utilisateurs sont généralement (mais pas toujours) marqués avec un UserType Invité. Vous pouvez énumérer les utilisateurs invités à l’aide de [l’API Graph Microsoft](/graph/api/user-list?tabs=http), [PowerShell](/graph/api/user-list?tabs=http) ou le [Portail Azure](../enterprise-users/users-bulk-download.md).

### <a name="use-email-domains-and-companyname-property"></a>Utiliser les domaines de courrier et la propriété companyName

Les organisations externes peuvent être déterminées par les noms de domaine des adresses e-mail des utilisateurs externes. Si des fournisseurs d’identité de contrôle serveur consommateur tels que Google sont pris en charge, cela peut ne pas être possible. Dans ce cas, nous vous recommandons d’écrire l’attribut companyName pour identifier clairement l’organisation externe de l’utilisateur.

### <a name="use-allow-or-deny-lists"></a>Utiliser des listes d’autorisation ou de refus

Déterminez si votre organisation souhaite autoriser la collaboration avec uniquement des organisations spécifiques. Déterminez également si votre organisation souhaite bloquer la collaboration avec des organisations spécifiques. Au niveau du locataire, une [liste d’autorisation ou de refus](../external-identities/allow-deny-list.md) permet de contrôler l’ensemble des acceptations et invitations B2B quelle que soit la source (comme Microsoft Teams, Microsoft SharePoint ou le portail Azure).

Si vous utilisez la gestion des droits d’utilisation, vous pouvez également étendre les packages d’accès à un sous-ensemble de vos partenaires en sélectionnant le paramètre Organisations connectées spécifiques comme indiqué ci-dessous.

![Capture d’écran de la mise en liste d’autorisation ou du refus lors de la création d’un nouveau package d’accès.](media/secure-external-access/2-new-access-package.png)

## <a name="find-access-being-granted-to-external-users"></a>Rechercher l’accès accordé aux utilisateurs externes

Une fois que vous avez un inventaire des utilisateurs et des organisations externes, vous pouvez déterminer l’accès accordé à ces utilisateurs à l’aide de l’API Graph Microsoft pour déterminer [l’appartenance au groupe](/graph/api/resources/groups-overview) Azure AD ou [l’attribution directe des applications](/graph/api/resources/approleassignment) dans Azure AD.

### <a name="enumerate-application-specific-permissions"></a>Énumérer les autorisations spécifiques à l’application

Vous pouvez également être en mesure d’effectuer une énumération des autorisations spécifiques à l’application. Par exemple, vous pouvez générer par programmation un rapport d’autorisation pour SharePoint Online en utilisant [ce script](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64).

Examinez en particulier l’accès à toutes vos applications stratégiques et critiques afin de vous tenir informé de tout accès externe.

### <a name="detect-ad-hoc-sharing"></a>Détecter le partage ad hoc

Si vos plans d’e-mail et de réseau l’activent, vous pouvez examiner le contenu partagé par e-mail ou par le biais d’applications SaaS (software as a service) non autorisées. La [protection contre la perte des données Microsoft 365](/microsoft-365/compliance/data-loss-prevention-policies) vous permet d’identifier, d’empêcher et d’analyser le partage accidentel d’informations sensibles dans votre infrastructure Microsoft 365. [Microsoft Defender for Cloud Apps](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) peut vous aider à identifier l’utilisation d’applications SaaS non autorisées dans votre environnement.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la sécurisation de l’accès externe aux ressources. Nous vous recommandons d’effectuer les actions dans l’ordre indiqué.

1. [Déterminer votre posture de sécurité pour l’accès externe](1-secure-access-posture.md)

2. [Découvrir votre état actuel](2-secure-access-current-state.md) (Vous êtes ici.)

3. [Créer un plan de gouvernance](3-secure-access-plan.md)

4. [Utiliser des groupes pour la sécurité](4-secure-access-groups.md)

5. [Transition vers Azure AD B2B](5-secure-access-b2b.md)

6. [Sécuriser l’accès avec la gestion des droits d’utilisation](6-secure-access-entitlement-managment.md)

7. [Sécuriser l’accès avec des stratégies d’Accès conditionnel](7-secure-access-conditional-access.md)

8. [Sécuriser l’accès avec des étiquettes de confidentialité](8-secure-access-sensitivity-labels.md)

9. [Sécuriser l’accès à Microsoft Teams, OneDrive et SharePoint](9-secure-access-teams-sharepoint.md)
