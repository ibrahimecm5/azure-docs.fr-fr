---
title: 'Tutoriel : Créer une alerte de requête de journal pour une ressource Azure'
description: Tutoriel pour créer une alerte de requête de journal pour une ressource Azure.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 09/16/2021
ms.openlocfilehash: 526abdb9a5e3c16fe58551c2aa8784caf329d1c2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350670"
---
# <a name="tutorial-create-a-log-query-alert-for-an-azure-resource"></a>Tutoriel : Créer une alerte de requête de journal pour une ressource Azure
Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Les règles d’alerte de requête de journal créent une alerte quand une requête de journal retourne un résultat particulier. Par exemple, recevez une alerte quand un événement particulier est créé sur une machine virtuelle, ou envoyez un avertissement quand un trop grand nombre de demandes anonymes sont effectuées sur un compte de stockage.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Accéder aux requêtes de journal prédéfinies conçues pour prendre en charge des règles d’alerte pour différents types de ressources
> * Créer une règle d’alerte de requête de journal
> * Créer un groupe d’actions pour définir les détails de la notification


## <a name="prerequisites"></a>Prérequis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants : 

- Une ressource Azure à superviser. Vous pouvez utiliser n’importe quelle ressource de votre abonnement Azure qui prend en charge les paramètres de diagnostic. Pour déterminer si une ressource prend en charge les paramètres de diagnostic, accédez à son menu dans le portail Azure et vérifiez qu’une option **Paramètres de diagnostic** figure dans la section **Supervision** du menu.


Si vous utilisez une ressource Azure autre qu’une machine virtuelle :

- Un paramètre de diagnostic pour envoyer les journaux de ressource à partir de vos ressources Azure vers un espace de travail Log Analytics. Consultez [Tutoriel : Créer un espace de travail Log Analytics dans Azure Monitor](../essentials/tutorial-resource-logs.md).

Si vous utilisez une machine virtuelle Azure :

- Une règle de collecte de données pour envoyer des journaux et des métriques d’invité à un espace de travail Log Analytics. Consultez [Tutoriel : Collecter des journaux et métriques d’invité sur une machine virtuelle Azure](../vm/tutorial-monitor-vm-guest.md).

   
 
 ## <a name="select-a-log-query-and-verify-results"></a>Sélectionner une requête de journal et vérifier les résultats
Les données sont récupérées à partir d’un espace de travail Log Analytics à l’aide d’une requête de journal écrite en langage KQL (Kusto Query Language). Les insights et solutions dans Azure Monitor fournissent des requêtes de journal afin de récupérer des données pour un service particulier, mais vous pouvez travailler directement avec des requêtes de journal et leurs résultats dans le portail Azure avec Log Analytics. 

Sélectionnez **Journaux** dans le menu de votre ressource. Log Analytics s’ouvre avec la fenêtre **Requêtes** qui contient des requêtes prégénérées pour votre **Type de ressource**. Sélectionnez **Alertes** pour voir les requêtes spécifiquement conçues pour les règles d’alerte.

> [!NOTE]
> Si la fenêtre **Requêtes** ne s’ouvre pas, cliquez sur **Requêtes** en haut à droite. 

:::image type="content" source="media/tutorial-log-alert/queries.png" lightbox="media/tutorial-log-alert/queries.png"alt-text="Log Analytics avec la fenêtre des requêtes":::

Sélectionnez une requête et cliquez sur **Exécuter** pour la charger dans l’éditeur de requête et retourner des résultats. Vous pouvez modifier la requête et la réexécuter. Par exemple, la requête **Afficher les demandes anonymes** pour les comptes de stockage est indiquée ci-dessous. Vous pouvez modifier la valeur **AuthenticationType** ou filtrer sur une autre colonne.

:::image type="content" source="media/tutorial-log-alert/query-results.png" lightbox="media/tutorial-log-alert/query-results.png"alt-text="Résultats de la requête":::


## <a name="create-alert-rule"></a>Créer une règle d’alerte
Une fois que vous avez vérifié votre requête, vous pouvez créer la règle d’alerte. Sélectionnez **Nouvelle règle d’alerte** pour créer une règle d’alerte basée sur la requête de journal actuelle. L’**étendue** est déjà définie sur la ressource actuelle. Vous n’avez pas besoin de changer cette variable.

:::image type="content" source="media/tutorial-log-alert/create-alert-rule.png" lightbox="media/tutorial-log-alert/create-alert-rule.png"alt-text="Créer une règle d’alerte":::
## <a name="configure-condition"></a>Configurer une condition

Sous l’onglet **Condition**, la **Requête de journal** est déjà renseignée. La section **Mesure** définit la manière dont les enregistrements de la requête de journal sont mesurés. Si la requête ne calcule pas le total, la seule option est de **Compter** le nombre de **Lignes de table**. Si la requête comprend une ou plusieurs colonnes totalisées, vous pouvez utiliser le nombre de **Lignes de table** ou un calcul basé sur l’une des colonnes totalisées. La **Granularité d’agrégation** définit l’intervalle de temps sur lequel les valeurs collectées sont agrégées. 

:::image type="content" source="media/tutorial-log-alert/alert-rule-condition.png" lightbox="media/tutorial-log-alert/alert-rule-condition.png"alt-text="Condition de règle d’alerte":::

### <a name="configure-dimensions"></a>Configurer des dimensions
**Diviser par dimension** vous permet de créer des alertes distinctes pour différentes ressources. Ce paramètre est utile quand vous créez une règle d’alerte qui s’applique à plusieurs ressources. Si l’étendue est définie sur une seule ressource, ce paramètre n’est généralement pas utilisé.

:::image type="content" source="media/tutorial-log-alert/alert-rule-dimensions.png" lightbox="media/tutorial-log-alert/alert-rule-dimensions.png"alt-text="Dimensions de règle d’alerte":::


## <a name="configure-alert-logic"></a>Configurer une logique d’alerte
Dans la logique d’alerte, configurez l’**Opérateur** et la **Valeur de seuil** à comparer à la valeur retournée par la mesure.  Une alerte est créée quand cette valeur est True. Sélectionnez une valeur pour la **Fréquence d’évaluation** qui définit la fréquence d’exécution et d’évaluation de la requête de journal. Le coût de la règle d’alerte augmente quand la fréquence est basse. Quand vous sélectionnez une fréquence, le coût mensuel estimé s’affiche en plus d’un aperçu des résultats de la requête sur une période donnée.

Par exemple, si la mesure est **Lignes de table**, la logique d’alerte peut être **Supérieure à 0**, ce qui indique qu’au moins un enregistrement a été retourné. Si la mesure est une valeur de colonnes, la logique doit peut-être être supérieure ou inférieure à une valeur de seuil en particulier. Dans l’exemple ci-dessous, la requête de journal recherche des demandes anonymes dans un compte de stockage. Si une demande anonyme a été effectuée, nous devons déclencher une alerte. Dans ce cas, une seule ligne retournée déclenche l’alerte et la logique d’alerte doit être **Supérieure à 0**.

:::image type="content" source="media/tutorial-log-alert/alert-rule-alert-logic.png" lightbox="media/tutorial-log-alert/alert-rule-alert-logic.png"alt-text="Logique d'alerte":::



## <a name="configure-actions"></a>Configurer les actions
[!INCLUDE [Action groups](../../../includes/azure-monitor-tutorial-action-group.md)]

## <a name="configure-details"></a>Configurer les détails
[!INCLUDE [Alert details](../../../includes/azure-monitor-tutorial-alert-details.md)]

:::image type="content" source="media/tutorial-log-alert/alert-rule-details.png" lightbox="media/tutorial-log-alert/alert-rule-details.png"alt-text="Détails des règles d’alerte":::

Cliquez sur **Créer une règle d’alerte** pour créer la règle d’alerte.

## <a name="view-the-alert"></a>Voir l’alerte
[!INCLUDE [View alert](../../../includes/azure-monitor-tutorial-view-alert.md)]


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à créer une alerte de requête de journal pour une ressource Azure, consultez les workbooks pour créer des visualisations interactives des données de monitoring.

> [!div class="nextstepaction"]
> [Classeurs Azure Monitor](../visualize/workbooks-overview.md)