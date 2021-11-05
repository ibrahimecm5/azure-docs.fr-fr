---
title: Se connecter à un cluster Service Fabric managé
description: Découvrez comment vous connecter à un cluster Service Fabric managé
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: aa168a283be909678a199eee887d69bb3eb2024a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096290"
---
# <a name="connect-to-a-service-fabric-managed-cluster"></a>Se connecter à un cluster Service Fabric managé

Une fois que vous avez déployé un cluster via le [modèle managé des ressources Azure, Portail](quickstart-managed-cluster-template.md) ou [PowerShell](tutorial-managed-cluster-deploy.md), il existe différentes façons de vous connecter à votre cluster managé et de l’afficher. 

## <a name="use-azure-portal"></a>Utiliser le portail Azure

Accédez à votre ressource de cluster managé en

 1) accédant à https://portal.azure.com/

 2) Accédez à votre ressource de cluster en recherchant Service Fabric et en sélectionnant « clusters managés Service Fabric »

 3) Sélectionner votre cluster

 4) Dans cette expérience, vous pouvez afficher et modifier certains paramètres. Pour plus d’informations, consultez les [options de configuration du cluster](how-to-managed-cluster-configuration.md) disponibles.

## <a name="use-service-fabric-explorer"></a>Utiliser Service Fabric Explorer

[Service Fabric Explorer](https://github.com/Microsoft/service-fabric-explorer) (SFX) est une application dédiée à l’inspection et à la gestion de l’intégrité des applications et des clusters d’un cluster Microsoft Azure Service Fabric. 

Pour accéder à SFX pour votre cluster managé
 
 1) accédant à https://portal.azure.com/
 
 2) Accédez à votre ressource de cluster en recherchant Service Fabric et en sélectionnant « clusters managés Service Fabric »

 3) Sélectionner votre cluster

 4) Localisez le `SF Explorer` situé dans le coin supérieur droit, par exemple : https://mycluster.region.cloudapp.azure.com:19080/Explorer

## <a name="use-powershell-modules"></a>Utiliser des modules PowerShell

Les modules PowerShell suivants sont disponibles pour la connexion, l’affichage et la modification des configurations de votre cluster. 

* Installez le [Kit de développement logiciel (SDK) Service Fabric et le module PowerShell](service-fabric-get-started.md).

* Installez [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (ou ultérieur).

### <a name="using-the-service-fabric-powershell-module"></a>Utilisation du Module Service Fabric PowerShell
Pour utiliser ce module, vous avez besoin de l’empreinte numérique du certificat de cluster. Vous pouvez trouver cette valeur dans la sortie des propriétés du cluster de votre déploiement de ressources ou en interrogeant les propriétés du cluster sur une ressource existante.

La commande suivante peut être utilisée pour interroger votre ressource de cluster afin d’obtenir l’empreinte numérique du certificat de cluster.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprints
```

Avec l’empreinte numérique du certificat de cluster, vous êtes prêt à vous connecter à votre cluster.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="using-the-azure-service-fabric-powershell-module"></a>Utilisation du Module Service Fabric PowerShell Azure

Le module Service Fabric Azure vous permet d’effectuer des opérations telles que la création d’un cluster managé, la mise à l’échelle d’un type de nœud et l’affichage d’informations sur les ressources de cluster managées. Les cmdlets spécifiques prises en charge pour les clusters managés sont nommées `AzServiceFabricManagedCluster*` que vous pouvez référencer dans la documentation du [module PowerShell AZ.ServiceFabric](/powershell/module/az.servicefabric/).


L’exemple suivant utilise une des cmdlets pour afficher les détails d’un cluster managé.

```powershell
$rgName = "testResourceGroup"
$clusterName = "mycluster"
Get-AzServiceFabricManagedCluster -ResourceGroupName $rgName -Name $clusterName
```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer une application sur un cluster managé avec Azure Resource Manager](how-to-managed-cluster-app-deployment-template.md)
* [Configurer les options de cluster managé](how-to-managed-cluster-configuration.md)



