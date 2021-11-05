---
title: Gérer l’utilisation et les coûts à l’aide des journaux d’activité Azure Monitor
description: Découvrez comment changer le plan tarifaire et gérer la stratégie de rétention et de volume des données de votre espace de travail Log Analytics dans Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/17/2021
ms.author: bwren
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 650ccc08d5cec7e037a6203f71ac8429f1bc2bf0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078994"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gérer l’utilisation et les coûts avec les journaux Azure Monitor    

> [!NOTE]
> Cet article explique en quoi consistent vos coûts pour les journaux Azure Monitor et comment les contrôler. Un article associé, [Surveiller l’utilisation et l’estimation des coûts](../usage-estimated-costs.md), explique comment visualiser l’utilisation et les coûts estimés avec plusieurs fonctionnalités de surveillance Azure en fonction des différents modèles tarifaires. Tous les prix et coûts de cet article sont fournis uniquement à titre d’exemple. 

Les journaux Azure Monitor sont conçus pour la mise à l’échelle et la prise en charge de la collecte, de l’indexation et du stockage de quantités importantes de données quotidiennes provenant de toute source de votre entreprise ou déployées dans Azure.  Même si ce peut être un élément moteur pour votre organisation, la rentabilité est au final ce qui importe le plus. À cette fin, il est important de comprendre que le coût d’un espace de travail Log Analytics n’est pas seulement fonction du volume des données collectées, mais qu’il dépend aussi du plan sélectionné et de la durée de stockage des données générées à partir de vos sources connectées.  

Cet article explique comment surveiller de manière proactive le volume de données ingérées et la croissance du stockage. Il explique également comment définir des limites pour contrôler les coûts associés. 

## <a name="pricing-model"></a>Modèle de tarification

Les tarifs par défaut de Log Analytics suivent un modèle de **paiement à l’utilisation**, basé sur le volume de données ingérées, et peuvent varier si la période de conservation des données est plus longue. Le volume de données s’exprime par la taille des données qui seront stockées en Go (10^9 octets). Chaque espace de travail Log Analytics est facturée en tant que service distinct et s’ajoute à la facture de votre abonnement Azure. La quantité de données ingérées peut être considérable en fonction des facteurs suivants : 

  - Le jeu de solutions de gestion activées et leur configuration
  - Le nombre et le type des ressources surveillées
  - Le type de données collectées à partir de chaque ressource analysée
  
En plus du modèle Paiement à l’utilisation, Log Analytics possède des **niveaux d’engagement** peut vous permettre d’économiser jusqu’à 30 % par rapport au tarif de Paiement à l’utilisation. Avec la tarification par niveau d’engagement, vous pouvez vous engager à acheter l’ingestion des données à partir de 100 Go/jour à un prix inférieur à celui de Paiement à l’utilisation. Toute utilisation au-delà du niveau d’engagement (dépassement) est facturée au même prix par Go que le niveau d’engagement actuel. Les niveaux d’engagement ont une période d’engagement de 31 jours. Pendant la période d’engagement, vous pouvez passer à un niveau d’engagement supérieur (ce qui relance la période d’engagement de 31 jours), mais vous ne pouvez pas revenir à Paiement à l’utilisation ou à un niveau d’engagement inférieur avant que la période d’engagement ne soit terminée. La facturation des niveaux d’engagement se fait sur une base quotidienne. [En savoir plus](https://azure.microsoft.com/pricing/details/monitor/) sur la tarification des niveaux d’engagement et de Paiement à l’utilisation de Log Analytics. 

> [!NOTE]
> À compter du 2 juin 2021, les **réservations de capacité** seront désormais appelées **Niveaux d’engagement**. Les données collectées au-delà de votre niveau d’engagement (dépassement) sont désormais facturées au même prix par Go que le niveau d’engagement actuel, ce qui réduit les coûts par rapport à l’ancienne méthode de facturation au tarif Paiement à l’utilisation et permet aux utilisateurs disposant d’importants volumes de données d’affiner leur niveau d’engagement. Trois nouveaux niveaux d’engagement ont également été ajoutés : 1000, 2000 et 5000 Go/jour. 

À tous les niveaux tarifaires, la taille des données d’un événement est calculée à partir d’une représentation des propriétés sous forme d’une chaîne stockée dans Log Analytics pour cet événement, que les données soient envoyées par un agent ou qu’elles soient ajoutées pendant le processus d’ingestion. Cela comprend tous les [champs personnalisés](custom-fields.md) ajoutés à mesure que des données sont collectées, puis stockées dans Log Analytics. Plusieurs propriétés communes à tous les types de données, notamment certaines [propriétés standard de Log Analytics](./log-standard-columns.md), ne sont pas incluses dans le calcul de la taille de l’événement. Cela inclut `_ResourceId`, `_SubscriptionId`, `_ItemId`, `_IsBillable`, `_BilledSize` et `Type`. Toutes les autres propriétés stockées dans Log Analytics sont incluses dans le calcul de la taille de l’événement. Certains types de données sont exempts de frais d’ingestion de données, par exemple les [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity), [Heartbeat](/azure/azure-monitor/reference/tables/heartbeat), [Usage](/azure/azure-monitor/reference/tables/usage) et [Operation](/azure/azure-monitor/reference/tables/operation). Certaines solutions ont davantage de stratégies spécifiques à la solution sur l’ingestion gratuite des données, par exemple [Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/) rend les données de visualisation des dépendances gratuites pour les 180 premiers jours d’évaluation de serveur. Pour déterminer si un événement est exclu de la facturation pour l’ingestion des données, vous pouvez utiliser la propriété [_IsBillable](log-standard-columns.md#_isbillable) comme illustré [ci-dessous](#data-volume-for-specific-events). L’utilisation est exprimée en Go (10^9 octets). 

En outre, certaines solutions, telles que [Azure Defender (Security Center)](https://azure.microsoft.com/pricing/details/azure-defender/), [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) et [Configuration Management](https://azure.microsoft.com/pricing/details/automation/), ont leurs propres modèles tarifaires. 

### <a name="log-analytics-dedicated-clusters"></a>Clusters dédiés Log Analytics

Les [clusters dédiés Log Analytics](logs-dedicated-clusters.md) sont des collections d’espaces de travail regroupés dans un seul cluster Azure Data Explorer managé pour prendre en charge des scénarios avancés comme les [clés gérées par le client](customer-managed-keys.md). Les clusters dédiés Log Analytics utilisent le même modèle de tarification de niveau d’engagement que les espaces de travail, à la différence qu’un cluster doit avoir un niveau d’engagement d’au moins 500 Go/jour. Il n’existe aucune option de paiement à l’utilisation pour les clusters. Le niveau d’engagement du cluster a une période d’engagement de 31 jours après l’augmentation du niveau d’engagement. Au cours de la période d’engagement, le niveau d’engagement ne peut pas être réduit, mais il peut être augmenté à tout moment. Lorsque des espaces de travail sont associés à un cluster, la facturation d’ingestion des données de ces espaces de travail est effectuée au niveau du cluster à l’aide du niveau d’engagement configuré. En savoir plus sur la [création de clusters d’analytique des journaux d'activité](customer-managed-keys.md#create-cluster) et [l’association de ces clusters à des espaces de travail](customer-managed-keys.md#link-workspace-to-cluster). Pour plus d’informations sur la tarification par niveau d’engagement, consultez la [page de tarification Azure Monitor]( https://azure.microsoft.com/pricing/details/monitor/).

Le niveau d’engagement du cluster est configuré par programmation avec Azure Resource Manager à l’aide du paramètre `Capacity` sous `Sku`. La valeur de `Capacity` est spécifiée en unités de Go et peut avoir les valeurs suivantes : 500, 1 000, 2 000 ou 5 000 Go/jour. Toute utilisation au-delà du niveau d’engagement (dépassement) est facturée au même prix par Go que le niveau d’engagement actuel.  Pour plus d’informations, consultez [Clé gérée par le client Azure Monitor](customer-managed-keys.md).

Il existe deux modes de facturation pour l’utilisation sur un cluster. Ceux-ci peuvent être spécifiés par le paramètre `billingType` lors de la [création d’un cluster](logs-dedicated-clusters.md#create-a-dedicated-cluster) ou définis après la création. Les deux modes sont : 

- **Cluster** : dans ce cas (valeur par défaut), la facturation des données ingérées est effectuée au niveau du cluster. Les quantités de données ingérées de chaque espace de travail associé à un cluster sont agrégées pour calculer la facture quotidienne du cluster. Les allocations par nœud d’[Azure Defender (Security Center)](../../security-center/index.yml) sont appliquées au niveau de l’espace de travail avant cette agrégation de données agrégées dans tous les espaces de travail du cluster. 

- **Espaces de travail** : les coûts du niveau d’engagement pour votre cluster sont attribués proportionnellement aux espaces de travail du cluster, en fonction du volume d’ingestion des données de chaque espace de travail (après prise en compte des allocations par nœud depuis [Azure Defender [Security Center]](../../security-center/index.yml) pour chaque espace de travail). Si le volume total de données ingérées dans un cluster au cours d’une journée est inférieur au niveau d’engagement, chaque espace de travail est facturé pour ses données ingérées au tarif effectif du niveau d’engagement par Go en leur facturant une fraction du niveau d’engagement, et la partie inutilisée du niveau d’engagement est facturée à la ressource du cluster. Si le volume total de données ingérées dans un cluster au cours d’une journée est supérieur au niveau d’engagement, chaque espace de travail est facturé pour une fraction du niveau d’engagement, en fonction de sa fraction des données ingérées ce jour-là, et chaque espace de travail pour une fraction des données ingérées au-delà du niveau d’engagement. Si le volume total de données ingérées dans un espace de travail au cours d’une journée est supérieur au niveau d’engagement, rien n’est facturé à la ressource de cluster.

Dans les options de facturation du cluster, la conservation des données est facturée pour chaque espace de travail. La facturation du cluster commence lorsque le cluster est créé, que les espaces de travail aient ou non été associés au cluster. Les espaces de travail associés à un cluster ne disposent plus de leur propre niveau tarifaire.

## <a name="estimating-the-costs-to-manage-your-environment"></a>Estimation des coûts de gestion de votre environnement 

Si vous n’utilisez pas encore les journaux Azure Monitor, vous pouvez utiliser la [calculatrice de prix Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) pour estimer le coût d’utilisation de Log Analytics. Dans la zone **Rechercher**, entrez « Azure Monitor », puis sélectionnez la vignette Azure Monitor. Faites défiler la page vers **Azure Monitor**, puis développez la section **Log Analytics**. Ici, vous pouvez entrer les Go de données que vous souhaitez collecter. Si vous évaluez les journaux Azure Monitor, vous pouvez utiliser les statistiques de données à partir de votre propre environnement. Vous verrez ci-dessous comment déterminer le [nombre de machines virtuelles surveillées](#understanding-nodes-sending-data) et le [volume de données que votre espace de travail ingère](#understanding-ingested-data-volume). Si vous n’utilisez pas encore Log Analytics, voici quelques conseils pour l’estimation des volumes de données :

1. **Surveillance des machines virtuelles :** avec l’analyse standard activée, 1 à 3 Go de données ingérées par mois et par machine virtuelle surveillée. 
2. **Surveillance des clusters Azure Kubernetes service (AKS) :** les détails sur les volumes de données attendus pour la surveillance d’un cluster AKS type sont disponibles [ici](../containers/container-insights-cost.md#estimating-costs-to-monitor-your-aks-cluster). Suivez ces [meilleures pratiques](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost) pour contrôler les frais de surveillance du cluster AKS. 
3. **Surveillance des applications :** la calculatrice de prix Azure Monitor comprend un estimateur de volume de données se basant sur l’utilisation de l’application et sur une analyse statistique des volumes de données Application Insights. Dans la section Application Insights de la calculatrice de prix, basculez le commutateur en regard de « Estimer le volume de données en fonction de l’activité de l’application » pour l’utiliser. 

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Consultation de l’utilisation de Log Analytics sur votre facture Azure 

Le moyen le plus simple pour afficher l’utilisation facturée d’un espace de travail Log Analytics particulier est d’accéder à la page **Présentation** de l’espace de travail et de cliquer sur **Afficher le coût** dans le coin supérieur droit de la section Essentials en haut de la page. Cette opération lance l’analyse des coûts à partir de Azure Cost Management + facturation déjà étendue à cet espace de travail.  

Vous pouvez également démarrer dans le hub [Azure Cost Management + Billing](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json). Vous pouvez utiliser la fonctionnalité « Analyse des coûts » pour voir vos dépenses en ressources Azure. Pour suivre vos dépenses Log Analytics, vous pouvez ajouter un filtre par « type de ressource » (microsoft.operationalinsights/workspace pour Log Analytics et microsoft.operationalinsights/cluster pour les clusters Log Analytics). Pour **Regrouper par**, sélectionnez **Catégorie du compteur** ou **Compteur**. D’autres services comme Azure Defender (Security Center) et Azure Sentinel facturent également leur utilisation en fonction des ressources de l’espace de travail Log Analytics. Pour voir la correspondance avec le nom du service, vous pouvez sélectionner la vue Tableau au lieu d’un graphique. 

Pour mieux comprendre votre utilisation, vous pouvez [télécharger votre utilisation à partir du portail Azure](../../cost-management-billing/understand/download-azure-daily-usage.md). Dans la feuille de calcul téléchargée, vous pouvez voir l’utilisation par ressource Azure (par exemple, espace de travail Log Analytics) par jour. Dans cette feuille de calcul Excel, vous trouverez l’utilisation de vos espaces de travail Log Analytics en filtrant d’abord la colonne « Catégorie du compteur » pour afficher « Log Analytics », « Insight and Analytics » (utilisé par certains niveaux tarifaires hérités) et « Azure Monitor » (utilisé par les niveaux tarifaires de niveau d’engagement), puis en ajoutant sur la colonne « ID d’instance » le filtre « contient espace de travail » ou « contient cluster » (ce dernier pour inclure l’utilisation du cluster Log Analytics). L’utilisation est indiquée dans la colonne « Quantité consommée » et l’unité pour chaque entrée est affichée dans la colonne « Unité de mesure ». Pour plus d’informations, consultez [Vérifier votre facture d’abonnement Azure](../../cost-management-billing/understand/review-individual-bill.md). 

## <a name="understand-your-usage-and-optimizing-your-pricing-tier"></a>Comprendre votre utilisation et optimiser votre niveau tarifaire
<a name="understand-your-usage-and-estimate-costs"></a>

Pour en savoir plus sur les tendances d’utilisation et choisir le niveau de tarification journal Analytics le plus économique, consultez **Utilisation et coûts estimés Log Analytics**. Cela montre la quantité de données collectées par chaque solution, la quantité de données conservées et une estimation de vos coûts pour chaque niveau de tarification en fonction des modèles récents d'ingestion de données. 

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="Utilisation et estimation des coûts":::

Pour explorer vos données plus en détail, sélectionnez l’icône en haut à droite d’un des graphiques sur la page **Utilisation et estimation des coûts**. Vous pouvez maintenant utiliser cette requête pour obtenir de plus amples informations sur votre utilisation.  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="Vue Journaux d’activité":::

La page **Utilisation et estimation des coûts** vous permet de consulter votre volume de données pour le mois en cours. Ce volume inclut toutes les données facturables reçues et conservées dans votre espace de travail Log Analytics.  
 
Les frais liés à Log Analytics sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure sont affichées dans la section **Facturation** du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="changing-pricing-tier"></a>Changement de niveau tarifaire

Pour modifier le niveau tarifaire Log Analytics de votre espace de travail :

1. Dans le Portail Azure, ouvrez **Utilisation et estimation des coûts** à partir de votre espace de travail où vous verrez une liste de chacun des niveaux tarifaires disponibles pour cet espace de travail.

2. Passez en revue les coûts estimés pour chacun des niveaux tarifaires. Cette estimation est basée sur les 31 derniers jours d’utilisation, de sorte qu’elle repose sur le fait que les 31 derniers jours sont représentatifs de votre utilisation habituelle. Dans l’exemple ci-dessous, vous pouvez voir de quelle manière, en fonction des modèles de données des 31 derniers jours, cet espace de travail coûterait moins cher avec le niveau Paiement à l’utilisation (no 1) par rapport au niveau d’engagement de 100 Go/jour (no 2).  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="Niveaux de tarification":::
    
3. Après avoir vérifié les coûts estimés en fonction des 31 derniers jours d’utilisation, et si vous décidez de modifier le niveau tarifaire, cliquez sur **Sélectionner**.  

### <a name="changing-pricing-tier-via-arm"></a>Changement de niveau tarifaire via ARM

Vous pouvez également [définir le niveau tarifaire via Azure Resource Manager](./resource-manager-workspace.md) en utilisant l’objet `sku` pour définir le niveau tarifaire et le `capacityReservationLevel` paramètre si le niveau tarifaire est `capacityresrvation`. (En savoir plus sur la [définition des propriétés de l’espace de travail via ARM](/azure/templates/microsoft.operationalinsights/2020-08-01/workspaces?tabs=json#workspacesku-object).) Voici un exemple de modèle Azure Resource Manager pour définir votre espace de travail sur un niveau d’engagement de 300 Go/jour (qui s’appelle `capacityreservation` dans Resource Manager). 

```
{
  "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "YourWorkspaceName",
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "location": "yourWorkspaceRegion",
      "properties": {
                    "sku": {
                      "name": "capacityreservation",
                      "capacityReservationLevel": 300
                    }
      }
    }
  ]
}
```

Pour utiliser ce modèle dans PowerShell, après avoir [installé le module Azure Az PowerShell](/powershell/azure/install-az-ps), connectez-vous à Azure en utilisant `Connect-AzAccount`, sélectionnez l’abonnement contenant votre espace de travail à l’aide de `Select-AzSubscription -SubscriptionId YourSubscriptionId`, puis appliquez le modèle (enregistré dans un fichier nommé template.json) :

```
New-AzResourceGroupDeployment -ResourceGroupName "YourResourceGroupName" -TemplateFile "template.json"
```

Pour définir le niveau tarifaire sur d’autres valeurs telles que Paiement à l’utilisation (appelé `pergb2018` pour la SKU), omettez la propriété `capacityReservationLevel`. En savoir plus sur la [création de modèles ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md), [l’ajout d’une ressource à votre modèle](../../azure-resource-manager/templates/template-tutorial-add-resource.md) et l’[application de modèles](../resource-manager-samples.md). 

### <a name="tracking-pricing-tier-changes"></a>Suivi des modifications du niveau tarifaire

Les modifications apportées au niveau de tarification d’un espace de travail sont enregistrées dans le [Journal d’activité](../essentials/activity-log.md) avec un événement avec l’opération nommée « Créer un espace de travail ». L’onglet **Historique des modifications** de l’événement affiche les niveaux tarifaires anciens et nouveaux à la ligne `properties.sku.name`.  Cliquez sur l’option « Journal d’activité » dans votre espace de travail pour afficher les événements dont l’étendue est limitée à un espace de travail particulier. Pour surveiller la modification du niveau tarifaire, vous pouvez créer une alerte pour l’opération « Créer un espace de travail ». 

## <a name="legacy-pricing-tiers"></a>Niveaux de tarification hérités

Les abonnements qui contenaient un espace de travail Log Analytics ou une ressource Application Insights le 2 avril 2018, ou qui sont liés à un Contrat Entreprise qui a débuté avant le 1er février 2019 et qui est toujours actif, continueront d’avoir accès à l’utilisation des niveaux tarifaires hérités : **Essai gratuit**, **Autonome (par Go)** et **Par nœud (OMS)** . Les espaces de travail du niveau tarifaire Essai gratuit présentent une ingestion des données quotidienne limitée à 500 Mo (à l’exception des types de données de sécurité collectés par [Azure Defender (Security Center)](../../security-center/index.yml)) et une conservation des données limitée à sept jours. Le niveau tarifaire Essai gratuit est conçu à des fins d’évaluation uniquement. Aucun Contrat de niveau de service n’est fourni pour le niveau Gratuit.  Les espaces de travail des niveaux tarifaires Autonome et Par nœud présentent une rétention configurable par l’utilisateur de 30 à 730 jours.

L’utilisation au niveau tarifaire Autonome est facturée en fonction du volume de données ingérées. Elle est rapporté dans le service **Log Analytics**, et le compteur est nommé « Data Analyzed » (Données analysées). 

Le niveau tarifaire par nœud est facturé par machine virtuelle (nœud) surveillée sur une granularité horaire. Pour chaque nœud analysé, 500 Mo de données non facturées par jour sont affectées à l’espace de travail. Cette allocation est calculée avec une granularité horaire et est agrégée chaque jour au niveau de l’espace de travail. Les données ingérées au-delà de l’allocation de données quotidienne agrégée sont facturées par Go comme dépassement de données. Sur votre facture, le service indiqué est **Insight and Analytics** pour l’utilisation de Log Analytics si l’espace de travail se trouve dans le niveau tarifaire Par nœud. L’utilisation est signalée sur trois compteurs :

- **Nœud** : il s’agit de l’utilisation du nombre de nœuds surveillés (machines virtuelles) en unités de nœud par mois.
- **Dépassement de données par nœud** : il s’agit du nombre de Go de données ingérées au-delà de l’allocation de données agrégées.
- **Données incluses par nœud** : il s’agit de la quantité de données ingérées qui a été couverte par l’allocation de données agrégées. Ce compteur est également utilisé lorsque l’espace de travail se trouve dans tous les niveaux de tarification pour afficher la quantité de données couvertes par le service Azure Defender (Security Center).

> [!TIP]
> Si votre espace de travail a accès au niveau tarifaire **Par nœud**, mais que vous vous demandez si ce serait moins cher dans un niveau Paiement à l’utilisation, vous pouvez [utiliser la requête ci-dessous](#evaluating-the-legacy-per-node-pricing-tier) pour obtenir facilement une suggestion. 

Les espaces de travail créés avant avril 2016 peuvent continuer à accéder aux niveaux tarifaires **Standard** et **Premium** d’origine qui ont une durée fixe de conservation des données de 30 jours et 365 jours, respectivement. Il n’est pas possible de créer des espaces de travail avec les niveaux tarifaires **Standard** ou **Premium**. De plus, si un espace de travail est retiré de ces niveaux tarifaires, il ne peut pas y être inclus de nouveau. Les compteurs d’ingestion de données sur votre facture Azure pour ces niveaux hérités sont appelés « Données analysées ».

### <a name="legacy-pricing-tiers-and-azure-defender-security-center"></a>Niveaux tarifaires hérités et Azure Defender (Security Center)

Il existe également des comportements entre l’utilisation de niveaux Log Analytics hérités et la façon dont l’utilisation est facturée pour [Azure Defender (Security Center)](../../security-center/index.yml). 

- Si l’espace de travail se trouve dans le niveau Standard ou Premium hérité, Azure Defender est facturé uniquement pour l’ingestion des données Log Analytics, et non par nœud.
- Si l’espace de travail se trouve dans le niveau Par nœud hérité, Azure Defender est facturé selon le [modèle de tarification actuel basé sur les nœuds d’Azure Defender](https://azure.microsoft.com/pricing/details/security-center/). 
- Dans d’autres niveaux tarifaires (notamment les niveaux d’engagement), si Azure Defender a été activé avant le 19 juin 2017, Azure Defender est facturé uniquement pour l’ingestion des données Log Analytics. Sinon, Azure Defender est facturé selon le modèle de tarification actuel basé sur les nœuds d’Azure Defender.

Pour plus d’informations sur les limitations de niveau tarifaire, consultez [Abonnement Azure et limites, quotas et contraintes de service](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).

Aucun des niveaux tarifaires hérités n’a de tarification régionale.  

> [!NOTE]
> Pour utiliser les droits que vous obtenez à l’achat de la suite OMS E1, OMS E2 ou du module complémentaire OMS pour System Center, sélectionnez le niveau tarifaire *Par nœud* de Log Analytics.

## <a name="log-analytics-and-azure-defender-security-center"></a>Log Analytics et Azure Defender (Security Center)

La facturation d’[Azure Defender pour les serveurs (Security Center)](../../security-center/index.yml) est étroitement liée à la facturation de Log Analytics. Azure Defender [applique une facturation au nombre de services surveillés](https://azure.microsoft.com/pricing/details/azure-defender/) et fournit une allocation de données de 500 Mo/serveur/jour qui est appliquée au sous-ensemble suivant de [types de données de sécurité](/azure/azure-monitor/reference/tables/tables-category#security) (WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus) et aux types de données Update et UpdateSummary lorsque la solution Update Management n’est pas exécutée sur l’espace de travail ou que le ciblage de solution est activé ([en savoir plus](../../security-center/security-center-pricing.md#what-data-types-are-included-in-the-500-mb-data-daily-allowance)). Le nombre de serveurs surveillés est calculé sur une granularité horaire. Les contributions quotidiennes à l’allocation de données de chaque serveur surveillé sont agrégées au niveau de l’espace de travail. Si l’espace de travail est au niveau tarifaire hérité Par nœud, les allocations d’Azure Defender et de Log Analytics sont combinées et appliquées conjointement à toutes les données ingérées facturables.  

## <a name="change-the-data-retention-period"></a>Changer la période de rétention des données

Les étapes suivantes décrivent la configuration de la durée de conservation des données de journal dans votre espace de travail. La conservation des données au niveau de l’espace de travail peut être configurée entre 30 et 730 jours (2 ans) pour tous les espaces de travail, sauf si elle utilise le niveau tarifaire Essai gratuit hérité. La rétention de types de données individuels peut être définie sur une valeur minimale de 4 jours. [En savoir plus](https://azure.microsoft.com/pricing/details/monitor/) sur la tarification pour une conservation des données plus longue. Pour conserver les données au-delà de 730 jours, envisagez d’utiliser une [Exportation des données de l’espace de travail Log Analytics](logs-data-export.md).

### <a name="workspace-level-default-retention"></a>Conservation par défaut au niveau de l’espace de travail

Pour définir la durée de conservation par défaut pour votre espace de travail :
 
1. Dans le Portail Azure, à partir de votre espace de travail, sélectionnez **Utilisation et estimation des coûts** dans le volet gauche.
2. En haut de la page **Utilisation et estimation des coûts**, sélectionnez **Conservation des données**.
3. Dans le volet, déplacez le curseur pour augmenter ou diminuer le nombre de jours, puis sélectionnez **OK**.  Si vous avez opté pour le niveau *gratuit*, vous ne pouvez pas modifier la période de conservation des données et vous devez passer au niveau payant afin de contrôler ce paramètre.

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="Changer le paramètre de conservation des données de l’espace de travail":::

Lorsque la durée de conservation des données est raccourcie, il existe une période de grâce de plusieurs jours avant la suppression des données antérieures au nouveau paramètre de conservation. 

La page **Conservation des données** permet de paramétrer la conservation sur 30, 31, 60, 90, 120, 180, 270, 365, 550 et 730 jours. Si un autre paramétrage est requis, vous pouvez le configurer dans [Azure Resource Manager](./resource-manager-workspace.md) à l’aide du paramètre `retentionInDays`. Lorsque vous définissez la période de conservation des données sur 30 jours, vous pouvez déclencher une suppression définitive et immédiate des données antérieures à l’aide du paramètre `immediatePurgeDataOn30Days` (en éliminant la période de grâce). Cela peut être utile pour les scénarios liés à la conformité où une suppression immédiate des données est impérative. Cette fonctionnalité de purge immédiate est exposée uniquement via Azure Resource Manager. 

Les espaces de travail dont la période de rétention est de 30 jours peuvent en réalité conserver des données pendant 31 jours. S’il est impératif que les données soient conservées pendant seulement 30 jours, utilisez Azure Resource Manager pour définir la période de rétention sur 30 jours et avec le paramètre `immediatePurgeDataOn30Days`.  

Par défaut, deux types de données, `Usage` et `AzureActivity`, sont conservées gratuitement pendant un minimum de 90 jours. Si la rétention de l’espace de travail augmente au-delà de 90 jours, la rétention de ces types de données est également augmentée. Ces types de données sont également exempts des frais d’ingestion de données. 

Les types de données provenant des ressources Application Insights basées sur l’espace de travail (`AppAvailabilityResults`, `AppBrowserTimings`, `AppDependencies`, `AppExceptions`, `AppEvents`, `AppMetrics`, `AppPageViews`, `AppPerformanceCounters`, `AppRequests`, `AppSystemEvents` et `AppTraces`) sont également conservés 90 jours sans frais par défaut. Leur rétention peut être ajustée à l’aide de la fonctionnalité de rétention par type de données. 

L’[API de purge](/rest/api/loganalytics/workspacepurge/purge) Log Analytics n’affecte pas la facturation de la conservation et est destinée à être utilisée dans des cas très limités. Pour réduire votre facture de rétention, la période de rétention doit être réduite pour l’espace de travail ou pour des types de données spécifiques. Découvrez-en plus sur la gestion des [données personnelles stockées dans Log Analytics et Application Insights](./personal-data-mgmt.md).

### <a name="retention-by-data-type"></a>Durée de conservation par type de données

Il est également possible de spécifier des paramètres de rétention différents pour des types de données individuels allant de 4 à 730 jours (à l’exception des espaces de travail du niveau tarifaire essai gratuit hérité), qui remplacent la rétention par défaut au niveau de l’espace de travail. Chaque type de données est une sous-ressource de l’espace de travail. Par exemple, la table SecurityEvent peut être traitée dans [Azure Resource Manager](../../azure-resource-manager/management/overview.md) comme suit :

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Notez que le type de données (table) est sensible à la casse. Pour obtenir les paramètres actuels de rétention par type de données d’un type de données particulier (dans cet exemple, SecurityEvent), utilisez :

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> La rétention n’est retournée pour un type de données que si elle a été explicitement définie. Les types de données dont la rétention n’a pas été explicitement définie (et qui héritent donc de la rétention de l’espace de travail) ne retournent rien suite à cet appel. 

Afin d'obtenir les paramètres actuels de rétention par type de données de tous les types de données de votre espace de travail pour lesquels la rétention par type de données a été définie, il suffit d'omettre le type de données spécifique, par exemple :

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Pour définir la durée de conservation d’un type de données particulier (dans cet exemple, SecurityEvent) à 730 jours, utilisez :

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Les valeurs valides pour `retentionInDays` sont comprises entre 4 et 730.

Les types de données `Usage` et `AzureActivity` ne peuvent pas être définis avec une durée de conservation personnalisée. Ils prennent la durée maximale de conservation de l’espace de travail par défaut ou 90 jours. 

Un excellent outil pour se connecter directement à Azure Resource Manager pour définir la durée de conservation par type de données est l’outil OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Découvrez plus en détail ARMClient dans les articles de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) et Daniel Bowbyes.  Voici un exemple d’utilisation d’ARMClient, définissant une durée de conservation de 730 jours pour les données SecurityEvent :

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> La définition d’une durée de conservation sur des types de données individuels peut être utilisée pour réduire vos coûts de conservation de données. Pour les données collectées à partir d’octobre 2019 (date de publication de cette fonctionnalité), la réduction de la durée de conservation de certains types de données peut réduire vos coûts de conservation à terme. Pour les données collectées précédemment, la définition d’une durée de conservation inférieure pour un type individuel n’affecte pas vos coûts de conservation.  

## <a name="manage-your-maximum-daily-data-volume"></a>Gérer votre volume de données maximal quotidien

Vous pouvez configurer une limite quotidienne et restreindre l’ingestion quotidienne de votre espace de travail, mais soyez vigilant, car votre objectif n’est pas d’atteindre la limite quotidienne. Si vous l’atteignez, vous perdrez des données pour le reste de la journée, ce qui peut impacter les autres services et solutions Azure dont les fonctionnalités dépendent de la disponibilité de données à jour dans l’espace de travail. Ces fonctionnalités peuvent correspondre, par exemple, à votre capacité à observer et à recevoir des alertes lorsque les conditions d’intégrité des ressources de service informatique sont impactées. La limite quotidienne est destinée à être utilisée comme un moyen de gérer une **augmentation inattendue** du volume de données de vos ressources managées ou lorsque vous souhaitez limiter les frais non planifiés de votre espace de travail. Il n’est pas adéquat de définir une limite quotidienne pour à respecter chaque jour dans un espace de travail.

Chaque espace de travail a sa limite quotidienne appliquée à une heure différente de la journée. L’heure de réinitialisation est indiquée sur la page **Limite quotidienne** (voir ci-dessous). L’heure de réinitialisation ne peut pas être configurée. 

Peu après que cette limite quotidienne est atteinte, la collecte des types de données facturables s’arrête pour le reste de la journée. La latence inhérente à l’application de la limite quotidienne signifie que la limite n’est pas appliquée au niveau de limite quotidienne spécifiée. Une bannière d’avertissement s’affiche en haut de la page de l’espace de travail Log Analytics sélectionné, et un événement d’opération est envoyé vers la table *Opération* dans la catégorie **LogManagement**. La collecte de données reprend après l’heure de réinitialisation définie dans *La limite quotidienne est fixée à*. Nous vous recommandons de définir une règle d’alerte en fonction de cet événement d’opération, configuré pour avertir lorsque la limite de données quotidienne a été atteinte. Pour plus d’informations, consultez [Alerte lorsque la limite quotidienne est atteinte](#alert-when-daily-cap-is-reached). 

> [!NOTE]
> La limite quotidienne ne peut pas arrêter la collecte de données, car précisément le niveau de cap spécifié et certaines données excédentaires sont attendus, en particulier si l’espace de travail reçoit des volumes de données élevés. Les données collectées au-delà de la capacité sont toujours facturées. Pour une requête qui est utile pour l’étude du comportement de la limite quotidienne, consultez la section [Voir l’effet de la limite quotidienne](#view-the-effect-of-the-daily-cap) de cet article. 

> [!WARNING]
> La limite quotidienne n’arrête pas la collecte des types de données **WindowsEvent**, **SecurityAlert**, **SecurityBaseline**, **SecurityBaselineSummary**, **SecurityDetection**, **SecurityEvent**, **WindowsFirewall**, **MaliciousIPCommunication**, **LinuxAuditLog**, **SysmonEvent**, **ProtectionStatus**, **Update** et **UpdateSummary**, à l’exception des espaces de travail dans lesquels Azure Defender (Security Center) a été installé avant le 19 juin 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifier la limite de données quotidienne à définir

Pour comprendre les tendances d’ingestion des données et la limite quotidienne de volume à définir, consultez [Utilisation et estimation des coûts Log Analytics](../usage-estimated-costs.md). Considérez-le avec précaution, car vous ne pouvez pas surveiller vos ressources une fois la limite atteinte. 

### <a name="set-the-daily-cap"></a>Définir le plafond quotidien

Les étapes suivantes décrivent la configuration d’une limite pour gérer le volume des données ingérées quotidiennement par l’espace de travail Log Analytics.  

1. À partir de votre espace de travail, sélectionnez **Utilisation et estimation des coûts** dans le volet gauche.
2. Dans la page **Utilisation et estimation des coûts** de l’espace de travail sélectionné, sélectionnez **Limite de données** en haut de la page. 
3. Par défaut, la **Limite de données** est définie sur **Désactiver**. Cliquez sur **Activer** pour l’activer, puis définissez la limite de volume de données en Go/jour.

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Configurer la limite de données dans Log Analytics":::
    
Vous pouvez utiliser Azure Resource Manager pour configurer la limite quotidienne. Pour la configurer, définissez le paramètre `dailyQuotaGb` sous `WorkspaceCapping` comme décrit dans [Espaces de travail - Créer ou mettre à jour](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping). 

Vous pouvez effectuer le suivi des modifications apportées à la limite quotidienne à l’aide de cette requête :

```kusto
_LogOperation | where Operation == "Workspace Configuration" | where Detail contains "Daily quota"
```

En savoir plus sur la fonction [_LogOperation](./monitor-workspace.md). 

### <a name="view-the-effect-of-the-daily-cap"></a>Voir l’effet de la limite quotidienne

Pour afficher l’effet de la limite quotidienne, il est important de tenir compte des types de données de sécurité qui ne sont pas inclus dans la limite quotidienne et de l’heure de réinitialisation de votre espace de travail. L’heure de réinitialisation de la limite quotidienne est visible dans la page **Limite quotidienne**. Vous pouvez utiliser la requête suivante pour effectuer le suivi des volumes de données soumis à la limite quotidienne entre les réinitialisations de limite quotidienne. Dans cet exemple, l’heure de réinitialisation de l’espace de travail est 14:00. Vous devez la mettre à jour pour votre espace de travail.

```kusto
let DailyCapResetHour=14;
Usage
| where DataType !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) // Quantity in units of MB
| render areachart  
```
Ajoutez les types de données `Update` et `UpdateSummary` à la ligne `where Datatype` lorsque la solution Update Management n’est pas exécutée sur l’espace de travail ou lorsque le ciblage de solution est activé ([en savoir plus](../../security-center/security-center-pricing.md#what-data-types-are-included-in-the-500-mb-data-daily-allowance).)


### <a name="alert-when-daily-cap-is-reached"></a>Alerte lorsque la limite quotidienne est atteinte

Le portail Azure présente un indice visuel lorsque le seuil limite des données est atteint, mais ce comportement n’est pas nécessairement en harmonie avec la façon dont vous gérez les problèmes opérationnels exigeant une attention immédiate. Pour recevoir une notification d’alerte, vous pouvez créer une règle d’alerte dans Azure Monitor. Pour en savoir plus, reportez-vous à l’article sur [la création, l’affichage et la gestion des alertes](../alerts/alerts-metric.md).

Pour vous aider à démarrer, voici les paramètres que nous recommandons pour l’alerte interrogeant la table `Operation` à l’aide de la fonction `_LogOperation` ([en savoir plus](./monitor-workspace.md)). 

- Cible : sélectionnez votre ressource Log Analytics.
- Critères : 
   - Nom du signal : Recherche personnalisée dans les journaux
   - Requête de recherche : `_LogOperation | where Operation =~ "Data collection stopped" | where Detail contains "OverQuota"`
   - Basé sur : Nombre de résultats
   - Condition : Supérieur à
   - Seuil : 0
   - Période : 5 (minutes)
   - Fréquence : 5 (minutes)
- Nom de la règle d'alerte : limite de données quotidienne atteinte
- Gravité : avertissement (Sev 1)

Une fois que l’alerte est définie et que la limite est atteinte, l’alerte est déclenchée et effectue la réponse définie dans le groupe d’actions. Il peut informer votre équipe des manières suivantes :

- E-mails et SMS
- Actions automatisées à l’aide de webhooks
- Runbooks Azure Automation
- [Intégration à une solution ITSM externe](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Résolution des problèmes à l’origine d’une utilisation plus importante que prévu

Une utilisation plus importante est due à l’un ou plusieurs des éléments suivants :
- Plus de nœuds que prévu envoient des données à l’espace de travail Log Analytics. Pour plus d’informations, consultez la section [Présentation des nœuds qui envoient des données](#understanding-nodes-sending-data) de cet article.
- Plus de données que prévu sont envoyées à l’espace de travail Log Analytics (peut-être parce que l’on commence à utiliser une nouvelle solution ou que l’on modifie la configuration d’une solution existante). Pour plus d’informations, consultez la section [Présentation du volume de données ingéré](#understanding-ingested-data-volume) de cet article.

Si vous observez une ingestion des données élevée signalée à l’aide des enregistrements `Usage` (voir la section [Volume de données par solution](#data-volume-by-solution)), mais que vous n’observez pas les mêmes résultats en additionnant `_BilledSize` directement sur le [type de données](#data-volume-for-specific-events), il est possible que vous ayez des données importantes arrivées tardivement. Pour plus d’informations sur ce diagnostic, consultez la section [Données arrivant en retard](#late-arriving-data) de cet article. 

### <a name="log-analytics-workspace-insights"></a>Log Analytics Workspace Insights

Comprendre vos volumes de données dans l’onglet **Usage** du [classeur Log Analytics Workspace Insights](log-analytics-workspace-insights-overview.md). Sur le **Tableau de bord Utilisation**, vous pouvez facilement voir :
- Les tables de données qui ingèrent le plus de volume de données dans la table principale  
- Les principales ressources qui apportent des données 
- La tendance de l’ingestion des données

Vous pouvez basculer sur les **requêtes supplémentaires** pour facilement exécuter d’autres requêtes plus utiles à la compréhension des modèles de données. 

En savoir plus sur les [capacités de l’onglet Usage](log-analytics-workspace-insights-overview.md#usage-tab). 

Bien que ce classeur puisse répondre à la plupart des questions sans même avoir à exécuter une requête, pour répondre à des questions plus spécifiques ou pour effectuer des analyses plus approfondies, les requêtes dans les deux sections suivantes vous aideront à commencer. 

## <a name="understanding-nodes-sending-data"></a>Présentation des nœuds qui envoient des données

Pour plus d’informations sur le nombre de nœuds qui ont signalé quotidiennement des pulsations de l’agent au cours du mois précédent, utilisez cette requête :

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Pour obtenir le nombre de nœuds envoyant des données au cours des dernières 24 heures, utilisez cette requête : 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Pour obtenir la liste des nœuds qui envoient des données (et le volume de données envoyées par chacun d’eux), utilisez cette requête :

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>Nœuds facturés par le niveau tarifaire hérité Par nœud

Le [niveau tarifaire hérité Par nœud](#legacy-pricing-tiers) facture les nœuds avec une granularité horaire et ne compte pas non plus les nœuds qui envoient uniquement un ensemble de types de données de sécurité. Son nombre quotidien de nœuds devrait être semblable à la requête suivante :

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

Le nombre d’unités sur votre facture est exprimé en unités nœuds par mois, ce qui est représenté par `billableNodeMonthsPerDay` dans la requête. Si la solution Update Management est installée sur l’espace de travail, ajoutez les types de données **Update** et **UpdateSummary** à la liste dans la clause WHERE de la requête ci-dessus. Enfin, il existe une complexité supplémentaire dans l’algorithme de facturation réel lorsque le ciblage de solution utilisé n’est pas représenté dans la requête ci-dessus. 


> [!TIP]
> Utilisez ces requêtes `find` avec parcimonie, car les analyses exécutées sur différents types de données sont [gourmandes en ressources](./query-optimization.md#query-performance-pane). Si vous n’avez pas besoin des résultats **par ordinateur**, interrogez le type de données **Usage** (voir ci-dessous).

## <a name="understanding-ingested-data-volume"></a>Présentation du volume de données ingéré

Sur la page **Utilisation et estimation des coûts**, le graphique *Ingestion de données par solution* montre le volume total des données envoyées et la quantité envoyée par chaque solution. Vous pouvez dégager des tendances, par exemple si l’utilisation des données globales (ou l’utilisation par une solution particulière) augmente, reste stable ou diminue. 

### <a name="data-volume-for-specific-events"></a>Volume de données pour des événements spécifiques

Pour examiner la taille des données ingérées pour un ensemble particulier d’événements, vous pouvez interroger la table spécifique (`Event` dans cet exemple), puis limiter la requête aux événements intéressants (dans cet exemple, l’ID d’événement 5145 ou 5156) :

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Notez que la clause `where _IsBillable = true` exclut les types de données de certaines solutions pour lesquels il n'existe aucun frais d'ingestion. [En savoir plus](./log-standard-columns.md#_isbillable) sur `_IsBillable`.

### <a name="data-volume-by-solution"></a>Volume de données par solution

La requête utilisée pour afficher le volume de données facturable par solution au cours du dernier mois (à l’exception du dernier jour partiel) peut être créée en utilisant le type de données [Utilisation](/azure/azure-monitor/reference/tables/usage) de la façon suivante :

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

La clause avec `TimeGenerated` permet uniquement de s’assurer que l’expérience de requête dans le portail Azure va au-delà des 24 heures par défaut. Quand vous utilisez le type de données **Usage**, `StartTime` et `EndTime` représentent la période pour laquelle les résultats sont présentés. 

### <a name="data-volume-by-type"></a>Volume de données par type

Vous pouvez approfondir pour afficher les tendances par type de données :

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

Ou pour afficher un tableau par solution et par type pour le mois précédent,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Volume de données par ordinateur

Le type de données **Usage** n’inclut pas d’informations au niveau de l’ordinateur. Pour voir la **taille** des données ingérées facturables par ordinateur, utilisez la [propriété](./log-standard-columns.md#_billedsize) **_BilledSize**, qui fournit la taille en octets :

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer, Type
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes desc nulls last
```

La **propriété** [_IsBillable](./log-standard-columns.md#_isbillable) spécifie si les données ingérées occasionnent des frais. Le type **Usage** est omis, car il s’agit uniquement d’une analyse des tendances des données. 

Pour afficher le **nombre** d'événements facturables ingérés par ordinateur, utilisez 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount desc nulls last
```

> [!TIP]
> Utilisez ces requêtes `find` avec parcimonie, car les analyses exécutées sur différents types de données sont [gourmandes en ressources](./query-optimization.md#query-performance-pane). Si vous n’avez pas besoin des résultats **par ordinateur**, interrogez le type de données **Usage**.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume de données par ressource Azure, groupe de ressources ou abonnement

Pour les données provenant de nœuds hébergés dans Azure, vous pouvez obtenir la **taille** des données ingérées __par ordinateur__. Utilisez la [propriété](./log-standard-columns.md#_resourceid) _ResourceId, qui fournit le chemin d’accès complet à la ressource :

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Pour les données provenant de nœuds hébergés dans Azure, vous pouvez obtenir la **taille** des données ingérées __par abonnement Azure__ via la propriété **_SubscriptionId** comme suit :

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, _SubscriptionId
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _SubscriptionId | sort by BillableDataBytes nulls last
```

Pour récupérer le volume de données par groupe de ressources, vous pouvez analyser **_ResourceId** :

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

Si besoin, vous pouvez aussi analyser plus en détail **_ResourceId** :

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> Utilisez ces requêtes `find` avec parcimonie, car les analyses exécutées sur différents types de données sont [gourmandes en ressources](./query-optimization.md#query-performance-pane). Si vous n’avez pas besoin des résultats par abonnement, groupe de ressources ou nom de ressource, interrogez le type de données **Usage**.

> [!WARNING]
> Certains champs du type de données **Usage**, bien que faisant partie du schéma, sont maintenant déconseillés et leurs valeurs ne sont plus fournies. Il s’agit de **Computer** et des champs liés à l’ingestion (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** et **AverageProcessingTimeMs**).

## <a name="late-arriving-data"></a>Données arrivant en retard   

Des situations particulières peuvent survenir lorsque des données sont ingérées avec d’anciens timestamps. Par exemple, si un agent ne peut pas communiquer avec Log Analytics en raison d’un problème de connectivité ou si un hôte a une date/heure incorrecte. Cela peut se manifester par une divergence apparente entre les données ingérées signalées par le type de données **Usage** et une requête additionnant **_BilledSize** sur les données brutes pour un jour particulier spécifié par **TimeGenerated**, le timestamp au moment où l’événement a été généré.

Pour diagnostiquer les problèmes de données arrivant en retard, utilisez la colonne **_TimeReceived** ([en savoir plus](./log-standard-columns.md#_timereceived)) en plus de la colonne **TimeGenerated**. **_TimeReceived** correspond à l’heure de réception de l’enregistrement par le point d’ingestion Azure Monitor dans le cloud Azure. Par exemple, en utilisant les enregistrements **Usage**, vous avez observé des volumes élevés de données **W3CIISLog** ingérées le 2 mai 2021. Voici une requête identifie les timestamps sur ces données ingérées : 

```Kusto
W3CIISLog
| where TimeGenerated > datetime(1970-01-01)
| where _TimeReceived >= datetime(2021-05-02) and _TimeReceived < datetime(2021-05-03) 
| where _IsBillable == true
| summarize BillableDataMB = sum(_BilledSize)/1.E6 by bin(TimeGenerated, 1d)
| sort by TimeGenerated asc 
```

L’instruction `where TimeGenerated > datetime(1970-01-01)` sert simplement à fournir un indice à l’interface utilisateur de Log Analytics pour l’examen de toutes les données.  

## <a name="querying-for-common-data-types"></a>Interrogation de types de données courants

Pour explorer plus en détail la source de données d’un type de données particulier, voici quelques exemples de requêtes :

+ Ressources **Application Insights basées sur l’espace de travail**
  - En savoir plus dans [Gérer l’utilisation et les coûts pour Application Insights](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
+ une solution de **sécurité**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ une solution de **gestion des journaux**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ le type de données **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ le type de données **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ le type de données **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ le type de données **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>Conseils pour réduire le volume de données

Ce tableau présente quelques suggestions pour réduire le volume de journaux d’activité collectés.

| Source du volume de données important | Comment réduire le volume de données |
| -------------------------- | ------------------------- |
| Règles de collecte des données      | L'[agent Azure Monitor](../agents/azure-monitor-agent-overview.md) utilise des règles de collecte de données pour gérer la collecte de données. Vous pouvez [limiter la collecte de données](../agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries) à l’aide de requêtes XPath personnalisées. | 
| Container Insights         | [Configurez Container Insights](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost) de manière à collecter uniquement les données requises. |
| Azure Sentinel | Examinez les [sources de données Sentinel](../../sentinel/connect-data-sources.md) que vous avez récemment activées en tant que sources de volume de données supplémentaire. [En savoir plus](../../sentinel/azure-sentinel-billing.md) sur les coûts et la facturation d’Azure Sentinel. |
| Événements de sécurité            | Sélectionnez [les événements de sécurité courants ou minimaux](../../security-center/security-center-enable-data-collection.md#data-collection-tier). <br> Modifier la stratégie d’audit de sécurité pour collecter les événements nécessaires uniquement. Plus particulièrement, examinez la nécessité de collecter des événements pour : <br> - [auditer la plateforme de filtrage](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)). <br> - [auditer le registre](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10)). <br> - [auditer le système de fichiers](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10)). <br> - [auditer l’objet de noyau](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10)). <br> - [auditer la manipulation de handle](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10)). <br> - auditer le stockage amovible |
| Compteurs de performance       | Modifiez la [configuration du compteur de performances](../agents/data-sources-performance-counters.md) de façon à : <br> - Réduire la fréquence de collecte. <br> - Réduire le nombre de compteurs de performance. |
| Journaux d’événements                 | Modifiez la [configuration du journal d’événements](../agents/data-sources-windows-events.md) de façon à : <br> - Réduire le nombre de journaux des événements collectés. <br> - Collecter uniquement les niveaux d’événement requis Par exemple, ne collectez pas les événements de niveau *Informations*. |
| syslog                     | Modifiez la [configuration du syslog](../agents/data-sources-syslog.md) de façon à : <br> - Réduire le nombre d’installations collectées. <br> - Collecter uniquement les niveaux d’événement requis Par exemple, ne collectez pas les événements de niveau *Informations* et *Débogage*. |
| AzureDiagnostics           | Modifiez la [collecte des journaux de ressources](../essentials/diagnostic-settings.md#create-in-azure-portal) pour : <br> - Réduire le nombre de journaux d’activité d’envoi de ressources à Log Analytics. <br> - Collecter uniquement les journaux d’activité nécessaires. |
| Données de solution d’ordinateurs n’ayant pas besoin de la solution | Utilisez le [ciblage de solution](../insights/solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement. |
| Application Insights | Passer en revue les options de [gestion du volume de données Application Insights](../app/pricing.md#managing-your-data-volume). |
| [SQL Analytics](../insights/azure-sql.md) | Utilisez [Set-AzSqlServerAudit](/powershell/module/az.sql/set-azsqlserveraudit) pour ajuster les paramètres d’audit. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Obtention des nœuds facturés au niveau tarifaire Par nœud

Pour obtenir une liste des ordinateurs qui seront facturés en tant que nœuds si l’espace de travail se trouve au niveau tarifaire hérité Par nœud, recherchez les nœuds qui envoient des **types de données facturés** (certains types de données sont gratuits). Pour ce faire, utilisez la [propriété _IsBillable](./log-standard-columns.md#_isbillable) et utilisez le champ tout à gauche du nom de domaine complet. Vous obtenez alors le nombre d'ordinateurs dont les données sont facturées à l'heure (ce qui correspond à la granularité à laquelle les nœuds sont comptés et facturés) :

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Obtention du nombre de nœuds Security et Automation

Pour afficher le nombre de nœuds Security distincts, vous pouvez utiliser la requête :

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Pour afficher le nombre de nœuds Automation distincts, utilisez la requête :

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Évaluation du niveau tarifaire hérité Par nœud

Il est souvent difficile pour les clients de décider si les espaces de travail ayant accès au niveau tarifaire hérité **Par nœud** sont mieux dans ce niveau ou dans l’une des offres actuelles, **Paiement à l’utilisation** ou **Niveau d’engagement**.  Cela implique de bien comprendre le compromis entre le coût fixe par nœud supervisé dans le niveau de tarification Par nœud et son allocation de données incluse de 500 Mo/nœud/jour, et le coût supporté en payant simplement les données ingérées dans le niveau Paiement à l’utilisation (Par Go). 

Pour faciliter cette évaluation, vous pouvez utiliser la requête suivante pour obtenir une recommandation sur le niveau tarifaire optimal en fonction des modèles d’utilisation d’un espace de travail. Cette requête examine les nœuds supervisés et les données ingérées dans un espace de travail au cours des sept derniers jours, et elle évalue pour chaque jour le niveau de tarification qui aurait été optimal. Pour utiliser la requête, vous devez :

- Spécifier si l’espace de travail utilise Azure Defender (Security Center) en définissant **workspaceHasSecurityCenter** sur **vrai** ou **faux**. 
- Mettre à jour les tarifs si vous avez des remises spécifiques.
- Spécifier le nombre de jours à examiner et à analyser en définissant **daysToEvaluate**. Cela est utile si la requête prend trop de temps pour essayer de consulter sept jours de données. 

Voici la requête de recommandation de niveau tarifaire :

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CommitmentTier100Price = 196.; // Enter your price for the 100 GB/day commitment tier
let CommitmentTier200Price = 368.; // Enter your price for the 200 GB/day commitment tier
let CommitmentTier300Price = 540.; // Enter your price for the 300 GB/day commitment tier
let CommitmentTier400Price = 704.; // Enter your price for the 400 GB/day commitment tier
let CommitmentTier500Price = 865.; // Enter your price for the 500 GB/day commitment tier
let CommitmentTier1000Price = 1700.; // Enter your price for the 1000 GB/day commitment tier
let CommitmentTier2000Price = 3320.; // Enter your price for the 2000 GB/day commitment tier
let CommitmentTier5000Price = 8050.; // Enter your price for the 5000 GB/day commitment tier
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CommitmentTier100DailyCost = CommitmentTier100Price + max_of(billableGB - 100, 0.)* CommitmentTier100Price/100.
| extend CommitmentTier200DailyCost = CommitmentTier200Price + max_of(billableGB - 200, 0.)* CommitmentTier200Price/200.
| extend CommitmentTier300DailyCost = CommitmentTier300Price + max_of(billableGB - 300, 0.)* CommitmentTier300Price/300.
| extend CommitmentTier400DailyCost = CommitmentTier400Price + max_of(billableGB - 400, 0.)* CommitmentTier400Price/400.
| extend CommitmentTier500DailyCost = CommitmentTier500Price + max_of(billableGB - 500, 0.)* CommitmentTier500Price/500.
| extend CommitmentTier1000DailyCost = CommitmentTier1000Price + max_of(billableGB - 1000, 0.)* CommitmentTier1000Price/1000.
| extend CommitmentTier2000DailyCost = CommitmentTier2000Price + max_of(billableGB - 2000, 0.)* CommitmentTier2000Price/2000.
| extend CommitmentTier5000DailyCost = CommitmentTier5000Price + max_of(billableGB - 5000, 0.)* CommitmentTier5000Price/5000.
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CommitmentTier100DailyCost,CommitmentTier200DailyCost,
    CommitmentTier300DailyCost, CommitmentTier400DailyCost, CommitmentTier500DailyCost, CommitmentTier1000DailyCost, CommitmentTier2000DailyCost, CommitmentTier5000DailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CommitmentTier100DailyCost, "Commitment tier (100 GB/day)",
    MinCost == CommitmentTier200DailyCost, "Commitment tier (200 GB/day)",
    MinCost == CommitmentTier300DailyCost, "Commitment tier (300 GB/day)",
    MinCost == CommitmentTier400DailyCost, "Commitment tier (400 GB/day)",
    MinCost == CommitmentTier500DailyCost, "Commitment tier (500 GB/day)",
    MinCost == CommitmentTier1000DailyCost, "Commitment tier (1000 GB/day)",
    MinCost == CommitmentTier2000DailyCost, "Commitment tier (2000 GB/day)",
    MinCost == CommitmentTier5000DailyCost, "Commitment tier (5000 GB/day)",
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CommitmentTier100DailyCost, CommitmentTier200DailyCost, CommitmentTier300DailyCost, CommitmentTier400DailyCost, CommitmentTier500DailyCost, CommitmentTier1000DailyCost, CommitmentTier2000DailyCost, CommitmentTier5000DailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Cette requête n’est pas une réplication exacte de la façon dont l’utilisation est calculée, mais elle fournit des suggestions de niveau tarifaire dans la plupart des cas.  

> [!NOTE]
> Pour utiliser les droits que vous obtenez à l’achat de la suite OMS E1, OMS E2 ou du module complémentaire OMS pour System Center, sélectionnez le niveau tarifaire *Par nœud* de Log Analytics.

## <a name="create-an-alert-when-data-collection-is-high"></a>Créer une alerte lorsque la collection de données est volumineuse

Cette section décrit la procédure de création d’une alerte à l’aide des [Alertes de journal](../alerts/alerts-unified-log.md) Azure Monitor lorsque le volume de données au cours des dernières 24 heures a dépassé une quantité spécifiée. 

Pour déclencher une alerte si le volume de données ingérées facturables au cours des dernières 24 heures est supérieur à 50 Go : 

- **Définir la condition d’alerte** spécifiez votre espace de travail Log Analytics comme cible de la ressource.
- **Critères d’alerte** spécifiez les éléments suivants :
   - **Nom du signal** sélectionnez **Recherche de journal personnalisée**
   - **Requête de recherche** sur `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50`.  
   - La **logique d’alerte** est **basée sur** le *nombre de résultats* et **Condition** est *supérieur à* un **seuil**  de *0*
   - **Période** de *1440* minutes et **Fréquence des alertes** définie toutes les *1440* minutes pour s’exécuter une fois par jour.
- **Définir les détails de l’alerte** spécifiez les éléments suivants :
   - **Nom** défini sur *Billable data volume greater than 50 GB in 24 hours* (Volume de données facturables supérieur à 50 Go en 24 heures)
   - **Gravité** : *Avertissement*

Pour être informé lorsque l’alerte de journal correspond aux critères, spécifiez un [groupe d’actions](../alerts/action-groups.md) existant ou créez-en un.

Lorsque vous recevez une alerte, utilisez la procédure des sections précédentes pour résoudre les problèmes à l’origine d’une utilisation plus importante que prévu.

## <a name="data-transfer-charges-using-log-analytics"></a>Frais de transfert de données à l’aide de Log Analytics

L’envoi de données à Log Analytics peut occasionner des frais de bande passante. Mais ils sont limités aux machines virtuelles où un agent Log Analytics est installé et ne s’appliquent pas lors de l’utilisation des paramètres de diagnostic ou avec d’autres connecteurs intégrés à Azure Sentinel. Comme décrit dans la [page sur les tarifs de bande passante Azure](https://azure.microsoft.com/pricing/details/bandwidth/), le transfert de données entre des services Azure situés dans deux régions différentes est facturé en tant que transfert de données sortant au tarif normal. Le transfert de données entrantes est gratuit. Toutefois, ce coût est très modique par rapport aux coûts liés à l’ingestion de données de Log Analytics. Ainsi, la maîtrise des coûts pour Log Analytics doit s’appuyer sur le [volume de données ingérées](#understanding-ingested-data-volume). 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Dépannage si Log Analytics ne collecte plus de données

Si vous utilisez le niveau tarifaire hérité Gratuit et que vous avez envoyé plus de 500 Mo de données le même jour, la collecte de données s’arrête pour le reste de la journée. La limite quotidienne est la principale raison pour laquelle Log Analytics arrête la collecte de données ou des données semblent manquantes. Log Analytics crée un événement de type **Opération** lorsque la collecte de données démarre et s’arrête. Exécutez la requête suivante dans la recherche pour vérifier si vous atteignez la limite quotidienne et si des données sont manquantes : 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Lorsque la collecte de données s’arrête, **OperationStatus** a la valeur **Warning**. Lorsque la collecte de données démarre, **OperationStatus** a la valeur **Succeeded**. Le tableau suivant dresse la liste des raisons pour lesquelles la collecte de données s’arrête et suggère une action pour la reprendre.

|Raison pour laquelle la collecte s’arrête| Solution| 
|-----------------------|---------|
|La limite quotidienne de votre espace de travail a été atteinte|Attendez que la collecte redémarre automatiquement ou augmentez la limite du volume de données quotidien décrite dans la section Gérer le volume de données maximal quotidien. L’heure de réinitialisation de la limite quotidienne s’affiche sur la page **Limite quotidienne**. |
| Votre espace de travail a atteint le [débit d’ingestion de données](../service-limits.md#log-analytics-workspaces) | La limite par défaut du débit d’ingestion de données envoyées à partir de ressources Azure à l’aide des paramètres de diagnostic est d’environ 6 Go/min par espace de travail. Il s’agit d’une valeur approximative dans la mesure où la taille réelle peut varier d’un type de données à l’autre en fonction de la longueur du journal et de son taux de compression. Cette limite ne s’applique pas aux données envoyées à partir d’agents ou de l’API de collecteur de données. Si vous envoyez des données vers un espace de travail unique à un débit supérieur, certaines données sont supprimées et un événement est envoyé toutes les 6 heures à la table Operation de votre espace de travail tant que le seuil est dépassé. Si votre volume d’ingestion continue à dépasser la limite du débit ou si vous pensez l’atteindre bientôt, vous pouvez demander une augmentation de votre espace de travail en envoyant un e-mail à LAIngestionRate@microsoft.com ou en effectuant une demande de support. L’événement à rechercher indiquant une limite de débit d’ingestion de données peut être trouvé par la requête `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"`. |
|Limite quotidienne de niveau tarifaire hérité Gratuit atteinte |Attendez le jour suivant pour que la collecte redémarre automatiquement ou passez à un niveau tarifaire payant.|
|Abonnement Azure à l’état interrompu pour la raison suivante :<br> Fin de l’essai gratuit<br> Expiration du Pass Azure<br> Limite de dépense mensuelle atteinte (par exemple, sur un abonnement MSDN ou Visual Studio)|Passer à un abonnement payant<br> Supprimer la limite ou attendre sa réinitialisation|

Pour être averti quand la collecte de données s’arrête, utilisez les étapes décrites dans la section [Alerte lorsque la limite quotidienne est atteinte](#alert-when-daily-cap-is-reached). Utilisez les étapes décrites dans [Créer un groupe d’actions](../alerts/action-groups.md) pour configurer une action d’e-mail, de webhook ou de runbook pour la règle d’alerte. 

## <a name="limits-summary"></a>Synthèse des limites

D’autres limites de Log Analytics s’appliquent, dont certaines dépendent du niveau tarifaire de Log Analytics. Celles-ci sont documentées dans [Abonnement Azure et limites, quotas et contraintes de service](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).


## <a name="next-steps"></a>Étapes suivantes

- Consultez [Recherche de données dans les journaux Azure Monitor](../logs/log-query-overview.md) pour apprendre à utiliser le langage de recherche. Vous pouvez utiliser des requêtes de recherche pour effectuer des analyses supplémentaires sur les données d’utilisation.
- Utilisez les étapes décrites dans [Création d’une alerte de journal](../alerts/alerts-metric.md) pour être averti lorsqu’un critère de recherche est rempli.
- Utilisez le [ciblage de solution](../insights/solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement.
- Pour configurer une règle efficace de collecte d’événements, passez en revue [Stratégie de filtrage d’Azure Defender (Security Center)](../../security-center/security-center-enable-data-collection.md).
- Modifier la [configuration du compteur de performances](../agents/data-sources-performance-counters.md).
- Pour modifier vos paramètres de collecte d’événements, consultez [Configuration du journal des événements](../agents/data-sources-windows-events.md).
- Pour modifier vos paramètres de collecte de messages syslog, consultez [Configuration syslog](../agents/data-sources-syslog.md).
- Pour modifier vos paramètres de collecte de messages syslog, consultez [Configuration syslog](../agents/data-sources-syslog.md).
