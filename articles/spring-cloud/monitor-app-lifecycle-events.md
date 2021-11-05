---
title: Surveiller des événements de cycle de vie d’application à l’aide du Journal des activités Azure et d’Azure Service Health
description: Surveillez des événements de cycle de vie d’application et configurez des alertes avec le Journal des activités Azure et Azure Service Health.
author: karlerickson
ms.author: shiqiu
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: 594c8f48a51b386c687feda5d46a2423dd4e973b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064028"
---
# <a name="monitor-app-lifecycle-events-using-azure-activity-log-and-azure-service-health"></a>Surveiller des événements de cycle de vie d’application à l’aide du Journal des activités Azure et d’Azure Service Health

Cet article explique comment surveiller des événements de cycle de vie d’application et configurer des alertes avec le Journal des activités Azure et Azure Service Health.

Azure Spring Cloud fournit des outils intégrés pour surveiller l’état et l’intégrité de vos applications. Les événements du cycle de vie d’une application vous aident à comprendre les modifications apportées à vos applications afin de pouvoir prendre les mesures nécessaires. 

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Une instance du service Azure Spring Cloud déployée avec au moins une application déjà été créée dans celle-ci. Pour plus d’informations, consultez [Démarrage rapide : Déployer votre première application Spring Boot dans Azure Spring Cloud](quickstart.md). 

## <a name="monitor-app-lifecycle-events-triggered-by-users-in-azure-activity-logs"></a>Surveiller des événements de cycle de vie d’application déclenchés par des utilisateurs dans les journaux d’activité Azure

Les [journaux d’activité Azure](../azure-monitor/essentials/activity-log.md) contiennent des événements de ressource émis par des opérations effectuées sur les ressources de votre abonnement. Les détails relatifs aux événements de cycle de vie d’application (démarrage, arrêt, redémarrage) ajoutés dans les journaux d’activités Azure sont les suivants :

- Le moment où a eu lieu l’opération.
- L’état de l’opération.
- La ou les instances créées lors du démarrage de votre application
- La ou les instances supprimées lors de l’arrêt de votre application
- La ou les instances supprimées et créées lors du redémarrage de votre application

Par exemple, lorsque vous redémarrez votre application, vous pouvez trouver les instances affectées dans la page des détails du **Journal d’activité** sur le portail Azure.

:::image type="content" source="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" lightbox="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" alt-text="Capture d’écran du portail Azure montrant les détails d’un redémarrage dans le journal d’activité":::

## <a name="monitor-app-lifecycle-events-in-azure-service-health"></a>Surveiller des événements de cycle de vie d’application dans Azure Service Health

[Azure Resource Health](../service-health/resource-health-overview.md) vous aide à diagnostiquer des problèmes susceptibles d’affecter la disponibilité de votre service, ainsi qu’à obtenir une assistance pour les résoudre. Ces problèmes incluent les incidents de service, les périodes de maintenance planifiée et les pannes régionales. Les événements de redémarrage d’application sont ajoutés à Azure Service Health. Il s’agit aussi bien d’incidents inattendus (par exemple, une panne d’application non planifiée) que d’actions planifiées (par exemple, une maintenance planifiée).

### <a name="monitor-unplanned-app-lifecycle-events"></a>Surveiller des événements de cycle de vie d’application non planifiés

Lorsque votre application est redémarrée en raison d’événements non planifiés, votre instance Azure Spring Cloud affiche l’état **dégradé** dans la section **Intégrité des ressources** du portail Azure. L’état détérioré signifie que votre ressource a détecté une perte potentielle de performances, bien qu’elle soit toujours disponible pour utilisation. Les événements non planifiés sont, par exemple, des pannes d’application, des échecs de contrôle d’intégrité et des pannes système.

:::image type="content" source="media/monitor-app-lifecycle-events/resource-health-detail.png" alt-text="Capture d’écran du volet d’intégrité des ressources":::

Vous pouvez rechercher l’état le plus récent, la cause racine et les instances affectées dans la page de l’historique d’intégrité.

:::image type="content" source="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" lightbox="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" alt-text="Capture d’écran d’exemples de journaux pour des événements de cycle de vie d’application non planifiés":::


### <a name="monitor-planned-app-lifecycle-events"></a>Surveiller des événements de cycle de vie d’application planifiés

Votre application peut être redémarrée pendant une maintenance de plateforme. Vous pouvez recevoir une notification de maintenance à l’avance de la page **Maintenance planifiée** d’Azure Service Health.

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" alt-text="Capture d’écran d’un exemple de notification pour la maintenance planifiée":::

Lors d’une maintenance de plateforme, votre instance Azure Spring Cloud présente également un état **dégradé**. Si un redémarrage est nécessaire lors d’une maintenance de plateforme, Azure Spring Cloud effectue une mise à jour propagée pour mettre à jour vos applications de manière incrémentielle. Les mises à jour propagées sont conçues pour mettre à jour vos charges de travail sans temps d’arrêt. L’état le plus récent figure dans la page de l’historique d’intégrité.

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" alt-text="Capture d’écran d’un exemple de journal pour une maintenance planifiée en cours":::

>[!NOTE]
> Actuellement, Azure Spring Cloud effectue une maintenance planifiée régulière pour mettre à niveau la version Kubernetes sous-jacente tous les 2-4 mois. Pour obtenir une chronologie détaillée de la maintenance, vérifiez les notifications sur la page Azure Service Health.

## <a name="set-up-alerts"></a>Configurer des alertes

Vous pouvez configurer des alertes pour des événements de cycle de vie d’application. Les notifications sur l’intégrité du service sont également stockées dans le journal d’activité Azure. Le journal d’activité stockant un volume important d’informations, il existe une interface utilisateur distincte qui facilite l’affichage et la configuration d’alertes sur des notifications d’intégrité de service.

La liste suivante décrit les principales étapes nécessaires pour configurer une alerte : 

1. Configurez un groupe d’actions avec les actions à entreprendre lors du déclenchement d’une alerte. Les types d’actions sont, par exemple, l’envoi d’un appel vocal, SMS ou e-mail, ou le déclenchement de diverses actions automatisées. Plusieurs alertes peuvent utiliser le même groupe d’actions ou des groupes d’actions différents selon les besoins de l’utilisateur.
2. Configurez des règles d’alerte. Les alertes utilisent des groupes d’actions pour avertir les utilisateurs qu’une alerte pour un événement spécifique du cycle de vie de l’application a été déclenché.

### <a name="set-up-alerts-on-activity-log"></a>Configurer des alertes sur un journal d’activité

Les étapes suivantes montrent comment créer une règle d’alerte de journal d’activité dans le portail Azure :

1. Accédez à **Journal d’activité**, ouvrez la page des détails d’un journal d’activité quelconque, puis sélectionnez **Nouvelle règle d’alerte**.

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert.png" alt-text="Capture d’écran d’une alerte de journal d’activité":::

2. Sélectionnez l’**Étendue** de l’alerte.

3. Spécifiez la **Condition** de l’alerte.

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" alt-text="Capture d’écran d’une condition d’alerte de journal d’activité":::

4. Sélectionnez **Actions**, puis ajoutez les **Détails de la règle d’alerte**.

5. Sélectionnez **Créer une règle d’alerte**.

### <a name="set-up-alerts-to-monitor-app-lifecycle-events-in-azure-service-health"></a>Configurer des alertes pour surveiller des événements de cycle de vie d’application dans Azure Service Health

Les étapes suivantes montrent comment créer une règle d’alerte pour des notifications d’intégrité de service dans le portail Azure :

1. Accédez à **Intégrité des ressources** sous **Service Health**, puis sélectionnez **Ajouter une alerte d’intégrité de ressource**.

   :::image type="content" source="media/monitor-app-lifecycle-events/add-resource-health-alert.png" alt-text="Capture d’écran du volet d’intégrité des ressources avec le bouton « Ajouter une alerte d’intégrité » en évidence":::

2. Sélectionnez la **Ressource** nécessaire pour l’alerte.

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-target.png" alt-text="Capture d’écran d’une cible d’alerte d’intégrité de ressource":::

3. Spécifiez la **Condition d’alerte**.

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-condition.png" alt-text="Capture d’écran d’une condition d’alerte d’intégrité de ressource":::

4. Sélectionnez **Actions**, puis ajoutez les **Détails de la règle d’alerte**.

5. Sélectionnez **Créer une règle d’alerte**.

### <a name="set-up-alerts-to-monitor-the-planned-maintenance-notification"></a>Configurer des alertes pour surveiller la notification de maintenance planifiée

Les étapes suivantes montrent comment créer une règle d’alerte pour des notifications de maintenance planifiée dans le portail Azure :

1. Accédez à **Alertes d’intégrité** sous **Service Health**, puis sélectionnez **Ajouter une alerte d’intégrité de service**.

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert.png" alt-text="Capture d’écran du volet des alertes d’intégrité avec le bouton « Ajouter une alerte d’intégrité de service » en évidence":::

2. Entrez des valeurs pour **Abonnement**, **Service(s)** , **Région(s)** , **Type d’événement**, **Actions** et **Détails de la règle d’alerte**.

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" lightbox="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" alt-text="Capture d’écran du volet « Créer une alerte de règle » pour Service Health":::

3. Sélectionnez **Créer une règle d’alerte**.

## <a name="next-steps"></a>Étapes suivantes

[Diagnostiquer et résoudre les problèmes dans Azure Spring Cloud par vous-même](how-to-self-diagnose-solve.md)
