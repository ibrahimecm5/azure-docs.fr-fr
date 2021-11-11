---
title: Intégration d’une machine à Azure Automanage avec un modèle ARM
description: Découvrez comment intégrer une machine à Azure Automanage avec un modèle Azure Resource Manager.
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: cdb10c265ddae4aaf83450c1951ef6cb5c2219df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443848"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Intégration d’une machine à Automanage avec un modèle Azure Resource Manager (ARM)


## <a name="overview"></a>Vue d’ensemble
Suivez les étapes ci-dessous afin d’intégrer une machine aux meilleures pratiques d’Automanage avec un modèle ARM.

## <a name="prerequisites"></a>Prérequis
* Vous devez disposer des [autorisations RBAC](./automanage-virtual-machines.md#required-rbac-permissions) nécessaires
* Vous devez être dans une région prise en charge et l’image de machine virtuelle prise en charge est mise en surbrillance dans ces [prérequis](./automanage-virtual-machines.md#prerequisites)


## <a name="arm-template-overview"></a>Présentation des modèles ARM
Le modèle ARM suivant intégrera votre machine spécifiée sur les meilleures pratiques d’Azure Automanage. Pour plus d’informations sur le modèle ARM et les étapes de déploiement, reportez-vous à la section Déploiement de modèle ARM [ci-dessous](#arm-template-deployment).
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "configurationProfile": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2021-04-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfile')]",
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>Déploiement de modèle ARM
Le modèle ARM ci-dessus crée une attribution de profil de configuration pour votre machine spécifiée. 

Les valeurs `configurationProfile` possibles sont les suivantes :
* "/providers/Microsoft.Automanage/bestPractices/AzureBestPracticesProduction"
* "/providers/Microsoft.Automanage/bestPractices/AzureBestPracticesDevTest"

Suivez ces étapes pour déployer le modèle ARM :
1. Enregistrez le modèle ARM ci-dessus en tant que `azuredeploy.json`
1. Exécutez le déploiement de modèle ARM avec `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`
1. Fournissez les valeurs pour machineName, automanageAccountName et configurationProfileAssignment lorsque vous y êtes invité
1. Vous avez terminé !

Comme avec n’importe quel modèle ARM, il est possible de factoriser les paramètres dans un fichier `azuredeploy.parameters.json` distinct et de les utiliser en tant qu’arguments lors du déploiement.

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur Automanage pour [Linux](./automanage-linux.md) et [Windows](./automanage-windows-server.md)