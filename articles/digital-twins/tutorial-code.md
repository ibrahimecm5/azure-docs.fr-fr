---
title: 'Tutoriel : Coder une application cliente'
titleSuffix: Azure Digital Twins
description: Suivez ce tutoriel pour apprendre à écrire le code minimal d’une application cliente Azure Digital Twins à l’aide du Kit de développement logiciel (SDK) .NET (C#).
author: baanders
ms.author: baanders
ms.date: 10/18/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: df99bfd1dfd07749b4906e9bd7f9f62ece68066b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501701"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Tutoriel : Codage avec les API Azure Digital Twins

Les développeurs travaillant avec Azure Digital Twins écrivent généralement des applications clientes pour interagir avec leur instance du service Azure Digital Twins. Ce tutoriel destiné aux développeurs fournit une introduction à la programmation par rapport au service Azure Digital Twins à l’aide du [SDK Azure Digital Twins pour .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). Il décrit étape par étape comment écrire une application console cliente C# à partir de rien.

> [!div class="checklist"]
> * Configurer le projet
> * Démarrer avec le code de projet   
> * Exemple de code complet
> * Nettoyer les ressources
> * Étapes suivantes

## <a name="prerequisites"></a>Prérequis

Ce tutoriel sur Azure Digital Twins utilise la ligne de commande pour la création et la configuration du projet. Ainsi, vous pouvez utiliser n’importe quel éditeur de code pour effectuer les exercices.

Pour commencer, il vous faut :
* Un éditeur de code.
* **.NET Core 3.1** sur votre ordinateur de développement. Vous pouvez télécharger cette version du kit SDK .NET Core pour plusieurs plateformes à partir du site [Télécharger .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

### <a name="prepare-an-azure-digital-twins-instance"></a>Préparer une instance Azure Digital Twins

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Configurer le projet

Une fois que vous êtes prêt à utiliser votre instance Azure Digital Twins, commencez à configurer le projet d’application cliente. 

Ouvrez une invite de commandes ou une autre fenêtre de console sur votre machine, puis créez un répertoire de projet vide dans lequel vous souhaitez stocker votre travail au cours de ce tutoriel. Nommez le répertoire comme vous le souhaitez (par exemple *DigitalTwinsCodeTutorial*).

Accédez au nouveau répertoire.

Une fois dans le répertoire du projet, **créez un projet d’application console .NET vide**. Dans la fenêtre de commande, vous pouvez exécuter la commande suivante afin de créer un projet C# minimal pour la console :

```cmd/sh
dotnet new console
```

Cette commande créera plusieurs fichiers dans votre répertoire, notamment un nommé *Program.cs* où vous écrirez la majeure partie de votre code.

Laissez la fenêtre de commande ouverte parce que vous allez l’utiliser tout au long du tutoriel.

Ensuite, **ajouter deux dépendances à votre projet** qui seront nécessaires pour utiliser Azure Digital Twins. La première est le package pour le [SDK Azure Digital Twins pour .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), tandis que la seconde fournit des outils facilitant l’authentification auprès d’Azure.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
```

## <a name="get-started-with-project-code"></a>Démarrer avec le code de projet

Dans cette section, vous allez commencer à écrire le code de votre nouveau projet d’application pour fonctionner avec Azure Digital Twins. Les actions couvertes sont les suivantes :
* Authentification auprès du service
* Chargement d’un modèle
* Interception des erreurs
* Création de jumeaux numériques
* Création de relations
* Interrogation de jumeaux numériques

Vous trouverez également une section contenant le code complet à la fin du tutoriel. Vous pouvez utiliser cette section en guise de référence pour vérifier votre programme au fur et à mesure.

Pour commencer, ouvrez le fichier *Program.cs* dans un éditeur de code de votre choix. Vous verrez un modèle de code minimal qui ressemble à ceci :

:::row:::
    :::column:::
        :::image type="content" source="media/tutorial-code/starter-template.png" alt-text="Capture d’écran d’un extrait d’exemple de code dans un éditeur de code." lightbox="media/tutorial-code/starter-template.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Tout d’abord, ajoutez des lignes `using` en haut du code pour tirer les dépendances nécessaires.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Azure_Digital_Twins_dependencies":::

Ensuite, vous allez ajouter du code à ce fichier pour activer certaines fonctionnalités. 

### <a name="authenticate-against-the-service"></a>S’authentifier auprès du service

La première chose que votre application doit faire, c’est s’authentifier auprès du service Azure Digital Twins. Vous pouvez ensuite créer une classe cliente de service pour accéder aux fonctions du SDK.

Pour vous authentifier, vous avez besoin du *nom d’hôte* de votre instance Azure Digital Twins.

Dans *Program.cs*, collez le code suivant sous la ligne « Hello, World ! » dans la méthode `Main`. Affectez à `adtInstanceUrl` le *nom d’hôte* de votre instance d’Azure Digital Twins.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Authentication_code":::

Enregistrez le fichier. 

Dans votre fenêtre de commande, exécutez le code avec cette commande : 

```cmd/sh
dotnet run
```

Cette commande restaure les dépendances lors de la première exécution, puis exécute le programme. 
* Si aucune erreur ne se produit, le programme imprime *Service client created - ready to go*.
* Étant donné qu’il n’y a pas encore de gestion des erreurs dans ce projet, si un problème quelconque se produit, une exception est levée par le code.

### <a name="upload-a-model"></a>Charger un modèle

Azure Digital Twins n’a aucun vocabulaire de domaine intrinsèque. C’est vous qui définissez, à l’aide de **modèles**, les types d’éléments de votre environnement que vous pouvez représenter dans Azure Digital Twins. Les [modèles](concepts-twins-graph.md) sont similaires aux classes dans les langages de programmation orientés objet. Ils fournissent des modèles de [jumeaux numériques](concepts-models.md) définis par l’utilisateur à suivre et à instancier ultérieurement. Ils sont écrits dans un langage de type JSON appelé **DTDL (Digital Twins Definition Language)** .

La première étape de la création d’une solution Azure Digital Twins consiste à définir au moins un modèle dans un fichier DTDL.

Dans le répertoire où vous avez créé votre projet, créez un fichier .json nommé *SampleModel.json*. Collez-y le corps de fichier suivant : 

:::code language="json" source="~/digital-twins-docs-samples/models/SampleModel.json":::

> [!TIP]
> Si vous utilisez Visual Studio pour ce tutoriel, vous souhaiterez peut-être sélectionner le nouveau fichier JSON et affecter la valeur *Copier si plus récent* ou *Toujours copier* à la propriété *Copier dans le répertoire de sortie* dans l’inspecteur de propriété. Cela permettra à Visual Studio de trouver le fichier JSON avec le chemin par défaut quand vous exécuterez le programme avec **F5** lors des étapes suivantes du tutoriel.

> [!TIP] 
> Il existe un [exemple de validateur DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator) indépendant du langage qui vous permet de vérifier les documents de modèle et la validité du DTDL. Il repose sur la bibliothèque de l’analyseur DTDL, qui est décrite en détail dans [Analyser et valider des modèles](how-to-parse-models.md).

Ensuite, ajoutez du code à *Program.cs* pour charger le modèle que vous avez créé dans votre instance Azure Digital Twins.

Tout d’abord, ajoutez quelques instructions `using` au début du fichier :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_dependencies":::

Ensuite, préparez l’utilisation des méthodes asynchrones dans le SDK de service C# en modifiant la signature de la méthode `Main` pour autoriser l’exécution asynchrone. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Async_signature":::

> [!NOTE]
> L’utilisation de `async` n’est pas strictement nécessaire, car le SDK fournit également des versions synchrones de tous les appels. Ce tutoriel utilise `async`.

Vient ensuite le premier bloc de code qui interagit avec le service Azure Digital Twins. Ce code charge le fichier DTDL que vous avez créé à partir de votre disque, puis le charge sur votre instance de service Azure Digital Twins. 

Collez le code suivant sous le code d’autorisation que vous avez ajouté plus tôt.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp_excerpt_model.cs" id="ClientExcerptModel":::

Dans votre fenêtre de commande, exécutez le programme avec cette commande : 

```cmd/sh
dotnet run
```
La mention « Upload a model » sera affichée dans la sortie, indiquant que ce code a été atteint, mais il n’existe pas encore de sortie pour préciser si le chargement a été effectué correctement.

Pour ajouter une instruction print indiquant tous les modèles chargés avec succès sur l’instance, ajoutez le code suivant juste après la section précédente :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Print_model":::

**Avant de réexécuter le programme pour tester ce nouveau code**, rappelez-vous que la dernière fois que vous avez exécuté le programme, vous avez déjà chargé votre modèle. Azure Digital Twins ne vous permet pas de charger deux fois le même modèle. Par conséquent, si vous tentez de charger à nouveau le même modèle, le programme doit lever une exception.

Avec ces informations en tête, réexécutez le programme avec cette commande dans votre fenêtre de commande :

```cmd/sh
dotnet run
```

Le programme doit lever une exception. Quand vous essayez de charger un modèle qui a déjà été chargé, le service retourne une erreur de « requête incorrecte » par le biais de l’API REST. Par conséquent, le SDK client Azure Digital Twins lèvera à son tour une exception pour chaque code de retour de service autre qu’un succès. 

La section suivante traite des exceptions de ce type et explique comment les gérer dans votre code.

### <a name="catch-errors"></a>Intercepter les erreurs

Pour empêcher que le programme se plante, vous pouvez ajouter du code d’exception autour du code de chargement du modèle. Encapsulez l’appel client existant `await client.CreateModelsAsync(typeList)` dans un gestionnaire try/catch comme suit :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_try_catch":::

À présent, si vous exécutez le programme avec `dotnet run` dans votre fenêtre de commande, vous verrez que vous obtenez un code d’erreur. La sortie du code de création de modèle indique l’erreur suivante :

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Capture d’écran d’une console montrant la sortie du programme, qui se solde par une erreur « 409 : Échec de la demande de service. État : 409 (conflit).».":::

Désormais, ce tutoriel encapsulera tous les appels aux méthodes de service dans des gestionnaires try/catch.

### <a name="create-digital-twins"></a>Créer des jumeaux numériques

Maintenant que vous avez chargé un modèle dans Azure Digital Twins, vous pouvez utiliser cette définition de modèle pour créer des **jumeaux numériques**. Les [jumeaux numériques](concepts-twins-graph.md) sont des instances d’un modèle ; ils représentent les entités au sein de votre environnement d’entreprise (par exemple les capteurs dans une ferme, les salles d’un bâtiment ou les voyants d’une voiture). Cette section crée quelques jumeaux numériques basés sur le modèle que vous avez chargé.

Ajoutez le code suivant à la fin de la méthode `Main` pour créer et initialiser trois jumeaux numériques basés sur ce modèle.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Initialize_twins":::

Dans votre fenêtre de commande, exécutez le programme avec `dotnet run`. Dans la sortie, recherchez les messages print indiquant que sampleTwin-0, sampleTwin-1 et sampleTwin-2 ont été créés. 

Ensuite, réexécutez le programme. 

Notez qu’aucune erreur n’est générée quand les jumeaux sont créés la deuxième fois, bien qu’ils existent déjà après la première exécution. Contrairement à la création de modèle, la création de jumeau est, au niveau REST, un appel *PUT* avec une sémantique *upsert*. L’utilisation de ce type d’appel REST signifie que si un jumeau existe déjà, une tentative de création du même jumeau remplacera simplement le jumeau d’origine. Aucune erreur n’est levée.

### <a name="create-relationships"></a>Créer des relations

Ensuite, vous pouvez créer des **relations** entre les jumeaux que vous avez créés, afin de les raccorder sur un **graphe de jumeaux**. Les [graphes de jumeaux](concepts-twins-graph.md) servent à représenter votre environnement entier.

Ajoutez une **nouvelle méthode statique** à la classe `Program`, sous la méthode `Main` (le code a désormais deux méthodes) :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Create_relationship":::

Ensuite, ajoutez le code suivant à la fin de la méthode `Main` pour appeler la méthode `CreateRelationship` et utiliser le code que vous venez d’écrire :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_create_relationship":::

Dans votre fenêtre de commande, exécutez le programme avec `dotnet run`. Dans la sortie, recherchez des instructions print indiquant que les deux relations ont été créées avec succès.

Azure Digital Twins ne vous permet pas de créer une relation s’il en existe déjà une portant le même ID. Par conséquent, si vous exécutez le programme plusieurs fois, vous verrez des exceptions lors de la création de la relation. Ce code intercepte les exceptions et les ignore. 

### <a name="list-relationships"></a>Lister les relations

Le code que vous allez ensuite ajouter vous permet d’afficher la liste des relations que vous avez créées.

Ajoutez la **nouvelle méthode** suivante à la classe `Program` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="List_relationships":::

Ensuite, ajoutez le code suivant à la fin de la méthode `Main` pour appeler le code `ListRelationships` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_list_relationships":::

Dans votre fenêtre de commande, exécutez le programme avec `dotnet run`. Vous devez voir une liste de toutes les relations que vous avez créées dans une instruction de sortie similaire à celle-ci :

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Capture d’écran d’une console montrant la sortie du programme qui se traduit par un message listant les relations entre jumeaux." lightbox="media/tutorial-code/list-relationships.png":::

### <a name="query-digital-twins"></a>Interroger des jumeaux numériques

L’une des principales fonctionnalités d’Azure Digital Twins est la capacité à [interroger facilement et efficacement](concepts-query-language.md) votre graphe de jumeaux pour répondre à des questions sur votre environnement.

La dernière section de code à ajouter dans ce tutoriel exécute une requête sur l’instance Azure Digital Twins. La requête utilisée dans cet exemple retourne tous les jumeaux numériques de l’instance.

Ajoutez cette instruction `using` pour permettre l’utilisation de la classe `JsonSerializer` pour présenter plus aisément les informations de jumeau numérique :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_dependencies":::

Ensuite, ajoutez le code suivant à la fin de la méthode `Main` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_twins":::

Dans votre fenêtre de commande, exécutez le programme avec `dotnet run`. La sortie doit afficher tous les jumeaux numériques de cette instance.

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

## <a name="complete-code-example"></a>Exemple de code complet

À ce stade du tutoriel, vous disposez d’une application cliente complète, capable d’effectuer des actions de base sur Azure Digital Twins. Pour référence, le code complet du programme dans *Program.cs* est indiqué ci-dessous :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

À l’issue de ce tutoriel, vous pourrez choisir les ressources à supprimer, en fonction de ce que vous souhaitez faire ensuite.

* **Si vous envisagez de passer au tutoriel suivant**, vous pourrez y réutiliser l’instance utilisée dans ce tutoriel. Vous pouvez conserver les ressources Azure Digital Twins que vous avez configurées ici et ignorer le reste de cette section.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Vous pouvez également supprimer le dossier de projet de votre ordinateur local.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une application console cliente .NET à partir de rien. Vous avez écrit du code pour cette application cliente afin d’effectuer les actions de base sur une instance Azure Digital Twins.

Passez au tutoriel suivant pour découvrir les opérations que vous pouvez effectuer avec cet exemple d’application cliente : 

> [!div class="nextstepaction"]
> [Explorer les bases avec un exemple d’application cliente](tutorial-command-line-app.md)
