---
title: Tutoriel sur la rotation des ressources avec deux jeux d’informations d’identification
description: Utilisez ce tutoriel pour découvrir comment automatiser la rotation d’un secret pour des ressources qui utilisent deux jeux d’informations d’authentification.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 60d99ea9b1add522701a82bbd461e4ef064286aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070448"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatiser la permutation d’un secret pour des ressources qui ont deux jeux d’informations d’authentification

La meilleure façon de s’authentifier auprès des services Azure consiste à utiliser une [identité managée](../general/authentication.md). Il existe toutefois des scénarios dans lesquels cette approche est inappropriée. Dans ces cas-là, des clés d’accès ou des mots de passe sont utilisés. Vous devez fréquemment permuter les clés d’accès et les mots de passe.

Ce tutoriel montre comment automatiser la rotation régulière des secrets pour les bases de données et les services qui utilisent deux jeux d’informations d’authentification. Plus précisément, ce tutoriel montre comment permuter les clés de compte de stockage Azure stockées dans Azure Key Vault en tant que secrets. Vous utiliserez une fonction déclenchée par une notification Azure Event Grid. 

> [!NOTE]
> Les clés de compte de stockage peuvent être gérées automatiquement dans Key Vault si vous fournissez des jetons de signature d’accès partagé pour l’accès délégué au compte de stockage. Il existe des services qui nécessitent des chaînes de connexion de compte de stockage avec des clés d’accès. Pour ce scénario, nous vous recommandons cette solution.

Voici la solution de permutation décrite dans ce tutoriel : 

![Diagramme illustrant la solution de permutation](../media/secrets/rotation-dual/rotation-diagram.png)

Dans cette solution, Azure Key Vault stocke les clés d’accès individuelles de compte de stockage sous la forme de versions du même secret en alternant entre la clé primaire et la clé secondaire dans les versions qui se suivent. Quand une clé d’accès est stockée dans la dernière version du secret, la clé secondaire est regénérée et ajoutée à Key Vault en tant que nouvelle dernière version du secret. La solution fournit le cycle de permutation complet de l’application pour actualiser jusqu’à la clé regénérée la plus récente. 

1. Trente jours avant la date d’expiration d’un secret, Key Vault publie l’événement d’expiration proche sur Event Grid.
1. Event Grid vérifie les abonnements aux événements et utilise HTTP POST pour appeler le point de terminaison de l’application de fonction qui est abonné à l’événement.
1. L’application de fonction identifie la clé secondaire (pas la dernière) et appelle le compte de stockage pour la regénérer.
1. L’application de fonction ajoute la nouvelle clé regénérée à Azure Key Vault en tant que nouvelle version du secret.

## <a name="prerequisites"></a>Prérequis
* Un abonnement Azure. [Créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure [Cloud Shell](https://shell.azure.com/). Ce tutoriel utilise le portail Cloud Shell avec PowerShell env
* Azure Key Vault.
* Deux comptes de stockage Azure.

Vous pouvez utiliser ce lien de déploiement si vous n’avez pas de coffre de clés ou de comptes de stockage existants :

[![Lien intitulé Deploy to Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Sous **Groupe de ressources**, sélectionnez **Créer**. Nommez le groupe **vaultrotation**, puis sélectionnez **OK**.
1. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).

    ![Capture d’écran montrant comment créer un groupe de ressources.](../media/secrets/rotation-dual/dual-rotation-1.png)

Vous disposez maintenant d’un coffre de clés et de deux comptes de stockage. Vous pouvez vérifier cette configuration dans l’interface Azure CLI ou Azure PowerShell en exécutant cette commande :
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

Le résultat doit ressembler à la sortie suivante :

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Créer et déployer la fonction de permutation de clé

Ensuite, vous allez créer une application de fonction avec une identité managée par le système en plus d’autres composants nécessaires. Vous allez également déployer la fonction de permutation pour les clés de compte de stockage.

La fonction de permutation de l’application de fonction nécessite les composants et la configuration suivants :
- Un plan Azure App Service
- Un compte de stockage pour gérer les déclencheurs de l’application de fonction
- Une stratégie d’accès pour accéder aux secrets dans Key Vault
- Le rôle de service d’opérateur de clé de compte de stockage affecté à l’application de fonction afin qu’elle puisse accéder aux clés d’accès de compte de stockage
- Une fonction de permutation de clé avec un déclencheur d’événement et un déclencheur HTTP (permutation à la demande)
- Un abonnement aux événements Event Grid pour l’événement **SecretNearExpiry**

1. Sélectionnez le lien de déploiement d’un modèle Azure : 

   [![Lien de déploiement d’un modèle Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Dans la liste **Groupe de ressources**, sélectionnez **vaultrotation**.
1. Dans la zone **Groupe de ressources du compte de stockage**, entrez le nom du groupe de ressources dans lequel se trouve votre compte de stockage. Conservez la valeur par défaut **[resourceGroup().name]** si votre compte de stockage se trouve déjà dans le même groupe de ressources que celui dans lequel vous allez déployer la fonction de permutation de clés.
1. Dans la zone **Nom du compte de stockage**, entrez le nom du compte de stockage qui contient les clés d’accès à permuter. Si vous utilisez un compte de stockage créé dans la section [Prérequis](#prerequisites), conservez la valeur par défaut **[concat(resourceGroup().name, 'storage')]** .
1. Dans la zone **Groupe de ressources du coffre de clés**, entrez le nom du groupe de ressources dans lequel se trouve votre coffre de clés. Conservez la valeur par défaut **[resourceGroup().name]** si votre coffre de clés existe déjà dans le même groupe de ressources que celui dans lequel vous allez déployer la fonction de permutation de clés.
1. Dans la zone **Nom du coffre de clés**, entrez le nom du coffre de clés. Si vous utilisez un coffre de clés créé dans la section [Prérequis](#prerequisites), conservez la valeur par défaut **[concat(resourceGroup().name, '-kv')]** .
1. Dans la zone **Type du plan App Service**, sélectionnez le plan d’hébergement. Le **plan Premium** n’est nécessaire que si votre coffre de clés se trouve derrière un pare-feu.
1. Dans la zone **Nom de l’application de fonction**, entrez le nom de l’application de fonction.
1. Dans la zone **Nom du secret**, entrez le nom du secret dans lequel vous allez stocker les clés d’accès.
1. Dans la zone **URL du dépôt**, entrez l’emplacement GitHub du code de la fonction. Dans ce tutoriel, vous pouvez utiliser **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).

   ![Capture d’écran montrant comment créer et déployer une fonction.](../media/secrets/rotation-dual/dual-rotation-2.png)

Après avoir effectué les étapes précédentes, vous disposez d’un compte de stockage, d’une batterie de serveurs, d’une application de fonction et d’Application Insights. Une fois le déploiement terminé, la page suivante s’affiche :

   ![Capture d’écran montrant la page Votre déploiement a été effectué.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> En cas de problème, vous pouvez sélectionner **Redéployer** pour terminer le déploiement des composants.


Vous trouverez des modèles de déploiement et du code pour la fonction de permutation dans [Exemples Azure](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Ajouter les clés d’accès de compte de stockage dans Key Vault

Commencez par définir votre stratégie d’accès pour accorder les autorisations **Gérer les secrets** à votre utilisateur principal :
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

Vous pouvez maintenant créer un secret ayant pour valeur une clé d’accès de compte de stockage. Vous avez également besoin de l’ID de ressource du compte de stockage, de la période de validité du secret et de l’ID de clé à ajouter au secret, afin que la fonction de permutation puisse regénérer la clé dans le compte de stockage.

Déterminez l’ID de ressource du compte de stockage. Vous pouvez trouver cette valeur dans la propriété `id`.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Listez les clés d’accès de compte de stockage afin de pouvoir récupérer les valeurs des clés :
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Ajoutez le secret au coffre de clés avec la date d’expiration définie sur le lendemain, une période de validité de 60 jours et l’ID de ressource du compte de stockage. Exécutez cette commande, en utilisant les valeurs récupérées pour `key1Value` et `storageAccountResourceId` :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Le secret ci-dessus déclenchera l’événement `SecretNearExpiry` au bout de quelques minutes. Cet événement déclenchera à son tour la fonction permettant de permuter le secret, avec l’expiration définie sur 60 jours. Dans cette configuration, l’événement « SecretNearExpiry » est déclenché tous les 30 jours (30 jours avant l’expiration) et la fonction de permutation alterne la permutation entre key1 et key2.

Vous pouvez vérifier que les clés d’accès ont été regénérées en récupérant la clé de compte de stockage et le secret Key Vault, puis en les comparant.

Utilisez cette commande pour obtenir des informations sur le secret :
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

Remarquez que `CredentialId` est mis à jour vers l’autre `keyName` et que `value` est regénérée :

![Capture d’écran montrant la sortie de la commande A Z keyvault secret show pour le premier compte de stockage.](../media/secrets/rotation-dual/dual-rotation-4.png)

Récupérez les clés d’accès pour comparer les valeurs :
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Notez que la `value` de la clé est identique au secret du coffre de clés :

![Capture d’écran montrant la sortie de la commande A Z storage account keys list pour le premier compte de stockage.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Ajouter des comptes de stockage pour la permutation

Vous pouvez réutiliser la même application de fonction pour permuter les clés de plusieurs comptes de stockage. 

Pour ajouter des clés de compte de stockage à une fonction existante en vue d’une permutation, vous avez besoin des éléments suivants :
- Le rôle de service d’opérateur de clé de compte de stockage affecté à l’application de fonction afin qu’elle puisse accéder aux clés d’accès de compte de stockage
- Un abonnement aux événements Event Grid pour l’événement **SecretNearExpiry**

1. Sélectionnez le lien de déploiement d’un modèle Azure : 

   [![Lien de déploiement d’un modèle Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Dans la liste **Groupe de ressources**, sélectionnez **vaultrotation**.
1. Dans la zone **Groupe de ressources du compte de stockage**, entrez le nom du groupe de ressources dans lequel se trouve votre compte de stockage. Conservez la valeur par défaut **[resourceGroup().name]** si votre compte de stockage se trouve déjà dans le même groupe de ressources que celui dans lequel vous allez déployer la fonction de permutation de clés.
1. Dans la zone **Nom du compte de stockage**, entrez le nom du compte de stockage qui contient les clés d’accès à permuter.
1. Dans la zone **Groupe de ressources du coffre de clés**, entrez le nom du groupe de ressources dans lequel se trouve votre coffre de clés. Conservez la valeur par défaut **[resourceGroup().name]** si votre coffre de clés existe déjà dans le même groupe de ressources que celui dans lequel vous allez déployer la fonction de permutation de clés.
1. Dans la zone **Nom du coffre de clés**, entrez le nom du coffre de clés.
1. Dans la zone **Nom de l’application de fonction**, entrez le nom de l’application de fonction.
1. Dans la zone **Nom du secret**, entrez le nom du secret dans lequel vous allez stocker les clés d’accès.
1. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).

   ![Capture d’écran montrant comment créer un compte de stockage supplémentaire.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Ajouter une autre clé d’accès de compte de stockage dans Key Vault

Déterminez l’ID de ressource du compte de stockage. Vous pouvez trouver cette valeur dans la propriété `id`.
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Listez les clés d’accès de compte de stockage afin de pouvoir récupérer la valeur key2 :
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

Ajoutez le secret au coffre de clés avec la date d’expiration définie sur le lendemain, une période de validité de 60 jours et l’ID de ressource du compte de stockage. Exécutez cette commande, en utilisant les valeurs récupérées pour `key2Value` et `storageAccountResourceId` :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Utilisez cette commande pour obtenir des informations sur le secret :
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

Remarquez que `CredentialId` est mis à jour vers l’autre `keyName` et que `value` est regénérée :

![Capture d’écran montrant la sortie de la commande A Z keyvault secret show pour le second compte de stockage.](../media/secrets/rotation-dual/dual-rotation-8.png)

Récupérez les clés d’accès pour comparer les valeurs :
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Notez que la `value` de la clé est identique au secret du coffre de clés :

![Capture d’écran montrant la sortie de la commande A Z storage account keys list pour le second compte de stockage.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Fonctions de permutation Key Vault pour deux jeux d’informations d’identification

Modèle de fonctions de rotation pour deux ensembles d’informations d’identification et plusieurs fonctions prêtes à l’emploi :

- [Modèle de projet](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Cache Redis](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Compte de stockage](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> Les fonctions de rotation ci-dessus sont créées par un membre de la communauté et non par Microsoft. Les fonctions Azure fournies par la communauté ne sont pas prises en charge par les programmes ou services d’assistance technique Microsoft. Elles sont proposées EN L’ÉTAT sans garantie d’aucune sorte.

## <a name="next-steps"></a>Étapes suivantes

- Tutoriel : [Permutation des secrets pour un jeu d’informations d’identification](./tutorial-rotation.md)
- Présentation : [Supervision de Key Vault avec Azure Event Grid](../general/event-grid-overview.md)
- Procédure : [Créer votre première fonction sur le Portail Azure](../../azure-functions/functions-get-started.md)
- Procédure : [Recevoir un e-mail en cas de changement d’un secret Key Vault](../general/event-grid-logicapps.md)
- Référence : [Schéma des événements Azure Event Grid pour Azure Key Vault](../../event-grid/event-schema-key-vault.md)
