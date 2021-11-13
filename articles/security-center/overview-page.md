---
title: Page « Vue d’ensemble » ou tableau de bord principal de Microsoft Defender pour le cloud
description: Découvrir les fonctionnalités de la page de présentation de Defender pour le cloud
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 640b6fedd532174ff86e222a7479007b9e72b5bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437825"
---
# <a name="microsoft-defender-for-clouds-overview-page"></a>Page de présentation de Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Quand vous ouvrez Microsoft Defender pour le cloud, la première page qui s’affiche est la page de présentation. 

Ce tableau de bord interactif fournit une vue unifiée de la posture de sécurité de vos charges de travail cloud hybrides. En outre, elle affiche des alertes de sécurité, des informations de couverture, etc.

Vous pouvez sélectionner n’importe quel élément de la page pour obtenir des informations plus détaillées.

:::image type="content" source="./media/overview-page/overview.png" alt-text="Capture d’écran de la page de présentation de Defender pour le cloud." lightbox="./media/overview-page/overview.png":::
## <a name="features-of-the-overview-page"></a>Fonctionnalités de la page de présentation

:::image type="content" source="./media/overview-page/top-bar-of-overview.png" alt-text="Capture d’écran de la barre supérieure de la page de présentation de Defender pour le cloud.":::

### <a name="metrics"></a>Mesures
La **barre de menus supérieure** offre :
- **Abonnements** : vous pouvez afficher et filtrer la liste des abonnements en sélectionnant ce bouton. Defender pour le cloud ajuste l’affichage pour refléter la posture de sécurité des abonnements sélectionnés.
- **Nouveautés** : ouvre les [notes de publication](release-notes.md) afin que vous puissiez être informé des nouvelles fonctionnalités, des correctifs de bogues et des fonctionnalités déconseillées.
- **Les numéros de haut niveau** pour les comptes cloud connectés, afin d’afficher le contexte des informations dans les mosaïques principales ci-dessous. De même que le nombre de ressources évaluées, de recommandations actives et d’alertes de sécurité. Sélectionnez le nombre des ressources évaluées pour accéder à [Inventaire des ressources](asset-inventory.md). En savoir plus sur la connexion de vos [comptes AWS](quickstart-onboard-aws.md) et vos [projets GCP](quickstart-onboard-gcp.md).

### <a name="feature-tiles"></a>Vignettes de fonctionnalité
Au centre de la page se trouvent les **vignettes de fonctionnalité**, chacune lié à une fonctionnalité de haut profil ou un tableau de bord dédié :
- **Degré de sécurisation** : Defender pour le cloud évalue continuellement vos ressources, vos abonnements et votre organisation pour y rechercher d’éventuels problèmes de sécurité. Il agrège ensuite toutes ses découvertes sous la forme d’un score qui vous permet de déterminer d’un coup d’œil votre niveau de sécurité actuel : plus le score est élevé, plus le niveau de risque identifié est faible. [Plus d’informations](secure-score-security-controls.md)
- **Protections de charge de travail** : il s’agit de la plateforme de protection de charge de travail cloud (CWPP) intégrée à Defender pour le cloud qui offre une protection avancée et intelligente de vos charges de travail exécutées sur Azure, sur des machines locales ou d’autres fournisseurs de cloud. À chaque type de ressource correspond un plan Microsoft Defender. La mosaïque affiche la couverture de vos ressources connectées (pour les abonnements actuellement sélectionnés) et les alertes récentes, avec un code de couleur par gravité. En savoir plus sur les [fonctionnalités de sécurité renforcée](enhanced-security-features-overview.md).
- **Conformité réglementaire** : Defender pour le cloud fournit des insights sur votre posture de conformité à partir d’évaluations continues de votre environnement Azure. Defender pour le cloud analyse les facteurs de risque dans votre environnement conformément aux bonnes pratiques de sécurité. Ces évaluations sont comparées à des contrôles de conformité provenant d’un ensemble de normes prises en charge. [Plus d’informations](regulatory-compliance-dashboard.md)
- **Firewall Manager** : cette vignette indique l’état de vos hubs et réseaux à partir d’[Azure Firewall Manager](../firewall-manager/overview.md).
- **Inventaire** : la page d’inventaire des ressources de Microsoft Defender pour le cloud vous permet de voir dans une même page la posture de sécurité des ressources que vous avez connectées à Microsoft Defender pour le cloud. Toutes les ressources avec des recommandations de sécurité non résolues sont affichées dans l’inventaire. Si vous avez activé l’intégration de Microsoft Defender pour point de terminaison et Microsoft Defender pour les serveurs, vous avez également accès à l’inventaire logiciel. La vignette de la page de présentation vous montre un aperçu du nombre total de ressources saines et non saines (pour les abonnements actuellement sélectionnés). [Plus d’informations](asset-inventory.md)
- **Protection des informations** : un graphique sur cette vignette montre les types de ressource analysés par [Azure Purview](../purview/overview.md) qui contiennent des données sensibles, et qui ont des recommandations et des alertes non résolues. Suivez le lien de l’**analyse** pour accéder aux comptes Azure Purview et configurer de nouvelles analyses, ou sélectionnez une autre partie de la vignette pour ouvrir l’[inventaire des ressources](asset-inventory.md) et voir vos ressources en fonction de la classification de sensibilité de vos données Purview. [Plus d’informations](information-protection.md)

### <a name="insights"></a>Insights
Le volet **Insights** offre des éléments personnalisés pour votre environnement, notamment :
- Vos ressources les plus attaquées
- Vos [contrôles de sécurité](secure-score-security-controls.md) qui ont le plus de chance d’augmenter votre degré de sécurisation
- Les suggestions actives avec le plus grand nombre de ressources impactées
- Billets de blog récents publiés par les experts de Microsoft Defender pour le cloud

## <a name="next-steps"></a>Étapes suivantes

Cette page a présenté la page de présentation de Defender pour le cloud. Pour plus d’informations, consultez :

- [Explorez et gérez vos ressources à l’aide des outils d’inventaire et de gestion des ressources](asset-inventory.md)
- [Degré de sécurisation dans Microsoft Defender pour le cloud](secure-score-security-controls.md)
