---
title: Qu’est-ce que le connecteur IoT ? -API de santé Azure
description: Dans cet article, vous allez découvrir les étapes que le connecteur IoT effectue avant de stocker les données IoMT dans le service FHIR.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: b74b3c1aefe2c7dd05421385e672385196aab15e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719426"
---
# <a name="what-is-iot-connector"></a>Qu’est-ce que le connecteur IoT ?

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

IoT Connector est une fonctionnalité facultative des API Azure Healthcare qui permet d’ingérer des données à partir d’appareils IoMT (Internet of Medical Things).

Cet article fournit une vue d’ensemble du Data Flow dans le connecteur IoT. Vous en apprendrez davantage sur les différentes étapes de traitement des données dans le service IoT Connector qui transforment les données des appareils en ressources d' [observation](https://www.hl7.org/fhir/observation.html) FHIR&#174;) rapides.

Voici les différentes étapes de l’extraction des données, une fois qu’elles sont reçues par le connecteur IoT.

## <a name="ingest"></a>Ingérer

La réception est la première étape où les données de l’appareil sont reçues dans le service IoT Connector. Le point de terminaison d’ingestion pour les données d’appareils est hébergé sur un Azure Event Hub. La plateforme [Azure Event Hub](../../event-hubs/index.yml) prend en charge une grande échelle et un débit élevé, avec la possibilité de recevoir et de traiter des millions de messages par seconde. Il permet également au service IoT Connector de consommer des messages de manière asynchrone, ce qui évite aux appareils d’attendre que les données de l’appareil soient traitées.

> [!NOTE]
> JSON est le seul format actuellement pris en charge pour les données d’appareils.

## <a name="normalize"></a>Normaliser

Normalize est l’étape suivante dans laquelle les données de l’appareil sont récupérées à partir du hub d’événements Azure ci-dessus et traitées à l’aide de modèles de mappage d’appareil. Ce processus de mappage entraîne une transformation des données d’appareils en un schéma normalisé. Le processus de normalisation simplifie non seulement le traitement des données à des étapes ultérieures, mais également pour la projection d’un message d’entrée en plusieurs messages normalisés. Par exemple, un appareil pourrait envoyer plusieurs signes vitaux pour la température du corps, la fréquence cardiaque, la pression artérielle et la fréquence respiratoire dans un message unique. Ce message d’entrée crée quatre ressources FHIR distinctes. Chaque ressource représente un signe vital différent, avec le message d’entrée projeté dans quatre messages normalisés différents.

## <a name="group"></a>Grouper

Le groupe est l’étape suivante dans laquelle les messages normalisés disponibles à partir de l’étape précédente sont regroupés à l’aide de trois paramètres différents : 

* Identité d’appareil
* Type de mesure 
* Période

Lee groupements par identité de l’appareil et types de mesure permettent d’utiliser le type de mesure [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Ce type fournit un moyen concis de représenter une série de mesures basée sur la durée à partir d’un appareil dans FHIR, tandis que la période de temps contrôle la latence à laquelle les ressources d’observation générées par le connecteur IoT sont écrites dans le service FHIR.

> [!NOTE]
> La valeur de la période est définie par défaut sur 15 minutes et ne peut pas être configurée pour la préversion.

## <a name="transform"></a>Transformer

Dans l’étape de transformation, les messages normalisés groupés sont traités par le biais de modèles de mappage de destination FHIR. Les messages correspondant à un type de modèle sont transformés en ressources d’observation basées sur FHIR, comme spécifié via le mappage.

À ce stade, la ressource de l’appareil, ainsi que la ressource du patient qui lui est associée, sont également récupérées à partir du service FHIR à l’aide de l’identificateur d’appareil présent dans le message. Ces ressources sont ajoutées en tant que référence à la ressource Observation en cours de création.

> [!NOTE]
>Toutes les recherches d’identité sont mises en cache une fois résolues pour réduire la charge sur le service FHIR. Si vous prévoyez de réutiliser des appareils avec plusieurs patients, il est recommandé de créer une ressource d’appareil virtuel qui est spécifique au patient et d’envoyer l’identificateur d’appareil virtuel dans la charge utile de message. L’appareil virtuel peut être lié à la ressource appareil réelle en tant que parent.

S’il n’existe aucune ressource d’appareil pour un identificateur d’appareil donné dans le service FHIR, le résultat dépend de la valeur du type de résolution défini au moment de la création. Lorsque la valeur est définie sur Rechercher, le message spécifique est ignoré et le pipeline continue à traiter d’autres messages entrants. S’il est défini sur créer, le service IoT Connector crée un appareil à peine et des ressources pour les patients sur le service FHIR.

## <a name="persist"></a>Conserver

Une fois la ressource FHIR d’observation générée à l’étape de transformation, la ressource est enregistrée dans le service FHIR. Si la ressource FHIR est nouvelle, elle est créée sur le service. Si la ressource FHIR existe déjà, elle sera mise à jour.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les mappages de connecteur IoT, consultez les guides suivants :

>[!div class="nextstepaction"]
>[Comment utiliser les mappages de périphérique](how-to-use-device-mappings.md)

>[!div class="nextstepaction"]
>[Utilisation des mappages de destination FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
