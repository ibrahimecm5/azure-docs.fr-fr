---
title: Structure des tableaux de bord Azure
description: Parcourez la structure JSON d’un tableau de bord Azure à l’aide d’un exemple de tableau de bord. Comprend une référence aux propriétés de ressource.
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 4f005d6b232a7bba15d55055d49ac1c7adf49866
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239677"
---
# <a name="the-structure-of-azure-dashboards"></a>Structure des tableaux de bord Azure

Ce document décrit la structure d’un tableau de bord Azure, en utilisant le tableau de bord suivant comme exemple :

:::image type="content" source="media/azure-portal-dashboards-structure/sample-dashboard.png" alt-text="Capture d’écran d’un exemple de tableau de bord dans le portail Azure.":::

Étant donné que les [tableaux de bord Azure sont des ressources](../azure-resource-manager/management/overview.md), ce tableau de bord peut être représenté au format JSON.  Le document JSON suivant représente le tableau de bord visualisé ci-dessus.

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
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
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

## <a name="common-resource-properties"></a>Propriétés des ressources communes

Analysons les sections appropriées du document JSON.  Les propriétés de niveau supérieur, __id__, __name__, __type__, __location__ et __tags__ sont des propriétés partagées par tous les types de ressources Azure. Autrement dit, elles n’ont pas grand-chose à voir avec le contenu du tableau de bord.

### <a name="id"></a>id

ID de ressource Azure, soumis aux [conventions de nommage des ressources Azure](/azure/architecture/best-practices/resource-naming). Quand le portail crée un tableau de bord, il choisit généralement un ID sous la forme d’un GUID, mais vous pouvez utiliser tout nom valide quand vous en créez un par programmation.

### <a name="name"></a>Nom

Le nom est le segment de l’ID de ressource qui n’inclut pas l’abonnement, le type de ressource ou les informations du groupe de ressources. En gros, il s’agit du dernier segment de l’ID de ressource.

### <a name="type"></a>Type

Tous les tableaux de bord sont de type __Microsoft.Portal/dashboards__.

### <a name="location"></a>Location

Contrairement à d’autres ressources, les tableaux de bord n’ont pas de composant d’exécution.  Pour les tableaux de bord, la propriété location indique l’emplacement géographique principal qui stocke la représentation JSON du tableau de bord. La valeur doit correspondre à l’un des codes d’emplacement que vous pouvez extraire à l’aide de l’[API des emplacements sur la ressource des abonnements](/rest/api/resources/subscriptions).

### <a name="tags"></a>Étiquettes

Les étiquettes (tags) sont une fonctionnalité commune des ressources Azure qui vous permettent d’organiser vos ressources dans des paires nom/valeur arbitraires. Les tableaux de bord comprennent une étiquette spéciale appelée __hidden-title__. Si cette propriété est renseignée pour votre tableau de bord, cette valeur lui sert aussi de nom d’affichage dans le portail. Vous ne pouvez pas renommer des ID de ressources Azure, mais vous pouvez renommer des étiquettes. Cette étiquette permet de disposer d’un nom d’affichage modifiable pour votre tableau de bord.

`"tags": { "hidden-title": "Created via API" }`

### <a name="properties"></a>Propriétés

L’objet properties contient deux propriétés, __lenses__ et __metadata__. La propriété __lenses__ contient des informations sur les vignettes dans le tableau de bord.  La propriété __metadata__ est là pour d’éventuelles futures fonctionnalités.

### <a name="lenses"></a>Filtres (lenses)

La propriété __lenses__ contient le tableau de bord. L’objet filtre dans cet exemple contient une propriété unique nommée « 0 ». Les filtres sont un concept de regroupement qui n’est actuellement pas implémenté. Pour l’instant, tous vos tableaux de bord ont cette propriété unique sur l’objet filtre, là encore nommée « 0 ».

### <a name="parts"></a>Éléments

L’objet situé sous “0” contient deux propriétés, __order__ et __parts__.  Dans la version actuelle des tableaux de bord, __order__ correspond toujours à 0. La propriété __parts__ contient un objet qui définit les parties individuelles (également appelées vignettes) sur le tableau de bord.

L’objet __parts__ une propriété pour chaque partie, où le nom de la propriété est un nombre. Le nombre n’est pas significatif.

Chaque objet parts individuel comporte u objet __position__ et un objet __metadata__.

### <a name="position"></a>Position

La propriété __position__ contient les informations de taille et d’emplacement de la partie, exprimées sous la forme __x__, __y__, __rowSpan__ et __colSpan__. Les valeurs sont indiquées en termes d’unités de grille. Ces unités de grille sont visibles quand le tableau de bord est en mode de personnalisation, comme illustré ici. Si vous voulez qu’une vignette ait une largeur de deux unités de grille, une hauteur d’une unité de grille et un emplacement dans le coin supérieur gauche du tableau de bord, alors l’objet position ressemble à ceci :

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

:::image type="content" source="media/azure-portal-dashboards-structure/grid-units.png" alt-text="Capture d’écran montrant les unités de grille pour un tableau de bord dans le portail Azure.":::

### <a name="metadata"></a>Métadonnées

Chaque partie a une propriété metadata ; un objet n’a qu’une seule propriété obligatoire appelée __type__. Cette chaîne indique au portail quelle partie afficher. Notre exemple de tableau de bord utilise ces types de parties de contrôle :

1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Utilisé pour afficher des mesures de surveillance.
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Utilisé pour afficher du texte ou des images avec une mise en forme basique pour les listes, les liens, etc.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – Utilisé pour afficher des vidéos de YouTube, Channel9 et tout autre type de vidéo qui fonctionne dans une balise vidéo HTML.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Utilisé pour afficher le nom et l’état d’une machine virtuelle Azure.

Chaque type de partie possède sa propre configuration. Les propriétés de configuration possibles sont appelées __inputs__, __settings__ et __asset__. 

### <a name="inputs"></a>Entrées

L’objet inputs contient généralement des informations qui lient une partie de contrôle à une instance de ressource.  La partie de la machine virtuelle utilisée dans notre exemple de tableau de bord contient une seule entrée qui utilise l’ID de ressource Azure pour exprimer la liaison.  Ce format d’ID de ressource est cohérent pour toutes les ressources Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```

La partie graphique des métriques a une seule entrée qui exprime la ressource avec laquelle effectuer la liaison, ainsi que des informations sur les métriques affichées. Voici l’entrée de la partie de contrôle qui montre les métriques Network In et Network Out.

```json
"inputs":
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
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
]

```

### <a name="settings"></a>Paramètres

L’objet settings contient les éléments configurables d’une partie.  Dans notre exemple de tableau de bord, la partie Markdown utilise des paramètres pour stocker le contenu Markdown personnalisé, ainsi qu’un titre et un sous-titre configurables.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

De même, la partie de contrôle vidéo possède ses propres paramètres qui contiennent un pointeur vers la vidéo à lire, un paramètre de lecture automatique et des informations de titre facultatives.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="asset"></a>Asset

Les parties de contrôle qui sont liées à des objets de portail gérables de première classe (appelés actifs) ont cette relation exprimée par le biais de l’objet asset.  Dans notre exemple de tableau de bord, la partie de contrôle de la machine virtuelle contient la description de cet actif.  La propriété __idInputName__ indique au portail que l’entrée d’ID (idInput) contient l’identificateur unique de l’actif, dans cet exemple, l’ID de ressource. La plupart des types de ressources Azure ont des actifs définis dans le portail.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment créer un tableau de bord [dans le portail Azure](azure-portal-dashboards.md) ou [par programmation](azure-portal-dashboards-create-programmatically.md).
- Découvrez comment [utiliser des vignettes Markdown dans les tableaux de bord Azure pour afficher du contenu personnalisé](azure-portal-markdown-tile.md).
