---
title: 'Tutoriel : Envoi d’une alerte quand une machine virtuelle Azure est en panne'
description: Créez une règle d’alerte dans Azure Monitor pour vous avertir de manière proactive si une machine virtuelle n’est pas disponible.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/04/2021
ms.openlocfilehash: af60844e6aced51989cbee07a787deba01a379f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350658"
---
# <a name="tutorial-create-alert-when-azure-virtual-machine-is-unavailable"></a>Tutoriel : Création d’une alerte qui se déclenche lorsqu’une machine virtuelle Azure n’est pas disponible
L’une des conditions d’alerte les plus courantes pour une machine virtuelle est son état d’exécution. Lorsque vous activez le monitoring avec des insights sur une machine virtuelle dans Azure Monitor, une pulsation est envoyée à Azure Monitor toutes les minutes. Vous pouvez créer une règle d’alerte de requête de journal qui envoie une alerte si aucune pulsation n’est détectée. Cette méthode permet de vous avertir non seulement si la machine virtuelle n’est pas en cours d’exécution, mais également si elle ne répond pas.


Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Afficher les données de journaux collectées par les insights sur une machine virtuelle dans Azure Monitor
> * Créer une règle d’alerte qui, à partir de données de journaux, vous informe de manière proactive si la machine virtuelle n’est pas disponible

## <a name="prerequisites"></a>Prérequis
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants : 

- Une machine virtuelle sur laquelle porte le monitoring
- Le monitoring avec les insights sur la machine virtuelle activés. Consultez [Tutoriel : Activation du monitoring pour une machine virtuelle Azure](tutorial-monitor-vm-enable.md).



## <a name="create-a-heartbeat-query"></a>Création d’une requête de pulsation
Il existe plusieurs façons de créer une règle d’alerte de requête de journal. Dans ce tutoriel, nous allons partir de l’onglet **Événements des journaux** de la vue **Carte**. Il donne un résumé des données de journaux collectées pour la machine virtuelle. 

:::image type="content" source="media/tutorial-monitor-vm/map-logs.png" lightbox="media/tutorial-monitor-vm/map-logs.png" alt-text="Vue Carte avec l’onglet Événements des journaux.":::

Cliquez sur **Pulsation** pour ouvrir Log Analytics, l’outil principal pour analyser les données de journaux collectées à partir de la machine virtuelle, à l’aide d’une simple requête sur les événements de pulsation. Si vous cliquez sur **TimeGenerated** pour effectuer un tri en fonction de cette colonne, vous pouvez voir qu’une pulsation est créée chaque minute.

:::image type="content" source="media/tutorial-monitor-vm/log-query-heartbeat.png" lightbox="media/tutorial-monitor-vm/log-query-heartbeat.png" alt-text="Alerte de requête de journal avec événements de pulsation.":::


Pour l’alerte, vous souhaitez ne renvoyer que les enregistrements de pulsations des cinq dernières minutes. Si aucun enregistrement n’est retourné, vous pouvez supposer que la machine virtuelle est en panne.

Ajoutez une ligne à la requête pour filtrer les résultats en les restreignant aux enregistrements créés au cours des cinq dernières minutes. Elle utilise la [fonction ago](/azure/data-explorer/kusto/query/agofunction), qui soustrait un certain intervalle de temps de l’heure actuelle.

```
Heartbeat
| where Computer == 'computer-name'
| where TimeGenerated > ago(5m)
```

Cliquez sur **Exécuter** pour afficher les résultats de cette requête, qui normalement ne comprennent que les pulsations des cinq dernières minutes.

:::image type="content" source="media/tutorial-monitor-vm/log-query-heartbeat-time-filter.png" lightbox="media/tutorial-monitor-vm/log-query-heartbeat-time-filter.png" alt-text="Alerte de requête de journal avec événements de pulsation utilisant le filtre de temps.":::

## <a name="create-alert-rule"></a>Créer une règle d’alerte
Maintenant que vous disposez de la requête de journal, vous pouvez créer une règle d’alerte qui envoie une alerte lorsque cette requête ne retourne aucun enregistrement. Si aucun enregistrement de pulsation n’est renvoyé sur les cinq dernières minutes, nous pouvons supposer que la machine virtuelle ne répondait pas pendant cet intervalle de temps. 

Cliquez sur **Nouvelle règle d'alerte** pour créer une règle à partir de la requête actuelle.

:::image type="content" source="media/tutorial-monitor-vm/new-alert-rule.png" lightbox="media/tutorial-monitor-vm/new-alert-rule.png" alt-text="Nouvelle règle d’alerte.":::


La **Requête de journal** est déjà renseignée dans la règle d’alerte. La **Mesure** est également déjà correcte, car nous voulons compter le nombre de lignes de table retournées par la requête. S’il est égal à zéro, une alerte doit être créée.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-01.png" lightbox="media/tutorial-monitor-vm/alert-rule-01.png" alt-text="Condition de règle d’alerte 1.":::

Faites défiler la page jusqu’à **Logique de l’alerte**, puis remplacez la valeur de **l’Opérateur** par **Égal à** et spécifiez une **Valeur seuil** de **0**. Cela signifie que nous souhaitons créer une alerte quand aucun enregistrement n’est retourné, ou lorsque le nombre d’enregistrements de la requête est égal à zéro.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-02.png" lightbox="media/tutorial-monitor-vm/alert-rule-02.png" alt-text="Condition de règle d’alerte 2.":::

## <a name="configure-action-group"></a>Configuration d’un groupe d'actions
La page **Actions** vous permet d’ajouter un ou plusieurs [groupes d’actions](../alerts/action-groups.md) à la règle d’alerte. Les groupes d’actions définissent un ensemble d’actions à entreprendre lorsqu’une alerte est déclenchée, par exemple l’envoi d’un e-mail ou d’un SMS.

Si vous disposez déjà d’un groupe d’actions, cliquez sur **Ajouter un groupe d’actions** pour ajouter un groupe existant à la règle d’alerte.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-actions.png" lightbox="media/tutorial-monitor-vm/alert-rule-actions.png" alt-text="Ajout d’un groupe d’actions à la règle d’alerte.":::

Si vous ne possédez pas encore de groupe d’actions dans votre abonnement, cliquez sur **Créer un groupe d’actions** pour en créer un. Sélectionnez un **Abonnement** et un **Groupe de ressources** pour le groupe d’actions. Ensuite, donnez-lui un **Nom de groupe d'actions** qui s’affichera sur le portail et un **Nom d'affichage** qui apparaîtra dans les notifications par e-mail et par SMS.

:::image type="content" source="media/tutorial-monitor-vm/action-group-basics.png" lightbox="./media/tutorial-monitor-vm/action-group-basics.png" alt-text="Informations de base sur le groupe d’actions.":::

Sélectionnez **Notifications** et ajoutez une ou plusieurs méthodes de notification des personnes concernées lorsque l’alerte est déclenchée.

:::image type="content" source="media/tutorial-monitor-vm/action-group-notifications.png" lightbox="./media/tutorial-monitor-vm/action-group-notifications.png" alt-text="Notifications du groupe d’actions.":::



## <a name="configure-details"></a>Configuration des détails
La page **Détails** permet de configurer différents paramètres de la règle d’alerte.

- Configurez **l’Abonnement** et le **Groupe de ressources** dans lesquels la règle d’alerte sera enregistrée. Il ne doit pas nécessairement s’agir du même groupe de ressources que celui de la ressource en cours de monitoring.
- Configurez la **Gravité** de l’alerte. Elle permet de regrouper les alertes présentant une importance relative similaire. Une gravité de niveau **Erreur** est appropriée pour une machine virtuelle qui ne répond pas.
- Maintenez la case **Activer l’alerte dès la création** cochée.
- Maintenez la case **Résoudre automatiquement les alertes** cochée. Elle a pour fonction de résoudre automatiquement l’alerte lorsque la machine virtuelle est à nouveau en ligne et que les enregistrements de pulsations sont à nouveau affichés.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-details.png" lightbox="media/tutorial-monitor-vm/alert-rule-details.png" alt-text="Détails de la règle d’alerte.":::

Cliquez sur **Vérifier + créer** pour créer la règle d’alerte.

## <a name="view-the-alert"></a>Affichage de l’alerte
Pour tester la règle d’alerte, arrêtez la machine virtuelle. Si vous avez configuré une notification dans votre groupe d’actions, vous devriez la recevoir au bout de quelques minutes. Vous verrez également une alerte indiquée dans le résumé qui s’affiche sur la page **Alertes** de la machine virtuelle.

:::image type="content" source="media/tutorial-monitor-vm/alerts-summary.png" lightbox="media/tutorial-monitor-vm/alerts-summary.png" alt-text="Résumé des alertes.":::

Cliquez sur la **Gravité** pour voir la liste de ces alertes. Cliquez sur l’alerte elle-même pour afficher ses détails.

:::image type="content" source="media/tutorial-monitor-vm/alerts-summary.png" lightbox="media/tutorial-monitor-vm/alerts-summary.png" alt-text="Liste des alertes.":::

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez créer une alerte à partir de données de journaux, collectez des journaux et des données de performances supplémentaires sur la machine virtuelle avec une règle de collecte des données.

> [!div class="nextstepaction"]
> [Collecte de journaux et métriques invités sur une machine virtuelle Azure](tutorial-monitor-vm-guest.md)

