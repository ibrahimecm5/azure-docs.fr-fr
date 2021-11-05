---
title: Erreurs de nom de compte de stockage
description: Décrit les erreurs qui peuvent se produire lors de la spécification d’un nom de compte de stockage dans un modèle Azure Resource Manager (modèle ARM) ou un fichier Bicep.
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.openlocfilehash: 78bf097ccaf5f826dc20a11ee36375111b1a7354
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096789"
---
# <a name="resolve-errors-for-storage-account-names"></a>Résoudre les erreurs liées aux noms de compte de stockage

Cet article décrit les erreurs de nom qui peuvent se produire lors du déploiement d’un compte de stockage avec un modèle Azure Resource Manager (modèle ARM) ou un fichier Bicep.

## <a name="symptom"></a>Symptôme

Si le nom de votre compte de stockage contient des caractères interdits, comme une lettre majuscule ou un point d’exclamation, vous recevez un message d’erreur :

```Output
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Pour les comptes de stockage, vous devez fournir un nom pour la ressource qui est unique dans Azure. Si vous ne fournissez pas un nom unique, une erreur s’affiche :

```Output
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Si vous déployez un compte de stockage portant le même nom qu’un compte de stockage existant dans votre abonnement, mais à un autre emplacement, vous recevez une erreur. L’erreur indique que le compte de stockage existe déjà dans un autre emplacement. Supprimez le compte de stockage existant ou indiquez le même emplacement que le compte de stockage existant.

## <a name="cause"></a>Cause :

Ce nom doit comprendre entre 3 et 24 caractères, uniquement des lettres en minuscules et des nombres. Pas de lettres majuscules ou de caractères spéciaux. Le nom doit être unique.

## <a name="solution"></a>Solution

Vous pouvez créer un nom unique en concaténant votre convention d’affectation de noms avec le résultat de la fonction `uniqueString`.

Les modèles ARM utilisent [concat](../templates/template-functions-string.md#concat) avec [uniqueString](../templates/template-functions-string.md#uniquestring).

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Bicep utilise l'[interpolation de chaîne](../bicep/bicep-functions-string.md#concat) avec [uniqueString](../bicep/bicep-functions-string.md#uniquestring).

```bicep
resource storageAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
```

Vérifiez que le nom de votre compte de stockage ne contient pas plus de 24 caractères. La fonction `uniqueString` renvoie 13 caractères. Si vous voulez concaténer un préfixe ou un suffixe, fournissez une valeur de 11 caractères au maximum.

Les exemples suivants utilisent un paramètre qui crée un préfixe avec un maximum de 11 caractères.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The prefix value for the storage account name."
    }
  }
}
```

```bicep
@description('The prefix value for the storage account name.')
@maxLength(11)
param storageNamePrefix string = 'storage'
```

Vous concaténez ensuite la valeur du paramètre avec la valeur `uniqueString` pour créer un nom de compte de stockage.

```json
"name": "[concat(parameters('storageNamePrefix'), uniquestring(resourceGroup().id))]"
```

```bicep
name: '${storageNamePrefix}${uniqueString(resourceGroup().id)}'
```
