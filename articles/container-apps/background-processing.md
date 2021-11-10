---
title: 'Tutoriel : déployer une application de traitement en arrière-plan avec Azure Container Apps (version préliminaire)'
description: Apprenez à créer une application qui s’exécute en continu en arrière-plan avec Azure Container Apps
services: container-apps
author: jorgearteiro
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: joarteir
ms.custom: ignite-fall-2021
ms.openlocfilehash: ca9fa15259222651570c1909f6c4557c79a87298
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027999"
---
# <a name="tutorial-deploy-a-background-processing-application-with-azure-container-apps-preview"></a>Tutoriel : déployer une application de traitement en arrière-plan avec Azure Container Apps (version préliminaire)

Azure Container Apps vous permet de déployer des applications sans avoir à exposer des points de terminaison publics. Dans ce tutoriel, vous déployez un exemple d’application qui lit des messages à partir d’une file d’attente stockage Azure et journalise les messages dans l’espace de travail Azure log Analytics. À l’aide de règles de mise à l’échelle des applications de conteneur, l’application peut augmenter ou diminuer en fonction de la longueur de la file d’attente stockage Azure. Lorsqu’il n’y a aucun message dans la file d’attente, l’application conteneur est mise à l’échelle à zéro.

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un environnement Container Apps pour déployer vos applications de conteneur
> * Créer une file d’attente stockage Azure pour envoyer des messages à l’application conteneur
> * Déployer votre application de traitement en arrière-plan en tant qu’application conteneur
> * Vérifier que les messages de la file d’attente sont traités par l’application conteneur

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour suivre ce tutoriel :

* **Azure CLI** : Azure CLI version 2.29.0 ou ultérieure doit être installée sur votre ordinateur local.
  * Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).
  
## <a name="setup"></a>Programme d’installation

Ce tutoriel utilise les variables d’environnement suivantes :

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOCATION="canadacentral"
CONTAINERAPPS_ENVIRONMENT="containerapps-env"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOCATION="canadacentral"
$CONTAINERAPPS_ENVIRONMENT="containerapps-env"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"
```

---

Créez une variable pour le nom de votre compte de stockage.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT="<MY_STORAGE_ACCOUNT_NAME>"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT="<storage account name>"
```

---

Remplacez l'espace réservé `<storage account name>` par votre propre valeur avant d’exécuter cet extrait de code. Les noms des comptes de stockage doivent être uniques dans Azure et comporter entre 3 et 24 caractères, uniquement des lettres minuscules ou des chiffres. Le compte de stockage sera créé à l’étape suivante.

Ensuite, connectez-vous à Azure à partir de l’interface CLI.

Exécutez la commande suivante et suivez les invites pour terminer le processus d’authentification.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az login
```

---

Pour vérifier que vous exécutez la dernière version de l’interface CLI, utilisez la commande `upgrade`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az upgrade
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az upgrade
```

---

Ensuite, installez l’extension Azure Container Apps dans l’interface CLI.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az extension add \
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az extension add `
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl
```

---

Maintenant que l’extension est installée, inscrivez l’espace de noms `Microsoft.Web`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az provider register --namespace Microsoft.Web
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az provider register --namespace Microsoft.Web
```

---

Vous utiliserez un groupe de ressources pour organiser les services liés à votre nouvelle application de conteneur. Créez le groupe à l’aide de la commande suivante :

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create \
  --name $RESOURCE_GROUP \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group create `
  --name $RESOURCE_GROUP `
  --location $LOCATION
```

---

Une fois l’interface CLI mise à niveau et un nouveau groupe de ressources disponible, vous pouvez créer un environnement Container Apps et déployer votre application de conteneur.

## <a name="create-an-environment"></a>Créer un environnement

Les environnements Azure Container Apps jouent le rôle de limite sécurisée autour d’un groupe d’applications de conteneur. Les différentes applications de conteneur d’un même environnement sont déployées dans le même réseau virtuel et écrivent les journaux dans le même espace de travail Log Analytics.

Azure Log Analytics permet de superviser votre application de conteneur et est nécessaire lors de la création d’un environnement Container Apps.

Créez un espace de travail Log Analytics à l’aide de la commande suivante :

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics workspace create \
  --resource-group $RESOURCE_GROUP \
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics workspace create `
  --resource-group $RESOURCE_GROUP `
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

---

Ensuite, récupérez l’ID client et la clé secrète client Log Analytics. Veillez à exécuter chaque requête séparément afin de disposer de suffisamment de temps pour que la requête se termine.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"')
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

---

Les applications de conteneur sont déployées dans un environnement Azure Container Apps. Pour créer l’environnement, exécutez la commande suivante :

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp env create \
  --name $CONTAINERAPPS_ENVIRONMENT \
  --resource-group $RESOURCE_GROUP \
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp env create `
  --name $CONTAINERAPPS_ENVIRONMENT `
  --resource-group $RESOURCE_GROUP `
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET `
  --location $LOCATION
```

---

## <a name="set-up-a-storage-queue"></a>Configurer une file d’attente de stockage

Création d’un compte Azure Storage.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage account create \
  --name $STORAGE_ACCOUNT \
  --resource-group $RESOURCE_GROUP \
  --location "$LOCATION" \
  --sku Standard_RAGRS \
  --kind StorageV2
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az storage account create `
  --name $STORAGE_ACCOUNT `
  --resource-group $RESOURCE_GROUP `
  --location $LOCATION `
  --sku Standard_RAGRS `
  --kind StorageV2
```

---

Ensuite, récupérez la chaîne de connexion de la file d’attente.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
QUEUE_CONNECTION_STRING=`az storage account show-connection-string -g $RESOURCE_GROUP --name $STORAGE_ACCOUNT --query connectionString --out json | tr -d '"'`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$QUEUE_CONNECTION_STRING=(az storage account show-connection-string -g $RESOURCE_GROUP --name $STORAGE_ACCOUNT --query connectionString --out json | tr -d '"')
```

---

Vous pouvez maintenant créer la file d’attente de messages.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage queue create \
  --name "myqueue" \
  --account-name $STORAGE_ACCOUNT \
  --connection-string $QUEUE_CONNECTION_STRING
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az storage queue create `
  --name "myqueue" `
  --account-name $STORAGE_ACCOUNT `
  --connection-string $QUEUE_CONNECTION_STRING
```

---

Enfin, vous pouvez envoyer un message à la file d’attente.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage message put \
  --content "Hello Queue Reader App" \
  --queue-name "myqueue" \
  --connection-string $QUEUE_CONNECTION_STRING
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az storage message put `
  --content "Hello Queue Reader App" `
  --queue-name "myqueue" `
  --connection-string $QUEUE_CONNECTION_STRING
```

---

## <a name="deploy-the-background-application"></a>Déployer l’application en arrière-plan

Créez un fichier nommé *queue.json* et collez le code de configuration suivant dans le fichier.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "defaultValue": "",
            "type": "String"
        },
        "queueconnection": {
            "defaultValue": "",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
    {
        "name": "queuereader",
        "type": "Microsoft.Web/containerApps",
        "apiVersion": "2021-03-01",
        "kind": "containerapp",
        "location": "[parameters('location')]",
        "properties": {
            "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
            "configuration": {
                "activeRevisionsMode": "single",
                "secrets": [
                {
                    "name": "queueconnection",
                    "value": "[parameters('queueconnection')]"
                }]
            },
            "template": {
                "containers": [
                    {
                        "image": "mcr.microsoft.com/azuredocs/containerapps-queuereader",
                        "name": "queuereader",
                        "env": [
                            {
                                "name": "QueueName",
                                "value": "myqueue"
                            },
                            {
                                "name": "QueueConnectionString",
                                "secretref": "queueconnection"
                            }
                        ]
                    }
                ],
                "scale": {
                    "minReplicas": 1,
                    "maxReplicas": 10,
                    "rules": [
                        {
                            "name": "myqueuerule",
                            "azureQueue": {
                                "queueName": "myqueue",
                                "queueLength": 100,
                                "auth": [
                                    {
                                        "secretRef": "queueconnection",
                                        "triggerParameter": "connection"
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        }
    }]
}

```

Vous pouvez désormais créer et déployer votre application conteneur.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az deployment group create --resource-group "$RESOURCE_GROUP" \
  --template-file ./queue.json \
  --parameters \
    environment_name="$CONTAINERAPPS_ENVIRONMENT" \
    queueconnection="$QUEUE_CONNECTION_STRING" \
    location="$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az deployment group create --resource-group "$RESOURCE_GROUP" `
  --template-file ./queue.json `
  --parameters `
    environment_name="$CONTAINERAPPS_ENVIRONMENT" `
    queueconnection="$QUEUE_CONNECTION_STRING" `
    location="$LOCATION"
```

---

Cette commande déploie l’application de démonstration à partir de l’image de conteneur publique appelée `mcr.microsoft.com/azuredocs/containerapps-queuereader` et définit les variables de secrets et d’environnements utilisées par l’application.

L’application met à l’échelle jusqu’à 10 réplicas en fonction de la longueur de la file d’attente définie dans la section `scale` du modèle ARM.

## <a name="verify-the-result"></a>Vérifier le résultat

L’application conteneur qui s’exécute en tant que processus en arrière-plan crée des entrées de journal dans Log analytics à mesure que les messages arrivent dans la file d’attente de Stockage Azure. Avant que vous puissiez interroger les données journalisées, vous devrez peut-être patienter quelques minutes avant que l’analyse n’arrive pour la première fois.

Exécutez la commande suivante pour afficher les messages journalisés. Cette commande requiert l’extension log Analytics. vous devez donc accepter l’invite d’installation de l’extension lorsque vous y êtes invité.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'queuereader' and Log_s contains 'Message ID'" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'queuereader' and Log_s contains 'Message ID'" `
  --out table
```

---

> [!TIP]
> Vous rencontrez des problèmes ? Faites-le nous savoir sur GitHub en ouvrant un problème dans le [dépôt Azure Container Apps](https://github.com/microsoft/azure-container-apps).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous avez terminé, nettoyez les ressources de vos applications de conteneur en exécutant la commande suivante pour supprimer votre groupe de ressources.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
  --resource-group $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group delete `
  --resource-group $RESOURCE_GROUP
```

---

Cette commande supprime l’ensemble du groupe de ressources, y compris l’instance des applications conteneur, le compte de stockage, l’espace de travail Log Analytics et toutes les autres ressources du groupe de ressources.
