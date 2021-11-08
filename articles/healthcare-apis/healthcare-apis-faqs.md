---
title: FAQ sur les API de santé Azure
description: Ce document fournit des réponses aux questions fréquemment posées sur les API de santé Azure.
services: healthcare-apis
author: ginalee-dotcom
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/05/2021
ms.author: ginle
ms.openlocfilehash: 6ce51e4cf916baaa22d14e902872185901b4d5da
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988612"
---
# <a name="frequently-asked-questions-about-azure-healthcare-apis-preview"></a>Forum aux questions sur les API Azure Healthcare (version préliminaire)

Voici quelques-unes des questions fréquemment posées sur les API de santé Azure.

## <a name="azure-healthcare-apis-the-basics"></a>API de santé Azure : notions de base

### <a name="what-is-the-azure-healthcare-apis"></a>Qu’est-ce que les API de santé Azure ?
Les API de santé Azure sont une plate-forme de données d’intégrité entièrement gérée qui permet l’échange rapide et la persistance des données d’intégrité protégées (PHI, protected Health Information) et des données d’intégrité par le biais de normes de l’industrie ouverte interopérables comme les FHIR® (Fast Healthcare Interoperability Resources) et les communications numériques en médecine (DICOM®).

### <a name="what-do-the-azure-healthcare-apis-enable-you-to-do"></a>Qu’est-ce que les API de santé Azure vous permettent d’effectuer ?
Les API de santé Azure vous permettent d’effectuer les opérations suivantes : 

* Connectez rapidement des sources de données d’intégrité disparates et des formats tels que des données structurées, de création d’images et de périphériques, et normalisez-les pour les rendre persistantes dans le Cloud.

* Transformez et transformez les données en FHIR. Par exemple, vous pouvez transformer les données d’intégrité à partir de formats hérités, tels que HL7v2 ou CDA, ou à partir de données IoT à haute fréquence dans des formats propriétaires d’appareils vers FHIR.

* Connecter vos données stockées dans les api de santé avec des services dans l’écosystème Azure, tels que la Synapse et les produits Microsoft, comme Teams, afin d’obtenir de nouvelles informations grâce à l’analytique et à la Machine Learning, ainsi qu’à la connexion à des applications FHIR intelligentes.

* Gérez les charges de travail avancées avec des fonctionnalités d’entreprise qui offrent fiabilité, évolutivité et sécurité pour garantir la protection de vos données, ainsi que des certifications de confidentialité et de conformité requises pour le secteur de la santé.

### <a name="can-i-migrate-my-existing-production-workload-from-azure-api-for-fhir-to-healthcare-apis"></a>Puis-je migrer la charge de travail de production existante de l’API Azure pour FHIR vers les API de santé ?
Non, malheureusement, nous n’offrons pas de fonctionnalités de migration pour l’instant. 

### <a name="what-is-the-pricing-of-azure-healthcare-apis"></a>Quelle est la tarification des API de santé Azure ?
Pendant la phase de la version préliminaire publique, les API Azure Healthcare peuvent être utilisées gratuitement

### <a name="what-regions-are-healthcare-apis-available"></a>Quelles sont les régions disponibles pour les API de santé ?
Pour obtenir les informations les plus récentes, reportez-vous à la page [produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir) . 
          
### <a name="what-are-the-subscription-quota-limits-for-the-azure-healthcare-apis"></a>Quelles sont les limites de quota d’abonnement pour les API de santé Azure ?

#### <a name="workspace-logical-container"></a>Espace de travail (conteneur logique) :
* 200 instances par abonnement (non ajustables)

#### <a name="dicom-service"></a>Service DICOM :
* 800 instances par abonnement (non ajustables)
* 10 instances DICOM par espace de travail (non réglable)

#### <a name="fhir-service"></a>Service FHIR :
* 25 instances par abonnement (non réglable)
* 10 instances FHIR par espace de travail (non réglable)

#### <a name="iot-connector"></a>Connecteur IoT :
* 25 connecteurs IoT par abonnement (non ajustables)
* 10 connecteurs IoT par espace de travail (non réglable)
* Une destination FHIR * par connecteur IoT (non réglable)

(*-FHIR destination est une ressource enfant du connecteur IoT)

## <a name="more-frequently-asked-questions"></a>Autres questions fréquentes
[FAQ sur le service FHIR des API de santé Azure](./fhir/fhir-faq.md)

[FAQ sur le service DICOM des API de santé Azure](./dicom/dicom-services-faqs.yml)

[FAQ sur le connecteur IoT des API de santé Azure](./iot/iot-connector-faqs.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
