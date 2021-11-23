---
title: Exemples de modèles Azure Resource Manager pour les expériences de chaos
description: Exemples de modèles Azure Resource Manager pour créer des expériences Azure Chaos Studio
services: chaos-studio
author: johnkemnetz
ms.topic: sample
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: dc0777380cf34465fc89a9e2e61d63aec0ca9e8c
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404366"
---
# <a name="resource-manager-template-samples-for-experiments-in-azure-chaos-studio"></a>Exemples de modèles Resource Manager pour les expériences dans Azure Chaos Studio
Cet article contient des exemples de [modèle Azure Resource Manager](../azure-resource-manager/templates/syntax.md) pour créer une [expérience de chaos](chaos-studio-chaos-experiments.md) dans Azure Chaos Studio. Chaque exemple comprend un fichier de modèle et un fichier de paramètres avec des exemples de valeurs à fournir au modèle.

## <a name="create-experiment-sample"></a>Créer une expérience (exemple)

Dans cet exemple, nous créons une expérience de chaos avec une seule ressource cible et une seule erreur de sollicitation du processeur. Vous pouvez modifier l’expérience en référençant notre [API REST](/rest/api/chaosstudio/experiments/create-or-update) et la [bibliothèque d’erreurs](chaos-studio-fault-library.md).

### <a name="template-file"></a>Fichier de modèle

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "experimentName": {
      "type": "string",
      "defaultValue": "simple-experiment",
      "metadata": {
        "description": "A name for the experiment."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "A region where the experiment will be deployed."
      }
    },
    "chaosTargetResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the chaos target. This is the resource ID of the resource being targeted plus the target proxy resource."
      }
    }
  },
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Chaos/experiments",
      "apiVersion": "2021-09-15-preview",
      "name": "parameters('experimentName')",
      "location": "parameters('location')",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "identity": {
          "properties": {
            "type": "SystemAssigned"
          }
        },
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "parameters('chaosTargetResourceId')"
              }
            ]
          }
        ],
        "startOnCreation": "false",
        "steps": [
          {
            "name": "Step1",
            "branches": [
              {
                "name": "Branch1",
                "actions": [
                  {
                    "duration": "PT10M",
                    "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
                    "parameters": [
                      {
                        "key": "pressureLevel",
                        "value": "95"
                      }
                    ],
                    "selectorId": "Selector1",
                    "type": "continuous"
                  }
                ]
              }
            ]
          }
        ]
      }
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>Fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "experimentName": {
        "value": "my-first-experiment"
      },
      "location": {
        "value": "eastus"
      },
      "chaosTargetResourceId": {
        "value": "eastus"
      }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez-en plus sur Chaos Studio](chaos-studio-overview.md).
* [Découvrez-en plus sur les expériences de chaos](chaos-studio-chaos-experiments.md).
