---
title: Déployer des types de nœuds Service Fabric avec des disques de données managées
description: Découvrez comment créer et déployer des types de nœuds Service Fabric avec des disques de données managées attachés
author: craftyhouse
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: micraft
ms.openlocfilehash: 17ec57ca04defa61080ebb5a67f879c737ea16fa
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460390"
---
# <a name="deploy-an-azure-service-fabric-cluster-node-type-with-managed-data-diskspreview"></a>Déployer un type de noeud de cluster Azure Service Fabric avec des disques de données managées (préversion)

>[!NOTE]
> La prise en charge des disques de données managées est actuellement en version préliminaire et ne doit pas être utilisée avec les charges de travail de production.


Les types de nœuds Service Fabric par défaut utilisent le disque temporaire sur chaque machine virtuelle dans le groupe de machines virtuelles identiques sous-jacent pour le stockage des données. Toutefois, étant donné que le disque temporaire n’est pas persistant et que la taille du disque temporaire est liée à une référence SKU de machine virtuelle donnée, celle-ci peut être trop restrictive pour certains scénarios. Avec les disques managés Azure, les clients disposent d’un disque de données persistant ; ils peuvent spécifier la taille et les performances qui seront utilisées pour un type de nœud, indépendamment d’une référence SKU de machine virtuelle. Le document suivant explique comment utiliser la prise en charge native de Service Fabric pour configurer et utiliser des disques managés Azure comme chemin d’accès aux données par défaut. Service Fabric configure automatiquement les disques managés Azure lors de la création d’un type de nœud et gère les situations où les machines virtuelles ou le groupe de machines virtuelles identiques sont réinitialisés.

* La taille de disque minimale requise pour le disque de données managé est de 50 Go.
* Dans les scénarios où plusieurs disques de données managés sont attachés, le client doit gérer les disques de données lui-même.

## <a name="configuring-virtual-machine-scale-set-to-use-managed-data-disks-in-service-fabric"></a>Configuration du groupe de machines virtuelles identiques pour utiliser des disques de données managés dans Service Fabric
Pour utiliser les disques managés Azure sur un type de nœud, configurez la ressource de groupe de machines virtuelles identiques sous-jacentes avec les éléments suivants :

* Ajoutez un disque managé dans la section des disques de données du modèle pour le groupe de machines virtuelles identiques. 
* Mettez à jour l’extension Service Fabric avec les paramètres suivants : 
    * Pour Windows : **useManagedDataDisk: true** et **dataPath: 'K:\\\\SvcFab'** .  Notez que la lettre de lecteur « K » est simplement une représentation. Il peut s’agir de n’importe quelle lettre de lecteur supérieure dans l’ordre lexicographique à la lettre de lecteur présente dans la référence SKU du groupe de machines virtuelles identiques.
    * Pour Linux : **useManagedDataDisk:true** et **dataPath: '\mnt\sfdataroot'** .

>[!NOTE]
> La prise en charge des disques de données managés pour les clusters Service Fabric Linux n’est pas disponible actuellement.

Modèle Azure Resource Manager d’extension pour Service Fabric
```json
{
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": false,
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
                            "dataPath": "K:\\\\SvcFab",
                            "useManagedDataDisk": true,
                            "durabilityLevel": "Bronze",
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            },
                            "systemLogUploadSettings": {
                                "Enabled": true
                            },
                        },
                        "typeHandlerVersion": "1.1"
                    }
                },
            ]
        },
        "storageProfile": 
        {
            "datadisks": [
                {
                    "lun": "1",
                    "createOption": "empty",
                    "diskSizeGB": "100",
                    "managedDisk": { "storageAccountType": "Standard_LRS" }
                }
            ]
        }
    }
}
```

## <a name="migrate-to-using-managed-data-disks-for-service-fabric-node-types"></a>Migrer vers des types de nœuds Service Fabric à l’aide de disques de données managés
* Pour tous les scénarios de migration, vous devez ajouter un nouveau type de nœud qui utilise des disques de données managés comme indiqué ci-dessus.
* Une fois les nouveaux types de nœud ajoutés, migrez les charges de travail vers les nouveaux types de nœuds.
* Une fois le déploiement des ressources terminé, vous pouvez commencer à désactiver les nœuds dans le type de nœud que vous souhaitez supprimer du cluster d’origine.

## <a name="next-steps"></a>Étapes suivantes 
* [Vue d’ensemble de Service Fabric](service-fabric-reliable-services-introduction.md)
* [Types de nœud et groupes de machines virtuelles identiques](service-fabric-cluster-nodetypes.md)
* [Planification de la capacité de Service Fabric](service-fabric-best-practices-capacity-scaling.md)
