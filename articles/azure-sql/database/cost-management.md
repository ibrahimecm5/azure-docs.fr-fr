---
title: Prévoir et gérer les coûts
description: Découvrez comment planifier et gérer les coûts pour Azure SQL Database avec l’analyse du coût dans le portail Azure.
author: MashaMSFT
ms.author: mathoma
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.subservice: service-overview
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: 5f1464c6fb74bc04dacb230cd2a5993f7566e82a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546877"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Planifier et gérer les coûts d’Azure SQL Database

Cet article explique comment planifier et gérer les coûts pour Azure SQL Database. 

Tout d’abord, utilisez la calculatrice de tarification Azure pour ajouter des ressources Azure et passez en revue les coûts estimés. Une fois que vous avez commencé à utiliser des ressources Azure SQL Database, utilisez les fonctionnalités de Cost Management pour définir des budgets et superviser les coûts. Vous pouvez également passer en revue les coûts prévus et déterminer les tendances des dépenses pour identifier les domaines où vous pourriez agir. Les coûts pour Azure SQL Database ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts d’Azure SQL Database. Tous les services et ressources Azure utilisés pour votre abonnement Azure, y compris les services tiers, vous sont toutefois facturés.

> [!div class="nextstepaction"]
> [Enquête en vue de l’amélioration d’Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="prerequisites"></a>Prérequis

L’analyse des coûts prend en charge la plupart des types de compte Azure, mais pas tous. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour un compte Azure. 

Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>Considérations relatives au coût initial de SQL Database

Lorsque vous travaillez avec Azure SQL Database, vous devez prendre en compte plusieurs caractéristiques permettant de réaliser des économies :

### <a name="vcore-or-dtu-purchasing-models"></a>Modèles d’achat vCore ou DTU 

Azure SQL Database prend en charge deux modèles d’achat : vCore et DTU. La façon dont vous êtes facturé varie selon les modèles d’achat. Il est donc important de comprendre le modèle qui convient le mieux à votre charge de travail lorsque vous planifiez et considérez les coûts. Pour plus d’informations sur les modèles d’achat vCore et DTU, consultez [Choisir entre les modèles d’achat vCore et DTU](purchasing-models.md).

### <a name="provisioned-or-serverless"></a>Approvisionné ou serverless

Dans le modèle d’achat vCore, Azure SQL Database prend également en charge deux types de niveaux de calcul : débit approvisionné et serverless. La façon dont vous êtes facturé pour chaque niveau de calcul varie. Il est donc important de comprendre ce qui fonctionne le mieux pour votre charge de travail lorsque vous planifiez et considérez les coûts. Pour plus d’informations, consultez [Vue d’ensemble du modèle vCore – Niveaux de calcul](service-tiers-sql-database-vcore.md#compute-tiers).

Dans le niveau de calcul approvisionné du modèle d’achat vCore, vous pouvez échanger vos licences actuelles contre des tarifs réduits. Pour plus d’informations, consultez [Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Pools élastiques

Pour les environnements comportant plusieurs bases de données dont les demandes d’utilisation sont variables et imprévisibles, les pools élastiques peuvent permettre de réaliser des économies par rapport à l’approvisionnement d’une même quantité de singletons. Pour plus d’informations, consultez [Pools élastiques](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Estimation des coûts d’Azure SQL Database

Utilisez la [calculatrice de tarification Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts des différentes configurations d’Azure SQL Database. Pour plus d’informations, consultez la [tarification d’Azure SQL Database](https://azure.microsoft.com/pricing/details/azure-sql-database/). 

Les informations et les prix figurant dans l’image suivante sont donnés à titre d’exemple uniquement : 

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Exemple de calculatrice de tarification Azure SQL Database":::

Vous pouvez également estimer l’incidence des différentes options de stratégie de rétention sur le coût. Les informations et les prix figurant dans l’image suivante sont donnés à titre d’exemple uniquement :

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Exemple de calculatrice de tarification Azure SQL Database pour le stockage":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Comprendre le modèle de facturation complet pour Azure SQL Database

Azure SQL Database s’exécute sur l’infrastructure Azure qui accumule les coûts avec Azure SQL Database lorsque vous déployez la nouvelle ressource. Il est important de comprendre qu’une infrastructure supplémentaire peut accumuler des frais.  

Azure SQL Database (à l’exception du niveau de calcul serverless) est facturé à un taux horaire prévisible. Si la base de données SQL est active pendant moins d’une heure, vous êtes facturé en utilisant le niveau de service le plus élevé qui a été sélectionné et le stockage et les E/S approvisionnés qui sont appliqués pendant cette heure, indépendamment de l’utilisation ou du fait que la base de données était active pendant moins d’une heure.

La facturation dépend de la référence de votre produit, du matériel de génération de votre référence SKU et de la catégorie du compteur. Azure SQL Database a les références SKU possibles suivantes :

- De base (B)
- Standard (S)
- Premium (P)
- Usage général (GP)
- Critique pour l’entreprise (BC)
- Et pour le stockage : stockage géoredondant (GRS), stockage localement redondant (LRS) et stockage redondant interzone (ZRS)
- Il est également possible d’avoir une référence déconseillée figurant parmi les offres de ressources dépréciées

Pour en savoir plus, voir [Niveaux de service](service-tiers-general-purpose-business-critical.md). 

Le tableau suivant montre les compteurs de facturation les plus courants et leur SKU possibles pour des **bases de données uniques** : 

| Mesure| Références SKU possibles | Description | 
| :----|:----|:----|
| Sauvegarde\* | GP/BC/HS | Mesure la consommation de stockage utilisée par les sauvegardes, facturée en fonction de la quantité de stockage utilisée en Go par mois. | 
| Sauvegarde (LTR) | GRS/LRS/ZRS/GF | Mesure la consommation de stockage utilisée par les sauvegardes à long terme configurées via la rétention à long terme, facturée selon la quantité de stockage utilisée. | 
| Compute  | B/S/P/GP/BC  | Mesure la consommation de vos ressources de calcul par heure. | 
| Calcul (réplica principal/nommé) | HS | Mesure la consommation de vos ressources de calcul par heure de votre réplica HS principal. 
| Calcul (réplica à haute disponibilité)             | HS | Mesure la consommation de vos ressources de calcul par heure de votre réplica HS secondaire. | 
| Calcul (module complémentaire ZR)              | GP | Mesure la consommation de vos ressources de calcul par minute de votre réplica ajouté et redondant dans une zone. | 
| Calcul (serverless)             | GP | Mesure la consommation de vos ressources de calcul serverless par minute.  | 
| Licence | GP/BC/HS | La facturation de votre SQL Server licence accumulée par mois. | 
| Stockage | B/S\*/P\*/G/BC/HS | Facturée mensuellement, en fonction de la quantité de données stockées par heure. |

\* Dans le modèle d’achat DTU, un ensemble initial de stockage pour les données et les sauvegardes est fourni sans coût supplémentaire. La taille du stockage dépend du niveau de service sélectionné. Du stockage de données supplémentaire peut être acheté dans les niveaux Standard et Premium. Pour plus d’informations, consultez la [tarification d’Azure SQL Database](https://azure.microsoft.com/pricing/details/azure-sql-database/). 

Le tableau suivant montre les compteurs de facturation les plus courants et leur SKU possibles pour des **pools élastiques** : 

| Mesure| Références SKU possibles | Description | 
|:----|:----|:----|
| Sauvegarde\* | GP/BC  | Mesure la consommation de stockage utilisée par les sauvegardes, facturée par Go par heure sur une base mensuelle. | 
| Compute  | B/S/P/GP/BC | Mesure la consommation de vos ressources de calcul par heure, par exemple vCore et mémoire ou DTU. | 
| Licence | GP/BC | La facturation de votre SQL Server licence accumulée par mois. | 
| Stockage | B/S\*/P\*/GP/HS | Facturée mensuellement, en fonction de la quantité de données stockées sur le disque à l’aide de l’espace de stockage par heure, et du débit en mégaoctets par seconde (Mo/s). |

\* Dans le modèle d’achat DTU, un ensemble initial de stockage pour les données et les sauvegardes est fourni sans coût supplémentaire. La taille du stockage dépend du niveau de service sélectionné. Du stockage de données supplémentaire peut être acheté dans les niveaux Standard et Premium. Pour plus d’informations, consultez la [tarification d’Azure SQL Database](https://azure.microsoft.com/pricing/details/azure-sql-database/). 

### <a name="using-monetary-credit-with-azure-sql-database"></a>Utilisation de Monetary Credit avec Azure SQL Database

Vous pouvez payer les frais Azure SQL Database avec votre crédit Paiement anticipé Azure (anciennement appelé « engagement financier »). Vous ne pouvez cependant pas utiliser le crédit Paiement anticipé Azure pour payer des frais pour des produits et services tiers, notamment ceux de la Place de marché Azure.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Vérifiez l’estimation du coût dans le portail Azure

Au cours du processus de création d’une base de données Azure SQL, vous pouvez voir les coûts estimés lors de la configuration du niveau de calcul. 

Pour accéder à cet écran, sélectionnez **Configurer la base de données** sous l’onglet **Concepts de base** de la page **Créer une base de données SQL**. Les informations et les prix figurant dans l’image suivante sont donnés à titre d’exemple uniquement :

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Exemple d’estimation des coûts dans le portail Azure":::

Si votre abonnement Azure a une limite de dépense, Azure vous empêche de dépenser au-delà que le montant de votre crédit. À mesure que vous créez et utilisez des ressources Azure, vos crédits sont utilisés. Quand vous atteignez votre limite de crédit, les ressources que vous avez déployées sont désactivées pour le reste de cette période de facturation. Vous ne pouvez pas changer votre limite de crédit, mais vous pouvez la supprimer. Pour plus d’informations sur les limites de dépense, consultez [Limite de dépense Azure](../../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Superviser les coûts

Lorsque vous commencez à utiliser Azure SQL Database, vous pouvez voir les coûts estimés dans le portail. Procédez comme suit pour passer en revue les estimations des coûts :

1. Connectez-vous au portail Azure et accédez au groupe de ressources de votre base de données Azure SQL. Vous pouvez localiser le groupe de ressources en accédant à votre base de données et en sélectionnant **Groupe de ressources** dans la section **Vue d’ensemble**.
1. Dans le menu, sélectionnez **Analyse des coûts**.
1. Affichez **Coûts cumulés** et définissez le graphique en bas sous **Nom de service**. Ce graphique montre une estimation de vos coûts actuels pour SQL Database. Pour réduire les coûts de la page entière à Azure SQL Database, sélectionnez **Ajouter un filtre** puis sélectionnez **Azure SQL Database**. Les informations et les prix figurant dans l’image suivante sont donnés à titre d’exemple uniquement :

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Exemple montrant les coûts cumulés pour le portail Azure":::

À partir de là, vous pouvez explorer les coûts par vous-même. Pour plus d’informations sur les différents paramètres d’analyse des coûts, consultez [Démarrer l’analyse des coûts](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Créer des budgets

Vous pouvez créer des [budgets](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. 

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre analyse. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources. Pour plus d’informations sur les options de filtre lorsque vous créez un budget, consultez [Options de regroupement et de filtre](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. Cela est utile lorsque vous devez effectuer une analyse de données supplémentaire sur le coût. Par exemple, une équipe Finance peut analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Autres façons de gérer et de réduire les coûts d’Azure SQL Database

Azure SQL Database vous permet également d’effectuer un scale-up ou un scale-down des ressources pour contrôler les coûts en fonction des besoins de votre application. Pour plus d’informations, consultez [Mettre à l’échelle de façon dynamique les ressources de base de données](scale-resources.md).

Économisez de l’argent en vous engageant à réserver des ressources de calcul pendant un à trois ans. Pour plus d’informations, consultez [Réduire les coûts des ressources grâce à une capacité de réserve](reserved-capacity-overview.md).


## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).