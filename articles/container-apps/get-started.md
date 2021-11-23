---
title: 'Démarrage rapide : Déployer votre première application de conteneur'
description: Déployez votre première application dans la préversion d’Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: bb2b24c080f7f8173280d5c31ca711d7241d04f1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552714"
---
# <a name="quickstart-deploy-your-first-container-app"></a>Démarrage rapide : Déployer votre première application de conteneur

La préversion d’Azure Container Apps vous permet d’exécuter des microservices et des applications conteneurisées sur une plateforme serverless. Avec Container Apps, vous bénéficiez des avantages de l’exécution de conteneurs tout en évitant les soucis de configuration manuelle de l’infrastructure cloud et des orchestrateurs de conteneurs complexes.

Dans ce guide de démarrage rapide, vous créez un environnement Container Apps sécurisé et déployez votre première application conteneur.

## <a name="prerequisites"></a>Prérequis

- Installez [Azure CLI](/cli/azure/install-azure-cli).

## <a name="setup"></a>Programme d’installation

Commencez par vous connecter à Azure à partir de l’interface CLI. Exécutez la commande suivante et suivez les invites pour terminer le processus d’authentification.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az login
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

Ensuite, définissez les variables d’environnement suivantes :

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
RESOURCE_GROUP="my-container-apps"
LOCATION="canadacentral"
LOG_ANALYTICS_WORKSPACE="my-container-apps-logs"
CONTAINERAPPS_ENVIRONMENT="my-environment"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-container-apps"
$LOCATION="canadacentral"
$LOG_ANALYTICS_WORKSPACE="my-container-apps-logs"
$CONTAINERAPPS_ENVIRONMENT="my-environment"
```

---

Une fois ces variables définies, vous pouvez créer un groupe de ressources pour organiser les services liés à votre nouvelle application conteneur.

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
  --location "$LOCATION"
```

---

Une fois l’interface CLI mise à niveau et un nouveau groupe de ressources disponible, vous pouvez créer un environnement Container Apps et déployer votre application de conteneur.

## <a name="create-an-environment"></a>Créer un environnement

Un environnement dans Azure Container Apps crée une limite sécurisée autour d’un groupe d’applications de conteneur. Les applications de conteneur déployées dans le même environnement sont déployées dans le même réseau virtuel et écrivent les journaux dans le même espace de travail Log Analytics.

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

Ensuite, récupérez l’ID client et la clé secrète client Log Analytics.

# <a name="bash"></a>[Bash](#tab/bash)

Veillez à exécuter chaque requête séparément afin de disposer de suffisamment de temps pour que la requête se termine.

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Veillez à exécuter chaque requête séparément afin de disposer de suffisamment de temps pour que la requête se termine.

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=(az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
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
  --location "$LOCATION"
```

---

## <a name="create-a-container-app"></a>Créer une application de conteneur

Maintenant que vous avez créé un environnement, vous pouvez déployer votre première application de conteneur. À l’aide de la commande `containerapp create`, déployez une image conteneur dans Azure Container Apps.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name my-container-app \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest \
  --target-port 80 \
  --ingress 'external' \
  --query configuration.ingress.fqdn
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name my-container-app `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest `
  --target-port 80 `
  --ingress 'external' `
  --query configuration.ingress.fqdn
```

---

En définissant `--ingress` sur `external`, vous rendez l’application conteneur disponible pour les demandes publiques.

Ici, la commande `create` retourne le nom de domaine complet de l’application conteneur. Copiez cet emplacement dans un navigateur web et vous verrez le message suivant.

:::image type="content" source="media/get-started/azure-container-apps-quickstart.png" alt-text="Votre premier déploiement Azure Container Apps.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’envisagez pas de continuer à utiliser cette application, vous pouvez supprimer l’instance Azure Container Apps et tous les services associés en supprimant le groupe de ressources.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
  --name $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group delete `
  --name $RESOURCE_GROUP
```

---

> [!TIP]
> Vous rencontrez des problèmes ? Faites-le nous savoir sur GitHub en ouvrant un problème dans le [dépôt Azure Container Apps](https://github.com/microsoft/azure-container-apps).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Environnements dans Azure Container Apps](environment.md)
