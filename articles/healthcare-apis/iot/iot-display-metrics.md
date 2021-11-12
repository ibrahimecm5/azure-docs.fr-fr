---
title: Afficher la journalisation des métriques du connecteur IoT-API Azure Healthcare
description: Cet article explique comment afficher les métriques d’un connecteur IoT
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: 6718270b7b996813f4503e58cf2609e7eb3add00
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349397"
---
# <a name="how-to-display-iot-connector-metrics"></a>Comment afficher les métriques d’un connecteur IoT

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à afficher les métriques de connecteur IoT dans le Portail Azure. 

## <a name="display-metrics"></a>Afficher les métriques

1. Dans votre espace de travail API Azure Healthcare, sélectionnez **connecteurs IOT**. 

     :::image type="content" source="media\iot-metrics\iot-workspace-displayed-with-connectors-button.png" alt-text="Sélectionnez le bouton connecteurs IoT." lightbox="media\iot-metrics\iot-connectors-button.png"::: 

2. Sélectionnez le connecteur IoT pour lequel vous souhaitez afficher les mesures.

    :::image type="content" source="media\iot-metrics\iot-connector-select.png" alt-text="Sélectionnez le connecteur IoT pour lequel vous souhaitez afficher les métriques." lightbox="media\iot-metrics\iot-connector-select.png":::
    
3. Sélectionnez **métriques** dans la page connecteur IOT.

   :::image type="content" source="media\iot-metrics\iot-select-metrics.png" alt-text="Cliquez sur le bouton Métriques." lightbox="media\iot-metrics\iot-metrics-button.png"::: 

4. À partir de la page métriques, vous pouvez créer les métriques que vous souhaitez afficher pour votre connecteur IoT. Pour cet exemple, nous allons choisir les sélections suivantes :

    * **Scope** = nom du connecteur IOT (**par défaut**)
    * **Metric namespace** = métriques standard (**par défaut**) 
    * **Metric** = métriques du connecteur IOT que vous souhaitez afficher. Pour cet exemple, nous allons choisir le **nombre de messages entrants**.
    * **Aggregation** = la manière dont vous souhaitez afficher les mesures. Pour cet exemple, nous allons choisir **Count**. 

    :::image type="content" source="media\iot-metrics\iot-select-metrics-to-display.png" alt-text="Sélectionnez les mesures à afficher." lightbox="media\iot-metrics\iot-metrics-selection-close-up.png"::: 

5. Nous pouvons maintenant voir les métriques du connecteur IoT pour le **nombre de messages entrants** affichés sur le portail Azure.

    > [!TIP]
    > Vous pouvez ajouter des métriques supplémentaires en sélectionnant le bouton **Ajouter une mesure** et en effectuant vos choix.

    :::image type="content" source="media\iot-metrics\iot-metrics-add-button.png" alt-text="Sélectionnez le bouton Ajouter une mesure pour ajouter d’autres métriques." lightbox="media\iot-metrics\iot-add-metric-button.png":::

    > [!IMPORTANT]
    > Si vous laissez la page métriques, les paramètres de métriques sont perdus et devront être recréés. Si vous souhaitez enregistrer les mesures de votre connecteur IoT pour un affichage ultérieur, vous pouvez les épingler à un tableau de bord Azure sous forme de vignette.

## <a name="pinning-metrics-tile-on-azure-portal-dashboard"></a>Vignette épinglage des métriques sur Portail Azure tableau de bord

1. Pour épingler la vignette métriques à un tableau de bord Portail Azure, sélectionnez le bouton **Épingler au tableau de bord** :

    :::image type="content" source="media\iot-metrics\iot-metrics-select-add-pin-to-dashboard.png" alt-text="Sélectionnez le bouton épingler au tableau de bord." lightbox="media\iot-metrics\iot-pin-to-dashboard-button.png":::

2. Sélectionnez le tableau de bord sur lequel vous souhaitez afficher les métriques de connecteur IoT. Pour cet exemple, nous allons utiliser un tableau de bord privé nommé `IoT connector Metrics` . Sélectionnez **épingler** pour ajouter la vignette mesures au tableau de bord.

    :::image type="content" source="media\iot-metrics\iot-select-pin-to-dashboard.png" alt-text="Sélectionnez tableau de bord et bouton épingler pour terminer le processus d’épinglage du tableau de bord." lightbox="media\iot-metrics\iot-select-pin-to-dashboard.png":::

3. Vous recevrez une confirmation indiquant que la vignette des mesures a été correctement ajoutée au tableau de bord.

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-pinned-successful.png" alt-text="Vignette des métriques correctement épinglée au tableau de bord." lightbox="media\iot-metrics\iot-select-dashboard-pinned-successful.png":::

4. Une fois que vous avez reçu une confirmation réussie, sélectionnez **tableau de bord**.

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-with-metrics-tile.png" alt-text="Sélectionnez le bouton tableau de bord." lightbox="media\iot-metrics\iot-dashboard-button.png":::

5. Sélectionnez le tableau de bord auquel vous avez épinglé la vignette mesures. Pour cet exemple, le tableau de bord est `IoT connector Metrics` . Le tableau de bord affiche la vignette des métriques du connecteur IoT que vous avez créée lors des étapes précédentes.

    :::image type="content" source="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png" alt-text="Tableau de bord avec la vignette des métriques du connecteur IoT épinglé." lightbox="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png":::

## <a name="conclusion"></a>Conclusion 

L’accès aux métriques est essentiel pour la surveillance et le dépannage.  Le connecteur IoT vous aide à effectuer ces actions par le biais de mesures. 

## <a name="next-steps"></a>Étapes suivantes

Consultez les questions fréquemment posées sur le connecteur IoT.

>[!div class="nextstepaction"]
>[FAQ sur le connecteur IoT](iot-connector-faqs.md)

(FHIR&#174;) est une marque déposée de HL7 qui est utilisée avec l’autorisation de HL7.
