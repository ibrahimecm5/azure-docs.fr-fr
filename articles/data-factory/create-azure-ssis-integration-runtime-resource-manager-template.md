---
title: Utiliser un modèle Azure Resource Manager pour créer un runtime d’intégration
description: Découvrez comment utiliser un modèle Azure Resource Manager pour créer un runtime d'intégration Azure-SSIS dans Azure Data Factory afin de pouvoir déployer et exécuter des packages SSIS dans Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 322a2277169b53a9c49d496524789636a709af2a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852633"
---
# <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Utiliser un modèle Azure Resource Manager pour créer un runtime d’intégration

Dans le cadre de cette section, vous allez utiliser un modèle Azure Resource Manager pour créer le runtime d’intégration Azure-SSIS. 

## <a name="sample-azure-resource-manager-template"></a>Exemple de modèle Azure Resource Manager

Les étapes suivantes permettent de créer un runtime d'intégration Azure-SSIS avec un modèle Azure Resource Manager :

1. Créez un fichier JSON avec le modèle Azure Resource Manager suivant. Remplacez les valeurs entre chevrons (espaces réservés) par vos propres valeurs.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Pour déployer le modèle Azure Resource Manager, exécutez la commande `New-AzResourceGroupDeployment` comme indiqué dans l’exemple suivant. Dans l’exemple, `ADFTutorialResourceGroup` est le nom de votre groupe de ressources. `ADFTutorialARM.json` est le fichier qui contient la définition JSON pour votre fabrique de données et le runtime d’intégration Azure-SSIS.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Cette commande crée votre fabrique de données et un runtime d’intégration Azure-SSIS dans cette fabrique, mais elle ne démarre pas le runtime d’intégration.

3. Pour démarrer l’IR Azure-SSIS, exécutez la commande `Start-AzDataFactoryV2IntegrationRuntime` :

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes. Toutefois, l’IR Azure-SSIS peut prendre jusqu’à 20-30 minutes pour joindre un réseau virtuel.
>
> Si vous utilisez le catalogue SSISDB, le service Data Factory se connecte à votre serveur de base de données pour le préparer. Il configure également les autorisations et les paramètres de votre réseau virtuel, s’ils sont spécifiés, et joint votre runtime d’intégration Azure-SSIS au réseau virtuel.
> 
> Quand vous provisionnez un runtime d’intégration Azure-SSIS, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants assurent la connectivité aux fichiers Excel et Access ainsi qu’à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Pour plus d’informations sur les composants intégrés/préinstallés, consultez [Composants intégrés/préinstallés sur Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Pour plus d’informations sur les composants supplémentaires que vous pouvez installer, consultez [Personnalisation des configurations du runtime d’intégration Azure-SSIS](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment configurer un IR Azure-SSIS à l’aide du portail Azure](create-azure-ssis-integration-runtime-portal.md).
- [Découvrez comment approvisionner un IR Azure-SSIS à l'aide d'Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md).
- [Déployez et exécutez vos packages SSIS dans Azure Data Factory](create-azure-ssis-integration-runtime-deploy-packages.md).

Consultez les autres rubriques relatives au runtime d’intégration Azure SSIS dans cette documentation :

- [Runtime d’intégration Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations sur les runtimes d’intégration en général, notamment sur le runtime d’intégration Azure-SSIS.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
- [Déployer, exécuter et surveiller des packages SSIS dans Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Se connecter à SSISDB dans Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Planifier les exécutions de packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
