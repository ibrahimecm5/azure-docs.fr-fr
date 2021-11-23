---
title: Création d'une stratégie d’accès conditionnel - Azure Active Directory
description: Quelles sont les options disponibles pour créer une stratégie d’accès conditionnel et que signifient-elles ?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 997b3ec9b784b8b52b826b526102a97bce7d3286
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301052"
---
# <a name="building-a-conditional-access-policy"></a>Créer une stratégie d’accès conditionnel

Comme expliqué dans l’article [Qu’est-ce que l’accès conditionnel ?](overview.md), une stratégie d'accès conditionnel est une instruction if-then d'**affectations** et de **contrôles d'accès**. Une stratégie d’accès conditionnel regroupe des signaux pour prendre des décisions et appliquer des stratégies organisationnelles.

Comment une organisation crée-t-elle ces stratégies ? Qu’est-ce qui est requis ? Comment sont-elles appliquées ?

![Accès conditionnel (signaux + décisions + application = stratégies)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

Plusieurs stratégies d’accès conditionnel peuvent s’appliquer à un utilisateur individuel à tout moment. Dans ce cas, toutes les stratégies qui s’appliquent doivent être satisfaites. Par exemple, si une première stratégie demande une authentification multifacteur (MFA) et qu’une autre demande un appareil conforme, vous devez procéder à la MFA et utiliser un appareil compatible. Toutes les attributions sont reliées par l’opérateur logique **AND**. Si vous configurez plusieurs attributions, ces dernières doivent toutes être satisfaites pour qu’une stratégie soit déclenchée.

Si une stratégie « Demander un des contrôles sélectionnés » est sélectionnée, nous vous inviterons dans l’ordre défini dès que les conditions de la stratégie sont satisfaites, et l’accès sera accordé.

Toutes les stratégies sont appliquées en deux phases :

- Phase 1 : Collecter les détails de la session 
   - Collectez les détails de la session, tels que l’emplacement réseau et l’identité de l’appareil, qui seront nécessaires à l’évaluation de la stratégie. 
   - La phase 1 de l’évaluation de la stratégie se produit pour les stratégies activées et les stratégies en [mode rapport seul](concept-conditional-access-report-only.md).
- Phase 2 : Application 
   - Utilisez les détails de la session collectés lors de la phase 1 pour identifier les exigences qui n’ont pas été respectées. 
   - Si une stratégie est configurée pour bloquer l’accès, grâce au contrôle d’octroi et de blocage, l’application s’arrête ici et l’utilisateur est bloqué. 
   - L’utilisateur sera invité à remplir d’autres exigences de contrôle d’octroi qui n’ont pas été satisfaites durant la phase 1 dans l’ordre suivant, jusqu’à ce que la stratégie soit satisfaite :  
      - Authentification multifacteur 
      - Application cliente approuvée/stratégie de protection d’application 
      - Appareil géré (jonction d’Azure AD conforme ou hybride) 
      - Conditions d’utilisation 
      - Contrôles personnalisés  
   - Une fois que tous les contrôles d’octroi ont été satisfaits, appliquez les contrôles de session (appliqués par l’application, Microsoft Defender for Cloud Apps et durée de vie du jeton) 
   - La phase 2 de l’évaluation de la stratégie se produit pour toutes les stratégies activées. 

## <a name="assignments"></a>Attributions

La partie Affectations contrôle les personnes, les éléments et l’emplacement de la stratégie d’accès conditionnel.

### <a name="users-and-groups"></a>Utilisateurs et groupes

Les [utilisateurs et groupes](concept-conditional-access-users-groups.md) affectent les personnes que la stratégie inclura ou exclura. Cette affectation peut inclure tous les utilisateurs, des groupes d’utilisateurs spécifiques, des rôles d’annuaire ou des utilisateurs invités externes. 

### <a name="cloud-apps-or-actions"></a>Applications ou actions cloud

Les [actions ou applications cloud](concept-conditional-access-cloud-apps.md) peuvent inclure ou exclure des applications cloud, des actions utilisateur ou des contexte d’authentification auxquels s’appliquera la stratégie.

### <a name="conditions"></a>Conditions

Une stratégie peut comporter plusieurs [conditions](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Risque à la connexion

Pour les organisations dotées d'[Azure AD Identity Protection](../identity-protection/overview-identity-protection.md), les détections de risques générées peuvent influencer vos stratégies d’accès conditionnel.

#### <a name="device-platforms"></a>Plateformes d’appareils

Les organisations utilisant plusieurs plateformes de système d’exploitation d'appareil peuvent souhaiter appliquer des stratégies spécifiques sur différentes plateformes. 

Les informations utilisées pour calculer la plateforme d'appareil proviennent de sources non vérifiées, telles que des chaînes d'agent utilisateur qui peuvent être modifiées.

#### <a name="locations"></a>Emplacements

Les données d’emplacement sont fournies par les données de géolocalisation IP. Les administrateurs peuvent choisir de définir des emplacements et d’en marquer certains comme approuvés, par exemple les emplacements réseau de leur organisation.

#### <a name="client-apps"></a>Applications clientes

Par défaut, toutes les stratégies d’accès conditionnel nouvellement créées s’appliquent à tous les types d’applications clientes, même si la condition des applications clientes n’est pas configurée.

Le comportement de la condition des applications clientes a été mis à jour en août 2020. Si vous disposez de stratégies d’accès conditionnel, elles restent inchangées. Toutefois, si vous sélectionnez une stratégie, le bouton bascule de configuration a été supprimé et les applications clientes auxquelles la stratégie s’applique sont sélectionnées.

#### <a name="device-state"></a>État de l’appareil

Ce contrôle est utilisé pour exclure des appareils hybrides Azure AD joints ou marqués comme conformes dans Intune. Cette exclusion permet de bloquer les appareils non gérés. 

#### <a name="filters-for-devices-preview"></a>Filtres pour appareils (préversion)

Ce contrôle permet de cibler des appareils spécifiques en fonction de leurs attributs dans une stratégie.

## <a name="access-controls"></a>Contrôles d’accès

La partie Contrôles d’accès de la stratégie d’accès conditionnel contrôle la manière dont une stratégie est appliquée.

### <a name="grant"></a>Accorder

Avec [Accorder](concept-conditional-access-grant.md), les administrateurs peuvent appliquer une stratégie pour bloquer ou autoriser l’accès.

#### <a name="block-access"></a>Bloquer l’accès

Le blocage permet de bloquer l'accès d'affectations spécifiées. Le contrôle de blocage est un puissant outil et doit être utilisé moyennant les connaissances appropriées.

#### <a name="grant-access"></a>Accorder l'accès

Le contrôle d'octroi peut déclencher l’application d’un ou de plusieurs contrôles. 

- Exiger une authentification multifacteur (Azure AD Multi-Factor Authentication)
- Exiger que l'appareil soit marqué comme conforme (Intune)
- Exiger un appareil joint Azure AD Hybride
- Demander une application cliente approuvée
- Exiger une stratégie de protection des applications
- Nécessite une modification du mot de passe
- Exiger des conditions d’utilisation

Les administrateurs peuvent choisir d’exiger un des contrôles précédents ou tous les contrôles sélectionnés à l’aide des options suivantes. La valeur par défaut pour plusieurs contrôles consiste à tous les exiger.

- Exiger tous les contrôles sélectionnés
- Exiger un des contrôles sélectionnés

### <a name="session"></a>session

Les [contrôles de session](concept-conditional-access-session.md) peuvent limiter l’expérience 

- Utiliser les restrictions appliquées par l’application
   - Fonctionne actuellement avec Exchange Online et SharePoint Online uniquement.
      - Transmet des informations sur l’appareil pour permettre le contrôle de l’expérience qui octroie un accès total ou limité.
- Utiliser le contrôle d'application par accès conditionnel
   - Utilise les signaux de Microsoft Defender for Cloud Apps pour effectuer des opérations telles que : 
      - Bloquer le téléchargement, couper, copier et imprimer des documents sensibles.
      - Surveiller un comportement de session à risque.
      - Exiger l’étiquetage des fichiers sensibles.
- Fréquence de connexion
   - Possibilité de modifier la fréquence de connexion par défaut pour l’authentification moderne.
- Session de navigateur persistante
   - Permet aux utilisateurs de rester connectés après la fermeture et la réouverture de la fenêtre du navigateur.

## <a name="simple-policies"></a>Stratégies simples

Une stratégie d’accès conditionnel doit contenir au moins les éléments suivants à appliquer :

- **Nom** de la stratégie.
- **Attributions**
   - **Utilisateurs et/ou groupes** auxquels appliquer la stratégie.
   - **Applications ou actions cloud** auxquelles appliquer la stratégie.
- **Contrôles d’accès**
   - **Octroyer** ou **Bloquer** des contrôles

![Stratégie d’accès conditionnel vide](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

Dans l’article [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md), nous présentons quelques stratégies qui nous semblent utiles pour la plupart des organisations.

## <a name="next-steps"></a>Étapes suivantes

[Créer une stratégie d’accès conditionnel](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json#create-a-conditional-access-policy)

[Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)

[Planifier un déploiement d'Azure AD Multi-Factor Authentication basé sur le cloud](../authentication/howto-mfa-getstarted.md)

[Gestion de la conformité des appareils avec Intune](/intune/device-compliance-get-started)

[Microsoft Defender for Cloud Apps et accès conditionnel](/cloud-app-security/proxy-intro-aad)
