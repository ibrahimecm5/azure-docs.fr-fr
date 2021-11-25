---
title: Galerie de classeurs dans Microsoft Defender pour le cloud
description: Découvrez comment créer des rapports riches et interactifs de données de Defender pour le cloud avec la galerie de classeurs Azure Monitor intégrée
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 07c4a521764e404b7c55dd6ad4c7d30898e448a6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526408"
---
# <a name="create-rich-interactive-reports-of-defender-for-cloud-data"></a>Créer des rapports interactifs et riches avec les données de Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les [Classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md) fournissent un canevas flexible pour l’analyse des données et la création de rapports visuels riches au sein du portail Azure. Ils vous permettent d’exploiter plusieurs sources de données à travers l’écosystème Azure et de les combiner dans des expériences interactives unifiées.

Les Classeurs fournissent un ensemble riche de fonctionnalités pour la visualisation de vos données Azure. Pour obtenir des exemples détaillés de chaque type de visualisation, consultez les [exemples de visualisations et la documentation](../azure-monitor/visualize/workbooks-text-visualizations.md). 

Dans Defender pour le cloud, vous pouvez accéder aux classeurs intégrés pour suivre la posture de sécurité de votre organisation. Vous pouvez également créer des classeurs personnalisés pour afficher un vaste éventail de données à partir de Defender pour le cloud ou d’autres sources de données prises en charge.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Classeur sur le degré de sécurisation dans le temps.":::

## <a name="availability"></a>Disponibilité

| Aspect                          | Détails                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| État de sortie :                  | Disponibilité générale                                                                                                                    |
| Prix :                        | Gratuit                                                                                                                                         |
| Rôles et autorisations obligatoires : | Pour enregistrer des classeurs, vous devez disposer au minimum d’autorisations de [Contributeur de classeur](../role-based-access-control/built-in-roles.md#workbook-contributor) sur le groupe de ressources cible |
| Clouds :                         | :::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-microsoft-defender-for-cloud"></a>Galerie de classeurs dans Microsoft Defender pour le cloud

Avec la fonctionnalité Classeurs Azure intégrée, Microsoft Defender pour le cloud facilite la création de vos propres classeurs interactifs personnalisés. Defender pour le cloud inclut également une bibliothèque avec les classeurs suivants prêts pour personnalisation :

- [Classeur « Degré de sécurisation dans le temps »](#use-the-secure-score-over-time-workbook) : suivez les scores de vos abonnements et les modifications apportées aux recommandations pour vos ressources
- [Classeur « Mises à jour du système »](#use-the-system-updates-workbook) : visualisez les mises à jour système manquantes par ressources, système d’exploitation, gravité, etc.
- [Classeur « Résultats de l’évaluation des vulnérabilités »](#use-the-vulnerability-assessment-findings-workbook) : visualisez les découvertes des analyses de vulnérabilités de vos ressources Azure
- [Classeur « conformité dans le temps »](#use-the-compliance-over-time-workbook) : afficher l’état de la conformité d’un abonnement avec les normes réglementaires ou industrielles sélectionnées 

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Galerie de classeurs intégrés dans Microsoft Defender pour le cloud.":::

Choisissez l’un des classeurs fournis ou créez le vôtre.

> [!TIP]
> Utilisez le bouton **Modifier** pour personnaliser les classeurs fournis à votre convenance. Lorsque vous avez fini d’apporter des modifications, sélectionnez **Enregistrer** pour enregistrer vos modifications dans un nouveau classeur.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="Modification des classeurs fournis pour les personnaliser en fonction de vos besoins particuliers.":::

### <a name="use-the-secure-score-over-time-workbook"></a>Utiliser le classeur « Degré de sécurisation dans le temps »

Ce classeur utilise les données de degré de sécurisation de votre espace de travail Log Analytics. Ces données doivent être exportées à partir de l’outil d’exportation continue, comme décrit dans [Configurer l’exportation continue à partir des pages de Defender pour le cloud dans le portail Azure](continuous-export.md?tabs=azure-portal).

Quand vous configurez l’exportation continue, définissez la fréquence d’exportation sur **diffusion en continu des mises à jour** et **captures instantanées**.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="Pour le classeur de degré de sécurisation dans le temps, vous devez sélectionner les deux options dans les paramètres de fréquence d’exportation de votre configuration d’exportation continue.":::

> [!NOTE]
> Les captures instantanées étant exportées chaque semaine, vous devez attendre au moins une semaine que la première capture instantanée soit exportée avant de pouvoir consulter les données de ce classeur.

> [!TIP]
> Pour configurer l’exportation continue au sein de votre organisation, utilisez les stratégies « DeployIfNotExist » d’Azure Policy fournies décrites dans [Configurer l’exportation continue à grande échelle](continuous-export.md?tabs=azure-policy).

Le classeur du degré de sécurisation dans le temps comporte cinq graphiques pour la création de rapports sur les abonnements pour les espaces de travail sélectionnés :

|Graph  |Exemple  |
|---------|---------|
|**Évaluer les tendances de la dernière semaine et du mois**<br>Utilisez cette section pour surveiller le score actuel et les tendances générales des scores de vos abonnements.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Tendances du degré de sécurisation sur le classeur intégré.":::|
|**Score agrégé pour tous les abonnements sélectionnés**<br>Pointez votre souris sur un point quelconque de la courbe de tendance afin de voir le score agrégé à n’importe quelle date dans l’intervalle de temps sélectionné.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Score agrégé pour tous les abonnements sélectionnés":::.|
|**Recommandations avec le plus de ressources non saines**<br>Ce tableau vous aide à trier les recommandations qui comptent le plus de ressources devenues non saines au cours de la période sélectionnée.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Recommandations avec le plus de ressources non saines":::.|
|**Scores pour des contrôles de sécurité spécifiques**<br>Les contrôles de sécurité de Defender pour le cloud sont des regroupements logiques de recommandations. Ce graphique vous permet de voir en un clin d’œil les scores hebdomadaires de tous vos contrôles.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Scores pour vos contrôles de sécurité sur la période sélectionnée.":::|
|**Modifications de ressources**<br>Les recommandations comptant le plus de ressources ayant changé d’état (sain, non sain ou non applicable) pendant la période sélectionnée sont répertoriées ici. Sélectionnez une recommandation dans la liste pour ouvrir une nouvelle table répertoriant les ressources spécifiques.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Recommandations comptant le plus de ressources ayant changé d’état d’intégrité.":::|
|||

### <a name="use-the-system-updates-workbook"></a>Utiliser le classeur « Mises à jour système »

Ce classeur est basé sur la recommandation de sécurité « Les mises à jour système doivent être installées sur vos machines ».

Le classeur vous aide à identifier les machines avec des mises à jour en suspens.

Vous pouvez afficher la situation des abonnements sélectionnés en fonction des éléments suivants :

- Liste des ressources avec des mises à jour en suspens
- Liste des mises à jour manquantes dans vos ressources

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Classeur sur les mises à jour système de Defender pour le cloud, basé sur la recommandation de sécurité des mises à jour manquantes":::

### <a name="use-the-vulnerability-assessment-findings-workbook"></a>Utiliser le classeur « Résultats de l’évaluation des vulnérabilités »

Defender pour le cloud inclut des scanneurs de vulnérabilité pour vos machines, des conteneurs dans des registres de conteneurs et des serveurs SQL.

Apprenez-en davantage sur l’utilisation de ces scanneurs :

- [Rechercher des vulnérabilités avec la Gestion des menaces et des vulnérabilités Microsoft](deploy-vulnerability-assessment-tvm.md)
- [Rechercher des vulnérabilités avec le scanneur Qualys intégré](deploy-vulnerability-assessment-vm.md)
- [Analyser les images de votre registre à la recherche de vulnérabilités](defender-for-container-registries-usage.md)
- [Analyser vos ressources SQL à la recherche de vulnérabilités](defender-for-sql-on-machines-vulnerability-assessment.md)

Les résultats de chacun de ces types de ressource sont rapportés dans des recommandations séparées :

- [Les vulnérabilités de vos machines virtuelles doivent être corrigées](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1195afff-c881-495e-9bc5-1486211ae03f) (notamment les résultats de la Gestion des menaces et des vulnérabilités Microsoft, le scanneur Qualys intégré et toutes les [solutions BYOL VA](deploy-vulnerability-assessment-byol-vm.md) configurées)
- [Les vulnérabilités dans les images Azure Container Registry doivent être corrigées (avec Qualys)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/dbd0cb49-b563-45e7-9724-889e799fa648)
- [Les résultats des vulnérabilités des bases de données SQL doivent être résolus](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/82e20e14-edc5-4373-bfc4-f13121257c37)
- [Les résultats des vulnérabilités des serveurs SQL Server sur les machines doivent être résolus](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/f97aa83c-9b63-4f9a-99f6-b22c4398f936)

Ce classeur rassemble ces résultats et les organise par gravité, type de ressource et catégorie.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Rapport des résultats de l’évaluation des vulnérabilités de Defender pour le cloud.":::


### <a name="use-the-compliance-over-time-workbook"></a>Utiliser le classeur « conformité dans le temps »

Microsoft Defender pour le cloud compare continuellement la configuration de vos ressources aux exigences des normes, réglementations et tests d’évaluation du secteur. Les normes intégrées incluent NIST SP 800-53, CSCF CSP SWIFT v2020, Canada Federal PBMM, HIPAA HITRUST, etc. Vous pouvez sélectionner les normes spécifiques pertinentes pour votre organisation à l’aide du tableau de bord de conformité réglementaire. En savoir plus sur [Personnaliser l’ensemble de normes du tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).

Ce classeur suit votre état de conformité dans le temps aux différentes normes que vous avez ajoutées à votre tableau de bord.

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-select-standards.png" alt-text="Sélectionnez les normes de votre rapport de conformité dans le temps.":::

Lorsque vous sélectionnez une norme dans la zone vue d’ensemble du rapport, le volet inférieur révèle une répartition plus détaillée :

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="Répartition détaillée des modifications concernant une norme spécifique.":::

Vous pouvez continuer l’exploration vers le niveau supérieur jusqu’au niveau de recommandation pour afficher les ressources qui ont réussi ou échoué pour chaque contrôle. 

> [!TIP]
> Pour chaque panneau du rapport, vous pouvez exporter les données vers Excel à l’aide de l’option « Exporter vers Excel ».
>
> :::image type="content" source="media/custom-dashboards-azure-workbooks/export-workbook-data.png" alt-text="Exportation des données de classeur de conformité vers Excel.":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Importer des classeurs à partir d’autres galeries de classeurs

Si vous avez créé des classeurs dans d’autres services Azure et souhaitez les déplacer vers la galerie de classeurs de Microsoft Defender pour le cloud :

1. Ouvrez le classeur cible.

1. Dans la barre d’outils, sélectionnez **Modifier**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Modification d’un classeur Azure Monitor.":::

1. À partir de la barre d’outils, sélectionnez **</>** pour accéder à l’Éditeur avancé.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="Lancement de l’éditeur avancé pour obtenir le code JSON du modèle de galerie.":::

1. Copiez le code JSON du modèle de galerie du classeur.

1. Ouvrez la galerie de classeurs dans Defender pour le cloud, puis dans la barre de menus, sélectionnez **Nouveau**.
1. Sélectionnez **</>** pour accéder à l’Éditeur avancé.
1. Collez l’intégralité du code JSON du modèle de galerie.
1. Sélectionnez **Appliquer**.
1. Dans la barre d’outils, sélectionnez **Enregistrer sous**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Enregistrement du classeur dans la galerie de Defender pour le cloud.":::

1. Entrez les informations requises pour l’enregistrement du classeur :
   1. nom du classeur ;
   2. région souhaitée ;
   3. abonnement, groupe de ressources et partage appropriés.

Vous trouverez votre classeur enregistré dans la catégorie **Classeurs récemment modifiés** .


## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit la page Classeurs Azure Monitor intégrée de Defender pour le cloud avec des rapports intégrés et l’option permettant de créer vos propres rapports personnalisés et interactifs.

- En savoir plus sur les [Classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md)
- Les classeurs intégrés tirent (pull) leurs données des recommandations de Defender pour le cloud. Apprenez-en davantage sur les nombreuses recommandations de sécurité dans [Recommandations de sécurité – Guide de référence](recommendations-reference.md)
