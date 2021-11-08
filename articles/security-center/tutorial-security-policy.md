---
title: Utilisation de stratégies de sécurité | Microsoft Docs
description: Découvrez comment utiliser les stratégies de sécurité dans Microsoft Defender pour le cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: memildin
ms.openlocfilehash: ac92e330c9d511337bc2d6cafa08da7ba455f180
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425004"
---
# <a name="manage-security-policies"></a>Gérer les stratégies de sécurité

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cette page explique comment les stratégies de sécurité sont configurées et comment les afficher dans Microsoft Defender pour le cloud. 

Pour comprendre les relations entre les initiatives, les stratégies et les recommandations, consultez [Que sont les stratégies de sécurité, les initiatives et les recommandations ?](security-policy-concept.md)

## <a name="who-can-edit-security-policies"></a>Qui peut modifier des stratégies de sécurité ?

Defender pour le cloud utilise le contrôle d’accès en fonction du rôle (Azure RBAC), lequel fournit des rôles intégrés que vous pouvez attribuer à des utilisateurs, groupes et services Azure. Lorsque les utilisateurs ouvrent Defender pour le cloud, ils ne voient que les informations associées aux ressources auxquelles ils peuvent accéder. Autrement dit, les utilisateurs se voient attribuer le rôle de *propriétaire*, *contributeur* ou *lecteur* pour l’abonnement de la ressource. Il existe également deux rôles spécifiques à Defender pour le cloud :

- **Lecteur de sécurité** : a le droit de consulter les éléments de Defender pour le cloud tels que les recommandations, les alertes, la stratégie et l’intégrité. Ne peut pas apporter de modifications.
- **Administrateur Sécurité** : a les mêmes droits de consultation que *Lecteur Sécurité*. Peut également mettre à jour la stratégie de sécurité et ignorer les alertes.

Vous pouvez modifier les stratégies de sécurité sur le portail Azure Policy, par l’intermédiaire de l’API REST ou à l’aide de Windows PowerShell.

## <a name="manage-your-security-policies"></a>Gérer vos stratégies de sécurité

Pour afficher vos stratégies de sécurité dans Defender pour le cloud :

1. Dans le menu de **Defender pour le cloud**, sélectionnez **Stratégie de sécurité**.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="Page Gestion de stratégie.":::

   Dans l’écran **Gestion de stratégie**, vous pouvez voir le nombre de groupes d’administration, d’abonnements et d’espaces de travail, ainsi que votre structure de groupes d’administration.

1. Sélectionnez le groupe d’administration ou l’abonnement dont vous souhaitez afficher les stratégies.

1. La page relative à la stratégie de sécurité de cet abonnement ou ce groupe d’administration s’affiche. Elle présente les stratégies disponibles et attribuées.

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Page stratégie de sécurité de Defender pour le cloud" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > Si l’étiquette « Groupe d’administration hérité » figure en regard de votre stratégie par défaut, cela signifie que la stratégie a été attribuée à un groupe d’administration et que l’abonnement que vous consultez en a hérité.

1. Choisissez parmi les options disponibles sur cette page :

    1. Pour utiliser des normes sectorielles, sélectionnez **Ajouter des normes**. Pour plus d’informations, consultez [Personnaliser l’ensemble de normes du tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).

    1. Pour attribuer et gérer des initiatives personnalisées, sélectionnez **Ajouter des initiatives personnalisées**. Pour plus d’informations, consultez [Utilisation d’initiatives et de stratégies de sécurité personnalisées](custom-security-policies.md).

    1. Pour afficher et modifier l’initiative par défaut, sélectionnez **Afficher la stratégie actuelle**, puis procédez de la manière décrite ci-dessous. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Écran Stratégie actuelle.":::

       L’écran **Stratégie de sécurité** reflète l’action effectuée par les stratégies affectées sur l’abonnement ou le groupe d’administration que vous avez sélectionnés.
       
       * Utilisez les liens fournis en haut pour ouvrir une **attribution de stratégie** s’appliquant à l’abonnement ou au groupe d’administration. Ces liens vous permettent d’accéder à l’attribution et de modifier ou désactiver la stratégie. Par exemple, si vous voyez qu’une attribution de stratégie particulière refuse effectivement la protection du point de terminaison, utilisez le lien pour modifier ou désactiver la stratégie.
       
       * Dans la liste des stratégies, vous pouvez voir l’application effective de la stratégie sur votre groupe d’administration ou abonnement. Les paramètres de chaque stratégie qui s’appliquent à l’étendue sont pris en compte et le résultat cumulé des actions effectuées par la stratégie s’affiche. Par exemple, si un attribution de la stratégie est désactivée, mais définie sur AuditIfNotExist dans une autre, l’effet cumulé applique AuditIfNotExist. L’effet le plus actif est toujours prioritaire.
       
       * L’effet des stratégies peut être : Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Pour plus d’informations sur la manière dont les effets sont appliqués, consultez [Comprendre les effets de Policy](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Lorsque vous affichez les stratégies attribuées, vous pouvez voir plusieurs affectations et comment chacune d’elles est configurée individuellement.


## <a name="disable-security-policies-and-disable-recommendations"></a>Désactiver des stratégies de sécurité et des recommandations

Lorsque votre initiative de sécurité déclenche une recommandation qui n’est pas pertinente pour votre environnement, vous pouvez empêcher cette recommandation de s’afficher à nouveau. Pour désactiver une recommandation, désactivez la stratégie spécifique qui génère la recommandation.

La recommandation que vous souhaitez désactiver continuera de s’afficher si elle est requise par une norme réglementaire que vous avez appliquée à l’aide des outils de conformité réglementaire de Defender pour le cloud. Même si vous avez désactivé une stratégie dans l’initiative intégrée, une autre de l’initiative de la norme réglementaire déclenchera toujours la recommandation si celle-ci est nécessaire à la conformité. Vous ne pouvez pas désactiver les stratégies d’initiatives de normes réglementaires.

Pour plus d’informations sur les recommandations, consultez [Gestion des recommandations de sécurité](review-security-recommendations.md).

1. Dans le menu de Defender pour le cloud, sélectionnez **Stratégie de sécurité**.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Démarrage du processus de gestion des stratégies dans Microsoft Defender pour le cloud.":::

1. Sélectionnez le groupe d'abonnements ou d'administration pour lequel vous souhaitez désactiver la recommandation (et la stratégie).

   > [!NOTE]
   > N’oubliez pas qu’un groupe d’administration applique ses stratégies à ses abonnements. Par conséquent, si vous désactivez la stratégie d’un abonnement et que celui-ci appartient à un groupe d’administration qui utilise encore la même stratégie, vous continuez à recevoir les recommandations de la stratégie. La stratégie sera toujours appliquée à partir du niveau d’administration, et les suggestions seront toujours générées.

1. Dans les sections **Stratégie par défaut**, **Normes réglementaires et de l’industrie** ou **Vos initiatives personnalisées**, sélectionnez l’initiative appropriée contenant la stratégie que vous souhaitez désactiver.

1. Ouvrez la section **Paramètres** et recherchez la stratégie qui appelle la suggestion que vous souhaitez désactiver.

1. Dans la liste déroulante, remplacez la valeur de la stratégie correspondante par **Désactivé**.

   ![Désactiver une stratégie.](./media/tutorial-security-policy/disable-policy.png)

1. Sélectionnez **Enregistrer**.

   > [!NOTE]
   > La modification peut prendre jusqu’à 12 heures avant d’être effective.


## <a name="enable-a-security-policy"></a>Activer une stratégie de sécurité

Certaines stratégies de vos initiatives peuvent être désactivées par défaut. Par exemple, dans l’initiative d’Azure Security Benchmark, certaines stratégies sont fournies pour vous permettre de les activer uniquement si elles répondent à une exigence de conformité ou de réglementation spécifique pour votre organisation. Ces stratégies incluent des recommandations pour chiffrer les données au repos avec des clés gérées par le client, telles que « les registres de conteneur doivent être chiffrés avec une clé gérée par le client (CMK) ».

Pour activer une stratégie désactivée et vous assurer qu’elle est évaluée pour vos ressources :

1. Dans le menu de Defender pour le cloud, sélectionnez **Stratégie de sécurité**.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Démarrage du processus de gestion des stratégies dans Microsoft Defender pour le cloud.":::

1. Sélectionnez le groupe d'abonnements ou d'administration pour lequel vous souhaitez activer la recommandation (et la stratégie).

1. Dans les sections **Stratégie par défaut**, **Normes réglementaires et de l’industrie** ou **Vos initiatives personnalisées**, sélectionnez l’initiative appropriée contenant la stratégie que vous souhaitez désactiver.

1. Ouvrez la section **Paramètres** et recherchez la stratégie qui appelle la suggestion que vous souhaitez désactiver.

1. Dans la liste déroulante, remplacez la valeur de la stratégie correspondante par **AuditIfNotExists** ou **Enforce**.

1. Sélectionnez **Enregistrer**.

   > [!NOTE]
   > La modification peut prendre jusqu’à 12 heures avant d’être effective.


## <a name="next-steps"></a>Étapes suivantes
Cette page a décrit les stratégies de sécurité. Pour accéder à des informations connexes, consultez les rubriques suivantes :

- [Découvrez comment définir des stratégies avec PowerShell](../governance/policy/assign-policy-powershell.md)
- [Découvrez comment modifier une stratégie de sécurité dans Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Découvrez comment définir une stratégie qui couvre plusieurs abonnements ou s’applique à des groupes d’administration avec Azure Policy](../governance/policy/overview.md)
- [Découvrez comment activer Defender pour le cloud sur tous les abonnements d’un groupe d’administration](onboard-management-group.md)