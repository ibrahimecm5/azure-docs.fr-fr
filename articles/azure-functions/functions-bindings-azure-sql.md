---
title: Liaisons Azure SQL pour les fonctions
description: Découvrez comment utiliser des liaisons de Azure SQL dans Azure Functions.
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: ece79028f5cf0211f7d7345d54e1ccdb096cc6c9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490740"
---
# <a name="azure-sql-bindings-for-azure-functions-overview-preview"></a>Vue d’ensemble des liaisons Azure SQL pour Azure Functions (préversion)

Cet ensemble d’articles explique comment utiliser des liaisons [Azure SQL](/azure/azure-sql/) dans Azure Functions. Azure Functions prend en charge les liaisons d’entrée et de sortie pour les produits Azure SQL et SQL Server.

| Action | Type |
|---------|---------|
| Lire des données à partir d’une base de données | [Liaison d’entrée](./functions-bindings-azure-sql-input.md) |
| Enregistrer des données dans une base de données |[Liaison de sortie](./functions-bindings-azure-sql-output.md) |

> [!NOTE]
> Cette référence concerne [Azure Functions versions 2.x et ultérieures](functions-versions.md). 
>
> Cette liaison requiert une connectivité à une base de données Azure SQL ou SQL Server.

## <a name="add-to-your-functions-app"></a>Ajouter à votre application Functions

### <a name="functions"></a>Fonctions

Pour utiliser le déclencheur et les liaisons, vous devez référencer le package approprié. Le package NuGet est utilisé pour les bibliothèques de classes .NET, tandis que le bundle d’extensions est utilisé pour tous les autres types d’applications.

| Langage                                        | Ajouter via...                                   | Notes 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installation de la [préversion package NuGet] | |
<!--| Script C#, Java, JavaScript, Python, PowerShell | L’inscription du [bundle d’extensions]          | Il est recommandé d’utiliser l’[extension Azure Tools] avec Visual Studio Code. | -->


[Package NuGet en préversion]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Sql
[core tools]: ./functions-run-local.md
[Bundle d’extensions]: ./functions-bindings-register.md#extension-bundles
[Extension Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack


## <a name="known-issues"></a>Problèmes connus

- Les liaisons de sortie sur des tables avec des colonnes de types de données `NTEXT`, `TEXT` ou `IMAGE` ne sont pas prises en charge et les Upserts de données échouent. Ces types [seront supprimés](https://docs.microsoft.com/sql/t-sql/data-types/ntext-text-and-image-transact-sql) dans une future version de SQL Server et ne sont pas compatibles avec la fonction `OPENJSON` utilisée par cette liaison de Azure Functions.
- Les [classements](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#Collation_Defn) sensibles à la case ne sont actuellement pas pris en charge. L’[élément GitHub #133](https://github.com/Azure/azure-functions-sql-extension/issues/133) suit la progression de ce problème.


## <a name="open-source"></a>Open source

Les liaisons Azure SQL pour les Azure Functions sont open source et disponibles dans le référentiel à l’adresse [https://github.com/Azure/azure-functions-sql-extension](https://github.com/Azure/azure-functions-sql-extension).


## <a name="next-steps"></a>Étapes suivantes

- [Lire des données à partir d’une base de données (liaison d’entrée)](./functions-bindings-azure-sql-input.md)
- [Enregistrer des données dans une base de données (liaison de sortie)](./functions-bindings-azure-sql-output.md)