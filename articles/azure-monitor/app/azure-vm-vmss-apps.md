---
title: Surveiller les performances sur les machines virtuelles Azure - Azure Application Insights
description: Analyse des performances des applications pour les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure. Analysez la charge, le temps de réponse et les dépendances dans des graphiques, et définissez des alertes sur les performances.
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6b2eb0a9413766878e8abb9ee0ce24b490b10909
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067751"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Déployer Azure Monitor Application Insights Agent sur des machines virtuelles Azure et des groupes de machines virtuelles identiques Azure

Désormais, l’activation de la supervision pour vos applications web .NET ou Java qui s’exécutent sur des [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/) et des [groupes de machines virtuelles identiques Azure](../../virtual-machine-scale-sets/index.yml) est plus facile que jamais. Bénéficiez de tous les avantages de l’utilisation de Application Insights sans modifier votre code.

Cet article vous explique comment activer la supervision Application Insights à l’aide du module Application Insights Agent. De plus, il fournit une aide préliminaire à l’automatisation des déploiements à grande échelle.
> [!IMPORTANT]
> Les applications basées sur **Java** qui s’exécutent sur des machines virtuelles Azure et VMSS sont supervisées avec l’ **[agent Application Insights Java 3.0](./java-in-process-agent.md)** , qui est généralement disponible.

> [!IMPORTANT]
> L’agent Azure Application Insights pour les applications ASP.NET et ASP.NET Core s’exécutant sur des **machines virtuelles Azure et VMSS** est actuellement disponible en préversion publique. Pour surveiller vos applications ASP.NET qui s’exécutent **localement**, utilisez l’agent [Azure Application Insights pour les serveurs locaux](./status-monitor-v2-overview.md), qui est en disponibilité générale et pris en charge intégralement.
> La préversion pour les machines virtuelles Azure et VMSS est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou n’être pas prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Activer Application Insights

Il existe deux manières d’activer la supervision des applications pour les applications hébergées sur des machines virtuelles Azure et des groupes de machines virtuelles identiques Azure :

### <a name="auto-instrumentation-via-application-insights-agent"></a>Instrumentation automatique par le biais du module Application Insights Agent

* Cette méthode est la plus facile à activer, car elle ne nécessite aucune configuration avancée. Elle est souvent appelée supervision « runtime ».

* Pour les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure, nous recommandons au minimum d’activer ce niveau de supervision. Après cela, en fonction de votre scénario spécifique, vous pouvez évaluer si une instrumentation manuelle est nécessaire.

> [!NOTE]
> L’instrumentation automatique est disponible uniquement pour Java et les applications ASP.NET et ASP.NET Core hébergées par IIS. Utilisez un Kit de développement logiciel (SDK) pour instrumenter les applications Node.js et Python hébergées sur des machines virtuelles Azure et des groupes de machines virtuelles identiques Azure.


#### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

  * Le module Application Insights Agent collecte automatiquement les mêmes signaux de dépendance prêts à l’emploi que le kit SDK .NET. Pour en savoir plus, consultez [Collecte automatique de dépendance](./auto-collect-dependencies.md#net).
        
#### <a name="java"></a>Java
  * Pour Java, l’ **[agent Application Insights Java 3.0](./java-in-process-agent.md)** est l’approche recommandée. Les bibliothèques et les frameworks les plus populaires ainsi que les journaux et les dépendances sont [collectés automatiquement](./java-in-process-agent.md#auto-collected-requests), avec une multitude de [configurations supplémentaires](./java-standalone-config.md).

### <a name="code-based-via-sdk"></a>Avec du code via le kit SDK
    
#### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core
  * Pour les applications .NET, cette approche est beaucoup plus personnalisable, mais elle nécessite d’[ajouter une dépendance sur les packages NuGet du SDK Application Insights](./asp-net.md). Cette méthode implique également de gérer vous-même l’installation des mises à jour vers la dernière version des packages.

  * Utilisez cette méthode si vous devez effectuer des appels d’API personnalisés pour suivre les événements/dépendances qui ne sont pas capturés par défaut avec la supervision basée sur un agent. Pour en savoir plus, consultez l’[article sur l’API Application Insights pour les événements et mesures personnalisés](./api-custom-events-metrics.md).

    > [!NOTE]
    > Pour les applications .NET uniquement, si les deux méthodes, la supervision basée sur un agent et l’instrumentation manuelle basée sur un Kit de développement logiciel (SDK), sont détectées, seuls les paramètres de l’instrumentation manuelle sont appliqués. Cela évite que des données en double soient envoyées. Pour en savoir plus, consultez la [section de résolution des problèmes](#troubleshooting) ci-après.

#### <a name="java"></a>Java 

Si vous avez besoin de données de télémétrie personnalisées supplémentaires pour les applications Java, consultez ce qui [est disponible](./java-in-process-agent.md#custom-telemetry), ajoutez des [dimensions personnalisées](./java-standalone-config.md#custom-dimensions) ou utilisez des [processeurs de télémétrie](./java-standalone-telemetry-processors.md). 

#### <a name="nodejs"></a>Node.js

Pour instrumenter votre application Node.js, utilisez le [SDK](./nodejs.md).

#### <a name="python"></a>Python

Pour superviser les applications Python, utilisez le [SDK](./opencensus-python.md).

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Gérer Application Insights Agent pour les applications .NET sur des machines virtuelles Azure à l’aide de PowerShell

> [!NOTE]
> Avant d’installer l’agent Application Insights, vous aurez besoin d’une chaîne de connexion. [Créez une ressource Application Insights](./create-new-resource.md) ou copiez la chaîne de connexion à partir d’une ressource Application Insights existante.

> [!NOTE]
> Vous découvrez PowerShell ? Consultez le [Guide de prise en main](/powershell/azure/get-started-azureps).

Installer ou mettre à jour le module Application Insights Agent en tant qu’extension pour des machines virtuelles Azure
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Vous pouvez installer ou mettre à jour le module Application Insights Agent en tant qu’extension sur plusieurs machines virtuelles à grande échelle, à l’aide d’une boucle PowerShell.

Désinstaller l’extension Application Insights Agent d’une machine virtuelle Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Interroger l’état de l’extension Application Insights Agent pour une machine virtuelle Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Obtenir la liste des extensions installées pour une machine virtuelle Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Vous pouvez également voir les extensions installées dans le [panneau de la machine virtuelle Azure](../../virtual-machines/extensions/overview.md) au sein du portail.

> [!NOTE]
> Vérifiez l’installation en cliquant sur Flux de métriques temps réel dans Application Insights Resource associée à la chaîne de connexion que vous avez utilisée pour déployer l’extension de Application Insights Agent. Si vous envoyez des données provenant de plusieurs machines virtuelles, sélectionnez les machines virtuelles Azure cibles sous Nom du serveur. Il peut s’écouler jusqu’à une minute avant le début du transfert des données.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Gérer Application Insights Agent pour les applications .NET sur des groupes de machines virtuelles identiques Azure à l’aide de PowerShell

Installer ou mettre à jour le module Application Insights Agent en tant qu’extension pour un groupe de machines virtuelles identiques Azure
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoringWindows" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Désinstaller l’extension de supervision des applications sur des groupes de machines virtuelles identiques Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Interroger l’état de l’extension de supervision des applications pour des groupes de machines virtuelles identiques Azure
```powershell
# Not supported by extensions framework
```

Obtenir la liste des extensions installées pour des groupes de machines virtuelles identiques Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Dépannage

Vous trouverez des conseils de résolution des problèmes liés à l’extension Application Insights Monitoring Agent pour les applications .NET qui s’exécutent sur des machines virtuelles Azure et des groupes de machines virtuelles identiques Azure.

> [!NOTE]
> Dans la mesure où les applications .NET Core, Node.js et Python sont uniquement prises en charge sur les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure via une instrumentation manuelle basée sur le kit SDK, les étapes ci-dessous ne s’appliquent pas à ces scénarios.

La sortie de l’exécution de l’extension est journalisées dans des fichiers figurant dans les répertoires suivants :
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [déployer une application sur un groupe de machines virtuelles identiques Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Configuration des tests de disponibilité web](monitor-web-app-availability.md), pour recevoir des alertes en cas d’interruption de votre point de terminaison.
