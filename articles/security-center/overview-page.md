---
title: Page « Vue d’ensemble » ou tableau de bord principal de Microsoft Defender pour le cloud
description: Découvrir les fonctionnalités de la page de présentation de Defender pour le cloud
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d0ab115e1e963283a2b8f7e7abd963653b9a39dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084077"
---
# <a name="microsoft-defender-for-clouds-overview-page"></a>Page de présentation de Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Quand vous ouvrez Microsoft Defender pour le cloud, la première page qui s’affiche est la page de présentation. 

Ce tableau de bord interactif fournit une vue unifiée de la posture de sécurité de vos charges de travail cloud hybrides. En outre, elle affiche des alertes de sécurité, des informations de couverture, etc.

Vous pouvez sélectionner n’importe quel élément de la page pour obtenir des informations plus détaillées.

:::image type="content" source="media/overview-page/overview.png" alt-text="Page de présentation de Microsoft Defender pour le cloud":::

## <a name="features-of-the-overview-page"></a>Fonctionnalités de la page de présentation

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Barre supérieure de la page de présentation de Defender pour le cloud":::

La **barre de menus supérieure** offre :
- **Abonnements** : vous pouvez afficher et filtrer la liste des abonnements en sélectionnant ce bouton. Defender pour le cloud ajuste l’affichage pour refléter la posture de sécurité des abonnements sélectionnés.
- **Nouveautés** : ouvre les [notes de publication](release-notes.md) afin que vous puissiez être informé des nouvelles fonctionnalités, des correctifs de bogues et des fonctionnalités déconseillées.
- **Les numéros de haut niveau** pour les comptes cloud connectés, afin d’afficher le contexte des informations dans les mosaïques principales ci-dessous. De même que le nombre de ressources évaluées, de recommandations actives et d’alertes de sécurité. Sélectionnez le nombre des ressources évaluées pour accéder à [Inventaire des ressources](asset-inventory.md). En savoir plus sur la connexion de vos [comptes AWS](quickstart-onboard-aws.md) et vos [projets GCP](quickstart-onboard-gcp.md).


Au centre de la page se trouvent **quatre mosaïques centrales**, chacune d’entre elles étant en lien vers un tableau de bord dédié pour plus d’informations :
- **Degré de sécurisation** : Defender pour le cloud évalue continuellement vos ressources, vos abonnements et votre organisation pour y rechercher d’éventuels problèmes de sécurité. Il agrège ensuite toutes ses découvertes sous la forme d’un score qui vous permet de déterminer d’un coup d’œil votre niveau de sécurité actuel : plus le score est élevé, plus le niveau de risque identifié est faible. [Plus d’informations](secure-score-security-controls.md)
- **Conformité réglementaire** : Defender pour le cloud fournit des insights sur votre posture de conformité à partir d’évaluations continues de votre environnement Azure. Defender pour le cloud analyse les facteurs de risque dans votre environnement cloud hybride conformément aux bonnes pratiques de sécurité. Ces évaluations sont comparées à des contrôles de conformité provenant d’un ensemble de normes prises en charge. [Plus d’informations](regulatory-compliance-dashboard.md)
- **Protections de charge de travail** : il s’agit de la plateforme de protection de charge de travail cloud (CWPP) intégrée à Defender pour le cloud qui offre une protection avancée et intelligente de vos charges de travail exécutées sur Azure, sur des machines locales ou d’autres fournisseurs de cloud. À chaque type de ressource correspond un plan Microsoft Defender. La mosaïque affiche la couverture de vos ressources connectées (pour les abonnements actuellement sélectionnés) et les alertes récentes, avec un code de couleur par gravité. En savoir plus sur [les plans de protection avancée de Microsoft Defender](defender-for-cloud-introduction.md).
- **Firewall Manager** : cette vignette indique l’état de vos hubs et réseaux à partir d’[Azure Firewall Manager](../firewall-manager/overview.md). 


Le volet **Insights** offre des éléments personnalisés pour votre environnement, notamment :
- Vos ressources les plus attaquées
- Vos contrôles de sécurité qui ont le potentiel le plus élevé pour augmenter votre score de sécurisé
- Les suggestions actives avec le plus grand nombre de ressources impactées
- Billets de blog récents publiés par les experts de Microsoft Defender pour le cloud

## <a name="next-steps"></a>Étapes suivantes

Cette page a présenté la page de présentation de Defender pour le cloud. Pour plus d’informations, consultez :

- [Explorez et gérez vos ressources à l’aide des outils d’inventaire et de gestion des ressources](asset-inventory.md)
- [Degré de sécurisation dans Microsoft Defender pour le cloud](secure-score-security-controls.md)
