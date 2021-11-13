---
title: Créer par programmation des tableaux de bord Azure
description: Utilisez un tableau de bord du portail Azure comme modèle pour créer par programmation des tableaux de bord Azure. Comprend une référence JSON.
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 15d94cf5efc857adf9bbc0cf98c9e3d34e5bda7d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239800"
---
# <a name="programmatically-create-azure-dashboards"></a>Créer par programmation des tableaux de bord Azure

Cet article vous guide tout au long des processus de création par programmation et de publication de tableaux de bord Azure. L’exemple de tableau de bord illustré ci-dessous sert de référence dans tout le document.

:::image type="content" source="media/azure-portal-dashboards-create-programmatically/sample-dashboard.png" alt-text="Capture d’écran d’un exemple de tableau de bord dans le portail Azure.":::

## <a name="overview"></a>Vue d’ensemble

Les tableaux de bord partagés dans le [portail Azure](https://portal.azure.com) sont des [ressources](../azure-resource-manager/management/overview.md), tout comme les machines virtuelles et les comptes de stockage. Vous pouvez gérer les ressources par programmation à l'aide des [API REST Azure Resource Manager](/rest/api/), de l'[interface de ligne de commande Azure (Azure CLI)](/cli/azure) et des [commandes Azure PowerShell](/powershell/azure/get-started-azureps).

De nombreuses fonctionnalités s'appuient sur ces API pour faciliter la gestion des ressources. Chacun de ces outils et API offre des moyens de créer, répertorier, récupérer, modifier et supprimer des ressources. Étant donné que les tableaux de bord sont des ressources, vous pouvez choisir l'API ou outil que vous souhaitez utiliser.

Quels que soient les outils que vous utilisez, pour créer un tableau de bord par programme, vous devez créer une représentation JSON de votre objet de tableau de bord. Cet objet contient des informations sur les vignettes du tableau de bord. Ces informations incluent les tailles, les positions, les ressources auxquelles elles sont liées et toutes les personnalisations de l'utilisateur.

La méthode la plus pratique pour générer ce document JSON consiste à utiliser le portail Azure pour créer un tableau de bord initial avec les vignettes de votre choix. Ensuite, exportez le JSON et créez un modèle à partir du résultat que vous pouvez modifier et utiliser dans des scripts, des programmes et des outils de déploiement.

## <a name="create-a-dashboard"></a>Création d’un tableau de bord

Pour créer un tableau de bord, sélectionnez **Tableau de bord** dans le menu [Portail Azure](https://portal.azure.com), puis choisissez **Nouveau tableau de bord**.

Pour obtenir des instructions détaillées, consultez [Créer un tableau de bord dans le portail Azure](azure-portal-dashboards.md).

## <a name="share-the-dashboard"></a>Partager le tableau de bord

Après avoir configuré le tableau de bord, l’étape suivante consiste à publier le tableau de bord à l’aide de la commande **Partager**.

Lorsque vous partagez un tableau de bord, vous devez choisir l’abonnement et le groupe de ressources vers lequel publier. Vous devez disposer d'un accès en écriture à l'abonnement et au groupe de ressources que vous choisissez. Pour plus d’informations, consultez [Attribuer des rôles Azure en utilisant le portail Azure](../role-based-access-control/role-assignments-portal.md).

Pour obtenir des instructions détaillées, consultez [Partager des tableaux de bord Azure en utilisant le contrôle d’accès en fonction du rôle Azure](azure-portal-dashboard-share-access.md).

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Extraire la représentation JSON du tableau de bord

Le partage du tableau de bord ne prend que quelques secondes. L’étape suivante consiste à exporter le document JSON à l’aide de la commande **Télécharger**.

:::image type="content" source="media/azure-portal-dashboards-create-programmatically/download-command.png" alt-text="Capture d’écran de la commande permettant d’exporter la représentation JSON d’un modèle dans le portail Azure.":::

## <a name="create-a-template-from-the-json"></a>Créer un modèle à partir du document JSON

L'étape suivante consiste à créer un modèle à partir de ce document JSON. Vous pourrez utiliser le modèle par programmation avec les API de gestion des ressources ou outils en ligne de commande appropriés, ou dans le portail.

Il n'est pas nécessaire de comprendre entièrement la structure JSON du tableau de bord pour créer un modèle. Vous pouvez conserver la structure et la configuration de chaque vignette. Paramétrez ensuite l'ensemble de ressources Azure vers lequel les vignettes pointent.

Examinez le tableau de bord JSON que vous avez exporté et recherchez toutes les occurrences des ID de ressource Azure. Notre exemple de tableau de bord comporte plusieurs parties de contrôle qui pointent toutes vers une seule machine virtuelle Azure. En effet, notre tableau de bord ne s'intéresse qu'à cette ressource. Si vous recherchez « /subscriptions » dans l’exemple JSON inclus à la fin du document, vous trouvez plusieurs occurrences de cet ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Pour publier ce tableau de bord pour toutes les prochaines machines virtuelles, paramétrez chaque occurrence de cette chaîne dans le document JSON.

Il existe deux approches pour les API qui créent des ressources dans Azure :

- Les API impératives créent une ressource à la fois. Pour plus d’informations, consultez [Ressources](/rest/api/resources/resources).
- Un système de déploiement basé sur un modèle crée plusieurs ressources dépendantes avec un seul appel d'API. Pour plus d'informations, consultez [Déployer des ressources à l'aide de modèles Resource Manager et d'Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

Le déploiement basé sur un modèle prend en charge le paramétrage et la création de modèles. Il s'agit de l'approche utilisée dans cet article.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Créer par programmation un tableau de bord à partir de votre modèle à l’aide d’un déploiement de modèle

Azure offre la possibilité d’orchestrer le déploiement de plusieurs ressources. Vous créez un modèle de déploiement qui exprime l’ensemble des ressources à déployer et les relations qui existent entre elles.  Le format JSON de chaque ressource est le même que si vous les créiez une par une. La différence est que le langage de gabarit ajoute quelques concepts comme les variables, les paramètres, les fonctions de base, etc. Cette syntaxe étendue est uniquement prise en charge dans le cadre du déploiement d'un modèle. Elle ne fonctionne pas si elle est utilisée avec les API impératives mentionnées précédemment. Pour plus d'informations, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](../azure-resource-manager/templates/syntax.md).

Le paramétrage doit être effectué en utilisant la syntaxe des paramètres du modèle.  Vous remplacez toutes les instances de l'ID de ressource que nous avons trouvé précédemment, comme indiqué ici.

Exemple de propriété JSON avec ID de ressource codé en dur :

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Exemple de propriété JSON convertie en version paramétrable selon des paramètres de modèle

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Déclarez les métadonnées de modèle requises et les paramètres situés en haut du modèle JSON comme ceci :

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

Après avoir configuré votre modèle, déployez-le en utilisant l'une des méthodes suivantes :

- [API REST](/rest/api/resources/deployments)
- [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure CLI](/cli/azure/group/deployment#az_group_deployment_create)
- [Page de déploiement du modèle sur le portail Azure](https://portal.azure.com/#create/Microsoft.Template)

Vous verrez ensuite deux versions de notre exemple de document JSON de tableau de bord. La première est la version que nous avons exportée à partir du portail et qui était déjà liée à une ressource. La seconde est la version de modèle que vous pouvez lier par programmation à toute machine virtuelle et déployer à l'aide d'Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard"></a>Représentation JSON de notre exemple de tableau de bord

Cet exemple est similaire à ce que vous verrez si vous avez suivi cet article. Les instructions ont exporté la représentation JSON d'un tableau de bord déjà déployé. Les identificateurs de ressources codés en dur indiquent que ce tableau de bord pointe vers une machine virtuelle Azure spécifique.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Représentation sous forme de modèle de notre exemple de tableau de bord

La version du modèle du tableau de bord a défini trois paramètres appelés `virtualMachineName`, `virtualMachineResourceGroup`et `dashboardName`.  Les paramètres vous permettent de faire pointer ce tableau de bord vers une autre machine virtuelle Azure à chaque déploiement. Ce tableau de bord peut être configuré par programmation et déployé pour pointer vers n'importe quelle machine virtuelle Azure. Pour tester cette fonctionnalité, copiez le modèle suivant et collez-le sur la [page de déploiement de modèle du portail Azure](https://portal.azure.com/#create/Microsoft.Template).

Cet exemple déploie un tableau de bord par lui-même, mais le langage de modèle vous permet de déployer plusieurs ressources et de regrouper un ou plusieurs tableaux de bord à leurs côtés.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

Maintenant que vous avez vu un exemple d’utilisation d’un modèle paramétrable pour déployer un tableau de bord, vous pouvez essayer de déployer le modèle en utilisant les [API REST Azure Resource Manager](/rest/api/), [Azure CLI](/cli/azure) ou les [commandes Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Créer par programmation un tableau de bord à l’aide d’Azure CLI

Préparez votre environnement pour l’interface Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ces exemples utilisent le tableau de bord suivant : [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Veillez à remplacer tout le contenu entre crochets par vos valeurs.

Exécutez la commande [az portal dashboard create](/cli/azure/portal/dashboard#az_portal_dashboard_create) pour créer un tableau de bord basé sur votre modèle :

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Vous pouvez mettre à jour un tableau de bord à l’aide de la commande [az portal dashboard update](/cli/azure/portal/dashboard#az_portal_dashboard_update) :

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Pour afficher les détails d’un tableau de bord, exécutez la commande [az portal dashboard show](/cli/azure/portal/dashboard#az_portal_dashboard_show) :

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Pour voir tous les tableaux de bord de l’abonnement actif, utilisez [az portal dashboard list](/cli/azure/portal/dashboard#az_portal_dashboard_list) :

```azurecli
az portal dashboard list
```

Vous pouvez aussi voir tous les tableaux de bord d’un groupe de ressources :

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser des vignettes Markdown dans les tableaux de bord Azure pour afficher du contenu personnalisé](azure-portal-markdown-tile.md).
- Explorez toutes les commandes Azure CLI pour les tableaux de bord en consultant [Tableau de bord du portail az](/cli/azure/portal/dashboard).
- Découvrez comment [gérer les paramètres et les préférences du portail Azure](set-preferences.md).
