---
title: connecteur IoT Power BI Microsoft-api de santé Azure
description: Dans cet article, vous allez apprendre à utiliser le connecteur IoT et Power BI
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 014bf6fe316252536a14c15bad8255285226ce10
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894722"
---
# <a name="iot-connector-and-microsoft-power-bi"></a>Connecteur IoT et Power BI Microsoft

Dans cet article, nous allons explorer l’utilisation du connecteur IoT et de Microsoft Power Business Intelligence (BI).

## <a name="iot-connector-and-power-bi-reference-architecture"></a>connecteur IoT et architecture de référence Power BI

l’architecture de référence ci-dessous montre les composants de base de l’utilisation des services cloud Microsoft pour permettre l’Power BI sur Internet des données médicales (IoMT) et les données FHIR&#174;). 

vous pouvez même incorporer des tableaux de bord Power BI à l’intérieur du client Microsoft Teams pour améliorer la coordination de l’équipe de soins. pour plus d’informations sur l’incorporation d’Power BI dans Teams, consultez [cette page](/power-bi/collaborate-share/service-embed-report-microsoft-teams).

:::image type="content" source="media/iot-concepts/iot-connector-power-bi.png" alt-text="Capture d’écran du connecteur IoT et Power BI." lightbox="media/iot-concepts/iot-connector-power-bi.png":::

Le connecteur IoT peut ingérer des données IoT à partir de la plupart des appareils IoT ou des passerelles, quel que soit l’emplacement, le centre de données ou le Cloud. 

nous encourageons l’utilisation de Azure IoT services pour faciliter la connectivité de l’appareil/de la passerelle.

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-power-bi.png" alt-text="Capture d’écran du connecteur IoT, IoT Hub et Power BI." lightbox="media/iot-concepts/iot-connector-iot-hub-power-bi.png":::

pour certaines solutions, Azure IoT Central peut être utilisé à la place de Azure IoT Hub.

Azure IoT Edge peut être utilisé dans avec IoT Hub pour créer un point de terminaison local pour les appareils et/ou la connectivité dans l’appareil.

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-power-bi.png" alt-text="Capture d’écran du connecteur IoT, IoT Hub, IoT Edge et Power BI." lightbox="media/iot-concepts/iot-connector-iot-edge-power-bi.png":::

## <a name="next-steps"></a>Étapes suivantes

dans cet article, vous avez appris le connecteur IoT et l’intégration de Power BI. Pour obtenir une vue d’ensemble du connecteur IoT, consultez

>[!div class="nextstepaction"]
>[Vue d’ensemble du connecteur IoT](iot-connector-overview.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
