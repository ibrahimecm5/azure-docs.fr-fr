---
title: Implémenter des recommandations de sécurité dans Microsoft Defender pour le cloud | Microsoft Docs
description: Ce article explique comment appliquer les recommandations dans Microsoft Defender pour le cloud pour protéger vos ressources et respecter les stratégies de sécurité.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: d01a8f806154cdf0260f0dcf8c9f6c65386c99f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437907"
---
# <a name="implement-security-recommendations-in-microsoft-defender-for-cloud"></a>Recommandations de sécurité dans Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les recommandations fournissent des suggestions visant à mieux sécuriser vos ressources. Pour appliquer une recommandation, vous devez suivre les étapes de correction qu’elle fournit.

## <a name="remediation-steps"></a>Étapes de correction <a name="remediation-steps"></a>

Après avoir lu toutes les recommandations, vous devez décider laquelle appliquer en premier. Nous vous recommandons de traiter en priorité les contrôles de sécurité qui sont le plus susceptibles d’augmenter votre degré de sécurisation.

1. Sélectionnez une recommandation dans la liste.

1. Suivez les instructions dans la section **Étapes de correction**. Chaque recommandation comprend un ensemble d’instructions. La capture d’écran suivante montre les étapes de correction permettant de configurer les applications de manière à autoriser uniquement le trafic via HTTPS.

    :::image type="content" source="./media/implement-security-recommendations/security-center-remediate-recommendation.png" alt-text="Étapes de correction manuelle pour une recommandation." lightbox="./media/implement-security-recommendations/security-center-remediate-recommendation.png":::

1. Une fois l’opération terminée, une notification vous indique si le problème est résolu.

## <a name="fix-button"></a>Bouton Corriger

Pour simplifier la correction et améliorer la sécurité de votre environnement (et ainsi votre degré de sécurisation), de nombreuses recommandations comportent une option **Corriger**.

La fonctionnalité **Corriger** vous permet d'appliquer rapidement une recommandation à plusieurs ressources.

> [!TIP]
> La fonctionnalité **Corriger** est uniquement disponible pour des recommandations spécifiques. Pour connaître les recommandations concernées, utilisez le filtre **Actions de réponse** dans la liste des recommandations :
> 
> :::image type="content" source="media/implement-security-recommendations/quick-fix-filter.png" alt-text="Utilisez les filtres disponibles au-dessus de la liste des recommandations pour rechercher des recommandations incluant l’option Corriger.":::

Pour implémenter une option **Corriger** :

1. Dans la liste des recommandations accompagnées de l'icône d'action **Corriger**, :::image type="icon" source="media/implement-security-recommendations/fix-icon.png" border="false":::sélectionnez une recommandation.

    :::image type="content" source="./media/implement-security-recommendations/security-center-recommendations-fix-action.png" alt-text="Liste des recommandations mettant en évidence les recommandations accompagnées de l'action Corriger" lightbox="./media/implement-security-recommendations/security-center-recommendations-fix-action.png":::

1. Sous l’onglet **Ressources défectueuses**, sélectionnez les ressources auxquelles vous souhaitez implémenter la recommandation, puis sélectionnez **Corriger**.

    > [!NOTE]
    > Certaines des ressources listées peuvent être désactivées, ce qui est le cas si vous n’avez pas les autorisations appropriées pour les modifier.

1. Dans la zone de confirmation, lisez les détails et les implications de la correction.

    ![Correctif rapide.](./media/implement-security-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > Les implications sont listées dans la zone grise de la fenêtre **Corriger les ressources** qui s’ouvre après que vous avez cliqué sur **Corriger**. Elles indiquent les changements apportés si vous utilisez l'option **Corriger**.

1. Ajoutez les paramètres appropriés, si nécessaire, et approuvez la correction.

    > [!NOTE]
    > Une fois la correction terminée, il peut s’écouler plusieurs minutes avant que les ressources apparaissent sous l’onglet **Ressources saines**. Pour voir les actions de correction, consultez le [journal d’activité](#activity-log).

1. Une fois l’opération terminée, une notification s’affiche pour vous informer si la mise à jour a réussi.

## <a name="fix-actions-logged-to-the-activity-log"></a>Actions de correction consignées dans le journal d'activité <a name="activity-log"></a>

L’opération de correction applique la configuration à la ressource à l’aide d’un déploiement de modèle ou d’une requête `PATCH` d’API REST. Ces opérations sont consignées dans le [journal d’activité Azure](../azure-monitor/essentials/activity-log.md).


## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment appliquer les recommandations fournies par Defender pour le cloud. Pour en savoir plus sur la définition et la sélection des recommandations pour votre environnement, consultez la page suivante :

- [Présentation des stratégies de sécurité, des initiatives et des recommandations](security-policy-concept.md).