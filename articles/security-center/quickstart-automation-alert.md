---
title: Créer une automatisation de la sécurité pour des alertes de sécurité spécifiques en utilisant un modèle Azure Resource Manager (modèle ARM)
description: Découvrez comment créer une automatisation Microsoft Defender pour le cloud afin de déclencher une application logique, qui sera déclenchée par des alertes Defender pour le cloud à l’aide d’un modèle Azure Resource Manager (modèle ARM).
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: 18d5dcfa0559bbf94da14fdca975da568d011da2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472254"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-arm-template"></a>Démarrage rapide : Créer une réponse automatique à une alerte de sécurité spécifique en utilisant un modèle ARM

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ce guide de démarrage rapide explique comment utiliser un modèle Azure Resource Manager (modèle ARM) pour créer une automatisation du workflow qui déclenche une application logique lorsque des alertes de sécurité particulières sont reçues par Microsoft Defender pour le cloud.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer dans Azure.](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.security%2fsecuritycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Pour obtenir la liste des rôles et des autorisations nécessaires à l’utilisation de la fonctionnalité d’automatisation du workflow Microsoft Defender pour le cloud, consultez [Automatisation de workflow](workflow-automation.md).

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/securitycenter-create-automation-for-alertnamecontains/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.security/securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>Ressources appropriées

- [**Microsoft.Security/automations**](/azure/templates/microsoft.security/automations) : automatisation qui déclenchera l’application logique lors de la réception d’une alerte Microsoft Defender pour le cloud contenant une chaîne spécifique.
- [**Microsoft.Logic/workflows**](/azure/templates/microsoft.logic/workflows) : une application logique vide déclenchable.

Pour d’autres modèles de démarrage rapide Defender pour le cloud, consultez ces [modèles ajoutés par la communauté](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Déployer le modèle

- **PowerShell** :

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.security/securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **CLI** :

  ```azurecli-interactive
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.security/securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **Portail** :

  [![Déployer dans Azure.](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.security%2fsecuritycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

  Pour plus d’informations sur cette option de déploiement, consultez [Utiliser un bouton de déploiement pour étendre des modèles à partir du dépôt GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Utilisez le portail Azure pour vérifier que l’automatisation du workflow a été déployée.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez **Defender pour le cloud**.
1. Dans la barre de menus supérieure, sélectionnez l’icône de filtre, puis sélectionnez l’abonnement spécifique sur lequel vous avez déployé la nouvelle automatisation du workflow.
1. Dans le menu Defender pour le cloud, ouvrez **Automatisation de workflow** et recherchez votre nouvelle automatisation.
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Liste des automatisations configurées." lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > Si vous comptez de nombreuses automatisations de workflow dans votre abonnement, utilisez l’option **filtrer par nom**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez l’automatisation du workflow par le biais du portail Azure.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez **Defender pour le cloud**.
1. Dans la barre de menus supérieure, sélectionnez l’icône de filtre, puis sélectionnez l’abonnement spécifique sur lequel vous avez déployé la nouvelle automatisation du workflow.
1. Dans le menu Defender pour le cloud, ouvrez **Automatisation de workflow** et recherchez l’automatisation à supprimer.
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="Procédure de suppression d’une automatisation de workflow." lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. Activez la case à cocher de l’élément à supprimer.
1. Dans la barre d’outils, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un tutoriel pas à pas vous guidant tout au long du processus de création d’un modèle, consultez :

> [!div class="nextstepaction"]
> [Tutoriel : Créer et déployer votre premier modèle Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
