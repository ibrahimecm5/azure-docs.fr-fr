---
title: Gérer les révisions dans la préversion d’Azure Container Apps
description: Gérez les révisions et la répartition du trafic dans Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: a1818ba143e05656cb94f70f0c849fc2dd39ba96
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026365"
---
# <a name="manage-revisions-azure-container-apps-preview"></a>Gérer les révisions dans la préversion d’Azure Container Apps

La prise en charge de plusieurs révisions dans Azure Container Apps vous permet de gérer le versioning et la quantité de [trafic envoyé à chaque révision](#traffic-splitting). Utilisez les commandes suivantes pour contrôler la façon dont votre application conteneur gère les révisions.

## <a name="list"></a>List

Listez toutes les révisions associées à votre application conteneur avec `az containerapp revision list`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision list \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  -o table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision list `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  -o table
```

---

Quand vous interagissez avec cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.

## <a name="show"></a>Afficher

Affichez les détails d’une révision spécifique en utilisant `az containerapp revision show`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision show \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision show `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

Quand vous interagissez avec cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.

## <a name="update"></a>Update

Pour mettre à jour une application conteneur, utilisez `az containerapp update`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp update \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp update `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

---

Quand vous interagissez avec cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.

## <a name="activate"></a>Activer

Activez une révision avec `az containerapp revision activate`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision activate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```poweshell
az containerapp revision activate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

Quand vous interagissez avec cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.

## <a name="deactivate"></a>Désactivation

Désactivez les révisions qui ne sont plus utilisées avec `az container app revision deactivate`. La désactivation arrête tous les réplicas en cours d’exécution d’une révision.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision deactivate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision deactivate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

Quand vous interagissez avec cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.

## <a name="restart"></a>Redémarrer

Les révisions d’application conteneur existantes n’ont pas accès à ce secret tant qu’elles ne sont pas redémarrées

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision restart \
  --name <REVISION_NAME> \
  --app <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision restart `
  --name <REVISION_NAME> `
  --app <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

Quand vous interagissez avec cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.

## <a name="set-active-revision-mode"></a>Définir le mode de révision actif

Configurez si votre application conteneur prend en charge plusieurs révisions actives.

La propriété `activeRevisionsMode` accepte deux valeurs :

- `multiple` : configure l’application conteneur pour autoriser plusieurs révisions actives.

- `single` : désactive automatiquement toutes les autres révisions quand une révision est activée. L’activation du mode `single` permet de désactiver automatiquement toutes les autres révisions quand une révision est créée à la suite d’un changement de niveau révision.

```json
{
  ...
  "resources": [
  {
    ...
    "properties": {
        "configuration": {
          "activeRevisionsMode": "multiple"
      }
    }
  }]
}
```

Le fragment de configuration suivant montre comment définir la propriété `activeRevisionsMode`. Les changements effectués sur cette propriété nécessitent le contexte du modèle ARM complet de l’application conteneur.

## <a name="traffic-splitting"></a>Fractionnement du trafic

En attribuant des valeurs de pourcentage, vous pouvez choisir comment équilibrer le trafic entre les différentes révisions. Les règles de répartition du trafic sont attribuées en définissant des pondérations sur les différentes révisions.

L’exemple suivant montre comment répartir le trafic sur trois révisions.

```json
{
  ...
  "configuration": {
    "ingress": {
      "traffic": [
        {
          "revisionName": <REVISION1_NAME>,
          "weight": 50
        },
        {
          "revisionName": <REVISION2_NAME>,
          "weight": 30
        },
        {
          "latestRevision": true,
          "weight": 20
        }
      ]
    }
  }
}
```

Chaque révision obtient le trafic en fonction des règles suivantes :

- 50 % des demandes sont envoyées à REVISION1
- 30 % des demandes sont envoyées à REVISION2
- 20 % des demandes sont envoyées à la dernière révision

La somme totale de toutes les pondérations de révision doit être égale à 100.

Dans cet exemple, remplacez les espaces réservés `<REVISION*_NAME>` par les noms de révision de votre application conteneur. Vous accédez aux noms de révision avec la commande [list](#list).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Prise en main](get-started.md)
