---
title: 'Démarrage rapide : Bibliothèque de client de réponses aux questions pour .NET'
description: Ce guide de démarrage rapide vous montre comment bien démarrer avec la bibliothèque de client de réponses aux questions pour .NET. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Les réponses aux questions vous permettent de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré, comme des documents de questions fréquentes (FAQ), des URL et des manuels de produit.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: include
ms.date: 11/11/2021
ms.openlocfilehash: e374ef91c414b9d352b3c0cddd8af59da3e699a0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354146"
---
Utilisez ce guide de démarrage rapide pour la bibliothèque de client de réponses aux questions pour .NET pour :

* Obtenir une réponse d’une base de connaissances.
* Revoir une réponse à partir d’un corps de texte que vous envoyez avec votre question.
* Obtenir le score de confiance pour la réponse à votre question.

 [Documentation de référence sur l’API][questionanswering_refdocs]|[Code source][questionanswering_client_src] | [Package (NuGet)][questionanswering_nuget_package]  | [Exemples][questionanswering_samples] |

[questionanswering_nuget_package]: https://nuget.org/packages/Azure.AI.Language.
[questionanswering_refdocs]: https://docs.microsoft.com/dotnet/api/Azure.AI.Language.QuestionAnswering/
[questionanswering_client_src]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.QuestionAnswering/src/
[questionanswering_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.QuestionAnswering/samples/README.md

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* L’[IDE Visual Studio](https://visualstudio.microsoft.com/vs/) ou la version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Les réponses aux questions nécessitent une [ressource Langage](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) avec la fonctionnalité de réponses aux questions personnalisées activée pour générer une clé d’API et un point de terminaison. <!--TODO: Change link-->
    * Après le déploiement de votre ressource de langage, sélectionnez **Accéder à la ressource**. Vous avez besoin de la clé et du point de terminaison de la ressource que vous créez pour vous connecter à l’API. Collez votre clé et votre point de terminaison dans le code ci-dessous, plus loin dans le guide de démarrage rapide.
* Une base de connaissances existante à interroger. Si vous n’avez pas configuré de base de connaissances, vous pouvez suivre les instructions du [**Guide de démarrage rapide de Language Studio**](../quickstart/sdk.md). Ou ajoutez une base de connaissances qui utilise cette [URL du guide d’utilisation de Surface](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf) comme source de données.

## <a name="setting-up"></a>Configuration

### <a name="cli"></a>Interface de ligne de commande

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `question-answering-quickstart`. Cette commande crée un projet C# simple nommé « Hello World » avec un seul fichier source : *program.cs*.

```console
dotnet new console -n question-answering-quickstart
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

Dans le répertoire de l’application, installez la bibliothèque de réponses aux questions personnalisées pour .NET à l’aide de la commande suivante :

```console
dotnet add package Azure.AI.Language.QuestionAnswering
```

## <a name="query-a-knowledge-base"></a>Interroger une base de connaissances

#### <a name="generate-an-answer-from-a-knowledge-base"></a>Générer une réponse à partir d’une base de connaissances

L’exemple ci-dessous vous permet d’interroger une base de connaissances avec `GetAnswers` pour obtenir une réponse à votre question.

Vous devrez mettre à jour le code ci-dessous et fournir vos propres valeurs pour les variables suivantes.

|Nom de la variable | Valeur |
|--------------------------|-------------|
| `endpoint`               | Cette valeur se trouve dans la section **Clés et point de terminaison** quand vous examinez votre ressource à partir du portail Azure. Vous pouvez également trouver la valeur dans **Language Studio** > **Réponses aux questions** > **Déployer la base de connaissances** > **Obtenir une URL de prédiction**. Voici un exemple de point de terminaison : `https://southcentralus.api.cognitive.microsoft.com/`|
| `credential` | Cette valeur se trouve dans la section **Clés et point de terminaison** quand vous examinez votre ressource à partir du portail Azure. Vous pouvez utiliser Key1 ou Key2. Toujours avoir deux clés valides pour la permutation de clés sécurisée sans temps d’arrêt. Vous pouvez également trouver la valeur dans **Language Studio** > **Réponses aux questions** > **Déployer la base de connaissances** > **Obtenir une URL de prédiction**. La valeur de clé fait partie de l’exemple de demande.|
| `projectName` | Nom de votre projet de réponses aux questions.|
| `deploymentName`             | Il existe deux valeurs possibles : `test` et `production`. `production` dépend de la façon dont vous avez déployé votre base de connaissances à partir de **Language Studio** > **Réponses aux questions** > **Déployer la base de connaissances**.|

À partir du répertoire du projet, ouvrez le fichier *program.cs* et remplacez le contenu par le code suivant :

```csharp
using Azure;
using Azure.AI.Language.QuestionAnswering;
using System;

namespace question_answering
{
    class Program
    {
        static void Main(string[] args)
        {

            Uri endpoint = new Uri("https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/");
            AzureKeyCredential credential = new AzureKeyCredential("{YOUR-LANGUAGE-RESOURCE-KEY}");
            string projectName = "{YOUR-PROJECT-NAME}";
            string deploymentName = "production";

            string question = "How long should my Surface battery last?";

            QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);
            QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);

            Response<AnswersResult> response = client.GetAnswers(question, project);

            foreach (KnowledgeBaseAnswer answer in response.Value.Answers)
            {
                Console.WriteLine($"Q:{question}");
                Console.WriteLine($"A:{answer.Answer}");
            }
        }
    }
}
```

Tandis que nous programmons en dur les variables pour notre exemple. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../../key-vault/general/overview.md) fournit un stockage de clé sécurisé.

Après avoir mis à jour `Program.cs` avec le code ci-dessus et substitué les valeurs correctes des variables. Exécutez l’application avec la commande `dotnet run` à partir du répertoire de votre application.

```console
dotnet run
```

La réponse se présente comme suit :

```console
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
```

Pour plus d’informations sur la façon de connaître le degré de confiance des réponses aux questions quant au fait qu’il s’agit de la réponse appropriée, ajoutez une instruction print sous les instructions print existantes :

```csharp
Console.WriteLine($"Q:{question}");
Console.WriteLine($"A:{answer.Answer}");
Console.WriteLine($"({answer.Confidence})"); // add this line
```

Si vous réexécutez `dotnet run`, vous recevrez maintenant un résultat avec un score de confiance :

```console
Q:How much battery life do I have left?
A:If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
(0.9185)
```

Le score de confiance retourne une valeur comprise entre 0 et 1. Vous pouvez considérer cela comme un pourcentage et le multiplier par 100. Ainsi, un score de confiance de 0,9185 signifie que, d’après les réponses aux questions, il existe 91,85 % de chance qu’il s’agisse de la bonne réponse à la question en fonction de la base de connaissances.

Si vous souhaitez exclure les réponses où le score de confiance est inférieur à un certain seuil, utilisez `AnswerOptions` pour ajouter la propriété `ConfidenceScoreThreshold`.

```csharp
QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);
QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);
AnswersOptions options = new AnswersOptions(); //Add this line
options.ConfidenceThreshold = 0.95; //Add this line

Response<AnswersResult> response = client.GetAnswers(question, project, options); //Add the additional options parameter
```

Notre score de confiance étant de `.9185` d’après notre précédente exécution du code, la définition du seuil sur `.95` entraînera le retour de la [réponse par défaut](../how-to/change-default-answer.md).

```console
Q:How much battery life do I have left?
A:No good match found in KB
(0)
```

## <a name="query-text-without-a-knowledge-base"></a>Interroger un texte sans base de connaissances

Vous pouvez également utiliser les réponses aux questions sans base de connaissances avec `GetAnswersFromText`. Dans ce cas, vous fournissez aux réponses aux questions une question et les enregistrements de texte dans lesquels vous souhaitez rechercher une réponse au moment où la demande est envoyée.

Pour cet exemple, vous devez uniquement modifier les variables pour `endpoint` et `credential`.

```csharp
using Azure;
using Azure.AI.Language.QuestionAnswering;
using System;
using System.Collections.Generic;


namespace questionansweringcsharp
{
    class Program
    {
        static void Main(string[] args)
        {

            Uri endpoint = new Uri("https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/");
            AzureKeyCredential credential = new AzureKeyCredential("YOUR-LANGUAGE-RESOURCE-KEY");
            QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);

            IEnumerable<TextDocument> records = new[]
            {
                new TextDocument("doc1", "Power and charging.It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
                         "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it"),
                new TextDocument("doc2", "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
                         "The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."),
            };

            AnswersFromTextOptions options = new AnswersFromTextOptions("How long does it takes to charge a surface?", records);
            Response<AnswersFromTextResult> response = client.GetAnswersFromText(options);

           foreach (TextAnswer answer in response.Value.Answers)
            {
                if (answer.Confidence > .9)
                {
                    string BestAnswer = response.Value.Answers[0].Answer;

                    Console.WriteLine($"Q:{options.Question}");
                    Console.WriteLine($"A:{BestAnswer}");
                    Console.WriteLine($"Confidence Score: ({response.Value.Answers[0].Confidence:P2})"); //:P2 converts the result to a percentage with 2 decimals of accuracy. 
                    break;
                }
                else
                {
                    Console.WriteLine($"Q:{options.Question}");
                    Console.WriteLine("No answers met the requested confidence score.");
                    break;
                }
            }

        }
    }
}
```

Pour exécuter le code ci-dessus, remplacez `Program.cs` par le contenu du bloc de script ci-dessus et modifiez les variables `endpoint` et `credential` afin qu’elles correspondent à la ressource de langue que vous avez créée dans le cadre des prérequis.

Dans ce cas, nous parcourons toutes les réponses et retournons uniquement la réponse avec le score de confiance le plus élevé qui est supérieur à 0,9. Pour en savoir plus sur les options disponibles avec `GetAnswersFromText`.
