---
author: bwren
ms.author: bwren
ms.service: azure-monitor
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 5d31cea42dd51cee65e79c44b2a44615db027aff
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297973"
---
Dans Azure Monitor, les données de journal sont stockées dans un espace de travail Log Analytics. Si vous avez déjà créé un espace de travail dans votre abonnement, vous pouvez l’utiliser. Vous pouvez également choisir d’utiliser l’espace de travail par défaut créé dans chaque abonnement Azure. 

Si vous voulez créer un espace de travail Log Analytics, vous pouvez utiliser la procédure suivante. Si vous envisagez d’en utiliser un existant, passez à la section suivante.

À partir de **Tous les services** dans le portail Azure, sélectionnez **Espaces de travail Log Analytics**.

:::image type="content" source="media/azure-monitor-tutorial-workspace/azure-portal.png" lightbox="media/azure-monitor-tutorial-workspace/azure-portal.png" alt-text="Sélectionner Espaces de travail Log Analytics dans le portail Azure":::

Cliquez sur **Créer** pour créer un espace de travail.

:::image type="content" source="media/azure-monitor-tutorial-workspace/create-workspace.png" lightbox="media/azure-monitor-tutorial-workspace/create-workspace.png" alt-text="Bouton Créer un espace de travail":::

Sous l’onglet **Informations de base**, sélectionnez un **abonnement**, un **groupe de ressources** et une **région** pour l’espace de travail. Ces derniers n’ont pas besoin d’être identiques à ceux de la ressource supervisée. Indiquez un **nom** globalement unique sur l’ensemble des abonnements Azure Monitor.

:::image type="content" source="media/azure-monitor-tutorial-workspace/workspace-basics.png" lightbox="media/azure-monitor-tutorial-workspace/workspace-basics.png" alt-text="Notions de base sur les espaces de travail":::


Cliquez sur **Vérifier + créer** pour créer l’espace de travail.