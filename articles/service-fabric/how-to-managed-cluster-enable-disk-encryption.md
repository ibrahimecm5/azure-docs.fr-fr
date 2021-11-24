---
title: Activer le chiffrement de disque pour les nœuds de cluster managés Service Fabric
description: Découvrez comment activer le chiffrement de disque pour les nœuds de cluster managé dans Windows à l’aide d’un modèle ARM.
ms.topic: how-to
ms.date: 11/8/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d1d1db17d6914904d9995883903c50982de84daa
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486939"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-nodes"></a>Activer le chiffrement de disque pour les nœuds de cluster managé Service Fabric

Les clusters managés Service Fabric prennent en charge deux options de chiffrement de disque pour aider à protéger vos données afin de répondre aux engagements de votre organisation en matière de sécurité et de conformité. L’option recommandée est le chiffrement sur l’hôte, mais Azure Disk Encryption est également pris en charge. Passez en revue les [options de chiffrement de disque](../virtual-machines/disk-encryption-overview.md) et assurez-vous que l’option sélectionnée répond à vos besoins.


## <a name="enable-encryption-at-host-preview"></a>Activer le chiffrement au niveau de l’hôte (préversion)

Cette méthode de chiffrement constitue une amélioration par rapport à [Azure Disk Encryption](how-to-managed-cluster-enable-disk-encryption.md) en prenant en charge l’ensemble des types et des images de systèmes d’exploitation, notamment les images personnalisées, pour vos machines virtuelles en chiffrant des données dans le service Stockage Azure. Cette méthode n’utilise pas le processeur de vos machines virtuelles ni n’affecte les performances de vos machines virtuelles, ce qui permet aux charges de travail d’utiliser toutes les ressources SKU disponibles des machines virtuelles.

> [!Note]
> Vous ne pouvez pas l’activer sur les types de nœuds existants. Vous devez provisionner un nouveau type de nœud et migrer votre charge de travail.

> [!Note]
> L’état de chiffrement de disque d’Azure Security Center s’affiche comme étant non sain pour l’instant lors de l’utilisation du chiffrement sur l’hôte

Suivez ces étapes et référencez cet [exemple de modèle](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-HostEncryption) pour déployer un nouveau cluster managé Service Fabric avec le chiffrement de l’hôte activé.

1. Passez en revue les [restrictions](../virtual-machines/windows/disks-enable-host-based-encryption-powershell.md#restrictions) suivantes pour confirmer qu’elles correspondent à vos exigences.

2. Configurez les [prérequis](../virtual-machines/windows/disks-enable-host-based-encryption-powershell.md#prerequisites) nécessaires avant le déploiement du cluster.

3. Configurez la propriété `enableEncryptionAtHost` dans le modèle de cluster managé pour chaque type de nœud. Le chiffrement de disque est obligatoire. L’exemple est préconfiguré.

   * La valeur apiVersion de la ressource de cluster managé Service Fabric doit être **2021-11-01-preview** ou ultérieure.

   ```json
        {
               "apiVersion": "[variables('sfApiVersion')]",
               "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
               "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
               "location": "[resourcegroup().location]",
               "properties": {
                   "enableEncryptionAtHost": true
                   ...
               }
       }
   ```

4. Déployer et vérifier

   Déployez votre cluster managé configuré avec le chiffrement de l’hôte activé.

   ```powershell
   $clusterName = "<clustername>" 
   $resourceGroupName = "<rg-name>"

   New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug -Verbose 
   ```

   Vous pouvez vérifier l’état de chiffrement du disque sur un groupe identique sous-jacent du type de nœud au moyen de la commande `Get-AzVmss`. Vous devez tout d’abord rechercher le nom du groupe de ressources de prise en charge de votre cluster managé (contenant le réseau virtuel sous-jacent, l’équilibreur de charge, l’adresse IP publique, le groupe de sécurité réseau, le ou les groupes identiques et les comptes de stockage). Veillez à modifier `NodeTypeNAme` pour le nom du type de nœud de cluster que vous souhaitez vérifier (comme spécifié dans votre modèle de déploiement).

   ```powershell
   $NodeTypeName = "NT2"
   $clustername = <clustername>
   $resourceGroupName = "<rg-name>"
   $supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName -Name $clustername).ClusterId
   $VMSS = Get-AzVmss -ResourceGroupName $supportResourceGroupName -Name $NodeTypeName
   $VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
   ```

   La sortie de retour doit ressembler à ceci :

   ```console
   $VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
   True
   ```

## <a name="enable-azure-disk-encryption"></a>Activer Azure Disk Encryption
Azure Disk Encryption fournit un chiffrement de volume pour les disques de système d’exploitation et de données des machines virtuelles Azure en utilisant la fonctionnalité DM-Crypt de Linux ou la fonctionnalité BitLocker de Windows. ADE est intégré à Azure Key Vault pour faciliter le contrôle et la gestion des clés et des secrets de chiffrement des disques.

Dans ce guide, vous allez apprendre à activer le chiffrement de disque sur les nœuds de cluster managé Service Fabric dans Windows, à l’aide de la fonctionnalité [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) pour les [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) avec des modèles Azure Resource Manager (ARM).

1. S’inscrire auprès d’Azure Disk Encryption

   La préversion du chiffrement de disque des groupes de machines virtuelles identiques nécessite une inscription automatique. Exécutez la commande suivante :

   ```powershell
   Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
   ```

   Vérifiez l’état de l’inscription en exécutant :

   ```powershell
   Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
   ```

   Lorsque l’état passe à *Enregistré*, vous êtes prêt à continuer.

2. Provisionner un coffre de clés pour le chiffrement de disque

   Azure Disk Encryption exige Azure Key Vault pour contrôler et gérer les clés et les secrets de chiffrement de disque. Votre coffre de clés et le cluster managé Service Fabric doivent résider dans le même abonnement et la même région Azure. Tant que ces exigences sont respectées, vous pouvez utiliser un coffre de clés nouveau ou existant en l’activant pour le chiffrement de disque.

3. Créer un coffre de clés avec le chiffrement de disque activé

   Exécutez les commandes suivantes qui permettent de créer un coffre de clés pour le chiffrement de disque. Assurez-vous que la région de votre Key Vault se trouve dans la même région que votre cluster.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   $resourceGroupName = "<rg-name>" 
   $keyvaultName = "<kv-name>" 

   New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
   New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli
   $resourceGroupName = "<rg-name>" 
   $keyvaultName = "<kv-name>" 

   az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
   ```

   ---

4. Mettre à jour un coffre de clés existant pour activer le chiffrement de disque

   Exécutez les commandes permettant d’activer le chiffrement de disque pour un coffre de clés existant.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli
   az keyvault update --name keyvaultName --enabled-for-disk-encryption 
   ```

   ---

### <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Mettre à jour le fichier de paramètres et le modèle pour le chiffrement de disque

L’étape suivante vous guide tout au long des modifications nécessaires du modèle pour activer le chiffrement de disque sur un [cluster managé existant](tutorial-managed-cluster-deploy.md). Vous pouvez aussi déployer un nouveau cluster managé Service Fabric avec le chiffrement de disque activé, à l’aide de ce modèle : https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Ajoutez les paramètres suivants au modèle, en substituant votre propre abonnement, nom de groupe de ressources et nom de coffre de clés sous `keyVaultResourceId` :

   ```json
   "parameters": {
    "keyVaultResourceId": { 
      "type": "string", 
      "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
      "metadata": { 
      "description": "Full resource id of the Key Vault used for disk encryption." 
   } 
    },
    "volumeType": { 
     "type": "string", 
     "defaultValue": "All", 
     "metadata": { 
      "description": "Type of the volume OS or Data to perform encryption operation" 
   }
   }
   }, 
   ```

2. Ensuite, ajoutez l’extension de machine virtuelle `AzureDiskEncryption` aux types de nœuds du cluster managé dans le modèle :

   ```json
   "properties": { 
   "vmExtensions": [ 
   { 
   "name": "AzureDiskEncryption", 
   "properties": { 
     "publisher": "Microsoft.Azure.Security", 
     "type": "AzureDiskEncryption", 
     "typeHandlerVersion": "2.2", 
     "autoUpgradeMinorVersion": true, 
     "settings": {      
           "EncryptionOperation": "EnableEncryption", 
           "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
        "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
        "VolumeType": "[parameters('volumeType')]" 
        } 
      } 
   } 
   ] 
   } 
   ```

3. Enfin, mettez à jour le fichier des paramètres, en substituant votre propre abonnement, groupe de ressources et nom de coffre de clés dans *keyVaultResourceId* :

   ```json
   "parameters": { 
   ...
    "keyVaultResourceId": { 
     "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
     "value": "All" 
    }    
   } 
   ```

4. Déployer et vérifier les modifications

   Lorsque vous êtes prêt, déployez les modifications pour activer le chiffrement de disque sur votre cluster managé.

   ```powershell
   $clusterName = "<clustername>" 

   New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug -Verbose 
   ```

   Vous pouvez vérifier l’état de chiffrement du disque sur un groupe identique sous-jacent du type de nœud au moyen de la commande `Get-AzVmssDiskEncryption`. Vous devez tout d’abord rechercher le nom du groupe de ressources de prise en charge de votre cluster managé (contenant le réseau virtuel sous-jacent, l’équilibreur de charge, l’adresse IP publique, le groupe de sécurité réseau, le ou les groupes identiques et les comptes de stockage). Veillez à modifier `VmssName` pour le nom du type de nœud de cluster que vous souhaitez vérifier (comme spécifié dans votre modèle de déploiement).

   ```powershell
   $VmssName = "NT1"
   $clustername = <clustername>
   $supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName -Name $clustername).ClusterId
   Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
   ```

   Le résultat doit ressembler à ceci :

   ```console
   ResourceGroupName            : SFC_########-####-####-####-############
   VmScaleSetName               : NT1
   EncryptionEnabled            : True
   EncryptionExtensionInstalled : True
   ```

## <a name="next-steps"></a>Étapes suivantes

[Exemple : cluster managé Service Fabric de référence SKU Standard, un type de nœud avec chiffrement de disque activé](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Azure Disk Encryption pour les machines virtuelles Windows](../virtual-machines/windows/disk-encryption-overview.md)

[Chiffrer des groupes de machines virtuelles identiques avec Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)