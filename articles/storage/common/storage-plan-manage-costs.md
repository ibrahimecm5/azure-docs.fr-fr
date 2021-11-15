---
title: Planifier et gérer les coûts du Stockage Blob Azure
description: Découvrez comment planifier et gérer les coûts du Stockage Blob Azure avec l’analyse du coût dans le Portail Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 8cb7f667f2898d1508df2f58a3aea2ced68582e1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474796"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Planifier et gérer les coûts du Stockage Blob Azure

Cet article explique comment planifier et gérer les coûts du Stockage Blob Azure. Tout d’abord, utilisez la calculatrice de prix Azure pour estimer les coûts. Après avoir créé votre compte de stockage, optimisez le compte afin de payer uniquement ce dont vous avez besoin. Utilisez les fonctionnalités de gestion des coûts pour définir des budgets et surveiller les coûts. Vous pouvez également passer en revue les coûts prévus et surveiller les tendances des dépenses pour identifier les domaines où vous pourriez agir.

Gardez à l’esprit que les coûts du Stockage Blob ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment estimer et gérer les coûts du Stockage Blob. Cependant, vous êtes facturé pour tous les services et ressources Azure utilisés pour votre abonnement Azure, y compris les services tiers. Quand vous êtes familiarisé avec la gestion des coûts du Stockage Blob, vous pouvez appliquer des méthodes similaires pour gérer les coûts de tous les services Azure utilisés dans votre abonnement.

## <a name="estimate-costs"></a>Estimer les coûts

Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts avant de créer un compte de stockage Azure et de commencer à transférer des données vers ce compte.

1. Sur la page [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/), choisissez la vignette **Comptes de stockage**.

2. Faites défiler la page vers le bas et recherchez la section **Comptes de stockage** de votre estimation.

3. Choisissez les options souhaitées dans les listes déroulantes.

   L’estimation du coût est mise à jour à mesure que vous modifiez les valeurs de ces listes déroulantes. Cette estimation s’affiche dans le coin supérieur et en bas de l’écran d’estimation.

   ![Capture d’écran illustrant votre estimation](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Quand vous modifiez la valeur de la liste déroulante **Type**, d’autres options de cette feuille de calcul changent également. Utilisez les liens de la section **Plus d’informations** pour en savoir plus sur la fonction de chaque option et sur la façon dont ces options affectent le prix des opérations liées au stockage.

4. Modifiez les options restantes pour voir leur impact sur votre estimation.

## <a name="understand-the-full-billing-model-for-azure-blob-storage"></a>Comprendre le modèle de facturation complet pour le Stockage Blob Azure

Le Stockage Blob Azure s’exécute sur l’infrastructure Azure, qui génère des frais lorsque vous déployez de nouvelles ressources. Il est important de comprendre qu’il peut y avoir d’autres coûts d’infrastructure supplémentaires susceptibles de s’accumuler.

### <a name="how-youre-charged-for-azure-blob-storage"></a>Modèle de facturation du Stockage Blob Azure

lorsque vous créez ou utilisez des ressources de Stockage Blob, vous êtes facturé pour les compteurs suivants :

| Compteur | Unité |
|---|---|
| Stockage des données | par Go / par mois|
| Operations | par transaction |
| Transfert de données | Par Go |
| Métadonnées | par Go / par mois<sup>1 |
| Étiquettes d’index d’objet blob | Par étiquette<sup>2  |
| Modifier le flux | Par modification consignée<sup>2 |
| Étendues de chiffrement | Par mois<sup>2 |
| Accélération des requêtes | Par Go numérisé et par Go retourné |

<sup>1</sup> s’applique uniquement aux comptes qui ont un espace de noms hiérarchique.<br />
<sup>2</sup> s’applique uniquement si vous activez la fonctionnalité.<br />

Le trafic de données peut également entraîner des coûts de mise en réseau. Consultez [Tarification de la bande passante](https://azure.microsoft.com/pricing/details/data-transfers/).

À la fin de votre cycle de facturation, les frais associés à chaque compteur sont additionnés. Votre facture contient une section pour tous les coûts de Stockage Blob Azure. Chaque compteur est représenté par un élément de ligne distinct.

Le stockage de données et les métadonnées sont facturés par Go sur une base mensuelle. Pour les données et métadonnées stockées pendant moins d’un mois, vous pouvez estimer l’impact sur votre facture mensuelle en calculant le coût de chaque Go par jour. Vous pouvez utiliser une approche similaire pour estimer le coût des étendues de chiffrement en cours d’utilisation pendant moins d’un mois. Le nombre de jours d’un mois donné varie. Par conséquent, pour obtenir la meilleure approximation de vos coûts pour un mois donné, veillez à diviser le coût mensuel par le nombre de jours qui se produisent dans le mois en question.

### <a name="finding-the-unit-price-for-each-meter"></a>Recherche du prix unitaire pour chaque compteur

Pour trouver des prix unitaires, ouvrez la page de tarification appropriée. Si vous avez activé la fonctionnalité d’espace de noms hiérarchique sur votre compte, consultez la page [Tarification Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/). Si vous n’avez pas activé cette fonctionnalité, consultez la page [Tarification de l’objet blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/).

Sur la page de tarification, appliquez les filtres de redondance, de région et de devise appropriés. Les prix de chaque compteur s’affichent dans une table. Les prix varient en fonction des autres paramètres de votre compte, tels que les options de redondance des données, le niveau d’accès et le niveau de performance.

### <a name="flat-namespace-accounts-and-transaction-pricing"></a>Comptes d’espace de noms plat et tarification des transactions

Les clients peuvent effectuer une requête en utilisant soit le point de terminaison de Stockage Blob soit le point de terminaison de Data Lake Storage de votre compte. Pour plus d’informations sur les points de terminaison du compte de stockage, consultez [Points de terminaison du compte de stockage](storage-account-overview.md#storage-account-endpoints).

Les prix des transactions qui s’affichent dans la page [Tarification des objets blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/) s’appliquent uniquement aux requêtes qui utilisent le point de terminaison du Stockage Blob (par exemple : `https://<storage-account>.blob.core.windows.net`). Les prix indiqués ne s’appliquent pas aux requêtes qui utilisent le point de terminaison Data Lake Storage Gen2 (par exemple : `https://<storage-account>.dfs.core.windows.net`). Pour obtenir le prix de transaction de ces requêtes, ouvrez la page [Tarification Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/) et sélectionnez l’option **Espace de noms plat**.

> [!div class="mx-imgBorder"]
> ![option espace de noms plat](media/storage-plan-manage-costs/select-flat-namespace.png)

Les requêtes adressées au point de terminaison Data Lake Storage Gen2 peuvent provenir de l’une des sources suivantes :

- Les charges de travail qui utilisent le pilote Azure Blob File System ou [pilote ABFS](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html).

- REST appelle cette utilisation l’[API REST Azure Data Lake Store](/rest/api/storageservices/data-lake-storage-gen2)

- Les applications qui utilisent des API Data Lake Storage Gen2 à partir d’une bibliothèque cliente de stockage Azure.

### <a name="using-azure-prepayment-with-azure-blob-storage"></a>Utilisation de prépaiement Azure avec le Stockage Blob Azure

Vous pouvez payer les frais de Stockage Blob Azure avec votre crédit de prépaiement Azure (anciennement appelé « engagement financier »). Vous ne pouvez cependant pas utiliser le crédit Paiement anticipé Azure pour payer des frais pour des produits et services tiers, notamment ceux de Place de marché Azure.

## <a name="optimize-costs"></a>Optimiser les coûts

Envisagez d’utiliser ces options pour réduire les coûts.

- Réserver de la capacité de stockage

- Organiser les données en niveaux d’accès

- Déplacer automatiquement les données entre les niveaux d’accès

Cette section décrit chaque option plus en détail.

#### <a name="reserve-storage-capacity"></a>Réserver de la capacité de stockage

Vous pouvez réaliser des économies sur les coûts de stockage pour les données blob grâce à une capacité de réserve Stockage Azure. La capacité de réserve Stockage Azure offre une remise sur la capacité pour les objets blob de blocs et les données Azure Data Lake Storage Gen2 dans les comptes de stockage standard lorsque vous vous engagez à une réservation pour un an ou trois ans. Une réservation offre une quantité fixe de capacité de stockage pour la durée de la réservation. La capacité de réserve Stockage Azure peut réduire de manière significative les coûts de capacité pour les objets blob de blocs et les données Azure Data Lake Storage Gen2.

Pour en savoir plus, consultez [Optimiser les coûts de Stockage Blob avec une capacité réservée](../blobs/storage-blob-reserved-capacity.md).

#### <a name="organize-data-into-access-tiers"></a>Organiser les données en niveaux d’accès

Vous pouvez réduire les coûts en plaçant les données blob dans les niveaux d’accès les plus rentables. Choisissez parmi trois niveaux conçus pour optimiser vos coûts d’utilisation des données. Par exemple, le niveau d’accès *chaud* présente un coût de stockage plus élevé, mais un coût d’accès inférieur. Par conséquent, si vous envisagez d’accéder fréquemment aux données, le niveau d’accès chaud peut être le choix le plus rentable. Si vous prévoyez d’accéder moins fréquemment aux données, le niveau d’accès *froid* ou *archive* pourrait être le mieux adapté, car il augmente le coût d’accès aux données tout en réduisant le coût de stockage des données.

Pour en savoir plus, consultez [Niveaux d’accès Chaud, Froid et Archive pour les données d’objet blob](../blobs/access-tiers-overview.md?tabs=azure-portal).

#### <a name="automatically-move-data-between-access-tiers"></a>Déplacer automatiquement les données entre les niveaux d’accès

Utilisez les stratégies de gestion du cycle de vie pour déplacer périodiquement les données entre les niveaux afin d’effectuer le plus d’économies possible. Ces stratégies peuvent déplacer des données vers à l’aide des règles que vous indiquez. Par exemple, vous pouvez créer une règle qui déplace les objets blob vers le niveau Archive si cet objet blob n’a pas été modifié dans les 90 jours. En créant des stratégies qui ajustent le niveau d’accès de vos données, vous pouvez concevoir les options de stockage les moins onéreuses pour vos besoins.

Pour plus d’informations, consultez [Gérer le cycle de vie du Stockage Blob Azure](../blobs/lifecycle-management-overview.md?tabs=azure-portal)

## <a name="create-budgets"></a>Créer des budgets

Vous pouvez créer des [budgets](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts, et des alertes permettant d’avertir automatiquement les parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. Ils peuvent cependant avoir des fonctionnalités limitées quant à la gestion des coûts de services Azure individuels comme le coût du stockage Azure. En effet, ils sont conçus pour suivre les coûts à un niveau supérieur.

## <a name="monitor-costs"></a>Superviser les coûts

À mesure que vous utilisez des ressources Azure avec le stockage Azure, vous générez des coûts. Les coûts unitaires d’utilisation des ressources varient selon les intervalles de temps (secondes, minutes, heures et jours) ou selon l’utilisation d’unités (octets, mégaoctets, etc.). Des coûts sont générés dès que vous commencez à utiliser le stockage Azure. Vous pouvez voir les coûts dans le volet [Analyse du coût](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) du portail Azure.

Quand vous utilisez l’analyse du coût, vous pouvez voir les coûts du stockage Azure dans des graphes et des tableaux pour différents intervalles de temps. Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Vous pouvez également voir les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses et déterminer où des dépassements ont pu se produire. Par ailleurs, si vous avez créé des budgets, vous pouvez facilement voir à quel moment ils ont été dépassés.

> [!NOTE]
> L’analyse des coûts prend en charge différents types de comptes Azure. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour votre compte Azure. Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Pour voir les coûts du stockage Azure dans l’analyse du coût :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Ouvrez la fenêtre **Gestion des coûts + Facturation**, sélectionnez **Gestion des coûts** dans le menu, puis sélectionnez **Analyse des coûts**. Vous pouvez ensuite changer l’étendue pour un abonnement spécifique dans la liste déroulante **Étendue**.

   ![Capture d’écran illustrant la portée](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Pour voir uniquement les coûts du stockage Azure, sélectionnez **Ajouter un filtre**, puis sélectionnez **Nom du service**. Ensuite, choisissez **stockage** dans la liste.

   Voici un exemple montrant les coûts du stockage Azure uniquement :

   ![Capture d’écran illustrant le filtre par stockage](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

Dans l’exemple précédent, vous avez pu voir le coût actuel du service. Les coûts par région Azure (emplacements) et par groupe de ressources sont également présentés. Vous pouvez également ajouter d’autres filtres (par exemple : un filtre pour afficher les coûts de comptes de stockage spécifiques).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer des analyses supplémentaires des données concernant les coûts. Par exemple, une équipe Finance peut analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

## <a name="faq"></a>Forum aux questions

**Si j’utilise le stockage Azure pendant quelques jours seulement par mois, est-ce que le prorata est appliqué ?**

La capacité de stockage est facturée en unités de la quantité quotidienne moyenne de données stockées, exprimée en gigaoctets (Go) sur une période d’un mois. Par exemple, si vous avez utilisé 10 Go de stockage en continue au cours de la première moitié du mois et rien lors de la seconde moitié, vous êtes facturé pour votre utilisation moyenne de 5 Go de stockage.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail comment fonctionnent les tarifs avec Stockage Azure. Consultez [Vue d’ensemble de la tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/).
- [Optimisez les coûts de Stockage Blob avec une capacité réservée](../blobs/storage-blob-reserved-capacity.md).
- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
