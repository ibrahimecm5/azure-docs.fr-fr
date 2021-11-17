---
title: Hiérarchiser les actions de sécurité par sensibilité des données – Microsoft Defender pour le cloud
description: Utilisez les classifications de sensibilité des données d’Azure Purview dans Microsoft Defender pour le cloud.
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 7f82750d9b02bba6e843390562ae2eee060cef47
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478989"
---
# <a name="prioritize-security-actions-by-data-sensitivity"></a>Hiérarchiser les actions de sécurité par sensibilité des données

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Purview](../purview/overview.md), le service de gouvernance des données de Microsoft, fournit des informations détaillées sur la *sensibilité de vos données*. Grâce à la détection automatisée des données, à la classification des données sensibles et au lignage des données de bout en bout, Purview aide les organisations à gérer et à régir les données dans des environnements hybrides et multiclouds.

Les clients de Microsoft Defender pour le cloud utilisant Azure Purview peuvent bénéficier d’une couche vitale supplémentaire de métadonnées dans les alertes et les recommandations : des informations sur les données potentiellement sensibles concernées. Cette connaissance permet de résoudre le défi du triage et garantit que les professionnels de la sécurité peuvent concentrer leur attention sur les menaces pesant sur les données sensibles.

Cette page explique l’intégration des étiquettes de classification de la sensibilité des données de Purview dans Defender pour le cloud.

## <a name="availability"></a>Disponibilité
|Aspect|Détails|
|----|:----|
|État de sortie :|Préversion.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Prix :|Vous aurez besoin d’un compte Azure Purview pour créer les classifications de sensibilité des données et exécuter les analyses. L’affichage des résultats de l’analyse et l’utilisation de la sortie sont gratuits pour les utilisateurs de Defender pour le cloud.|
|Rôles et autorisations obligatoires :|**Administrateur de la sécurité** et **Contributeur de la sécurité**|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/no-icon.png":::Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet (**partiellement** : sous-ensemble d’alertes et évaluation des vulnérabilités pour les serveurs SQL. Les protections contre les menaces comportementales ne sont pas disponibles.)|
|||


## <a name="the-triage-problem-and-defender-for-clouds-solution"></a>Le problème du triage et la solution de Defender pour le cloud
Les équipes de sécurité sont régulièrement confrontées au défi du triage des problèmes entrants. 

Defender pour le cloud comprend deux mécanismes permettant de hiérarchiser les recommandations et les alertes de sécurité :

- Pour les recommandations, nous avons fourni des **contrôles de sécurité** pour vous aider à comprendre l’importance de chaque recommandation vis-à-vis de votre posture de sécurité globale. Defender pour le cloud inclut une valeur de **score de sécurité** pour chaque contrôle afin de vous aider à hiérarchiser votre travail de sécurité. Pour en savoir plus, consultez [Contrôles de sécurité et recommandations](secure-score-security-controls.md#security-controls-and-their-recommendations).

- Pour les alertes, nous avons attribué des **étiquettes de gravité** à chaque alerte pour vous aider à hiérarchiser l’ordre dans lequel vous vous occupez de chaque alerte. Pour en savoir plus, consultez [Comment les alertes sont-elles classifiées ?](alerts-overview.md#how-are-alerts-classified)

Toutefois, dans la mesure du possible, vous voudrez concentrer les efforts de l’équipe de sécurité sur les risques pour les **données** de l’organisation. Si deux recommandations ont le même impact sur votre score de sécurité, mais que l’une d’entre elles concerne une ressource contenant des données sensibles, l’idéal serait d’en tenir compte lors de l’établissement des priorités.

Les classifications de sensibilité des données et les étiquette de confidentialité des données d’Azure Purview fournissent ces informations.

## <a name="discover-resources-with-sensitive-data"></a>Découvrir les ressources contenant des données sensibles
Pour fournir les informations essentielles sur les données sensibles découvertes et s’assurer que vous disposez de ces informations lorsque vous en avez besoin, Defender pour le cloud affiche les informations de Purview à plusieurs emplacements.

> [!TIP]
> Si une ressource est analysée par plusieurs comptes Purview, les informations affichées dans Defender pour le cloud concernent l’analyse la plus récente.


### <a name="alerts-and-recommendations-pages"></a>Pages d’alertes et de recommandations
Lorsque vous examinez une recommandation ou enquêtez sur une alerte, les informations sur les données potentiellement sensibles concernées sont incluses dans la page.

Cette couche supplémentaire essentielle de métadonnées contribue à résoudre le défi du triage et garantit que votre équipe de sécurité peut concentrer son attention sur les menaces pesant sur les données sensibles.



### <a name="inventory-filters"></a>Filtres d’inventaire
La [page d’inventaire des ressources](asset-inventory.md) dispose d’une collection de filtres puissants pour regrouper vos ressources avec des alertes et des recommandations non résolues en fonction des critères pertinents pour tout scénario. Ces filtres incluent des **classifications de sensibilité des données** et des **étiquettes de confidentialité des données**. Utilisez ces filtres pour évaluer la posture de sécurité des ressources sur lesquelles Purview a découvert des données sensibles.

:::image type="content" source="./media/information-protection/information-protection-inventory-filters.png" alt-text="Capture d’écran des filtres de protection des informations dans la page d’inventaire des ressources de Microsoft Defender pour le cloud." lightbox="./media/information-protection/information-protection-inventory-filters.png":::

### <a name="resource-health"></a>Intégrité des ressources 
Lorsque vous sélectionnez une seule ressource, que ce soit à partir d’une alerte, d’une recommandation ou de la page d’inventaire, vous accédez à une page d’intégrité détaillée présentant une vue centrée sur la ressource avec les informations de sécurité importantes relatives à cette ressource. 

La page Intégrité des ressources fournit une vue instantanée de l’état d’intégrité global d’une ressource unique. Vous pouvez consulter des informations détaillées sur la ressource et toutes les recommandations qui s’y appliquent. En outre, si vous utilisez l’un des plans Microsoft Defender, vous pouvez également consulter les alertes de sécurité non résolues pour cette ressource spécifique.

Lorsque vous examinez l’intégrité d’une ressource spécifique, vous voyez les informations de Purview sur cette page et pouvez les utiliser pour déterminer les données qui ont été découvertes sur cette ressource ainsi que le compte Purview utilisé pour analyser la ressource.

:::image type="content" source="./media/information-protection/information-protection-resource-health.png" alt-text="Capture d’écran de la page d’intégrité des ressources de Defender pour le cloud montrant les classifications et les étiquettes de protection des informations d’Azure Purview." lightbox="./media/information-protection/information-protection-resource-health.png":::

### <a name="overview-tile"></a>Vignette d’aperçu
La vignette **Information Protection** dédiée dans le [tableau de bord de présentation](overview-page.md) de Defender pour le cloud montre la couverture d’Azure Purview. Elle montre également les types de ressources avec les données les plus sensibles découvertes.

Un graphique indique le nombre de recommandations et d’alertes par types de ressources classifiés. La vignette comprend également un lien vers Azure Purview pour analyser des ressources supplémentaires. Sélectionnez la vignette pour afficher les ressources classifiées dans la page d’inventaire des ressources de Defender pour le cloud.

:::image type="content" source="./media/information-protection/overview-dashboard-information-protection.png" alt-text="Capture d’écran de la vignette Information Protection dans le tableau de bord de présentation de Microsoft Defender pour le cloud." lightbox="./media/information-protection/overview-dashboard-information-protection.png":::


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez :

- [Qu’est-ce qu’Azure Purview ?](../purview/overview.md)
- [Sources de données et types de fichiers pris en charge](../purview/sources-and-scans.md) et [Magasins de données pris en charge par Purview](../purview/purview-connector-overview.md)
- [Meilleures pratiques de déploiement d’Azure Purview](../purview/deployment-best-practices.md)
- [Comment étiqueter vos données dans Azure Purview](../purview/how-to-automatically-label-your-content.md)
