---
title: recevoir des données de l’appareil via Azure IoT Hub-api de santé Azure
description: Dans ce didacticiel, vous allez apprendre à activer le routage des données de l’appareil à partir de IoT Hub vers le service FHIR via IoT Connector.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 9c4dd42d81374f75beb66f0564a2fb2b0fc38c01
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709212"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Tutoriel : Recevoir des données d’appareil via Azure IoT Hub

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Le connecteur IoT offre la possibilité de collecter et d’ingérer des données d’intégrité à partir de divers périphériques médicaux ou liés à l’intégrité IoT dans le service FHIR® (Fast Healthcare Interoperability Resources). le connecteur IoT est interopérable et réactif avec les appareils créés et gérés via Azure IoT Hub pour des flux de travail et une facilité d’utilisation améliorés. Ce tutoriel présente la procédure à suivre pour connecter et router des données d’appareil depuis Azure IoT Hub vers le connecteur IoT.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure actif - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ressource de service FHIR avec au moins un connecteur IoT- [déployer un connecteur IOT à l’aide de portail Azure](deploy-iot-connector-in-azure.md)
- Une ressource Azure IoT Hub connectée à un ou plusieurs appareils réels ou simulés - [Créer un hub IoT à l’aide du portail Azure](../../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)

> [!TIP]
> Si vous utilisez une application d’appareil simulé Azure IoT Hub, vous pouvez librement choisir entre les différents langages et systèmes pris en charge.

## <a name="get-connection-string-for-iot-connector"></a>Obtenir la chaîne de connexion pour IoT Connector

Azure IoT Hub requiert une chaîne de connexion pour se connecter en toute sécurité à votre connecteur IoT. Créez une nouvelle chaîne de connexion pour votre connecteur IoT, comme décrit dans [générer une chaîne de connexion](../azure-api-for-fhir/iot-fhir-portal-quickstart.md#generate-a-connection-string). Coffre cette chaîne de connexion à utiliser à l’étape suivante.

En arrière-plan, le connecteur IoT utilise une instance Azure Event Hub pour recevoir les messages d’appareil. La chaîne de connexion créée précédemment est, fondamentalement, la chaîne de connexion à ce hub d’événements sous-jacent.

## <a name="connect-azure-iot-hub-with-iot-connector"></a>Connecter Hub Azure IoT avec le connecteur IoT

Azure IoT Hub prend en charge une fonctionnalité appelée [routage des messages](../../iot-hub/iot-hub-devguide-messages-d2c.md), qui permet d’envoyer des données d’appareil à différents services Azure comme le compte de stockage, Event Hub et Service Bus. le connecteur IoT utilise cette fonctionnalité pour connecter et envoyer des données de l’appareil à partir de Azure IoT hub vers son point de terminaison Event hub.

> [!NOTE] 
> À ce stade, vous pouvez uniquement utiliser la commande PowerShell ou CLI pour [créer le routage des messages](../../iot-hub/tutorial-routing.md) parce que le hub d’événements du connecteur IOT n’est pas hébergé sur l’abonnement du client. par conséquent, il ne sera pas visible via le portail Azure. Toutefois, quand les objets de routage de message sont ajoutés à l’aide de PowerShell ou de l’interface CLI, vous pouvez les voir sur le portail Azure et vous pouvez les gérer à partir du portail.

La configuration d’un routage de message s’effectue en deux étapes.

### <a name="add-an-endpoint"></a>Ajout d’un point de terminaison

Cette étape consiste à définir un point de terminaison vers lequel le hub IoT Hub va router les données. Créez ce point de terminaison en utilisant la commande PowerShell [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIoTHubRoute) ou la commande CLI [az iot hub routing-endpoint create](/cli/azure/iot/hub/route#az_iot_hub_route_create) selon vos préférences.

Voici la liste des paramètres à utiliser avec la commande pour créer un point de terminaison :

|Paramètre PowerShell|Paramètre CLI|Description|
|---|---|---|
|ResourceGroupName|resource-group|Nom du groupe de ressources de votre ressource IoT Hub.|
|Nom|hub-name|Nom de votre ressource IoT Hub.|
|EndpointName|endpoint-name|Utilisez un nom que vous souhaitez attribuer au point de terminaison en cours de création.|
|EndpointType|endpoint-type|Entrez un point de terminaison auquel IoT Hub doit se connecter. Utilisez la valeur littérale « EventHub » pour PowerShell et « eventhub » pour l’interface CLI.|
|EndpointResourceGroup|endpoint-resource-group|Nom du groupe de ressources pour la ressource de service FHIR de votre connecteur IoT. Vous pouvez récupérer cette valeur à partir de la page vue d’ensemble du service FHIR.|
|EndpointSubscriptionID|endpoint-subscription-id|ID d’abonnement de la ressource du service FHIR de votre connecteur IoT. Vous pouvez récupérer cette valeur à partir de la page vue d’ensemble du service FHIR.|
|ConnectionString|connection-string|Chaîne de connexion à votre connecteur IoT. Utilisez la valeur que vous avez obtenue à l’étape précédente.|

### <a name="add-a-message-route"></a>Ajout d’une route de message
Cette étape consiste à définir une route de message en utilisant le point de terminaison créé précédemment. Créez une route en utilisant la commande PowerShell [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) ou la commande CLI [az iot hub route create](/cli/azure/iot/hub/route), selon vos préférences.

Voici la liste des paramètres à utiliser avec la commande pour ajouter un itinéraire de message :

|Paramètre PowerShell|Paramètre CLI|Description|
|---|---|---|
|ResourceGroupName|g|Nom du groupe de ressources de votre ressource IoT Hub.|
|Nom|hub-name|Nom de votre ressource IoT Hub.|
|EndpointName|endpoint-name|Nom du point de terminaison que vous avez créé précédemment.|
|RouteName|route-name|Nom que vous souhaitez attribuer à la route de message en cours de création.|
|Source|source-type|Type de donnée à envoyer au point de terminaison. Utilisez la valeur littérale « DeviceMessages » pour PowerShell et « devicemessages » pour l’interface CLI.|

## <a name="send-device-message-to-azure-iot-hub"></a>envoyer un message de l’appareil à Azure IoT Hub

Utilisez votre appareil (réel ou simulé) pour envoyer l’exemple de message de fréquence cardiaque présenté ci-dessous à Azure IoT Hub. Ce message est acheminé vers le connecteur IoT, où le message est transformé en ressource d’observation FHIR et stocké dans le service FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Veillez à envoyer le message de l’appareil conforme aux modèles de [mappage](how-to-use-fhir-mappings.md) configurés avec votre connecteur IOT.

## <a name="view-device-data-in-fhir-service"></a>Afficher les données de l’appareil dans le service FHIR

Vous pouvez afficher la ou les ressources d’observation FHIR créées par IoT Connector sur le service FHIR à l’aide de la publication. Pour plus d’informations, consultez [accéder au service FHIR à l’aide](./../use-postman.md)de l’affranchissement et envoyer une `GET` demande à `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` pour afficher les ressources d’observation FHIR avec une valeur de fréquence cardiaque envoyée dans l’exemple de message ci-dessus.

> [!TIP]
> Assurez-vous que votre utilisateur dispose de l’accès approprié au plan de données du service FHIR. Utilisez le [contrôle d’accès en fonction du rôle Azure](../azure-api-for-fhir/configure-azure-rbac.md) (RBAC Azure) pour attribuer les rôles de plan de données nécessaires.

## <a name="next-steps"></a>Étapes suivantes

dans ce didacticiel, vous configurez Azure IoT Hub pour acheminer les données des appareils vers le connecteur IoT. Sélectionnez l’une des étapes suivantes pour en savoir plus sur le connecteur IoT :

Découvrez les différentes étapes du workflow dans IoT Connector.

>[!div class="nextstepaction"]
>[Workflow de données du connecteur IoT](iot-data-flow.md)

(FHIR&#174;) est une marque déposée de HL7 qui est utilisée avec l’autorisation de HL7.
