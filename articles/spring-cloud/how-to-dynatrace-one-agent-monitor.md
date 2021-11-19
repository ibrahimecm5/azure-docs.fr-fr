---
title: Guide pratique pour le monitoring d’applications Spring Boot avec Dynatrace Java OneAgent
description: Guide pratique pour l’utilisation de Dynatrace Java OneAgent dans le cadre du monitoring d’applications Spring Boot dans Azure Spring Cloud
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: 4e84cb9d33e850ed3148c41cff25c3fe17a93ae8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486241"
---
# <a name="how-to-monitor-spring-boot-apps-with-dynatrace-java-oneagent"></a>Guide pratique pour le monitoring d’applications Spring Boot avec Dynatrace Java OneAgent

Cet article explique comment utiliser Dynatrace OneAgent pour le monitoring d’applications Spring Boot dans Azure Spring Cloud.


Avec Dynatrace OneAgent, vous pouvez :

* Surveiller les applications avec Dynatrace OneAgent.
* Configurer Dynatrace OneAgent à l’aide de variables d’environnement.
* Vérifier toutes les données de surveillance à partir du tableau de bord Dynatrace.

La vidéo suivante présente Dynatrace OneAgent.

<br>

> [!VIDEO https://www.youtube.com/embed/PF0_SxuiZ2w]

## <a name="prerequisites"></a>Prérequis

* [Azure CLI](/cli/azure/install-azure-cli)
* [Un compte Dynatrace](https://www.dynatrace.com/)
* [Un jeton et jeton de locataire Dynatrace PaaS](https://www.dynatrace.com/support/help/reference/dynatrace-concepts/access-tokens/)

## <a name="activate-dynatrace-oneagent"></a>Activer Dynatrace OneAgent

Les sections suivantes décrivent comment activer Dynatrace OneAgent.

### <a name="prepare-your-azure-spring-cloud-environment"></a>Préparer votre environnement Azure Spring Cloud

1. Créer une instance Azure Spring Cloud
1. Créer une application que vous souhaitez signaler à Dynatrace en exécutant la commande suivante. Remplacer les espaces réservés *\<...>* par vos valeurs.
   ```azurecli
   az spring-cloud app create \
       --resource-group <your-resource-group-name> \
       --service <your-Azure-Spring-Cloud-name> \
       --name <your-application-name> \
       --is-public true 
   ```

### <a name="determine-the-values-for-the-required-environment-variables"></a>Déterminer les valeurs pour les variables d’environnement requises

Pour activer Dynatrace OneAgent sur votre instance Azure Spring Cloud, vous devez configurer quatre variables d’environnement : `DT_TENANT`, `DT_TENANTTOKEN`, `DT_CONNECTION_POINT`, et `DT_CLUSTER_ID`. Pour plus d’informations, consultez [Intégration de OneAgent dans Azure Spring Cloud](https://www.dynatrace.com/support/help/shortlink/azure-spring).

Pour les applications avec plusieurs instances, Dynatrace dispose de plusieurs méthodes pour les regrouper. `DT_CLUSTER_ID` est l’un des moyens. Pour plus d’informations, consultez [Personnaliser la structure des groupes de processus](https://www.dynatrace.com/support/help/how-to-use-dynatrace/process-groups/configuration/adapt-the-composition-of-default-process-groups/).

### <a name="add-the-environment-variables-to-your-application"></a>Ajouter les variables d'environnement à votre application

Vous pouvez ajouter les paires clé/valeur de la variable d’environnement à votre application à l’aide du portail Azure ou d’Azure CLI.

#### <a name="option-1-azure-cli"></a>Option 1 : Azure CLI

Pour ajouter les paires clé/valeur à l’aide d’Azure CLI, exécutez la commande suivante, en remplaçant les espaces réservés *\<...>* par les valeurs déterminées dans les étapes précédentes.

```azurecli
az spring-cloud app deploy \
    --resource-group <your-resource-group-name> \
    --service <your-Azure-Spring-Cloud-name> \
    --name <your-application-name> \
    --jar-path app.jar \
    --env \
        DT_TENANT=<your-environment-ID> \
        DT_TENANTTOKEN=<your-tenant-token> \
        DT_CONNECTION_POINT=<your-communication-endpoint>
```

#### <a name="option-2-portal"></a>Option 2 : Portail

Pour ajouter les paires clé/valeur à l’aide du portail Azure, procédez comme suit :

1. Accédez à la liste de vos applications existantes.

   :::image type="content" source="media/dynatrace-oneagent/existing-applications.png" alt-text="Capture d’écran du portail Azure montrant la section applications Azure Spring Cloud." lightbox="media/dynatrace-oneagent/existing-applications.png":::

1. Sélectionnez une application pour accéder à la page **vue d’ensemble** de l’application.

   :::image type="content" source="media/dynatrace-oneagent/overview-application.png" alt-text="Capture d’écran de la section vue d’ensemble de l’application." lightbox="media/dynatrace-oneagent/overview-application.png":::

1. Sélectionnez **Configurations** pour ajouter, mettre à jour ou supprimer des valeurs dans la section **Variables d’environnement** pour l’application.

   :::image type="content" source="media/dynatrace-oneagent/configuration-application.png" alt-text="Capture d’écran de l’onglet « variables d’environnement » de la section de configuration de l’application." lightbox="media/dynatrace-oneagent/configuration-application.png":::

## <a name="automate-provisioning"></a>Automatiser le provisionnement

À l’aide de Terraform ou d’un modèle de Azure Resource Manager (modèle ARM), vous pouvez également exécuter un pipeline d’automatisation de l’approvisionnement. Ce pipeline peut fournir une expérience pratique complète pour instrumenter et surveiller les nouvelles applications que vous créez et déployez.

### <a name="automate-provisioning-using-terraform"></a>Automatiser le provisionnement à l’aide de Terraform

Pour configurer les variables d’environnement dans un modèle Terraform, ajoutez le code suivant au modèle, en remplaçant les espaces réservés *\<...>* par vos propres valeurs. Pour plus d’informations, consultez [Gérer un déploiement Azure Spring Cloud actif](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment).

```terraform
environment_variables = {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>Automatiser le provisionnement à l’aide d’un modèle ARM

Pour configurer les variables d’environnement dans un modèle ARM, ajoutez le code suivant au modèle, en remplaçant les espaces réservés *\<...>* par vos propres valeurs. Pour plus d’informations, consultez [Microsoft.AppPlatform Spring/apps/déploiements](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json).

```ARM template
"environmentVariables": {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

## <a name="view-reports-in-dynatrace"></a>Afficher les rapports dans Dynatrace

Cette section décrit comment rechercher différents rapports dans Dynatrace.

> [!NOTE]
> Le menu et l’interface utilisateur Dynatrace évoluent progressivement. Pour cette raison, le tableau de bord peut être déplacé vers d’autres sections du site Web Dynatrace, et les captures d’écran suivantes peuvent ne pas refléter la version actuelle de l’interface utilisateur.

Une fois que vous avez ajouté les variables d’environnement à votre application, Dynatrace démarre la collecte des données. Pour afficher les rapports, utilisez le [menu Dynatrace](https://www.dynatrace.com/support/help/get-started/navigation/), accédez à **Services**, puis sélectionnez votre application.

Vous pouvez trouver le **Flux de service** à partir du **\<your-app-name>/Détails/Flux de service** :

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png" alt-text="Capture d’écran du rapport « Flux de service » Dynatrace." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png":::

Vous pouvez trouver les **Zones réactives de méthode** à partir de **\<your-app-name>/Détails/Zones réactives de méthode** :

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png" alt-text="Capture d’écran du rapport « Zones réactives de méthode »." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png":::

Vous pouvez trouver les **Instructions de base de données** à partir de **\<your-app-name>/Détails/Analyse du temps de réponse** :

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png" alt-text="Capture d’écran du rapport « Analyse du temps de réponse » et de la section « Instructions de base de données »." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png":::

Ensuite, accédez à la section **Analyse multidimensionnelle**.

Vous pouvez trouver les **Principales instructions de base de données** à partir de **Analyse multidimensionnelle/Principales instructions de base de données** :

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png" alt-text="Capture d’écran du rapport « Principales instructions de base de données »." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png":::

Vous trouverez la **Vue d’ensemble des exceptions** dans **Analyse multidimensionnelle/Vue d’ensemble des exceptions** :

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png" alt-text="Capture d’écran du rapport « Vue d’ensemble des exceptions »." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png":::

Ensuite, accédez à la section **Profilage et optimisation**.

Vous pouvez trouver l'**Analyse du processeur** à partir du **Profilage et optimisation/Analyse du processeur** :

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png" alt-text="Capture d’écran du rapport « Analyse du processeur »." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png":::

Ensuite, accédez à la section **Bases de données**.

Vous pouvez trouver le **Suivi arrière** à partir de **Bases de données/Détails/Suivi arrière** :

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png" alt-text="Capture d’écran du rapport de suivi arrière." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png":::

## <a name="view-dynatrace-oneagent-logs"></a>Voir les journaux de Dynatrace OneAgent

Par défaut, Azure Spring Cloud imprime les journaux de niveau d’*information* de Dynatrace OneAgent vers `STDOUT`. Les journaux sont mélangés avec les journaux des applications. Vous pouvez récupérer la version d’agent explicite à partir des journaux des applications.

Vous pouvez également obtenir un agent Dynatrace à partir des emplacements suivants :

* Journaux Azure Spring Cloud
* Azure Spring Cloud Application Insights
* Azure Spring Cloud LogStream

Vous pouvez appliquer certaines variables d’environnement fournies par Dynatrace pour configurer la journalisation pour Dynatrace OneAgent. Par exemple, `DT_LOGLEVELCON` contrôle le niveau de journalisation.

> [!CAUTION]
> Nous vous recommandons vivement de ne pas remplacer le comportement par défaut de journalisation fourni par Azure Spring Cloud pour Dynatrace. Sinon, les scénarios de journalisation ci-dessus seront bloqués et le ou les fichiers journaux pourront être perdus. Par exemple, vous ne devez pas transmettre les variables d’environnement `DT_LOGLEVELFILE` à vos applications.

## <a name="dynatrace-oneagent-upgrade"></a>Mise à jour de Dynatrace OneAgent

La mise à jour automatique Dynatrace OneAgent est désactivée et sera mise à niveau tous les trimestres avec le JDK. La mise à jour de l’agent peut affecter les scénarios suivants :

* Les applications existantes qui utilisent Dynatrace OneAgent avant la mise à jour sont inchangées, mais nécessitent un redémarrage ou un redéploiement pour faire appel à la nouvelle version de Dynatrace OneAgent.
* Les applications créées après la mise à jour utiliseront la nouvelle version de Dynatrace OneAgent.

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>Configuration du trafic sortant de l’instance d’injection dans VNet

Pour une instance d’injection dans le réseau virtuel Azure Spring Cloud, vous devez vous assurer que le trafic sortant pour les points de terminaison de communication Dynatrace est correctement configuré pour Dynatrace OneAgent. Pour plus d’informations sur la façon d’obtenir `communicationEndpoints`, consultez [API de déploiement : informations de connectivité GET pour OneAgent](https://www.dynatrace.com/support/help/dynatrace-api/environment-api/deployment/oneagent/get-connectivity-info/). Pour plus d’informations, consultez [Responsabilités du client pour l’exécution d’Azure Spring Cloud dans VNET](vnet-customer-responsibilities.md).

## <a name="dynatrace-support-model"></a>Modèle de prise en charge Dynatrace

Pour plus d’informations sur les limitations lors du déploiement de Dynatrace OneAgent en mode application uniquement, consultez la section [plateformes d’applications Cloud](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix/#cloud-application-platforms) de la [matrice de prise en charge des fonctionnalités et de la plateforme OneAgent](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix).

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser le suivi distribué avec Azure Spring Cloud](how-to-distributed-tracing.md)
