---
title: Segments de nom et de type de ressource non valides
description: Décrit comment résoudre une erreur lorsque le nom et le type de la ressource n’ont pas le même nombre de segments.
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.openlocfilehash: c4b04c78f84dada1e41289d1523b9ee2c49bd4e5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404371"
---
# <a name="resolve-errors-for-resource-name-and-type-mismatch"></a>Résoudre les erreurs liées à la non-concordance du nom et du type de la ressource

Cet article explique comment résoudre l’erreur lorsque le format du nom de ressource ne correspond pas à celui du type de ressource.

## <a name="symptom"></a>Symptôme

Lors du déploiement d’un modèle, vous recevez une erreur avec le code d’erreur **InvalidTemplate**. Le message indique que le type et le nom de la ressource ne correspondent pas. Il suggère de corriger le nombre de segments dans le nom.

## <a name="cause"></a>Cause

Un type de ressource contient l’espace de noms du fournisseur de ressources et un ou plusieurs segments pour les types. Chaque segment représente un niveau dans la hiérarchie des ressources et est séparé par une barre oblique.

```
{resource-provider-namespace}/{type-segment-1}/{type-segment-2}
```

Le nom de la ressource contient un ou plusieurs segments séparés par des barres obliques. Le nombre de segments doit correspondre au nombre dans le type de ressource.

```
{name-segment-1}/{name-segment-2}
```

Si le type et le nom de la ressource contiennent un nombre différent de segments, vous obtenez cette erreur.

## <a name="solution"></a>Solution

Assurez-vous de bien comprendre le niveau du type de ressource. Par exemple, une ressource de coffre de clés a un type de ressource complet `Microsoft.KeyVault/vaults`. Vous pouvez ignorer l’espace de noms du fournisseur de ressources (**Microsoft.KeyVault**) et vous concentrer sur le type (**vaults**). Elle possède un segment.

Un secret de coffre de clés est une ressource enfant du coffre. Son type de ressource complet est `Microsoft.KeyVault/vaults/secrets`. Ce type de ressource a deux segments (**vaults/secrets**).

Pour spécifier un nom pour le coffre de clés, fournissez un seul segment, par exemple `examplevault123`. Pour spécifier un nom pour le secret, fournissez deux segments, par exemple `examplevault123/examplesecret`. Le premier segment indique le coffre de clés dans lequel ce secret est stocké.

L’exemple suivant montre un format valide pour le nom de la ressource.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
  ...
}
```

---

Une **erreur** s’affiche si vous avez fourni un nom avec plusieurs segments.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'contoso/examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "contoso/examplevault123",
  ...
}
```

---

Lorsque vous imbriquez une ressource enfant dans la ressource parente, fournissez simplement le segment supplémentaire. Le type et le nom complets de la ressource contiennent toujours les valeurs de la ressource parente, mais ils sont construits pour vous. Dans l’exemple suivant, le type est `secrets` et le nom est `examplesecret`.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
  resource kvsecret 'secrets' = {
    name: 'examplesecret'
    properties: {
     value: secretValue
    }
  }
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
  ...
  "resources": [
    {
      "type": "secrets",
      "apiVersion": "2019-09-01",
      "name": "examplesecret",
      "properties": {
        "value": "[parameters('secretValue')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', 'examplevault123')]"
      ]
    }
  ]
}
```

---

Lorsque vous définissez la ressource enfant en dehors du parent, fournissez le type de ressource complet. Pour JSON, indiquez le nom complet de la ressource.

Pour Bicep, utilisez la propriété `parent` et fournissez le nom symbolique de la ressource parente. Lorsque vous utilisez la propriété parent, le nom complet est construit pour vous, vous devez donc fournir le nom de la ressource enfant sous la forme d’un segment unique.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kvsecret 'Microsoft.KeyVault/vaults/secrets@2021-06-01-preview' = {
  name: 'examplesecret'
  parent: kv
  properties: {
     value: secretValue
  }
}

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults/secrets",
  "apiVersion": "2019-09-01",
  "name": "examplevault123/examplesecret",
  "properties": {
    "value": "[parameters('secretValue')]"
  },
  "dependsOn": [
    "[resourceId('Microsoft.KeyVault/vaults', 'examplevault123')]"
  ]
},
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
```

---

Pour plus d’informations, consultez [Définir le nom et le type des ressources enfants dans Bicep](../bicep/child-resource-name-type.md) ou [Définir le nom et le type des ressources enfants dans les modèles ARM](../templates/child-resource-name-type.md).