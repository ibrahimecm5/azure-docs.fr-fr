---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: f576840f87387ba6896c6fda3a5ef663cedaffaf
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132040412"
---
## <a name="supported-versions"></a>Versions prises en charge

Les versions du runtime Functions fonctionnent avec des versions spécifiques de .NET. Pour en savoir plus sur les versions de Functions, consultez [Vue d’ensemble des versions du runtime Azure Functions](../articles/azure-functions/functions-versions.md). La prise en charge des versions varie selon que vos fonctions s’exécutent in-process ou out-of-process (isolées). 

Le tableau suivant indique le niveau le plus élevé de .NET Core et .NET Framework pouvant être utilisé avec une version spécifique de Functions. 

| Version du runtime Functions | In-process<br/>([Bibliothèque de classes .NET](../articles/azure-functions/functions-dotnet-class-library.md)) | Out-of-process<br/>([Isolé .NET](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x | .NET 6.0 | .NET 6.0 |
| Functions 3.x | .NET Core 3.1 | .NET 5.0<sup>1</sup> |
| Functions 2.x | .NET Core 2.1<sup>2</sup> | n/a |
| Functions 1.x | .NET Framework 4.8 | n/a |


<sup>1</sup> Le processus de build nécessite également le [SDK .NET Core 3.1](https://dotnet.microsoft.com/download).   
<sup>2</sup> Pour plus d’informations, consultez [Considérations relatives à Functions v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).     

Pour obtenir les dernières informations sur les versions Azure Functions, notamment sur la suppression des versions mineures les plus anciennes, surveillez les [annonces Azure App Service](https://github.com/Azure/app-service-announcements/issues).
