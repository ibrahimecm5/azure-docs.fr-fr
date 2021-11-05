---
title: Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel via Azure PowerShell
description: Découvrez comment joindre un runtime d’intégration Azure-SSIS à un réseau virtuel via Azure PowerShell.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a4d06256407fd42bdfa9f92cc2306df7dd33983c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087853"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network-via-azure-powershell"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel via Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cet article explique comment joindre votre runtime d’intégration Azure-SQL Server Integration Services (SSIS) existant dans Azure Data Factory (ADF) à un réseau virtuel via Azure PowerShell. 

## <a name="create-variables"></a>Créer des variables

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Azure Resource Manager or Classic
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server configured with a private endpoint/IP firewall rule/virtual network service endpoint or Azure SQL Managed Instance that joins a virtual network to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend Azure Resource Manager virtual network, because classic virtual network will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for Azure SQL Database server configured with a virtual network service endpoint or a different subnet from the one used for Azure SQL Managed Instance that joins a virtual network
$SubnetId = $VnetId + '/subnets/' + $SubnetName 
# Virtual network injection method: Standard or Express. For comparison, see https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-virtual-network-configuration.
$VnetInjectionMethod = "Standard" # Standard by default, whereas Express lets you use the express virtual network injection method
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

## <a name="configure-a-virtual-network"></a>Configurer un réseau virtuel

Avant de joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel, vous devez configurer ce dernier. Pour configurer automatiquement les paramètres et les autorisations du réseau virtuel pour votre Azure-SSIS IR pour joindre un réseau virtuel, ajoutez le script suivant :

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Créer un runtime d’intégration Azure-SSIS et le joindre à un réseau virtuel

Vous pouvez créer un runtime d’intégration Azure-SSIS et le joindre à un réseau virtuel en même temps. Pour obtenir le script complet et toutes les instructions, consultez [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime-powershell.md).

## <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel

L’article [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime-powershell.md) explique comment créer un runtime d’intégration Azure-SSIS IR et le joindre à un réseau virtuel dans le même script. Si vous disposez déjà d’un Azure-SSIS IR, effectuez les opérations suivantes pour le joindre au réseau virtuel : 
1. arrêtez votre Azure-SSIS IR 
1. configurez votre Azure-SSIS IR pour joindre un réseau virtuel 
1. démarrez votre Azure-SSIS IR 

## <a name="stop-your-azure-ssis-ir"></a>arrêtez votre Azure-SSIS IR.

Vous devez arrêter votre Azure-SSIS IR avant de le joindre à un réseau virtuel. Cette commande libère tous ses nœuds et arrête la facturation :

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

## <a name="configure-your-azure-ssis-ir-to-join-a-virtual-network"></a>Configurer votre Azure-SSIS IR pour joindre un réseau virtuel

Pour joindre votre Azure-SSIS IR à un réseau virtuel, exécutez la commande `Set-AzDataFactoryV2IntegrationRuntime` : 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -SubnetId $SubnetId `
    -VNetInjectionMethod $VnetInjectionMethod

# Add public IP address parameters if you use the standard virtual network injection method and bring your own static public IP addresses
if($VnetInjectionMethod -eq "Standard")
{
    if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
    {
        $publicIPs = @($FirstPublicIP, $SecondPublicIP)
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -PublicIPs $publicIPs
    }
}
```

## <a name="start-your-azure-ssis-ir"></a>Démarrer votre Azure-SSIS IR

Pour démarrer votre Azure-SSIS IR, exécutez la commande suivante : 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Si vous utilisez la méthode d’injection de réseau virtuel express/standard, cette commande prend, respectivement, 5/20-30 minutes.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer un réseau virtuel pour injecter Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Méthode d’injection de réseau virtuel Express](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Méthode d’injection de réseau virtuel standard](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Joindre Azure-SSIS IR à un réseau virtuel via une interface utilisateur ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 

- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise un serveur Azure SQL Database pour héberger SSISDB. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il fournit des instructions sur l’utilisation d’un serveur Azure SQL Database configuré avec un point de terminaison de service de réseau virtuel, une règle de pare-feu IP ou un point de terminaison privé ou Azure SQL Managed Instance qui joint un réseau virtuel pour héberger SSISDB. Il explique comment joindre votre Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
