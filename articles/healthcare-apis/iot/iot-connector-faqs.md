---
title: FAQ sur le connecteur IoT-API Azure Healthcare
description: Ce document fournit des réponses aux questions fréquemment posées sur le connecteur IoT.
services: healthcare-apis
author: msjasteppe
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 153a4c86d0f6f1b2617327360569cd92d1a318ca
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990241"
---
# <a name="frequently-asked-questions-about-iot-connector"></a>Forum aux questions sur le connecteur IoT

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Voici quelques-unes des questions fréquemment posées sur le connecteur IoT.

## <a name="iot-connector-the-basics"></a>Connecteur IoT : notions de base

### <a name="what-are-the-differences-between-the-azure-api-for-fhir-iot-connector-preview-and-the-azure-healthcare-apis-iot-connector"></a>Quelles sont les différences entre l’API Azure pour FHIR IoT Connector (version préliminaire) et le connecteur IoT API Azure Healthcare ?

Le connecteur IoT des API Azure Healthcare est le successeur de l’API Azure pour les ressources d’interopérabilité de la santé rapide (FHIR&#174;) IoT Connector (version préliminaire). 

Plusieurs améliorations ont été introduites, notamment les points de terminaison d’ingestion des messages d’appareil hébergés par le client (par exemple, un hub d’événements Azure), l’utilisation des identités gérées et Azure Role-Based Access Control (Azure RBAC).

### <a name="can-i-use-iot-connector-with-a-different-fhir-service-other-than-the-azure-healthcare-apis-fhir-service"></a>Puis-je utiliser le connecteur IoT avec un autre service FHIR que le service FHIR des API de santé Azure ?

Non. Le connecteur IoT des API de santé Azure prend actuellement en charge uniquement le service FHIR des API de santé Azure pour la persistance des données. La version open source du connecteur IoT prend en charge l’utilisation de différents services FHIR. Pour plus d’informations, consultez la section [projets open source](iot-git-projects.md) .  

### <a name="what-versions-of-fhir-does-the-iot-connector-support"></a>Quelles sont les versions de FHIR prises en charge par le connecteur IoT ?

Actuellement, le connecteur IoT prend uniquement en charge la persistance de [HL7 FHIR&#174; R4](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491). 

### <a name="what-are-the-subscription-quota-limits-for-iot-connector"></a>Quelles sont les limites de quota d’abonnement pour le connecteur IoT ?

* 25 connecteurs IoT par abonnement (non ajustables)
* 10 connecteurs IoT par espace de travail (non réglable)
* Une destination FHIR * par connecteur IoT (non réglable)

(*-FHIR destination est une ressource enfant du connecteur IoT)

### <a name="can-i-use-the-iot-connector-with-device-messages-from-apple174-google174-or-fitbit174-devices"></a>Puis-je utiliser le connecteur IoT avec des messages d’appareil à partir d’appareils Apple&#174;, Google&#174; ou Fitbit&#174; ?

Oui. Le connecteur IoT prend en charge les messages de l’appareil à partir de toutes ces plateformes. Pour plus d’informations, consultez la section [projets open source](iot-git-projects.md) .  

## <a name="more-frequently-asked-questions"></a>Autres questions fréquentes
[FAQ sur les API de santé Azure](../healthcare-apis-faqs.md)

[FAQ sur le service FHIR des API de santé Azure](../fhir/fhir-faq.md)

[FAQ sur le service DICOM des API de santé Azure](../dicom/dicom-services-faqs.yml)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
