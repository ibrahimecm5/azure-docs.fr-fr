---
title: Recommandations de sécurité dans le Centre de sécurité Azure
description: Ce document vous montre comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure et à rester en conformité avec les stratégies de sécurité.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: memildin
ms.openlocfilehash: 23ae72a4ea8759dcb4dd55ab51476ad7761e1a08
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223231"
---
# <a name="review-your-security-recommendations"></a>Consulter vos recommandations de sécurité

Cette rubrique explique comment visualiser et comprendre les recommandations d’Azure Security Center pour vous aider à protéger vos ressources Azure.

## <a name="monitor-recommendations"></a>Surveiller les recommandations <a name="monitor-recommendations"></a>

Azure Security Center analyse l’état de sécurité de vos ressources pour identifier les vulnérabilités potentielles. 

1. Dans le menu de Security Center, ouvrez la page **Recommendations** pour voir les recommandations applicables à votre environnement. Les recommandations sont regroupées en contrôles de sécurité.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Recommandations regroupées par contrôle de sécurité." lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Pour rechercher des recommandations propres au type de ressource, à la gravité, à l’environnement ou à d’autres critères qui sont importants pour vous, utilisez les filtres facultatifs au-dessus de la liste des recommandations.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filtres permettant d’affiner la liste des recommandations d’Azure Security Center.":::

1. Développez un contrôle et sélectionnez une recommandation spécifique pour afficher la page de recommandation détaillée.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Page Détails de la recommandation." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    La page comprend les éléments suivants :

    1. Pour les recommandations prises en charge, la barre d’outils supérieure affiche tout ou partie des boutons suivants :
        - **Appliquer** et **Refuser** (voir [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md)).
        - **Afficher la définition de stratégie** pour accéder directement à l’entrée d’Azure Policy pour la stratégie sous-jacente.
        - **Ouvrir la requête** : Toutes les recommandations ont la possibilité d’afficher les informations détaillées sur les ressources concernées à l’aide d’Azure Resource Graph Explorer.
    1. **Indicateur de gravité**.
    1. **Intervalle d’actualisation** (le cas échéant).
    1. **Nombre de ressources exemptées** ; si des exemptions existent pour cette recommandation, il indique le nombre de ressources qui ont été exemptées.
    1. **Description** : Courte description du problème de sécurité.
    1. Le cas échéant, la page de détails comprend également un tableau des **recommandations connexes** :

        Les types de relations sont les suivants :

        - **Condition préalable** : Recommandation qui doit être complétée avant la recommandation sélectionnée
        - **Alternative** : recommandation différente qui offre une autre façon d’atteindre les objectifs de la recommandation sélectionnée
        - **Dépendante** : recommandation pour laquelle la recommandation sélectionnée est une condition préalable

        Pour chaque recommandation associée, le nombre de ressources défectueuses s’affiche dans la colonne « Ressources affectées ».

        > [!TIP]
        > Si une recommandation connexe est grisée, sa dépendance n’est pas encore terminée et n’est donc pas disponible.

    1. **Étapes de correction** : description des étapes manuelles nécessaires pour corriger le problème de sécurité sur les ressources concernées. Pour obtenir des recommandations avec l’option **Correctif****, vous pouvez sélectionner **Afficher la logique de correction** avant d’appliquer la correction suggérée à vos ressources.

    1. **Ressources concernées** : vos ressources sont regroupées dans des onglets :
        - **Ressources saines** : ressources pertinentes qui ne sont pas touchées ou sur lesquelles vous avez déjà corrigé le problème.
        - **Ressources non saines** : ressources qui sont encore concernées par le problème identifié.
        - **Ressources supprimées** - Resources qui ont été supprimées entre les cycles d’évaluation (comme défini par l’intervalle d’actualisation).
        - **Ressources non applicables** : ressources pour lesquelles la recommandation ne peut pas donner de réponse définitive. L’onglet Non applicables comprend également les raisons pour chaque ressource. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Ressources non applicables avec leurs raisons.":::

    1. Boutons d’action pour corriger la recommandation ou déclencher une application logique.


## <a name="review-recommendation-data-in-azure-resource-graph-explorer-arg"></a>Explorez les données de recommandation dans l’Explorateur Azure Resource Graph (ARG)

La barre d’outils de la page Détails de la recommandation inclut un bouton **Ouvrir une requête** pour explorer les détails dans [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml), un service Azure qui permet d’interroger les données relatives à la sécurité de Security Center sur plusieurs abonnements.

ARG est conçu pour fournir une exploration efficace des ressources avec la possibilité d’interroger à l’échelle dans vos environnements Cloud avec des fonctionnalités de filtrage, de regroupement et de tri robustes. Il s’agit d’un moyen rapide et efficace de demander des informations dans les abonnements Azure par programmation ou depuis le Portail Azure.

À l’aide de [Kusto Query Langage (KQL)](/azure/data-explorer/kusto/query/), vous pouvez croiser les données ASC avec d’autres propriétés de ressource.

Par exemple, cette page de détails de recommandation affiche quinze ressources affectées :

:::image type="content" source="./media/security-center-recommendations/open-query.png" alt-text="Le bouton **Ouvrir la requête** sur la page Détails de la recommandation.":::

Lorsque vous ouvrez la requête sous-jacente et que vous l’exécutez, l’Explorateur Azure Resource Graph retourne les quinze mêmes ressources et leur état d’intégrité pour cette recommandation : 

:::image type="content" source="./media/security-center-recommendations/run-query.png" alt-text="L’Explorateur Azure Resource Graph affiche les résultats de la recommandation présentée dans la capture d’écran précédente.":::


## <a name="preview-recommendations"></a>Recommandations sur la préversion

Les recommandations marquées **Preview (Préversion)** ne sont pas incluses dans les calculs de votre degré de sécurisation.

Elles doivent tout de même être corrigées dans la mesure du possible, ainsi lorsque la période de préversion se termine, elles seront prises en compte dans le calcul.

Exemple de recommandation de préversion :

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recommandation portant l’indicateur de préversion.":::
 
## <a name="next-steps"></a>Étapes suivantes

Ce document vous a présenté les recommandations de sécurité du Centre de sécurité. Pour obtenir des informations connexes :

- [Implémenter les recommandations](security-center-remediate-recommendations.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md).
- [Automatiser les réponses aux déclencheurs Security Center](workflow-automation.md) : automatiser les réponses aux recommandations.
- [Exempter une ressource d’une recommandation](exempt-resource.md)
- [Recommandations de sécurité - Guide de référence](recommendations-reference.md)