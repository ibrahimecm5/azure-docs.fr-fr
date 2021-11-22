---
title: Configurer ou modifier un type de nœud de cluster managé Service Fabric
description: Cet article explique comment modifier un type de nœud de cluster managé.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 02d332774b98e7097bca0bbea6bc7216af0057fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277255"
---
# <a name="service-fabric-managed-cluster-node-types"></a>Types de nœuds de cluster managés Service Fabric

Chaque type de nœud d’un cluster managé Service Fabric est adossé à un groupe de machines virtuelles identiques. Avec les clusters managés, vous apportez toutes les modifications requises par le biais du fournisseur de ressources de cluster managé Service Fabric. Toutes les ressources sous-jacentes pour le cluster sont créées et abstraites par le fournisseur de cluster managé en votre nom. Le fait que le fournisseur de ressources gère les ressources permet de simplifier le déploiement et la gestion du type de nœud de cluster, d’éviter des erreurs d’opération telles que la suppression d’un nœud de départ, et l’application de meilleures pratiques, telles que la validation d’une référence SKU de machine virtuelle, peut être utilisée en toute sécurité.

Le reste de ce document explique comment ajuster différents paramètres lors de la création d’un type de nœud, de l’ajustement du nombre d’instances du type de nœud, de l’activation des mises à niveau automatiques de l’image du système d’exploitation, de la modification de l’image du système d’exploitation et de la configuration des propriétés de sélection élective. Ce document se concentre également sur l’utilisation du Portail Azure ou de modèles Azure Resource Manager pour apporter des modifications.

> [!NOTE]
> Vous n’êtes pas en mesure de modifier le type de nœud lorsqu’une modification est en cours. Il est recommandé de laisser toutes les modifications demandées se terminer avant d’en apporter d’autres.


## <a name="add-or-remove-a-node-type-with-portal"></a>Ajouter ou supprimer un type de nœud avec le portail

Dans cette procédure pas à pas, vous allez apprendre à ajouter ou supprimer un type de nœud à l’aide du portail.

**Pour ajouter un type de nœud :**
1) Connectez-vous au [portail Azure](https://portal.azure.com/).

2) Accédez à la page Vue d’ensemble de votre ressource de cluster. 
![Exemple de page Vue d’ensemble][overview]

3) Sélectionnez `Node types` sous l’`Settings` ![Affichage Types de nœuds][addremove].

4) Cliquez sur `Add` en haut, renseignez les informations requises, puis cliquez sur Ajouter en bas et c’est tout !


**Pour supprimer un type de nœud :**
1) Connectez-vous au [portail Azure](https://portal.azure.com/).

2) Accédez à la page Vue d’ensemble de votre ressource de cluster. 
![Exemple de page Vue d’ensemble][overview]

3) Sélectionnez `Node types` sous l’`Settings` ![Affichage Types de nœuds][addremove].

4) Sélectionnez le `Node Type` que vous souhaitez supprimer et cliquez sur `Delete` en haut.

> [!NOTE]
> Il n’est pas possible de supprimer un type de nœud principal s’il s’agit du seul type de nœud principal du cluster.


## <a name="add-a-node-type-with-a-template"></a>Ajouter un type de nœud à l’aide d’un modèle

**Pour ajouter un type de nœud à l’aide d’un modèle ARM**

Ajoutez un autre type de ressource `Microsoft.ServiceFabric/managedclusters/nodetypes` avec les valeurs requises et effectuez un déploiement de cluster pour que le paramètre prenne effet.

* L’apiVersion de la ressource de cluster managé Service Fabric doit être **2021-05-01** ou ultérieure.

```json
          {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeType2Name'))]",
            "location": "[resourcegroup().location]",
            "dependsOn": [
              "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
            ],
            "properties": {
                "isPrimary": false,
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                "vmSize": "[parameters('nodeType2VmSize')]",
                "vmInstanceCount": "[parameters('nodeType2VmInstanceCount')]",
                "dataDiskSizeGB": "[parameters('nodeType2DataDiskSizeGB')]",
                "dataDiskType": "[parameters('nodeType2managedDataDiskType')]"
           }
```
Pour obtenir un exemple de configuration à deux types de nœuds, consultez notre [exemple de modèle ARM à deux types de nœuds](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/SF-Managed-Standard-SKU-2-NT).


## <a name="scale-a-node-type-manually-with-portal"></a>Mettre à l’échelle manuellement un type de nœud avec le portail

Dans cette procédure pas à pas, vous allez apprendre à modifier le nombre de nœuds pour un type de nœud à l’aide du portail.

1) Connectez-vous au [portail Azure](https://portal.azure.com/).

2) Accédez à la page Vue d’ensemble de votre ressource de cluster. 
![Exemple de page Vue d’ensemble][overview]

3) Dans la section `Node Types`, sous la section `Settings` 

4) Sélectionnez le `Node type name` que vous souhaitez modifier.

5) Ajustez le `Node count` à la nouvelle valeur voulue et sélectionnez `Apply` en bas. Dans cette capture d’écran, la valeur était `3` et a été ajustée sur `5`.
![Exemple d’augmentation du nombre de nœuds][adjust-node-count]

6) Le `Provisioning state` affiche à présent l’état `Updating` jusqu’à ce que l’achèvement. À l’achèvement, il affiche `Succeeded` à nouveau.
![Exemple de mise à jour d’un type de nœud][node-type-updating]


## <a name="scale-a-node-type-manually-with-a-template"></a>Mettre à l’échelle manuellement un type de nœud avec un modèle

Pour ajuster le nombre de nœuds pour un type de nœud à l’aide d’un modèle ARM, ajustez la propriété `vmInstanceCount` avec la nouvelle valeur et effectuez un déploiement de cluster pour que le paramètre prenne effet.

* L’apiVersion de la ressource de cluster managé Service Fabric doit être **2021-05-01** ou ultérieure.

> [!NOTE]
> Le fournisseur de cluster managé bloquera les ajustements de mise à l’échelle et renverra une erreur si la demande de mise à l’échelle ne respecte pas les conditions minimales requises.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                ...
                "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
                ...
            }
        }
}
```

## <a name="enable-automatic-os-image-upgrades"></a>Activer les mises à niveau de l’image du système d’exploitation

Vous pouvez choisir d’activer les mises à niveau automatiques de l’image du système d’exploitation vers les machines virtuelles exécutant vos nœuds de cluster managés. Bien que les ressources du groupe de machines virtuelles identiques soient managées en votre nom avec des clusters managés Service Fabric, vous avez le choix d’activer les mises à niveau automatiques de l’image du système d’exploitation pour vos nœuds de cluster. Comme avec les clusters [Service Fabric classiques](service-fabric-best-practices-infrastructure-as-code.md#virtual-machine-os-automatic-upgrade-configuration), les nœuds de cluster managés ne sont pas mis à niveau par défaut, afin d’éviter toute interruption involontaire de votre cluster.

Pour activer les mises à niveau automatiques du système d’exploitation :

* Utilisez la version `2021-05-01` (ou ultérieure) des ressources *Microsoft.ServiceFabric/managedclusters* et *Microsoft.ServiceFabric/managedclusters/nodetypes*
* Définir la propriété `enableAutoOSUpgrade` du cluster sur *true*
* Définir la propriété `vmImageVersion` de la ressource nodeTypes du cluster sur *latest*

Par exemple :

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Une fois activé, Service Fabric commence l’interrogation et le suivi des versions d’image du système d’exploitation dans le cluster managé. Si une nouvelle version du système d’exploitation est disponible, les types de nœuds de cluster (groupes de machines virtuelles identiques) sont mis à niveau successivement. Les mises à niveau du runtime Service Fabric sont effectuées uniquement après la confirmation qu’aucune mise à niveau d’une image de système d’exploitation de nœud de cluster n’est en cours.

En cas d’échec d’une mise à niveau, Service Fabric effectue une nouvelle tentative après 24 heures, pour un maximum de trois tentatives. Comme lors des mises à niveau de Service Fabric classiques (non managées), les applications ou les nœuds non sains peuvent bloquer la mise à niveau de l’image du système d’exploitation.

Pour plus d’informations sur les mises à niveau d’images, consultez [Mises à niveau automatiques d’images de système d’exploitation avec des groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="modify-the-os-image-for-a-node-type-with-portal"></a>Modifier l’image du système d’exploitation pour un type de nœud avec le portail

Dans cette procédure pas à pas, vous allez apprendre à modifier l’image de système d’exploitation pour un type de nœud à l’aide du portail.

1) Connectez-vous au [portail Azure](https://portal.azure.com/).

2) Accédez à la page Vue d’ensemble de votre ressource de cluster. 
![Exemple de page Vue d’ensemble][overview]

3) Dans la section `Node Types`, sous la section `Settings` 

4) Sélectionnez le `Node type name` que vous souhaitez modifier.

5) Ajustez le `OS Image` à la nouvelle valeur voulue et sélectionnez `Apply` en bas. ![Exemple qui illustre la modification de l’image du système d’exploitation] [change-os-image]

6) L’`Provisioning state` affiche alors l’état `Updating` et poursuit un domaine de mise à niveau à la fois. À l’achèvement, il affiche `Succeeded` à nouveau.
![Exemple de mise à jour d’un type de nœud][node-type-updating]


## <a name="modify-the-os-image-for-a-node-type-with-a-template"></a>Modifier l’image du système d’exploitation pour un type de nœud avec un modèle

Pour modifier l’image de système d’exploitation pour un type de nœud à l’aide d’un modèle ARM, ajustez la propriété `vmImageSku` avec la nouvelle valeur et effectuez un déploiement de cluster pour que le paramètre prenne effet. Le fournisseur de cluster managé réimagera chaque instance par domaine de mise à niveau.

* L’apiVersion de la ressource de cluster managé Service Fabric doit être **2021-05-01** ou ultérieure.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                ...
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                ...
            }
        }
}
```

## <a name="configure-placement-properties-for-a-node-type-with-portal"></a>Configurer les propriétés de sélection élective d’un type de nœud avec le portail

Dans cette procédure pas à pas, vous allez apprendre à modifier la propriété de sélection élective pour un type de nœud à l’aide du portail.

1) Connectez-vous au [portail Azure](https://portal.azure.com/).

2) Accédez à la page Vue d’ensemble de votre ressource de cluster. 
![Exemple de page Vue d’ensemble][overview]

3) Dans la section `Node Types`, sous la section `Settings` 

4) Sélectionnez le `Node type name` que vous souhaitez modifier.

5) Dans la section `Placement properties`, ajoutez le nom et la valeur souhaités, puis sélectionnez `Apply` en bas. Dans cette capture d’écran, le `Name` `SSD_Premium` a été utilisé avec `Value` défini sur `true`.
![Exemple qui montre comment ajouter une propriété de sélection élective][nodetype-placement-property]

6) Le `Provisioning state` affiche à présent l’état `Updating` jusqu’à ce que l’achèvement. À l’achèvement, il affiche `Succeeded` à nouveau.
![Exemple de mise à jour d’un type de nœud][node-type-updating]

Vous pouvez maintenant utiliser cette [propriété de sélection élective pour vous assurer que certaines charges de travail s’exécutent uniquement sur certains types de nœuds du cluster](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). 

## <a name="configure-placement-properties-for-a-node-type-with-a-template"></a>Configurer les propriétés de sélection élective d’un type de nœud avec un modèle

Pour ajuster les propriétés de sélection élective pour un type de nœud à l’aide d’un modèle ARM, ajustez la propriété `placementProperties` avec la nouvelle valeur et effectuez un déploiement de cluster pour que le paramètre prenne effet. L’exemple ci-dessous montre trois valeurs qui sont définies pour un type de nœud.

* L’apiVersion de la ressource de cluster managé Service Fabric doit être **2021-05-01** ou ultérieure.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "placementProperties": {
                    "PremiumSSD": "true",
                    "NodeColor": "green",
                    "SomeProperty": "5"
            }
        }
}
```
Vous pouvez maintenant utiliser cette [propriété de sélection élective pour vous assurer que certaines charges de travail s’exécutent uniquement sur certains types de nœuds du cluster](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). 

## <a name="modify-the-vm-sku-for-a-node-type"></a>Modifier la référence SKU de machine virtuelle pour un type de nœud

Le cluster managé Service Fabric ne prend pas en charge la modification sur place de la référence SKU de machine virtuelle, mais il est simple puis classique. Pour ce faire, vous devez procéder comme suit :
* [Créez un nouveau type de nœud](how-to-managed-cluster-modify-node-type.md#add-or-remove-a-node-type-with-portal) avec la référence SKU de machine virtuelle requise.
* Migrez votre charge de travail. Vous pouvez utiliser cette [propriété de sélection élective pour vous assurer que certaines charges de travail s’exécutent uniquement sur certains types de nœuds du cluster](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). 
* [Supprimer l’ancien type de nœud](how-to-managed-cluster-modify-node-type.md#add-or-remove-a-node-type-with-portal)



## <a name="configure-multiple-managed-disks-preview"></a>Configurer plusieurs disques managés (version préliminaire)
Les clusters managés Service Fabric par défaut configurent un disque managé. En configurant la propriété et les valeurs facultatives suivantes, vous pouvez ajouter des disques managés supplémentaires aux types de nœuds au sein d’un cluster. Vous pouvez spécifier la lettre de lecteur, le type de disque et la taille par disque.

Configurez des disques managés supplémentaires en déclarant la propriété `additionalDataDisks` et les paramètres requis dans votre modèle Resource Manager comme suit :

**Exigences concernant les fonctionnalités**
* Le numéro d’unité logique doit être unique par disque et ne peut pas utiliser le numéro d’unité logique réservé 0
* La lettre de disque ne peut pas utiliser les lettres réservées C ou D et ne peut pas être modifiée une fois créée. S sera utilisé comme valeur par défaut s’il n’est pas spécifié.
* Vous devez spécifier un [type de disque pris en charge](how-to-managed-cluster-managed-disk.md)
* La valeur apiVersion de la ressource de cluster managé Service Fabric doit être **2021-11-01-preview** ou ultérieure.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "additionalDataDisks": {
                    "lun": "1",
                    "diskSizeGB": "50",
                    "diskType": "Standard_LRS",
                    "diskLetter": "S" 
            }
        }
     }
```

Voir la [liste complète des paramètres disponibles](/azure/templates/microsoft.servicefabric/2021-11-01/managedclusters)

## <a name="configure-the-service-fabric-data-disk-drive-letter-preview"></a>Configurer la lettre de lecteur de disque de données Service Fabric (version préliminaire)
Les clusters managés Service Fabric par défaut configurent un disque de données Service Fabric et configurent automatiquement la lettre de lecteur sur tous les nœuds d’un type de nœud. Si vous configurez cette propriété et cette valeur facultatives, vous pouvez spécifier et récupérer la lettre de disque de données Service Fabric si vous avez des exigences spécifiques pour le mappage des lettres de lecteur.

**Exigences concernant les fonctionnalités**
* La lettre de disque ne peut pas utiliser les lettres réservées C ou D et ne peut pas être modifiée une fois créée. S sera utilisé comme valeur par défaut s’il n’est pas spécifié.
* La valeur apiVersion de la ressource de cluster managé Service Fabric doit être **2021-11-01-preview** ou ultérieure.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "dataDiskLetter": "S"      
            }
        }
     }
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Mettre à l’échelle automatiquement un nœud de cluster Service Fabric managé](how-to-managed-cluster-autoscale.md)
> [!div class="nextstepaction"]
> [Options de configuration du cluster Service Fabric géré](how-to-managed-cluster-configuration.md)
> [!div class="nextstepaction"]
> [Déployer une application sur un cluster Service Fabric managé](./tutorial-managed-cluster-deploy-app.md)


[overview]: ./media/how-to-managed-cluster-modify-node-type/sfmc-overview.png
[node-type-updating]: ./media/how-to-managed-cluster-modify-node-type/sfmc-adjust-node-type-updating.png
[adjust-node-count]: ./media/how-to-managed-cluster-modify-node-type/sfmc-adjust-node-counts.png
[change-nodetype-os-image]: ./media/how-to-managed-cluster-modify-node-type/sfmc-change-os-image.png
[nodetype-placement-property]: ./media/how-to-managed-cluster-modify-node-type/sfmc-nodetype-placement-property.png
[addremove]: ./media/how-to-managed-cluster-modify-node-type/sfmc-addremove-node-type.png

