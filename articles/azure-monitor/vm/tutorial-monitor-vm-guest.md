---
title: 'Tutoriel : Collecter des journaux et métriques d’invité sur une machine virtuelle Azure'
description: Créer une règle de collecte de données pour collecter des journaux et métriques d’invité à partir d’une machine virtuelle Azure.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/08/2021
ms.openlocfilehash: 5bd3b557bf7bc9ada4e75979e593c7fb0c338dd4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350439"
---
# <a name="tutorial-collect-guest-logs-and-metrics-from-azure-virtual-machine"></a>Tutoriel : Collecter des journaux et métriques d’invité sur une machine virtuelle Azure
Lorsque vous [activez l’analyse avec les insights de machine virtuelle](tutorial-monitor-vm-enable.md), elle collecte des données de performances à l’aide de l’agent Log Analytics. Pour collecter les journaux du système d’exploitation invité et envoyer des données de performances aux métriques Azure Monitor, installez l'[agent Azure Monitor](../agents/azure-monitor-agent-overview.md) et créez une [règle de collecte de données](../agents/data-collection-rule-overview.md) (DCR) définissant les données à collecter ainsi que leur destination. 

> [!NOTE]
> Avant l’agent Azure Monitor, les métriques d’invité pour les machines virtuelles Azure étaient collectées à l’aide de l’[extension de diagnostic Azure](../agents/diagnostics-extension-overview.md) pour Windows (WAD) et Linux (LAD). Ces agents sont toujours disponibles et peuvent être configurés à l’aide de l’élément de menu **Paramètres de diagnostic** de la machine virtuelle, mais ils sont en cours de remplacement par l’agent Azure Monitor.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez une règle de collecte de données qui envoie les données de performances d’invité aux métriques Azure Monitor et les événements de journal aux journaux Azure Monitor. 
> * Affichez les métriques d’invité dans Metrics Explorer.
> * Affichez les journaux d’invité dans Log Analytics.

## <a name="prerequisites"></a>Prérequis
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants : 

- Une machine virtuelle Azure à surveiller.


## <a name="create-data-collection-rule"></a>Créer une règle de collecte de données
Dans Azure Monitor, les [règles de collecte de données](../agents/data-collection-rule-overview.md) définissent les données à collecter ainsi que leur destination. Lorsque vous définissez la règle de collecte de données à l’aide du portail Azure, vous spécifiez les machines virtuelles auxquelles elle doit être appliquée. L’agent Azure Monitor sera automatiquement installé sur les machines virtuelles sur lesquelles il n’est pas déjà installé.

> [!NOTE]
> Actuellement, vous devez installer l’agent Azure Monitor à partir du menu **Superviser** du portail Azure. Cette fonctionnalité n’est pas encore disponible à partir du menu de la machine virtuelle. 

Dans le menu **Superviser** du portail Azure, sélectionnez **Règles de collecte de données**, puis **Créer** pour créer une règle de collecte de données.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-create.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-create.png" alt-text="Créer une règle de collecte de données":::

Dans l’onglet **Informations de base**, indiquez le **Nom de la règle** qui correspond au nom de la règle affiché dans le portail Azure. Sélectionnez un **Abonnement**, un **Groupe de ressources** et une **Région** où la DCR et ses associations seront stockées. Il ne doivent pas nécessairement être identiques aux ressources supervisées. Le **Type de plateforme** définit les options disponibles lorsque vous définissez le reste de la DCR. Sélectionnez *Windows* ou *Linux* en cas d’association aux ressources uniquement ou *Personnalisé* en cas d’association aux deux types.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-basics.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-basics.png" alt-text="Informations de base sur les règles de collecte de données":::

## <a name="select-resources"></a>Sélection des ressources
Sous l’onglet **Ressources** , identifiez une ou plusieurs machines virtuelles auxquelles s’appliquera la règle de collecte de données. L’agent Azure Monitor sera installé sur les machines virtuelles sur lesquelles il n’est pas déjà installé. Cliquez sur **Ajouter des ressources**, puis sélectionnez vos machines virtuelles, le groupe de ressources ou l’abonnement où se trouve votre machine virtuelle. La règle de collecte de données s’appliquera à toutes les machines virtuelles dans l’étendue sélectionnée.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-resources.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-resources.png" alt-text="Ressources de règle de collecte de données":::

## <a name="select-data-sources"></a>Sélectionner des sources de données
Une règle de collecte de données unique peut avoir plusieurs sources de données. Pour ce tutoriel, nous allons utiliser la même règle pour collecter les métriques et les journaux d’invité. Nous enverrons les métriques aux métriques Azure Monitor et aux journaux Azure Monitor pour permettre leur analyse avec Metrics Explorer et Log Analytics.

Sous l’onglet **Collecter et livrer**, cliquez sur **Ajouter une source de données**. Pour le **Typeype de source de données**, sélectionnez **Compteurs de performances**. Conservez le paramètre **De base** et sélectionnez les compteurs à collecter. **Personnalisé** vous permet de sélectionner des valeurs de métriques individuelles. 

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-metric.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-metric.png" alt-text="Source de données des métriques de règle de collecte de données":::

Sélectionnez l’onglet **Destination**. **Métriques Azure Monitor** doit déjà y figurer. Cliquez sur **Ajouter une destination** pour ajouter une autre destination. Sélectionnez **Journaux Azure Monitor** pour le **Type de destination**. Sélectionnez votre espace de travail Log Analytics pour le **compte ou l’espace de noms**. Cliquez sur **Ajouter une source de données** pour enregistrer la source de données.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-destination-metric.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-destination-metric.png" alt-text="Destination de la règle de collecte de données":::

Cliquez à nouveau sur **Ajouter une source de données** pour ajouter des journaux à la règle de collecte de données. Pour le **Type de source de données**, sélectionnez **Journaux des événements Windows** ou **Syslog Linux**. Sélectionnez les types de données de journal que vous souhaitez collecter. 

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-windows.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-windows.png" alt-text="Source de données de journal Windows de la règle de collecte de données":::

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-linux.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-linux.png" alt-text="Source de données de journal Linux de la règle de collecte de données":::

Sélectionnez l’onglet **Destination**. L’option **Journaux Azure Monitor** doit déjà être sélectionnée pour le **Type de destination**. Sélectionnez votre espace de travail Log Analytics pour le **compte ou l’espace de noms**. Si vous n’avez pas encore d’espace de travail, vous pouvez sélectionner l’espace de travail par défaut de votre abonnement, qui sera automatiquement créé. Cliquez sur **Ajouter une source de données** pour enregistrer la source de données.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-destination-logs.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-destination-logs.png" alt-text="Destination des journaux de la règle de collecte de données":::

Cliquez sur **Vérifier + créer** pour créer la règle de collecte de données et installer l’agent Azure Monitor sur les machines virtuelles sélectionnées.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-save.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-save.png" alt-text="Enregistrer une règle de collecte de données":::

## <a name="viewing-logs"></a>Consultation des journaux
Les données sont récupérées à partir d’un espace de travail Log Analytics à l’aide d’une requête de journal écrite en langage KQL (Kusto Query Language). Bien qu’un ensemble de requêtes précréées soient disponibles pour les machines virtuelles, nous allons utiliser une requête simple pour examiner les événements que nous collectons. 

Sélectionnez **Journaux** dans le menu de votre machine virtuelle. Log Analytics s’ouvre avec une fenêtre de requête vide et l’étendue définie sur cette machine. Les requêtes incluront uniquement les enregistrements collectés depuis cette machine. 

> [!NOTE]
> La fenêtre **Requêtes** peut s’afficher lorsque vous ouvrez Log Analytics. Cela comprend les requêtes précréées que vous pouvez utiliser. Pour le moment, fermez cette fenêtre, car nous allons créer manuellement une requête simple.

:::image type="content" source="media/tutorial-monitor-vm/log-analytics.png" lightbox="media/tutorial-monitor-vm/log-analytics.png" alt-text="Log Analytics":::


Dans la fenêtre de requête vide, entrez `Event` ou `Syslog` selon que votre machine exécute Windows ou Linux, puis cliquez sur **Exécuter**. Les événements collectés dans l'**Intervalle de temps** s’affichent.

> [!NOTE]
> Si la requête ne renvoie aucune donnée, vous devrez peut-être patienter quelques minutes avant que les événements soient créés sur la machine virtuelle concernée par la collecte. Vous devrez peut-être également modifier la source de données dans la règle de collecte de données afin d’inclure d’autres catégories d’événements.

:::image type="content" source="media/tutorial-monitor-vm/log-analytics-query.png" lightbox="media/tutorial-monitor-vm/log-analytics-query.png" alt-text="Log Analytics avec les résultats de la requête":::

Pour un tutoriel sur l’utilisation de Log Analytics afin d’analyser les données de journal, consultez [Tutoriel Log Analytics](../logs/log-analytics-tutorial.md). Pour un tutoriel sur la création de règles d’alerte à partir de données de journal, consultez [Tutoriel : Créer une alerte de requête de journal pour une ressource Azure](../alerts/tutorial-log-alert.md).

## <a name="view-guest-metrics"></a>Afficher les métriques d’invité
Vous pouvez afficher les métriques de votre machine virtuelle hôte avec Metrics Explorer sans règle de collecte de données comme [n’importe quelle autre ressource Azure](../essentials/tutorial-metrics.md). Cela étant, la règle de collecte de données vous permet d’utiliser Metrics Explorer pour afficher les métriques d’invité en plus des métriques d’hôte.

Sélectionnez **Métriques** dans le menu de votre machine virtuelle. Metrics Explorer s’ouvre avec l’étendue définie sur votre machine virtuelle. Cliquez sur **Espace de noms des métriques**, puis sélectionnez **Invité de machine virtuelle**. 

> [!NOTE]
> Si **Invité de machine virtuelle** ne s’affiche pas, vous devrez peut-être patienter quelques minutes supplémentaires pour que l’agent soit déployé et que les données commencent à être collectées.


:::image type="content" source="media/tutorial-monitor-vm/metrics-explorer.png" lightbox="media/tutorial-monitor-vm/metrics-explorer.png" alt-text="Metrics Explorer":::

Les métriques d’invité disponibles s’affichent. Sélectionnez une **Métrique** à ajouter au graphique.

:::image type="content" source="media/tutorial-monitor-vm/metrics-explorer-guest-metrics.png" lightbox="media/tutorial-monitor-vm/metrics-explorer-guest-metrics.png" alt-text="Metrics Explorer avec métriques d’invité":::

Vous pouvez accéder à un tutoriel complet sur l’affichage et l’analyse des données métriques à l’aide de Metrics Explorer dans [Tutoriel : Analyser les métriques d’une ressource Azure](../essentials/tutorial-metrics.md) et créer des alertes de métriques dans [Tutoriel : Créer une alerte de métrique pour une ressource Azure](../alerts/tutorial-metric-alert.md). 



## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous collectez des métriques d’invité pour la machine virtuelle, vous pouvez créer des alertes de métriques basées sur des métriques d’invité telles que la mémoire disponible et l’espace disque logique.

> [!div class="nextstepaction"]
> [Créer une alerte de métrique dans Azure Monitor](../alerts/tutorial-metric-alert.md)


