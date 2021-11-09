---
title: Fichier include
description: Fichier include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/20/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1e9d61eca35cc9ea9fe20731fa093354c0eac5f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253566"
---
## <a name="trusted-microsoft-services"></a>Services Microsoft approuvés
Lorsque vous activez le paramètre **Autoriser les services Microsoft approuvés pour contourner ce pare-feu**, les services suivants sont autorisés à accéder à vos ressources Event Hubs.

| Service approuvé | Scénarios d’utilisation pris en charge | 
| --------------- | ------------------------- | 
| Azure Event Grid | Autorise Azure Event Grid à envoyer des événements à Event Hubs dans votre espace de noms Event Hubs. Vous devez également procéder comme suit : <ul><li>Activer une identité attribuée par le système pour un sujet ou un domaine</li><li>Ajouter l’identité au rôle d’expéditeur de données Azure Event Hubs sur l’espace de noms Event Hubs</li><li>Ensuite, configurez l’abonnement aux événements qui utilise un hub d’événements comme point de terminaison pour utiliser l’identité affectée au système.</li></ul> <p>Pour plus d’informations, consultez [Remise d’événement avec une identité managée](../../event-grid/managed-service-identity.md)</p>|
| Azure Monitor (paramètres de diagnostic et groupes d’actions) | Autorise Azure Monitor à envoyer des informations de diagnostic et des notifications d’alerte à des Event Hubs dans votre espace de noms Event Hubs. Azure Monitor peut lire à partir de l’Event Hub et y écrire des données. |
| Azure Stream Analytics | Permet à un travail Azure Stream Analytics de lire des données ([entrée](../../stream-analytics/stream-analytics-add-inputs.md)) ou d’écrire des données dans des hubs d’événements ([sortie](../../stream-analytics/event-hubs-output.md)) dans votre espace de noms Event Hubs. <p>**Important !** Le travail Stream Analytics doit être configuré de manière à utiliser une **identité managée** pour accéder à l’Event Hub. Pour plus d’informations, consultez [Accès à Event Hubs à partir d’un travail Azure Stream Analytics à l’aide des identités managées (préversion)](../../stream-analytics/event-hubs-managed-identity.md). </p>|
| Azure IoT Hub | Autorise IoT Hub à envoyer des messages aux hubs d’événements dans votre espace de noms Event Hub. Vous devez également procéder comme suit : <ul><li>Activer une identité attribuée par le système pour le hub IoT.</li><li>Ajouter l’identité au rôle d’expéditeur de données Azure Event Hubs sur l’espace de noms Event Hubs.</li><li>Ensuite, configurer l’IoT Hub qui utilise un Event Hub comme point de terminaison personnalisé pour utiliser l’authentification basée sur l’identité.</li></ul>
| Gestion des API Azure | <p>Le service API Management vous permet d'envoyer des événements à un hub d'événements de votre espace de noms Event Hubs.</p> <ul><li>Vous pouvez déclencher des flux de travail personnalisés en envoyant des événements à votre hub d'événements lorsqu'une API est appelée à l'aide de la [stratégie send-request](../../api-management/api-management-sample-send-request.md).</li><li>Vous pouvez également traiter un hub d'événements en tant que back-end dans une API. Pour obtenir un exemple de stratégie, consultez [S'authentifier à l'aide d'une identité managée pour accéder à un hub d'événements](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml). Vous devez également procéder comme suit :<ol><li>Activez l'identité attribuée par le système sur l'instance d'API Management. Pour obtenir des instructions, consultez [Utiliser des identités managées dans Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md).</li><li>Ajoutez l'identité au rôle **Expéditeur de données Azure Event Hubs** dans l'espace de noms Event Hubs.</li></ol></li></ul> | 
| Azure IoT Central | <p>Autorise IoT Central à exporter des données vers des Event Hubs dans votre espace de noms Event Hubs. Vous devez également procéder comme suit :</p><ul><li>Activez l’identité affectée par le système pour votre application IoT Central.</li><li>Ajoutez l’identité au rôle **Expéditeur de données Azure Event Hubs** dans l’espace de noms Event Hubs.</li><li>Ensuite, configurez la [destination d’exportation Event Hubs sur votre application IoT Central](../../iot-central/core/howto-export-data.md) pour utiliser l’authentification basée sur l’identité.</li>
