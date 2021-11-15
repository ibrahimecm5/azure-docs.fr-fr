---
title: Valeurs de résilience par défaut pour l’accès conditionnel Azure AD
description: Valeurs de résilience par défaut et service d’authentification de sauvegarde Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e2bebe9769d38502a37134326228d41979cad5a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432678"
---
# <a name="conditional-access-resilience-defaults-preview"></a>Accès conditionnel : Valeurs de résilience par défaut (préversion)

En cas de panne du service d’authentification principal, le service d’authentification de sauvegarde Azure Active Directory (Azure AD) peut émettre automatiquement des jetons d’accès aux applications pour les sessions existantes. Cette fonctionnalité peut augmenter considérablement la résilience d’Azure AD, car les réauthentifications pour les sessions existantes représentent plus de 90 % des authentifications sur Azure AD. Le service d’authentification de sauvegarde ne prend pas en charge les nouvelles sessions ou les authentifications par les utilisateurs invités.

Pour les authentifications protégées par l’accès conditionnel, les stratégies sont réévaluées avant l’émission des jetons d’accès pour déterminer les éléments suivants :

1.  Quelles stratégies d’accès conditionnel s’appliquent ?
1.  Pour les stratégies qui s’appliquent, les contrôles requis ont-ils été effectués ?

Pendant une panne, toutes les conditions ne peuvent pas être évaluées en temps réel par le service d’authentification de sauvegarde pour déterminer si une stratégie d’accès conditionnel doit s’appliquer. Les valeurs de résilience par défaut de l’accès conditionnel sont un nouveau contrôle de session qui permet aux administrateurs de décider s’il faut bloquer les authentifications pendant une panne lorsqu’une condition de la stratégie ne peut pas être évaluée en temps réel ou s’il faut permettre l’évaluation des stratégies à l’aide des données collectées au début de la session de l’utilisateur. 

> [!IMPORTANT]
> Les valeurs de résilience par défaut sont automatiquement activées pour toutes les stratégies nouvelles et existantes. Microsoft recommande vivement de les laisser activées pour atténuer l’impact d’une panne. Les administrateurs peuvent désactiver les valeurs de résilience par défaut pour les stratégies d’accès conditionnel individuelles. 

## <a name="how-does-it-work"></a>Comment fonctionne-t-il ?

Pendant une panne, le service d’authentification de secours réémet automatiquement des jetons d’accès pour certaines sessions :

| Description de la session | Accès accordé |
| --- | --- |
| Nouvelle session | Non |
| Session existante : Aucune stratégie d’accès conditionnel n’est configurée | Oui |
| Session existante : Des stratégies d’accès conditionnel configurées et les contrôles requis, comme l’authentification multifacteur, ont été effectués précédemment | Oui |
| Session existante : Des stratégies d’accès conditionnel configurées et les contrôles requis, comme l’authentification multifacteur, n’ont pas été effectués précédemment | Déterminé par les valeurs de résilience par défaut |

Lorsqu’une session existante expire pendant une panne d’Azure AD, la demande d’un nouveau jeton d’accès est acheminée vers le service d’authentification de secours et toutes les stratégies d’accès conditionnel sont réévaluées. S’il n’existe aucune stratégie d’accès conditionnel ou si tous les contrôles requis, tels que l’authentification multifacteur, ont déjà été effectués au début de la session, le service d’authentification de secours émet un nouveau jeton d’accès pour prolonger la session. 

Si les contrôles requis d’une stratégie n’ont pas été effectués précédemment, la stratégie est réévaluée pour déterminer si l’accès doit être accordé ou refusé. Toutefois, toutes les conditions ne peuvent pas être réévaluées en temps réel pendant une panne. Ces conditions sont notamment les suivantes : 

- Appartenance au groupe
- Appartenance au rôle
- Risque à la connexion
- Risque de l’utilisateur
- Localisation du pays (résolution des nouvelles coordonnées IP ou GPS)

## <a name="resilience-defaults-enabled"></a>Valeurs de résilience par défaut activées

Lorsque les valeurs de résilience par défaut sont activées, le service d’authentification de secours peut utiliser les données collectées au début de la session pour déterminer si la stratégie doit s’appliquer en l’absence de données en temps réel. Par défaut, les valeurs de résilience par défaut sont activées pour toutes les stratégies. Ce paramètre peut être désactivé pour des stratégies individuelles lorsque l’évaluation de la stratégie en temps réel est nécessaire pour accéder à des applications sensibles pendant une panne.

**Exemple** : Une stratégie dont les valeurs de résilience par défaut sont activées requiert que tous les administrateurs généraux accèdent au portail Azure pour effectuer l’authentification multifacteur. Avant une panne, si un utilisateur qui n’est pas un administrateur général accède au portail Azure, la stratégie ne s’applique pas et l’utilisateur se voit accorder l’accès sans être invité à utiliser l’authentification multifacteur. Pendant une panne, le service d’authentification de secours réévalue la stratégie pour déterminer si l’utilisateur doit être invité à utiliser l’authentification multifacteur. **Étant donné que le service d’authentification de secours ne peut pas évaluer l’appartenance à un rôle en temps réel, il utilise les données collectées au début de la session de l’utilisateur pour déterminer que la stratégie ne s’applique toujours pas. Par conséquent, l’utilisateur se voit accorder l’accès sans être invité à utiliser l’authentification multifacteur.**

## <a name="resilience-defaults-disabled"></a>Valeurs de résilience par défaut désactivées

Lorsque les valeurs de résilience par défaut sont désactivées, le service d’authentification de secours n’utilise pas les données collectées au début de la session pour évaluer les conditions. Pendant une panne, si une condition de stratégie ne peut pas être évaluée en temps réel, l’accès sera refusé.

**Exemple** : Une stratégie dont les valeurs de résilience par défaut sont désactivées requiert que tous les administrateurs généraux accèdent au portail Azure pour effectuer l’authentification multifacteur. Avant une panne, si un utilisateur qui n’est pas un administrateur général accède au portail Azure, la stratégie ne s’applique pas et l’utilisateur se voit accorder l’accès sans être invité à utiliser l’authentification multifacteur. Pendant une panne, le service d’authentification de secours réévalue la stratégie pour déterminer si l’utilisateur doit être invité à utiliser l’authentification multifacteur. **Étant donné que le service d’authentification de secours ne peut pas évaluer l’appartenance à un rôle en temps réel, il empêche l’utilisateur d’accéder au portail Azure.**

> [!WARNING]
> La désactivation des valeurs de résilience par défaut pour une stratégie qui s’applique à un groupe ou à un rôle réduit la résilience pour tous les utilisateurs de votre locataire. Comme l’appartenance à un groupe ou à un rôle ne peut pas être évaluée en temps réel pendant une panne, même les utilisateurs qui n’appartiennent pas au groupe ou au rôle attribué à la stratégie se verront refuser l’accès à l’application concernée par la stratégie. Pour éviter de réduire la résilience pour tous les utilisateurs qui ne sont pas concernés par la stratégie, envisagez d’appliquer la stratégie à des utilisateurs individuels plutôt qu’à des groupes ou des rôles. 

## <a name="testing-resilience-defaults"></a>Test des valeurs de résilience par défaut

Pour l’instant, il n’est pas possible d’effectuer un test à l’aide du service d’authentification de secours ni de simuler le résultat d’une stratégie dont les valeurs de résilience par défaut sont activées ou désactivées. Azure AD effectuera des exercices mensuels en utilisant le service d’authentification de secours et les journaux de connexion afficheront si le service d’authentification de secours a été utilisé pour émettre le jeton d’accès.

## <a name="configuring-resilience-defaults"></a>Configuration des valeurs de résilience par défaut

Vous pouvez configurer les valeurs de résilience par défaut de l’accès conditionnel à partir du portail Azure, des API MS Graph ou de PowerShell. 

### <a name="azure-portal"></a>Portail Azure

1.  Accédez à **Portail Azure** > **Sécurité** > **Accès conditionnel**.
1.  Créez une nouvelle stratégie ou sélectionnez une stratégie existante.
1.  Ouvrez les paramètres de contrôle de session.
1.  Sélectionnez Désactiver les valeurs de résilience par défaut pour désactiver le paramètre pour cette stratégie. Les connexions dans le cadre de la stratégie seront bloquées pendant une panne d’Azure AD.
1.  Enregistrez les modifications apportées à la stratégie.

### <a name="ms-graph-apis"></a>API MS Graph

Vous pouvez également gérer les valeurs de résilience par défaut pour vos stratégies d’accès conditionnel à l’aide de l’API MS Graph et de [Microsoft Graph Explorer](/graph/graph-explorer/graph-explorer-overview). 

Exemple d’URL de la demande : 

`PATCH https://graph.microsoft.com/beta/identity/conditionalAccess/policies/policyId`

Exemple de corps de la demande : 

```json

{
"sessionControls": {
"disableResilienceDefaults": true
}
}
```

### <a name="powershell"></a>PowerShell

Cette opération de correction peut être déployée à l’aide de Microsoft PowerShell après l’installation du module Microsoft.Graph.Authentication. Pour installer ce module, ouvrez une invite PowerShell avec élévation de privilèges et exécutez :

`Install-Module Microsoft.Graph.Authentication`

Connectez vous à Microsoft Graph, en demandant les étendues requises :

`Connect-MgGraph -Scopes Policy.Read.All,Policy.ReadWrite.ConditionalAccess,Application.Read.All -TenantId <TenantID>`

Authentifiez-vous lorsque vous y êtes invité.

Créez le corps JSON de la demande PATCH :

`$patchBody = '{"sessionControls": {"disableResilienceDefaults": true}}'`

Exécutez l’opération de correction :

`Invoke-MgGraphRequest -Method PATCH -Uri https://graph.microsoft.com/beta/identity/conditionalAccess/policies/<PolicyID> -Body $patchBody`

## <a name="recommendations"></a>Recommandations

Microsoft recommande d’activer les valeurs de résilience par défaut. Bien qu’il n’y ait pas de problèmes de sécurité directs, les clients doivent déterminer s’ils souhaitent permettre au service d’authentification de secours d’évaluer les stratégies d’accès conditionnel pendant une panne en utilisant les données collectées au début de la session, par opposition à en temps réel. 

Il est possible que l’appartenance d’un utilisateur à un rôle ou à un groupe ait changé depuis le début de la session. Grâce à [Évaluation continue de l’accès](concept-continuous-access-evaluation.md), les jetons d’accès sont valides pendant 24 heures, mais sont soumis à des événements de révocation instantanée. Le service d’authentification de secours s’abonne aux mêmes événements de révocation Évaluation continue de l’accès. Si le jeton d’un utilisateur est révoqué dans le cadre d’Évaluation continue de l’accès, l’utilisateur ne peut pas se connecter pendant une panne. Lorsque les valeurs de résilience par défaut sont activées, les sessions existantes qui expirent pendant une panne sont prolongées. Les sessions sont prolongées même si la stratégie a été configurée avec un contrôle de session pour imposer une fréquence de connexion. Par exemple, une stratégie dont les valeurs de résilience par défaut sont activées peut exiger que les utilisateurs se réauthentifient toutes les heures pour accéder à un site SharePoint. Pendant une panne, la session de l’utilisateur serait prolongée même si Azure AD n’est pas disponible pour réauthentifier l’utilisateur. 

## <a name="next-steps"></a>Étapes suivantes

- [Évaluation continue de l’accès](concept-continuous-access-evaluation.md)
