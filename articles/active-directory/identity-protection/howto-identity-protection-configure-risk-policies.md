---
title: Stratégies de risque – Azure Active Directory Identity Protection
description: Activer et configurer les stratégies en matière de risque dans Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e191603d9508ab921c28e60ae13096e7283dc332
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401958"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Procédure : Configurer et activer des stratégies de risque

Comme nous l’avons appris dans l’article précédent, [Stratégies de protection des identités](concept-identity-protection-policies.md), nous avons deux stratégies de risque que nous pouvons activer dans notre annuaire. 

- Stratégie en matière de risque à la connexion
- Stratégie de risque d’utilisateur

![Page de vue d’ensemble de la sécurité permettant d’activer les stratégies de risque utilisateur et de connexion](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Ensemble, les deux stratégies automatisent la réponse aux détections de risques dans votre environnement et permettent aux utilisateurs de résoudre eux-mêmes des problèmes quand des risques sont détectés. 

## <a name="choosing-acceptable-risk-levels"></a>Choix des niveaux de risque acceptables

Les organisations doivent déterminer le niveau de risque consenti lorsqu’elles mettent dans la balance l’expérience utilisateur et la posture de sécurité. 

La recommandation de Microsoft est de définir le seuil de stratégie de risque de l’utilisateur sur **Élevé** et la stratégie de risque de connexion sur **Moyen et supérieur**. Elle consiste également à autoriser les options d’auto-correction. Le choix de bloquer l’accès au lieu d’autoriser les options d’auto-correction, telles que la modification de mot de passe et l’authentification multifacteur, aura un impact sur vos utilisateurs et vos administrateurs. Réfléchissez mûrement à ce choix lorsque vous configurez vos stratégies.

La sélection d’un niveau de risque **Élevé** réduit la fréquence de déclenchement d’une stratégie et minimise l’impact sur les utilisateurs. Cependant, cela a pour effet d’exclure les détections de risque **Faible** et **Moyen**. Par conséquent, il se peut qu’un cybercriminel soit en mesure d’exploiter une identité compromise. La sélection d’un seuil **Minimum** entraîne davantage d’interruptions utilisateur.

Les [emplacements réseau](../conditional-access/location-condition.md) approuvés qui ont été configurés sont utilisés par Identity Protection dans certaines détections de risques afin de réduire les faux positifs.

### <a name="risk-remediation"></a>Mesures de correction des risques

Les organisations peuvent choisir de bloquer l’accès en cas de détection d’un risque. Le blocage empêche parfois les utilisateurs légitimes de faire ce dont ils ont besoin. Une meilleure solution consiste à autoriser l’auto-correction à l’aide de l’authentification multifacteur (MFA) Azure AD et de la réinitialisation de mot de passe en libre-service (SSPR).

- En cas de déclenchement d’une stratégie de risque utilisateur : 
   - Les administrateurs peuvent exiger une réinitialisation de mot de passe sécurisé. Pour ce faire, Azure AD MFA doit avoir été appliqué avant que l’utilisateur ne crée un mot de passe avec SSPR, ce qui réinitialise le risque de l’utilisateur. 
- En cas de déclenchement d’une stratégie de risque de connexion : 
   - Azure AD MFA peut être déclenché, ce qui permet à l’utilisateur de prouver qu’il s’agit bien de lui à l’aide de l’une de ses méthodes d’authentification inscrites, ce qui réinitialise le risque de connexion. 

> [!WARNING]
> Les utilisateurs doivent s’inscrire à Azure AD MFA et SSPR avant de faire face à une situation nécessitant une correction. Les utilisateurs qui ne sont pas inscrits sont bloqués et ont besoin de l’intervention d’un administrateur.
> 
> La modification du mot de passe (je connais mon mot de passe et je souhaite le remplacer par un nouveau) en dehors du processus risqué de correction de stratégie utilisateur ne répond pas aux exigences de réinitialisation de mot de passe sécurisé.

## <a name="exclusions"></a>Exclusions

Les stratégies permettent d’exclure des utilisateurs, tels que vos [comptes d’administrateur d’accès en urgence ou d’interruption](../roles/security-emergency-access.md). Les organisations peuvent avoir besoin d’exclure d’autres comptes de stratégies spécifiques en fonction de la façon dont les comptes sont utilisés. Les exclusions doivent être examinées régulièrement pour déterminer si elles sont toujours applicables.

## <a name="enable-policies"></a>Activer les stratégies

Il existe deux emplacements où ces stratégies peuvent être configurées, l’Accès conditionnel et la Protection de l’identité. La configuration à l’aide de stratégies d’accès conditionnel est la méthode préférée, qui fournit davantage de contexte, notamment : 

   - des données de diagnostic améliorées ;
   - l’intégration du mode rapport uniquement ;
   - la prise en charge de l'API Graph.
   - Utiliser d’autres attributs d’accès conditionnel dans la stratégie

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

Avant d’activer les stratégies de correction, les organisations peuvent vouloir [examiner](howto-identity-protection-investigate-risk.md) et [corriger](howto-identity-protection-remediate-unblock.md) tout risque actif.

### <a name="user-risk-with-conditional-access"></a>Risque utilisateur avec accès conditionnel

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**.
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs**.
   1. Sous **Exclure**, sélectionnez **Utilisateurs et groupes**, puis choisissez les comptes d’accès d’urgence ou de secours de votre organisation. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications ou actions cloud** > **Inclure**, sélectionnez **Toutes les applications cloud**.
1. Dans **Conditions** > **des risques utilisateur**, définissez **Configurer** sur **Oui**. 
   1. Sous **Configurer les niveaux de risque utilisateur nécessaires à l’application de la stratégie**, sélectionnez **Élevé**.
   1. Sélectionnez **Terminé**.
1. Sous **Contrôles d’accès** > **Octroyer**.
   1. Sélectionnez **Accorder l’accès**, **Nécessite une modification du mot de passe**.
   1. Sélectionnez **Sélectionner**.
1. Confirmez vos paramètres et définissez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer votre stratégie.

### <a name="sign-in-risk-with-conditional-access"></a>Risque de connexion avec accès conditionnel

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**.
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs**.
   1. Sous **Exclure**, sélectionnez **Utilisateurs et groupes**, puis choisissez les comptes d’accès d’urgence ou de secours de votre organisation. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications ou actions cloud** > **Inclure**, sélectionnez **Toutes les applications cloud**.
1. Dans **Conditions** > **Risque de connexion**, définissez **Configurer** sur **Oui**. Sous **Sélectionner le niveau de risque de connexion auquel cette stratégie s’applique** 
   1. Sélectionnez **Haut** et **Moyen**.
   1. Sélectionnez **Terminé**.
1. Sous **Contrôles d’accès** > **Octroyer**.
   1. Sélectionnez **Accorder l’accès**, **Exiger l’authentification multifacteur**.
   1. Sélectionnez **Sélectionner**.
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer votre stratégie.

## <a name="next-steps"></a>Étapes suivantes

- [Activer la stratégie d'inscription Azure AD Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [Qu'est-ce qu'un risque ?](concept-identity-protection-risks.md)

- [Examiner les détections de risques](howto-identity-protection-investigate-risk.md)

- [Simuler des détections de risques](howto-identity-protection-simulate-risk.md)
