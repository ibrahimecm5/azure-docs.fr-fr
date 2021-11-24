---
title: Superviser des points de terminaison en ligne managés (préversion)
titleSuffix: Azure Machine Learning
description: Supervisez les points de terminaison en ligne managés et créez des alertes avec Application Insights.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.date: 10/21/2021
ms.topic: conceptual
ms.custom: how-to, devplatv2
ms.openlocfilehash: c704af7d5249ca1a24da554e7789d3c92a360861
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519455"
---
# <a name="monitor-managed-online-endpoints-preview"></a>Superviser des points de terminaison en ligne managés (préversion)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Dans cet article, vous allez découvrir comment superviser les[Points de terminaison en ligne managés Azure Machine Learning (préversion)](concept-endpoints.md). Utilisez Application Insights pour voir les métriques et créer des alertes pour rester à jour avec vos points de terminaison en ligne managés.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Voir les métriques de votre point de terminaison en ligne managé
> * Créer un tableau de bord pour vos métriques
> * Créer une alerte de métrique

## <a name="prerequisites"></a>Prérequis

- Déployez un point de terminaison en ligne managé Azure Machine Learning (préversion).
- Vous devez disposer au moins d’un [accès en lecture](../role-based-access-control/role-assignments-portal.md) sur le point de terminaison.

## <a name="view-metrics"></a>Afficher les mesures

Pour voir les métriques d’un déploiement ou d’un point de terminaison managé, effectuez les étapes suivantes :
1. Accédez au [portail Azure](https://portal.azure.com).
1. Accédez à la ressource de déploiement ou de point de terminaison en ligne managé.

    Les déploiements et points de terminaison en ligne managés sont des ressources ARM (Azure Resource Manager) que vous pouvez trouver en accédant à leur groupe de ressources propriétaire. Recherchez les types de ressource **point de terminaison en ligne Machine Learning** et **déploiement en ligne Machine Learning**.

1. Dans la colonne de gauche, sélectionnez **Métriques**.

## <a name="available-metrics"></a>Métriques disponibles

Les métriques affichées varient en fonction de la ressource que vous sélectionnez. L’étendue des métriques des points de terminaison en ligne managés est différente de celle des déploiements en ligne managés (préversion).

### <a name="metrics-at-endpoint-scope"></a>Métriques dans l’étendue des points de terminaison

- Latence des demandes
- Latence des demandes P50 (latence des demandes au 50e centile)
- Latence des demandes P90 (latence des demandes au 90e centile)
- Latence des demandes P95 (latence des demandes au 95e centile)
- Requêtes par minute
- Nouvelles connexions par seconde
- Nombre de connexions actives
- Octets réseau

> ![NOTE] La bande passante sera limitée si les limites sont dépassées (consultez la section relative aux points de terminaison en ligne managés dans [Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)). Pour déterminer si les demandes sont limitées :
> - Surveiller la métrique « Octets réseau »
> - Les en-têtes de réponse comporteront les champs suivants : `ms-azureml-bandwidth-request-delay-ms` et `ms-azureml-bandwidth-response-delay-ms`. Les valeurs des champs sont les retards, en millisecondes, de la limitation de bande passante.

Division dans les dimensions suivantes :

- Déploiement
- Code d’état
- Classe de code d’état

### <a name="metrics-at-deployment-scope"></a>Métriques dans l’étendue du déploiement

- Pourcentage d’utilisation de l’UC
- Capacité de déploiement (nombre d’instances du type d’instance demandé)
- Utilisation du disque
- Utilisation de la mémoire du GPU (applicable uniquement aux instances de GPU)
- Utilisation du GPU (applicable uniquement aux instances de GPU)
- Pourcentage d’utilisation de la mémoire

Division sur la dimension suivante :

- InstanceId

## <a name="create-a-dashboard"></a>Création d’un tableau de bord

Vous pouvez créer des tableaux de bord personnalisés pour visualiser les données de plusieurs sources sur le portail Azure, notamment les métriques de votre point de terminaison en ligne managé. Pour plus d’informations, consultez [Créer des tableaux de bord d’indicateurs de performance clés (KPI) personnalisés à l’aide d’Application Insights](../azure-monitor/app/tutorial-app-dashboards.md#add-custom-metric-chart).
    
## <a name="create-an-alert"></a>Créer une alerte

Vous pouvez aussi créer des alertes personnalisées pour être notifié des mises à jour importantes de l’état de votre point de terminaison en ligne managé :

1. En haut à droite de la page de métriques, sélectionnez **Nouvelle règle d’alerte**.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-new-alert-rule.png" alt-text="Supervision des points de terminaison en ligne : capture d’écran avec le bouton « Nouvelle règle d’alerte » entouré d’un cadre rouge":::

1. Sélectionnez un nom de condition pour indiquer à quel moment l’alerte doit être déclenchée.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-configure-signal-logic.png" alt-text="Supervision des points de terminaison en ligne : capture d’écran avec le bouton « Configurer la logique du signal » entouré d’un cadre rouge":::

1. Sélectionnez **Ajouter des groupes d’actions** > **Créer des groupes d’actions** pour spécifier ce qui doit se passer quand votre alerte se déclenche.

1. Choisissez **Créer une règle d’alerte** pour finaliser la création de l’alerte.


## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [examiner les coûts concernant votre point de terminaison déployé](./how-to-view-online-endpoints-costs.md).
* Découvrez plus en détail [Metrics Explorer](../azure-monitor/essentials/metrics-charts.md).