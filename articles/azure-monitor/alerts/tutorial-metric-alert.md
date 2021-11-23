---
title: 'Tutoriel : Créer une alerte de métrique pour une ressource Azure'
description: Découvrez comment créer un graphique de métrique avec Azure Metrics Explorer.
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: 431ef4418741a0406eeb4d350879dbc1be7ae737
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350504"
---
# <a name="tutorial-create-a-metric-alert-for-an-azure-resource"></a>Tutoriel : Créer une alerte de métrique pour une ressource Azure
Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Les règles d’alerte de métrique créent une alerte quand la valeur de métrique d’une ressource Azure dépasse un seuil.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une règle d’alerte de métrique dans Metrics Explorer
> * Configurer le seuil d’alerte
> * Créer un groupe d’actions pour définir les détails de la notification

## <a name="prerequisites"></a>Prérequis
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants : 

- Une ressource Azure à superviser. Vous pouvez utiliser n’importe quelle ressource de votre abonnement Azure qui prend en charge les métriques. Pour déterminer si une ressource prend en charge les métriques, accédez à son menu dans le portail Azure et vérifiez qu’une option **Métriques** figure dans la section **Supervision** du menu.
- Graphique dans Metrics Explorer avec une ou plusieurs métriques pour lesquelles vous souhaitez recevoir une alerte. Suivez le [Tutoriel : Analyser les métriques d’une ressource Azure](../essentials/tutorial-metrics.md).

## <a name="create-new-alert-rule"></a>Créer une nouvelle règle d’alerte
Dans Metrics Explorer, cliquez sur **Nouvelle règle d’alerte**. La règle est préconfigurée avec l’objet cible et la métrique que vous avez sélectionnée dans Metrics Explorer.

:::image type="content" source="media/tutorial-metric-alert/new-alert-rule.png" alt-text="Nouvelle règle d’alerte" lightbox="media/tutorial-metric-alert/new-alert-rule.png":::

## <a name="configure-alert-logic"></a>Configurer une logique d’alerte
La ressource est déjà sélectionnée. Vous devez modifier la logique de signal pour spécifier la valeur de seuil et les autres détails de la règle d’alerte. 

Cliquez sur le **Nom de la condition** pour voir ces paramètres. 

:::image type="content" source="./media/tutorial-metric-alert/configuration.png" lightbox="./media/tutorial-metric-alert/configuration.png" alt-text="Configuration d’une règle d’alerte":::

Le graphique montre la valeur du signal sélectionné dans le temps pour vous permettre de voir quand l’alerte a été déclenchée. Ce graphique est mis à jour quand vous spécifiez la logique de signal.

:::image type="content" source="./media/tutorial-metric-alert/signal-logic.png" lightbox="./media/tutorial-metric-alert/signal-logic.png" alt-text="Logique de signal d’une règle d’alerte":::

La **Logique d’alerte** est définie par la condition et l’heure de l’évaluation. L’alerte se déclenche quand cette condition est True. Indiquez une **Valeur de seuil** pour votre règle d’alerte, et modifiez l’**Opérateur** et le **Type d’agrégation** pour définir la logique dont vous avez besoin.

:::image type="content" source="./media/tutorial-metric-alert/alert-logic.png" lightbox="./media/tutorial-metric-alert/alert-logic.png" alt-text="Logique d’alerte de la règle d’alerte":::

Vous pouvez accepter la granularité temporelle par défaut ou la modifier en fonction de vos besoins. La **Fréquence d’évaluation** définit la fréquence à laquelle la logique d’alerte est évaluée. La **Granularité d’agrégation** définit l’intervalle de temps sur lequel les valeurs collectées sont agrégées.

Cliquez sur **Terminé** quand vous avez terminé de configurer la logique de signal.

## <a name="configure-actions"></a>Configurer les actions
[!INCLUDE [Action groups](../../../includes/azure-monitor-tutorial-action-group.md)]

## <a name="configure-details"></a>Configurer les détails
[!INCLUDE [Alert details](../../../includes/azure-monitor-tutorial-alert-details.md)]

:::image type="content" source="./media/tutorial-metric-alert/alert-details.png" lightbox="./media/tutorial-metric-alert/alert-details.png" alt-text="Détails des règles d’alerte":::


Cliquez sur **Créer une règle d’alerte** pour créer la règle d’alerte.


## <a name="view-the-alert"></a>Voir l’alerte
[!INCLUDE [View alert](../../../includes/azure-monitor-tutorial-view-alert.md)]


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à créer une alerte de métrique pour une ressource Azure, utilisez l’un des tutoriels suivants pour collecter les données de journal.

> [!div class="nextstepaction"]
> [Collecter les journaux d’une ressource Azure](../essentials/tutorial-resource-logs.md)
> [!div class="nextstepaction"]
> [Collecter des journaux et métriques d’invité sur une machine virtuelle Azure](../vm/tutorial-monitor-vm-guest.md)