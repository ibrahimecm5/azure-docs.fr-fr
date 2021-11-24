---
title: Spécification de l’API de modèle ARM des applications de conteneur
description: Explorez les propriétés disponibles dans le modèle ARM d’applications de conteneur.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: reference
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0f0c4c360b8b9b3cdbe78c833df2a76c2b27d84
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323521"
---
# <a name="container-apps-preview-arm-template-api-specification"></a>Spécification de l’API de modèle ARM des applications de conteneur

Les déploiements des applications Azure Container sont alimentés par un modèle Azure Resource Manager (ARM). Les tableaux suivants décrivent les propriétés disponibles dans le modèle ARM de l’application conteneur.

L'[exemple de modèle ARM pour obtenir des exemples d’utilisation](#example).

## <a name="resources"></a>Ressources

Les entrées du tableau du modèle ARM `resources` ont les propriétés suivantes :

| Propriété | Description | Type de données |
|---|---|--|
| `name` | Nom de l’application conteneur. | string |
| `location` | Région Azure dans laquelle l’instance de conteneur d’applications est déployée. | string |
| `tags` | Collection de balises Azure associées à l’application de conteneur. | tableau |
| `type` | Le point de terminaison ARM `Microsoft.Web/containerApps` détermine toujours l’API vers laquelle effectuer le transfert  | string |

Dans cet exemple, vous placez vos valeurs à la place des jetons d’espace réservé placés `<>` entre crochets.

## <a name="properties"></a>properties

L’objet d’une ressource `properties` a les propriétés suivantes :

| Propriété | Description | Type de données | Lecture seule |
|---|---|---|---|
| `provisioningState` | État d’une opération de longue durée, par exemple lors de la création d’une nouvelle révision de conteneur. Les valeurs possibles sont les suivantes : approvisionnement, approvisionné, échec. Vérifiez si l’application est en cours d’exécution. | string | Oui |
| `environmentId` | L’ID d’environnement de votre application conteneur. **Cette propriété est obligatoire.** | string | No |
| `latestRevisionName` | Nom de la dernière révision. | string | Oui |
| `latestRevisionFqdn` | URL de la dernière révision. | string | Oui |

La valeur `environmentId` prend la forme suivante :

```console
/subscriptions/<SUBSCRIPTION_ID>/resourcegroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/environmentId/<ENVIRONMENT_NAME>
```

Dans cet exemple, vous placez vos valeurs à la place des jetons d’espace réservé placés `<>` entre crochets.

## <a name="propertiesconfiguration"></a>properties.configuration

L’objet d’une ressource `properties.configuration` a les propriétés suivantes :

| Propriété | Description | Type de données |
|---|---|---|
| `activeRevisionsMode` | La définition de la valeur `multiple` vous permet de gérer plusieurs révisions. Paramètre pour `single` désactiver automatiquement les anciennes révisions et conserver uniquement la révision la plus récente active. | string |
| `secrets` | Définit des valeurs secrètes dans votre application conteneur. | object |
| `ingress` | Objet qui définit la configuration d’accessibilité publique d’une application de conteneur. | object |
| `registries` | Objet de configuration qui référence les informations d’identification pour les registres de conteneurs privés. Les entrées définies avec `secretref` référencent l’objet de configuration de secrets. | object |

Les modifications apportées à la section `configuration` sont des [modifications de portée application](revisions.md#application-scope-changes), qui ne déclenchent pas une nouvelle révision.

## <a name="propertiestemplate"></a>properties.template

L’objet d’une ressource `properties.template` a les propriétés suivantes :

| Propriété | Description | Type de données |
|---|---|---|
| `revisionSuffix` | Nom convivial pour une révision. Cette valeur doit être unique car le runtime rejette les conflits avec les valeurs de suffixe de nom de révision existantes. | string |
| `containers` | Objet de configuration qui définit les images de conteneur incluses dans l’application conteneur. | object |
| `scale` | Objet de configuration qui définit les règles de mise à l’échelle pour l’application de conteneur. | object |
| `dapr` | Objet de configuration qui définit les paramètres Dapr pour l’application de conteneur. | object  |

Les modifications apportées à la section `template` sont des [modifications d’étendue de révision](revisions.md#revision-scope-changes), qui déclenchent une nouvelle révision.

## <a name="example"></a>Exemple

Voici un exemple de modèle ARM utilisé pour déployer une application de conteneur.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "containerappName": {
            "defaultValue": "mycontainerapp",
            "type": "String"
        },
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "defaultValue": "myenvironment",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2021-03-01",
            "type": "Microsoft.Web/containerApps",
            "name": "[parameters('containerappName')]",
            "location": "[parameters('location')]",
            "properties": {
                "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
                "configuration": {
                    "secrets": [
                        {
                            "name": "mysecret",
                            "value": "thisismysecret"
                        }
                    ],
                    "ingress": {
                        "external": true,
                        "targetPort": 80,
                        "allowInsecure": false,
                        "traffic": [
                            {
                                "latestRevision": true,
                                "weight": 100
                            }
                        ]
                    }
                },
                "template": {
                    "revisionSuffix": "myrevision",
                    "containers": [
                        {
                            "name": "nginx",
                            "image": "nginx",
                            "env": [
                                {
                                    "name": "HTTP_PORT",
                                    "value": "80"
                                },
                                {
                                    "name": "SECRET_VAL",
                                    "secretRef": "mysecret"
                                }
                            ],
                            "resources": {
                                "cpu": 0.5,
                                "memory": "1Gi"
                            }
                        }
                    ],
                    "scale": {
                        "minReplicas": 1,
                        "maxReplicas": 3
                    }
                }
            }
        }
    ]
}
```
