---
title: Créer un espace de travail Log Analytics dans le portail Azure | Microsoft Docs
description: Découvrez comment créer un espace de travail Log Analytics pour permettre la collecte de données et les solutions de gestion à partir de vos environnements locaux et cloud dans le portail Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/18/2021
ms.openlocfilehash: 3d9e075e50b4be0a21f4138a49faf8309474ad9c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440048"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Créer un espace de travail Log Analytics dans le portail Azure
Utilisez le menu **Espaces de travail Log Analytics** pour créer un espace de travail Log Analytics à l’aide du portail Azure. Un espace de travail Log Analytics est un environnement unique pour les données de journal d’activité Azure Monitor. Chaque espace de travail dispose d’un référentiel de données et d’une configuration propres. Les sources de données et les solutions sont configurées de façon à stocker leurs données dans un espace de travail particulier. Vous devez avoir un espace de travail Log Analytics si vous souhaitez collecter des données à partir des sources suivantes :

* Ressources Azure dans votre abonnement
* Ordinateurs locaux surveillés par System Center Operations Manager
* Regroupements d’appareils à partir de Configuration Manager 
* Données de diagnostics ou de journaux du Stockage Azure

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure-portal"></a>Se connecter au portail Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Créer un espace de travail
Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.

![Portail Azure](media/quick-create-workspace/azure-portal-01.png)
  
Cliquez sur **Ajouter**, puis fournissez des valeurs pour les options suivantes :

   * Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   * Pour **Groupe de ressources**, choisissez d’utiliser un groupe de ressources déjà configuré ou créez-en un.  
   * Attribuez un nom au nouvel **Espace de travail Log Analytics** comme *DefaultLAWorkspace*. Ce nom doit être unique par groupe de ressources.
   * Sélectionnez une **Région** disponible.  Pour plus d’informations, consultez les [régions dans lesquelles Log Analytics est disponible](https://azure.microsoft.com/regions/services/) et recherchez Azure Monitor à partir du champ **Rechercher un produit**.  


        ![Créer le panneau de ressources Log Analytics](media/quick-create-workspace/create-workspace.png)  


Cliquez sur **Vérifier + créer** pour passer en revue les paramètres, puis sur **Créer** pour créer l’espace de travail. Cette action sélectionne le niveau tarifaire par défaut Paiement à l’utilisation, ce qui n’entraîne pas aucun changement tant que vous ne commencez pas à collecter une quantité suffisante de données. Pour plus d’informations d’autres niveaux tarifaires, consultez les [Détails de la tarification de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).



## <a name="troubleshooting"></a>Dépannage
Lorsque vous créez un espace de travail qui a été supprimé au cours des 14 derniers jours et qui se trouve dans l’[état de suppression réversible](../logs/delete-workspace.md#soft-delete-behavior), l’opération peut avoir des résultats différents en fonction de la configuration de votre espace de travail :
1. Si vous fournissez les mêmes nom d’espace de travail, groupe de ressources, abonnement et région que dans l’espace de travail supprimé, votre espace de travail est récupéré avec ses données, sa configuration et ses agents connectés.
2. Le nom de l’espace de travail doit être unique par groupe de ressources. Si vous utilisez un nom d’espace de travail qui existe déjà ou qui est en suppression réversible dans votre groupe de ressources, le message d’erreur suivant s’affiche : Le nom de l’espace de travail « workspace-name » n’est pas unique ou est en conflit avec un autre nom. Pour annuler la suppression réversible afin de supprimer définitivement votre espace de travail et d’en créer un nouveau sous le même nom, procédez comme suit pour récupérer l’espace de travail avant d’effectuer la suppression définitive :
   - [Récupérer](../logs/delete-workspace.md#recover-workspace) votre espace de travail
   - [Supprimer définitivement](../logs/delete-workspace.md#permanent-workspace-delete) votre espace de travail
   - Créer un espace de travail en reprenant le même nom d’espace de travail

## <a name="next-steps"></a>Étapes suivantes
Disposant à présent d’un espace de travail, vous pouvez configurer la collecte des données de télémétrie de surveillance, exécuter des recherches dans les journaux pour analyser ces données et ajouter une solution de gestion pour fournir des données et insights analytiques supplémentaires. 

* Pour créer des règles d’alerte et analyser l’intégrité de votre espace de travail, consultez [Analyser l’intégrité de l’espace de travail Log Analytics dans Azure Monitor](../logs/monitor-workspace.md). 
* Pour activer la collecte de données à partir de ressources Azure avec Diagnostics Azure ou le stockage Azure, consultez [Collecter des journaux d’activité et des métriques des services Azure à utiliser dans Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace).
