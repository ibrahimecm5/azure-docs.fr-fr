---
title: Exporter les métriques de connecteur IoT via les paramètres de diagnostic-API de santé Azure
description: Cet article explique comment exporter des métriques de connecteur IoT via les paramètres de diagnostic
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: eda593fcbb1f7116d7f6a7a1c29af375ca354d6b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337222"
---
# <a name="export-iot-connector-metrics-through-diagnostic-settings"></a>Exporter les métriques du connecteur IoT via les paramètres de diagnostic

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à exporter les journaux de métriques du connecteur IoT. Ce sont les [**paramètres de diagnostic**](../../azure-monitor/essentials/diagnostic-settings.md) du portail Azure qui permettent d’activer la journalisation des métriques. 

## <a name="enable-metrics-logging-for-iot-connector"></a>Activer la journalisation des métriques pour le connecteur IoT
1. Pour activer la journalisation des métriques pour le connecteur IoT, sélectionnez votre service FHIR&#174; (Fast Healthcare Interoperability Resources) dans le Portail Azure. 

2. Accédez à **Paramètres de diagnostic**. 

3. Sélectionnez **+ Ajouter un paramètre de diagnostic**.

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Entrez un nom dans la boîte de dialogue **Nom du paramètre de diagnostic**.

5. Sélectionnez la méthode que vous souhaitez utiliser pour accéder à vos journaux de diagnostic :

    1. **Archivez les journaux dans un compte de stockage** pour les auditer ou les inspecter manuellement. Le compte de stockage que vous souhaitez utiliser doit déjà être créé.
    2. **Diffuser vers Event Hub** pour l’ingestion d’un service tiers ou d’une solution d’analyse personnalisée. Vous devez créer un espace de noms Event Hub et une stratégie de hub d’événements avant de pouvoir configurer cette étape.
    3. **Envoyez les journaux vers l’espace de travail Log Analytics** dans Azure Monitor. Vous devrez créer votre espace de travail Logs Analytics avant de pouvoir sélectionner cette option.

6. Sélectionnez les **Erreurs, le trafic et la latence pour le** connecteur IOT.  Sélectionnez les catégories de métriques supplémentaires que vous souhaitez capturer pour le service FHIR.

7. Sélectionnez **Enregistrer**.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> L’affichage des premiers journaux de métriques dans le référentiel de votre choix peut demander jusqu’à 15 minutes.  
 
Pour plus d’informations sur l’utilisation des journaux de diagnostic, consultez la [documentation relative aux journaux de ressources d’Azure](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="conclusion"></a>Conclusion 
L’accès aux journaux de métriques est essentiel pour la supervision et la résolution des problèmes.  Le connecteur IoT vous permet d’effectuer ces actions via les journaux de métriques. 

## <a name="next-steps"></a>Étapes suivantes

Consultez les questions fréquemment posées sur le connecteur IoT.

>[!div class="nextstepaction"]
>[FAQ sur le connecteur IoT](iot-connector-faqs.md)

(FHIR&#174;) est une marque déposée de HL7 qui est utilisée avec l’autorisation de HL7.
