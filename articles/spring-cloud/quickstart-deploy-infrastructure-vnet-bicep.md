---
title: Démarrage rapide - Provisionner Azure Spring Cloud avec Bicep
description: Ce guide de démarrage rapide explique comment utiliser Bicep pour déployer un cluster Spring Cloud dans un réseau virtuel existant.
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ssarwa
ms.date: 11/12/2021
ms.openlocfilehash: ac155d1937638b4be26c74214d63d35edd009322
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488808"
---
# <a name="quickstart-provision-azure-spring-cloud-using-bicep"></a>Démarrage rapide : Provisionner Azure Spring Cloud avec Bicep

Ce guide de démarrage rapide explique comment utiliser un modèle Bicep pour déployer un cluster Azure Spring Cloud dans un réseau virtuel existant.

Azure Spring Cloud permet de déployer facilement des applications de microservices Spring Boot sur Azure, et sans aucune modification du code. Le service gère l’infrastructure des applications Spring Cloud, ce qui permet aux développeurs de se concentrer sur leur code. Azure Spring Cloud assure la gestion du cycle de vie en utilisant des outils complets, tels que la supervision et les diagnostics, la gestion des configurations, la découverte de services, l’intégration CI/CD, les déploiements bleus-verts, etc.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Deux sous-réseaux dédiés pour le cluster Azure Spring Cloud, un pour le runtime du service et un autre pour les applications de microservice Spring Boot. Pour connaître la configuration requise pour le sous-réseau et le réseau virtuel, consultez la section [Conditions requises pour le réseau virtuel](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).
* Un espace de travail Log Analytics existant pour les paramètres de diagnostic d’Azure Spring Cloud. Pour plus d’informations, consultez [Analyser les journaux et les métriques avec les paramètres de diagnostic](diagnostic-services.md).
* Trois plages de routage CIDR (Classless InterDomain Routing) internes (au moins */16* chacune) que vous avez identifiées à l’usage du cluster Azure Spring Cloud. Ces plages CIDR ne seront pas directement routables et seront utilisées uniquement en interne par le cluster Azure Spring Cloud. Les clusters ne peuvent pas utiliser les plages *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* ou *192.0.2.0/24* pour les plages CIDR Spring Cloud internes, ou les plages d’adresses IP incluses dans la plage d’adresses du réseau virtuel du cluster.
* Autorisation de service accordée au réseau virtuel. Le fournisseur de ressources Azure Spring Cloud nécessite une autorisation Propriétaire sur votre réseau virtuel pour accorder un principal de service dédié et dynamique sur le réseau virtuel en vue d’un déploiement et d’une maintenance supplémentaires. Pour obtenir des instructions et plus d’informations, consultez la section [Accorder l’autorisation du service au réseau virtuel](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).
* Si vous utilisez un pare-feu Azure ou une appliance virtuelle réseau, vous devez également répondre aux conditions préalables suivantes :
   * Règles de réseau et de nom de domaine complet (FQDN). Pour plus d’informations, consultez la [Conditions requises pour le réseau virtuel](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Un itinéraire défini par l’utilisateur (UDR) unique appliqué à chacun des sous-réseaux de runtime de service et d’application de microservice Spring Boot. Pour plus d’informations sur les UDR, consultez [Routage du trafic de réseau virtuel](../virtual-network/virtual-networks-udr-overview.md). Avant le déploiement du cluster Spring Cloud, l’UDR doit être configuré avec un itinéraire pour *0.0.0.0/0* et pour destination votre appliance virtuelle réseau. Pour plus d’informations, consultez la section [Apporter votre propre table de routage](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="deploy-using-bicep"></a>Déployer en utilisant Bicep

Suivez ces étapes pour déployer le cluster :

1. Créez un fichier *azuredeploy.bicep* avec le contenu suivant :

   ```Bicep
   @description('The instance name of the Azure Spring Cloud resource')
   param springCloudInstanceName string

   @description('The name of the Application Insights instance for Azure Spring Cloud')
   param appInsightsName string

   @description('The resource ID of the existing Log Analytics workspace. This will be used for both diagnostics logs and Application    Insights')
   param laWorkspaceResourceId string

   @description('The resourceID of the Azure Spring Cloud App Subnet')
   param springCloudAppSubnetID string

   @description('The resourceID of the Azure Spring Cloud Runtime Subnet')
   param springCloudRuntimeSubnetID string

   @description('Comma-separated list of IP address ranges in CIDR format. The IP ranges are reserved to host underlying Azure Spring Cloud    infrastructure, which should be 3 at least /16 unused IP ranges, must not overlap with any Subnet IP ranges')
   param springCloudServiceCidrs string = '10.0.0.0/16,10.2.0.0/16,10.3.0.1/16'

   @description('The tags that will be associated to the Resources')
   param tags object = {
     environment: 'lab'
   }

   var springCloudSkuName = 'S0'
   var springCloudSkuTier = 'Standard'
   var location = resourceGroup().location

   resource appInsights 'Microsoft.Insights/components@2020-02-02-preview' = {
     name: appInsightsName
     location: location
     kind: 'web'
     tags: tags
     properties: {
       Application_Type: 'web'
       Flow_Type: 'Bluefield'
       Request_Source: 'rest'
       WorkspaceResourceId: laWorkspaceResourceId
     }
   }

   resource springCloudInstance 'Microsoft.AppPlatform/Spring@2020-07-01' = {
     name: springCloudInstanceName
     location: location
     tags: tags
     sku: {
       name: springCloudSkuName
       tier: springCloudSkuTier
     }
     properties: {
       networkProfile: {
         serviceCidr: springCloudServiceCidrs
         serviceRuntimeSubnetId: springCloudRuntimeSubnetID
         appSubnetId: springCloudAppSubnetID
       }
     }
   }

   resource springCloudMonitoringSettings 'Microsoft.AppPlatform/Spring/monitoringSettings@2020-07-01' = {
     name: '${springCloudInstance.name}/default'
     properties: {
       traceEnabled: true
       appInsightsInstrumentationKey: appInsights.properties.InstrumentationKey
     }
   }

   resource springCloudDiagnostics 'microsoft.insights/diagnosticSettings@2017-05-01-preview' = {
     name: 'monitoring'
     scope: springCloudInstance
     properties: {
       workspaceId: laWorkspaceResourceId
       logs: [
         {
           category: 'ApplicationConsole'
           enabled: true
           retentionPolicy: {
             days: 30
             enabled: false
           }
         }
       ]
     }
   }
   ```

1. Ouvrez une fenêtre Bash et exécutez la commande Azure CLI suivante, en remplaçant les caractères génériques *\<value>* par les valeurs suivantes :

   - **resource-group :** Le nom du groupe de ressources pour le déploiement de l’instance Spring Cloud.
   - **springCloudInstanceName :** le nom de la ressource Azure Spring Cloud.
   - **appInsightsName :** le nom de l’instance Application Insights pour Azure Spring Cloud.
   - **laWorkspaceResourceId :** l’ID de ressource de l’espace de travail Log Analytics existant (par exemple, */   subscriptions/\<your subscription>/resourcegroups/\<your log analytics resource group>/providers/   Microsoft.OperationalInsights/workspaces/\<your log analytics workspace name>* .)
   - **springCloudAppSubnetID :** le resourceID du sous-réseau de l’application Azure Spring Cloud.
   - **springCloudRuntimeSubnetID :** le resourceID du sous-réseau du runtime Azure Spring Cloud.
   - **springCloudServiceCidrs :** une liste séparée par des virgules de plages d’adresses IP (3 au total) au format CIDR. Les plages d’adresses IP sont réservées pour héberger l’infrastructure Azure Spring Cloud sous-jacente. Ces 3 plages doivent être au moins des plages d’adresses IP inutilisées */16*, et ne doivent chevaucher aucune plage d’adresses IP de sous-réseau routable utilisée au sein du réseau.

   ```azurecli
   az deployment group create \
       --resource-group <value> \
       --name initial \
       --template-file azuredeploy.bicep \
       --parameters \
           springCloudInstanceName=<value> \
           appInsightsName=<value> \
           laWorkspaceResourceId=<value> \
           springCloudAppSubnetID=<value> \
           springCloudRuntimeSubnetID=<value> \
           springCloudServiceCidrs=<value>
   ```

   Cette commande utilise le modèle Bicep pour créer une instance Azure Spring Cloud dans un réseau virtuel existant. La commande crée également une instance Application Insights basée sur un espace de travail Azure Monitor Log Analytics existant.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Vous pouvez utiliser le portail Azure pour vérifier les ressources déployées, ou Azure CLI ou un script Azure PowerShell pour lister les ressources déployées.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous prévoyez d’utiliser d’autres guides de démarrage rapide et tutoriels, vous pouvez conserver ces ressources. Quand vous n’en avez plus besoin, supprimez le groupe de ressources, ce qui supprime également les ressources qu’il contient. Pour supprimer le groupe de ressources à l’aide d’Azure CLI, utilisez les commandes suivantes :

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une instance Azure Spring Cloud dans un réseau virtuel existant à l’aide de Bicep, puis vous avez validé le déploiement. Pour plus d’informations sur Azure Spring Cloud, accédez aux ressources ci-dessous.

- Déployez l’un des exemples d’application suivants à partir des emplacements ci-dessous :
   - [Pet Clinic App with MySQL Integration](https://github.com/azure-samples/spring-petclinic-microservices) (microservices avec un backend mysql).
   - [Hello World simple](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Utilisez des [domaines personnalisés](tutorial-custom-domain.md) avec Azure Spring Cloud.
- Exposez des applications dans Azure Spring Cloud à Internet à l’aide d’[Azure Application Gateway](expose-apps-gateway-azure-firewall.md).
- Affichez l’[architecture de référence Azure Spring Cloud](reference-architecture.md) sécurisée de bout en bout, basée sur [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).
