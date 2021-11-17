---
title: Guide pratique pour monitorer des applications Spring Boot avec l’agent Java AppDynamics (préversion)
titleSuffix: Azure Spring Cloud
description: Guide pratique pour l’utilisation de l’agent AppDynamics dans le cadre du monitoring d’applications Spring Boot s’exécutant dans Azure Spring Cloud.
author: KarlErickson
ms.author: jiec
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: 308869704426d8b62a92da3b739ce3b9218f860e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500846"
---
# <a name="how-to-monitor-spring-boot-apps-with-the-appdynamics-java-agent-preview"></a>Guide pratique pour monitorer des applications Spring Boot avec l’agent Java AppDynamics (préversion)

Cet article explique comment utiliser l’agent AppDynamics dans le cadre du monitoring d’applications Spring Boot s’exécutant dans Azure Spring Cloud.

Avec l’agent Java AppDynamics, vous pouvez :

- Surveillance des applications
- Configurer l’agent Java AppDynamics à l’aide de variables d’environnement
- Vérifier toutes les données de monitoring à partir du tableau de bord AppDynamics

La vidéo suivante présente l’agent in-process Java AppDynamics.

<br>

> [!VIDEO https://www.youtube.com/embed/4dZuRX5bNAs]

## <a name="prerequisites"></a>Prérequis

* [Azure CLI](/cli/azure/install-azure-cli)
* [Un compte AppDynamics](https://www.appdynamics.com/)

## <a name="activate-the-appdynamics-java-in-process-agent"></a>Activer l’agent in-process Java AppDynamics

Pour l’ensemble du workflow, vous devez :

* Activer l’agent in-process Java AppDynamics dans Azure Spring Cloud pour générer des données de métriques d’application.
* Connecter l’agent AppDynamics au contrôleur AppDynamics pour collecter et visualiser les données du contrôleur.

![Schéma montrant une application Spring Boot dans la zone « Azure Spring Cloud » avec une flèche bidirectionnelle la connectant à une zone « AppDynamics Agent », qui comporte également une flèche pointant vers la zone « AppDynamics Controller »](media/how-to-appdynamics-java-agent-monitor/appdynamics-activation.jpg)

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-cli"></a>Activer une application avec l’agent AppDynamics à l’aide d’Azure CLI

Pour activer une application par le biais d’Azure CLI, procédez comme suit.

1. Créez un groupe de ressources.
1. Créer une instance Azure Spring Cloud
1. Créez une application à l’aide de la commande suivante. Remplacer les espaces réservés *\<...>* par vos valeurs.

    ```azurecli
    az spring-cloud app create \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --is-public true
    ```

1. Créez un déploiement avec l’agent AppDynamics à l’aide de variables d’environnement.

    ```azurecli
    az spring-cloud app deploy \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --jar-path app.jar \
        --jvm-options="-javaagent:/opt/agents/appdynamics/java/javaagent.jar" \
        --env APPDYNAMICS_AGENT_APPLICATION_NAME=<your-app-name> \
              APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY=<your-agent-access-key> \
              APPDYNAMICS_AGENT_ACCOUNT_NAME=<your-agent-account-name> \
              APPDYNAMICS_AGENT_NODE_NAME=<your-agent-node-name> \
              APPDYNAMICS_AGENT_TIER_NAME=<your-agent-tier-name> \
              APPDYNAMICS_CONTROLLER_HOST_NAME=<your-AppDynamics-controller-host-name> \
              APPDYNAMICS_CONTROLLER_SSL_ENABLED=true \
              APPDYNAMICS_CONTROLLER_PORT=443
    ```

Azure Spring Cloud préinstalle l’agent Java AppDynamics sur le chemin d’accès */opt/agents/appdynamics/java/javaagent.jar*. Vous pouvez activer l’agent à partir des options JVM de vos applications, puis configurer l’agent à l’aide de variables d’environnement. Vous pouvez trouver des valeurs pour ces variables dans [Surveiller Azure Spring Cloud avec l’agent Java](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent). Pour plus d’informations sur la façon dont ces variables permettent d’afficher et d’organiser les rapports dans l’interface utilisateur AppDynamics, consultez [Niveaux et nœuds](https://docs.appdynamics.com/21.9/en/application-monitoring/tiers-and-nodes).

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-portal"></a>Activer une application avec l’agent AppDynamics à l’aide du portail Azure

Pour activer une application par le biais du portail Azure, procédez comme suit.

1. Accédez à votre instance Azure Spring Cloud dans le portail Azure.

1. Sélectionnez **Applications** dans la section **Paramètres** du volet de navigation gauche.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png" alt-text="Capture d’écran du Portail Azure montrant la section Applications" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png":::

1. Sélectionnez l’application pour accéder à la page **Vue d’ensemble**.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png" alt-text="Portail Azure capture d’écran de la page vue d’ensemble de l’application" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png":::

1. Sélectionnez **Configuration** dans le volet de navigation gauche pour ajouter, mettre à jour ou supprimer les variables d’environnement de l’application.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png" alt-text="Capture d’écran du Portail Azure montrant la section « Variables d’environnement » de la page de configuration de l’application" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png":::

1. Sélectionnez **Paramètres généraux** pour ajouter, mettre à jour ou supprimer les options JVM de l’application.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png" alt-text="Capture d’écran du Portail Azure montrant la section « Paramètres généraux » de la page de configuration de l’application, avec « Options JVM » en surbrillance" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png":::

## <a name="automate-provisioning"></a>Automatiser le provisionnement

Vous pouvez également exécuter un pipeline d’automatisation du provisionnement à l’aide de Terraform ou d’un modèle Azure Resource Manager (modèle ARM). Ce pipeline peut fournir une expérience pratique complète pour instrumenter et surveiller les nouvelles applications que vous créez et déployez.

### <a name="automate-provisioning-using-terraform"></a>Automatiser le provisionnement à l’aide de Terraform

Pour configurer les variables d’environnement dans un modèle Terraform, ajoutez le code suivant au modèle, en remplaçant les espaces réservés *\<...>* par vos propres valeurs. Pour plus d’informations, consultez [Gérer un déploiement Azure Spring Cloud actif](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment).

```terraform
resource "azurerm_spring_cloud_java_deployment" "example" {
  ...
  jvm_options = "-javaagent:/opt/agents/appdynamics/java/javaagent.jar"
  ...
    environment_variables = {
      "APPDYNAMICS_AGENT_APPLICATION_NAME" : "<your-app-name>",
      "APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY" : "<your-agent-access-key>",
      "APPDYNAMICS_AGENT_ACCOUNT_NAME" : "<your-agent-account-name>",
      "APPDYNAMICS_AGENT_NODE_NAME" : "<your-agent-node-name>",
      "APPDYNAMICS_AGENT_TIER_NAME" : "<your-agent-tier-name>",
      "APPDYNAMICS_CONTROLLER_HOST_NAME" : "<your-AppDynamics-controller-host-name>",
      "APPDYNAMICS_CONTROLLER_SSL_ENABLED" : "true",
      "APPDYNAMICS_CONTROLLER_PORT" : "443"
  }
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>Automatiser le provisionnement à l’aide d’un modèle ARM

Pour configurer les variables d’environnement dans un modèle ARM, ajoutez le code suivant au modèle, en remplaçant les espaces réservés *\<...>* par vos propres valeurs. Pour plus d’informations, consultez [Microsoft.AppPlatform Spring/apps/déploiements](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json).

```ARM template
"deploymentSettings": {
  "environmentVariables": {
    "APPDYNAMICS_AGENT_APPLICATION_NAME" : "<your-app-name>",
    "APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY" : "<your-agent-access-key>",
    "APPDYNAMICS_AGENT_ACCOUNT_NAME" : "<your-agent-account-name>",
    "APPDYNAMICS_AGENT_NODE_NAME" : "<your-agent-node-name>",
    "APPDYNAMICS_AGENT_TIER_NAME" : "<your-agent-tier-name>",
    "APPDYNAMICS_CONTROLLER_HOST_NAME" : "<your-AppDynamics-controller-host-name>",
    "APPDYNAMICS_CONTROLLER_SSL_ENABLED" : "true",
    "APPDYNAMICS_CONTROLLER_PORT" : "443"
  },
  "jvmOptions": "-javaagent:/opt/agents/appdynamics/java/javaagent.jar",
  ...
}
```

## <a name="review-reports-in-the-appdynamics-dashboard"></a>Consulter les rapports dans le tableau de bord AppDynamics

Cette section présente différents rapports dans AppDynamics.

La capture d’écran suivante présente une vue d’ensemble de vos applications dans le tableau de bord AppDynamics :

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg" alt-text="Capture d’écran AppDynamics montrant la page Applications" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg":::

Le **tableau de bord de l’application** affiche les informations globales pour chacune de vos applications, comme illustré dans les captures d’écran suivantes à l’aide d’exemples d’applications :

- `api-gateway`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg" alt-text="Capture d’écran AppDynamics montrant le tableau de bord d’application pour l’exemple d’application de passerelle API" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg":::

- `customers-service`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg" alt-text="Capture d’écran AppDynamics montrant le tableau de bord d’application pour l’exemple d’application customers-service" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg":::

La capture d’écran suivante vous montre comment obtenir des informations de base à partir du tableau de bord **Appels de base de données**.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg" alt-text="Capture d’écran AppDynamics montrant le tableau de bord Appels de base de données" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg":::

Vous pouvez également obtenir des informations sur les appels de base de données les plus lents, comme indiqué dans les captures d’écran suivantes :

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg" alt-text="Capture d’écran AppDynamics montrant le tableau de bord Appels de base de données les plus lents" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg":::

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg" alt-text="Capture d’écran AppDynamics montrant la page des instantanés corrélés accessible à partir de la page des appels de base de données les plus lents" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg":::

La capture d’écran suivante montre l’analyse d’utilisation de la mémoire dans la section **Tas** de la page **Mémoire** :

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg" alt-text="Capture d’écran AppDynamics montrant la section Tas de la page Mémoire" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg":::

Vous pouvez également voir le processus de garbage collection, comme illustré dans cette capture d’écran :

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg" alt-text="Capture d’écran AppDynamics montrant la section Garbage collection de la page Mémoire" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg":::

La capture d’écran qui suit **présente la page réglage**. 

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg" alt-text="Capture d’écran AppDynamics montrant la page Transactions lentes" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg":::

Vous pouvez définir plus de métriques pour la machine virtuelle Java (JVM), comme illustré dans cette capture d’écran de l’**Explorateur de métriques** :

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg" alt-text="Capture d’écran AppDynamics montrant l’Explorateur de métriques" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg":::

## <a name="view-appdynamics-agent-logs"></a>Afficher les journaux de l’agent AppDynamics

Par défaut, Azure Spring Cloud imprime les journaux de niveau d’*information* de l’agent AppDynamics vers `STDOUT`. Les journaux sont mélangés avec les journaux des applications. Vous pouvez récupérer la version d’agent explicite à partir des journaux des applications.

Vous pouvez également obtenir les journaux de l’agent AppDynamics aux emplacements suivants :

* Journaux Azure Spring Cloud
* Azure Spring Cloud Application Insights
* Azure Spring Cloud LogStream

## <a name="learn-about-appdynamics-agent-upgrade"></a>En savoir plus sur la mise à niveau de l’agent AppDynamics

L’agent AppDynamics sera mis à niveau régulièrement avec JDK (chaque trimestre). La mise à jour de l’agent peut affecter les scénarios suivants :

* Les applications existantes qui utilisent l’agent AppDynamics avant la mise à jour sont inchangées, mais nécessitent un redémarrage ou un redéploiement pour faire appel à la nouvelle version de l’agent AppDynamics.
* Les applications créées après la mise à jour utiliseront la nouvelle version de l’agent AppDynamics.

## <a name="configure-vnet-injection-instance-outbound-traffic"></a>Configurer le trafic sortant de l’instance d’injection de réseau virtuel

Pour les instances d’injection de réseau virtuel d’Azure Spring Cloud, assurez-vous que le trafic sortant est correctement configuré pour l’agent AppDynamics. Pour plus d’informations, consultez [Domaines SaaS et plages d’adresses IP](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges) et [Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel](vnet-customer-responsibilities.md).

## <a name="understand-the-limitations"></a>Comprendre les limitations

Pour comprendre les limitations de l’agent AppDynamics, consultez [Surveiller Azure Spring Cloud avec l’agent Java](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent).

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser l’agent In-process Java d’Application Insights dans Azure Spring Cloud](/azure/spring-cloud/how-to-application-insights)
