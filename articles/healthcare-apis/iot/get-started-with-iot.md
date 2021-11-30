---
title: Prise en main du connecteur IoT-API Azure Healthcare
description: Ce document explique comment prendre en main le connecteur IoT dans les API de santé Azure.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: 65080a0d884d6388f713ea80f4447f4c8d9fba7b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273145"
---
# <a name="get-started-with-the-iot-connector"></a>Prise en main avec le connecteur IOT

Cet article décrit les étapes de base pour prendre en main le connecteur IoT dans les [API de santé Azure](../healthcare-apis-overview.md). 

Comme condition préalable, vous aurez besoin d’un abonnement Azure et bénéficier des autorisations appropriées pour créer des groupes de ressources Azure et déployer des ressources Azure. Vous pouvez suivre toutes les étapes ou ignorer certains si vous disposez d’un environnement existant. En outre, vous pouvez combiner toutes les étapes et les compléter dans des scripts de l’API REST, de Azure CLI et de PowerShell.

[![Prise en main avec DICOM](media/get-started-with-iot.png)](media/get-started-with-iot.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Créer un espace de travail dans votre abonnement Azure

Vous pouvez créer un espace de travail à partir de la [portail Azure](../healthcare-apis-quickstart.md) ou à l’aide de PowerShell, Azure CLI et l’API REST]. Vous trouverez des scripts à partir des [exemples d’API de santé](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts).

> [!NOTE]
> Il existe des limites quant au nombre d’espaces de travail et au nombre d’instances de connecteur IoT que vous pouvez créer dans chaque abonnement Azure.

## <a name="create-the-fhir-service-and-an-event-hub"></a>Créer le service FHIR et un hub d’événements

Le connecteur IoT fonctionne avec Azure Event Hub et le service FHIR. Vous pouvez créer un nouveau [service FHIR](../fhir/get-started-with-fhir.md) ou en utiliser un existant dans un espace de travail identique ou différent. De même, vous pouvez créer un [hub d’événements](../../event-hubs/event-hubs-create.md) ou en utiliser un existant.


## <a name="create-a-iot-connector-in-the-workspace"></a>Créer un connecteur IoT dans l’espace de travail

Vous pouvez créer un connecteur IoT à partir de la [portail Azure](deploy-iot-connector-in-azure.md) ou à l’aide de PowerShell, Azure CLI ou de l’API REST. Vous trouverez des scripts à partir des [exemples d’API de santé](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts).

Si vous le souhaitez, vous pouvez créer un [service FHIR](../fhir/fhir-portal-quickstart.md) et un [service DICOM](../dicom/deploy-dicom-services-in-azure.md) dans l’espace de travail.

## <a name="assign-roles-to-allow-iot-to-access-event-hub"></a>Attribuer des rôles pour autoriser IoT à accéder au hub d’événements

Par défaut, le connecteur IoT récupère les données du concentrateur d’événements spécifié à l’aide de l’identité gérée par le système. Pour plus d’informations sur la façon d’attribuer le rôle au connecteur IoT à partir d' [Event Hub](../../healthcare-apis/iot/deploy-iot-connector-in-azure.md#granting-iot-connector-access).

## <a name="assign-roles-to-allow-iot-to-access-fhir-service"></a>Attribuer des rôles pour autoriser IoT à accéder au service FHIR

Le connecteur IoT conserve les données dans le magasin FHIR à l’aide de l’identité gérée par le système. Consultez les détails sur la façon d’attribuer le rôle au connecteur IoT à partir du [service FHIR](../../healthcare-apis/iot/deploy-iot-connector-in-azure.md#accessing-the-iot-connector-from-the-fhir-service).

## <a name="sending-data-to-the-iot-connector"></a>Envoi de données au connecteur IoT

Vous pouvez envoyer des données au hub d’événements, qui est associé au connecteur IoT. Si vous ne voyez aucune donnée dans le magasin FHIR, vérifiez les mappages et les attributions de rôles pour le connecteur IoT.

## <a name="iot-connector-mappings-data-flow-ml-power-bi-and-teams-notifications"></a>mappages de connecteur IoT, workflow de données, ML, Power BI et Teams notifications

vous trouverez plus d’informations sur les mappages de connecteur iot, le workflow de données, le service machine learning, les Power BI et les notifications de Teams dans la documentation du [connecteur iot](iot-connector-overview.md) .

## <a name="next-steps"></a>Étapes suivantes

Cet article décrit les étapes de base pour commencer à utiliser le connecteur IoT. Pour plus d’informations sur le déploiement du connecteur IoT dans l’espace de travail, consultez.

>[!div class="nextstepaction"]
>[Déployer IoT Connector dans le Portail Azure](deploy-iot-connector-in-azure.md)