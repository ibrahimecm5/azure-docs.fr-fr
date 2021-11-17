---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: 7257f70acb8b931791f3f08437ba61b05e1b4f6d
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026676"
---
Cet article prend en charge la création de deux types de fonctions C# compilées : 

| Modèle d’exécution | Description |
| --- | --- |
| **[In-process](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| Le code de votre fonction s’exécute dans le même processus que le processus hôte Functions. Prend en charge .NET Core 3.1 et .NET 6.0. Pour en découvrir plus, consultez [Développer des fonctions de bibliothèque de classes C# à l’aide d’Azure Functions](../articles/azure-functions/functions-dotnet-class-library.md). |
| **[Processus isolé](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| Le code de votre fonction s’exécute dans un processus Worker .NET distinct. Prend en charge .NET Core 5.0 et .NET 6.0. Pour plus d’informations, consultez [Développer des fonctions de processus isolé en C#](../articles/azure-functions/dotnet-isolated-process-guide.md). |