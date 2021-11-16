---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/23/2021
ms.author: glenga
ms.openlocfilehash: 8d580bbfd87f1d1df9d36be95a98c65d80736806
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224301"
---
## <a name="language-support-details"></a>Détails de la prise en charge des langues 

Le tableau suivant répertorie les langues prises en charge par les fonctions qui peuvent s’exécuter sur Linux ou Windows. Il indique également si votre langue prend en charge la modification dans le Portail Azure. Le langage est basé sur l’option **Pile d’exécution** que vous choisissez lors de la [création de votre application de fonction dans le Portail Azure](../articles/azure-functions/functions-create-function-app-portal.md#create-a-function-app). Cela est identique à l'option `--worker-runtime` lors de l’utilisation de la commande `func init` dans Azure Functions Core Tools. 

| Langage | Pile d’exécution | Linux | Windows | Modification dans le portail<sup>1</sup> |
|:--- |:-- |:--|:--- |:--- |
| [Bibliothèque de classes C#](../articles/azure-functions/functions-dotnet-class-library.md)<sup>2</sup> |.NET|✓ |✓ | | 
| [Script C#](../articles/azure-functions/functions-reference-csharp.md) | .NET | ✓ |✓ |✓ |
| [JavaScript](../articles/azure-functions/functions-reference-node.md) | Node.js |✓ |✓ | ✓ |
| [Python](../articles/azure-functions/functions-reference-python.md) | Python |✓ | | |
| [Java](../articles/azure-functions/functions-reference-java.md) | Java |✓ |✓ | |
| [PowerShell](../articles/azure-functions/functions-reference-powershell.md) |PowerShell Core | |✓ |✓ |
| [TypeScript](../articles/azure-functions/functions-reference-node.md) | Node.js |✓ |✓ |  |
| [Go/Rust/other](../articles/azure-functions/functions-custom-handlers.md) | Gestionnaires personnalisés |✓ |✓ | |

<sup>1</sup> En cas d’exécution sur Linux, la modification dans le portail est uniquement prise en charge dans un [Plan dédié (App Service)](../articles/azure-functions/dedicated-plan.md).   
<sup>2</sup> Dans le portail, vous ne pouvez actuellement pas créer d’applications de fonction qui s’exécutent sur .NET 5.0. Pour en savoir plus, consultez [Développer et publier des fonctions .NET 5 à l’aide d’Azure Functions](../articles/azure-functions/dotnet-isolated-process-guide.md). 

Pour plus d’informations, consultez [Prise en charge du système d’exploitation/Runtime](../articles/azure-functions/functions-scale.md#operating-systemruntime). 

Lorsque la modification dans le portail n’est pas disponible, vous devez [développer vos fonctions localement](../articles/azure-functions/functions-develop-local.md#local-development-environments).