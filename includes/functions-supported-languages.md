---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2e5699a4522ef9da30b51a97eba49d7bc6e2c001
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025973"
---
|Langage                                 |1.x         |2.x| 3.x | 4.x |
|-----------------------------------------|------------|---| --- | --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>1</sup>)| Disponibilité générale (.NET Core 3.1)<br/>[GA (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) | GA (.NET 6.0) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|Disponibilité générale (Nœud 6)|Disponibilité générale (Nœuds 10 et 8)| Disponibilité générale (Nœuds 14, 12 et 10) | Disponibilité générale (Nœuds 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>1</sup>)| Disponibilité générale (.NET Core 3.1) | GA (.NET 6.0) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N/A|Disponibilité générale (Java 8)| GA (Java 11 et 8)| GA (Java 11 et 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N/A|GA (PowerShell Core 6)| Disponibilité générale (PowerShell 7.0 et Core 6)| Disponibilité générale (PowerShell 7.0)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N/A|Disponibilité générale (Python 3.7 et 3.6)| Disponibilité générale (Python 3.9, 3.8, 3.7 et 3.6)| Disponibilité générale (Python 3.9, 3.8)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript)<sup>2</sup> |N/A|GA| GA | GA |

<sup>1</sup> Les applications de bibliothèque de classes .NET qui ciblent le runtime version 2.x s’exécutent sur .NET Core 3.1 en mode de compatibilité .NET Core 2.x. Pour plus d’informations, consultez [Considérations relatives à Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Prise en charge via la transpilation vers JavaScript.

Pour plus d’informations sur les versions linguistiques prises en charge, consultez l’article du Guide du développeur spécifique à une langue.   
Pour plus d’informations sur les modifications prévues sur la prise en charge des langages, consultez la [Feuille de route Azure](https://azure.microsoft.com/roadmap/?tag=functions).
