---
title: Superviser une application dans la préversion d’Azure Container Apps
description: Découvrez comment les applications sont supervisées et journalisées dans Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: c9c324c90ad401d43be1e2d6768ea82121fc1ae8
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027830"
---
# <a name="monitor-an-app-in-azure-container-apps-preview"></a>Superviser une application dans la préversion d’Azure Container Apps

Azure Container Apps collecte une grande quantité de données sur votre application conteneur et les stocke avec [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md). Cet article décrit les journaux disponibles, et explique comment écrire et consulter les journaux.

## <a name="writing-to-a-log"></a>Écriture dans un journal

Quand vous écrivez dans les [flux de sortie standard (stdout) ou d’erreur standard (stderr)](https://wikipedia.org/wiki/Standard_streams), les agents de journalisation Container Apps écrivent des journaux pour chaque message.

Quand un message est journalisé, les informations suivantes sont collectées dans la table du journal :

| Propriété | Notes |
|---|---|
| `RevisionName` | |
| `ContainerAppName` | |
| `ContainerGroupID` | |
| `ContainerGroupName` | |
| `ContainerImage` | |
| `ContainerID` | Identificateur unique du conteneur. Vous pouvez utiliser cette valeur pour identifier les incidents du conteneur. |
| `Stream` | Indique si `stdout` ou `stderr` est utilisé pour la journalisation. |
| `EnvironmentName` | |

### <a name="simple-text-vs-structured-data"></a>Texte simple et données structurées

Vous pouvez journaliser une seule chaîne de texte ou une ligne de données JSON sérialisées. Les informations s’affichent différemment selon le type de données que vous journalisez.

| Type de données | Description |
|---|---|
| Une seule ligne de texte | Le texte apparaît dans la colonne `Log_s`. |
| JSON sérialisé | Les données sont analysées par l’agent de journalisation et affichées dans des colonnes qui correspondent aux noms de propriété de l’objet JSON. |

## <a name="viewing-logs"></a>Consultation des journaux

Les données journalisées via une application conteneur sont stockées dans la table personnalisée `ContainerAppConsoleLogs_CL` de l’espace de travail Log Analytics. Vous pouvez voir les journaux dans le portail Azure ou avec l’interface CLI.

Définissez le nom de votre groupe de ressources et l’espace de travail Log Analytics, puis récupérez le `LOG_ANALYTICS_WORKSPACE_CLIENT_ID` avec les commandes suivantes.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"

LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"

$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv
```

---

Utilisez la commande CLI suivante pour voir les journaux sur la ligne de commande.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'my-container-app' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'my-container-app' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" `
  --out table
```

---

La sortie suivante montre le type de réponse de la commande CLI.

```console
ContainerAppName_s    Log_s                 TableName      TimeGenerated
--------------------  --------------------  -------------  ------------------------
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:09:00.168Z
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:11:36.197Z
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:11:43.171Z
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Sécuriser votre application de conteneur](secure-app.md)
