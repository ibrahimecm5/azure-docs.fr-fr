---
title: Configurer la mise à l’échelle automatique des nœuds de cluster managés Service Fabric
description: Découvrez comment configurer des stratégies de mise à l’échelle automatique sur un cluster managé Service Fabric.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 6c2dd14535b726d242d7076bcac94894e4c560dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097549"
---
# <a name="introduction-to-autoscaling-on-service-fabric-managed-clusters-preview"></a>Introduction à la mise à l’échelle automatique sur les clusters managés Service Fabric (préversion)
La [mise à l’échelle automatique](../azure-monitor/autoscale/autoscale-overview.md) offre une grande élasticité et permet l’ajout ou la réduction des nœuds à la demande sur un type de nœud secondaire. Ce comportement automatisé et élastique réduit les frais généraux dus à la gestion et l’impact potentiel sur l’entreprise en surveillant et en optimisant le nombre de nœuds traitant votre charge de travail. Vous configurez des règles pour votre charge de travail et laissez la mise à l’échelle automatique s’occuper du reste. Lorsque les seuils définis sont atteints, les règles de mise à l’échelle automatique prennent des mesures pour ajuster la capacité de votre type de nœud. La mise à l’échelle automatique peut être activée, désactivée ou configurée à tout moment. Cet article présente un exemple de déploiement et explique comment activer ou désactiver la mise à l’échelle automatique et comment configurer un exemple de stratégie de mise à l’échelle automatique.


**Configuration requise et métriques prises en charge :**
* Pour utiliser la mise à l’échelle automatique sur des clusters managés, vous devez utiliser la version d’API `2021-07-01-preview` ou une version ultérieure.
* Le niveau tarifaire du cluster doit être Standard.
* La mise à l’échelle automatique eut uniquement être configurée sur un type de nœud secondaire dans votre cluster.
* Après avoir activé la mise à l’échelle automatique pour un type de nœud, configurez la propriété `vmInstanceCount` sur `-1` lors du redéploiement de la ressource.
* Seules les [métriques publiées par Azure Monitor](../azure-monitor/essentials/metrics-supported.md) sont prises en charge.

La charge d’un service particulier qui varie dans le temps correspond à un scénario courant dans lequel la mise à l’échelle automatique peut s’avérer utile. Par exemple, un service comme une passerelle peut être mis à l’échelle en fonction de la quantité de ressources nécessaires pour gérer les demandes entrantes. Examinons un exemple de ce à quoi pourraient ressembler ces règles de mise à l’échelle, que nous utiliserons plus tard dans l’article :
* Si toutes les instances de ma passerelle utilisent plus de 70 % en moyenne, effectuez un scale-out du service de passerelle en ajoutant deux instances supplémentaires. Effectuez cette opération toutes les 30 minutes, mais n’ayez jamais plus de 20 instances au total.
* Si toutes les instances de ma passerelle utilisent moins de 40 % des cœurs en moyenne, effectuez un scale-in du service en supprimant une instance. Effectuez cette opération toutes les 30 minutes, mais n’ayez jamais moins de 3 instances au total.

## <a name="example-autoscale-deployment"></a>Exemple de déploiement de la mise à l’échelle automatique

Cet exemple présente les étapes suivantes : 
* La création d’un cluster managé Service Fabric Standard avec deux types de nœuds, `NT1` et `NT2` par défaut.
* L’ajout de règles de mise à l’échelle automatique au type de nœud secondaire, `NT2`.

>[!NOTE] 
> La mise à l’échelle automatique du type de nœud est effectuée en fonction des métriques de l’hôte de l’UC VMSS du cluster managé. La ressource VMSS est résolue automatiquement dans le modèle. 


La procédure suivante vous guidera pas à pas dans la configuration d’un cluster avec la mise à l’échelle automatique configurée.

1) Créer un groupe de ressources dans une région

   ```powershell 
   Login-AzAccount
   Select-AzSubscription -SubscriptionId $subscriptionid
   New-AzResourceGroup -Name $myresourcegroup -Location $location
   ```

2) Créer une ressource de cluster

   Téléchargez cet [exemple de cluster managé Service Fabric Standard](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/azuredeploy.json). Exécutez cette commande pour déployer la ressource de cluster :

   ```powershell
   $parameters = @{ 
   clusterName = $clusterName
   adminPassword = $VmAdminPassword
   clientCertificateThumbprint = $clientCertificateThumbprint
   } 
   New-AzResourceGroupDeployment -Name "deploy_cluster" -ResourceGroupName $resourceGroupName -TemplateFile .\azuredeploy.json -TemplateParameterObject $parameters -Verbose
   ```

3) Configurer et activer les règles de mise à l’échelle automatique sur un type de nœud secondaire
 
   Téléchargez l’[exemple de modèle de mise à l’échelle automatique pour un cluster managé](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/sfmc-deploy-autoscale.json) que vous utiliserez pour configurer la mise à l’échelle automatique avec les commandes suivantes :

   ```powershell
   $parameters = @{ 
   clusterName = $clusterName
   }
   New-AzResourceGroupDeployment -Name "deploy_autoscale" -ResourceGroupName $resourceGroupName -TemplateFile .\sfmc-deploy-autoscale.json -TemplateParameterObject $parameters -Verbose 
   ```

>[!NOTE]
> Une fois ce déploiement terminé, les futurs déploiements de ressources de cluster devront définir la propriété `vmInstanceCount` sur `-1` sur les types de nœuds secondaires pour lesquels les règles de mise à l’échelle automatique sont activées. Cela permettra de s’assurer que les déploiements de clusters n’entrent pas en conflit avec la mise à l’échelle automatique.


## <a name="enable-or-disable-autoscaling-on-a-secondary-node-type"></a>Activer ou désactiver la mise à l’échelle automatique sur un type de nœud secondaire

Les types de nœuds déployés par un cluster managé Service Fabric n’activent pas la mise à l’échelle automatique par défaut. La mise à l’échelle automatique peut être activée ou désactivée à tout moment, par type de nœud, ceux qui sont configurés et disponibles.

Pour activer cette fonctionnalité, configurez la propriété `enabled` sous le type `Microsoft.Insights/autoscaleSettings` dans un modèle ARM, comme indiqué ci-dessous :

```JSON
    "resources": [
            {
            "type": "Microsoft.Insights/autoscaleSettings",
            "apiVersion": "2015-04-01",
            "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
            "location": "[resourceGroup().location]",
            "properties": {
                "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
                "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'), '/nodetypes/', parameters('nodeType2Name'))]",
                "enabled": true,
            ...
```

Pour désactiver la mise à l’échelle automatique, définissez la valeur sur `false`.

## <a name="delete-autoscaling-rules"></a>Supprimer des règles de mise à l’échelle automatique

Pour supprimer une configuration de stratégies de mise à l’échelle automatique pour un type de nœud, vous pouvez exécuter la commande PowerShell suivante.

```PowerShell
Remove-AzResource -ResourceId "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/microsoft.insights/autoscalesettings/$name" -Force
```

## <a name="set-policies-for-autoscaling"></a>Définir des stratégies pour la mise à l’échelle automatique

 Par défaut, un cluster managé Service Fabric ne configure pas de [stratégie pour la mise à l’échelle automatique](../azure-monitor/autoscale/autoscale-understanding-settings.md). Les stratégies de mise à l’échelle automatique doivent être configurées pour que toute action de mise à l’échelle se produise sur les ressources sous-jacentes.

L’exemple suivant configure une stratégie pour que `nodeType2Name` comporte au moins 3 nœuds, mais autorise un scale-up jusqu’à 20 nœuds. Elle déclenchera un scale-up lorsque l’utilisation moyenne de l’UC sera de 70 % au cours des 30 dernières minutes, avec une granularité de 1 minute. Elle déclenchera un scale-down lorsque l’utilisation moyenne de l’UC sera inférieure à 40 % au cours des 30 dernières minutes, avec une granularité de 1 minute.

```JSON
    "resources": [
            {
            "type": "Microsoft.Insights/autoscaleSettings",
            "apiVersion": "2015-04-01",
            "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
            "location": "[resourceGroup().location]",
            "properties": {
                "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
                "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'), '/nodetypes/', parameters('nodeType2Name'))]",
                "enabled": "[parameters('enableAutoScale')]",
                "profiles": [
                    {
                        "name": "Autoscale by percentage based on CPU usage",
                        "capacity": {
                            "minimum": "3",
                            "maximum": "20",
                            "default": "3"
                        },
                        "rules": [
                            {
                                "metricTrigger": {
                                  "metricName": "Percentage CPU",
                                  "metricNamespace": "",
                                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/SFC_', reference(resourceId('Microsoft.ServiceFabric/managedClusters', parameters('clusterName')), '2021-07-01-preview').clusterId,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('nodeType2Name'))]",
                                  "timeGrain": "PT1M",
                                  "statistic": "Average",
                                  "timeWindow": "PT30M",
                                  "timeAggregation": "Average",
                                  "operator": "GreaterThan",
                                  "threshold": 70
                                },
                                "scaleAction": {
                                  "direction": "Increase",
                                  "type": "ChangeCount",
                                  "value": "5",
                                  "cooldown": "PT5M"
                                }
                            },
                            {
                                "metricTrigger": {
                                  "metricName": "Percentage CPU",
                                  "metricNamespace": "",
                                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/SFC_', reference(resourceId('Microsoft.ServiceFabric/managedClusters', parameters('clusterName')), '2021-07-01-preview').clusterId,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('nodeType2Name'))]",
                                  "timeGrain": "PT1M",
                                  "statistic": "Average",
                                  "timeWindow": "PT30M",
                                  "timeAggregation": "Average",
                                  "operator": "LessThan",
                                  "threshold": 40
                                },
                                "scaleAction": {
                                  "direction": "Decrease",
                                  "type": "ChangeCount",
                                  "value": "1",
                                  "cooldown": "PT5M"
                                }
                            }
                            ]
                    }
                    ]
                }
            }
        
    ]                           
```

Vous pouvez télécharger ce [modèle ARM d’activation de la mise à l’échelle automatique](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/sfmc-deploy-autoscale.json) qui contient l’exemple ci-dessus.


## <a name="view-configured-autoscale-definitions-of-your-managed-cluster-resource"></a>Afficher les définitions de mise à l’échelle automatique configurées de votre ressource de cluster managé

Vous pouvez afficher les paramètres de mise à l’échelle automatique configurés à l’aide d’[Azure Resource Explorer](https://resources.azure.com/).

1) Allez dans [Azure Resource Explorer](https://resources.azure.com/).

2) Accédez à `subscriptions` -> `SubscriptionName` -> `resource group` -> `microsoft.insights` -> `autoscalesettings` -> Nome de la stratégie de mise à l’échelle automatique, p. ex : `sfmc01-NT2`. 

   Vous verrez un résultat similaire à celui-ci dans l’arborescence de navigation :

   ![Exemple d’arborescence dans Azure Resource Explorer][autoscale-are-tree]


3) Dans la partie droite, vous pouvez afficher la définition complète de ce paramètre de mise à l’échelle. 

   Dans cet exemple, la mise à l’échelle automatique est configurée avec une règle de scale-out et de scale-in basée sur le pourcentage de l’UC.

   ![Exemple de détails de la mise à l’échelle automatique d’un type de nœud dans Azure Resource Explorer][autoscale-nt-details]



## <a name="troubleshooting"></a>Dépannage

Voici quelques points importants à prendre en compte : 

* Examinez les événements de mise à l’échelle automatique déclenchés par rapport aux types de nœuds secondaires de clusters managés.

   1) Accédez au journal d’activité du cluster.
   2) Vérifiez le journal d’activité pour l’opération Scale-up/scale-down de mise à l’échelle automatique terminé.

* Combien de machines virtuelles sont configurées pour le type de nœud et la charge de travail se produit-elle sur toutes ces machines ou seulement sur certaines d’entre elles ?

* Les seuils d’augmentation et de diminution de la taille des instances sont-ils suffisamment différents ?

   Supposons que vous définissez une règle pour effectuer un scale-out lorsque l’utilisation moyenne du processeur est supérieure à 50 % pendant cinq minutes et une règle pour effectuer un scale-in lorsque l’utilisation moyenne du processeur est inférieure à 50 %. Ce paramètre entraîne un problème d’« oscillation » lorsque l’utilisation du processeur est proche de ce seuil, avec des actions de mise à l’échelle entraînant constamment l’augmentation et la diminution de la taille du groupe. De ce fait, le service de mise à l’échelle automatique tente d’empêcher l’« oscillation », ce qui peut se manifester par une absence de mise à l’échelle. Par conséquent, assurez-vous que les seuils d’augmentation et de diminution de la taille des instances sont suffisamment différents pour laisser une marge lors de la mise à l’échelle.

* Pouvez-vous effectuer un scale-in ou un scale-out sur un type de nœud ?
   Ajustez le nombre de nœuds au niveau du type de nœud et assurez-vous que l’opération se déroule correctement. [Comment mettre à l’échelle un type de nœud sur un cluster managé](./how-to-managed-cluster-modify-node-type.md#scale-a-node-type-manually-with-a-template).

* Vérifiez vos ressources Microsoft.ServiceFabric/managedclusters/nodetypes et Microsoft.Insights dans Azure Resource Explorer.

   Azure Resource Explorer est un outil de dépannage indispensable qui vous indique l’état de vos ressources Azure Resource Manager. Cliquez sur votre abonnement et examinez le groupe de ressources sur lequel vous effectuez un dépannage. Sous le fournisseur de ressources `ServiceFabric/managedclusters/clustername`, regardez sous `NodeTypes` pour les types de nœud que vous avez créés et vérifiez que les propriétés visant à valider `provisioningState` sont à l’état `Succeeded`. Ensuite, accédez au fournisseur de ressources Microsoft.Insights sous `clustername` et vérifiez que les règles de mise à l’échelle automatique sont correctes. 

* Les valeurs de métriques émises sont-elles conformes aux attentes ?
   Utilisez le [module PowerShell `Get-AzMetric` pour obtenir les valeurs de métriques d’une ressource](/powershell/module/az.monitor/get-azmetric) et les examiner.


Une fois que vous avez effectué ces étapes, si vous rencontrez toujours des problèmes de mise à l’échelle automatique, vous pouvez essayer les ressources suivantes : [Consigner une demande de support](./service-fabric-support.md#create-an-azure-support-request). Soyez prêt à partager le modèle et une vue de vos données de performance.


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [En savoir plus sur la prise en charge de la mise à l’échelle automatique d’Azure Monitor](../azure-monitor/autoscale/autoscale-overview.md)
> [!div class="nextstepaction"]
> [Examiner des métriques dans Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)
> [!div class="nextstepaction"]
> [Options de configuration du cluster Service Fabric géré](how-to-managed-cluster-configuration.md)

[autoscale-are-tree]: ./media/how-to-managed-cluster-autoscale/autoscale-are-tree.png
[autoscale-nt-details]: ./media/how-to-managed-cluster-autoscale/autoscale-nt-details.png


