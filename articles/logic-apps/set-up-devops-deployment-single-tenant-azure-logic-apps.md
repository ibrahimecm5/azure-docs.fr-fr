---
title: Configurer DevOps pour Azure Logic Apps monolocataire
description: Découvrez comment configurer le déploiement DevOps pour les workflows dans Azure Logic Apps monolocataire.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: bfc901e3fa9c3a3f266f9cddffde84c4d1461c8f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425498"
---
# <a name="set-up-devops-deployment-for-single-tenant-azure-logic-apps"></a>Configurer le déploiement DevOps pour Azure Logic Apps monolocataire

Cet article explique comment déployer un projet d’application logique monolocataire à partir de Visual Studio Code sur votre infrastructure en utilisant des outils et de processus DevOps. Selon que vous préférez GitHub ou Azure DevOps pour le déploiement, choisissez la voie et les outils qui conviennent le mieux à votre scénario. Vous pouvez utiliser les exemples inclus qui contiennent des exemples de projets d’application logique ainsi que des exemples de déploiement Azure avec GitHub ou Azure DevOps. Pour plus d’informations sur DevOps pour un locataire unique, consultez [Vue d’ensemble du déploiement DevOps pour Azure Logic Apps monolocataire](devops-deployment-single-tenant-azure-logic-apps.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un projet d’application logique monolocataire créé avec [Visual Studio Code et l’extension Azure Logic Apps (standard)](create-single-tenant-workflows-visual-studio-code.md#prerequisites).

  Si vous n’avez pas encore configuré votre projet ou votre infrastructure d’application logique, vous pouvez utiliser les exemples de projets inclus pour déployer un exemple d’application et d’infrastructure, en fonction des options de source et de déploiement que vous préférez utiliser. Pour plus d’informations sur ces exemples de projets et les ressources incluses pour exécuter l’exemple d’application logique, consultez [Déployer votre infrastructure](#deploy-infrastructure).

- Si vous souhaitez effectuer un déploiement sur Azure, vous avez besoin d’une ressource **Application logique (standard)** existante créée dans Azure. Pour créer rapidement une ressource d’application logique vide, consultez [Créer des workflows d’application logique monolocataire - Portail](create-single-tenant-workflows-azure-portal.md).

<a name="deploy-infrastructure"></a>

## <a name="deploy-infrastructure-resources"></a>Déployer des ressources d’infrastructure

Si vous n’avez pas encore configuré de projet ou d’infrastructure d’application logique, vous pouvez utiliser les exemples de projets suivants pour déployer un exemple d’application et d’infrastructure, en fonction des options de source et de déploiement que vous préférez utiliser :

- [Exemple GitHub pour Azure Logic Apps monolocataire](https://github.com/Azure/logicapps/tree/master/github-sample)

  Cet exemple comprend un exemple de projet d’application logique pour Azure Logic Apps monolocataire ainsi que des exemples pour le déploiement Azure et pour GitHub Actions.

- [Exemple Azure DevOps pour Azure Logic Apps monolocataire](https://github.com/Azure/logicapps/tree/master/azure-devops-sample)

  Cet exemple comprend un exemple de projet d’application logique pour Azure Logic Apps monolocataire ainsi que des exemples pour le déploiement Azure et pour Azure Pipelines.
  
Les deux exemples incluent les ressources suivantes qu’une application logique utilise pour s’exécuter.

| Nom de la ressource | Obligatoire | Description |
|---------------|----------|-------------|
| Application logique (standard) | Yes | Cette ressource Azure contient les workflows qui s’exécutent dans Azure Logic Apps monolocataire. |
| Plan d’hébergement Functions Premium ou App Service | Yes | Cette ressource Azure spécifie les ressources d’hébergement à utiliser pour l’exécution de votre application logique, telles que le calcul, le traitement, le stockage, le réseau, etc. <p><p>**Important** : Dans l’expérience actuelle, la ressource **Application logique (standard)** nécessite le [plan d’hébergement **Workflow Standard**](logic-apps-pricing.md#standard-pricing), qui est basé sur le plan d’hébergement Functions Premium. |
| Compte Azure Storage | Oui, pour les flux de travail avec et sans état | Cette ressource Azure stocke les métadonnées, les clés de contrôle d’accès, l’état, les entrées, les sorties, l’historique des exécutions et d’autres informations sur vos flux de travail. |
| Application Insights | Facultatif | Cette ressource Azure fournit des fonctionnalités de supervision pour vos workflows. |
| Connexions d’API | Facultatif, s’il n’en existe aucune | Ces ressources Azure définissent toutes les connexions d’API managées que vos workflows utilisent pour exécuter des opérations de connecteur managé, comme Office 365, SharePoint, etc. <p><p>**Important** : Dans votre projet d’application logique, le fichier **connections.json** contient des métadonnées, des points de terminaison et des clés pour toutes les connexions d’API managées et fonctions Azure utilisées par vos workflows. Pour utiliser des connexions et des fonctions différentes dans chaque environnement, veillez à paramétriser le fichier **connections.json** et à mettre à jour les points de terminaison. <p><p>Pour plus d’informations, consultez [Ressources de connexion d’API et stratégies d’accès](#api-connection-resources). |
| Modèle Azure Resource Manager (ARM) | Facultatif | Cette ressource Azure définit un déploiement d’infrastructure de référence que vous pouvez réutiliser ou [exporter](../azure-resource-manager/templates/template-tutorial-export-template.md). Le modèle comprend également les stratégies d’accès requises, par exemple, pour utiliser des connexions d’API managées. <p><p>**Important** : L’exportation du modèle ARM n’inclut pas tous les paramètres associés pour les ressources de connexion d’API utilisées par vos workflows. Pour plus d’informations, consultez [Rechercher les paramètres de connexion d’API](#find-api-connection-parameters). |
||||

<a name="api-connection-resources"></a>

## <a name="api-connection-resources-and-access-policies"></a>Ressources de connexion d’API et stratégies d’accès

Dans Azure Logic Apps monolocataire, chaque ressource de connexion d’API ou managée dans vos workflows nécessite une stratégie d’accès associée. Cette stratégie a besoin de l’identité de votre application logique pour fournir les autorisations permettant d’accéder à l’infrastructure de connecteur managée. Les exemples de projets inclus comprennent un modèle ARM qui comporte toutes les ressources d’infrastructure nécessaires, y compris ces stratégies d’accès.

Le diagramme suivant montre les dépendances entre votre projet d’application logique et les ressources d’infrastructure :

![Diagramme conceptuel montrant les dépendances d’infrastructure pour un projet d’application logique dans le modèle Azure Logic Apps monolocataire.](./media/set-up-devops-deployment-single-tenant-azure-logic-apps/infrastructure-dependencies.png)

<a name="find-api-connection-parameters"></a>

### <a name="find-api-connection-parameters"></a>Rechercher les paramètres de connexion d’API

Si vos workflows utilisent des connexions d’API managées, l’utilisation de la fonctionnalité d’exportation de modèle n’inclut pas tous les paramètres associés. Dans un modèle ARM, chaque [définition de ressource de connexion d’API](logic-apps-azure-resource-manager-templates-overview.md#connection-resource-definitions) a le format général suivant :

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {}
}
```

Pour rechercher les valeurs que vous devez utiliser dans l’objet `properties` afin d’élaborer la définition de la ressource de connexion, vous pouvez utiliser l’API suivante pour un connecteur spécifique :

`GET https://management.azure.com/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/{connector-name}?api-version=2016-06-01`

Dans la réponse, recherchez l’objet `connectionParameters`, qui contient toutes les informations permettant d’élaborer la définition de ressource pour ce connecteur spécifique. L’exemple suivant illustre une définition de ressource pour une connexion managée SQL :

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {
      "displayName": "sqltestconnector",
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/sql"
      },
      "parameterValues": {
         "authType": "windows", 
         "database": "TestDB",
         "password": "TestPassword",
         "server": "TestServer",
         "username": "TestUserName"
      }
   }
}
```

Vous pouvez également vérifier la trace réseau en prévision de la création d’une connexion dans le concepteur Logic Apps. Recherchez l’appel `PUT` à l’API managée pour le connecteur comme décrit précédemment, puis examinez le corps de la demande afin d’obtenir toutes les informations dont vous avez besoin.

## <a name="deploy-logic-app-resources-zip-deploy"></a>Déployer des ressources d’application logique (déploiement zip)

Après avoir poussé votre projet d’application logique vers votre dépôt source, vous pouvez configurer des pipelines de build et de mise en production qui déploient des applications logiques sur une infrastructure à l’intérieur ou en dehors d’Azure.

### <a name="build-your-project"></a>Générer votre projet

Pour configurer un pipeline de build basé sur le type de projet de votre application logique, effectuez les actions correspondantes répertoriées dans le tableau suivant :

| Type de projet | Description et étapes |
|--------------|-----------------------|
| Basé sur NuGet | La structure de projet NuGet est basée sur .NET Framework. Pour générer ces projets, veillez à suivre les étapes de génération de .NET Standard. Pour plus d’informations, consultez la documentation [Créer un package NuGet avec MSBuild](/nuget/create-packages/creating-a-package-msbuild). |
| Basé sur un lot | Le projet d’extension basé sur un lot n’est pas propre à un langage et ne nécessite aucune étape de génération propre au langage. Vous pouvez utiliser n’importe quelle méthode pour compresser vos fichiers projet. <p><p>**Important** : Assurez-vous que votre fichier .zip contient les véritables artefacts de build, y compris tous les dossiers de flux de travail, les fichiers config tels que host.json, connections.json et tout autre fichier connexe. |
|||

### <a name="release-to-azure"></a>Publier sur Azure

Pour configurer un pipeline de mise en production qui effectue le déploiement sur Azure, choisissez l’option associée pour GitHub, Azure DevOps ou Azure CLI.

> [!NOTE]
> Azure Logic Apps ne prend pas en charge les emplacements de déploiement Azure.

#### <a name="github"></a>[GitHub](#tab/github)

Pour les déploiements GitHub, vous pouvez déployer votre application logique avec [GitHub Actions](https://docs.github.com/actions), par exemple, l’action GitHub dans Azure Functions. Pour effectuer cette action, vous devez transmettre les informations suivantes :

- Nom d’application logique à utiliser pour le déploiement
- Fichier zip qui contient vos véritables artefacts de build, y compris tous les dossiers de flux de travail, les fichiers config tels que host.json, connections.json et tout autre fichier connexe.
- Votre [profil de publication](../azure-functions/functions-how-to-github-actions.md#generate-deployment-credentials), qui est utilisé pour l’authentification

```yaml
- name: 'Run Azure Functions Action'
  uses: Azure/functions-action@v1
  id: fa
  with:
   app-name: 'MyLogicAppName'
   package: 'MyBuildArtifact.zip'
   publish-profile: 'MyLogicAppPublishProfile'
```

Pour plus d’informations, consultez la documentation [Livraison continue avec GitHub Actions](../azure-functions/functions-how-to-github-actions.md).

#### <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Pour les déploiements Azure DevOps, vous pouvez déployer votre application logique en utilisant la [tâche de déploiement d’application de fonction Azure](/azure/devops/pipelines/tasks/deploy/azure-function-app?view=azure-devops&preserve-view=true) dans Azure Pipelines. Pour effectuer cette action, vous devez transmettre les informations suivantes :

- Nom d’application logique à utiliser pour le déploiement
- Fichier zip qui contient vos véritables artefacts de build, y compris tous les dossiers de flux de travail, les fichiers config tels que host.json, connections.json et tout autre fichier connexe.
- Votre [profil de publication](../azure-functions/functions-how-to-github-actions.md#generate-deployment-credentials), qui est utilisé pour l’authentification

```yaml
- task: AzureFunctionApp@1
  displayName: 'Deploy logic app workflows'
  inputs:
     azureSubscription: 'MyServiceConnection'
     appType: 'workflowapp'
     appName: 'MyLogicAppName'
     package: 'MyBuildArtifact.zip'
     deploymentMethod: 'zipDeploy'
```

Pour plus d’informations, consultez la documentation [Déployer une fonction Azure avec Azure Pipelines](/azure/devops/pipelines/targets/azure-functions-windows).

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Si vous utilisez d’autres outils de déploiement, vous pouvez déployer votre application logique monolocataire à l’aide de l’interface de ligne de commande Azure. Avant de commencer, vous devez disposer des éléments suivants :

- Extension Azure CLI la plus récente installée sur votre ordinateur local.

  - Si vous n’êtes pas équipé de cette extension, consultez le [guide d’installation de votre système d’exploitation ou de votre plateforme](/cli/azure/install-azure-cli).

  - Si vous n’êtes pas sûr de disposer de la dernière version, suivez les [étapes pour vérifier votre environnement et la version de l’interface CLI](#check-environment-cli-version).

- Extension Azure Logic Apps monolocataire (Standard) *en préversion* pour Azure CLI.

  Si vous n’avez pas cette extension, suivez les [étapes pour l’installer](#install-logic-apps-cli-extension). Bien qu’Azure Logic Apps monolocataire soit mis à la disposition générale, l’extension Azure Logic Apps monolocataire pour Azure CLI est encore en préversion.

- Groupe de ressources Azure à utiliser pour le déploiement de votre application logique.

  Si vous ne disposez pas de ce groupe de ressources, suivez les [étapes pour le créer](#create-resource-group).

- Compte de stockage Azure à utiliser avec votre application logique pour la conservation des données et de l’historique des exécutions.

  Si vous ne disposez pas de ce compte de stockage, suivez les [étapes pour en créer un](/cli/azure/storage/account#az_storage_account_create).

<a name="check-environment-cli-version"></a>

##### <a name="check-environment-and-cli-version"></a>Vérifier l’environnement et la version de l’interface CLI

1. Connectez-vous au [portail Azure](https://portal.azure.com). Dans un terminal ou une fenêtre de commande, vérifiez que votre abonnement est actif en exécutant la commande [`az login`](/cli/azure/authenticate-azure-cli) :

   ```azurecli-interactive
   az login
   ```

1. Dans un terminal ou une fenêtre de commande, vérifiez votre version d’Azure CLI en exécutant la commande `az` avec le paramètre obligatoire suivant :

   ```azurecli-interactive
   az --version
   ```

1. Si vous ne disposez pas de la dernière version d’Azure CLI, mettez à jour votre installation en suivant le [guide d’installation pour votre système d’exploitation ou votre plateforme](/cli/azure/install-azure-cli).

   Pour plus d’informations sur la dernière version, consultez les [notes de publication les plus récentes](/cli/azure/release-notes-azure-cli?tabs=azure-cli).

<a name="install-logic-apps-cli-extension"></a>

##### <a name="install-azure-logic-apps-standard-extension-for-azure-cli"></a>Installer l’extension Azure Logic Apps (Standard) pour Azure CLI

Actuellement, seule la version *préversion* est disponible pour cette extension. Si vous n’avez pas déjà installé cette extension, exécutez la commande, `az extension add`, avec les paramètres requis suivants :

```azurecli-interactive
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

Pour récupérer la dernière extension, à savoir la version 0.1.2, exécutez les commandes suivantes pour supprimer l’extension existante, puis installez la dernière version à partir de la source :

```azurecli-interactive
az extension remove --name logicapp
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

> [!NOTE]
> Si une nouvelle version de l’extension est disponible, les versions actuelles et ultérieures affichent un message. Bien que cette extension soit en préversion, vous pouvez utiliser la commande suivante pour effectuer une mise à niveau vers la dernière version sans la supprimer manuellement et la réinstaller :
>
> `az logicapp upgrade`

<a name="create-resource-group"></a>

#### <a name="create-resource-group"></a>Créer un groupe de ressources

Si vous n’avez pas encore configuré un groupe de ressources pour votre application logique, créez-en un en exécutant la commande `az group create`. À moins que vous n’ayez déjà défini un abonnement par défaut pour votre compte Azure, veillez à utiliser le paramètre `--subscription` avec le nom ou l’identificateur de votre abonnement. Sinon, vous n’avez pas besoin d’utiliser le paramètre `--subscription`.

> [!TIP]
> Pour définir un abonnement par défaut, exécutez la commande suivante et remplacez `MySubscription` par le nom ou l’identificateur de votre abonnement.
>
> `az account set --subscription MySubscription`

Par exemple, la commande suivante crée un groupe de ressources nommé `MyResourceGroupName` à l’aide de l’abonnement Azure nommé `MySubscription` à l’emplacement `eastus` :

```azurecli
az group create --name MyResourceGroupName 
   --subscription MySubscription 
   --location eastus
```

Si votre groupe de ressources est correctement créé, la sortie affiche la valeur de `provisioningState` comme étant `Succeeded` :

```output
<...>
   "name": "testResourceGroup",
   "properties": {
      "provisioningState": "Succeeded"
    },
<...>
```

<a name="deploy-logic-app"></a>

##### <a name="deploy-logic-app"></a>Déployer l’application logique

Pour déployer votre artefact compressé sur un groupe de ressources Azure, exécutez la commande `az logicapp deployment` avec les paramètres obligatoires suivants :

> [!IMPORTANT]
> Assurez-vous que votre fichier zip contient les artefacts de votre projet à la racine. Ces artefacts comprennent tous les dossiers de flux de travail, les fichiers config tels que host.json, connections.json et tout autre fichier associé. N’ajoutez pas de dossiers supplémentaires ni ne placez d’artefacts dans des dossiers qui n’existent pas déjà dans la structure de votre projet. Par exemple, cette liste montre un exemple de structure de fichier MyBuildArtifacts.zip :
>
> ```output
> MyStatefulWorkflow1-Folder
> MyStatefulWorkflow2-Folder
> connections.json
> host.json
> ```

```azurecli-interactive
az logicapp deployment source config-zip --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --src MyBuildArtifact.zip
```

---

### <a name="release-to-containers"></a>Publier sur des conteneurs

Si vous conteneurisez votre application logique, le déploiement fonctionne essentiellement comme tout autre conteneur déployé et géré par vos soins.

Pour obtenir des exemples qui montrent comment implémenter un pipeline de déploiement et de build de conteneur de bout en bout, consultez [CI/CD pour les conteneurs](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/).

## <a name="next-steps"></a>Étapes suivantes

- [déploiement DevOps pour Azure Logic Apps monolocataire](devops-deployment-single-tenant-azure-logic-apps.md)

Nous aimerions que vous nous fassiez part de votre expérience avec Azure Logic Apps monolocataire.

- Pour tout bogue ou problème que vous rencontreriez, [créez un problème dans GitHub](https://github.com/Azure/logicapps/issues).
- Pour toute question, demande, commentaire ou retour d’expérience, utilisez de [formulaire de commentaires](https://aka.ms/logicappsdevops).
