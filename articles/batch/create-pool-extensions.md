---
title: Utiliser des extensions avec des pools Batch
description: Les extensions sont de petites applications qui facilitent la configuration et l’installation post-approvisionnement sur des nœuds de calcul Batch.
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: bac3c6c53552ac99aef6210cbe184da754580ca0
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508681"
---
# <a name="use-extensions-with-batch-pools"></a>Utiliser des extensions avec des pools Batch

Les extensions sont de petites applications qui facilitent la configuration et l’installation post-approvisionnement sur des nœuds de calcul Batch. Vous pouvez sélectionner l’une des extensions autorisées par Azure Batch et l’installer sur les nœuds de calcul lorsqu’elle est approvisionnée. Après cela, l’extension peut effectuer l’opération prévue.

Vous pouvez vérifier l’état en direct des extensions que vous utilisez et récupérer les informations qu’elles renvoient pour pouvoir effectuer des détections, des corrections ou des fonctionnalités de diagnostic.

## <a name="prerequisites"></a>Prérequis

- Les pools avec des extensions doivent utiliser la [configuration de machine virtuelle](nodes-and-pools.md#virtual-machine-configuration).
- Le type d’extension CustomScript est réservé au service Azure Batch et ne peut pas être remplacé.
- Certaines extensions ont besoin, pour fonctionner correctement, d’une identité managée au niveau du pool qui soit accessible dans le contexte d’un nœud de calcul. Le cas échéant, consultez [Configuration des identités managées dans des pools Batch](managed-identity-pools.md).

## <a name="supported-extensions"></a>Extensions prises en charge

Les extensions qui peuvent actuellement être installées lors de la création d’un pool Batch sont les suivantes :

- [Extension Azure Key Vault pour Linux](../virtual-machines/extensions/key-vault-linux.md)
- [Extension Azure Key Vault pour Windows](../virtual-machines/extensions/key-vault-windows.md)
- [Extension de monitoring et d’analytique des journaux Azure Monitor pour Linux](../virtual-machines/extensions/oms-linux.md)
- [Extension de monitoring et d’analytique des journaux Azure Monitor pour Windows](../virtual-machines/extensions/oms-windows.md)
- [Extension Desired State Configuration (DSC) Azure](../virtual-machines/extensions/dsc-overview.md)
- [Extension Diagnostics Azure pour machines virtuelles Windows](../virtual-machines/windows/extensions-diagnostics.md)
- [Extension du pilote GPU HPC pour Windows sur AMD](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md)
- [Extension du pilote GPU HPC pour Windows sur NVIDIA](../virtual-machines/extensions/hpccompute-gpu-windows.md)
- [Extension Microsoft Antimalware pour Windows](../virtual-machines/extensions/iaas-antimalware-windows.md)

Vous pouvez demander la prise en charge de serveurs de publication et/ou de types d’extension supplémentaires en ouvrant une demande de support.

## <a name="create-a-pool-with-extensions"></a>Créer un pool avec des extensions

L’exemple ci-dessous crée un pool Batch de nœuds Linux qui utilise l’extension Azure Key Vault.

URI de l’API REST

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Corps de la requête

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Obtenir des données d’extension à partir d’un pool

L’exemple ci-dessous récupère les données de l’extension Azure Key Vault.

URI de l’API REST

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Corps de la réponse

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez différentes manières de [copier des applications et des données dans des nœuds de pool](batch-applications-to-pool-nodes.md).
- En savoir plus sur l’utilisation de [nœuds et de pools](nodes-and-pools.md).
