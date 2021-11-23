---
title: 'Tutoriel : Collecter les journaux d’une ressource Azure'
description: Tutoriel permettant de configurer les paramètres de diagnostic pour envoyer les journaux d’une ressource Azure dans un espace de travail Log Analytics afin de pouvoir les analyser avec une requête de journal.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 11/08/2021
ms.openlocfilehash: 90a1f193bf1f136e4d3501b68bfab8346604fbbe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297969"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Tutoriel : Collecter et analyser des journaux de ressources à partir d’une ressource Azure
Les journaux de ressources fournissent des insights sur le fonctionnement détaillé des ressources Azure, et sont utiles pour superviser leur intégrité et leur disponibilité. Les ressources Azure génèrent automatiquement des journaux de ressource, mais vous devez créer un paramètre de diagnostic pour les collecter. Ce tutoriel vous guide tout au long du processus de création d’un paramètre de diagnostic pour envoyer des journaux de ressource à un espace de travail Log Analytics dans lequel vous pouvez les analyser avec des requêtes de journal.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un espace de travail Log Analytics dans Azure Monitor.
> * Créer un paramètre de diagnostic pour collecter des journaux de ressources. 
> * Créer une requête de journal simple pour analyser des journaux.


## <a name="prerequisites"></a>Prérequis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants : 

- Une ressource Azure à superviser. Vous pouvez utiliser n’importe quelle ressource de votre abonnement Azure qui prend en charge les paramètres de diagnostic. Pour déterminer si une ressource prend en charge les paramètres de diagnostic, accédez à son menu dans le portail Azure et vérifiez qu’une option **Paramètres de diagnostic** figure dans la section **Supervision** du menu.


> [!NOTE]
> Cette procédure ne s’applique pas aux machines virtuelles Azure, car leur menu **Paramètres de diagnostic** est utilisé pour configurer l’extension de diagnostic.

## <a name="create-a-log-analytics-workspace"></a>Créer un espace de travail Log Analytics
[!INCLUDE [Create workspace](../../../includes/azure-monitor-tutorial-workspace.md)]

## <a name="create-a-diagnostic-setting"></a>Créer un paramètre de diagnostic
Les [paramètres de diagnostic](../essentials/diagnostic-settings.md) définissent où les journaux de ressources doivent être envoyés pour une ressource particulière. Un même paramètre de diagnostic peut avoir plusieurs [destinations](../essentials/diagnostic-settings.md#destinations), mais nous n’utiliserons qu’un espace de travail Log Analytics dans ce tutoriel.

Sous la section **Monitoring** du menu de votre ressource, sélectionnez **Paramètres de diagnostic** et cliquez sur **Ajouter un paramètre de diagnostic**.

> [!NOTE]
> Certaines ressources peuvent nécessiter des sélections supplémentaires. Par exemple, pour un compte de stockage vous devez sélectionner une ressource pour pouvoir voir l’option **Ajouter un paramètre de diagnostic**. Vous pouvez également voir une étiquette **Préversion** pour certaines ressources si leurs paramètres de diagnostic sont actuellement en préversion publique.

:::image type="content" source="media/tutorial-resource-logs/diagnostic-settings.png" lightbox="media/tutorial-resource-logs/diagnostic-settings.png"alt-text="Paramètres de diagnostic":::


Chaque paramètre de diagnostic comporte trois parties :
 
   - **Name** : il n’a aucun effet significatif, et n’a pour but que de décrire le paramètre.
   - **Categories** : catégories de journaux à envoyer à chacune des destinations. L’ensemble des catégories varie en fonction de chaque service Azure.
   - **Destinations** : Une ou plusieurs destinations auxquelles envoyer les journaux. Tous les services Azure partagent le même ensemble de destinations possibles. Chaque paramètre de diagnostic peut définir une ou plusieurs destinations, mais pas plus d’une destination d’un type spécifique. 

Entrez un nom pour le paramètre de diagnostic et sélectionnez les catégories à collecter. Consultez la documentation de chaque service pour obtenir la définition de ses catégories disponibles. **AllMetrics** envoie à l’espace de travail les mêmes métriques de plateforme que celles disponibles dans les métriques Azure Monitor pour la ressource. Cela vous permet d’analyser ces données avec des requêtes de journal, ainsi que d’autres données de monitoring. Sélectionnez **Envoyer vers un espace de travail Log Analytics**, puis sélectionnez l’espace de travail que vous avez créé. 

:::image type="content" source="media/tutorial-resource-logs/diagnostic-setting-details.png" lightbox="media/tutorial-resource-logs/diagnostic-setting-details.png"alt-text="Détails du paramètre de diagnostic":::

Cliquez sur **Enregistrer** pour enregistrer les paramètres de diagnostic.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Utiliser une requête de journal pour récupérer des journaux
Les données sont récupérées à partir d’un espace de travail Log Analytics à l’aide d’une requête de journal écrite en langage KQL (Kusto Query Language). Un ensemble de requêtes précréées est disponible pour de nombreux services Azure. Vous n’avez donc pas besoin de connaître KQL pour commencer.

Sélectionnez **Journaux** dans le menu de votre ressource. Log Analytics s’ouvre avec la fenêtre **Requêtes** qui contient des requêtes prégénérées pour votre **Type de ressource**. 

> [!NOTE]
> Si la fenêtre **Requêtes** ne s’ouvre pas, cliquez sur **Requêtes** en haut à droite. 

:::image type="content" source="media/tutorial-resource-logs/queries.png" lightbox="media/tutorial-resource-logs/queries.png"alt-text="La capture d’écran montre des exemples de requêtes utilisant des journaux de ressource.":::


Parcourez les requêtes disponibles. Identifiez-en une à exécuter et cliquez sur **Exécuter**. La requête est ajoutée à la fenêtre de requête et les résultats sont retournés.

:::image type="content" source="media/tutorial-resource-logs/query-results.png" lightbox="media/tutorial-resource-logs/query-results.png"alt-text="La capture d’écran montre les résultats d’un exemple de requête de journal.":::

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous collectez des journaux de ressource, créez une alerte de requête de journal pour être averti de manière proactive quand des données intéressantes sont identifiées dans vos données de journal.

> [!div class="nextstepaction"]
> [Créer une alerte de requête de journal pour une ressource Azure](../alerts/tutorial-log-alert.md)
