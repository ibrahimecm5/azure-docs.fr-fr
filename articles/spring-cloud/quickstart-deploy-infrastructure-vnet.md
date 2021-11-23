---
title: 'Démarrage rapide : Approvisionner Azure Spring Cloud à l’aide d’un modèle Azure Resource Manager (modèle ARM)'
description: Ce guide de démarrage rapide explique comment utiliser un modèle ARM pour déployer un cluster Spring Cloud dans un réseau virtuel existant.
services: azure-resource-manager
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-java
ms.author: rhudson
ms.date: 11/12/2021
ms.openlocfilehash: c34d2d9f88f97b80892b4329def4ca84f5f7b70c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488858"
---
# <a name="quickstart-provision-azure-spring-cloud-using-an-arm-template"></a>Démarrage rapide : Approvisionner Azure Spring Cloud à l’aide d’un modèle ARM

Ce démarrage rapide explique comment utiliser un modèle Azure Resource Manager (modèle ARM) pour déployer un cluster Azure Spring Cloud dans un réseau virtuel existant.

Azure Spring Cloud permet de déployer facilement des applications de microservices Spring Boot sur Azure, et sans aucune modification du code. Le service gère l’infrastructure des applications Spring Cloud, ce qui permet aux développeurs de se concentrer sur leur code. Azure Spring Cloud assure la gestion du cycle de vie en utilisant des outils complets, tels que la supervision et les diagnostics, la gestion des configurations, la découverte de services, l’intégration CI/CD, les déploiements bleus-verts, etc.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-spring-cloud-reference-architecture%2Fmain%2FARM%2Fbrownfield-deployment%2fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Deux sous-réseaux dédiés pour le cluster Azure Spring Cloud, un pour le runtime du service et un autre pour les applications de microservice Spring Boot. Pour connaître la configuration requise pour le sous-réseau et le réseau virtuel, consultez la section [Conditions requises pour le réseau virtuel](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).
* Un espace de travail Log Analytics existant pour les paramètres de diagnostic d’Azure Spring Cloud et une ressource Application Insights basée sur un espace de travail. Pour plus d’informations, consultez [Analyser les journaux et les métriques avec les paramètres de diagnostic](diagnostic-services.md) et [Agent in-process Java Application Insights dans Azure Spring Cloud](how-to-application-insights.md).
* Trois plages de routage CIDR (Classless InterDomain Routing) internes (au moins */16* chacune) que vous avez identifiées à l’usage du cluster Azure Spring Cloud. Ces plages CIDR ne seront pas directement routables et seront utilisées uniquement en interne par le cluster Azure Spring Cloud. Les clusters ne peuvent pas utiliser les plages *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* ou *192.0.2.0/24* pour les plages CIDR Spring Cloud internes, ou les plages d’adresses IP incluses dans la plage d’adresses du réseau virtuel du cluster.
* Autorisation de service accordée au réseau virtuel. Le fournisseur de ressources Azure Spring Cloud nécessite une autorisation Propriétaire sur votre réseau virtuel pour accorder un principal de service dédié et dynamique sur le réseau virtuel en vue d’un déploiement et d’une maintenance supplémentaires. Pour obtenir des instructions et plus d’informations, consultez la section [Accorder l’autorisation du service au réseau virtuel](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).
* Si vous utilisez un pare-feu Azure ou une appliance virtuelle réseau, vous devez également répondre aux conditions préalables suivantes :

   * Règles de réseau et de nom de domaine complet (FQDN). Pour plus d’informations, consultez la [Conditions requises pour le réseau virtuel](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Un itinéraire défini par l’utilisateur (UDR) unique appliqué à chacun des sous-réseaux de runtime de service et d’application de microservice Spring Boot. Pour plus d’informations sur les UDR, consultez [Routage du trafic de réseau virtuel](../virtual-network/virtual-networks-udr-overview.md). Avant le déploiement du cluster Spring Cloud, l’UDR doit être configuré avec un itinéraire pour *0.0.0.0/0* et pour destination votre appliance virtuelle réseau. Pour plus d’informations, consultez la section [Apporter votre propre table de routage](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide provient de l’[architecture de référence Azure Spring Cloud](reference-architecture.md).

:::code language="json" source="~/azure-spring-cloud-reference-architecture/ARM/brownfield-deployment/azuredeploy.json":::

Deux ressources Azure sont définies dans le modèle :

* [Microsoft.AppPlatform/Spring](/azure/templates/microsoft.appplatform/spring) : créer une instance Azure Spring Cloud.
* [Microsoft.Insights/Components](/azure/templates/microsoft.insights/components) : créer un espace de travail Application Insights.

Pour les déploiements d’Azure CLI, de Terraform et de Bicep, consultez le dépôt [Azure Spring Cloud Reference Architecture](https://github.com/Azure/azure-spring-cloud-reference-architecture) sur GitHub.

## <a name="deploy-the-template"></a>Déployer le modèle

Suivez ces étapes pour déployer le modèle :

1. Cliquez sur l’image ci-après pour vous connecter à Azure et ouvrir un modèle. Le modèle crée une instance Azure Spring Cloud dans un réseau virtuel existant, et une instance de Application Insights basée sur un espace de travail dans un espace de travail Azure Monitor Log Analytics existant.

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-spring-cloud-reference-architecture%2Fmain%2FARM%2Fbrownfield-deployment%2fazuredeploy.json)

2. Entrez des valeurs dans les champs suivants :

   - **Groupe de ressources** : sélectionnez **Créer**, entrez un nom unique pour le **groupe de ressources**, puis sélectionnez **OK**.
   - **springCloudInstanceName :** entrez le nom de la ressource Azure Spring Cloud.
   - **appInsightsName :** entrez le nom de l’instance Application Insights pour Azure Spring Cloud.
   - **laWorkspaceResourceId :** entrez l’ID de ressource de l’espace de travail Log Analytics existant (par exemple, */subscriptions/\<your subscription>/resourcegroups/\<your log analytics resource group>/providers/Microsoft.OperationalInsights/workspaces/\<your log analytics workspace name>* ).
   - **springCloudAppSubnetID :** entrez le resourceID du sous-réseau de l’application Azure Spring Cloud.
   - **springCloudRuntimeSubnetID :** entrez le resourceID du sous-réseau du runtime Azure Spring Cloud.
   - **springCloudServiceCidrs :** entrez une liste séparée par des virgules de plages d’adresses IP (3 au total) au format CIDR. Les plages d’adresses IP sont réservées pour héberger l’infrastructure Azure Spring Cloud sous-jacente. Ces 3 plages doivent être au moins des plages d’adresses IP inutilisées */16*, et ne doivent chevaucher aucune plage d’adresses IP de sous-réseau routable utilisée au sein du réseau.
   - **balises :** entrez des balises personnalisées.

3. Sélectionnez **Vérifier + créer**, puis **Créer**.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Vous pouvez utiliser le portail Azure pour vérifier les ressources déployées, ou Azure CLI ou un script Azure PowerShell pour lister les ressources déployées.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous prévoyez d’utiliser d’autres guides de démarrage rapide et tutoriels, vous pouvez conserver ces ressources. Quand vous n’en avez plus besoin, supprimez le groupe de ressources, ce qui supprime également les ressources qu’il contient. Pour supprimer le groupe de ressources à l’aide d’Azure CLI ou d’Azure PowerShell, utilisez les commandes suivantes :

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/azure-cli)

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce démarrage rapide, vous avez déployé une instance Azure Spring Cloud dans un réseau virtuel existant à l’aide d’un modèle ARM, puis validé le déploiement. Pour plus d’informations sur Azure Spring Cloud et Azure Resource Manager, accédez aux ressources ci-dessous.

- Déployez l’un des exemples d’application suivants à partir des emplacements ci-dessous :
   - [Pet Clinic App with MySQL Integration](https://github.com/azure-samples/spring-petclinic-microservices) (microservices avec un backend mysql).
   - [Hello World simple](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Utilisez des [domaines personnalisés](tutorial-custom-domain.md) avec Azure Spring Cloud.
- Exposez des applications dans Azure Spring Cloud à Internet à l’aide d’[Azure Application Gateway](expose-apps-gateway-azure-firewall.md).
- Affichez l’[architecture de référence Azure Spring Cloud](reference-architecture.md) sécurisée de bout en bout, basée sur [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).
- Découvrez [Azure Resource Manager](../azure-resource-manager/management/overview.md).
