---
title: 'Tutoriel : Superviser les ressources Azure Spring Cloud avec des alertes et des groupes d’actions | Microsoft Docs'
description: Découvrez comment utiliser les alertes Spring Cloud.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.custom: devx-track-java
ms.openlocfilehash: 98ac612782eff1b304473b12f3c8883a684bff28
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491676"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Tutoriel : Superviser les ressources Spring Cloud avec des alertes et des groupes d’actions

**Cet article s’applique à :** ✔️ Java ✔️ C#

Les alertes Azure Spring Cloud prennent en charge la supervision des ressources en fonction de conditions telles que le stockage disponible, le taux de requêtes ou l’utilisation des données. Une alerte envoie une notification quand les taux ou les conditions répondent aux spécifications définies.

Deux étapes sont nécessaires pour configurer un pipeline d’alerte :

1. Configurez un groupe d’actions avec les actions à entreprendre quand une alerte se déclenche, par exemple un e-mail, un SMS, un runbook ou un Webhook. Les groupes d’actions peuvent être réutilisés entre différentes alertes.
2. Configurez des règles d’alerte. Les règles lient les modèles de métrique aux groupes d’actions en fonction de l’élément cible : ressource, métrique, condition, agrégation de temps, etc.

## <a name="prerequisites"></a>Prérequis

En plus des exigences liées à Azure Spring, les procédures de ce tutoriel fonctionnent avec une instance Azure Spring Cloud déployée.  Pour bien démarrer, suivez un [guide de démarrage rapide](./quickstart.md).

Les procédures suivantes initialisent le **groupe d’actions** et l’**alerte** à partir de l’option **Alertes** dans le volet de navigation gauche d’une instance de Spring Cloud. (La procédure peut également démarrer à partir de la page **Vue d’ensemble d’Azure Monitor** du portail Azure.)

Accédez à partir d’un groupe de ressources à votre instance de Spring Cloud. Sélectionnez **Alertes** dans le volet gauche, puis sélectionnez **Gérer les actions** :

![Capture d’écran de la page du groupe de ressources du portail](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Configurer un groupe d’actions

Pour commencer la procédure d’initialisation d’un nouveau **groupe d’actions**, sélectionnez **Ajouter un groupe d’actions**.

![Capture d’écran d’ajout d’un groupe d’actions dans le portail](media/alerts-action-groups/action-1.png)

Dans la page **Ajouter un groupe d’actions** :

1. Spécifiez le **Nom du groupe d’actions** et le **Nom court**.

1. Spécifiez l’**Abonnement** et le **Groupe de ressources**.

1. Spécifiez le **Nom de l’action**.

1. Sélectionnez le **Type d’action**.  Cela entraîne l’ouverture d’un autre volet sur la droite, qui permet de définir l’action à entreprendre au moment de l’activation.

1. Définissez l’action à l’aide des options du volet droit.  Dans le cas présent, il s’agit d’utiliser une notification par e-mail.

1. Sélectionnez **OK** dans le volet d’action de droite.

1. Sélectionnez **OK** dans la boîte de dialogue **Ajouter un groupe d’actions**.

   ![Capture d’écran de définition d’action dans le portail](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Configurer une alerte

Les étapes précédentes ont permis de créer un **Groupe d’actions** qui utilise l’e-mail. Vous pouvez également utiliser les notifications par téléphone, les webhooks, les fonctions Azure, etc. Les étapes suivantes configurent une **alerte**.

1. Revenez à la page **Alertes** et sélectionnez **Gérer les règles d’alerte**.

   ![Capture d’écran de définition d’alerte dans le portail](media/alerts-action-groups/alerts-2.png)

1. Sélectionnez la **Ressource** nécessaire pour l’alerte.

1. Sélectionnez **Nouvelle règle d’alerte**.

   ![Capture d’écran d’ajout d’une nouvelle règle d’alerte dans le portail](media/alerts-action-groups/alerts-3.png)

1. Dans la page **Créer une règle**, spécifiez la **RESSOURCE**.

1. Le paramètre **CONDITION** fournit de nombreuses options pour superviser vos ressources **Spring Cloud**.  Sélectionnez **Ajouter** pour ouvrir le volet **Configurer la logique du signal**.

1. Sélectionnez une condition. Cet exemple utilise un **pourcentage d’utilisation de l’UC du système**.

   ![Capture d’écran d’ajout d’une nouvelle règle d’alerte 2 dans le portail](media/alerts-action-groups/alerts-3-1.png)

1. Faites défiler le volet **Configurer la logique du signal** pour définir la **Valeur de seuil** à superviser.

   ![Capture d’écran d’ajout d’une nouvelle règle d’alerte 3 dans le portail](media/alerts-action-groups/alerts-3-2.png)

1. Sélectionnez **Terminé**.

   Pour plus d’informations sur les conditions pouvant être supervisées, consultez [Options de métriques du portail de l’utilisateur](./concept-metrics.md#user-metrics-options).

1. Sous **ACTIONS**, sélectionnez **Sélectionner un groupe d’actions**. Dans le volet **ACTIONS**, sélectionnez le **Groupe d’actions** que vous avez défini.

   ![Capture d’écran d’ajout d’une nouvelle règle d’alerte 4 dans le portail](media/alerts-action-groups/alerts-3-3.png)

1. Faites défiler la page, puis, sous **DÉTAILS DE L’ALERTE**, nommez la règle d’alerte.

1. Définissez la **gravité**.

1. Sélectionnez **Créer une règle d’alerte**.

   ![Capture d’écran d’ajout d’une nouvelle règle d’alerte 5 dans le portail](media/alerts-action-groups/alerts-3-4.png)

1. Vérifiez que la nouvelle règle d’alerte est activée.

   ![Capture d’écran d’ajout d’une nouvelle règle d’alerte 6 dans le portail](media/alerts-action-groups/alerts-4.png)

Vous pouvez également créer une règle à l’aide de la page **Métriques** :

![Capture d’écran d’ajout d’une nouvelle règle d’alerte 7 dans le portail](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à configurer des alertes et des groupes d’actions pour une application dans Azure Spring Cloud. Pour en savoir plus sur les groupes d’actions, consultez :

> [!div class="nextstepaction"]
> [Créer et gérer des groupes d’actions dans le portail Azure](../azure-monitor/alerts/action-groups.md)

> [!div class="nextstepaction"]
> [Comportement des alertes par SMS dans les groupes d’actions](../azure-monitor/alerts/alerts-sms-behavior.md)
