---
title: Créer un registre confidentiel Microsoft Azure à l’aide d’un modèle Azure Resource Manager
description: Découvrez comment créer un registre confidentiel Microsoft Azure à l’aide d’un modèle Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 04/15/2021
ms.openlocfilehash: 0b393c39940e0912a2497b6efba9ca920ce71fe7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438832"
---
# <a name="quickstart-create-an-microsoft-azure-confidential-ledger-with-an-arm-template"></a>Démarrage rapide : Créer un registre confidentiel Microsoft Azure avec un modèle ARM

[Registre confidentiel Microsoft Azure](overview.md) est un nouveau service, hautement sécurisé, pour la gestion des enregistrements de données sensibles. Ce guide de démarrage rapide explique comment utiliser un modèle Azure Resource Manager (ARM) pour créer un nouveau registre.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.confidentialledger%2Fconfidential-ledger-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

### <a name="azure-subscription"></a>Abonnement Azure

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

### <a name="register-the-resource-provider"></a>Inscrire le fournisseur de ressources

[!INCLUDE [Register the microsoft.ConfidentialLedger resource provider](../../includes/confidential-ledger-register-rp.md)]

### <a name="obtain-your-principal-id"></a>Récupération de l’ID de principal

Un ID de principal est nécessaire pour le modèle. Vous pouvez obtenir le vôtre en exécutant la commande Azure CLI [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list), avec l’indicateur `--show-mine` :

```azurecli-interactive
az ad sp list --show-mine -o table
```

Votre ID de principal est indiqué dans la colonne « ObjectId ».

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.confidentialledger/confidential-ledger-create/azuredeploy.json":::

Ressources Azure définies dans le modèle :

- Microsoft.ConfidentialLedger/ledgers

## <a name="deploy-the-template"></a>Déployer le modèle

1. Sélectionnez l’image ci-après pour vous connecter à Azure et ouvrir le modèle.

    [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.confidentialledger%2Fconfidential-ledger-create%2Fazuredeploy.json)

1. Sélectionnez ou entrez les valeurs suivantes.

    Sauf si la valeur est spécifiée, utilisez la valeur par défaut pour créer le registre confidentiel.

    - **Nom du registre** : sélectionnez un nom pour votre registre. Un nom de registre doit être globalement unique.
    - **Emplacement** : Sélectionnez un emplacement. Par exemple, **USA Est**.
    - **PrincipalId** : indiquez l’ID de principal que vous avez noté dans la section [Prérequis](#obtain-your-principal-id) ci-dessus.

1. Sélectionnez **Achat**. Une fois la ressource de registre confidentiel déployée, vous recevrez une notification.

Le portail Azure est utilisé pour déployer le modèle. Outre le portail Azure, vous pouvez également utiliser Azure PowerShell, l’interface Azure CLI et l’API REST. Pour découvrir d’autres méthodes de déploiement, consultez [Déployer des modèles](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Vous pouvez utiliser le portail Azure pour vérifier la ressource du registre.

## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres articles relatifs à Registre confidentiel Azure peuvent se baser sur ce démarrage rapide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.

Si vous n’en avez plus besoin, supprimez le groupe de ressources. Cela a pour effet de supprimer la ressource de registre. Pour supprimer le groupe de ressources avec Azure CLI ou Azure PowerShell :

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé une ressource de registre confidentiel à l’aide d’un modèle ARM et validé le déploiement. Pour en savoir plus sur le service, consultez [Vue d’ensemble de Registre confidentiel Microsoft Azure](overview.md).