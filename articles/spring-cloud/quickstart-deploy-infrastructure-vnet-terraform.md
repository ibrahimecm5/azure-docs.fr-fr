---
title: 'Démarrage rapide : Provisionner Azure Spring Cloud avec Terraform'
description: Ce guide de démarrage rapide explique comment utiliser Terraform pour déployer un cluster Spring Cloud dans un réseau virtuel existant.
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ariel
ms.date: 11/12/2021
ms.openlocfilehash: 313967fcadf1dac7ec2451b9dc51da1fac9bc435
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488750"
---
# <a name="quickstart-provision-azure-spring-cloud-using-terraform"></a>Démarrage rapide : Provisionner Azure Spring Cloud avec Terraform

Ce guide de démarrage rapide explique comment utiliser Terraform pour déployer un cluster Azure Spring Cloud dans un réseau virtuel existant.

Azure Spring Cloud permet de déployer facilement des applications de microservices Spring Boot sur Azure, et sans aucune modification du code. Le service gère l’infrastructure des applications Spring Cloud, ce qui permet aux développeurs de se concentrer sur leur code. Azure Spring Cloud assure la gestion du cycle de vie en utilisant des outils complets, tels que la supervision et les diagnostics, la gestion des configurations, la découverte de services, l’intégration CI/CD, les déploiements bleus-verts, etc.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* [HashiCorp Terraform](https://www.terraform.io/downloads.html)
* Deux sous-réseaux dédiés pour le cluster Azure Spring Cloud, l’un pour le runtime du service et l’autre pour les applications de microservices Spring Boot. Pour connaître la configuration requise pour le sous-réseau et le réseau virtuel, consultez la section [Conditions requises pour le réseau virtuel](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).
* Un espace de travail Log Analytics existant pour les paramètres de diagnostic d’Azure Spring Cloud et une ressource Application Insights basée sur un espace de travail. Pour plus d’informations, consultez [Analyser les journaux et les métriques avec les paramètres de diagnostic](diagnostic-services.md) et [Agent in-process Java Application Insights dans Azure Spring Cloud](how-to-application-insights.md).
* Trois plages de routage CIDR (Classless InterDomain Routing) internes (au moins */16* chacune) que vous avez identifiées à l’usage du cluster Azure Spring Cloud. Ces plages CIDR ne seront pas directement routables et seront utilisées uniquement en interne par le cluster Azure Spring Cloud. Les clusters ne peuvent pas utiliser les plages *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16* ou *192.0.2.0/24* pour les plages CIDR Spring Cloud internes, ou les plages d’adresses IP incluses dans la plage d’adresses du réseau virtuel du cluster.
* Autorisation de service accordée au réseau virtuel. Le fournisseur de ressources Azure Spring Cloud nécessite une autorisation Propriétaire sur votre réseau virtuel pour accorder un principal de service dédié et dynamique sur le réseau virtuel en vue d’un déploiement et d’une maintenance supplémentaires. Pour obtenir des instructions et plus d’informations, consultez la section [Accorder l’autorisation du service au réseau virtuel](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).
* Si vous utilisez un pare-feu Azure ou une appliance virtuelle réseau, vous devez également répondre aux conditions préalables suivantes :
   * Règles de réseau et de nom de domaine complet (FQDN). Pour plus d’informations, consultez la [Conditions requises pour le réseau virtuel](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
   * Un itinéraire défini par l’utilisateur (UDR) unique appliqué à chacun des sous-réseaux de runtime de service et d’application de microservice Spring Boot. Pour plus d’informations sur les UDR, consultez [Routage du trafic de réseau virtuel](../virtual-network/virtual-networks-udr-overview.md). Avant le déploiement du cluster Spring Cloud, l’UDR doit être configuré avec un itinéraire pour *0.0.0.0/0* et pour destination votre appliance virtuelle réseau. Pour plus d’informations, consultez la section [Apporter votre propre table de routage](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) de l’article [Déployer Azure Spring Cloud dans un réseau virtuel](how-to-deploy-in-azure-virtual-network.md).

## <a name="review-the-configuration-file"></a>Examiner le fichier de configuration

Le fichier de configuration utilisé dans ce démarrage rapide provient de l’[architecture de référence Azure Spring Cloud](reference-architecture.md).

```hcl
provider "azurerm" {
    features {}
}

resource "azurerm_resource_group" "sc_corp_rg" {
    name      = var.resource_group_name
    location  = var.location
}

resource "azurerm_application_insights" "sc_app_insights" {
  name                = var.app_insights_name
  location            = var.location
  resource_group_name = var.resource_group_name
  application_type    = "web"
  depends_on = [azurerm_resource_group.sc_corp_rg]
}

resource "azurerm_spring_cloud_service" "sc" {
  name                = var.sc_service_name
  resource_group_name = var.resource_group_name
  location            = var.location

  network {
    app_subnet_id                   = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.app_subnet_id}"
    service_runtime_subnet_id       = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.service_runtime_subnet_id}"
    cidr_ranges                     = var.sc_cidr
  }

  timeouts {
      create = "60m"
      delete = "2h"
  }

  depends_on = [azurerm_resource_group.sc_corp_rg]
  tags = var.tags

}

resource "azurerm_monitor_diagnostic_setting" "sc_diag" {
  name                        = "monitoring"
  target_resource_id          = azurerm_spring_cloud_service.sc.id
  log_analytics_workspace_id = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.OperationalInsights/workspaces/${var.sc_law_id}"

  log {
    category = "ApplicationConsole"
    enabled  = true

    retention_policy {
      enabled = false
    }
  }

  metric {
    category = "AllMetrics"

    retention_policy {
      enabled = false
    }
  }
}
```

## <a name="apply-the-configuration"></a>Appliquer la configuration

Pour appliquer une configuration, suivez les étapes ci-dessous :

1. Enregistrez le fichier [variables.tf](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/terraform/brownfield-deployment/variable.tf) localement, puis ouvrez-le dans un éditeur.

1. Modifiez le fichier, puis ajoutez les valeurs suivantes :

   - ID d’abonnement du compte Azure sur lequel vous allez effectuer le déploiement.

   - Un emplacement de déploiement dans les régions où Azure Spring Cloud est disponible, comme indiqué dans [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all). Vous aurez besoin de la forme abrégée du nom de l’emplacement. Pour obtenir cette valeur, utilisez la commande suivante pour générer une liste d’emplacements Azure, puis recherchez la valeur **Nom** pour la région que vous avez sélectionnée.

   ```azurecli
   az account list-locations --output table
   ```

   - Nom du groupe de ressources sur lequel vous allez effectuer le déploiement.
   - Un nom de votre choix pour le déploiement de Spring Cloud.
   - Le nom du groupe de ressources du réseau virtuel dans lequel vous allez déployer vos ressources.
   - Le nom du réseau virtuel spoke (par exemple, *vnet-spoke*).
   - Le nom du sous-réseau que doit utiliser le service d’application Spring Cloud (par exemple, *snet-app*).
   - Le nom du sous-réseau que doit utiliser le service de runtime Spring Cloud (par exemple, *snet-runtime*).
   - Le nom de l’espace de travail d’Azure Log Analytics.
   - Les plages CIDR de votre réseau virtuel que doit utiliser Azure Spring Cloud (par exemple, *XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16*).
   - Les paires clé/valeur à appliquer comme étiquettes à toutes les ressources qui prennent en charge les étiquettes. Pour plus d’informations, consultez [Utiliser des étiquettes pour organiser vos ressources Azure et votre hiérarchie de gestion](../azure-resource-manager/management/tag-resources.md).

1. Exécutez la commande ci-dessous pour initialiser les modules de Terraform :

   ```bash
   terraform init
   ```

1. Exécutez la commande suivante pour créer le plan de déploiement Terraform :

   ```bash
   terraform plan -out=springcloud.plan
   ```

1. Exécutez la commande suivante pour appliquer le plan de déploiement Terraform :

   ```bash
   terraform apply springcloud.plan
   ```

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Vous pouvez utiliser le portail Azure pour vérifier les ressources déployées, ou Azure CLI ou un script Azure PowerShell pour lister les ressources déployées.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous prévoyez d’utiliser d’autres guides de démarrage rapide et tutoriels, vous pouvez conserver ces ressources. Quand vous n’en avez plus besoin, supprimez les ressources créées dans cet article en utilisant la commande suivante.

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une instance Azure Spring Cloud dans un réseau virtuel existant à l’aide de Terraform, puis vous avez validé le déploiement. Pour plus d’informations sur Azure Spring Cloud, accédez aux ressources ci-dessous.

- Déployez l’un des exemples d’application suivants à partir des emplacements ci-dessous :
   - [Pet Clinic App with MySQL Integration](https://github.com/azure-samples/spring-petclinic-microservices) (microservices avec un backend mysql).
   - [Hello World simple](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
- Utilisez des [domaines personnalisés](tutorial-custom-domain.md) avec Azure Spring Cloud.
- Exposez des applications dans Azure Spring Cloud à Internet à l’aide d’[Azure Application Gateway](expose-apps-gateway-azure-firewall.md).
- Affichez l’[architecture de référence Azure Spring Cloud](reference-architecture.md) sécurisée de bout en bout, basée sur [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).
