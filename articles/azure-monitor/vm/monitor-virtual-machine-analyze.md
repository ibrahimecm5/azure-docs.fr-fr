---
title: 'Superviser les machines virtuelles avec Azure Monitor : Analyser les données de supervision'
description: Découvrez les différentes fonctionnalités d’Azure Monitor que vous pouvez utiliser pour analyser l’intégrité et les performances de vos machines virtuelles.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 9eb84e0b685a41cd0ead7aae1feb38c475465f83
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325669"
---
# <a name="monitor-virtual-machines-with-azure-monitor-analyze-monitoring-data"></a>Superviser les machines virtuelles avec Azure Monitor : Analyser les données de supervision
Cet article fait partie du scénario [Superviser les machines virtuelles et leurs charges de travail dans Azure Monitor](monitor-virtual-machine.md). Il décrit comment analyser les données de supervision de vos machines virtuelles une fois la configuration terminée.

Une fois que vous avez activé VM Insights sur vos machines virtuelles, les données sont disponibles pour analyse. Découvrez les différentes fonctionnalités d’Azure Monitor que vous pouvez utiliser pour analyser l’intégrité et les performances de vos machines virtuelles. Plusieurs de ces fonctionnalités offrent une expérience différente selon que vous analysez une seule machine ou plusieurs. Chaque expérience est décrite ici avec tout comportement unique de chaque fonctionnalité en fonction de l’expérience utilisée.

> [!NOTE]
> Cet article contient des conseils sur l’analyse des données collectées par Azure Monitor et VM Insights. Pour voir les données à configurer pour superviser les charges de travail exécutées sur les machines virtuelles, reportez-vous à [Superviser les charges de travail](monitor-virtual-machine-workloads.md).

## <a name="single-machine-experience"></a>Expérience sur une seule machine
Accédez à l’expérience d’analyse de machine unique à partir de la section **Supervision** du menu dans le portail Azure pour chaque machine virtuelle Azure et serveur avec Azure Arc. Ces options limitent les données que vous visualisez sur cette machine ou, au moins, en définissent un filtre initial. De cette façon, vous pouvez vous concentrer sur une machine particulière, afficher ses performances actuelles et ses tendances au fil du temps, et aider à identifier les problèmes susceptibles de se produire.

:::image type="content" source="media/monitor-virtual-machines/vm-menu.png" alt-text="Capture d’écran montrant l’analyse d’une machine virtuelle dans le portail Azure" lightbox="media/monitor-virtual-machines/vm-menu.png":::

- **Page Vue d’ensemble** : Sélectionnez l’onglet **Supervision** pour afficher les [métriques de plateforme](../essentials/data-platform-metrics.md) pour l’hôte de machine virtuelle. Vous bénéficiez d’un aperçu rapide de la tendance sur différentes périodes pour les métriques importantes, telles que le processeur, le réseau et le disque. Étant donné qu’il s’agit de métriques d’hôte, les compteurs du système d’exploitation invité, tels que la mémoire, ne sont pas inclus. Sélectionnez un graphique pour utiliser ces données dans l’[explorateur de métriques](../essentials/metrics-getting-started.md), où vous pouvez effectuer différentes agrégations et ajouter d’autres compteurs pour l’analyse.
- **Journal d’activité** : consultez les entrées du [journal d’activité](../essentials/activity-log.md#view-the-activity-log) qui sont filtrées pour la machine virtuelle actuelle. Utilisez ce journal pour afficher l’activité récente de la machine, notamment les modifications de sa configuration et le moment où elle a été arrêtée et démarrée. 
- **Insights** : ouvrez les [VM Insights](../vm/vminsights-overview.md) avec la carte pour la machine virtuelle actuelle sélectionnée. La carte vous indique les processus en cours d’exécution sur la machine, les dépendances sur les autres machines et les processus externes. Pour obtenir des détails sur l’utilisation de la vue cartographique pour une seule machine, consultez [Utiliser la fonctionnalité Carte de VM Insights pour comprendre les composants d’application](vminsights-maps.md#view-a-map-from-a-vm).

    Sélectionnez l’onglet **Performances** pour afficher les tendances des compteurs de performances critiques sur différentes périodes. Lorsque vous ouvrez VM Insights à partir du menu de la machine virtuelle, vous disposez également d’une table avec des métriques détaillées pour chaque disque. Pour obtenir des détails sur l’utilisation de la vue cartographique pour une seule machine, consultez [Performances des graphiques avec VM Insights](vminsights-performance.md#view-performance-directly-from-an-azure-vm). 

- **Alertes** : consultez les [alertes](../alerts/alerts-overview.md) de la machine virtuelle actuelle. Ces alertes utilisent uniquement la machine comme ressource cible, de sorte que d’autres alertes peuvent lui être associées. Vous devrez peut-être utiliser l’option **Alertes** dans le menu Azure Monitor pour afficher les alertes de toutes les ressources. Pour obtenir des détails, reportez-vous à [Superviser les machines virtuelles avec Azure Monitor – Alertes](monitor-virtual-machine-alerts.md).
- **Métriques** : ouvrez Metrics Explorer avec l’étendue définie sur la machine. Cette option est identique à la sélection de l’un des graphiques de performances dans la page **Vue d’ensemble**, à ceci près que la métrique n’est pas déjà ajoutée.
- **Paramètres de diagnostic** : activez et configurez l’[extension de diagnostic](../agents/diagnostics-extension-overview.md) pour la machine virtuelle actuelle. Cette option est différente de l’option **Paramètres de diagnostic** pour d’autres ressources Azure. Activez l’extension de diagnostic uniquement si vous devez envoyer des données à Azure Event Hubs ou au Stockage Azure.
- **Recommandations Advisor** : reportez-vous aux recommandations pour la machine virtuelle actuelle à partir d’[Azure Advisor](../../advisor/index.yml).
- **Journaux** : ouvrez [Log Analytics](../logs/log-analytics-overview.md) avec l’[étendue](../logs/scope.md) définie sur la machine virtuelle actuelle. Vous pouvez effectuer votre sélection parmi diverses requêtes existantes pour analyser les données de journal et de performances pour cette machine uniquement. 
- **Moniteur de connexion** : ouvrez le [moniteur de connexion Network Watcher](../../network-watcher/connection-monitor-overview.md) pour superviser les connexions entre la machine virtuelle actuelle et les autres machines virtuelles. 
- **Classeurs** : ouvrez la bibliothèque de classeurs avec les classeurs VM Insights pour les machines uniques. Pour obtenir la liste des classeurs VM Insights conçus pour des machines individuelles, consultez [Classeurs VM Insights](vminsights-workbooks.md#vm-insights-workbooks).

## <a name="multiple-machine-experience"></a>Expérience sur plusieurs machines
Accédez à l’expérience d’analyse de plusieurs machines à partir du menu **Superviser** dans le portail Azure pour chaque machine virtuelle Azure et serveur avec Azure Arc. Ces options vous permettent d’accéder à toutes les données pour que vous puissiez sélectionner les machines virtuelles que vous souhaitez comparer.

:::image type="content" source="media/monitor-virtual-machines/monitor-menu.png" alt-text="Capture d’écran montrant l’analyse de plusieurs machines virtuelles dans le portail Azure" lightbox="media/monitor-virtual-machines/monitor-menu.png":::

- **Journal d’activité** : consultez les entrées du [journal d’activité](../essentials/activity-log.md#view-the-activity-log) qui sont filtrées pour toutes les ressources. Créez un filtre pour un **type de ressource** de machines virtuelles ou de groupes de machines virtuelles identiques pour afficher les événements de toutes vos machines.
- **Alertes** : affichez les [alertes](../alerts/alerts-overview.md) de toutes les ressources, y compris les alertes liées aux machines virtuelles, mais associées à l’espace de travail. Créez un filtre pour un **type de ressource** de machines virtuelles ou de groupes de machines virtuelles identiques pour afficher les alertes de toutes vos machines. 
- **Métriques** : ouvrez [Metrics Explorer](../essentials/metrics-getting-started.md) sans étendue sélectionnée. Cette fonctionnalité est particulièrement utile lorsque vous souhaitez comparer des tendances sur plusieurs machines. Sélectionnez un abonnement ou un groupe de ressources pour ajouter rapidement un groupe de machines à analyser ensemble.
- **Journaux** : ouvrez [Log Analytics](../logs/log-analytics-overview.md) avec l’[étendue](../logs/scope.md) définie sur l’espace de travail. Vous pouvez effectuer votre sélection parmi diverses requêtes existantes pour analyser les données de journal et de performances pour toutes les machines. Vous pouvez également créer une requête personnalisée pour effectuer une analyse supplémentaire.
- **Classeurs** : ouvrez la bibliothèque de classeurs avec les classeurs VM Insights pour plusieurs machines. Pour obtenir la liste des classeurs VM Insights conçus pour plusieurs machines, consultez [Classeurs VM Insights](vminsights-workbooks.md#vm-insights-workbooks). 
- **Machines virtuelles** : ouvrez [VM Insights](../vm/vminsights-overview.md) avec l’onglet **Prise en main** ouvert. Cette action affiche toutes les machines de votre abonnement Azure et identifie celles qui sont supervisées. Utilisez cette vue pour intégrer des machines individuelles qui ne sont pas encore supervisées.

    Sélectionnez l’onglet **Performances** pour comparer les tendances des compteurs de performances critiques pour plusieurs machines sur différentes périodes. Sélectionnez toutes les machines d’un abonnement ou d’un groupe de ressources à inclure dans la vue. Pour obtenir des détails sur l’utilisation de la vue cartographique pour une seule machine, consultez [Performances des graphiques avec VM Insights](vminsights-performance.md#view-performance-directly-from-an-azure-vm).

    Sélectionnez l’onglet **Carte** pour afficher les processus en cours d’exécution sur les machines, les dépendances entre les machines et les processus externes. Sélectionnez toutes les machines d’un abonnement ou d’un groupe de ressources, ou inspectez les données d’une seule machine. Pour obtenir des détails sur l’utilisation de la vue cartographique pour plusieurs machines, consultez [Utiliser la fonctionnalité Carte de VM Insights pour comprendre les composants d’application](vminsights-maps.md#view-a-map-from-azure-monitor). 
 
## <a name="compare-metrics-and-logs"></a>Comparer les métriques et les journaux
Pour de nombreuses fonctionnalités d’Azure Monitor, vous n’avez pas besoin de comprendre les différents types de données utilisés et leur emplacement de stockage. Vous pouvez utiliser VM Insights, par exemple, sans savoir quelles données sont utilisées pour remplir la vue Performances, la vue cartographique et les classeurs. Vous vous concentrez simplement sur la logique que vous analysez. À mesure que vous approfondissez la question, vous devez comprendre la différence entre les [métriques](../essentials/data-platform-metrics.md) et les [journaux](../logs/data-platform-logs.md). Les différentes fonctionnalités d’Azure Monitor utilisent des types de données différents. Le type d’alerte que vous utilisez pour un scénario particulier dépend de la disponibilité de ces données dans un emplacement particulier.

Ce niveau de détail peut prêter à confusion si vous débutez avec Azure Monitor. Les informations suivantes vous aident à comprendre les différences entre les types de données :

- Toutes les données non numériques, telles que les événements, sont stockées dans des journaux. Les métriques peuvent inclure uniquement des données numériques échantillonnées selon des intervalles réguliers.
- Les données numériques peuvent être stockées dans des métriques et des journaux pour pouvoir être analysées de différentes façons et prendre en charge différents types d’alertes.
- Les données de performances du système d’exploitation invité sont envoyées aux journaux par VM Insights à l’aide de l’agent Log Analytics.
- Les données de performances du système d’exploitation invité sont envoyées aux métriques par l’agent Azure Monitor.

> [!NOTE]
> L’agent Azure Monitor envoie les données à la fois aux métriques et aux journaux. Dans ce scénario, il est utilisé uniquement pour les métriques, car l’agent Log Analytics envoie les données aux journaux, comme cela est actuellement requis pour les insights de machine virtuelle. Quand VM Insights utilisera l’agent Azure Monitor, ce scénario sera mis à jour pour supprimer l’agent Log Analytics.

## <a name="analyze-data-with-vm-insights"></a>Analyser les données avec VM Insights
VM Insights inclut plusieurs graphiques de performances qui vous permettent d’obtenir rapidement l’état du fonctionnement de vos machines supervisées, leurs performances de tendances dans le temps et les dépendances entre les machines et les processus. Il offre également une vue consolidée des différents aspects de toute machine supervisée, tels que ses propriétés et les événements collectés dans l’espace de travail Log Analytics.

L’onglet **Démarrage** affiche toutes les machines de votre abonnement Azure et identifie celles qui sont supervisées. Utilisez cette vue pour identifier rapidement les machines qui ne sont pas supervisées et pour intégrer des machines individuelles qui ne sont pas encore supervisées.

:::image type="content" source="media/monitor-virtual-machines/vminsights-get-started.png" alt-text="Capture d’écran montrant les informations de prise en main de VM Insights" lightbox="media/monitor-virtual-machines/vminsights-get-started.png":::

La vue **Performances** inclut plusieurs graphiques avec plusieurs indicateurs de performance clés (KPI) qui vous aideront à déterminer l’efficacité des machines. Ces graphiques montrent l’utilisation des ressources sur une période donnée. Vous pouvez les utiliser pour identifier les goulots d’étranglement, voir les anomalies ou basculer sur une perspective listant toutes les machines afin de visualiser l’utilisation des ressources en fonction de la métrique sélectionnée. Pour obtenir des détails sur l’utilisation de la vue Performances, consultez [Performances des graphiques avec VM Insights](vminsights-performance.md).

:::image type="content" source="media/monitor-virtual-machines/vminsights-performance.png" alt-text="Capture d’écran montrant les performances de VM Insights" lightbox="media/monitor-virtual-machines/vminsights-performance.png":::

Utilisez la vue **cartographique** pour voir les processus en cours d’exécution sur les machines, leurs dépendances sur les autres machines et les processus externes. Vous pouvez modifier la fenêtre de temps de la vue pour déterminer si ces dépendances ont changé par rapport à une autre période. Pour obtenir des détails sur l’utilisation de la vue cartographique, consultez [Utiliser la fonctionnalité Carte de VM Insights pour comprendre les composants d’application](vminsights-maps.md).

:::image type="content" source="media/monitor-virtual-machines/vminsights-map.png" alt-text="Capture d’écran montrant la carte VM Insights" lightbox="media/monitor-virtual-machines/vminsights-map.png":::

## <a name="analyze-metric-data-with-metrics-explorer"></a>Analyser les données de métriques avec Metrics Explorer
Metrics Explorer vous permet de tracer des graphiques, de corréler visuellement des tendances et d’étudier les pics et les creux des valeurs des métriques. Pour obtenir des détails sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../essentials/metrics-getting-started.md). 

Trois espaces de noms sont utilisés par les machines virtuelles.

| Espace de noms | Description | Condition requise |
|:---|:---|:---|
| Hôte de machine virtuelle | Les métriques d’hôte sont automatiquement collectées pour toutes les machines virtuelles Azure. Liste détaillée des métriques sur [Microsoft.Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines). | Informations collectées automatiquement sans configuration requise. |
| Invité (classique) | Ensemble limité de données sur les performances du système d’exploitation invité et les applications. Disponible dans Metrics Explorer, mais pas dans les autres fonctionnalités Azure Monitor, telles que les alertes de métrique.  | [Extension de diagnostic](../agents/diagnostics-extension-overview.md) installée. Les données sont lues à partir du Stockage Azure.  |
| Invité de machine virtuelle | Les données sur les performances des applications et du système d’exploitation invité sont disponibles pour toutes les fonctionnalités de Microsoft Azure Monitor, via des mesures. | [Agent Azure Monitor](../agents/azure-monitor-agent-overview.md) installé avec une [règle de collecte de données](../agents/data-collection-rule-overview.md). |

## <a name="analyze-log-data-with-log-analytics"></a>Analyser les données de journal avec Log Analytics
Log Analytics vous permet d’effectuer une analyse personnalisée de vos données de journal. Utilisez Log Analytics lorsque vous souhaitez approfondir les données utilisées pour créer les vues dans VM Insights. Vous pouvez analyser différentes logiques et agrégations de ces données, mettre en corrélation les données de sécurité collectées par Microsoft Defender pour le cloud et Microsoft Sentinel avec vos données d’intégrité et de disponibilité ou utiliser les données collectées pour vos [charges de travail](monitor-virtual-machine-workloads.md).

Vous n’avez pas forcément besoin de savoir comment écrire une requête de journal pour utiliser Log Analytics. Il existe plusieurs requêtes prédéfinies que vous pouvez sélectionner et exécuter sans modification ou utiliser comme début d’une requête personnalisée. Sélectionnez **Requêtes** en haut de l’écran Log Analytics et affichez les requêtes avec un **type de ressource** défini sur **Machines virtuelles** ou sur **Groupes de machines virtuelles identiques**. Pour obtenir des informations sur l’utilisation de ces requêtes, consultez [Utilisation de requêtes dans Azure Monitor Log Analytics](../logs/queries.md). Pour suivre un tutoriel sur l’utilisation de Log Analytics pour exécuter des requêtes et utiliser leurs résultats, consultez [Tutoriel sur Log Analytics](../logs/log-analytics-tutorial.md).

:::image type="content" source="media/monitor-virtual-machines/vm-queries.png" alt-text="Capture d’écran montrant les requêtes de machine virtuelle" lightbox="media/monitor-virtual-machines/vm-queries.png":::

Lorsque vous démarrez Log Analytics à partir de VM Insights à l’aide du volet Propriétés dans la vue **Performances** ou **cartographique**, les tables sont listées qui contiennent des données pour l’ordinateur sélectionné. Sélectionnez une table pour ouvrir Log Analytics avec une requête simple qui retourne tous les enregistrements de cette table pour l’ordinateur sélectionné. Utilisez ces résultats ou modifiez la requête pour une analyse plus complexe. L’[étendue](../log/../logs/scope.md) définie sur l’espace de travail signifie que vous avez accès aux données de tous les ordinateurs qui utilisent cet espace de travail. 

:::image type="content" source="media/monitor-virtual-machines/table-query.png" alt-text="Capture d’écran montrant une requête de table" lightbox="media/monitor-virtual-machines/table-query.png":::

## <a name="visualize-data-with-workbooks"></a>Visualiser des données dans des classeurs
Les [classeurs](../visualize/workbooks-overview.MD) fournissent des rapports interactifs dans le portail Azure et associent différents types de données en une seule vue. Les classeurs regroupent du texte, des  [requêtes de journal](/azure/data-explorer/kusto/query/), des métriques et des paramètres sous la forme de rapports interactifs complets. Les classeurs sont modifiables par tous les membres de l’équipe ayant accès aux mêmes ressources Azure.

Les classeurs sont utiles pour les scénarios tels que les suivants :

* Explorer l’utilisation de votre machine virtuelle quand vous ne connaissez pas à l’avance les métriques intéressantes, telles que l’utilisation du processeur, l’espace disque, la mémoire et les dépendances réseau. Contrairement à d’autres outils d’analyse de l’utilisation, les classeurs vous permettent d’associer plusieurs types de visualisations et d’analyses, ce qui les rend très utiles pour ce type d’exploration sous forme libre.
* Expliquer à votre équipe le fonctionnement d’une machine virtuelle provisionnée récemment, en affichant les métriques des compteurs clés et d’autres événements de journal.
* Partager les résultats d’une expérimentation de redimensionnement de votre machine virtuelle avec d’autres membres de votre équipe. Vous pouvez expliquer les objectifs de l’expérience avec du texte. Ensuite, vous pouvez présenter les différentes métriques d’utilisation et les requêtes analytiques utilisées pour évaluer l’expérimentation, en vous aidant de légendes claires indiquant si chaque métrique se situe au-dessus ou en dessous de la cible.
* Créer des rapports relatifs à l’impact d’une panne sur l’utilisation de votre machine virtuelle, en combinant des données, une explication du texte et une présentation des étapes suivantes pour éviter à l’avenir d’éventuelles interruptions.

Les insights de machine virtuelle incluent les classeurs suivants. Vous pouvez utiliser ces classeurs ou vous en servir comme point de départ pour créer des classeurs personnalisés et répondre à vos besoins spécifiques.

### <a name="single-virtual-machine"></a>une seule machine virtuelle

| Classeur | Description |
|----------|-------------|
| Performances | Fournit une version personnalisable de la vue Performances qui utilise tous les compteurs de performances Log Analytics que vous avez activés. | 
| Connexions | Fournit une vue détaillée des connexions entrantes et sortantes de votre machine virtuelle. | 

### <a name="multiple-virtual-machines"></a>Plusieurs machines virtuelles

| Classeur | Description |
|----------|-------------|
| Performances | Fournit une version personnalisable de la Liste des N premiers et de la vue Graphiques dans un classeur unique qui utilise tous les compteurs de performances Log Analytics que vous avez activés.|
| Compteurs de performance | Fournit une vue Graphique des N premiers sur un vaste ensemble de compteurs de performances. |
| Connexions | Fournit une vue détaillée des connexions entrantes et sortantes de vos machines supervisées. |
| Ports actifs | Fournit une liste des processus qui sont liés aux ports sur les machines supervisées et à leur activité dans la plage de temps choisie. |
| Ouvrir des ports | Fournit le nombre de ports ouverts sur vos machines supervisées et les détails concernant ces ports. |
| Connexions ayant échoué | Affiche le nombre de connexions ayant échoué sur vos machines supervisées, la tendance des échecs et si le pourcentage d’échecs augmente au fil du temps. |
| Sécurité et audit | Fournit une analyse de votre trafic TCP/IP qui génère des rapports sur les connexions globales, les connexions malveillantes et les emplacements où les points de terminaison IP résident globalement. Pour activer toutes les fonctionnalités, vous devez activer la détection de la sécurité. |
| Trafic TCP | Fournit un rapport classé pour vos machines supervisées et leur trafic réseau envoyé, reçu et total dans une grille et affiché sous la forme d’une courbe de tendance. |
| Comparaison du trafic | Compare les tendances du trafic réseau pour une seule machine ou un groupe de machines. |
| Agent Log Analytics | Analyse l’intégrité de vos agents, y compris le nombre d’agents se connectant à un espace de travail qui ne sont pas intègres et l’effet de l’agent sur les performances de la machine. Ce classeur n’est pas disponible à partir de VM Insights comme les autres classeurs. Dans le menu Azure Monitor, accédez à **Classeurs** et sélectionnez **Modèles publics**. |

Pour obtenir des instructions sur la création de vos propres classeurs personnalisés, consultez [Créer des rapports interactifs VM Insights avec des classeurs](vminsights-workbooks.md).

:::image type="content" source="media/monitor-virtual-machines/workbook-example.png" alt-text="Capture d’écran montrant les classeurs de machine virtuelle" lightbox="media/monitor-virtual-machines/workbook-example.png":::

## <a name="next-steps"></a>Étapes suivantes

* [Créer des alertes à partir de données collectées](monitor-virtual-machine-alerts.md)
* [Superviser les charges de travail en cours d’exécution sur des machines virtuelles](monitor-virtual-machine-workloads.md)
