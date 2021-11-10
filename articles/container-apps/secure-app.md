---
title: Sécuriser une application dans Azure Container Apps (préversion)
description: Apprenez à sécuriser les applications dans Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: aca235582babedaa9bf6a7c44a835734ba4cb5dc
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026992"
---
# <a name="secure-an-app-in-azure-container-apps-preview"></a>Sécuriser une application dans Azure Container Apps (préversion)

Azure Container Apps permet à votre application de stocker de façon sécurisée des valeurs de configuration sensibles. Une fois définies au niveau de l’application, les valeurs sécurisées sont disponibles pour les conteneurs, dans les règles de mise à l’échelle et via Dapr.

- Les secrets sont délimités à une application, en dehors de toute révision spécifique d’une application.
- L’ajout, la suppression ou le changement de secrets ne génèrent pas de nouvelles révisions.
- Chaque révision d’application peut référencer un ou plusieurs secrets.
- Plusieurs révisions peuvent référencer le ou les mêmes secrets.

Lors de la mise à jour ou de la suppression d’un secret, vous pouvez répondre à ces changements de l’une des deux manières suivantes :

 1. Déployer une nouvelle révision.
 2. Redémarrer une révision existante.

Un secret mis à jour ou supprimé ne redémarre pas automatiquement une révision.

- Avant de supprimer un secret, déployez une nouvelle révision qui ne fait plus référence à l’ancien secret.
- Si vous changez une valeur de secret, vous devez redémarrer la révision pour utiliser la nouvelle valeur.

## <a name="defining-secrets"></a>Définition de secrets

# <a name="arm-template"></a>[Modèle ARM](#tab/arm-template)

Les secrets sont définis au niveau de l’application dans la section `resources.properties.configuration.secrets`.

```json
"resources": [
{
    ...
    "properties": {
        "configuration": {
            "secrets": [
            {
                "name": "queue-connection-string",
                "value": "<MY-CONNECTION-STRING-VALUE>"
            }],
        }
    }
}
```

Ici, une chaîne de connexion à un compte de stockage de file d’attente est déclarée dans le tableau `secrets`. Pour utiliser cette configuration, remplacez `<MY-CONNECTION-STRING-VALUE>` par la valeur de votre chaîne de connexion.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Les secrets sont définis avec le paramètre `--secrets`.

- Le paramètre accepte un jeu de paires nom/valeur délimité par des virgules.
- Chaque paire est délimitée par un signe égal (`=`).

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name queuereader \
  --environment "my-environment-name" \
  --image demos/queuereader:v1 \
  --secrets "queue-connection-string=$CONNECTION_STRING" \
```

Ici, une chaîne de connexion à un compte de stockage de file d’attente est déclarée dans le paramètre `--secrets`. La valeur de `queue-connection-string` provient d’une variable d’environnement nommée `$CONNECTION_STRING`.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Les secrets sont définis avec le paramètre `--secrets`.

- Le paramètre accepte un jeu de paires nom/valeur délimité par des virgules.
- Chaque paire est délimitée par un signe égal (`=`).

```azurecli
az containerapp create `
  --resource-group "my-resource-group" `
  --name queuereader `
  --environment "my-environment-name" `
  --image demos/queuereader:v1 `
  --secrets "queue-connection-string=$CONNECTION_STRING" `
```

Ici, une chaîne de connexion à un compte de stockage de file d’attente est déclarée dans le paramètre `--secrets`. La valeur de `queue-connection-string` provient d’une variable d’environnement nommée `$CONNECTION_STRING`.

---

## <a name="using-secrets"></a>Utilisation de secrets

Les secrets d’application sont référencés via la propriété `secretref`. Les valeurs de secret sont mappées à des secrets au niveau de l’application où la valeur `secretref` correspond au nom de secret déclaré au niveau de l’application.

## <a name="example"></a>Exemple

L’exemple suivant montre une application qui déclare une chaîne de connexion au niveau de l’application et qui est utilisée dans toute la configuration via `secretref`.

# <a name="arm-template"></a>[Modèle ARM](#tab/arm-template)

Dans cet exemple, la chaîne de connexion d’application est déclarée comme `queue-connection-string` et devient disponible ailleurs dans les sections de configuration.

:::code language="json" source="code/secure-app-arm-template.json" highlight="11,12,13,27,28,29,30,31,44,45,61,62":::

Ici, la variable d’environnement nommée `connection-string` obtient sa valeur du secret `queue-connection-string` au niveau de l’application. De plus, la configuration d’autorisation de la règle de mise à l’échelle Stockage File d’attente Azure utilise `queue-connection-string`, car une connexion est établie.

Pour éviter de valider des valeurs de secret dans le contrôle de code source avec votre modèle ARM, transmettez les valeurs de secret en tant que paramètres de modèle ARM.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Dans cet exemple, vous créez une application avec un secret référencé dans une variable d’environnement à l’aide d’Azure CLI.

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name myQueueApp \
  --environment "my-environment-name" \
  --image demos/myQueueApp:v1 \
  --secrets "queue-connection-string=$CONNECTIONSTRING" \
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

Ici, la variable d’environnement nommée `connection-string` obtient sa valeur du secret `queue-connection-string` au niveau de l’application en utilisant `secretref`.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Dans cet exemple, vous créez une application avec un secret référencé dans une variable d’environnement à l’aide d’Azure CLI.

```azurecli
az containerapp create `
  --resource-group "my-resource-group" `
  --name myQueueApp `
  --environment "my-environment-name" `
  --image demos/myQueueApp:v1 `
  --secrets "queue-connection-string=$CONNECTIONSTRING" `
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

Ici, la variable d’environnement nommée `connection-string` obtient sa valeur du secret `queue-connection-string` au niveau de l’application en utilisant `secretref`.

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Containers](containers.md)
