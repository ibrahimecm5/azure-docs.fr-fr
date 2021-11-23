---
title: 'Didacticiel : Activation de la surveillance pour une machine virtuelle Azure'
description: Activez la surveillance avec VM Insights dans Azure Monitor pour surveiller une machine virtuelle Azure.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/04/2021
ms.openlocfilehash: 4156685d707a6e4fd6319a7130750838434f1efa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350533"
---
# <a name="tutorial-enable-monitoring-for-azure-virtual-machine"></a>Didacticiel : Activation de la surveillance pour une machine virtuelle Azure
Pour surveiller l’intégrité et les performances d’une machine virtuelle Azure, vous devez installer un agent pour collecter des données à partir de son système d’exploitation invité. VM Insights est une fonctionnalité d’Azure Monitor pour la surveillance du système d’exploitation invité et des charges de travail s’exécutant sur des machines virtuelles Azure. Lorsque vous activez la surveillance d’une machine virtuelle Azure, elle installe les agents nécessaires et commence à collecter les informations de performances, de processus et de dépendances du système d’exploitation invité. 

> [!NOTE]
> Si vous ne connaissez pas Azure Monitor, commencez par le [Didacticiel : Superviser des ressources Azure avec Azure Monitor](../essentials/monitor-azure-resource.md). Les machines virtuelles Azure génèrent des données de surveillance similaires à celles d’autres ressources Azure, telles que des métriques de plateforme et un journal d’activité. Ce didacticiel explique comment activer une analyse supplémentaire propre aux machines virtuelles.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez un espace de travail Log Analytics pour collecter des données de performances et de journal à partir de la machine virtuelle.
> * Activez VM Insights pour la machine virtuelle qui installe les agents requis et lance la collecte des données. 
> * Inspectez les graphiques analysant des données de performances collectées à partir de la machine virtuelle. 
> * Inspectez la carte montrant les processus s’exécutant sur la machine virtuelle et les dépendances par rapport à d’autres systèmes.


> [!NOTE]
> VM Insights installe l’agent Log Analytics qui collecte les données de performances du système d’exploitation invité des machines virtuelles. Il ne collecte pas les journaux du système d’exploitation invité et n’envoie pas de données de performances aux métriques d’Azure Monitor. Pour cette fonctionnalité; consultez [Didacticiel : Collecter des journaux et métriques d’invité sur une machine virtuelle Azure](tutorial-monitor-vm-guest.md).

## <a name="prerequisites"></a>Prérequis
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants : 

- Une machine virtuelle Azure à surveiller.



## <a name="create-a-log-analytics-workspace"></a>Créer un espace de travail Log Analytics
[!INCLUDE [Create workspace](../../../includes/azure-monitor-tutorial-workspace.md)]


## <a name="enable-monitoring"></a>Activer la supervision
Sélectionnez **Insights** dans le menu de la machine virtuelle sur le portail Azure. Si VM Insights n’a pas encore été activé, vous devriez voir un écran similaire à celui ci-après, qui vous permet d’activer la surveillance. Cliquez sur **Activer**.

> [!NOTE]
> Si vous avez sélectionné l’option **Activer la surveillance détaillée** lors de la création de votre machine virtuelle, il se peut que VM Insights soit déjà activé. Sélectionnez votre espace de travail, puis cliquez à nouveau sur **Activer**. Il s’agit de l’espace de travail auquel sont envoyées les données que VM Insights collecte.

:::image type="content" source="media/tutorial-monitor-vm/enable-vminsights-02.png" lightbox="media/tutorial-monitor-vm/enable-vminsights-02.png" alt-text="Activer VM Insights avec un espace de travail":::

Vous allez voir un message indiquant que la surveillance est en cours d’activation. L’installation de l’agent et le démarrage de la collecte de données peuvent prendre plusieurs minutes. 

> [!NOTE]
> Il se peut que vous receviez un message indiquant qu’une mise à niveau est disponible pour VM Insights. Si c’est le cas, avant de continuer, sélectionnez l’option permettant d’effectuer la mise à niveau.

## <a name="view-performance"></a>Afficher les performances
Une fois le déploiement terminé, vous verrez des affichages sous l’onglet **Performances** dans VM Insights, avec des données de performances pour la machine. Vous voyez ainsi les valeurs des principales métriques d’invité au fil du temps. 

:::image type="content" source="media/tutorial-monitor-vm/performance.png" lightbox="media/tutorial-monitor-vm/performance.png" alt-text="Affichage de performance de VM Insights":::

## <a name="view-processes-and-dependencies"></a>Afficher les processus et les dépendances
Sélectionnez l’onglet **Cartes** pour afficher les processus et les dépendances de la machine virtuelle. La machine virtuelle active se trouve au centre de l’affichage. Affichez les processus s’exécutant sur celle-ci en développant **Processus**.

:::image type="content" source="media/tutorial-monitor-vm/map-processes.png" lightbox="media/tutorial-monitor-vm/map-processes.png" alt-text="Affichage de carte VM Insights avec des processus":::


## <a name="view-machine-details"></a>Afficher les détails de la machine
L’affichage **Cartes** fournit différents onglets contenant les informations collectées sur la machine virtuelle. Cliquez sur les onglets pour voir ce qui est disponible.

:::image type="content" source="media/tutorial-monitor-vm/map-details.png" lightbox="media/tutorial-monitor-vm/map-details.png" alt-text="Affichage de carte VM Insights avec les détails de la machine":::

## <a name="next-steps"></a>Étapes suivantes
À présent que vous collectez des données de la machine virtuelle, vous pouvez les utiliser pour créer des alertes qui vous avertissent de manière proactive lors de la détection de problèmes.

> [!div class="nextstepaction"]
> [Créer une alerte qui se déclenche quand une machine virtuelle Azure n’est pas disponible](tutorial-monitor-vm-alert.md)

