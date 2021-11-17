---
title: Comment utiliser l’agent Java In-process d’Application Insights dans Azure Spring Cloud
description: Comment surveiller les applications et les microservices à l’aide de l’agent Java In-process d’Application Insights dans Azure Spring Cloud.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 5652c28f90c697bc2394e2bb018bff3d8a7ddc7a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324666"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud"></a>Agent In-process Java d’Application Insights dans Azure Spring Cloud

Cet article explique comment surveiller les applications et les microservices en utilisant l’agent Java d’Application Insights dans Azure Spring Cloud.

À l’aide de cette fonctionnalité, vous pouvez :

* Lancer des recherches dans des données de suivi avec différents filtres
* Voir une carte des dépendances des microservices.
* Vérifier les performances des requêtes.
* Surveiller les métriques dynamiques en temps réel.
* Vérifier les échecs des requêtes.
* Vérifier les métriques de l’application.

Application Insights peut fournir de nombreuses perspectives observables, notamment :

* Mise en correspondance d’applications
* Performances
* Échecs
* Mesures
* Métriques temps réel
* Disponibilité

## <a name="enable-java-in-process-agent-for-application-insights"></a>Activation de l’agent Java In-process pour Application Insights

Activez l’agent Java In-Process à l’aide de la procédure suivante.

1. Accédez à la page de la vue d’ensemble du service de votre instance de service.
2. Sélectionnez l’entrée **Application Insights** dans le volet **Supervision**.
3. Sélectionnez **Activer Application Insights** pour activer l’intégration d’**Application Insights**.
4. Sélectionnez une instance d’Application Insights ou créez-en une.
   Ici, vous pouvez également personnaliser le taux d’échantillonnage de 0 à 100.
5. Sélectionnez **Enregistrer** pour enregistrer la modification.

## <a name="portal"></a>Portail

1. Accédez à la page **Service | Vue d’ensemble** et sélectionnez **Application Insights** dans la section **Supervision**.
2. Sélectionnez **Activer Application Insights** pour activer Application Insights dans Azure Spring Cloud.
3. Une fois **Application Insights** activé, vous pouvez configurer un taux d’échantillonnage facultatif (10,0 % par défaut).

   [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

> [!Note]
> N’utilisez pas la même IA dans différentes instances Azure Spring Cloud. Sinon, vous verrez des données mixtes.

## <a name="using-the-application-insights-feature"></a>Utilisation de la fonctionnalité Application Insights

Lorsque la fonctionnalité **Application Insights** est activée, vous pouvez :

Dans le volet de navigation de gauche, sélectionnez **Application Insights** pour accéder à la page **Vue d’ensemble** d’Application Insights.

* Sélectionnez **Plan de l’application** pour afficher l’état des appels entre les applications.

   [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Sélectionnez le lien entre le service client et `petclinic` pour voir plus de détails, par exemple une requête SQL.

* Dans le volet de navigation de gauche, sélectionnez **Performances** pour voir les données de performances de toutes les opérations des applications, ainsi que les dépendances et les rôles.

   [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* Dans le volet de navigation de gauche, sélectionnez **Échecs** pour voir s’il s’agit d’un événement inattendu pour vos applications.

   [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* Dans le volet de navigation de gauche, sélectionnez **Métriques** et sélectionnez l’espace de noms. Vous verrez les métriques Spring Boot et les métriques personnalisées, le cas échéant.

   [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* Dans le volet de navigation de gauche, sélectionnez **Métriques en temps réel** pour voir les métriques en temps réel de différentes dimensions.

   [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* Dans le volet de navigation de gauche, sélectionnez **Disponibilité** pour surveiller la disponibilité et la réactivité des applications Web en créant des [tests de disponibilité dans Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

   [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="automation"></a>Automatisation

Les sections suivantes décrivent l’automatisation de votre déploiement à l’aide de modèles Azure Resource Manager (modèles ARM) ou de Terraform.
    
### <a name="arm-templates"></a>Modèles ARM

Pour effectuer un déploiement à l’aide d’un modèle ARM, copiez le contenu suivant dans un fichier *azuredeploy.json*. Pour plus d’informations, consultez [Microsoft.AppPlatform Spring/monitoringSettings](/azure/templates/microsoft.appplatform/spring/monitoringsettings).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

### <a name="terraform"></a>Terraform

Pour un déploiement Terraform, utilisez le modèle suivant. Pour plus d’informations, consultez [azurerm_spring_cloud_service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_service).

```terraform
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_application_insights" "example" {
  name                = "tf-test-appinsights"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  application_type    = "web"
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "example-springcloud"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  sku_name            = "S0"

  config_server_git_setting {
    uri          = "https://github.com/Azure-Samples/piggymetrics"
    label        = "config"
    search_paths = ["dir1", "dir2"]
  }

  trace {
    connection_string = azurerm_application_insights.example.connection_string
    sample_rate       = 10.0
  }

  tags = {
    Env = "staging"
  }
}
```

## <a name="cli"></a>Interface de ligne de commande

Vous pouvez gérer Application Insights à l’aide des commandes Azure CLI. Dans les commandes suivantes, veillez à remplacer le texte *\<placeholder>* par les valeurs décrites. L’espace réservé *\<service-name>* fait référence au nom de votre instance Azure Spring Cloud.

Pour configurer Application Insights au moment de la création d’une instance Azure Spring Cloud, utilisez la commande suivante. Pour l’argument `app-insights`, vous pouvez spécifier un nom ou un ID de ressource Application Insights.

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name "serviceName" \
    --app-insights <name-or-resource-ID> \
    --sampling-rate <sampling-rate>
```

Vous pouvez également utiliser une chaîne de connexion Application Insights (choix par défaut) ou une clé d’instrumentation, comme indiqué dans l’exemple suivant.

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights-key <connection-string-or-instrumentation-key> \
    --sampling-rate <sampling-rate>
```

Pour désactiver Application Insights au moment de la création d’une instance Azure Spring Cloud, utilisez la commande suivante :

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --disable-app-insights
```

Pour vérifier les paramètres Application Insights d’une instance existante Azure Spring Cloud, utilisez la commande suivante :

```azurecli
az spring-cloud app-insights show \
    --resource-group <resource-group-name> \
    --name <service-name>
```

Pour activer Application Insights à l’aide d’une chaîne de connexion (choix par défaut) ou d’une clé d’instrumentation, utilisez la commande suivante :

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights-key <connection-string-or-instrumentation-key> \
    --sampling-rate <sampling-rate>
```

Pour activer Application Insights à l’aide du nom ou de l’ID de ressource, utilisez la commande suivante :

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights <name-or-resource-ID> \
    --sampling-rate <sampling-rate>
```

Pour désactiver Application Insights dans une instance Azure Spring Cloud existante, utilisez la commande suivante :

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --disable
```

## <a name="java-agent-updateupgrade"></a>Mise à jour/mise à niveau de l’agent Java

L’agent Java est régulièrement mis à jour/mis à niveau avec le JDK, ce qui peut affecter les scénarios suivants.

> [!Note]
> La version du JDK est mise à jour ou à niveau tous les trimestres.

* Les applications existantes qui utilisent l’agent Java avant la mise à jour/mise à niveau ne sont pas affectées.
* Les applications créées après la mise à jour/mise à niveau tirent profit de la nouvelle version de l’agent Java.
* Les applications existantes qui n’ont pas utilisé l’agent Java auparavant nécessitent un redémarrage ou un redéploiement pour pouvoir profiter de la nouvelle version de l’agent Java.

## <a name="java-agent-configuration-hot-loading"></a>Chargement à chaud de la configuration de l’agent Java

Azure Spring Cloud a activé un mécanisme de chargement à chaud pour ajuster les paramètres de configuration de l’agent sans redémarrer les applications.

> [!Note]
> Le mécanisme de chargement à chaud a un délai exprimé en minutes.

* Lorsque l’agent Java a été activé précédemment, les modifications apportées à l’instance Application Insights et/ou à SamplingRate ne nécessitent PAS le redémarrage des applications.
* Si vous activez l’agent Java, vous devez redémarrer les applications.
* Lorsque vous désactivez l’agent Java, les applications arrêtent d’envoyer toutes les données d’analyse après un retard en minutes. Vous pouvez redémarrer les applications pour supprimer l’agent de l’environnement de runtime Java.

## <a name="concept-matching-between-azure-spring-cloud-and-application-insights"></a>Correspondance du Concept entre Azure Spring Cloud et Application Insights

| Azure Spring Cloud | Application Insights                                         |
| ------------------ | ------------------------------------------------------------ |
| `App`              | * __Cartographie d’application__/Rôle<br />* __Métriques temps réel__/Rôle<br />* __Échecs__/Rôles/Cloud Rôle<br />* __Performances__/Rôles/Rôle Cloud |
| `App Instance`     | * __Cartographie d’application__/Instance de rôle<br />* __Métriques temps réel__/Nom du service<br />* __Échecs__/Rôles/Instance Cloud<br />* __Performances__/Rôles/Instance Cloud |

Le nom `App Instance` d’Azure Spring Cloud sera modifié ou généré dans les scénarios suivants :

* Vous créez une application.
* Vous déployez un fichier JAR ou un code source dans une application existante.
* Vous lancez un déploiement bleu/vert.
* Vous redémarrez l'application.
* Vous arrêtez le déploiement d’une application, puis vous la redémarrez.

Lorsque les données sont stockées dans Application Insights, elles contiennent l’historique des instances d’application Azure Spring Cloud créées ou déployées depuis l’activation de l’agent Java. Cela signifie que, dans le portail Application Insights, vous pouvez voir les données d’Application créées hier, puis supprimées dans un intervalle de temps spécifique, comme les dernières 24 heures. Les scénarios suivants illustrent son fonctionnement :

* Vous avez créé une application autour de 8:00 aujourd’hui à partir d’Azure Spring Cloud avec l’agent Java activé, puis vous avez déployé un fichier JAR dans cette application autour de 8:10 aujourd’hui. Après quelques tests, vous modifiez le code et déployez un nouveau fichier JAR dans cette application à 8:30 aujourd’hui. Ensuite, vous prenez un arrêt et, lorsque vous revenez à 11:00, vous vérifiez certaines données de Application Insights. Voici ce que vous verrez :
  * Trois instances dans la cartographie d’application avec des intervalles de temps au cours des dernières 24 heures, ainsi que les défaillances, les performances et les métriques.
  * Une instance dans la cartographie d’application mappage d’application avec un intervalle de temps au cours de la dernière heure, ainsi que les défaillances, les performances et les métriques.
  * Une instance dans les métriques en temps réel.
* Vous avez créé une application autour de 8:00 aujourd’hui à partir d’Azure Spring Cloud avec l’agent Java activé, puis vous avez déployé un fichier JAR dans cette application autour de 8:10 aujourd’hui. Environ 8:30, vous essayez d’effectuer un déploiement bleu/vert avec un autre fichier JAR. Actuellement, vous avez deux déploiements pour cette application. Après une interruption d’environ 11:00, vous souhaitez vérifier certaines données de Application Insights. Voici ce que vous verrez :
  * Trois instances dans la cartographie d’application avec des intervalles de temps au cours des dernières 24 heures, ainsi que les défaillances, les performances et les métriques.
  * Deux instances dans la cartographie d’application avec des intervalles de temps au cours de la dernière heure, ainsi que les défaillances, les performances et les métriques.
  * Deux instances de métriques en temps réel.
## <a name="see-also"></a>Voir aussi

* [Utiliser le suivi distribué avec Azure Spring Cloud](./how-to-distributed-tracing.md)
* [Analyser les journaux et les métriques](diagnostic-services.md)
* [Effectuer le streaming des journaux en temps réel](./how-to-log-streaming.md)
* [Plan de l’application](../azure-monitor/app/app-map.md)
* [Métriques temps réel](../azure-monitor/app/live-stream.md)
* [Performances](../azure-monitor/app/tutorial-performance.md)
* [Échecs](../azure-monitor/app/tutorial-runtime-exceptions.md)
* [Métriques](../azure-monitor/essentials/tutorial-metrics.md)
* [Journaux d’activité](../azure-monitor/logs/data-platform-logs.md)