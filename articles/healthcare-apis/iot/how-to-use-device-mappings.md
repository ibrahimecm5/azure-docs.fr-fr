---
title: mappages d’appareils dans IoT Connector-api de santé Azure
description: Cet article explique comment configurer et utiliser les modèles de mappage d’appareil avec les API de santé Azure IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 96b110b03f211d62a0ed778e40aa7a6ad8c8c3ce
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733414"
---
# <a name="how-to-use-device-mappings"></a>Comment utiliser les mappages de périphérique

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

> [!TIP]
> Consultez l’outil [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages de chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la [version open source](https://github.com/microsoft/iomt-fhir) du connecteur IOT.

Cet article explique comment configurer le connecteur IoT à l’aide de mappages d’appareils.

Le connecteur IoT requiert deux types de mappages JSON. Le premier type, le **mappage d’appareils**, est chargé de mapper les charges utiles d’appareils envoyées au point de terminaison `devicedata` Azure Event Hub. Il extrait les types, les identificateurs d’appareil, les dates et heures de mesure, ainsi que la ou les valeur(s) de mesure. 

Le deuxième type de **mappage de destination FHIR (Fast Healthcare Interoperability Resources&#174;)** contrôle le mappage de la ressource FHIR. Il permet de configurer la durée de la période d’observation, le type de données FHIR utilisé pour stocker les valeurs et le ou les code(s) de terminologie. 

Les deux types de mappages sont composés dans un document JSON en fonction de leur type. Ces documents JSON sont ensuite ajoutés à votre connecteur IoT via le Portail Azure. Le document de mappage d’appareil est ajouté via la page **mappage d’appareil** et le document de mappage de destination FHIR via la page de **destination** .

> [!NOTE]
> Les mappages sont stockés dans un stockage d’objets BLOB sous-jacent et chargés à partir d’un objet BLOB par exécution de calcul. Une fois mis à jour, ils doivent prendre effet immédiatement. 

## <a name="device-mappings-overview"></a>Vue d’ensemble des mappages d’appareils

Les mappages d’appareils fournissent des fonctionnalités permettant d’extraire le contenu d’un message de périphérique dans un format commun pour une évaluation supplémentaire. Chaque message de périphérique reçu est évalué par rapport à tous les modèles de mappage d’appareil. 

Un seul message d’appareil entrant peut être divisé en plusieurs messages sortants qui sont ensuite mappés à différentes observations dans le service FHIR. 

Le résultat est un objet de données normalisé représentant la valeur ou les valeurs analysées par les modèles. 

Le modèle de données normalisé contient quelques propriétés obligatoires qui doivent être recherchées et extraites :

|Propriété|Description|
|--------|-----------|
|**Type**|Nom/type pour classifier la mesure. Cette valeur est utilisée pour établir une liaison avec le mappage de destination FHIR requis. Plusieurs mappages peuvent être générés dans le même type, ce qui vous permet de mapper différentes représentations sur plusieurs appareils à une sortie commune unique.|
|**OccurenceTimeUtc**|Heure à laquelle la mesure a été effectuée.|
|**DeviceId**|Identificateur de l’appareil. Cette valeur doit correspondre à un identificateur sur la ressource de l’appareil qui existe sur le service FHIR de destination.|
|**Propriétés**|Extrayez au moins une propriété afin que la valeur puisse être enregistrée dans la ressource d’observation créée. Les propriétés sont un regroupement de paires de valeurs clés extraites pendant la normalisation.|

> [!IMPORTANT]
> Le modèle normalisé complet est défini par l’interface [IMeasurement](https://github.com/microsoft/iomt-fhir/blob/master/src/lib/Microsoft.Health.Fhir.Ingest.Schema/IMeasurement.cs) .

Vous trouverez ci-dessous des exemples conceptuels de ce qui se passe pendant la normalisation et le processus de transformation dans IoT Connector :

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="Diagramme de normalisation des données IoT example1" lightbox="media/iot-data-normalization-high-level.png":::

:::image type="content" source="media/concepts-iot-mapping-templates/normalization-example.png" alt-text="Workflow de normalisation des données IoT example2" lightbox="media/concepts-iot-mapping-templates/normalization-example.png":::

La charge utile de contenu est un message Azure Event Hub, qui est composé de trois parties : Corps, Propriétés et SystemProperties. Le `Body` est un tableau d’octets qui représente une chaîne encodée en UTF-8. Pendant l’évaluation du modèle, le tableau d’octets est automatiquement converti en valeur de chaîne. `Properties` est un regroupement de valeurs clés à utiliser par le créateur du message. `SystemProperties` est également un regroupement de valeurs clés réservé par l’infrastructure Azure Event Hub avec des entrées automatiquement remplies par celles-ci.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
## <a name="mapping-with-jsonpath"></a>Mappage avec JSONPath

Les cinq types de mappage de contenu d’appareil pris en charge aujourd’hui s’appuient sur les JSONPath pour correspondre au mappage et aux valeurs extraites requis. Vous trouverez plus d’informations sur JSONPath [ici](https://goessner.net/articles/JsonPath/). Les cinq types de modèles utilisent l' [implémentation JSON .net](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) pour la résolution des expressions JSONPath.

Vous pouvez définir un ou plusieurs modèles dans le modèle de mappage d’appareil. Chaque message de l’appareil Event Hub reçu est évalué par rapport à tous les modèles de mappage d’appareil. 

Un seul message d’appareil entrant peut être divisé en plusieurs messages sortants qui sont ensuite mappés à différentes observations dans le service FHIR. 

Différents types de modèles existent et peuvent être utilisés lors de la génération du fichier de mappage d’appareil.

|Name                                                                     | Description                                                                   |  
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------|
|[JsonPathContentTemplate](./how-to-use-jsonpath-content-mappings.md)     |Modèle qui prend en charge l’écriture d’expressions à l’aide de JsonPath                  
|[CollectionContentTemplate](./how-to-use-collection-content-mappings.md) |Modèle utilisé pour représenter une liste de modèles qui seront utilisés pendant la normalisation.                                                            |                                                           
|[CalculatedContentTemplate](./how-to-use-calculated-functions-mappings.md)|Modèle qui prend en charge l’écriture d’expressions à l’aide de plusieurs langages d’expression. Prend en charge la transformation des données via l’utilisation de fonctions JmesPath.|
|[IotJsonPathContentTemplate](./how-to-use-iot-jsonpath-content-mappings.md)|Modèle qui prend en charge les messages envoyés à partir d’Azure IOT Hub ou la fonctionnalité d’exportation de données héritée d’Azure IOT central.|
|[IotCentralJsonPathContentTemplate](./how-to-use-iot-central-json-content-mappings.md)|Modèle qui prend en charge les messages envoyés via la fonctionnalité d’exportation de données d’Azure IOT central.|  

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser les mappages de périphérique. Pour savoir comment utiliser les mappages de destination FHIR, consultez

>[!div class="nextstepaction"]
>[Utilisation des mappages de destination FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
