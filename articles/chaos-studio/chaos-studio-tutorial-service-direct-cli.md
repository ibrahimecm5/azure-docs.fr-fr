---
title: Créer une expérience qui utilise une erreur directe de service à l’aide d’Azure Chaos Studio avec le portail Azure
description: Créer une expérience qui utilise une erreur directe de service avec Azure CLI
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: how-to
ms.date: 11/10/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 7366cf2a8452b40830a9b73f314caeaf63368c64
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373510"
---
# <a name="create-a-chaos-experiment-that-uses-a-service-direct-fault-with-the-azure-cli"></a>Créer une expérience de chaos qui utilise une erreur directe de service avec Azure CLI

Vous pouvez utiliser une expérience de chaos pour vérifier que votre application résiste aux défaillances en les provoquant dans un environnement contrôlé. Dans ce guide, vous allez provoquer un basculement Azure Cosmos DB en lecture seule et en écriture à l’aide d’une expérience de chaos et d’Azure Chaos Studio. L’exécution de cette expérience peut vous aider à vous prémunir contre la perte de données lorsqu’un événement de basculement se produit.

Ces mêmes étapes peuvent être utilisées pour configurer et exécuter une expérience d’erreur de service direct. Une erreur de **service direct** s’exécute directement sur une ressource Azure sans nécessiter d’instrumentation, contrairement aux erreurs basées sur l’agent qui demandent l’installation de l’agent Chaos.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Un compte Azure Cosmos DB. Si vous ne disposez pas d’un compte Cosmos DB, vous pouvez [suivre ces étapes pour en créer un](../cosmos-db/sql/create-cosmosdb-resources-portal.md).
- Au moins deux configurations de région, une de lecture et une d’écriture pour votre compte Azure Cosmos DB.

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également ouvrir Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/bash](https://shell.azure.com/bash). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell et sélectionnez **Entrée** pour les exécuter.

Si vous préférez installer et utiliser l’interface de ligne de commande localement, vous aurez besoin d’Azure CLI version 2.0.30 ou ultérieure pour ce tutoriel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-chaos-studio-on-your-azure-cosmos-db-account"></a>Activer Chaos Studio sur votre compte Azure Cosmos DB

Chaos Studio ne peut pas injecter d’erreurs sur une ressource, sauf si elle a d’abord été intégrée à Chaos Studio. Vous intégrez une ressource à Chaos Studio en créant une [cible et des fonctionnalités](chaos-studio-targets-capabilities.md) sur la ressource. Les comptes Azure Cosmos DB n’ont qu’un seul type de cible (service direct) et qu’une seule fonctionnalité (basculement), mais d’autres ressources peuvent comporter jusqu’à deux types de cibles (l’un pour les erreurs de service direct, l’autre pour les erreurs basées sur l’agent) et de nombreuses fonctionnalités.

1. Créez une cible en remplaçant par `$RESOURCE_ID` l’ID de ressource de la ressource que vous intégrez, et `$TARGET_TYPE` par le [type de cible que vous intégrez](chaos-studio-fault-providers.md) :

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/$TARGET_TYPE?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    Par exemple, si vous intégrez une machine virtuelle en tant que cible directe de service :

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

2. Créez les fonctionnalités sur la cible en remplaçant par `$RESOURCE_ID` l’ID de ressource de la ressource que vous intégrez, `$TARGET_TYPE` par le [type de cible que vous intégrez](chaos-studio-fault-providers.md) et `$CAPABILITY` par le [nom de la fonctionnalité d’erreur que vous activez](chaos-studio-fault-library.md).
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/$TARGET_TYPE/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    Par exemple, si vous activez la fonctionnalité d’arrêt de machine virtuelle :

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

Vous venez d’achever l’intégration de votre compte Azure Cosmos DB à Chaos Studio.

## <a name="create-an-experiment"></a>Créer une expérience
Votre compte Azure Cosmos DB étant intégré, vous pouvez créer votre expérience. Une expérience de chaos définit les actions que vous souhaitez effectuer sur les ressources cibles, organisées en étapes, qui s’exécutent de manière séquentielle, et les branches qui s’exécutent en parallèle.

1. Formulez votre JSON d’expérience à partir de l’exemple de JSON ci-dessous. Modifiez le JSON pour qu’il corresponde à l’expérience que vous souhaitez exécuter à l’aide de l’[API de création d’expérience](/rest/api/chaosstudio/experiments/create-or-update) et de la [bibliothèque d’erreurs](chaos-studio-fault-library.md).

    ```json
    {
      "location": "eastus",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "steps": [
          {
            "name": "Step1",
            "branches": [
              {
                "name": "Branch1",
                "actions": [
                  {
                    "type": "continuous",
                    "selectorId": "Selector1",
                    "duration": "PT10M",
                    "parameters": [
                      {
                        "key": "readRegion",
                        "value": "East US 2"
                      }
                    ],
                    "name": "urn:csci:microsoft:cosmosDB:failover/1.0"
                  }
                ]
              }
            ]
          }
        ],
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB"
              }
            ]
          }
        ]
      }
    }
    ```
    
2. Créez l’expérience à l’aide d’Azure CLI, en remplaçant `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` et `$EXPERIMENT_NAME` par les propriétés de votre expérience. Assurez-vous que vous avez enregistré et chargé votre JSON d’expérience, puis mettez à jour `experiment.json` avec votre nom de fichier JSON.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    Chaque expérience crée une identité managée affectée par le système correspondante. Notez le `principalId` pour cette identité dans la réponse en vue de l’étape suivante.

## <a name="give-experiment-permission-to-your-azure-cosmos-db-account"></a>Accorder une autorisation d’expérience à votre compte Azure Cosmos DB
Lorsque vous créez une expérience de chaos, Chaos Studio crée une identité managée affectée par le système qui exécute des erreurs sur vos ressources cibles. Cette identité doit avoir les [autorisations appropriées](chaos-studio-fault-providers.md) sur la ressource cible pour que l’expérience s’exécute correctement.

Donnez à l’expérience un accès à vos ressources à l’aide de la commande ci-dessous, en remplaçant `$EXPERIMENT_PRINCIPAL_ID` par le principalId de l’étape précédente, et `$RESOURCE_ID` par l’ID de ressource de la ressource cible (en l’occurrence, l’ID de ressource d’instance Cosmos DB). Remplacez le rôle par le [rôle intégré approprié pour ce type de ressource](chaos-studio-fault-providers.md). Exécutez cette commande pour chaque ressource ciblée dans votre expérience. 

```azurecli-interactive
az role assignment create --role "Cosmos DB Operator" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID
```

## <a name="run-your-experiment"></a>Exécuter votre expérience
Vous pouvez désormais exécuter votre service. Pour voir l’impact, nous vous recommandons d’ouvrir la vue d’ensemble de votre compte Azure Cosmos DB et d’accéder à **Répliquer les données globalement** dans un onglet de navigateur distinct. L’actualisation périodique pendant l’expérimentation affichera l’échange de régions.

1. Démarrez l’expérience à l’aide d’Azure CLI, en remplaçant `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` et `$EXPERIMENT_NAME` par les propriétés de votre expérience.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. La réponse comprend une URL d’état que vous pouvez utiliser pour interroger l’état de l’expérience pendant l’exécution de celle-ci.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez exécuté une expérience directe de service Azure Cosmos DB, vous êtes prêt à :
- [Créer une expérience qui utilise des erreurs basées sur un agent](chaos-studio-tutorial-agent-based-portal.md)
- [Choisir votre expérience](chaos-studio-run-experiment.md)
