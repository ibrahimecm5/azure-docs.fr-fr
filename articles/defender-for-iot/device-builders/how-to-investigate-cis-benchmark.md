---
title: Investiguer la recommandation sur le benchmark CIS
description: Effectuez des investigations de base et avancées à partir des recommandations de base du système d’exploitation.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 8777b4c134dc92cd8e8a94424f57355239ee32c5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293570"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Examiner une recommandation de base du système d’exploitation (basée sur le test d’évaluation CIS)

Effectuez des investigations de base et avancées à partir des recommandations de base du système d’exploitation.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Investigation d’une recommandation de base de sécurité du système d’exploitation  

Vous pouvez examiner les recommandations de base du système d’exploitation en accédant à [Defender pour IoT dans le Portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started). Pour plus d’informations, consultez l’article expliquant comment [examiner des recommandations de sécurité](quickstart-investigate-security-recommendations.md).

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Investigation d’une recommandation avancée de sécurité du système d’exploitation  

Cette section décrit comment mieux comprendre les résultats des tests de base du système d’exploitation et interroger les événements dans Azure Log Analytics.  

L’investigation d’une recommandation avancée de sécurité du système d’exploitation est uniquement prise en charge à l’aide de Log Analytics. Connectez Defender pour IoT à un espace de travail Log Analytics avant de continuer. Pour plus d’informations sur les recommandations avancées de sécurité de base du système d’exploitation, consultez [Configurer une solution basée sur un agent Microsoft Defender pour IoT](how-to-configure-agent-based-solution.md).

Pour interroger vos événements de sécurité IoT dans Log Analytics pour des alertes :

1. Accédez à la page **Alertes**.

1. Sélectionnez **Examiner les recommandations dans l’espace de travail Log Analytics**.

Pour interroger vos événements de sécurité IoT dans Log Analytics pour des recommandations :

1. Accédez à la page **Recommandations**.

1. Sélectionnez **Examiner les recommandations dans l’espace de travail Log Analytics**.

1. Sélectionnez **Afficher les détails des règles de base du système d’exploitation** dans l’aperçu rapide **Détails de la recommandation** pour afficher les détails d’un appareil spécifique.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Affichez les détails d’un appareil spécifique.":::

Pour interroger vos événements de sécurité IoT directement dans l’espace de travail Log Analytics :

1. Accédez à la page **Journaux**.

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Sélectionnez des journaux dans le volet gauche.":::

1. Sélectionnez **Examiner les alertes**, ou choisissez l’option **Examiner les alertes dans Log Analytics** dans une recommandation de sécurité ou une alerte.

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Requêtes utiles pour examiner les ressources de base du système d’exploitation

> [!Note]
> Veillez à remplacer `<device-id>` par le ou les noms que vous avez donnés à votre appareil dans chacune des requêtes suivantes.

### <a name="retrieve-the-latest-information"></a>Récupérer les dernières informations

- **Échec de flotte d’appareils** : Exécutez la requête suivante pour récupérer les dernières informations sur les vérifications ayant échoué dans la flotte d’appareils :

    ```kusto
    let lastDates = SecurityIoTRawEvent |
    where RawEventName == "Baseline" |
    summarize TimeStamp=max(TimeStamp) by DeviceId;
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId |
    extend event = parse_json(EventDetails) |
    where event.BaselineCheckResult == "FAIL" |
    project DeviceId, event.BaselineCheckId, event.BaselineCheckDescription
    ```

- **Défaillance d’appareil spécifique** : exécutez la requête suivante pour récupérer les dernières informations sur les vérifications ayant échoué sur un appareil spécifique :  

    ```kusto
    let id = SecurityIoTRawEvent | 
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)) |
    where TimeGenerated <= now() |
    where RawEventName == "Baseline" |
    where DeviceId == "<device-id>" |
    summarize arg_max(TimeGenerated, IoTRawEventId) |
    project IoTRawEventId;
    SecurityIoTRawEvent |
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)), extraDetails = todynamic(EventDetails) |
    where IoTRawEventId == toscalar(id) |
    where extraDetails.BaselineCheckResult == "FAIL" |
    project DeviceId, CceId = extraDetails.BaselineCheckId, Description = extraDetails.BaselineCheckDescription
    ```

- **Erreur d’appareil spécifique** : exécutez cette requête pour récupérer les dernières informations sur les vérifications comportant une erreur sur un appareil spécifique :

    ```kusto
    let id = SecurityIoTRawEvent |
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)) |
    where TimeGenerated <= now() |
    where RawEventName == "Baseline" |
    where DeviceId == "<device-id>" |
    summarize arg_max(TimeGenerated, IoTRawEventId) |
    project IoTRawEventId;
    SecurityIoTRawEvent |
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)), extraDetails = todynamic(EventDetails) |
    where IoTRawEventId == toscalar(id) |
    where extraDetails.BaselineCheckResult == "ERROR" |
    project DeviceId, CceId = extraDetails.BaselineCheckId, Description = extraDetails.BaselineCheckDescription
    ```

- **Mettre à jour la liste des appareils d’une flotte d’appareils qui ont échoué à un test spécifique** : exécutez cette requête pour récupérer la liste mise à jour des appareils (dans la flotte d’appareils) qui ont échoué à un test spécifique :  

    ```kusto
    let lastDates = SecurityIoTRawEvent |
    where RawEventName == "Baseline" |
    summarize TimeStamp=max(TimeStamp) by DeviceId;
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId |
    extend event = parse_json(EventDetails) |
    where event.BaselineCheckResult == "FAIL" |
    where event.BaselineCheckId contains "6.2.8" |
    project DeviceId;
    ```

## <a name="next-steps"></a>Étapes suivantes

[Investiguer les recommandations de sécurité](quickstart-investigate-security-recommendations.md).
