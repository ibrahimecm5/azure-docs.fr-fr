---
title: Qu’est-ce que Privileged Identity Management ? – Azure | Microsoft Docs
description: Fournit une vue d’ensemble d’Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim,azuread-video-2020,contperf-fy21q3-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd9a0e11eb58a99623acdf9c8c61656bc32dde6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285983"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Qu’est-ce qu’Azure AD Privileged Identity Management ?

 Privileged Identity Management (PIM) est un service dans Azure Active Directory (Azure AD) qui vous permet de gérer, de contrôler et de superviser l’accès aux ressources importantes de votre organisation. Ces ressources incluent des ressources dans Azure AD et Azure ainsi que d’autres services Microsoft Online Services tels que Microsoft 365 ou Microsoft Intune. La vidéo suivante présente les fonctionnalités et concepts importants d’Azure AD Privileged Identity Management (PIM).
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>Motifs d’utilisation

Les organisations veulent limiter le nombre de personnes qui ont accès aux informations ou aux ressources sécurisées afin de réduire le risque :

- qu’un utilisateur malveillant accède à ces données ;
- qu’un utilisateur autorisé ait par inadvertance un impact sur une ressource sensible.

Cependant, les utilisateurs doivent pouvoir continuer à effectuer des opérations privilégiées dans les applications Azure AD, Azure, Microsoft 365 ou SaaS. Les organisations peuvent donner aux utilisateurs un accès privilégié juste-à-temps aux ressources Azure et Azure AD et peuvent superviser ce que ces utilisateurs font avec leur accès privilégié.

## <a name="license-requirements"></a>Conditions de licence :

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Pour plus d’informations sur les licences utilisateur, consultez [Exigences relatives aux licences pour l’utilisation de Privileged Identity Management](subscription-requirements.md).

## <a name="what-does-it-do"></a>Fonction

Privileged Identity Management assure une activation de rôle basée sur l’heure et l’approbation pour atténuer les risques d’autorisations d’accès excessives, injustifiées ou malveillantes sur les ressources qui vous intéressent. Voici quelques-unes des principales fonctionnalités de Privileged Identity Management :

- Fournir un accès privilégié **juste-à-temps** à Azure AD et aux ressources Azure
- Affecter un accès aux ressources **limité dans le temps** à l’aide de dates de début et de fin
- Exiger une **approbation** pour activer les rôles privilégiés
- Appliquer l’**authentification multifacteur** pour l’activation des rôles
- Utiliser la **justification** pour comprendre le motif d’activation des utilisateurs
- Recevoir des **notifications** lors de l’activation de rôles privilégiés
- Effectuer des **révisions d’accès** pour vérifier que les utilisateurs ont toujours besoin de leurs rôles
- Télécharger l’**historique des audits** (internes ou externes)
- Empêcher la suppression de la **dernière attribution active du rôle Administrateur général**

## <a name="what-can-i-do-with-it"></a>À quoi sert PIM ?

Une fois que vous aurez configuré Privileged Identity Management, vous verrez les options **Tâches**, **Gérer** et **Activité** dans le menu de navigation de gauche. En tant qu’administrateur, vous avez le choix entre des options telles que la gestion des **rôles Azure AD**, la gestion des rôles de **ressources Azure** ou les groupes d’accès privilégié. Lorsque vous choisissez ce que vous souhaitez gérer, vous voyez l’ensemble d’options approprié correspondant à cette option.

![Capture d’écran de Privileged Identity Management dans le portail Azure](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Qui peut faire quoi ?

Pour les rôles Azure AD dans Privileged Identity Management, seul un utilisateur qui détient le rôle Administrateur de rôle privilégié ou Administrateur général peut gérer les attributions des autres administrateurs. Les administrateurs généraux, les administrateurs de la sécurité, les lecteurs généraux et les lecteurs Sécurité peuvent aussi consulter les attributions de rôles Azure AD dans Privileged Identity Management.

Pour les rôles de ressources Azure dans Privileged Identity Management, seul un administrateur d’abonnement, un propriétaire de ressource ou un administrateur de l’accès utilisateur aux ressources peut gérer les affectations des autres administrateurs. Par défaut, les utilisateurs qui possèdent un rôle d’administrateur de rôle privilégié, d’administrateur de la sécurité ou de lecteur Sécurité ne peuvent pas consulter les attributions de rôles de ressources Azure dans Privileged Identity Management.

## <a name="terminology"></a>Terminologie

Pour mieux comprendre Privileged Identity Management et sa documentation, vous devez connaître les termes suivants.

| Terme ou concept | Catégorie d’attribution de rôle | Description |
| --- | --- | --- |
| Éligible | Type | Attribution de rôle qui oblige l’utilisateur à effectuer une ou plusieurs actions pour utiliser ce rôle. Lorsqu’un utilisateur devient éligible pour un rôle, il peut l’activer pour réaliser des tâches privilégiées. Il n’existe aucune différence entre un accès accordé de façon permanente à un utilisateur et l’affectation d’un rôle éligible. La seule différence réside dans le fait que certaines personnes n’ont pas besoin d’un accès permanent. |
| active | Type | Attribution de rôle qui n’exige aucune action de la part de l’utilisateur pour être utilisée. Les utilisateurs actifs disposent des privilèges affectés au rôle. |
| Activer |  | Processus dans lequel une ou plusieurs actions sont exécutées dans le but d’utiliser un rôle pour lequel un utilisateur est éligible. Il peut s’agir de procéder à une vérification de l’authentification multifacteur (MFA), de fournir une justification professionnelle ou de demander une approbation aux approbateurs désignés. |
| Affecté | State | Utilisateur qui dispose d’une attribution de rôle active. |
| Activé | State | Utilisateur qui dispose d’une attribution de rôle éligible, qui a effectué les actions nécessaires à l’activation du rôle et qui est maintenant actif. Une fois le rôle activé, l’utilisateur peut s’en servir durant une période prédéfinie avant d’être obligé de l’activer à nouveau. |
| Éligibilité permanente | Duration | Attribution de rôle qui permet à un utilisateur d’être toujours éligible à l’activation du rôle. |
| Active en permanence | Duration | Attribution de rôle qui permet à un utilisateur de toujours utiliser un rôle sans effectuer aucune action. |
| éligible avec limitation dans le temps | Duration | Attribution de rôle qui permet à un utilisateur d’être éligible à l’activation d’un rôle uniquement pendant une période. |
| actif avec limitation dans le temps | Duration | Attribution de rôle qui permet à un utilisateur d’utiliser un rôle uniquement pendant une période. |
| Accès juste-à-temps (JIT) |  | Modèle où les utilisateurs reçoivent des autorisations temporaires pour effectuer des tâches privilégiées. De cette façon, les utilisateurs malveillants ou non autorisés ne peuvent pas accéder aux ressources une fois que les autorisations ont expiré. L’accès est accordé uniquement au moment où les utilisateurs en ont besoin. |
| Principe des privilèges d’accès minimum |  | Pratique de sécurité recommandée qui consiste à accorder à chaque utilisateur les privilèges minimum dont il a besoin pour accomplir les tâches qu’il est autorisé à effectuer. Cette pratique réduit le nombre d’administrateurs généraux et utilise à la place des rôles d’administrateur spécifiques pour certains scénarios. |

## <a name="extend-and-renew-assignments"></a>Étendre et renouveler les attributions

Après avoir configuré vos attributions de propriétaire ou de membres limitées dans le temps, vous vous demanderez peut-être ce qui se passe si une attribution expire. Dans cette nouvelle version, nous proposons deux options pour ce scénario :

- Étendre : quand une attribution de rôle arrive bientôt à expiration, l’utilisateur peut utiliser Privileged Identity Management pour demander son extension.
- Renouveler : quand une attribution de rôle a expiré, l’utilisateur peut utiliser Privileged Identity Management pour demander son renouvellement.

Ces deux actions initiées par l’utilisateur nécessitent l’approbation d’un administrateur général ou d’un administrateur de rôle privilégié. Les administrateurs n’ont pas besoin de s’occuper de la gestion des expirations d’attributions. Il suffit d’attendre que les demandes d’extension ou de renouvellement arrivent pour les approuver ou les refuser simplement.

## <a name="scenarios"></a>Scénarios

Privileged Identity Management prend en charge les scénarios suivants :

### <a name="privileged-role-administrator-permissions"></a>Autorisations d’administrateur de rôle privilégié

- Activer l’approbation pour des rôles spécifiques
- Spécifier les utilisateurs ou groupes approbateurs pour approuver des demandes
- Afficher l’historique des demandes et approbations pour tous les rôles privilégiés

### <a name="approver-permissions"></a>Autorisations d’approbateur

- Afficher les approbations (demandes) en attente
- Approuver ou rejeter des demandes d’élévation de rôle (unique et en bloc)
- Justifier mon approbation ou rejet

### <a name="eligible-role-user-permissions"></a>Autorisations d’utilisateur de rôle éligible

- Demander l’activation d’un rôle qui nécessite une approbation
- Afficher l’état de votre demande d’activation
- Exécuter la tâche dans Azure AD si l’activation a été approuvée

## <a name="managing-privileged-access-azure-ad-groups-preview"></a>Gestion des groupes Azure AD d’accès privilégié (préversion)

Dans Privileged Identity Management (PIM), vous pouvez désormais attribuer l’éligibilité à l’appartenance ou à la propriété de groupes d’accès privilégié. À partir de cette préversion, vous pouvez attribuer des rôles intégrés Azure AD (Azure Active Directory) à des groupes cloud et utiliser PIM pour gérer l’éligibilité à l’appartenance ou à la propriété des groupes et les activer. Pour plus d’informations sur les groupes avec attribution de rôle dans Azure AD, consultez [Utiliser des groupes Azure AD pour gérer les attributions de rôles](../roles/groups-concept.md).

>[!Important]
> Pour attribuer un groupe d’accès privilégié à un rôle afin d’obtenir un accès administratif à Exchange, au Centre de sécurité et de conformité ou à SharePoint, utilisez l’expérience **Rôles et administrateurs** du portail Azure AD, et non l’expérience Groupes d’accès privilégié pour que l’utilisateur ou le groupe puisse être activé dans le groupe.

### <a name="different-just-in-time-policies-for-each-group"></a>Stratégies juste-à-temps différentes pour chaque groupe

Certaines organisations utilisent des outils comme la collaboration B2B (business-to-business) Azure AD pour inviter leurs partenaires en tant qu’invités à leur organisation Azure AD. Au lieu d’utiliser une seule stratégie juste-à-temps pour toutes les attributions à un rôle privilégié, vous pouvez créer deux groupes d’accès privilégié différents avec leurs propres stratégies. Vous pouvez appliquer des exigences moins strictes à vos employés approuvés et des exigences plus strictes comme un workflow d’approbation à vos partenaires quand ils demandent une activation dans le groupe qui leur est attribué.

### <a name="activate-multiple-role-assignments-in-one-request"></a>Activation de plusieurs attributions de rôle en une seule requête

Avec la préversion des groupes d’accès privilégié, vous pouvez accorder aux administrateurs spécifiques à une charge de travail un accès rapide à plusieurs rôles avec une seule requête juste-à-temps. Par exemple, les administrateurs Office de niveau 3 peuvent avoir besoin d’un accès juste-à-temps aux rôles Administrateur Exchange, Administrateur des applications Office, Administrateur Teams et Administrateur de recherche pour examiner minutieusement les incidents tous les jours. Jusqu’à présent, quatre requêtes consécutives étaient nécessaires, ce qui représentait un processus particulièrement long. Au lieu de cela, vous pouvez créer un groupe avec attribution de rôle appelé « Administrateurs Office de niveau 3 », l’attribuer à chacun des quatre rôles mentionnés précédemment (ou à n’importe quel rôle intégré Azure AD) et l’activer pour l’accès privilégié dans la section Activité du groupe. Après avoir activé le groupe pour l’accès privilégié, vous pouvez configurer les paramètres juste-à-temps pour les membres du groupe et définir vos administrateurs et propriétaires comme éligibles. Quand l’accès privilégié est accordé aux administrateurs dans le groupe, ces derniers deviennent membres des quatre rôles Azure AD.

## <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Inviter des utilisateurs invités et attribuer des rôles de ressources Azure dans Privileged Identity Management

Les utilisateurs invités Azure Active Directory (Azure AD) font partie des fonctionnalités de collaboration B2B (Business-to-Business) dans Azure AD, afin que vous puissiez gérer utilisateurs et fournisseurs invités externes en tant qu’invités dans Azure AD. Par exemple, vous pouvez utiliser ces fonctionnalités Privileged Identity Management pour les tâches d’identité Azure ayant des invités, comme l’attribution de l’accès à des ressources Azure spécifiques, la spécification de la durée et de la date de fin de l’attribution ou la nécessité d’une vérification en deux étapes lors de l’attribution ou de l’activation. Pour plus d’informations sur la manière d’inviter un invité dans votre organisation et de gérer son accès, consultez [Ajouter des utilisateurs B2B Collaboration dans le portail Azure AD](../external-identities/add-users-administrator.md).

### <a name="when-would-you-invite-guests"></a>Quand voulez-vous convier des invités ?

Voici quelques exemples où vous pourriez convier des invités à votre organisation :

- Autoriser un fournisseur externe indépendant qui dispose uniquement d’un compte e-mail à accéder à vos ressources Azure dans le cadre d’un projet.
- Autoriser un partenaire externe d’une grande organisation qui utilise les services de fédération Active Directory (AD FS) locaux à accéder à votre application de notes de frais.
- Autoriser les ingénieurs du support technique ne faisant pas partie de votre organisation, par exemple les ingénieurs du support technique Microsoft, à accéder temporairement à vos ressources Azure pour résoudre des problèmes.

### <a name="how-does-collaboration-using-b2b-guests-work"></a>Comment fonctionne la collaboration avec des invités B2B ?

Lorsque vous utilisez une collaboration B2B, vous pouvez inviter un utilisateur externe à votre organisation en tant qu’invité. L’invité peut être géré en tant qu’utilisateur dans votre organisation, mais il doit être authentifié dans son organisation d’origine et non dans votre organisation Azure AD. Cela signifie que si l’invité n’a plus accès à son organisation d’origine, il n’a également plus accès à votre organisation. Par exemple, si l’invité quitte son organisation, il perd automatiquement l’accès à toutes les ressources que vous avez partagées avec lui dans Azure AD sans que vous ayez rien à faire. Pour plus d’informations sur B2B Collaboration, consultez [Présentation de l’accès utilisateur invité dans Azure Active Directory B2B](../external-identities/what-is-b2b.md).

![Diagramme montrant comment un utilisateur invité est authentifié dans son annuaire d’origine](./media/pim-configure/b2b-external-user.png)

## <a name="next-steps"></a>Étapes suivantes

- [Exigences relatives aux licences pour l’utilisation de Privileged Identity Management](subscription-requirements.md)
- [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Déployer Privileged Identity Management](pim-deployment-plan.md)
