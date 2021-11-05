---
title: CI/CD avec Azure Pipelines et des fichiers Bicep
description: Décrit comment configurer l’intégration continue dans Azure Pipelines à l’aide de fichiers Bicep. Il montre comment utiliser une tâche de Azure CLI pour déployer un fichier Bicep.
author: mumian
ms.topic: conceptual
ms.author: jgao
ms.date: 06/23/2021
ms.openlocfilehash: 30ab8481456dd03f4ecee597c9626c07772bbe3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059753"
---
# <a name="integrate-bicep-with-azure-pipelines"></a>Intégrer Bicep à Azure Pipelines

Vous pouvez intégrer des fichiers Bicep à Azure Pipelines pour l’intégration continue et la livraison continue (CI/CD). Dans cet article, vous allez apprendre à utiliser une tâche de pipeline Azure CLI pour déployer un fichier Bicep.

## <a name="prepare-your-project"></a>Préparation du projet

Cet article suppose que votre fichier Bicep et votre organisation Azure DevOps sont prêts pour la création du pipeline. Les étapes suivantes montrent comment vérifier que vous êtes prêt :

* Vous disposez d’une organisation Azure DevOps. Si vous n’en avez pas, [créez-en une gratuitement](/azure/devops/pipelines/get-started/pipelines-sign-up). Si votre équipe a déjà une organisation Azure DevOps, assurez-vous d’être un administrateur du projet Azure DevOps que vous souhaitez utiliser.

* Vous avez configuré une [connexion de service](/azure/devops/pipelines/library/connect-to-azure) pour votre abonnement Azure. Les tâches dans le pipeline s’exécutent sous l’identité du principal du service. Pour obtenir des instructions sur la création d’une connexion, consultez [Créer un projet DevOps](../templates/deployment-tutorial-pipeline.md#create-a-devops-project).

* Vous disposez d’un [fichier Bicep](./quickstart-create-bicep-use-visual-studio-code.md) qui définit l’infrastructure de votre projet.

## <a name="create-pipeline"></a>Création d’un pipeline

1. Si vous n’avez pas ajouté un pipeline précédemment, vous devez en créer un nouveau. À partir de votre organisation Azure DevOps, sélectionnez **Pipelines** et **Nouveau pipeline**.

   ![Ajouter un nouveau pipeline](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Spécifiez où votre code est stocké. L’illustration suivante montre la sélection du **Git Azure Repos**.

   ![Sélectionner une source de code](./media/add-template-to-azure-pipelines/select-source.png)

1. À partir de cette source, sélectionnez le référentiel contenant le code de votre projet.

   ![Sélectionner un référentiel](./media/add-template-to-azure-pipelines/select-repo.png)

1. Sélectionnez le type de pipeline à créer. Vous pouvez sélectionner **Pipeline de démarrage**.

   ![Sélectionner un pipeline](./media/add-template-to-azure-pipelines/select-pipeline.png)

Vous êtes prêt à ajouter une tâche Azure PowerShell ou le fichier de copie et à déployer des tâches.

## <a name="azure-cli-task"></a>Tâche Azure CLI

Le fichier YAML suivant crée un groupe de ressources et déploie un fichier Bicep à l’aide d’une [tâche de Azure CLI](/azure/devops/pipelines/tasks/deploy/azure-cli) :

```yml
trigger:
- master

name: Deploy Bicep files

variables:
  vmImageName: 'ubuntu-latest'

  azureServiceConnection: '<your-connection-name>'
  resourceGroupName: '<your-resource-group-name>'
  location: '<your-resource-group-location>'
  templateFile: './azuredeploy.bicep'
pool:
  vmImage: $(vmImageName)

steps:
- task: AzureCLI@2
  inputs:
    azureSubscription: $(azureServiceConnection)
    scriptType: bash
    scriptLocation: inlineScript
    inlineScript: |
      az --version
      az group create --name $(resourceGroupName) --location $(location)
      az deployment group create --resource-group $(resourceGroupName) --template-file $(templateFile)
```

Une tâche Azure CLI accepte les entrées suivantes :

* `azureSubscription` : indiquez le nom de la connexion de service que vous avez créée.  Voir [Préparation du projet](#prepare-your-project).
* `scriptType` : utilisez **bash**.
* `scriptLocation` : utilisez **inlineScript** ou **scriptPath**. Si vous spécifiez **scriptPath**, vous devrez également spécifier un paramètre `scriptPath`.
* `inlineScript` : spécifiez vos lignes de script.  Le script fourni dans l’exemple génère un fichier Bicep nommé *azuredeploy.bicep* et situé à la racine du dépôt.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’utilisation de Bicep avec Azure Pipelines et pour obtenir des conseils pratiques, consultez [Déployer des ressources Azure à l’aide de Bicep et Azure Pipelines](/learn/paths/bicep-azure-pipelines/) sur **Microsoft Learn**.
* Pour utiliser l’opération de simulation dans un pipeline, consultez [Test ARM templates with What-If in a pipeline](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/).
* Pour en savoir plus sur l’utilisation d’un fichier Bicep avec GitHub Actions, consultez [Déployer des fichiers Bicep avec GitHub Actions](./deploy-github-actions.md).