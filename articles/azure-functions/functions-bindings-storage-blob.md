---
title: Déclencheur et liaisons de Stockage Blob Azure pour Azure Functions
description: Découvrez comment utiliser le déclencheur et les liaisons Stockage Blob Azure dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 5d93ebd083ccd887b8267c1bc9b82c6ba7b34c05
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432374"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Vue d’ensemble des liaisons de Stockage Blob Azure pour Azure Functions

Azure Functions s’intègre à [Stockage Azure](../storage/index.yml) via [des déclencheurs et des liaisons](./functions-triggers-bindings.md). L’intégration à Stockage Blob vous permet de créer des fonctions qui réagissent aux modifications de données d’objets Blob ainsi qu’à la lecture et à l’écriture de valeurs.

| Action | Type |
|---------|---------|
| Exécuter une fonction quand des données de stockage d’objets Blob sont modifiées | [Déclencheur](./functions-bindings-storage-blob-trigger.md) |
| Lire des données de stockage d’objets Blob dans une fonction | [Liaison d’entrée](./functions-bindings-storage-blob-input.md) |
| Autoriser une fonction à écrire des données de stockage d’objets Blob |[Liaison de sortie](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Ajouter à votre application de fonction

### <a name="functions-2x-and-higher"></a>Functions 2.x et versions ultérieures

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | L’installation du [package NuGet], version 3.x | |
| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) avec Visual Studio Code. |
| Script C# (en ligne uniquement dans le portail Azure)         | Ajout d’une liaison                            | Pour mettre à jour des extensions de liaison existantes sans avoir à republier votre application de fonction, voir [Mettre à jour vos extensions]. |

#### <a name="storage-extension-5x-and-higher"></a>Extension Stockage 5.x et ultérieur

Une nouvelle version de l’extension de liaisons de Stockage est désormais disponible. Elle introduit la possibilité de [se connecter à l’aide d’une identité plutôt que d’un secret](./functions-reference.md#configure-an-identity-based-connection). Pour obtenir un tutoriel sur la configuration de vos applications de fonction avec des identités managées, consultez le tutoriel sur la [création d’une application de fonction avec des connexions basées sur l’identité](./functions-identity-based-connections-tutorial.md). Pour les applications .NET, la nouvelle version de l’extension change également les types vers lesquels vous pouvez effectuer une liaison, en remplaçant les types `WindowsAzure.Storage` et `Microsoft.Azure.Storage` par des types plus récents [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs). Pour en savoir plus sur les différences de ces nouveaux types et comment migrer vers eux, consultez le [Guide de migration Azure.Storage.Blobs](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/storage/Azure.Storage.Blobs/AzureStorageNetMigrationV12.md).

Cette version de l’extension est disponible en installant le [package NuGet] version 5.x, ou elle peut être ajoutée à partir du groupe d’extensions v3 en ajoutant le code suivant dans votre fichier `host.json` :

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[3.3.0, 4.0.0)"
  }
}
```

Pour en savoir plus, consultez [Mettre à jour vos extensions].

[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Package NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Mettre à jour vos extensions]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Les applications Functions 1.x ont automatiquement une référence au package NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), version 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>Paramètres host.json

Cette section décrit les paramètres de configuration d’application de fonction disponibles pour les fonctions de cette liaison. Ces paramètres s’appliquent uniquement lors de l’utilisation de la [version d’extension 5.0.0 ou d’une version supérieure](#storage-extension-5x-and-higher). L’exemple de fichier host.json ci-dessous contient seulement les paramètres des versions 2.x et ultérieures pour cette liaison. Pour plus d’informations sur les paramètres de configuration de l’application de fonction dans les versions 2.x et ultérieures, consultez [Référence host.json pour Azure Functions](functions-host-json.md).

> [!NOTE]
> Cette section ne s’applique pas aux versions d’extension antérieures à 5.0.0. Pour ces versions antérieures, il n’y a pas de paramètre de configuration de l’application de fonction pour les blobs.

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (nombre de mémoires à tores magnétiques)|Nombre entier d’appels simultanés autorisés pour chaque fonction déclenchée par un objet blob. La valeur minimale autorisée est de 1.|

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter une fonction quand les données de stockage Blob changent](./functions-bindings-storage-blob-trigger.md)
- [Lire des données de stockage de blobs lors de l’exécution d’une fonction](./functions-bindings-storage-blob-input.md)
- [Écrire des données de stockage d’objets blob à partir d’une fonction](./functions-bindings-storage-blob-output.md)
