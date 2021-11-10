---
title: Surveiller visuellement Azure Data Factory
description: Découvrez comment superviser visuellement des fabriques de données Azure
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 79dcf37a5f70e68bddfc165ea8798994c7af9798
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845529"
---
# <a name="visually-monitor-azure-data-factory"></a>Surveiller visuellement Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Une fois que vous avez créé et publié un pipeline dans Azure Data Factory, vous pouvez l’associer à un déclencheur ou lancer manuellement une exécution ad hoc. Vous pouvez surveiller l’ensemble de vos exécutions de pipeline en mode natif dans l’expérience utilisateur d’Azure Data Factory. Pour ouvrir l’expérience de surveillance, sélectionnez la vignette **Surveiller et gérer** dans le panneau de fabrique de données du [Portail Azure](https://portal.azure.com/). Si vous êtes déjà dans l’expérience utilisateur ADF, cliquez sur l’icône **Surveiller** dans la barre latérale gauche.

Par défaut, toutes les exécutions de fabrique de données sont affichées dans le fuseau horaire local du navigateur. Si vous changez le fuseau horaire, tous les champs d’heure et de date sont alignés sur le fuseau horaire sélectionné.

## <a name="monitor-pipeline-runs"></a>Surveiller des exécutions de pipelines

L’affichage de la surveillance par défaut est la liste des exécutions de pipeline déclenchées dans la période sélectionnée. Vous pouvez modifier l’intervalle de temps et filtrer par état, nom de pipeline ou annotation. Pointez sur l’exécution du pipeline spécifique pour recevoir des actions spécifiques de l’exécution, telles que réexécuter, et le rapport consommation.

:::image type="content" source="media/monitor-visually/pipeline-runs.png" alt-text="Liste pour la supervision des exécutions de pipelines":::

La grille d’exécution du pipeline contient les colonnes suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom du pipeline | Nom du pipeline |
| Démarrage de l’exécution | Date et heure de début de l’exécution de pipeline (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Fin de l’exécution | Date et heure de fin de l’exécution de pipeline (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Duration | Durée d’exécution (HH:MM:SS) |
| Déclenché par | Nom du déclencheur qui a démarré le pipeline |
| Statut | **Échec**, **Réussite**, **En cours**, **Annulée** ou **En file d'attente** |
| Annotations | Balises filtrables associées à un pipeline  |
| Paramètres | Paramètres d’exécution de pipeline (paires nom/valeur) |
| Error | En cas d’échec du pipeline, l’erreur d’exécution |
| ID d’exécution | ID de l’exécution de pipeline |

Vous devez cliquer manuellement sur le bouton **Actualiser** pour actualiser la liste des exécutions de pipelines et d’activités. L’actualisation automatique n’est pas prise en charge actuellement.

:::image type="content" source="media/monitor-visually/refresh.png" alt-text="Bouton Actualiser":::

Pour afficher les résultats d’un débogage, sélectionnez l’onglet **Débogage**.

:::image type="content" source="media/iterative-development-debugging/view-debug-runs.png" alt-text="Sélectionner l’icône View active debug runs (Afficher les exécutions de débogage actives)":::

## <a name="monitor-activity-runs"></a>Surveiller des exécutions d’activités

Pour obtenir une vue détaillée des activités individuelles d’un pipeline spécifique, cliquez sur le nom de celui-ci.

:::image type="content" source="media/monitor-visually/view-activity-runs.png" alt-text="Afficher les exécutions d’activités":::

La liste montre les exécutions d’activités correspondant à chaque exécution de pipeline. Pointez sur l’activité spécifique pour obtenir des informations spécifiques de son exécution, telles que l’entrée JSON, la sortie JSON et des expériences de surveillance détaillées spécifiques de l’activité.

:::image type="content" source="media/monitor-visually/activity-runs.png" alt-text="Il existe des informations sur SalesAnalyticsMLPipeline, suivies d’une liste d’exécutions d’activités.":::

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom de l’activité | Nom de l’activité dans le pipeline |
| Type d’activité | Type de l’activité, par exemple **Copy**, **ExecuteDataFlow** ou **AzureMLExecutePipeline** |
| Actions | Icônes qui vous permettent de voir des informations d’entrée JSON, des informations de sortie JSON ou des expériences de surveillance détaillées sur les activités | 
| Démarrage de l’exécution | Date et heure de début de l’exécution de l’activité (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Duration | Durée d’exécution (HH:MM:SS) |
| Statut | **Échec**, **Réussite**, **En cours** ou **Annulée** |
| Runtime d’intégration | Runtime d’intégration sur lequel l’activité a été exécutée |
| Propriétés de l’utilisateur | Propriétés définies par l’utilisateur de l’activité |
| Error | En cas d’échec de l’activité, l’erreur d’exécution |
| ID d’exécution | ID de l’exécution d’activité |

En cas d’échec d’une activité, vous pouvez voir le message d’erreur détaillé en cliquant sur l’icône dans la colonne d’erreur. 

:::image type="content" source="media/monitor-visually/activity-run-error.png" alt-text="Une notification s’affiche avec les détails de l’erreur, notamment le code d’erreur, le type d’échec et les détails de l’erreur.":::

### <a name="promote-user-properties-to-monitor"></a>Promouvoir les propriétés de l’utilisateur à superviser

Promouvez n’importe quelle propriété de l’activité de pipeline en tant que propriété d’utilisateur pour la surveiller. Ainsi, vous pouvez promouvoir les propriétés **Source** et **Destination** de l’activité de copie dans votre pipeline en tant que propriétés d’utilisateur.

> [!NOTE]
> Vous pouvez promouvoir jusqu’à cinq propriétés de l’activité de pipeline en tant que propriétés d’utilisateur.

:::image type="content" source="media/monitor-visually/promote-user-properties.png" alt-text="Créer des propriétés d’utilisateur":::

Une fois les propriétés d’utilisateur créées, vous pouvez les superviser dans les listes de supervision.

:::image type="content" source="media/monitor-visually/choose-user-properties.png" alt-text="Ajouter des colonnes à la liste des exécutions d’activités pour les propriétés d’utilisateur":::

 Si la source de l’activité de copie est un nom de table, vous pouvez superviser le nom de table source en tant que colonne dans la liste des exécutions d’activités.

:::image type="content" source="media/monitor-visually/view-user-properties.png" alt-text="Liste des exécutions d’activités avec colonnes pour les propriétés d’utilisateur":::


## <a name="rerun-pipelines-and-activities"></a>Réexécuter des pipelines et activités
 
 Le comportement de réexécution des activités de conteneur est le suivant :
 
- `Wait` : l’activité se comporte comme avant.
- `Set Variable` : l’activité se comporte comme avant.
- `Filter` : l’activité se comporte comme avant.
- L’activité `Until` évalue l’expression et exécute une boucle jusqu’à ce que la condition soit satisfaite. Les activités internes peuvent toujours être ignorées en fonction des règles de réexécution.
- L’activité `Foreach` exécute toujours une boucle sur les éléments qu’elle reçoit. Les activités internes peuvent toujours être ignorées en fonction des règles de réexécution.
- `If and switch` : les conditions sont toujours évaluées. Les activités internes peuvent toujours être ignorées en fonction des règles de réexécution.
- `Execute pipeline activity` : le pipeline enfant est déclenché, mais toutes les activités du pipeline enfant peuvent quand même être ignorées en fonction des règles de réexécution.


Pour réexécuter un pipeline depuis le début, pointez dessus, puis sélectionnez **Réexécuter**. Si vous sélectionnez plusieurs pipelines, vous pouvez utiliser le bouton **Réexécuter** pour les exécuter tous.

:::image type="content" source="media/monitor-visually/rerun-pipeline.png" alt-text="Réexécuter un pipeline":::

Si vous souhaitez réexécuter un pipeline à partir d’un point spécifique, vous pouvez le faire à partir de l’affichage des exécutions d’activité. Sélectionnez l’activité à partir de laquelle vous souhaitez, puis choisissez **Réexécuter à partir d’une activité**. 

:::image type="content" source="media/monitor-visually/rerun-activity.png" alt-text="Réexécuter une activité":::

### <a name="rerun-from-failed-activity"></a>Réexécuter à partir d’une activité ayant échoué

Si une activité échoue, expire ou est annulée, vous pouvez réexécuter le pipeline à partir de cette activité ayant échoué en sélectionnant **Réexécuter à partir d’une activité ayant échoué**.

:::image type="content" source="media/monitor-visually/rerun-failed-activity.png" alt-text="Réexécuter une activité ayant échoué":::

### <a name="view-rerun-history"></a>Voir l’historique des réexécutions

Vous pouvez voir l’historique des réexécutions de toutes les exécutions du pipeline en mode Liste.

:::image type="content" source="media/monitor-visually/rerun-history-1.png" alt-text="Afficher l’historique":::

Vous pouvez également voir l’historique des réexécutions d’une exécution de pipeline spécifique.

:::image type="content" source="media/monitor-visually/view-rerun-history.png" alt-text="Voir l’historique d’une exécution de pipeline":::

## <a name="monitor-consumption"></a>Surveiller la consommation

Vous pouvez voir les ressources qu’une exécution de pipeline utilise en cliquant sur l’icône de consommation en regard de l’exécution. 

:::image type="content" source="media/monitor-visually/monitor-consumption-1.png" alt-text="Capture d’écran montrant où vous pouvez voir les ressources consommées par un pipeline.":::

Un clic sur l’icône ouvre un rapport consommation des ressources utilisées pour l’exécution de ce pipeline. 

:::image type="content" source="media/monitor-visually/monitor-consumption-2.png" alt-text="Surveiller la consommation":::

Vous pouvez connecter ces valeurs à la [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/details/data-factory/) pour estimer le coût d’exécution du pipeline. Pour plus d’informations sur la tarification d’Azure Data Factory, consultez [Compréhension de la tarification](pricing-concepts.md).

> [!NOTE]
> Ces valeurs retournées par la calculatrice de prix constituent une estimation. Celle-ci ne reflète pas le montant exact qu’Azure Data Factory vous facturera 

## <a name="gantt-views"></a>Vues Gantt

Un diagramme de Gantt est un affichage qui vous permet de voir l’historique des exécutions sur un intervalle de temps. En basculant vers un affichage de Gantt, vous pouvez voir toutes les exécutions de pipeline regroupées par nom, affichées sous forme de barres proportionnelles au temps d’exécution. Vous pouvez également regrouper par annotations/balises que vous avez créées sur votre pipeline. L’affichage de Gantt est également disponible au niveau de l’exécution de l’activité.

:::image type="content" source="media/monitor-visually/select-gantt.png" alt-text="Exemple de diagramme de Gantt":::

La longueur de la barre indique la durée du pipeline. Vous pouvez également sélectionner la barre pour voir plus de détails.

:::image type="content" source="media/monitor-visually/view-gantt-run.png" alt-text="Durée du diagramme de Gantt":::

## <a name="alerts"></a>Alertes

Vous pouvez déclencher des alertes sur les métriques prises en charge dans Data Factory. Pour commencer, sélectionnez **Superviser** > **Alertes et métriques** dans la page de supervision de Data Factory.

:::image type="content" source="media/monitor-visually/start-page.png" alt-text="Page de supervision de Data Factory":::

Pour voir une présentation de sept minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Créez des alertes

1.  Sélectionnez **Nouvelle règle d’alerte** pour créer une alerte.

    :::image type="content" source="media/monitor-visually/new-alerts.png" alt-text="Bouton Nouvelle règle d’alerte":::

1.  Spécifiez le nom de la règle et sélectionnez la gravité de l’alerte.

    :::image type="content" source="media/monitor-visually/name-and-severity.png" alt-text="Zones pour le nom de la règle et la gravité":::

1.  Sélectionnez les critères d’alerte.

    :::image type="content" source="media/monitor-visually/add-criteria-1.png" alt-text="Zone pour les critères cibles":::

    :::image type="content" source="media/monitor-visually/add-criteria-2.png" alt-text="Capture d’écran montrant où vous sélectionnez une métrique pour configurer la condition d’alerte.":::

    :::image type="content" source="media/monitor-visually/add-criteria-3.png" alt-text="Liste des critères":::

    Vous pouvez créer des alertes sur différentes métriques, notamment celles relatives au nombre/à la taille des entités ADF, aux exécutions d’activité/de pipeline/de déclencheur, à l’utilisation de l’UC/à la mémoire/au nombre de nœuds/à la file d’attente du runtime d’intégration (IR), ainsi qu’aux exécutions de packages SSIS et aux opérations de démarrage/d’arrêt de l’IR SSIS.

1.  Configurez la logique d’alerte. Vous pouvez créer une alerte pour la métrique sélectionnée pour tous les pipelines et activités correspondantes. Vous pouvez également sélectionner un type d’activité, un nom d’activité, un nom de pipeline ou un type d’échec particulier.

    :::image type="content" source="media/monitor-visually/alert-logic.png" alt-text="Options de configuration de la logique d’alerte":::

1.  Configurez les notifications vocales, push, par e-mail et par SMS pour l’alerte. Choisissez ou créez un groupe d’actions pour les notifications d’alerte.

    :::image type="content" source="media/monitor-visually/configure-notification-1.png" alt-text="Options de configuration des notifications":::

    :::image type="content" source="media/monitor-visually/configure-notification-2.png" alt-text="Options d’ajout de notification":::

1.  Créez la règle d’alerte

    :::image type="content" source="media/monitor-visually/create-alert-rule.png" alt-text="Options de création de règle d’alerte":::

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la supervision et la gestion des pipelines, consultez l’article [Surveiller et gérer les pipelines par programmation](./monitor-programmatically.md).
