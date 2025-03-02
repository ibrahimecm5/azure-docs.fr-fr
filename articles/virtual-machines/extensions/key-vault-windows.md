---
title: Extension de machine virtuelle Azure Key Vault pour Windows
description: Déployez un agent réalisant une actualisation automatique des secrets Key Vault sur des machines virtuelles à l’aide d’une extension de machine virtuelle.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d2fe9cecae13cdd6ff82256466ff1fa045b73189
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128569311"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Extension de machine virtuelle Key Vault pour Windows

L’extension de machine virtuelle Key Vault assure l’actualisation automatique des certificats stockés dans un coffre de clés Azure. Plus précisément, l’extension surveille une liste de certificats examinés qui sont stockés dans des coffres de clés et, lorsqu’une modification est détectée, récupère et installe les certificats correspondants. Ce document présente les plateformes, configurations et options de déploiement qui sont prises en charge pour l’extension de machine virtuelle Key Vault pour Windows. 

### <a name="operating-system"></a>Système d’exploitation

L’extension de machine virtuelle Key Vault prend en charge les versions ci-dessous de Windows :

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

L’extension de machine virtuelle Key Vault est également prise en charge sur les machines virtuelles locales personnalisées qui sont téléchargées et converties en une image spécialisée pour une utilisation dans Azure à l’aide de l’installation de Windows Server 2019 Core.

### <a name="supported-certificate-content-types"></a>Types de contenu de certificat pris en charge

- PKCS#12
- PEM

## <a name="prerequisites"></a>Prérequis

  - Instance Key Vault avec un certificat. Consultez [Créer un Key Vault](../../key-vault/general/quick-create-portal.md)
  - Une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) doit être attribuée à la machine virtuelle
  - La stratégie d’accès à Key Vault doit être définie avec des secrets `get` et l’autorisation `list` pour l’identité managée de machine virtuelle/VMSS afin de récupérer la partie du secret d’un certificat. Consultez [Comment s’authentifier auprès de Key Vault](../../key-vault/general/authentication.md) et [Attribuer une stratégie d’accès Key Vault](../../key-vault/general/assign-access-policy-cli.md).
  -  Les groupes de machines virtuelles identiques doivent avoir le paramètre d’identité suivant :

  ``` 
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "[parameters('userAssignedIdentityResourceId')]": {}
    }
  }
  ```
  
  - L’extension AKV doit avoir ce paramètre : 

  ```
  "authenticationSettings": {
    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
  }
  ```

## <a name="extension-schema"></a>Schéma d’extensions

L’extrait JSON suivant illustre le schéma de l’extension de machine virtuelle Key Vault. L’extension ne nécessite pas de paramètres protégés, car tous ses paramètres sont considérés comme des informations publiques. L’extension requiert une liste de certificats surveillés, une fréquence d’interrogation et le magasin de certificats de destination. Plus précisément :  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <string specifying polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> Vos URL de certificats observés doivent être de la forme `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Cela est dû au fait que le chemin `/secrets` retourne le certificat complet, y compris la clé privée, contrairement au chemin `/certificates`. Vous trouverez plus d’informations sur les certificats ici : [Certificats Key Vault](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> La propriété 'authenticationSettings' est **requise** uniquement pour les machines virtuelles avec des **identités affectées par l’utilisateur**.
> Elle spécifie l'identité à utiliser pour l'authentification auprès de Key Vault.


### <a name="property-values"></a>Valeurs de propriétés

| Nom | Valeur/Exemple | Type de données |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | Date |
| publisher | Microsoft.Azure.KeyVault | string |
| type | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | MY | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine ou CurrentUser (respect de la casse) | string |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | tableau de chaînes
| msiEndpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Les modèles sont idéaux lorsque vous déployez une ou plusieurs machines virtuelles nécessitant une actualisation de certificats après le déploiement. Vous pouvez déployer l’extension sur des machines virtuelles individuelles ou dans des groupes de machines virtuelles identiques. La configuration et le schéma sont communs aux deux types de modèle. 

La configuration JSON d’une extension de machine virtuelle doit être imbriquée dans le fragment de la ressource de machine virtuelle du modèle, plus précisément dans l’objet `"resources": []` de la machine virtuelle et, dans le cas d’un groupe de machines virtuelles identiques, sous l’objet `"virtualMachineProfile":"extensionProfile":{"extensions" :[]`.

 > [!NOTE]
> L’extension de machine virtuelle nécessite l’attribution d’une identité managée par le système ou l’utilisateur pour s’authentifier auprès du coffre de clés.  Consultez [Comment s’authentifier auprès de Key Vault et attribuer une stratégie d’accès Key Vault.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <string specifying polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>Tri des dépendances d’extension
L’extension de machine virtuelle Key Vault prend en charge le tri des extensions si elle est configurée. Par défaut, l’extension signale qu’elle a démarré correctement dès qu’elle a démarré l’interrogation. Toutefois, elle peut être configurée de façon à attendre d’avoir téléchargé la liste complète des certificats avant de signaler un démarrage réussi. Si d’autres extensions dépendent de l’installation du jeu complet de certificats avant de démarrer, alors l’activation de ce paramètre permettra à ces extensions de déclarer une dépendance vis-à-vis de l’extension Key Vault. Cela empêchera ces extensions de démarrer avant que tous les certificats dont elles dépendent aient été installés. L’extension retentera le téléchargement initial indéfiniment et restera à l’état `Transitioning`.

Pour activer cette fonction, définissez ce qui suit :
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```

> [!Note] 
> L’utilisation de cette fonctionnalité n’est pas compatible avec un modèle ARM qui crée une identité affectée par le système et met à jour une stratégie d’accès Key Vault avec celle-ci. Cela conduit à une impasse, car la stratégie d’accès du coffre ne peut pas être mise à jour tant que toutes les extensions n’ont pas démarré. Vous devez utiliser à la place une *identité MSI unique affectée par l’utilisateur* et inscrire vos coffres au préalable sur une liste de contrôle d’accès en utilisant cette identité avant de les déployer.

## <a name="azure-powershell-deployment"></a>Déploiement d’Azure PowerShell

> [!WARNING]
> Les clients PowerShell ajoutent souvent `\` à `"` dans le fichier settings.js, ce qui entraîne l’échec de akvvm_service avec l’erreur `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`. Les caractères `\` et `"` en trop sont visibles dans le portail, dans **Extensions** sous **Paramètres**. Pour éviter cela, initialisez `$settings` en tant que `HashTable` PowerShell :
> 
> ```powershell
> $settings = @{
>     "secretsManagementSettings" = @{ 
>         "pollingIntervalInS"       = "<pollingInterval>"; 
>         "certificateStoreName"     = "<certStoreName>"; 
>         "certificateStoreLocation" = "<certStoreLoc>"; 
>         "observedCertificates"     = @("<observedCert1>", "<observedCert2>") } }
> ```
>
  
Azure PowerShell vous permet de déployer l’extension de machine virtuelle Key Vault sur une machine virtuelle ou un groupe de machines virtuelles identiques existants. 

* Pour déployer l’extension sur une machine virtuelle :
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Pour déployer l’extension sur un groupe de machines virtuelles identiques :

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

L’interface de ligne de commande Azure permet de déployer l’extension de machine virtuelle Key Vault sur une machine virtuelle ou un groupe de machines virtuelles identiques existants. 
 
* Pour déployer l’extension sur une machine virtuelle :
    
    ```azurecli
       # Start the deployment
         az vm extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Pour déployer l’extension sur un groupe de machines virtuelles identiques :

   ```azurecli
        # Start the deployment
        az vmss extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

Tenez compte des restrictions et conditions suivantes :
- Restrictions de Key Vault :
  - Doit exister au moment du déploiement 
  - La stratégie d’accès Key Vault doit être définie pour l’identité VM/VMSS à l’aide d’une identité managée. Consultez [Comment s’authentifier auprès de Key Vault](../../key-vault/general/authentication.md) et [Attribuer une stratégie d’accès Key Vault](../../key-vault/general/assign-access-policy-cli.md).

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

* Le nombre de observedCertificates que vous pouvez configurer est-il limité ?
  Non, l'extension de machine virtuelle Key Vault ne limite pas le nombre de observedCertificates.

### <a name="troubleshoot"></a>Dépanner

Vous pouvez récupérer des données sur l’état des déploiements des extensions à partir du portail Azure et par le biais d’Azure PowerShell. Pour voir l’état de déploiement des extensions d’une machine virtuelle donnée, exécutez la commande suivante à l’aide d’Azure PowerShell.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure CLI**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

#### <a name="logs-and-configuration"></a>Journaux et configuration
Les journaux d’extension de machine virtuelle Key Vault existent uniquement localement sur la machine virtuelle. Ils sont très instructifs pour la résolution des problèmes

|Emplacement|Description|
|--|--|
| C:\WindowsAzure\Logs\WaAppAgent.log | Indique le moment où une mise à jour de l’extension s’est produite. |
| C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<most recent version\>\ | Affiche l’état du téléchargement du certificat. L’emplacement de téléchargement est toujours le magasin MY de l’ordinateur Windows (certlm.msc). |
| C:\Packages\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<most recent version\>\RuntimeSettings\ | Les journaux du service d’extension de machine virtuelle Key Vault affichent l’état du service akvvm_service. |
| C:\Packages\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<most recent version\>\Status\    | Configuration et fichiers binaires du service d’extension de machine virtuelle Key Vault. |
|||  


### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
