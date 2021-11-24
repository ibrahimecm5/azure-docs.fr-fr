---
title: projets de GitHub associés pour le connecteur IoT-api de santé Azure
description: IoT connector dispose d’une bibliothèque open source robuste (GitHub) permettant d’ingérer des messages d’appareil à partir d’appareils mobiles populaires.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/23/2021
ms.author: jasteppe
ms.openlocfilehash: c7b80efbb032315bfca36511cf07e1f5a1f3b8a4
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061464"
---
# <a name="open-source-projects"></a>Projets open source

consultez nos projets open source sur GitHub qui fournissent du code source et des instructions pour déployer des services pour diverses utilisations avec IoT connector. 

## <a name="iot-connector-github-projects"></a>projets de GitHub du connecteur IoT

### <a name="fhir-integration"></a>Intégration de FHIR

* [Microsoft/IoMT-fhir](https://github.com/microsoft/iomt-fhir): version open source du service administré des API Azure Healthcare pour le connecteur IOT. Peut être utilisé avec n’importe quel service FHIR&#174; (Fast Healthcare Interoperability Resources) qui prend en charge [FHIR R4&#174;](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491)

### <a name="device-and-fhir-destination-mappings"></a>Mappages de destination des appareils et FHIR

* [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): outil pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages pour le chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la version open source.

### <a name="wearables-integration"></a>Intégration d’un portable

Fitbit

* [Microsoft/Fitbit-on-fhir](https://github.com/microsoft/FitbitOnFHIR): mettez Fitbit&#174; données à un service fhir.

HealthKit

* [Microsoft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): ajoutez des données Apple&#174; healthkit&#174; à un service fhir.

* [Microsoft/healthkit-to-fhir](https://github.com/microsoft/healthkit-to-fhir): offre un moyen simple de créer des ressources fhir à partir de HKObjects

Google Fit sur FHIR

* [Microsoft/googlefit-on-fhir](https://github.com/microsoft/googlefit-on-fhir): mettez Google Fit données&#174; à un service fhir.

Synchronisation des données d’intégrité

* [Microsoft/Health-Data-Sync](https://github.com/microsoft/health-data-sync): une bibliothèque de&#174; SWIFT qui simplifie et automatise l’exportation de données HealthKit dans un magasin externe.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment déployer IoT Connector dans le Portail Azure

>[!div class="nextstepaction"]
>[Déployer le service géré de connecteur IoT](deploy-iot-connector-in-azure.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
