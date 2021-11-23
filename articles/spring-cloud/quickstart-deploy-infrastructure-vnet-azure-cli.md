---
title: 'Démarrage rapide : Provisionner Azure Spring Cloud avec Azure CLI'
description: Ce guide de démarrage rapide explique comment utiliser Azure CLI pour déployer un cluster Spring Cloud dans un réseau virtuel existant.
services: azure-cli
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-java
ms.author: vramasubbu
ms.date: 11/12/2021
ms.openlocfilehash: 967303cc675ebc6537630f08e6871f5b32cf6af4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488827"
---
# <a name="quickstart-provision-azure-spring-cloud-using-azure-cli"></a>Démarrage rapide : Provisionner Azure Spring Cloud avec Azure CLI

Ce guide de démarrage rapide explique comment utiliser Azure CLI pour déployer un cluster Azure Spring Cloud dans un réseau virtuel existant.

Azure Spring Cloud permet de déployer facilement des applications de microservices Spring Boot sur Azure, et sans aucune modification du code. Le service gère l’infrastructure des applications Spring Cloud, ce qui permet aux développeurs de se concentrer sur leur code. Azure Spring Cloud assure la gestion du cycle de vie en utilisant des outils complets, tels que la supervision et les diagnostics, la gestion des configurations, la découverte de services, l’intégration CI/CD, les déploiements bleus-verts, etc.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Deux sous-réseaux dédiés pour le cluster Azure Spring Cloud, un pour le runtime du service et un autre pour les applications de microservice Spring Boot. Pour connaître la configuration requise pour le sous-réseau et le réseau virtuel, consultez la section [Conditions requises pour le réseau virtuel](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).
* Un espace de travail Log Analytics existant pour les paramètres de diagnostic d’Azure Spring Cloud et une ressource Application Insights basée sur un espace de travail. Pour plus d’informations, consultez [Analyser les journaux et les métriques avec les paramètres de diagnostic](diagnostic-services.md) et [Agent in-process Java Application Insights dans Azure Spring Cloud](how-to-application-insights.md).
* Trois plages de routage CIDR (Classless InterDomain Routing) internes (au moins */16* chacune) que vous avez identifiées à l’usage du cluster Azure Spring Cloud. Ces plages CIDR ne seront pas directement routables et seront utilisées uniquement en interne par le cluster Azure Spring Cloud. Les clusters ne peuvent pas utiliser les plages *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* ou *192.0.2.0/24* pour les plages CIDR Spring Cloud internes, ou les plages d’adresses IP incluses dans la plage d’adresses du réseau virtuel du cluster.
* Autorisation de service accordée au réseau virtuel. Le fournisseur de ressources Azure Spring Cloud nécessite une autorisation Propriétaire sur votre réseau virtuel pour accorder un principal de service dédié et dynamique sur le réseau virtuel en vue d’un déploiement et d’une maintenance supplémentaires. Pour obtenir des instructions et plus d’informations, consultez la section [Accorder l’autorisation du service au réseau virtuel](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).
* Si vous utilisez un pare-feu Azure ou une appliance virtuelle réseau, vous devez également répondre aux conditions préalables suivantes :
   * Règles de réseau et de nom de domaine complet (FQDN). Pour plus d’informations, consultez la [Conditions requises pour le réseau virtuel](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Un itinéraire défini par l’utilisateur (UDR) unique appliqué à chacun des sous-réseaux de runtime de service et d’application de microservice Spring Boot. Pour plus d’informations sur les UDR, consultez [Routage du trafic de réseau virtuel](../virtual-network/virtual-networks-udr-overview.md). Avant le déploiement du cluster Spring Cloud, l’UDR doit être configuré avec un itinéraire pour *0.0.0.0/0* et pour destination votre appliance virtuelle réseau. Pour plus d’informations, consultez la section [Apporter votre propre table de routage](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="review-the-azure-cli-deployment-script"></a>Examiner le script de déploiement Azure CLI

Le script de déploiement utilisé dans ce guide de démarrage rapide provient de l’[architecture de référence Azure Spring Cloud](reference-architecture.md).

```azurecli
#!/bin/bash

echo "Enter Azure Subscription ID: "
read subscription
subscription=$subscription

echo "Enter Azure region for resource deployment: "
read region
location=$region

echo "Enter Azure Spring cloud Resource Group Name: "
read azurespringcloudrg
azurespringcloud_resource_group_name=$azurespringcloudrg

echo "Enter Azure Spring cloud VNet Resource Group Name: "
read azurespringcloudvnetrg
azurespringcloud_vnet_resource_group_name=$azurespringcloudvnetrg

echo "Enter Azure Spring cloud Spoke VNet : "
read azurespringcloudappspokevnet
azurespringcloudappspokevnet=$azurespringcloudappspokevnet

echo "Enter Azure Spring cloud App SubNet : "
read azurespringcloudappsubnet
azurespringcloud_app_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudappsubnet

echo "Enter Azure Spring cloud Service SubNet : "
read azurespringcloudservicesubnet
azurespringcloud_service_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudservicesubnet

echo "Enter Azure Log Analytics Workspace Resource Group Name: "
read loganalyticsrg
loganalyticsrg=$loganalyticsrg

echo "Enter Log Analytics Workspace Resource ID: "
read workspace
workspaceID='/subscriptions/'$subscription'/resourcegroups/'$loganalyticsrg'/providers/microsoft.operationalinsights/workspaces/'$workspace

echo "Enter Reserved CIDR Ranges for Azure Spring Cloud: "
read reservedcidrrange
reservedcidrrange=$reservedcidrrange

echo "Enter key=value pair used for tagging Azure Resources (space separated for multiple tags): "
read tag
tags=$tag

randomstring=$(LC_ALL=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 13 | head -n 1)
azurespringcloud_service='spring-'$randomstring #Name of unique Spring Cloud resource
azurespringcloud_appinsights=$azurespringcloud_service
azurespringcloud_resourceid='/subscriptions/'$subscription'/resourceGroups/'$azurespringcloud_resource_group_name'/providers/Microsoft.AppPlatform/Spring/'$azurespringcloud_service

# Create Application Insights
az monitor app-insights component create \
    --app ${azurespringcloud_service} \
    --location ${location} \
    --kind web \
    -g ${azurespringcloudrg} \
    --application-type web \
    --workspace ${workspaceID}

# Create Azure Spring Cloud Instance
az spring-cloud create \
   -n ${azurespringcloud_service} \
   -g ${azurespringcloudrg} \
   -l ${location} \
   --enable-java-agent true \
   --app-insights ${azurespringcloud_service} \
   --sku Standard \
   --app-subnet ${azurespringcloud_app_subnet_name} \
   --service-runtime-subnet ${azurespringcloud_service_subnet_name} \
   --reserved-cidr-range ${reservedcidrrange} \
   --tags ${tags}

# Update diagnostic setting for Azure Spring Cloud instance
az monitor diagnostic-settings create  \
   --name monitoring \
   --resource ${azurespringcloud_resourceid} \
   --logs    '[{"category": "ApplicationConsole","enabled": true}]' \
   --workspace  ${workspaceID}
```

## <a name="deploy-the-cluster"></a>Déployer le cluster

Pour déployer le cluster Azure Spring Cloud à l’aide du script Azure CLI, effectuez les étapes suivantes :

1. Connectez-vous à Azure en utilisant la commande suivante :

   ```azurecli
   az login
   ```

   Une fois connecté, cette commande génère des informations sur tous les abonnements auxquels vous avez accès. Notez le nom et l’ID de l’abonnement que vous souhaitez utiliser.

1. Définissez l’abonnement cible.

   ```azurecli
   az account set --subscription "<your subscription name>"
   ```

1. Inscrivez le fournisseur de ressources Azure Spring Cloud.

   ```azurecli
   az provider register --namespace 'Microsoft.AppPlatform'
   ```

1. Ajoutez les extensions nécessaires à Azure CLI.

   ```azurecli
   az extension add --name spring-cloud
   ```

1. Choisissez un emplacement de déploiement dans les régions où Azure Spring Cloud est disponible, comme indiqué dans [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all).

1. Utilisez la commande suivante pour générer la liste des emplacements Azure. Notez le **Nom** abrégé de la région que vous avez sélectionnée à l’étape précédente.

   ```azurecli
   az account list-locations --output table
   ```

1. Créez un groupe de ressources dans lequel déployer la ressource.

   ```azurecli
   az group create --name <your-resource-group-name> --location <location-name>
   ```

1. Enregistrez le script Bash [deploySpringCloud.sh](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/CLI/brownfield-deployment/deploySpringCloud.sh) en local, puis exécutez-le à partir de l’invite Bash.

   ```azurecli
   ./deploySpringCloud.sh
   ```

1. Entrez les valeurs suivantes lorsque le script vous y invite :

   - L’ID d’abonnement Azure que vous avez enregistré précédemment.
   - Le nom de l’emplacement Azure que vous avez enregistré précédemment.
   - Le nom du groupe de ressources que vous avez créé précédemment.
   - Le nom du groupe de ressources du réseau virtuel dans lequel vous allez déployer vos ressources.
   - Le nom du réseau virtuel spoke (par exemple, *vnet-spoke*).
   - Le nom du sous-réseau que doit utiliser le service d’application Spring Cloud (par exemple, *snet-app*).
   - Le nom du sous-réseau que doit utiliser le service de runtime Cloud Spring (par exemple, *snet-runtime*).
   - Le nom du groupe de ressources de l’espace de travail Azure Log Analytics qui doit être utilisé pour le stockage des journaux de diagnostic.
   - Le nom de l’espace de travail Azure Log Analytics (par exemple, *la-cb5sqq6574o2a*).
   - Les plages CIDR de votre réseau virtuel que doit utiliser Azure Spring Cloud (par exemple, *XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16*).
   - Les paires clé/valeur à appliquer comme étiquettes à toutes les ressources qui prennent en charge les étiquettes. Pour plus d’informations, consultez [Utiliser des étiquettes pour organiser vos ressources Azure et votre hiérarchie de gestion](../azure-resource-manager/management/tag-resources.md). Utilisez une liste séparée par des espaces pour appliquer plusieurs étiquettes (par exemple, *environment=Dev BusinessUnit=finance*).

Une fois ces informations fournies, le script crée et déploie les ressources Azure.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Vous pouvez utiliser le Portail Azure pour consulter les ressources déployées, ou Azure CLI pour les répertorier.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous prévoyez d’utiliser d’autres guides de démarrage rapide et tutoriels, vous pouvez conserver ces ressources. Quand vous n’en avez plus besoin, supprimez le groupe de ressources, ce qui supprime également les ressources qu’il contient. Pour supprimer le groupe de ressources à l’aide d’Azure CLI, utilisez les commandes suivantes :

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une instance Azure Spring Cloud dans un réseau virtuel existant à l’aide d’Azure CLI, puis vous avez validé le déploiement. Pour plus d’informations sur Azure Spring Cloud, accédez aux ressources ci-dessous.

- Déployez l’un des exemples d’application suivants à partir des emplacements ci-dessous :
   - [Pet Clinic App with MySQL Integration](https://github.com/azure-samples/spring-petclinic-microservices) (microservices avec un backend mysql).
   - [Hello World simple](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Utilisez des [domaines personnalisés](tutorial-custom-domain.md) avec Azure Spring Cloud.
- Exposez des applications dans Azure Spring Cloud à Internet à l’aide d’[Azure Application Gateway](expose-apps-gateway-azure-firewall.md).
- Affichez l’[architecture de référence Azure Spring Cloud](reference-architecture.md) sécurisée de bout en bout, basée sur [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).
