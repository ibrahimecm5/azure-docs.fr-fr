---
title: Planifier et gérer les coûts de Microsoft Azure Sentinel
description: Découvrez comment planifier, comprendre et gérer les coûts et la facturation pour Microsoft Sentinel en utilisant l’analyse des coûts dans le portail Azure et d’autres méthodes.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.custom: subject-cost-optimization, ignite-fall-2021
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 8bd0b6aee85ffe7b5dcc2b646b90a0b1678e4dc6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524889"
---
# <a name="plan-and-manage-costs-for-microsoft-sentinel"></a>Planifier et gérer les coûts de Microsoft Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article explique comment planifier et gérer les coûts pour Microsoft Sentinel. Vous devez d’abord utiliser la calculatrice de prix Azure pour planifier les coûts Microsoft Sentinel avant d’ajouter des ressources pour le service. Ensuite, lorsque vous ajoutez les ressources Azure, passez en revue les coûts estimés.

Une fois que vous avez commencé à utiliser des ressources Microsoft Sentinel, utilisez les fonctionnalités Cost Management pour définir des budgets et superviser les coûts. Vous pouvez également passer en revue les coûts prévus et déterminer les tendances des dépenses pour identifier les domaines où vous pourriez agir. Cet article décrit plusieurs façons de gérer et d’optimiser les coûts Microsoft Sentinel.

Les coûts pour Microsoft Sentinel ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts pour Microsoft Sentinel. Tous les services et ressources Azure utilisés par votre abonnement Azure, dont les services partenaires, vous sont toutefois facturés.

## <a name="prerequisites"></a>Configuration requise

- Pour afficher les données de coût et effectuer une analyse des coûts dans Cost Management, vous devez disposer d’un type de compte Azure pris en charge, avec au moins un accès en lecture.

    L’analyse des coûts dans Cost Management prend en charge la plupart des types de comptes Azure, mais pas tous. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

    Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

- Vous devez disposer de détails sur vos sources de données. Microsoft Sentinel vous permet d’importer des données à partir d’une ou de plusieurs sources de données. Certaines de ces sources de données sont gratuites, et d’autres engendrent des frais. Pour plus d’informations, consultez [Sources de données gratuites](#free-data-sources).

## <a name="estimate-costs-before-using-microsoft-sentinel"></a>Estimer les coûts avant d’utiliser Microsoft Sentinel

Si vous n’utilisez pas encore Microsoft Sentinel, vous pouvez vous servir de la [calculatrice de prix Microsoft Sentinel](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel) pour estimer les coûts potentiels. Entrez *Microsoft Sentinel* dans la zone de recherche puis sélectionnez la vignette Microsoft Sentinel obtenue. La calculatrice de prix vous aide à estimer vos coûts probables en fonction de l’ingestion et de la conservation des données que vous prévoyez.

Par exemple, vous pouvez saisir le nombre de Go de données quotidiennes que vous prévoyez d’ingérer dans Microsoft Sentinel, ainsi que la région de votre espace de travail. La calculatrice fournit le coût mensuel global pour les composants suivants :

- Ingestion des données Log Analytics
- Analyse des données Microsoft Sentinel
- Conservation des données Log Analytics

> [!NOTE]
> Les coûts indiqués dans cette image sont donnés à titre d’exemple uniquement. Ils ne sont pas destinés à refléter les coûts réels.

:::image type="content" source="media/billing/pricing-calculator.png" alt-text="Exemple de capture d’écran montrant le coût estimé dans la calculatrice de prix Azure." lightbox="media/billing/pricing-calculator.png" :::

## <a name="understand-the-full-billing-model-for-microsoft-sentinel"></a>Comprendre le modèle de facturation complet pour Microsoft Sentinel

Microsoft Sentinel offre un modèle de tarification flexible et prévisible. Pour plus d’informations, voir la [page tarification de Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/). Pour connaître les frais connexes de Log Analytics, consultez [Tarification Azure Monitor pour Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft Sentinel s’exécute sur l’infrastructure Azure qui cumule les coûts lorsque vous déployez de nouvelles ressources. Il est important de comprendre qu’il peut y avoir d’autres coûts d’infrastructure supplémentaires susceptibles de s’accumuler.

### <a name="how-youre-charged-for-microsoft-sentinel"></a>Comment vous êtes facturé pour Microsoft Sentinel

Il existe deux façons de payer pour le service Microsoft Sentinel : le **paiement à l’utilisation** et les **niveaux d’engagement**.

- Le **paiement à l’utilisation** est le modèle par défaut, basé sur le volume réel de données stockées et, éventuellement, sur la conservation des données au-delà de 90 jours. Le volume de données est mesuré en Go (10^9 octets).

- Log Analytics et Microsoft Sentinel proposent également des tarifs par **niveau d’engagement**, anciennement appelés Réservations de capacité, qui sont plus prévisibles et permettent d’économiser jusqu’à 65 % par rapport au tarif de paiement à l’utilisation.

    Avec la tarification par niveau d’engagement, vous pouvez acheter un engagement à partir de 100 Go/jour. Toute utilisation dépassant le niveau d’engagement est facturée au tarif du niveau d’engagement que vous avez sélectionné. Par exemple, un niveau d’engagement de 100 Go vous facture le volume de données engagé de 100 Go, plus tout Go/jour supplémentaire au tarif réduit pour ce niveau.

    Vous pouvez augmenter votre niveau d’engagement à tout moment et le réduire tous les 31 jours, afin d’optimiser les coûts à mesure que votre volume de données augmente ou diminue. Pour voir votre niveau tarifaire Microsoft Sentinel actuel, sélectionnez **Paramètres** dans le volet de navigation gauche de Microsoft Sentinel, puis sélectionnez l’onglet **Tarification**. Votre niveau tarifaire actuel est marqué **Niveau actuel**.

    Pour définir et modifier votre niveau d’engagement, consultez [Définir ou modifier le niveau tarifaire](#set-or-change-pricing-tier).

### <a name="understand-your-microsoft-sentinel-bill"></a>Comprendre votre facture Microsoft Sentinel

Les compteurs facturables sont les composants individuels de votre service qui apparaissent sur votre facture et sont également affichés dans l’analyse des coûts sous votre service. À la fin de votre cycle de facturation, les frais associés à chaque compteur sont additionnés. Votre facture contient une section pour tous les coûts Microsoft Sentinel. Chaque compteur est représenté par un élément de ligne distinct.

Pour voir votre facture Azure, sélectionnez **Analyse des coûts** dans le volet de navigation gauche de **Cost Management and Billing**. Sur l’écran **Analyse des coûts**, sélectionnez le caret déroulant dans le champ **Afficher**, puis sélectionnez **Détails de la facture**.

> [!NOTE]
> Les coûts indiqués dans cette image sont donnés à titre d’exemple uniquement. Ils ne sont pas destinés à refléter les coûts réels.

![Capture d’écran montrant la section Microsoft Sentinel d’un exemple de facture Azure.](media/billing/sample-bill.png)

Les frais relatifs à Microsoft Sentinel et à Log Analytics s’affichent sur votre facture Azure sous la forme de lignes distinctes en fonction du plan tarifaire sélectionné. Si vous dépassez l’utilisation du niveau d’engagement de votre espace de travail au cours d’un mois donné, la facture Azure présente une ligne pour le niveau d’engagement avec son coût fixe associé, et une ligne distincte pour l’ingestion au-delà du niveau d’engagement, facturée au même tarif du niveau d’engagement.

Le tableau suivant montre la manière dont les coûts Microsoft Sentinel et Log Analytics apparaissent dans les colonnes **Nom du service** et **Compteur** de votre facture Azure :

| Coût | Nom du service | Compteur |
|--|--|--|
| Niveau d’engagement Microsoft Sentinel | `sentinel` | **niveau d’engagement de `n` Go** |
| Niveau d’engagement Log Analytics | `azure monitor` | **niveau d’engagement de `n` Go** |
| Dépassement de Microsoft Sentinel par rapport au niveau d’engagement ou à Paiement à l’utilisation| `sentinel` |**analyse**|
| Dépassement de Log Analytics par rapport au niveau d’engagement ou à Paiement à l’utilisation| `log analytics` |**ingestion des données**|

Pour plus d’informations sur l’affichage et le téléchargement de votre facture Azure, consultez les [informations relatives aux coûts et à la facturation d’Azure](../cost-management-billing/understand/download-azure-daily-usage.md).

### <a name="costs-for-other-services"></a>Coûts des autres services

Microsoft Sentinel s’intègre à de nombreux autres services Azure pour offrir des capacités améliorées. Ces services incluent Azure Logic Apps, Azure Notebooks et les modèles BYOML (Bring Your Own Machine Learning). Certains de ces services peuvent entraîner des frais supplémentaires. Certains connecteurs de données et solutions de Microsoft Sentinel utilisent Azure Functions pour l’ingestion des données, qui a également un coût associé distinct.

Pour plus d’informations sur la tarification de ces services, consultez :

- [Tarification Automation-Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
- [Tarification Notebooks](https://azure.microsoft.com/pricing/details/machine-learning/)
- [Tarification BYOML](https://azure.microsoft.com/pricing/details/machine-learning-studio/)
- [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/)

Tout autre service que vous utilisez peut avoir des coûts associés.

### <a name="data-retention-costs"></a>Coûts de conservation des données

Une fois que vous avez activé Microsoft Sentinel sur un espace de travail Log Analytics, vous pouvez conserver gratuitement toutes les données ingérées dans l’espace de travail pendant les 90 premiers jours. La conservation des données au-delà de 90 jours est facturée selon les [tarifs standard de Log Analytics pour la conservation des données](https://azure.microsoft.com/pricing/details/monitor/).

Vous pouvez spécifier différents paramètres de conservation des données pour des types de données individuels. Pour plus d’informations, consultez [Durée de conservation par type de données](../azure-monitor/logs/manage-cost-storage.md#retention-by-data-type).

### <a name="other-cef-ingestion-costs"></a>Autres coûts d’ingestion CEF

Le CEF est un format d’événements Syslog pris en charge dans Microsoft Sentinel. Vous pouvez utiliser le CEF pour apporter de précieuses informations de sécurité provenant de diverses sources à votre espace de travail Microsoft Sentinel. Les journaux CEF atterrissent dans la table CommonSecurityLog de Microsoft Sentinel, qui comprend tous les champs CEF standard à jour.

De nombreux appareils et sources de données autorisent des champs de journalisation au-delà du schéma CEF standard. Ces champs supplémentaires atterrissent dans la table AdditionalExtensions. Ces champs peuvent avoir des volumes d’ingestion plus élevés que les champs CEF standard, car le contenu des événements dans ces champs peut être variable.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Coûts qui peuvent s’additionner après la suppression des ressources

La suppression de Microsoft Sentinel n’entraîne pas la suppression de l’espace de travail Log Analytics sur lequel Microsoft Sentinel a été déployé, ni de frais distincts que l’espace de travail peut encourir.

### <a name="free-trial"></a>Essai gratuit

Essayez Microsoft Sentinel gratuitement pendant les 31 premiers jours. Microsoft Sentinel peut être activé sans coût supplémentaire sur un espace de travail Azure Monitor Log Analytics, selon les limites indiquées ci-dessous :

- Les **nouveaux espaces de travail log Analytics** peuvent ingérer gratuitement jusqu’à 10 Go/jour de données de journal pour les 31 derniers jours. Les nouveaux espaces de travail incluent des espaces de travail datant de moins de trois jours.

   L’ingestion des données Log Analytics et les frais liés Microsoft Sentinel ne doivent pas être payés pendant la période d’évaluation de 31 jours. Cette version d’essai gratuite est soumise à une limite de 20 espaces de travail par locataire Azure.

- Les **espaces de travail log Analytics existants** peuvent activer Microsoft Sentinel sans coût supplémentaire. Les espaces de travail existants incluent tous les espaces de travail créés il y a plus de trois jours.

   Pendant la période d’essai de 31 jours, vous ne serez pas redevable des frais liés à Microsoft Sentinel uniquement.

L’utilisation au-delà de ces limites sera facturée en fonction de la tarification indiquée sur la page [Tarification Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel). Les frais liés aux fonctionnalités supplémentaires [d’automatisation](automation-in-azure-sentinel.md) et de[ BYOML (apportez votre propre machine learning)](bring-your-own-ml.md) sont toujours applicables durant l’essai gratuit.

> [!TIP]
> Pendant la période d’essai gratuit, recherchez des ressources pour la gestion des coûts, la formation et bien plus encore sur l’onglet **Nouveautés & guides > Essai gratuit** dans Microsoft Sentinel.
>
> Cet onglet affiche également des détails sur les dates de votre version d’évaluation gratuite et le nombre de jours restants jusqu’à leur expiration.
>
### <a name="free-data-sources"></a>Sources de données gratuites

Les sources de données suivantes sont gratuites avec Microsoft Sentinel :

- Journaux d’activité Azure.
- Journaux d’audit d’Office 365, y compris toute l’activité SharePoint, l’activité de l’administrateur Exchange et Teams.
- Alertes de sécurité, y compris les alertes de Microsoft Defender pour le cloud, de Microsoft 365 Defender, Microsoft Defender pour Office 365, Microsoft Defender pour Identity et Microsoft Defender pour point de terminaison.
- Alertes Microsoft Defender pour le Cloud et Microsoft Defender pour les applications Cloud. Toutefois, les journaux bruts pour certains types de données de Microsoft 365 Defender, de Defender pour les applications cloud, d’Azure Active Directory (Azure AD) et d’Azure Information Protection (AIP) sont payants.

Le tableau suivant répertorie les sources de données gratuites que vous pouvez activer dans Microsoft Sentinel. Certains connecteurs de données, tels que Microsoft 365 Defender et Defender pour les applications cloud, incluent des types de données gratuits et payants.

| Connecteur de données Microsoft Sentinel   | Type de données | Gratuit ou payant |
|-------------------------------------|--------------------------------|------------------|
| **Journaux d’activité Azure**         | AzureActivity                  | Gratuit             |
| **Azure AD Identity Protection**         | SecurityAlert (IPC)                  | Gratuit             |
| **Office 365**                     | OfficeActivity (SharePoint)    | Gratuit|
|| OfficeActivity (Exchange)|Gratuit|
|| OfficeActivity (Teams)          | Gratuit|
| **Microsoft Defender pour le cloud**                  | SecurityAlert (Defender pour le Cloud)             | Gratuit             |
| **Microsoft Defender pour IoT**          | SecurityAlert (Defender pour l’IoT)     | Gratuit             |
| **Microsoft 365 Defender**          | SecurityIncident | Gratuit|
||SecurityAlert| Gratuit|
||DeviceEvents                    | Payant|
||DeviceFileEvents                | Payant|
||DeviceImageLoadEvents           | Payant|
||DeviceInfo                      | Payant|
||DeviceLogonEvents               | Payant|
||DeviceNetworkInfo             | Payant|
||DeviceNetworkInfo               | Payant|
||DeviceProcessEvents             | Payant|
||DeviceRegistryEvents            | Payant|
||DeviceFileCertificateInfo       | Payant|
| **Microsoft Defender for Endpoint** | SecurityAlert (MDATP)          | Gratuit             |
| **Microsoft Defender pour Identity** | SecurityAlert (AATP)           | Gratuit             |
| **Microsoft Defender for Cloud Apps**   | SecurityAlert (Defender pour les applications cloud)           | Gratuit             |
||MCASShadowITReporting           | Payant|

Pour les connecteurs de données qui incluent à la fois des types de données gratuits et payants, vous pouvez sélectionner les types de données que vous souhaitez activer.

![Capture d’écran montrant la page Connecteurs de données pour Defender pour les applications cloud, avec le type gratuit SecurityAlert sélectionné et le type payant MCASShadowITReporting non sélectionné.](media/billing/data-types.png)

Pour plus d’informations sur les sources de données et les connecteurs gratuits et payants, consultez [Connecter des sources de données](connect-data-sources.md).

> [!NOTE]
> Les connecteurs de données listés comme préversion publique ne génèrent pas de coût. Les connecteurs de données génèrent un coût uniquement une fois qu’ils sont en disponibilité générale.
>

## <a name="estimate-microsoft-sentinel-costs"></a>Estimer les coûts de Microsoft Sentinel

Si vous n’utilisez pas encore Microsoft Azure Sentinel, vous pouvez utiliser la [calculatrice de prix de Microsoft Azure Sentinel](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel) pour estimer le coût potentiel d’utilisation de Microsoft Azure Sentinel. Entrez *Microsoft Sentinel* dans la zone de recherche puis sélectionnez la vignette Microsoft Sentinel obtenue. La calculatrice de prix vous aide à estimer vos coûts probables en fonction de l’ingestion et de la conservation des données que vous prévoyez.

Par exemple, vous pouvez saisir le nombre de Go de données quotidiennes que vous prévoyez d’ingérer dans Microsoft Sentinel, ainsi que la région de votre espace de travail. La calculatrice fournit le coût mensuel global pour les composants suivants :

- Ingestion des données Log Analytics
- Analyse des données Microsoft Sentinel
- Conservation des données Log Analytics

## <a name="manage-and-monitor-microsoft-sentinel-costs"></a>Gérer et surveiller les coûts de Microsoft Sentinel

À mesure que vous utilisez des ressources Azure avec Microsoft Sentinel, vous générez des coûts. Les coûts unitaires d’utilisation des ressources Azure varient selon les intervalles de temps (secondes, minutes, heures et jours) ou selon l’utilisation d’unités (octets et mégaoctets). Dès que l’utilisation de Microsoft Sentinel est lancée, des coûts sont occasionnés, que vous pouvez voir dans l’[analyse des coûts](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Quand vous effectuez une analyse des coûts, vous voyez les coûts Microsoft Sentinel dans des graphes et des tableaux pour différents intervalles de temps. Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Vous pouvez aussi afficher les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses. Ceci vous permet de voir où des dépassements ont pu se produire. Si vous avez créé des budgets, vous pouvez aussi facilement voir à quel moment ils ont été dépassés.

Le hub [Azure Cost Management and Billing](../cost-management-billing/costs/quick-acm-cost-analysis.md) offre une fonctionnalité utile. Après avoir ouvert **Cost Management and Billing** dans le portail Azure, sélectionnez **Cost Management** dans le volet de navigation gauche, puis sélectionnez l’[étendue](..//cost-management-billing/costs/understand-work-scopes.md) ou l’ensemble des ressources à examiner, par exemple un abonnement Azure ou un groupe de ressources.

L’écran **Analyse des coûts** présente des vues détaillées de votre utilisation et de vos coûts Azure, avec la possibilité d’appliquer divers contrôles et filtres.

Par exemple, pour voir les graphiques de vos coûts quotidiens pour une certaine période :

1. Cliquez sur le caret déroulant dans le champ **Afficher** et sélectionnez **Coûts cumulés** ou **Coûts quotidiens**.
1. Cliquez sur le caret déroulant dans le champ Date et sélectionnez une plage de dates.
1. Cliquez sur le caret déroulant à côté de **Granularité** et sélectionnez **Quotidien**.

> [!NOTE]
> Les volumes d’ingestion de données Microsoft Sentinel apparaissent sous **Insights sur la sécurité** dans certains graphiques d’utilisation du portail.

Les niveaux tarifaires de Microsoft Sentinel n’incluent pas les frais de Log Analytics. Pour modifier votre engagement de niveau tarifaire pour Log Analytics, consultez [Changement de niveau tarifaire](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier).

Pour plus d’informations, consultez [Créer des budgets](#create-budgets) et [Autres façons de gérer et de réduire les coûts de Microsoft Sentinel](#other-ways-to-manage-and-reduce-microsoft-sentinel-costs).

### <a name="using-azure-prepayment-with-microsoft-sentinel"></a>Utilisation du prépaiement Azure avec Microsoft Sentinel

Vous pouvez payer les frais Microsoft Sentinel avec votre crédit de paiement anticipé Azure. Toutefois, vous ne pouvez pas utiliser le crédit de paiement anticipé Azure afin de payer des factures à des organisations tierces pour leurs produits et services, ou pour des produits de la Place de marché Azure.

### <a name="run-queries-to-understand-your-data-ingestion"></a>Exécuter des requêtes pour comprendre votre ingestion des données

Microsoft Sentinel utilise un langage de requête étendu pour analyser d’énormes volumes de données opérationnelles, interagir avec elles et en tirer des insights en quelques secondes. Voici quelques requêtes Kusto que vous pouvez utiliser pour comprendre votre volume d’ingestion de données.

Exécutez la requête suivante pour afficher le volume d’ingestion de données par solution :

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| render columnchart
```

Exécutez la requête suivante pour afficher le volume d’ingestion de données par type de données :

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType
| render columnchart
```

Exécutez la requête suivante pour afficher le volume d’ingestion de données par solution et par type de données :

```kusto
Usage
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| sort by Solution asc, DataType asc
```

### <a name="deploy-a-workbook-to-visualize-data-ingestion"></a>Déployer un classeur pour visualiser l’ingestion des données

Le classeur **Rapport sur l’utilisation de l’espace de travail** fournit des statistiques sur la consommation, le coût et l’utilisation des données de votre espace de travail. Le classeur indique l’état d’ingestion des données de l’espace de travail et la quantité de données gratuites et facturables. Vous pouvez utiliser la logique du classeur pour surveiller l’ingestion de données et les coûts et pour créer des vues personnalisées et des alertes basées sur des règles.

Ce classeur fournit également des détails granulaires sur l’ingestion. Le classeur décompose les données de votre espace de travail par table de données et fournit des volumes par table et par entrée pour vous aider à mieux comprendre vos modèles d’ingestion.

Pour activer le classeur Rapport sur l’utilisation de l’espace de travail :

1. Dans le volet de navigation gauche de Microsoft Sentinel, sélectionnez **Gestion des menaces** > **Classeurs**.
1. Entrez *utilisation de l’espace de travail* dans la barre de recherche, puis sélectionnez **Rapport sur l’utilisation de l’espace de travail**.
1. Sélectionnez **Afficher le modèle** pour utiliser le classeur tel quel, ou **Enregistrer** pour créer une copie modifiable du classeur. Si vous enregistrez une copie, sélectionnez **Afficher le classeur enregistré**.
1. Dans le classeur, sélectionnez l’**abonnement** et l’**espace de travail** que vous souhaitez afficher, puis définissez le **TimeRange** (intervalle de temps) sur la période que vous souhaitez visualiser. Vous pouvez régler le bouton bascule **Afficher l’aide** sur **Oui** pour afficher des explications intégrées dans le classeur.

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer une analyse supplémentaire des données concernant les coûts. Par exemple, une équipe Finance peut analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

## <a name="create-budgets"></a>Créer des budgets

Vous pouvez créer des [budgets](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts.

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande précision dans votre supervision. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre disponibles lorsque vous créez un budget, consultez [Options de regroupement et de filtre](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

### <a name="use-a-playbook-for-cost-management-alerts"></a>Utiliser un guide opérationnel pour les alertes relatives à la gestion des coûts

Pour vous aider à contrôler votre budget Microsoft Sentinel, vous pouvez créer un guide opérationnel de gestion des coûts. Le guide opérationnel vous envoie une alerte si votre espace de travail Microsoft Sentinel dépasse un budget, que vous définissez, dans un délai donné.

La communauté GitHub de Microsoft Sentinel fournit le guide opérationnel de gestion des coûts [`Send-IngestionCostAlert`](https://github.com/iwafula025/Azure-Sentinel/tree/master/Playbooks/Send-IngestionCostAlert) sur GitHub. Ce guide opérationnel est activé par un déclencheur de périodicité et vous offre un niveau élevé de flexibilité. Vous pouvez contrôler la fréquence d’exécution, le volume d’ingestion et le message à déclencher, en fonction de vos besoins.

### <a name="define-a-data-volume-cap-in-log-analytics"></a>Définir un plafond de volume de données dans Log Analytics

Dans Log Analytics, vous pouvez activer un plafond de volume quotidien qui limite l’ingestion quotidienne de votre espace de travail. Le plafond quotidien peut vous aider à gérer les augmentations inattendues du volume de données, à rester sous votre limite et à limiter les frais non planifiés.

Pour définir un plafond de volume quotidien, sélectionnez **Utilisation et estimation des coûts** dans le volet de navigation gauche de votre espace de travail Log Analytics, puis sélectionnez **Plafond quotidien**. Sélectionnez **Activé**, entrez une valeur limite de volume quotidien, puis sélectionnez **OK**.

![Capture d’écran montrant l’écran Utilisation et estimation des coûts et la fenêtre Plafond quotidien.](media/billing/daily-cap.png)

L’écran **Utilisation et estimation des coûts** indique également la tendance du volume de données ingérées au cours des 31 derniers jours, ainsi que le volume total de données conservées.

> [!IMPORTANT]
> Le plafond quotidien ne limite pas la collecte de tous les types de données. Les données de sécurité sont exclues du plafond. Pour plus d’informations sur la gestion du plafond quotidien dans Log Analytics, consultez [Gérer votre volume de données maximal quotidien](../azure-monitor/logs/manage-cost-storage.md#manage-your-maximum-daily-data-volume).

## <a name="other-ways-to-manage-and-reduce-microsoft-sentinel-costs"></a>Autres façons de gérer et de réduire les coûts Microsoft Sentinel

Pour gérer les coûts d’ingestion et de conservation des données :

- [Utilisez la tarification par niveau d’engagement pour optimiser les coûts](#set-or-change-pricing-tier) en fonction de votre volume de données ingérées.
- [Séparez les données non relatives à la sécurité dans un espace de travail différent](#separate-non-security-data-in-a-different-workspace).
- [Optimisez les coûts de Log Analytics à l’aide de clusters dédiés](#optimize-log-analytics-costs-with-dedicated-clusters).
- [Réduisez les coûts de conservation des données à long terme grâce à Azure Data Explorer (ADX)](#reduce-long-term-data-retention-costs-with-adx).
- [Utiliser les règles de collecte de données pour vos événements de sécurité Windows](#use-data-collection-rules-for-your-windows-security-events).

### <a name="set-or-change-pricing-tier"></a>Définir ou modifier le niveau tarifaire

Pour optimiser les économies les plus importantes, surveillez votre volume d’ingestion pour vous assurer que vous disposez du niveau d’engagement qui correspond le mieux à vos modèles de volume d’ingestion. Vous pouvez augmenter ou diminuer votre niveau d’engagement pour vous aligner sur l’évolution des volumes de données.

Vous pouvez augmenter votre niveau d’engagement à tout moment, ce qui redémarre la période d’engagement de 31 jours. Toutefois, pour revenir à Paiement à l’utilisation ou à un niveau d’engagement inférieur, vous devez attendre la fin de la période d’engagement de 31 jours. La facturation des niveaux d’engagement se fait sur une base quotidienne.

Pour voir votre niveau tarifaire Microsoft Sentinel actuel, sélectionnez **Paramètres** dans le volet de navigation gauche de Microsoft Sentinel, puis sélectionnez l’onglet **Tarification**. Votre niveau tarifaire actuel est marqué **Niveau actuel**.

Pour modifier votre engagement de niveau tarifaire, sélectionnez l’un des autres niveaux sur la page de tarification, puis sélectionnez **Appliquer**. Vous devez disposer du rôle **Contributeur** ou **Propriétaire** dans Microsoft Sentinel pour modifier le niveau tarifaire.

![Capture d’écran montrant la page Tarification dans les paramètres de Microsoft Sentinel, avec Paiement à l’utilisation indiqué comme niveau tarifaire actuel.](media/billing/pricing.png)

> [!NOTE]
> Les volumes d’ingestion de données Microsoft Sentinel apparaissent sous **Insights sur la sécurité** dans certains graphiques d’utilisation du portail.

Les niveaux tarifaires de Microsoft Sentinel n’incluent pas les frais de Log Analytics. Pour modifier votre engagement de niveau tarifaire pour Log Analytics, consultez [Changement de niveau tarifaire](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier).

### <a name="separate-non-security-data-in-a-different-workspace"></a>Séparer les données non relatives à la sécurité dans un espace de travail différent

Microsoft Sentinel analyse toutes les données ingérées dans les espaces de travail Log Analytics avec Microsoft Sentinel. Il est préférable de disposer d’un espace de travail distinct pour les données d’opérations non liées à la sécurité, afin de s’assurer qu’elles n’entraînent pas de coûts de Microsoft Sentinel.

Lors de la chasse ou de l’investigation des menaces dans Microsoft Sentinel, vous pouvez avoir besoin d’accéder aux données opérationnelles stockées dans ces espaces de travail Azure Log Analytics autonomes. Vous pouvez accéder à ces données en utilisant des requêtes interespaces de travail dans l’expérience d’exploration des journaux et les classeurs. Toutefois, vous ne pouvez pas utiliser les requêtes de chasse ni les règles analytiques portant sur plusieurs espaces de travail, sauf si Microsoft Sentinel est activé sur tous les espaces de travail.

### <a name="optimize-log-analytics-costs-with-dedicated-clusters"></a>Optimiser les coûts de Log Analytics à l’aide de clusters dédiés

Si vous ingérez au moins 500 Go/jour dans votre espace de travail Microsoft Sentinel ou dans des espaces de travail de la même région, envisagez de passer à un cluster dédié Log Analytics pour réduire les coûts. Un niveau d’engagement pour cluster dédié Log Analytics agrège les volumes de données des espaces de travail qui ingèrent collectivement un total de 500 Go/jour ou plus.

Les clusters dédiés Log Analytics ne s’appliquent pas aux niveaux d’engagement de Microsoft Sentinel. Les coûts de Microsoft Sentinel s’appliquent toujours par espace de travail dans le cluster dédié.

Vous pouvez ajouter plusieurs espaces de travail Microsoft Sentinel à un cluster dédié Log Analytics. L’utilisation d’un cluster dédié Log Analytics pour Microsoft Sentinel présente deux avantages :

- Les requêtes interespaces de travail s’exécutent plus rapidement si tous les espaces de travail impliqués dans la requête se trouvent dans le cluster dédié. Il est toujours préférable d’avoir le moins d’espaces de travail possible dans votre environnement, et un cluster dédié conserve toujours la [limite de 100 espaces de travail](../azure-monitor/logs/cross-workspace-query.md) à inclure dans une seule requête interespace de travail.

- Tous les espaces de travail du cluster dédié peuvent partager le niveau d’engagement Log Analytics défini sur le cluster. Le fait de ne pas avoir à s’engager sur des niveaux d’engagement Log Analytics distincts pour chaque espace de travail peut permettre de réaliser des économies et des gains d’efficacité. En activant un cluster dédié, vous vous engagez à un niveau d’engagement Log Analytics minimum de 500 Go d’ingestion par jour.

Voici d’autres considérations à prendre en compte pour passer à un cluster dédié afin d’optimiser les coûts :

- Le nombre maximum de clusters par région et par abonnement est de deux.
- Tous les espaces de travail liés à un cluster doivent se trouver dans la même région.
- Le nombre maximal d’espaces de travail liés à un cluster est de 1000.
- Vous pouvez dissocier un espace de travail lié à votre cluster. Le nombre d’opérations de liaison sur un espace de travail particulier est limité à 2 par période de 30 jours.
- Vous ne pouvez pas déplacer un espace de travail existant vers un cluster avec clé gérée par le client (CMK). Vous devez créer l’espace de travail dans le cluster.
- Le déplacement d’un cluster vers un autre groupe de ressources ou un autre abonnement n’est pas pris en charge actuellement.
- Un lien d’espace de travail vers un cluster échoue si l’espace de travail est lié à un autre cluster.

Pour plus d’informations sur les clusters dédiés, consultez [Clusters dédiés Log Analytics](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters).

### <a name="reduce-long-term-data-retention-costs-with-adx"></a>Réduire les coûts de conservation des données à long terme grâce à ADX

La conservation des données Microsoft Sentinel est gratuite pendant les 90 premiers jours. Pour ajuster la période de conservation des données dans Log Analytics, sélectionnez **Utilisation et estimation des coûts** dans le volet de navigation gauche, puis sélectionnez **Conservation des données** et réglez le curseur.

Les données de sécurité de Microsoft Sentinel peuvent perdre une partie de leur valeur après quelques mois. Les utilisateurs du centre des opérations de sécurité (SOC) n’ont peut-être pas besoin d’accéder aux anciennes données aussi fréquemment qu’aux nouvelles, mais ils peuvent tout de même avoir besoin d’accéder aux données à des fins d’investigation ou d’audit sporadiques. Pour réduire les coûts de conservation des données de Microsoft Sentinel, vous pouvez utiliser Azure Data Explorer pour la conservation des données à long terme à moindre coût. ADX offre un juste équilibre entre coût et convivialité pour les données vieillies qui n’ont plus besoin des renseignements sur la sécurité de Microsoft Sentinel.

Grâce à ADX, vous pouvez stocker des données à moindre coût, tout en continuant à les explorer à l’aide des mêmes requêtes KQL (Kusto Query Language) que dans Microsoft Sentinel. Vous pouvez également utiliser la fonctionnalité de proxy d’ADX pour effectuer des requêtes multiplateformes. Ces requêtes agrègent et mettent en corrélation les données réparties dans ADX, Application Insights, Microsoft Sentinel et Log Analytics.

Pour plus d’informations, consultez [Intégrer Azure Data Explorer pour la conservation des journaux à long terme](store-logs-in-azure-data-explorer.md).

### <a name="use-data-collection-rules-for-your-windows-security-events"></a>Utiliser les règles de collecte de données pour vos événements de sécurité Windows

Le [connecteur d’événements de sécurité Windows](connect-windows-security-events.md?tabs=LAA) vous permet de diffuser des événements de sécurité à partir de n’importe quel ordinateur exécutant Windows Server connecté à votre espace de travail Microsoft Sentinel, dont les serveurs physiques, virtuels ou locaux, ou dans n’importe quel cloud. Ce connecteur prend en charge l’agent Azure Monitor, qui utilise des règles de collecte de données pour définir les données à collecter à partir de chaque agent.

Les règles de collecte de données vous permettent de gérer les paramètres de collecte à grande échelle, tout en continuant d’autoriser des configurations uniques, délimitées pour les sous-ensembles de machines. Pour plus d’informations, consultez [Configurer la collecte de données pour l’agent Azure Monitor](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md).

En plus des ensembles d’événements prédéfinis que vous pouvez sélectionner pour l’ingestion, tels que Tous les événements, Minimal ou Courant, les règles de collecte de données vous permettent de créer des filtres personnalisés et de sélectionner des événements spécifiques à ingérer. L’agent Azure Monitor utilise ces règles pour filtrer les données à la source, puis ingérer uniquement les événements que vous avez sélectionnés tout en laissant les autres données de côté. La sélection d’événements spécifiques à ingérer peut vous aider à optimiser vos coûts et à faire plus d’économies.

> [!NOTE]
> Les coûts indiqués dans cette image sont donnés à titre d’exemple uniquement. Ils ne sont pas destinés à refléter les coûts réels.

![Capture d’écran montrant un écran Analyse des coûts de Cost Management and Billing.](media/billing/cost-management.png)

Vous pouvez également appliquer d’autres contrôles. Par exemple, pour afficher uniquement les coûts associés à Microsoft Sentinel, sélectionnez **Ajouter un filtre**, **Nom du service**, puis les noms de service **sentinel**, **log analytics** et **azure monitor**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Pour obtenir des conseils supplémentaires sur la réduction du volume de données Log Analytics, consultez [Conseils pour réduire le volume de données](../azure-monitor/logs/manage-cost-storage.md#tips-for-reducing-data-volume).
