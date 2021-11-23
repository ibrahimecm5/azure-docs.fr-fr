---
title: Mappages de destination FHIR dans le connecteur IoT-API de santé Azure
description: Cet article explique comment configurer et utiliser les mappages de destination FHIR dans le connecteur IoT des API de santé Azure.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 9f6374ad6ba021e6f67f1ea9780aa90d49a7f5a2
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132940293"
---
# <a name="how-to-use-the-fhir-destination-mappings"></a>Comment utiliser les mappages de destination FHIR

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Cet article explique comment configurer le connecteur IoT à l’aide des mappages de destination Fast Healthcare Interoperability Resources (FHIR&#174;).

> [!TIP]
> Consultez l’outil [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages de chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la [version open source](https://github.com/microsoft/iomt-fhir) du connecteur IOT.

Voici un exemple conceptuel de ce qui se produit pendant le processus de normalisation et de transformation dans le connecteur IoT :

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="Diagramme de normalisation des données IoT example1" lightbox="media/iot-data-normalization-high-level.png":::

## <a name="fhir-destination-mappings"></a>Mappages de destination FHIR

Une fois que le contenu de l’appareil est extrait dans un modèle normalisé, les données sont collectées et regroupées en fonction de l’identificateur de l’appareil, du type de mesure et de la période de temps. La sortie de ce regroupement est envoyée pour la conversion en ressource FHIR (actuellement [Observation](https://www.hl7.org/fhir/observation.html)). Le modèle de mappage de destination FHIR contrôle la manière dont les données sont mappées à une observation FHIR. Une observation doit-elle être créée pour un point dans le temps ou sur une période d’une heure ? Quels codes doivent être ajoutés à l’observation ? La valeur doit-elle être représentée en tant que [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) ou en tant que [quantité](https://www.hl7.org/fhir/datatypes.html#Quantity)? Ces types de données sont toutes des options que la destination FHIR mappe à des contrôles de configuration.

> [!NOTE]
> Les mappages sont stockés dans un stockage d’objets BLOB sous-jacent et chargés à partir d’un objet BLOB par exécution de calcul. Une fois mis à jour, ils doivent prendre effet immédiatement. 

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate

Le CodeValueFhirTemplate est actuellement le seul modèle pris en charge dans le mappage de destination FHIR pour l’instant.  Il vous permet de définir des codes, la période d’effet et la valeur de l’observation. Plusieurs types valeur sont pris en charge : [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept) et [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). Outre ces valeurs configurables, l’identificateur de la ressource d’observation et la liaison à l’appareil approprié et aux ressources des patients sont gérés automatiquement.

| Propriété | Description 
| --- | ---
|**TypeName**| Type de mesure auquel ce modèle doit être lié. Il doit y avoir au moins un modèle de mappage d’appareil qui génère ce type.
|**PeriodInterval**|Période pendant laquelle l’observation créée doit être représentée. Les valeurs prises en charge sont 0 (une instance), 60 (une heure), 1440 (un jour).
|**Catégorie**|N’importe quel nombre de [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) pour classifier le type d’observation créé.
|**Codes**|Un ou plusieurs [Codages](http://hl7.org/fhir/datatypes-definitions.html#coding) à appliquer à l’observation créée.
|**Codes[].Code**|Code du [codage](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes[].System**|Système pour le [Codage](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes[].Display**|Affichage pour le [Codage](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Valeur**|Valeur à extraire et à représenter dans l’observation. Pour plus d’informations, consultez [Modèles de type valeur](#value-type-templates).
|**Composants**|*Facultatif :* Un ou plusieurs composants à créer sur l’observation.
|**Components[].Codes**|Un ou plusieurs [Codages](http://hl7.org/fhir/datatypes-definitions.html#coding) à appliquer au composant.
|**Components[].Value**|Valeur à extraire et à représenter dans le composant. Pour plus d’informations, consultez [Modèles de type valeur](#value-type-templates).

### <a name="value-type-templates"></a>Modèles de type valeur 

Voici les modèles de type valeur actuellement pris en charge :

#### <a name="sampleddata"></a>SampledData

Représente le type de données [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR. Les mesures d’observation sont écrites dans un flux de valeur démarrant à un point précis dans le temps et incrémentant à l’aide de la période définie. Si aucune valeur n’est présente, un `E` est écrit dans le flux de données. Si la période est telle que deux valeurs supplémentaires occupent la même position dans le flux de données, la valeur la plus récente est utilisée. La même logique est appliquée lorsqu’une observation utilisant SampledData est mise à jour.

| Propriété | Description 
| --- | ---
|**DefaultPeriod**|Période par défaut en millisecondes à utiliser. 
|**Unité**|Unité à définir sur l’origine de SampledData. 

#### <a name="quantity"></a>Quantité

Représente le type de données [Quantity](http://hl7.org/fhir/datatypes.html#Quantity) FHIR. Si plusieurs valeurs sont présentes dans le regroupement, seule la première valeur est utilisée. Quand une nouvelle valeur arrive et qu’elle est mappée à la même observation, elle remplace l’ancienne valeur.

| Propriété | Description 
| --- | --- 
|**Unité**| Représentation de l’unité.
|**Code**| Forme codée de l’unité.
|**Système**| Système qui définit la forme d’unité codée.

### <a name="codeableconcept"></a>CodeableConcept

Représente le type de données [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR. La valeur réelle n’est pas utilisée.

| Propriété | Description 
| --- | --- 
|**Text**|Représentation du texte brut. 
|**Codes**|Un ou plusieurs [Codages](http://hl7.org/fhir/datatypes-definitions.html#coding) à appliquer à l’observation créée.
|**Codes[].Code**|Code du [codage](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes[].System**|Système pour le [Codage](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codes[].Display**|Affichage pour le [Codage](http://hl7.org/fhir/datatypes-definitions.html#coding).

### <a name="examples"></a>Exemples

**Fréquence cardiaque : SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```

**Étapes : SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```

**Pression artérielle : SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```

**Pression artérielle : Quantity**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```

**Appareil supprimé : CodeableConcept**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```

> [!TIP]
> Pour plus d’informations sur les erreurs et les problèmes courants, consultez le [Guide de dépannage](./iot-troubleshoot-guide.md) du connecteur IOT.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser les mappages de destination FHIR. Pour savoir comment utiliser les mappages de périphériques, consultez.

>[!div class="nextstepaction"]
>[Comment utiliser les mappages de périphérique](how-to-use-device-mappings.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
