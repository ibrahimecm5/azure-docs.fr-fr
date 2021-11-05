---
title: Recommandations de sécurité de Microsoft Defender pour le cloud pour MFA
description: Découvrez comment appliquer l’authentification multifacteur pour vos abonnements Azure avec Microsoft Defender pour le cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ba741d97e3a9537b64957ce419b06208fd2a528
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097099"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Gérer la mise en œuvre de l’authentification multifacteur (MFA) sur vos abonnements

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Si vous utilisez uniquement des mots de passe pour authentifier vos utilisateurs, vous n’êtes pas entièrement protégé contre les attaques. Les utilisateurs utilisent souvent des mots de passe faibles ou réutilisent les mêmes mots de passe pour plusieurs services. Lorsque [MFA](https://www.microsoft.com/security/business/identity/mfa) est activée, vos comptes sont plus sûrs et les utilisateurs peuvent toujours s’authentifier auprès de presque n’importe quelle application avec l’authentification unique (SSO).

Il existe plusieurs façons d’activer la MFA pour vos utilisateurs Azure Active Directory (AD) en fonction des licences que votre organisation possède. Cette page fournit les informations détaillées de chacune d’elles dans le contexte de Microsoft Defender pour le cloud.


## <a name="mfa-and-microsoft-defender-for-cloud"></a>MFA et Microsoft Defender pour le cloud 

Defender pour le cloud donne une valeur élevée à MFA. Le contrôle de sécurité qui contribue le plus à votre niveau de sécurité est **Activer MFA**. 

Les recommandations du contrôle Activer MFA s’assurent que vous respectez les pratiques recommandées pour les utilisateurs de vos abonnements :

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement
- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement

Il existe trois façons d’activer MFA et de respecter les deux recommandations de Defender pour le cloud : valeurs de sécurité par défaut, attribution par utilisateur, stratégie d’accès conditionnel (CA). Chacune de ces options est expliquée ci-dessous.

### <a name="free-option---security-defaults"></a>Option Gratuite - Valeurs de sécurité par défaut
Si vous utilisez l’édition gratuite d’Azure AD, utilisez les [valeurs de sécurité par défaut](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) pour activer l’authentification multifacteur sur votre locataire.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>MFA pour les clients Microsoft 365 Business, E3 ou E5
Les clients qui possèdent Microsoft 365 peuvent utiliser **l’attribution par utilisateur**. Dans ce scénario, la MFA Azure AD est activée ou désactivée pour tous les utilisateurs, pour tous les événements de connexion. Il n’est pas possible d’activer l’authentification multifacteur pour un sous-ensemble d’utilisateurs ou dans certains scénarios, et la gestion s’effectue via le portail Office 365.

### <a name="mfa-for-azure-ad-premium-customers"></a>MFA pour les clients Azure AD Premium
Pour une meilleure expérience utilisateur, effectuez une mise à niveau vers Azure AD Premium P1 ou P2 pour bénéficier des options de la **stratégie d’accès conditionnel (CA)** . Pour configurer une stratégie d’accès conditionnel, vous devez disposer [d’autorisations sur le locataire Azure Active Directory (AD)](../active-directory/roles/permissions-reference.md).

Votre stratégie d’accès conditionnel :
- doit mettre en œuvre l’authentification multifacteur
- doit inclure l’ID de l’application Gestion Microsoft Azure (797f4846-ba00-4fd7-ba43-dac1f8f63013) ou toutes les applications
- ne doit pas exclure l’ID de l’application Gestion Microsoft Azure

Les clients **Azure AD Premium P1** peuvent utiliser l’accès conditionnel Azure AD pour inviter les utilisateurs à exécuter l’authentification multifacteur dans le cadre de certains scénarios ou événements en fonction des besoins de votre entreprise. Autres licences offrant cette fonctionnalité : Enterprise Mobility + Security E3, Microsoft 365 F1 et Microsoft 365 E3.

**Azure AD Premium P2** offre les fonctionnalités de sécurité les plus robustes et une expérience utilisateur améliorée. Cette licence ajoute un [accès conditionnel basé sur les risques](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) aux fonctionnalités d’Azure AD Premium P1. L’accès conditionnel basé sur les risques s’adapte aux modèles de vos utilisateurs et minimise les invites pour l’authentification multifacteur. Autres licences qui incluent cette fonctionnalité : Enterprise Mobility + Security E5 ou Microsoft 365 E5.

En savoir plus dans la [documentation sur l’accès conditionnel Azure](../active-directory/conditional-access/overview.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identifier les comptes sans authentification multifacteur (MFA) activée

Vous pouvez afficher la liste des comptes d’utilisateur sans la MFA activée à partir de la page des détails des recommandations Defender pour le cloud ou à l’aide d’Azure Resource Graph.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Afficher les comptes sans la MFA activée dans le portail Azure
À partir de la page des détails des recommandations, sélectionnez un abonnement dans la liste **Ressources non saines** ou sélectionnez **Agir** pour afficher la liste.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Afficher les comptes sans la MFA activée à l’aide d’Azure Resource Graph
Pour connaître les comptes pour lesquels la MFA n’est pas activée, utilisez la requête Azure Resource Graph suivante. La requête retourne toutes les ressources défectueuses (comptes) de la recommandation « la MFA doit être activée sur les comptes avec des autorisations de propriétaire sur votre abonnement ». 

1. Ouvrez l’**Explorateur Azure Resource Graph**.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Lancement de la page de recommandations de l’Explorateur Azure Resource Graph**" :::

1. Entrez la requête suivante et sélectionnez **Exécuter la requête**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. La propriété `additionalData` révèle la liste des ID d’objet de compte pour les comptes auxquels la MFA n’est pas appliquée. 

    > [!NOTE]
    > Les comptes sont représentés par un ID d’objet plutôt qu’un nom de compte pour garantir la confidentialité des titulaires de compte.

> [!TIP]
> Vous pouvez également utiliser la méthode [Assessments - Get](/rest/api/securitycenter/assessments/get) de l’API REST Defender pour le cloud.


## <a name="faq---mfa-in-defender-for-cloud"></a>FAQ - MFA dans Defender pour le cloud

- [Nous utilisons déjà la stratégie de l’autorité de certification pour appliquer MFA. Pourquoi recevons-nous quand même les recommandations Defender pour le cloud ?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations)
- [Nous utilisons un outil MFA tiers pour appliquer MFA. Pourquoi recevons-nous quand même les recommandations Defender pour le cloud ?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations)
- [Pourquoi Defender pour le cloud montre les comptes d’utilisateur sans autorisations sur l’abonnement comme « nécessitant MFA » ?](#why-does-defender-for-cloud-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Nous mettons en œuvre la MFA avec PIM. Pourquoi les comptes PIM sont-ils affichés comme non conformes ?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Puis-je exempter ou ignorer certains des comptes ?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [La protection des identités et des accès de Defender pour le cloud présente-t-elle des limitations ?](#are-there-any-limitations-to-defender-for-clouds-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations"></a>Nous utilisons déjà la stratégie d’accès conditionnel pour mettre en œuvre la MFA. Pourquoi recevons-nous quand même les recommandations Defender pour le cloud ?
Pour déterminer la raison pour laquelle les recommandations sont toujours générées, vérifiez les options de configuration suivantes dans votre stratégie d’accès conditionnel MFA :

- Vous avez inclus les comptes dans la section **Utilisateurs** de votre stratégie d’accès conditionnel MFA (ou l’un des groupes dans la section **Groupes**).
- L’ID de l’application Gestion Azure (797f4846-ba00-4fd7-ba43-dac1f8f63013) ou toutes les applications, est/sont inclus(es) dans la section **Applications** de votre stratégie d’accès conditionnel MFA
- L’ID de l’application Gestion Azure n’est pas exclu dans la section **Applications** de votre stratégie d’accès conditionnel MFA

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations"></a>Nous utilisons un outil MFA tiers pour mettre en œuvre la MFA. Pourquoi recevons-nous quand même les recommandations Defender pour le cloud ?
Les recommandations MFA de Defender pour le cloud ne prennent pas en charge les outils MFA tiers (par exemple, DUO).

Si les recommandations ne sont pas pertinentes pour votre organisation, envisagez de les marquer comme étant « atténuées », comme décrit dans [Exemption de ressources et de recommandations dans votre niveau de sécurité](exempt-resource.md). Vous pouvez également [désactiver une recommandation](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-defender-for-cloud-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Pourquoi Defender pour le cloud montre les comptes d’utilisateur sans autorisations sur l’abonnement comme « nécessitant MFA » ?
Les recommandations MFA de Defender pour le cloud font référence aux rôles [Azure RBAC](../role-based-access-control/role-definitions-list.md) et au rôle [Administrateurs d’abonnements classiques Azure](../role-based-access-control/classic-administrators.md). Vérifiez qu’aucun des comptes n’a ces rôles.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Nous mettons en œuvre la MFA avec PIM. Pourquoi les comptes PIM sont-ils affichés comme non conformes ?
Les recommandations MFA de Defender pour le cloud ne prennent actuellement pas en charge les comptes PIM. Vous pouvez ajouter ces comptes à une stratégie d’accès conditionnel dans la section Utilisateurs/Groupe.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Puis-je exempter ou ignorer certains des comptes ?
La possibilité d’exempter certains comptes qui n’utilisent pas l’authentification MFA n’est actuellement pas prise en charge.  

### <a name="are-there-any-limitations-to-defender-for-clouds-identity-and-access-protections"></a>La protection des identités et des accès de Defender pour le cloud présente-t-elle des limitations ?
La protection des identités et des accès de Defender pour le cloud présente quelques limitations :

- Les recommandations d’identité ne sont pas disponibles pour les abonnements avec plus de 600 comptes. Dans ce cas, ces recommandations sont répertoriées sous « évaluations non disponibles ».
- Les recommandations d’identité ne sont pas disponibles pour les agents d’administration du partenaire fournisseur de solutions Cloud (CSP).
- Les recommandations d’identité n’identifient pas les comptes qui sont gérés à l’aide d’un système Privileged Identity Management (PIM). Si vous utilisez un outil PIM, il se peut que des résultats inexacts soient visibles dans le contrôle **Gérer l’accès et les autorisations**.


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez l’article suivant :

- [Protection de votre réseau dans Microsoft Defender pour le cloud](protect-network-resources.md)
