---
title: 'Démarrage rapide : Bibliothèque de client de gestion Azure pour .NET'
description: Dans ce guide de démarrage rapide, vous allez vous familiariser avec la bibliothèque de client de gestion Azure pour .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/04/2021
ms.author: pafarley
ms.openlocfilehash: 9424766aea22b0dc8b8b43e2eb14be5f3621f688
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520270"
---
[Documentation de référence](/dotnet/api/overview/azure/cognitiveservices/management) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/8.0.0-preview/) | [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>Configuration C# requise

* Un abonnement Azure valide - [Créer un abonnement gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* [!INCLUDE [contributor-requirement](./contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./terms-azure-portal.md)]

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>Créer une application C#

Créez une application .NET Core. Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `azure-management-quickstart`. Cette commande crée un projet C# simple nommé « Hello World » avec un seul fichier source : *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Vous pouvez générer l’application avec :

```console
dotnet build
```

La sortie de génération ne doit contenir aucun avertissement ni erreur. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Dans le répertoire de l'application, installez la bibliothèque de client de gestion Azure pour .NET à l'aide de la commande suivante :

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Si vous utilisez l’IDE Visual Studio, la bibliothèque de client est disponible sous forme de package NuGet téléchargeable.

### <a name="import-libraries"></a>Importer des bibliothèques

Ouvrez *program.cs* et ajoutez les instructions `using` suivantes en haut du fichier :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Authentifier le client

Ajoutez les champs suivants à la racine de *program.cs* et complétez-les avec les valeurs appropriées à l'aide du principal de service que vous avez créé et des informations de votre compte Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Ensuite, dans votre méthode **Main**, utilisez ces valeurs pour générer un objet **CognitiveServicesManagementClient**. Cet objet est nécessaire pour toutes vos opérations de gestion Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Appeler les méthodes de gestion

Ajoutez le code suivant à votre méthode **Main** pour répertorier les ressources disponibles, créer un exemple de ressource, répertorier les ressources que vous possédez, puis supprimer l'exemple de ressource. Vous définirez ces méthodes au cours des étapes suivantes.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-c"></a>Créer une ressource Cognitive Services (C#)

Pour créer une ressource Cognitive Services et s'y abonner, utilisez la méthode **Create**. Cette méthode ajoute une nouvelle ressource facturable au groupe de ressources que vous transmettez. Lorsque vous créez votre nouvelle ressource, vous devez connaître le « type » du service que vous souhaitez utiliser, ainsi que son niveau tarifaire (ou référence SKU) et un emplacement Azure : La méthode suivante utilise tous ces arguments et crée une ressource.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Choisir un service et son niveau tarifaire

Lorsque vous créez une nouvelle ressource, vous devez connaître le « type » du service que vous souhaitez utiliser, ainsi que le [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou référence SKU) souhaité. Ces informations et d'autres vous serviront de paramètres lors de la création de la ressource. Vous trouverez la liste des « types » de ressources Cognitive Services disponibles en appelant la méthode suivante dans votre script :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Afficher vos ressources

Pour afficher toutes les ressources disponibles sous votre compte Azure (dans tous les groupes de ressources), utilisez la méthode suivante :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Supprimer une ressource

La méthode suivante supprime la ressource spécifiée du groupe de ressources donné.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

Si vous devez récupérer une ressource supprimée, consultez [Récupérer des ressources Cognitive Services supprimées](../../manage-resources.md).

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```dotnet
dotnet run
```

## <a name="see-also"></a>Voir aussi

* Consultez **[Authentifier des requêtes auprès d’Azure Cognitive Services](../../authentication.md)** qui explique la façon de travailler en toute sécurité avec Cognitive Services.
* Consultez **[Présentation d’Azure Cognitive Services](../../what-are-cognitive-services.md)** pour obtenir la liste des différentes catégories présentes dans Cognitive Services.
* Consultez **[Prise en charge du langage naturel](../../language-support.md)** pour afficher la liste des langages naturels pris en charge par Cognitive Services.
* Consultez **[Utiliser Cognitive Services en tant que conteneurs](../../cognitive-services-container-support.md)** pour comprendre comment utiliser Cognitive Services en local.
* Consultez **[Planifier et gérer les coûts pour Cognitive Services](../../plan-manage-costs.md)** afin d’estimer le coût d’utilisation de Cognitive Services.
* Pour plus d’informations sur le kit de développement logiciel (SDK) de gestion, consultez la **[documentation de référence du kit de développement logiciel (SDK) de gestion Cognitive Services](/dotnet/api/overview/azure/cognitiveservices/management)** .
