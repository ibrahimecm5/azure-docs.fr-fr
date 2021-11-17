---
title: Utiliser des paramètres personnalisés avec un modèle Resource Manager
description: Découvrez comment utiliser des paramètres personnalisés dans un modèle de Gestionnaire des ressources avec intégration et remise continues dans Azure Data Factory.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6584120a0a66fd1d913fdee86a24ce3d91b2555f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519664"
---
# <a name="use-custom-parameters-with-the-resource-manager-template"></a>Utiliser des paramètres personnalisés avec le modèle Resource Manager

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si votre instance de développement dispose d’un dépôt Git associé, vous pouvez remplacer les paramètres du modèle Resource Manager par défaut du modèle Resource Manager généré en publiant ou exportant le modèle. Vous souhaiterez peut-être remplacer la configuration de paramètre Resource Manager par défaut dans les scénarios suivants :

* Vous utilisez CI/CD automatisé et souhaitez modifier certaines propriétés pendant le déploiement de Resource Manager, mais les propriétés ne sont pas paramétrables par défaut.
* Votre fabrique est si volumineuse que le modèle Resource Manager par défaut n’est pas valide car il dépasse le nombre maximum autorisé de paramètres (256).

    Pour gérer la limite de 256 paramètres personnalisés, il existe trois possibilités :    
  
    * Utilisez le fichier de paramètres personnalisés et supprimez les propriétés qui n’ont pas besoin de paramétrage, c.-à-d. celles qui peuvent conserver une valeur par défaut. Vous réduirez ainsi le nombre de paramètres.
    * Refactorisez la logique dans le flux de données pour réduire les paramètres. Par exemple, si les paramètres de pipeline ont tous la même valeur, vous pouvez utiliser de simples paramètres globaux à la place.
    * Fractionnez une fabrique de données en plusieurs flux de données.

Pour remplacer la configuration de paramètre Resource Manager par défaut, accédez au hub **Gérer** et sélectionnez **Modèle ARM** dans la section « Contrôle de code source ». Sous la section **Configuration de paramètre ARM**, cliquez sur l’icône **Modifier** dans « Modifier la configuration de paramètre » pour ouvrir l’éditeur de code de configuration de paramètre Resource Manager.

:::image type="content" source="media/author-management-hub/management-hub-custom-parameters.png" alt-text="Gérer les paramètres personnalisés":::

> [!NOTE]
> La **configuration de paramètre ARM** est uniquement activée en « mode GIT ». Actuellement, elle est désactivée en mode « en mode direct » ou en mode « Data Factory ».

La création d’une configuration de paramètre Resource Manager personnalisée crée un fichier nommé **arm-template-parameters-definition.json** dans le dossier racine de votre branche git. Vous devez utiliser ce nom de fichier exact.

:::image type="content" source="media/continuous-integration-delivery/custom-parameters.png" alt-text="Fichier des paramètres personnalisé":::

Lors de la publication à partir de la branche de collaboration, Data Factory lit ce fichier et utilise sa configuration pour générer les propriétés qui sont paramétrées. Si aucun fichier n’est trouvé, le modèle par défaut est utilisé.

Lors de l’exportation d’un modèle Resource Manager, Data Factory lit ce fichier à partir de la branche sur laquelle vous travaillez actuellement, et pas de la branche de collaboration. Vous pouvez créer ou modifier le fichier à partir d’une branche privée, dans laquelle vous pouvez tester vos modifications en sélectionnant **Exporter le modèle ARM** dans l’interface utilisateur. Vous pouvez ensuite fusionner le fichier dans la branche de collaboration.

> [!NOTE]
> Une configuration de paramètre Resource Manager personnalisée ne change pas la limite de 256 caractères du modèle ARM. Il vous permet de choisir et de diminuer le nombre de propriétés paramétrées.

## <a name="custom-parameter-syntax"></a>Syntaxe de paramètre personnalisé

Vous trouverez ci-dessous quelques recommandations à suivre lorsque vous créez le fichier de paramètres personnalisés **arm-template-parameters-definition.json**. Le fichier comprend une section pour chaque type d’entité : déclencheur, pipeline, service lié, jeu de données, runtime d’intégration et flux de données.

* Entrez le chemin d’accès de propriété sous le type d’entité correspondant.
* Définir un nom de propriété sur `*` indique que vous souhaitez paramétrer toutes les propriétés dans celle-ci (uniquement jusqu’au premier niveau, pas de manière récursive). Vous pouvez également fournir des exceptions à cette configuration.
* Définir la valeur d’une propriété sous forme de chaîne indique que vous souhaitez paramétrer la propriété. Utilisez le format `<action>:<name>:<stype>`.
   *  `<action>` peut être l’un des caractères suivants :
      * `=` permet de conserver la valeur actuelle en tant que valeur par défaut pour le paramètre.
      * `-` permet de ne pas conserver la valeur par défaut pour le paramètre.
      * `|` est un cas particulier pour les secrets Azure Key Vault pour les chaînes de connexion ou les clés.
   * `<name>` correspond au nom du paramètre. S’il est vide, il prend le nom du Si la valeur commence par un caractère `-`, le nom est abrégé. Par exemple, `AzureStorage1_properties_typeProperties_connectionString` serait abrégé en `AzureStorage1_connectionString`.
   * `<stype>` correspond au type de paramètre. Si `<stype>` est vide, le type par défaut est `string`. Valeurs prises en charge : `string`, `securestring`, `int`, `bool`, `object`, `secureobject` et `array`.
* La spécification d’un tableau dans le fichier de définition indique que la propriété correspondante dans le modèle est un tableau. Data Factory effectue une itération sur tous les objets du tableau en utilisant la définition spécifiée dans l’objet de runtime d’intégration du tableau. Le second objet, une chaîne, correspond alors au nom de la propriété et sert de nom au paramètre pour chaque itération.
* Une définition ne peut pas être spécifique à une instance de ressource. Toute définition s’applique à toutes les ressources de ce type.
* Par défaut, toutes les chaînes sécurisées, telles que les secrets Key Vault, et les chaînes sécurisées, telles que les chaînes de connexion, les clés et les jetons, sont paramétrables.
 
## <a name="sample-parameterization-template"></a>Exemple de modèle de paramétrage

Voici un exemple de ce à quoi peut ressembler la configuration d’un paramètre Resource Manager :

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/credentials" : {
        "properties": {
            "typeProperties": {
                "resourceId": "="
            }
        }
    }
}
```
Voici une explication de la façon dont le modèle précédent est construit, décomposé par type de ressource.

### <a name="pipelines"></a>Pipelines
    
* Toute propriété du chemin `activities/typeProperties/waitTimeInSeconds` est paramétrable. Toute activité dans un pipeline qui a une propriété au niveau du code nommée `waitTimeInSeconds` (par exemple, l’activité `Wait`) est paramétrable en tant que nombre, avec un nom par défaut. Mais elle n’a pas de valeur par défaut dans le modèle Resource Manager. Il s’agit d’une entrée obligatoire lors du déploiement de Resource Manager.
* De même, une propriété appelée `headers` (par exemple, dans une activité `Web`) est paramétrable avec le type `object` (JObject). Elle a une valeur par défaut, qui est la même que celle de la fabrique source.

### <a name="integrationruntimes"></a>IntegrationRuntimes

* Toutes les propriétés, sous le chemin `typeProperties` sont paramétrables avec des valeurs par défaut respectives. Par exemple, deux propriétés existent sous les propriétés de type `IntegrationRuntimes` : `computeProperties` et `ssisProperties`. Les deux types de propriété sont créés avec leurs valeurs et types (objet) par défaut respectifs.

### <a name="triggers"></a>Déclencheurs

* Sous `typeProperties`, deux propriétés sont paramétrables. La première est `maxConcurrency`, qui est spécifiée pour avoir une valeur par défaut et est de type `string`. Elle porte le nom de paramètre par défaut `<entityName>_properties_typeProperties_maxConcurrency`.
* La propriété `recurrence` est également paramétrable. Sous celle-ci, toutes les propriétés à ce niveau sont spécifiées pour être paramétrables sous forme de chaînes, avec des valeurs et noms de paramètres par défaut. La propriété `interval` est une exception, qui est paramétrée en tant que type `int`. Le nom du paramètre a pour suffixe `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. De même, la propriété `freq` est une chaîne et peut être paramétrée en tant que chaîne. La propriété `freq` est toutefois paramétrable sans valeur par défaut. Le nom est abrégé et suivi d’un suffixe. Par exemple : `<entityName>_freq`.

### <a name="linkedservices"></a>LinkedServices

* Les services liés sont uniques. Étant donné que les services liés et les jeux de données sont de types différents, vous pouvez fournir une personnalisation spécifique au type. Dans cet exemple, pour tous les services liés de type `AzureDataLakeStore`, un modèle spécifique est appliqué. Pour tous les autres (via `*`), un autre modèle est appliqué.
* La propriété `connectionString` est paramétrée en tant que valeur `securestring`. Elle n’a pas de valeur par défaut. Elle a un nom de paramètre raccourci doté du suffixe `connectionString`.
* La propriété `secretAccessKey` se trouve être un `AzureKeyVaultSecret` (par exemple, dans un service lié Amazon S3). Elle est paramétrable automatiquement en tant que secret Azure Key Vault et extraite du coffre de clés configuré. Vous pouvez également paramétrer le coffre de clés proprement dit.

### <a name="datasets"></a>Groupes de données

* La personnalisation spécifique au type est disponible pour les jeux de données, mais vous pouvez fournir une configuration sans avoir explicitement de configuration au niveau \*. Dans l’exemple précédent, toutes les propriétés du jeu de données sous `typeProperties` sont paramétrables.

> [!NOTE]
> Les **alertes et matrices Azure**, si elles sont configurées pour un pipeline, ne sont actuellement pas prises en charge en tant que paramètres pour les déploiements ARM. Pour réappliquer les alertes et les matrices dans un nouvel environnement, suivez [Surveillance, alertes et matrices Data Factory.](./monitor-metrics-alerts.md)
> 

## <a name="default-parameterization-template"></a>Modèle de paramétrage par défaut

Vous trouverez ci-dessous le modèle actuel. Si vous n’avez besoin d’ajouter que quelques paramètres, il peut être judicieux de modifier directement ce modèle, car vous ne perdrez pas la structure de paramétrage existante.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

## <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Exemple : paramétrage d’un ID de cluster interactif Azure Databricks existant

L’exemple suivant montre comment ajouter une valeur unique au modèle de paramétrage par défaut. Nous voulons seulement ajouter un ID de cluster Azure Databricks interactif pour un service Databricks lié au fichier de paramètres. Notez que ce fichier est le même que le fichier précédent, à l’exception de l’ajout de `existingClusterId` sous le champ de propriétés de `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’intégration et de la livraison continues](continuous-integration-delivery.md)
- [Automatiser l’intégration continue à l’aide des versions d’Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Promouvoir manuellement un modèle Resource Manager pour chaque environnement](continuous-integration-delivery-manual-promotion.md)
- [Modèles Resource Manager liés](continuous-integration-delivery-linked-templates.md)
- [Utilisation d’un environnement de production de correctif logiciel](continuous-integration-delivery-hotfix-environment.md)
- [Exemple de script de pré-déploiement et de post-déploiement](continuous-integration-delivery-sample-script.md)