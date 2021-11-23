---
title: 'Démarrage rapide : Investiguer les alertes de sécurité'
description: Comprenez, explorez et investiguez les alertes de sécurité Defender pour IoT sur vos appareils IoT.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: c55392650edf85c753bcde32a03e8ad313ebd148
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331506"
---
# <a name="quickstart-investigate-security-alerts"></a>Démarrage rapide : Investiguer les alertes de sécurité

Une investigation et une correction planifiées des alertes émises par Defender pour IoT constituent la meilleure façon de garantir la conformité et la protection dans votre solution IoT.

## <a name="investigate-new-security-alerts"></a>Investiguer de nouvelles alertes de sécurité

La liste des alertes de sécurité IoT Hub affiche toutes les alertes de sécurité agrégées pour votre IoT Hub. 

1. Dans le portail Azure, ouvrez l’**IoT Hub** à investiguer pour de nouvelles alertes.

1. Dans le menu **Sécurité**, sélectionnez **Alertes**. Toutes les alertes de sécurité pour l’IoT Hub s’affichent. Les alertes avec un indicateur **Nouvelle** marquent les alertes des dernières 24 heures.

    :::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Investiguer les nouvelles alertes de sécurité IoT à l’aide de l’indicateur de nouvelle alerte":::

1. Sélectionnez une alerte dans la liste pour ouvrir ses détails et en comprendre les spécificités. 

## <a name="security-alert-details"></a>Détails des alertes de sécurité

L’ouverture de chaque alerte agrégée affiche la description détaillée de celle-ci, les étapes de correction et l’ID de chaque appareil ayant déclenché une alerte. La gravité d’alerte et un accès direct à l’investigation sont disponibles à l’aide de Log Analytics. 

1. Accédez à **IoT Hub** > **Sécurité** > **Alertes**. 

1. Sélectionnez une alerte de sécurité dans la liste pour l’ouvrir. 

1. Examinez la **description**, la **gravité**, la **source de la détection** et les **détails de l’appareil** en lien avec l’alerte de tous les appareils qui ont émis celle-ci au cours de la période d’agrégation.

    :::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Examiner et vérifier les détails de chaque appareil dans une alerte agrégée"::: 

1. Après avoir vérifié les spécificités de l’alerte, suivez les instructions de l’**étape de correction manuelle** afin d’atténuer et de résoudre le problème à l’origine de l’alerte.

    :::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Suivre les étapes de correction manuelle pour vous atténuer ou résoudre les alertes de sécurité de votre appareil":::

1. Si une investigation supplémentaire est nécessaire, **examinez les alertes dans Log Analytics** en suivant le lien.
 
    :::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="Pour investiguer davantage sur une alerte, utiliser le lien pour examiner à l’aide de Log Analytics affiché à l’écran":::

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour en savoir plus sur les types d’alertes de sécurité et les personnalisations possibles.

> [!div class="nextstepaction"]
> [Compréhension des alertes de sécurité IoT](concept-security-alerts.md)
