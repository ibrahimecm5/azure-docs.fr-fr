---
title: 'Démarrage rapide : Créer un profil pour la haute disponibilité des applications - Azure PowerShell - Azure Traffic Manager'
description: Cet article de démarrage rapide décrit comment créer un profil Traffic Manager pour créer des applications web hautement disponibles.
services: traffic-manager
author: duongau
ms.author: duau
manager: kumud
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: a970013ebffc9ed4249baf34de39d146357742c4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074647"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Démarrage rapide : Créer un profil Traffic Manager pour assurer une haute disponibilité à vos applications web avec Azure PowerShell

Ce démarrage rapide explique comment créer un profil Traffic Manager qui assure une haute disponibilité pour votre application web.

Dans ce démarrage rapide, vous allez créer deux instances d’une application web. Chacune d’elles s’exécute dans une région Azure distincte. Vous allez créer un profil Traffic Manager en fonction de la [priorité du point de terminaison](traffic-manager-routing-methods.md#priority-traffic-routing-method). Le profil dirige le trafic utilisateur vers le site principal exécutant l’application web. Traffic Manager supervise en permanence l’application web. Si le site principal est indisponible, il assure un basculement automatique vers le site de secours.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Diagramme de l’environnement de déploiement Traffic Manager utilisant Azure PowerShell." border="false":::

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créer un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive

# Variables
$Location1="EastUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager

Créez un profil Traffic Manager avec [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) qui dirige le trafic utilisateur en fonction de la priorité du point de terminaison.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Créer des applications web

Pour ce guide de démarrage rapide, vous aurez besoin de deux instances d’une application web déployée dans deux régions Azure différentes (*USA Ouest* et *USA Est*). Chacune servira de point de terminaison principal et de point de terminaison de basculement à Traffic Manager.

### <a name="create-web-app-service-plans"></a>Créer des plans Web App Service
Créez des plans Web App Service avec [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) pour les deux instances de l’application web que vous allez déployer dans deux régions Azure différentes.

```azurepowershell-interactive

# Variables
$Location1="EastUS"
$Location2="WestEurope"

# Create an App service plan
New-AzAppservicePlan -Name "myAppServicePlanEastUS" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "myAppServicePlanEastUS" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Créer une application web dans le plan App Service
Créez deux instances de l’application web en utilisant la commande [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) dans les plans App Service, dans les régions Azure *USA Est* et *Europe Ouest*.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name myWebAppEastUS -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "myAppServicePlanEastUS").Id
$App2ResourceId=(New-AzWebApp -Name myWebAppWestEurope -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "myAppServicePlanWestEurope").Id

```

## <a name="add-traffic-manager-endpoints"></a>Ajouter des points de terminaison Traffic Manager
Ajoutez les deux applications web comme points de terminaison Traffic Manager avec [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) dans le profil Traffic Manager comme suit :
- Ajoutez l’application web située dans la région Azure *USA Est* comme point de terminaison principal afin de router tout le trafic utilisateur. 
- Ajoutez l’application web située dans la région Azure *Europe Ouest* comme point de terminaison de basculement. Quand le point de terminaison principal n’est pas disponible, le trafic est automatiquement routé vers le point de terminaison de basculement.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "myPrimaryEndpoint" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "myFailoverEndpoint" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Tester le profil Traffic Manager

Dans cette section, vous allez vérifier le nom de domaine de votre profil Traffic Manager. Vous allez aussi configurer le point de terminaison principal pour le rendre indisponible. Enfin, vous allez pouvoir constater que l’application est toujours disponible. Cela est dû au fait que Traffic Manager envoie le trafic au point de terminaison de basculement.

### <a name="determine-the-dns-name"></a>Déterminer le nom DNS

Vous pouvez déterminer le nom DNS du profil Traffic Manager en avec [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Copiez la valeur de **RelativeDnsName**. Le nom DNS de votre profil Traffic Manager est *http://<* nomdnsrelatif *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Afficher Traffic Manager en action
1. Dans un navigateur web, entrez le nom DNS de votre profil Traffic Manager (*http://<* nomdnsrelatif *>.trafficmanager.net*) pour afficher le site web par défaut de votre application web.

    > [!NOTE]
    > Dans ce scénario de démarrage rapide, toutes les demandes sont routées vers le point de terminaison principal. Il est défini sur **Priorité 1**.
2. Pour voir le basculement de Traffic Manager en action, désactivez votre site principal avec [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name "myPrimaryEndpoint" `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Copiez le nom DNS de votre profil Traffic Manager (*http://<* nomdnsrelatif *>.trafficmanager.net*) pour afficher le site web dans une nouvelle session de navigateur web.
4. Vérifiez que l’application web est toujours disponible.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous avez terminé, supprimez les groupes de ressources, les applications web et toutes les ressources associées avec [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un profil Traffic Manager qui assure une haute disponibilité pour votre application web. Pour plus d’informations sur le routage du trafic, passez aux tutoriels Traffic Manager.

> [!div class="nextstepaction"]
> [Didacticiels Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
