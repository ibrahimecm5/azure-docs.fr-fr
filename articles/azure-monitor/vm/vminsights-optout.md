---
title: Désactiver l’analyse de VM Insights
description: Cet article explique comment arrêter la surveillance de vos machines virtuelles dans VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 8edb225316dfd12acff5c1f6a036246d68d746c8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306432"
---
# <a name="disable-monitoring-of-your-vms-in-vm-insights"></a>Désactiver l’analyse de vos machines virtuelles dans VM Insights

Après avoir activé l’analyse de vos machines virtuelles, vous pouvez choisir de désactiver cette analyse dans VM Insights. Cet article explique comment désactiver la surveillance d’une ou plusieurs machines virtuelles.  

Actuellement, VM Insights ne prend pas en charge la désactivation sélective de l’analyse des machines virtuelles. Votre espace de travail Log Analytics peut prendre en charge VM Insights et d’autres solutions. Il peut également collecter d’autres données de surveillance. Si votre espace de travail Log Analytics fournit ces services, vous devez comprendre l’effet et les méthodes de désactivation de la surveillance avant de commencer.

VM Insights s’appuie sur les composants suivants pour fournir son expérience :

* Un espace de travail Log Analytics, qui stocke les données de surveillance collectées à partir des machines virtuelles et d’autres sources.
* Une collection de compteurs de performances configurés dans l’espace de travail. Cette collection met à jour la configuration de la surveillance sur toutes les machines virtuelles connectées à l’espace de travail.
* `VMInsights`, qui est une solution de supervision configurée dans l’espace de travail. Cette solution met à jour la configuration de la supervision sur toutes les machines virtuelles connectées à l’espace de travail.
* `MicrosoftMonitoringAgent` (pour Windows) ou `OmsAgentForLinux` (pour Linux), et `DependencyAgent`, qui sont des extensions de la machine virtuelle Azure. Ces extensions collectent et envoient des données à l’espace de travail.

Lorsque vous vous préparez à désactiver la surveillance de vos machines virtuelles, n’oubliez pas les considérations suivantes :

* Si vous évaluiez avec une seule machine virtuelle et que vous utilisiez l’espace de travail Log Analytics présélectionné par défaut, vous pouvez désactiver la surveillance en désinstallant l’agent de dépendances de la machine virtuelle et en déconnectant l’agent Log Analytics de cet espace de travail. Cette approche est adaptée si vous avez l’intention d’utiliser la machine virtuelle à d’autres fins et que vous décidez ultérieurement de la reconnecter à un autre espace de travail.
* Si vous avez sélectionné un espace de travail Log Analytics préexistant qui prend en charge d’autres solutions de surveillance et la collecte de données à partir d’autres sources, vous pouvez supprimer les composants de la solution de l’espace de travail sans interrompre ni affecter votre espace de travail.  

>[!NOTE]
> Après avoir supprimé les composants de la solution de votre espace de travail, il se peut que les données de performances et de carte continuent de s’afficher pour vos machines virtuelles Azure. Les données cesseront éventuellement d’apparaître dans les vues **Performances** et **Mappage**. L’option **Activer** sera disponible sur la machine virtuelle Azure sélectionnée et vous permettra de réactiver la supervision ultérieurement.  

## <a name="remove-vm-insights-completely"></a>Supprimer VM Insights complètement

Si vous avez encore besoin de l’espace de travail Log Analytics, procédez comme suit pour supprimer complètement VM Insights. Vous allez supprimer la solution `VMInsights` de l’espace de travail.  

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le portail Azure, sélectionnez **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste filtre les suggestions. Sélectionnez **Log Analytics**.
3. Dans votre liste d’espaces de travail Log Analytics, sélectionnez celui que vous avez choisi quand vous avez activé VM Insights.
4. Sur la gauche, sélectionnez **Solutions**.  
5. Dans la liste des solutions, sélectionnez **VMInsights(nom d’espace de travail)** . Dans la page **Vue d’ensemble** pour la solution, sélectionnez **Supprimer**. À l’invite de confirmation, sélectionnez **Oui**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Désactiver la surveillance et conserver l’espace de travail  

Si votre espace de travail Log Analytics a encore besoin de prendre en charge la surveillance à partir d’autres sources, procédez comme suit pour désactiver la surveillance sur la machine virtuelle que vous utilisiez pour évaluer VM Insights. Pour les machines virtuelles Azure, vous allez supprimer l’extension de machine virtuelle de l’agent de dépendances et l’extension de machine virtuelle de l’agent Log Analytics pour Windows ou Linux directement sur la machine virtuelle. 

>[!NOTE]
>Ne supprimez pas l’agent Log Analytics si : 
>
> * Azure Automation gère la machine virtuelle pour orchestrer les processus ou pour gérer la configuration ou les mises à jour. 
> * Microsoft Defender pour le cloud gère la machine virtuelle pour la sécurité et la détection des menaces. 
>
> Si vous supprimez l’agent Log Analytics, vous empêchez ces services et ces solutions de gérer de façon proactive votre machine virtuelle. 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
2. Dans le portail Azure, sélectionnez **Machines virtuelles**. 
3. Sélectionnez une machine virtuelle dans la liste. 
4. Sur la gauche, sélectionnez **Extensions**. Dans la page **Extensions**, sélectionnez **DependencyAgent**.
5. Dans la page des propriétés de l’extension, sélectionnez **Désinstaller**.
6. Dans la page **Extensions**, sélectionnez **MicrosoftMonitoringAgent**. Dans la page des propriétés de l’extension, sélectionnez **Désinstaller**.  
