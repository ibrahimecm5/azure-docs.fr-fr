---
title: Planification de la gestion des coûts d’Azure Data Factory
description: Découvrez comment prévoir et gérer les coûts d’Azure Data Factory avec l’analyse des coûts sur le Portail Azure.
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.subservice: pricing
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 11/01/2021
ms.openlocfilehash: 51f71bcf39f0b54bf0f7fb389fbbb9797fbb2151
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469461"
---
# <a name="plan-to-manage-costs-for-azure-data-factory"></a>Planification de la gestion des coûts d’Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment prévoir et gérer les coûts d’Azure Data Factory. 

Au début du projet ETL, vous commencez par utiliser une combinaison de la tarification Azure et de la consommation par pipeline. Vous vous servez également de calculatrices de prix pour prévoir et estimer les coûts d’Azure Data Factory avant d’ajouter des ressources pour le service. Ensuite, lorsque vous ajoutez les ressources Azure, passez en revue les coûts estimés. Une fois que vous avez commencé à utiliser des ressources Azure Data Factory, exploitez les fonctionnalités de Cost Management pour définir des budgets et superviser les coûts. Vous pouvez également passer en revue les coûts prévus et les tendances des dépenses pour identifier les domaines où il serait pertinent d’agir. Les coûts liés à Azure Data Factory ne représentent qu’une partie des coûts mensuels qui apparaissent sur votre facture Azure. Cet article explique comment prévoir et gérer les coûts de Data Factory. Cependant, tous les services et toutes les ressources Azure utilisés dans votre abonnement Azure, y compris les services tiers, vous sont facturés.

## <a name="prerequisites"></a>Prérequis

Analyse des coûts dans Cost Management prend en charge la plupart des types de compte Azure, mais pas tous. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour un compte Azure. Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-data-factory"></a>Estimation des coûts avant d’utiliser Azure Data Factory
 
Utilisez la [calculatrice de prix ADF](https://azure.microsoft.com/pricing/calculator/?service=data-factory) pour estimer le coût d’exécution de votre charge de travail ETL dans Azure Data Factory.  Pour utiliser la calculatrice, vous devez entrer des détails, tels que le nombre d’exécutions d’activité, le nombre d’heures d’unités d’intégration de données, le type de calcul utilisé pour Data Flow, le nombre de cœurs, le nombre d’instances, la durée d’exécution, etc.

L’une des questions les plus fréquemment posées sur la calculatrice de prix se rapporte aux valeurs à utiliser comme entrées.  Pendant la phase de preuve de concept, vous pouvez effectuer des exécutions d’évaluation à l’aide d’exemples de jeux de données pour comprendre la consommation de divers compteurs Azure Data Factory.  Ensuite, en fonction de la consommation de l’exemple de jeu de données, vous pouvez prévoir la consommation du jeu de données complet et la planification de mise en œuvre.

> [!NOTE]
> Les prix mentionnés dans les exemples ci-dessous sont hypothétiques et ne sont pas destinés à indiquer la tarification réelle.

Supposons, par exemple, que vous deviez déplacer quotidiennement 1 To de données, de AWS S3 vers Azure Data Lake Gen2.  Vous pouvez effectuer la preuve de concept du déplacement de 100 Go de données pour mesurer le débit d’ingestion de données, et comprendre la consommation de facturation correspondante.

Voici un exemple d’exécution de l’activité de copie (votre consommation réelle variera en fonction de la forme de votre jeu de données spécifique, des vitesses de réseau, des limites de sortie sur le compte S3, des limites d’entrée sur ADLS Gen2, entre autres facteurs).

:::image type="content" source="media/plan-manage-costs/s3-copy-run-details.png" alt-text="Exécution de la copie de S3":::

En tirant parti de la [supervision de la consommation au niveau de l’exécution du pipeline](#monitor-consumption-at-pipeline-run-level), vous pouvez voir les quantités de consommation des compteurs de déplacement de données correspondantes :

:::image type="content" source="media/plan-manage-costs/s3-copy-pipeline-consumption.png" alt-text="Consommation du pipeline de copie de S3":::

Par conséquent, le nombre total d’heures d’unités d’intégration de données (DIU) nécessaires au déplacement de 1 To par jour, et pour le mois entier, est le suivant :

1,2667 (heures DIU) * (1 To / 100 Go) * 30 (jours dans un mois) = 380 heures DIU

Vous pouvez maintenant intégrer 30 exécutions d’activité et 380 heures DIU dans la calculatrice de prix Azure Data Factory pour connaître l’estimation de votre facture mensuelle :

:::image type="content" source="media/plan-manage-costs/s3-copy-pricing-calculator.png" alt-text="Calculatrice de prix de la copie de S3":::

## <a name="understand-the-full-billing-model-for-azure-data-factory"></a>Présentation du modèle de facturation complet pour Azure Data Factory

Azure Data Factory s’exécute sur l’infrastructure Azure, qui génère des frais lorsque vous déployez de nouvelles ressources. Il est important de comprendre qu’il peut y avoir d’autres coûts d’infrastructure supplémentaires susceptibles de s’accumuler.

### <a name="how-youre-charged-for-azure-data-factory"></a>Modèle de facturation d’Azure Data Factory

Azure Data Factory est un service d’intégration de données élastique et serverless, conçu à l’échelle du cloud.  Ainsi, il n’existe aucun calcul de taille fixe à planifier pour la charge maximale ; vous spécifiez plutôt la quantité de ressources à allouer sur demande par opération, ce qui vous permet de concevoir les processus ETL d’une manière beaucoup plus scalable. De plus, Azure Data Factory est facturé sur la base d’un plan de consommation, ce qui signifie que vous payez uniquement ce que vous utilisez.

Lorsque vous créez ou utilisez des ressources Azure Data Factory, les compteurs suivants peuvent vous être facturés :

- Exécutions d’activités d’orchestration : sur la base du nombre d’orchestrations d’exécutions d’activités.
- Heures d’unités d’intégration de données : sur la base du nombre d’unités utilisées et de la durée d’exécution, pour les exécutions d’activités de copie sur Azure Integration Runtime.
- Heures de vCore : sur la base du type de calcul, du nombre de vCore et de la durée d’exécution, pour l’exécution et le débogage de flux de données.


À la fin de votre cycle de facturation, les frais associés à chaque compteur sont additionnés. Votre facture contient une section pour tous les coûts d’Azure Data Factory. Chaque compteur est représenté par un élément de ligne distinct.

### <a name="other-costs-that-might-accrue-with-azure-data-factory"></a>Autres coûts possibles avec Azure Data Factory

Lorsque vous créez des ressources pour Azure Data Factory (ADF), des ressources pour d’autres services Azure sont également créées. Notamment :

- Exécution de l’activité de pipeline
- Exécution de l’activité de pipeline externe
- Création, modification, récupération et analyse des artefacts Data Factory
- Durée SSIS Integration Runtime (IR) en fonction du type d’instance et de la durée

> [!NOTE]
> Vous pouvez affecter la même balise à votre ADF et à d’autres ressources Azure, en les plaçant dans la même catégorie pour afficher leur facturation consolidée. La balise ADF sera héritée par tous les IR SSIS. Si vous modifiez la balise ADF, vous devez arrêter et redémarrer tous les IR SSIS pour qu’elles héritent de la nouvelle balise, consultez la section [Reconfigurer IR SSIS](manage-azure-ssis-integration-runtime.md#to-reconfigure-an-azure-ssis-ir).

### <a name="using-azure-prepayment-with-azure-data-factory"></a>Paiement anticipé Azure avec Azure Data Factory

Vous pouvez payer les frais Azure Data Factory avec votre crédit Paiement anticipé Azure. Vous ne pouvez cependant pas utiliser le crédit Paiement anticipé Azure pour payer des frais pour des produits et services tiers, y compris ceux de la Place de marché Azure.

## <a name="monitor-costs"></a>Superviser les coûts

Les coûts d’Azure Data Factory peuvent être surveillés au niveau de la fabrique, de l’exécution de pipeline et de l’exécution d’activité.

### <a name="monitor-costs-at-factory-level"></a>Superviser les coûts au niveau de la fabrique

À mesure que vous utilisez des ressources Azure avec Data Factory, vous générez des coûts. Les coûts unitaires d’utilisation des ressources Azure varient selon les intervalles de temps (secondes, minutes, heures et jours) et selon l’utilisation d’unités (octets, mégaoctets, etc.). Dès que l’utilisation de Data Factory démarre, des coûts sont générés, que vous pouvez voir dans [Analyse des coûts](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Quand vous utilisez l’analyse des coûts, vous visualisez les coûts de Data Factory dans des graphes et des tableaux pour différents intervalles de temps. Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Vous pouvez aussi afficher les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses. Ceci vous permet de voir où des dépassements ont pu se produire. Si vous avez créé des budgets, vous pouvez aussi facilement voir à quel moment ils ont été dépassés.

Pour consulter les coûts de Data Factory dans l’analyse des coûts, procédez comme suit :

1. Connectez-vous au portail Azure.
2. Ouvrez l’étendue dans le portail Azure et sélectionnez **Analyse des coûts** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Analyse du coût** dans le menu. Sélectionnez **Étendue** pour passer à une autre étendue dans l’analyse des coûts.
3. Par défaut, le coût des services est affiché dans le premier graphique en anneau. Dans le graphe, sélectionnez la zone intitulée Azure Data Factory v2.

Les coûts mensuels réels s’affichent lors de l’ouverture initiale de l’analyse des coûts. Voici un exemple qui montre tous les coûts d’utilisation mensuelle.

:::image type="content" source="media/all-costs.png" alt-text="Exemple montrant les coûts cumulés pour un abonnement":::

- Pour limiter les coûts à un seul service, comme Data Factory, sélectionnez **Ajouter un filtre**, puis **Nom du service**. Ensuite, sélectionnez **Azure Data Factory v2**.

Voici un exemple montrant les coûts de Data Factory uniquement.

:::image type="content" source="media/service-specific-cost.png" alt-text="Exemple montrant les coûts cumulés pour Nom du service":::

Dans l’exemple précédent, vous avez pu voir le coût actuel du service. Les coûts par région Azure (localisation) et les coûts de Data Factory par groupe de ressources sont également présentés. À partir de là, vous pouvez explorer les coûts par vous-même.

### <a name="monitor-consumption-at-pipeline-run-level"></a>Superviser la consommation au niveau de l’exécution du pipeline

En fonction des types d’activités que vous développez dans votre pipeline, de la quantité de données déplacées et transformées, et de la complexité de la transformation, l’exécution d’un pipeline fera tourner différents compteurs de facturation dans Azure Data Factory.

Vous pouvez afficher la quantité de consommation de différents compteurs pour des exécutions de pipeline individuels dans l’expérience utilisateur Azure Data Factory. Pour ouvrir l’expérience de surveillance, sélectionnez la vignette **Surveiller et gérer** dans le panneau de fabrique de données du [Portail Azure](https://portal.azure.com/). Si vous êtes déjà dans l’expérience utilisateur ADF, cliquez sur l’icône **Surveiller** dans la barre latérale gauche. Une liste d’exécutions de pipeline constitue l’affichage de la supervision par défaut.

En cliquant sur le bouton **Consommation** situé en regard du nom du pipeline, vous affichez une fenêtre contextuelle ; celle-ci vous renseigne sur la consommation de l’exécution de votre pipeline en regroupant toutes les activités au sein du pipeline.

:::image type="content" source="media/plan-manage-costs/pipeline-run-consumption.png" alt-text="Consommation de l’exécution du pipeline":::

:::image type="content" source="media/plan-manage-costs/pipeline-consumption-details.png" alt-text="Détails de la consommation du pipeline":::

L’affichage de la consommation se rapportant à l’exécution du pipeline indique la quantité consommée sur chaque compteur Azure Data Factory de l’exécution du pipeline spécifique, mais il n’affiche pas le prix réel facturé, car le montant qui vous est facturé dépend du type de compte Azure dont vous disposez, et du type de devise utilisé.  Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md).

### <a name="monitor-consumption-at-activity-run-level"></a>Superviser la consommation au niveau de l’exécution de l’activité
Une fois la consommation agrégée au niveau de l’exécution du pipeline comprise, il reste des scénarios dans lesquels vous devez approfondir l’exploration et identifier l’activité la plus coûteuse au sein du pipeline.

Pour consulter la consommation au niveau de l’exécution de l’activité, accédez à l’IU **Créer et surveiller** de votre fabrique de données. À partir de l’onglet **Superviser**, dans lequel vous affichez la liste des exécutions de pipeline, cliquez sur le lien du **nom du pipeline** pour accéder à la liste des exécutions d’activités dans l’exécution de pipeline.  Cliquez sur le bouton **Sortie** en regard du nom de l’activité et recherchez la propriété **billableDuration** dans la sortie JSON :

Voici un exemple extrait d’une exécution de l’activité de copie :

:::image type="content" source="media/plan-manage-costs/copy-output.png" alt-text="Sortie de la copie":::

Et voici un exemple extrait d’une exécution de l’activité de mappage de Data Flow :

:::image type="content" source="media/plan-manage-costs/dataflow-output.png" alt-text="Sortie de Data Flow":::

## <a name="create-budgets"></a>Créer des budgets

Vous pouvez créer des [budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. 

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre analyse. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre disponibles lorsque vous créez un budget, consultez [Options de regroupement et de filtre](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer des analyses supplémentaires des données concernant les coûts. Par exemple, une équipe Finance peut analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- [Page de tarification d’Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Comprendre Azure Data Factory par des exemples](./pricing-concepts.md)
- [Calculatrice de prix d’Azure Data Factory](https://azure.microsoft.com/pricing/calculator/?service=data-factory)
