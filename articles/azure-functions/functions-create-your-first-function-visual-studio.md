---
title: 'Démarrage rapide : Créer votre première fonction C# dans Azure à l’aide de Visual Studio'
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser Visual Studio pour créer une fonction C# déclenchée par HTTP et qui s’exécute sur .NET Core 3.1 et à la publier sur Azure Functions.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 11/03/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f, contperf-fy21q3-portal
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: 60e40b675ac8bc32328ea68786329b372f91d056
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028208"
---
# <a name="quickstart-create-your-first-c-function-in-azure-using-visual-studio"></a>Démarrage rapide : Créer votre première fonction C# dans Azure à l’aide de Visual Studio

Azure Functions vous permet d’exécuter votre code C# dans un environnement serverless dans Azure. 

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Utiliser Visual Studio pour créer un projet de bibliothèque de classes C# sur .NET 6.0.
> * Créer une fonction qui répond à des requêtes HTTP. 
> * Exécuter votre code localement pour vérifier le comportement de la fonction.
> * Déployer votre projet de code sur Azure Functions.
 
[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]

Le fait de suivre ce guide de démarrage rapide entraîne une faible dépense de quelques cents USD tout au plus dans votre compte Azure.

Si vous souhaitez consulter une version de cet article adaptée à Visual Studio Code, [cliquez ici](create-first-function-vs-code-csharp.md).

## <a name="prerequisites"></a>Prérequis

+ [Visual Studio 2022](https://azure.microsoft.com/downloads/), qui prend en charge .NET 6.0. Assurez-vous de sélectionner la charge de travail **Développement Azure** lors de l’installation. 

+ [Abonnement Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). Si ce n’est déjà fait, créez un [compte gratuit](https://azure.microsoft.com/free/dotnet/) avant de commencer.

## <a name="create-a-function-app-project"></a>Créer un projet d’application de fonction

Le modèle de projet Azure Functions dans Visual Studio crée un projet de bibliothèque de classe C# que vous pouvez publier dans une application de fonction dans Azure. Vous pouvez utiliser une application de fonction pour regrouper des fonctions en une unité logique afin de faciliter la gestion, le déploiement, la mise à l’échelle et le partage des ressources.

1. Dans le menu de Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Dans **Créer un projet**, entrez *functions* dans la zone de recherche, choisissez le modèle **Azure Functions**, puis sélectionnez  **Suivant**.

1. Dans **Configurer votre nouveau projet**, entrez un **Nom de projet**, puis sélectionnez **Créer**. Le nom d’application de la fonction doit être valide en tant qu’espace de noms C#, afin de ne pas utiliser des traits d’union, des traits de soulignement ou d’autres caractères non alphanumériques.

1. Pour les paramètres **Créer une application Azure Functions**, utilisez les valeurs du tableau suivant :

    | Paramètre      | Valeur  | Description                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Version de .NET** | **.NET 6** | Cette valeur crée un projet de fonction qui s’exécute dans un processus interne avec la version 4.x du runtime d’Azure Functions. Vous pouvez également choisir **.NET 6 (isolé)** pour créer un projet qui s’exécute dans un processus Worker distinct. La version 1.x d’Azure Functions prend en charge .NET Framework. Pour plus d’informations, consultez [Vue d’ensemble des versions du runtime Azure Functions](./functions-versions.md).   |
    | **Modèle de fonction** | **Déclencheur HTTP** | Cette valeur crée une fonction déclenchée par une requête HTTP. |
    | **Compte de stockage (AzureWebJobsStorage)**  | **Émulateur de stockage** | Étant donné qu’une application de fonction dans Azure nécessite un compte de stockage, celui-ci est attribué ou créé quand vous publiez votre projet sur Azure. Un déclencheur HTTP n’utilise pas de chaîne de connexion de compte Stockage Azure ; tous les autres types de déclencheurs nécessitent une chaîne de connexion de compte Stockage Azure valide. Lorsque vous sélectionnez cette option, l’émulateur Azurite est utilisé. |
    | **Niveau d’autorisation** | **Anonyme** | La fonction créée peut être déclenchée par n’importe quel client sans fournir une clé. Ce paramètre d’autorisation facilite le test de votre nouvelle fonction. Pour plus d’informations sur les clés et autorisations, consultez [Clés d’autorisation](./functions-bindings-http-webhook-trigger.md#authorization-keys) et [Liaisons HTTP et webhook](./functions-bindings-http-webhook.md). |
    
    :::image type="content" source="../../includes/media/functions-vs-tools-create/functions-project-settings-v4.png" alt-text="Paramètres de projet Azure Functions":::

    Veillez à définir le **Niveau d’autorisation** sur **Anonyme**. Si vous choisissez le niveau par défaut **Fonction**, vous êtes invité à présenter la [clé de fonction](./functions-bindings-http-webhook-trigger.md#authorization-keys) dans les requêtes d’accès à votre point de terminaison de fonction.

1. Sélectionnez **Créer** pour créer le projet de fonction et la fonction de déclencheur HTTP.

Visual Studio crée un projet et une classe qui contient un code réutilisable pour le type de fonction de déclencheur HTTP. Le code réutilisable envoie une réponse HTTP qui inclut une valeur de la chaîne de requête ou du corps de requête. L’attribut `HttpTrigger` spécifie que l’exécution de la fonction est déclenchée par une requête HTTP.

## <a name="rename-the-function"></a>Renommer la fonction

L’attribut de méthode `FunctionName` définit le nom de la fonction qui, par défaut, est `Function1`. Comme les outils ne vous permettent pas de remplacer le nom de fonction par défaut quand vous créez votre projet, prenez une minute pour attribuer un nom plus approprié à la classe de fonction, au fichier et aux métadonnées.

1. Dans l’**Explorateur de fichiers**, cliquez avec le bouton droit sur le fichier Function1.cs et renommez-le `HttpExample.cs`.

1. Dans le code, renommez la classe Function1 en `HttpExample`.

1. Dans la méthode `HttpTrigger` nommée `Run`, renommez l’attribut de méthode `FunctionName` en `HttpExample`. 

Votre définition de fonction doit maintenant ressembler au code suivant :

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs" range="15-18"::: 

Maintenant que vous avez renommé la fonction, vous pouvez la tester sur votre ordinateur local.

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Visual Studio s’intègre à Azure Functions Core Tools pour vous permettre de tester vos fonctions en local avec le runtime Azure Functions complet.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de publier le projet sur Azure.

## <a name="publish-the-project-to-azure"></a>Publication du projet sur Azure

Avant de pouvoir publier votre projet, vous devez disposer d’une application de fonction dans votre abonnement Azure. La publication Visual Studio crée une application de fonction pour vous la première fois que vous publiez votre projet.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="verify-your-function-in-azure"></a>Vérifier votre fonction dans Azure

1. Dans Cloud Explorer, votre nouvelle application de fonction doit être sélectionnée. Si ce n’est pas le cas, développez votre abonnement > **App Services**, puis sélectionnez votre nouvelle application de fonction.

1. Cliquez avec le bouton droit sur l’application de fonction et choisissez **Ouvrir dans le navigateur**. Cela ouvre la racine de votre application de fonction dans votre navigateur web par défaut et affiche la page qui indique que votre application de fonction est en cours d’exécution. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure-v4.png" alt-text="Application de fonction en cours d’exécution":::

1. Dans la barre d’adresses du navigateur, ajoutez la chaîne `/api/HttpExample?name=Functions` à l’URL de base et exécutez la demande.

    L’URL qui appelle la fonction à déclencheur HTTP est au format suivant :

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

1. Accédez à cette URL pour voir dans le navigateur une réponse à la demande GET distante retournée par la fonction, qui ressemble à l’exemple suivant :

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Réponse de la fonction dans le navigateur":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous envisagez d’utiliser d’autres guides de démarrage rapide, tutoriels ou l’un des services que vous avez créés dans ce guide de démarrage rapide, ne supprimez pas les ressources.

*Ressources* dans Azure fait référence aux applications de fonction, fonctions, comptes de stockage, et ainsi de suite. Elles sont rassemblées en *groupes de ressources*, et vous pouvez supprimer tous les éléments d’un groupe en supprimant le groupe. 

Vous avez créé des ressources pour effectuer ces démarrages rapides. Vous pouvez être facturé pour ces ressources, en fonction de [l’état de votre compte](https://azure.microsoft.com/account/) et de la [tarification du service](https://azure.microsoft.com/pricing/). 

[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé Visual Studio pour créer et publier une application de fonction C# dans Azure à l’aide d’une simple fonction à déclencheur HTTP. 

Passez à l’article suivant pour savoir comment ajouter une liaison de file d’attente de stockage Azure à votre fonction :
> [!div class="nextstepaction"]
> [Ajouter une liaison de file d’attente Stockage Azure à votre fonction](functions-add-output-binding-storage-queue-vs.md)

