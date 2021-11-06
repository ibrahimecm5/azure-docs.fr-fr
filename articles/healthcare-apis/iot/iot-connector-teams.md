---
title: notifications de Teams et de connecteur IoT-api de santé Azure
description: dans cet article, vous allez apprendre à utiliser le connecteur IoT et les notifications de Teams
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 5f2cb9c9a4e4ef20e489c36a92285a6f96f74282
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894732"
---
# <a name="iot-connector-and-microsoft-teams-notifications"></a>connecteur IoT et notifications de Microsoft Teams

dans cet article, nous allons explorer l’utilisation du connecteur IoT et Microsoft Teams pour les notifications.

## <a name="iot-connector-and-teams-notifications-reference-architecture"></a>architecture de référence de connecteur IoT et de notifications de Teams

lorsque vous combinez un connecteur IoT, un service FHIR&#174; (Fast Healthcare interoperability resources) et Teams, vous pouvez activer plusieurs solutions de soins. 

vous trouverez ci-dessous le connecteur iot pour Teams architecture des notifications pour activer le connecteur iot, FHIR et Teams application Patient. 

vous pouvez même incorporer des tableaux de bord Power BI dans le client Microsoft Teams. pour plus d’informations sur l’incorporation de Power BI dans l’équipe Microsoft, consultez [ici](/power-bi/collaborate-share/service-embed-report-microsoft-teams).

:::image type="content" source="media/iot-concepts/iot-connector-teams.png" alt-text="Capture d’écran du connecteur IoT et Teams." lightbox="media/iot-concepts/iot-connector-teams.png":::

Le connecteur IoT pour peut recevoir des données IoT à partir de la plupart des appareils IoT ou des passerelles, quel que soit l’emplacement, le centre de données ou le Cloud. 

nous encourageons l’utilisation de Azure IoT services pour faciliter la connectivité de l’appareil/de la passerelle.

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-teams.png" alt-text="Capture d’écran du connecteur IoT et IoT Hub." lightbox="media/iot-concepts/iot-connector-iot-hub-teams.png":::

pour certaines solutions, Azure IoT Central peut être utilisé à la place de Azure IoT Hub.

Azure IoT Edge peut être utilisé dans avec IoT Hub pour créer un point de terminaison local pour les appareils et/ou la connectivité dans l’appareil.

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-teams.png" alt-text="Capture d’écran du connecteur IoT et IoT Edge." lightbox="media/iot-concepts/iot-connector-iot-edge-teams.png":::

## <a name="next-steps"></a>Étapes suivantes

dans cet article, vous avez appris le connecteur IoT et l’intégration d’Teams notifications. Pour obtenir une vue d’ensemble du connecteur IoT, consultez

>[!div class="nextstepaction"]
>[Vue d’ensemble du connecteur IoT](iot-connector-overview.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
