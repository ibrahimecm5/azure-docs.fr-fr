---
title: Automatiser les déploiements d’applications dans Azure Spring Cloud
description: Décrit comment utiliser la tâche Azure Spring Cloud pour Azure Pipelines.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 4f1075a690c806be76b0102c2954a4f5127dabee
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258332"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>Automatiser les déploiements d’applications dans Azure Spring Cloud

Cet article explique comment utiliser la [tâche Azure Spring Cloud pour Azure pipelines](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud) afin de déployer des applications.

Les outils d’intégration continue et de livraison continue vous permettent de déployer rapidement des mises à jour d’applications existantes avec un minimum d’effort et de risque. Azure DevOps vous aide à organiser et à contrôler ces travaux clés. 

La vidéo suivante décrit l’automatisation de bout en bout à l’aide des outils de votre choix, y compris Azure Pipelines.

<br>

> [!VIDEO https://www.youtube.com/embed/D2cfXAbUwDc?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

::: zone pivot="programming-language-csharp"

## <a name="create-an-azure-resource-manager-service-connection"></a>Créer une connexion de service Azure Resource Manager

Tout d’abord, créez une connexion de service du Gestionnaire de ressource Azure à votre projet Azure DevOps. Pour obtenir des instructions, consultez [Se connecter à Microsoft Azure](/azure/devops/pipelines/library/connect-to-azure). Veillez à sélectionner le même abonnement que celui utilisé pour votre instance de service Azure Spring Cloud.

## <a name="build-and-deploy-apps"></a>Créer et déployer des applications

Vous pouvez désormais générer et déployer vos projets à l’aide d’une série de tâches. Le modèle de Pipelines Azure suivant définit des variables, une tâche .NET Core pour générer l’application et une tâche Azure Spring Cloud pour déployer l’application.

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Deploy the planet app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(planetAppName)/publish-deploy-planet.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(planetMainEntry)

# Deploy the solar app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(solarAppName)/publish-deploy-solar.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(solarMainEntry)
```

::: zone-end
::: zone pivot="programming-language-java"

## <a name="set-up-an-azure-spring-cloud-instance-and-an-azure-devops-project"></a>Configurer une instance Azure Spring Cloud et un projet Azure DevOps

Tout d’abord, procédez comme suit pour configurer une instance Azure Spring Cloud existante à utiliser avec Azure DevOps.

1. Accédez à votre instance Azure Spring Cloud, puis créez une nouvelle application. 
1. Accédez au portail Azure DevOps, puis créez un nouveau projet sous l’organisation que vous avez choisie. Si vous ne possédez pas d’organisation Azure DevOps, vous pouvez en créer une gratuitement.
1. Sélectionnez **Repos**, puis importez le [code de démonstration Spring Boot](https://github.com/spring-guides/gs-spring-boot) dans le référentiel.

## <a name="create-an-azure-resource-manager-service-connection"></a>Créer une connexion de service Azure Resource Manager

Ensuite, créez une connexion de service du Gestionnaire de ressource Azure à votre projet Azure DevOps. Pour obtenir des instructions, consultez [Se connecter à Microsoft Azure](/azure/devops/pipelines/library/connect-to-azure). Veillez à sélectionner le même abonnement que celui utilisé pour votre instance de service Azure Spring Cloud.

## <a name="build-and-deploy-apps"></a>Créer et déployer des applications

Vous pouvez désormais générer et déployer vos projets à l’aide d’une série de tâches. Les sections suivantes présentent différentes options de déploiement de votre application à l’aide d’Azure DevOps.

### <a name="deploy-using-a-pipeline"></a>Déployer à l’aide d’un pipeline

Pour déployer un pipeline, procédez comme suit :

1. Sélectionnez **Pipelines**, puis créez un nouveau pipeline avec un modèle Maven.
1. Modifiez le fichier *azure-pipelines.yml* pour définir le `mavenPomFile` champ sur *« complete/pom.xml »* .
1. Sélectionnez **Afficher l’assistant** sur le côté droit, puis sélectionnez le modèle **Azure Spring Cloud** .
1. Sélectionnez la connexion de service que vous avez créée pour votre abonnement Azure, puis sélectionnez votre instance Spring Cloud et votre instance d’application. 
1. Désactiver **Utiliser le déploiement intermédiaire**.
1. Définissez **Package ou dossier** sur *complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar*.
1. Sélectionnez **Ajouter** pour ajouter cette tâche à votre pipeline.
  
   Les paramètres de votre pipeline doivent correspondre à l’image suivante.

   :::image type="content" source="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg" alt-text="Capture d’écran des paramètres du pipeline." lightbox="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg":::

   Vous pouvez également générer et déployer vos projets à l’aide du modèle de pipeline suivant. Cet exemple définit tout d’abord une tâche Maven pour générer l’application, suivie d’une deuxième tâche qui déploie le fichier JAR à l’aide de la tâche Azure Spring Cloud pour Azure Pipelines.

   ```yaml
   steps:
   - task: Maven@3
     inputs:
       mavenPomFile: 'complete/pom.xml'
   - task: AzureSpringCloud@0
     inputs:
       azureSubscription: '<your service connection name>'
       Action: 'Deploy'
       AzureSpringCloud: <your Azure Spring Cloud service>
       AppName: <app-name>
       UseStagingDeployment: false
       DeploymentName: 'default'
       Package: ./target/your-result-jar.jar
   ```

3. Sélectionnez **Enregistrer et exécuter**, puis attendez que la tâche se termine.

### <a name="blue-green-deployments"></a>Déploiement Blue-Green

Le déploiement présenté dans la section précédente reçoit immédiatement le trafic de l’application lors du déploiement. Cela vous permet de tester l’application dans l’environnement de production avant de recevoir le trafic des clients.

#### <a name="edit-the-pipeline-file"></a>Modifier le fichier du pipeline

Pour générer l’application de la même façon que celle présentée précédemment et la déployer dans un déploiement intermédiaire, utilisez le modèle suivant. Dans cet exemple, le déploiement intermédiaire doit déjà exister. Pour une autre approche, consultez [Stratégies de déploiement bleu-vert](concepts-blue-green-deployment-strategies.md).

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
    Package: ./target/your-result-jar.jar
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Set Production'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
```

#### <a name="use-the-releases-section"></a>Utiliser la section Publications

Les étapes suivantes vous montrent comment activer un déploiement Blue-Green à partir de la section **Publications** .

1. Sélectionnez **Pipelines**, créez un nouveau pipeline pour votre build Maven et publiez l’artefact.
   1. sélectionnez **Azure Repos Git** pour votre emplacement de code.
   1. Sélectionnez un référentiel dans lequel se trouve votre code.
   1. Sélectionnez le modèle **Maven** et modifiez le fichier pour définir le `mavenPomFile` champ sur *`complete/pom.xml`* .
   1. Sélectionnez **Afficher l’Assistant** sur le côté droit et sélectionnez le modèle **Publier les artefacts de build**.
   1. Définissez le **Chemin d’accès à publier** sur *complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar*.
   1. Sélectionnez **Enregistrer et exécuter**.

1. Sélectionnez **Publications**, puis **Créer une publication**.
1. Ajoutez un nouveau pipeline, puis sélectionnez **Travail vide** pour créer un travail.
1. Sous **Etapes**, sélectionnez la ligne **1 travail, 0 tâche**

   :::image type="content" source="media/spring-cloud-how-to-cicd/create-new-job.jpg" alt-text="Capture d’écran montrant où sélectionner pour ajouter une tâche à un travail.":::

   1. Sélectionnez le **+** pour ajouter une tâche au travail.
   1. Recherchez le modèle **Azure Spring Cloud**, puis sélectionnez **Ajouter** pour ajouter la tâche au travail.
   1. Sélectionnez **Déploiement Azure Spring Cloud :** pour modifier la tâche.
   1. Complétez cette tâche avec les informations de votre application, puis désactivez l'**Utilisation du déploiement intermédiaire**.
   1. Activez **Créer un déploiement intermédiaire s’il n’en existe pas**, puis entrez un nom dans **Déploiement**.
   1. Sélectionnez **Enregistrer** pour enregistrer cette tâche.
   1. Sélectionnez **OK**.
1. Sous l'onglet **Pipeline**, sélectionnez ensuite **Ajouter un artefact**.
   1. Sous **source (pipeline de Build),** sélectionnez le pipeline créé précédemment.
   1. Sélectionnez **Ajouter**, puis **Enregistrer**.
1. Sélectionnez **1 travail, 1 tâche** sous **Etapes**.
1. Accédez à la tâche de **Déploiement Azure Spring Cloud** à l'**Etape 1**, puis sélectionnez les points de suspension en regard de **Package ou dossier**.
1. Sélectionnez *spring-boot-complete-0.0.1-SNAPSHOT.jar* dans la boîte de dialogue, puis sélectionnez **OK**.

   :::image type="content" source="media/spring-cloud-how-to-cicd/change-artifact-path.jpg" alt-text="Capture d’écran de la boîte de dialogue « Sélectionner un fichier ou un dossier ».":::

1. Sélectionnez le **+** pour ajouter une autre tâche **Azure Spring Cloud** au travail.
2. Modifiez l’action pour **Définir le déploiement de production**.
3. Sélectionnez **Enregistrer**, puis **Créer une publication** pour démarrer automatiquement le déploiement. 

Pour vérifier l’état de publication actuel de votre application, sélectionnez **Afficher la publication**. Une fois cette tâche terminée, accédez au Portail Azure pour vérifier l’état de votre application.

### <a name="deploy-from-source"></a>Déployer à partir de la source

Pour déployer directement sur Azure sans étape de génération distincte, utilisez le modèle de pipeline suivant.

```yaml
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(Build.SourcesDirectory)
```

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Déployer votre première application Spring Boot dans Azure Spring Cloud](./quickstart.md)
