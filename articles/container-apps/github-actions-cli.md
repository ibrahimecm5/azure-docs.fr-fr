---
title: Publier des révisions avec GitHub Actions dans la préversion d’Azure Container Apps
description: En savoir plus sur la création automatique de nouvelles révisions à l’aide de GitHub Actions dans la préversion d’Azure Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.openlocfilehash: c06ce3b75ac9b33d0dd82b74ba068a6a0039bbee
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027372"
---
# <a name="publish-revisions-with-github-actions-in-azure-container-apps-preview"></a>Publier des révisions avec GitHub Actions dans la préversion d’Azure Container Apps

Azure Container Apps vous permet d’utiliser GitHub Actions pour publier des [révisions](revisions.md) sur votre application de conteneur. À mesure que les validations sont envoyées à votre référentiel GitHub, une action GitHub est déclenchée, et met à jour l’image du [conteneur](containers.md) dans le registre de conteneurs. Une fois le conteneur mis à jour dans le registre, Azure Container Apps crée une nouvelle révision basée sur l’image de conteneur mise à jour.

:::image type="content" source="media/github-actions/azure-container-apps-github-actions.png" alt-text="Les modifications apportées à un référentiel GitHub déclenchent une action pour créer une nouvelle révision.":::

L’action GitHub est déclenchée par les validations dans une branche spécifique de votre référentiel. Lorsque vous créez le lien d’intégration, vous décidez quelle branche déclenche l’action.

## <a name="authentication"></a>Authentification

Lors de l’ajout ou de la suppression d’une intégration GitHub Actions, vous pouvez vous authentifier en transmettant un [jeton d’accès personnel](https://docs.github.com/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) GitHub ou en utilisant l’expérience de connexion GitHub interactive. L’expérience interactive ouvre un formulaire dans votre navigateur web et vous donne la possibilité de vous connecter à GitHub. Après authentification, un jeton est renvoyé à l’interface CLI qui est utilisée par GitHub pour le reste de la session active.

- Pour passer un jeton d’accès personnel, utilisez le paramètre `--token` et fournissez une valeur de jeton.
- Si vous choisissez d’utiliser une connexion interactive, utilisez le paramètre `--login-with-github` sans valeur.

## <a name="add"></a>Ajouter

La commande `containerapp github-action add` crée une intégration GitHub Actions avec votre application de conteneur.

La première fois que vous joignez GitHub Actions à votre application de conteneur, vous devez fournir un contexte de principal de service. La commande suivante vous montre comment créer un principal de service.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az ad sp create-for-rbac \
  --name <SERVICE_PRINCIPAL_NAME> \
  --role "contributor" \
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> /subscriptions/<SUBSCRIPTION_ID> \
  --sdk-auth
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az ad sp create-for-rbac `
  --name <SERVICE_PRINCIPAL_NAME> `
  --role "contributor" `
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> /subscriptions/<SUBSCRIPTION_ID> `
  --sdk-auth
```

---

Quand vous interagissez avec cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.

La valeur de retour de cette commande est une charge utile JSON, qui comprend les valeurs `tenantId`, `cliendId` et `clientSecret` du principal de service.

L’exemple suivant montre comment ajouter une intégration en utilisant un jeton d’accès personnel.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action add \
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" \
  --docker-file-path "./dockerfile" \
  --branch <BRANCH_NAME> \
  --registry-url <URL_TO_CONTAINER_REGISTRY> \
  --registry-user-name <REGISTRY_USER_NAME> \
  --registry-password <REGISTRY_PASSWORD> \
  --service-principal-client-id <CLIENT_ID> \
  --service-principal-client-secret <CLIENT_SECRET> \
  --service-principal-tenant-id <TENANT_ID> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action add `
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" `
  --docker-file-path "./dockerfile" `
  --branch <BRANCH_NAME> `
  --registry-url <URL_TO_CONTAINER_REGISTRY> `
  --registry-user-name <REGISTRY_USER_NAME> `
  --registry-password <REGISTRY_PASSWORD> `
  --service-principal-client-id <CLIENT_ID> `
  --service-principal-client-secret <CLIENT_SECRET> `
  --service-principal-tenant-id <TENANT_ID> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

Quand vous interagissez avec cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.

## <a name="show"></a>Afficher

La commande `containerapp github-action show` retourne les paramètres de configuration de GitHub Actions pour une application de conteneur.

L’exemple suivant montre comment ajouter une intégration en utilisant le jeton d’accès personnel.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action show `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME>
```

---

Quand vous interagissez avec cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.

Cette commande retourne une charge utile JSON avec les paramètres de configuration d’intégration de GitHub Actions.

## <a name="delete"></a>Supprimer

La commande `containerapp github-action remove` supprime les actions GitHub de l’application de conteneur.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action remove \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action remove `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

Quand vous interagissez avec cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.
