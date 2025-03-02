---
title: Chiffrement des ressources sécurisées dans Azure Automation
description: Cet article décrit les concepts de configuration du compte Automation pour utiliser le chiffrement.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 07/27/2021
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 82f64ec617972b4e280e2b383c51829f648a3bef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524375"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Chiffrement des ressources sécurisées dans Azure Automation

Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont protégées dans Azure Automation avec plusieurs niveaux de chiffrement. En fonction de la clé de niveau supérieur utilisée pour le chiffrement, il existe deux modèles de chiffrement :

- Utilisation de clés gérées par Microsoft
- Utilisation des clés que vous gérez

## <a name="microsoft-managed-keys"></a>Clés gérées par Microsoft

Par défaut, votre compte Azure Automation utilise des clés gérées par Microsoft.

Chaque ressource sécurisée est chiffrée et stockée dans Azure Automation à l’aide d’une clé unique (clé de chiffrement de données) générée pour chaque compte Automation. Ces clés sont elles-mêmes chiffrées et stockées dans Azure Automation à l’aide d’une autre clé unique générée pour chaque compte, appelée clé de chiffrement de compte. Ces clés de chiffrement de compte sont chiffrées et stockées dans Azure Automation à l’aide de clés gérées par Microsoft.

## <a name="keys-that-you-manage-with-key-vault"></a>Clés que vous gérez avec Key Vault

Vous pouvez gérer le chiffrement des ressources sécurisées pour votre compte Automation avec vos propres clés. Quand vous spécifiez une clé gérée par le client au niveau du compte Automation, cette clé est utilisée pour protéger la clé de chiffrement de compte et contrôler son accès. Cette dernière est à son tour utilisée pour chiffrer et déchiffrer toutes les ressources sécurisées. Les clés managées par le client offrent plus de flexibilité pour créer, permuter, désactiver et révoquer des contrôles d’accès. Vous pouvez également effectuer un audit sur les clés de chiffrement utilisées pour protéger vos ressources sécurisées.

Utilisez Azure Key Vault pour stocker les clés gérées par le client. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. 

L'activation du Pare-feu Azure sur [Azure Key Vault](../key-vault/general/network-security.md) bloque l'accès à partir des runbooks Azure Automation pour ce service. L'accès sera bloqué même si l'exception de pare-feu autorisant les services Microsoft approuvés est activée, car Automation ne figure pas dans la liste des services approuvés. Lorsqu'un pare-feu est activé, l'accès n'est possible qu'à l'aide d'un Runbook Worker hybride et d'un [point de terminaison de service de réseau virtuel](../key-vault/general/overview-vnet-service-endpoints.md).

Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Utilisation de clés gérées par le client pour un compte Automation

Quand vous utilisez le chiffrement avec des clés gérées par le client pour un compte Automation, Azure Automation enveloppe la clé de chiffrement de compte avec la clé gérée par le client dans le coffre de clés associé. L'activation des clés gérées par le client n'a aucun impact sur les performances, et le compte est immédiatement chiffré avec la nouvelle clé.

Un nouveau compte Automation est toujours chiffré à l’aide de clés gérées par Microsoft. Il est impossible d’activer des clés gérées par le client au moment de la création du compte. Les clés gérées par le client sont stockées dans Azure Key Vault, et le coffre de clés doit être approvisionné avec des stratégies d’accès qui accordent des autorisations de clé à l’identité managée associée au compte Automation. L’identité managée est disponible uniquement après la création du compte de stockage.

Lorsque vous modifiez la clé utilisée pour le chiffrement de ressources sécurisées Azure Automation en activant ou en désactivant des clés gérées par le client, en mettant à jour la version de la clé ou en spécifiant une clé différente, le chiffrement de la clé de chiffrement du compte change, mais les ressources sécurisées de votre compte Azure Automation n'ont pas besoin d'être à nouveau chiffrées.

> [!NOTE]
> Pour activer une clé gérée par le client à l'aide de l'API REST Azure Automation, vous devez utiliser la version 2020-01-13-preview de l'API.

## <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Prérequis à l’utilisation de clés gérées par le client dans Azure Automation

Avant d’activer les clés gérées par le client pour un compte Automation, vous devez vérifier que les prérequis suivants sont remplis :

- Une instance d'[Azure Key Vault](../key-vault/general/basic-concepts.md) dans laquelle les propriétés **Suppression réversible** et **Ne pas vider** sont activées. Ces propriétés sont requises pour permettre la récupération des clés en cas de suppression accidentelle.
- Seules les clés RSA sont prises en charge avec le chiffrement Azure Automation. Pour plus d’informations sur les clés, consultez [À propos des clés, des secrets et des certificats Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md).
- Le compte Automation et le coffre de clés peuvent figurer dans des abonnements différents mais doivent se trouver dans le même locataire Azure Active Directory.
- Lorsque vous utilisez PowerShell, vérifiez que le [module Azure Az PowerShell](/powershell/azure/new-azureps-module-az) est installé. Pour plus d'informations sur son installation ou sa mise à niveau, consultez [Installer le module PowerShell Azure Az](/powershell/azure/install-az-ps).

## <a name="generate-and-assign-a-new-system-assigned-identity-for-an-automation-account"></a>Générer et attribuer une nouvelle identité affectée par le système pour un compte Automation

Pour utiliser des clés gérées par le client avec un compte Automation, votre compte Automation doit s’authentifier auprès du coffre de clés qui stocke les clés gérées par le client. Azure Automation utilise des identités managées attribuées par le système pour authentifier le compte auprès d’Azure Key Vault. Pour en savoir plus sur les identités managées, consultez la section [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="using-powershell"></a>Utilisation de PowerShell

Utilisez la cmdlet PowerShell [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) pour modifier un compte Azure Automation existant. Le paramètre `-AssignSystemIdentity` génère et attribue une nouvelle identité affectée par le système pour le compte Automation à utiliser avec d'autres services comme Azure Key Vault. Pour plus d'informations, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md) et [À propos d'Azure Key Vault](../key-vault/general/overview.md). Exécutez le code suivant :

```powershell
# Revise variables with your actual values.
$resourceGroup = "ResourceGroupName"
$automationAccount = "AutomationAccountName"
$vaultName = "KeyVaultName"
$keyName = "KeyName"

Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity
```

Le résultat doit être semblable à ce qui suit :

:::image type="content" source="./media/automation-secure-asset-encryption/set-azautomationaccount.png" alt-text="Sortie de la cmdlet Set-AzAutomationAccount.":::

Récupérez le `PrincipalId` pour une utilisation ultérieure. Exécutez le code suivant :

```powershell
$principalID = (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity.PrincipalId

$principalID
```

### <a name="using-rest"></a>Utilisation de REST

Configurez une identité managée affectée par le système pour le compte Automation à l'aide de l'appel d'API REST suivant :

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corps de la requête :

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

L’identité attribuée par le système pour le compte Automation est retournée dans une réponse semblable à la suivante :

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

## <a name="configuration-of-the-key-vault-access-policy"></a>Configuration de la stratégie d’accès de Key Vault

Une fois qu'une identité managée affectée par le système est attribuée au compte Automation, configurez l'accès au coffre de clés qui stocke les clés gérées par le client. Azure Automation doit disposer des autorisations relatives aux opérations **Get**, **Recover**, **WrapKey** et **UnwrapKey** pour que l'identité ait accès aux clés gérées par le client.

### <a name="using-powershell"></a>Utilisation de PowerShell

Utilisez la cmdlet PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) pour octroyer les autorisations nécessaires. Puis utilisez [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) pour créer une clé dans le coffre de clés. Exécutez le code suivant :

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $vaultName `
    -ObjectId $principalID `
    -PermissionsToKeys Get, Recover, UnwrapKey, WrapKey

Add-AzKeyVaultKey `
    -VaultName $vaultName `
    -Name $keyName `
    -Destination 'Software'
```

Le résultat doit être semblable à ce qui suit :

:::image type="content" source="./media/automation-secure-asset-encryption/add-azkeyvaultkey.png" alt-text="Sortie de la cmdlet Add-AzKeyVaultKey.":::

### <a name="using-rest"></a>Utilisation de REST

La stratégie d'accès peut être définie à l'aide de l'appel d'API REST suivant :

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Corps de la requête :

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> Les champs **tenantId** et **objectId** doivent être fournis avec les valeurs **identity.tenantId** et **identity.principalId** respectivement à partir de la réponse de l’identité managée pour le compte Automation.

## <a name="reconfigure-automation-account-to-use-customer-managed-key"></a>Reconfigurer le compte Automation pour utiliser la clé gérée par le client

Si vous souhaitez remplacer les clés gérées par Microsoft par des clés gérées par le client sur votre compte Automation, utilisez Azure PowerShell ou un modèle Azure Resource Manager.

### <a name="using-powershell"></a>Utilisation de PowerShell

Utilisez la cmdlet PowerShell [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) pour reconfigurer le compte Automation de manière à utiliser des clés gérées par le client.

```powershell
$vaultURI = (Get-AzKeyVault -VaultName $vaultName).VaultUri
$keyVersion = (Get-AzKeyVaultKey -VaultName $vaultName -KeyName $keyName).Version

Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity `
    -KeyName $keyName `
    -KeyVaultUri $vaultURI `
    -KeyVersion $keyVersion `
    -KeyVaultEncryption 
```

Vous pouvez vérifier que le changement a bien été effectué en exécutant la commande suivante :

```powershell
(Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Encryption `
    |  ConvertTo-Json 
```

Le résultat doit être semblable à ce qui suit :

:::image type="content" source="./media/automation-secure-asset-encryption/get-azautomationaccount.png" alt-text="Sortie de la cmdlet Get-AzAutomationAccount cmdlet.":::

### <a name="using-rest"></a>Utilisation de REST

Utilisez l'appel d'API REST suivant :

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corps de la requête :

```json
{
    "identity": {
    "type": "SystemAssigned"
    },
    "properties": {
        "encryption": {
            "keySource": "Microsoft.Keyvault",
            "keyvaultProperties": {
                "keyName": "sample-vault-key",
                "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
                "keyVersion": "7c73556c521340209371eaf623cc099d"
            }
        }
    }
}
```

Exemple de réponse

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>Rotation d’une clé gérée par le client

Vous pouvez permuter une clé gérée par le client dans Azure Key Vault en fonction de vos stratégies de conformité. Une fois la clé permutée, vous devez mettre à jour le compte Automation pour utiliser le nouvel URI de clé.

La permutation de la clé ne déclenche pas le rechiffrement des ressources sécurisées sur le compte Automation. Aucune action supplémentaire n'est requise.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Révocation de l’accès à une clé gérée par le client

Pour révoquer l’accès aux clés gérées par le client, utilisez PowerShell ou Azure CLI. Pour plus d’informations, consultez [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) ou [Interface de ligne de commande Azure Key Vault](/cli/azure/keyvault). La révocation de l’accès bloque efficacement l’accès à toutes les ressources sécurisées dans le compte Automation, car la clé de chiffrement est inaccessible pour Azure Automation.

## <a name="next-steps"></a>Étapes suivantes

- Pour comprendre ce qu’est l’Azure Key Vault, consultez [Présentation d’Azure Key Vault](../key-vault/general/overview.md).
- Pour utiliser des certificats, consultez [Gérer les certificats dans Azure Automation](shared-resources/certificates.md).
- Pour gérer les informations d’identification, consultez [Gérer les informations d’identification dans Azure Automation](shared-resources/credentials.md).
- Pour utiliser des variables Automation, consultez [Gérer les variables dans Azure Automation](shared-resources/variables.md).
- Pour obtenir de l’aide lors de l’utilisation de connexions, consultez [Gérer les connexions dans Azure Automation](automation-connections.md).
