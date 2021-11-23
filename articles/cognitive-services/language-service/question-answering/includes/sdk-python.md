---
title: 'Démarrage rapide : Bibliothèque de client de réponses aux questions pour Python'
description: Ce guide de démarrage rapide vous montre comment bien démarrer avec la bibliothèque de client de réponses aux questions pour Python.
ms.topic: include
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/11/2021
ms.openlocfilehash: 0aa0f65e9a277cbcb28870b970ead3e357057b2f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354064"
---
Utilisez ce guide de démarrage rapide pour la bibliothèque de client de réponses aux questions pour Python pour :

* Obtenir une réponse d’une base de connaissances.
* Revoir une réponse à partir d’un corps de texte que vous envoyez avec votre question.
* Obtenir le score de confiance pour la réponse à votre question.

[Documentation de référence sur l’API][questionanswering_refdocs] | [Code source][questionanswering_client_src] | [Package (PyPI)][questionanswering_pypi_package] | [Exemples Python][questionanswering_samples] |

[questionanswering_client_class]: https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient
[questionanswering_client_src]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-questionanswering/
[questionanswering_pypi_package]: https://pypi.org/project/azure-ai-language-questionanswering/
[questionanswering_refdocs]: https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html
[questionanswering_samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-questionanswering/samples/README.md

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Les réponses aux questions nécessitent une [ressource Langage](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) avec la fonctionnalité de réponses aux questions personnalisées activée pour générer une clé d’API et un point de terminaison.
    * Après le déploiement de votre ressource de langage, sélectionnez **Accéder à la ressource**. Vous avez besoin de la clé et du point de terminaison de la ressource que vous créez pour vous connecter à l’API. Collez votre clé et votre point de terminaison dans le code ci-dessous, plus loin dans le guide de démarrage rapide.
* Une base de connaissances existante à interroger. Si vous n’avez pas configuré de base de connaissances, vous pouvez suivre les instructions du [**Guide de démarrage rapide de Language Studio**](../quickstart/sdk.md). Ou ajoutez une base de connaissances qui utilise cette [URL du guide d’utilisation de Surface](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf) comme source de données.

## <a name="setting-up"></a>Configuration

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Après avoir installé Python, vous pouvez installer la bibliothèque de client avec :

```console
pip install azure-ai-language-questionanswering
```

## <a name="query-a-knowledge-base"></a>Interroger une base de connaissances

### <a name="generate-an-answer-from-a-knowledge-base"></a>Générer une réponse à partir d’une base de connaissances

L’exemple ci-dessous vous permet d’interroger une base de connaissances avec [get_answers](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers) pour obtenir une réponse à votre question. Vous pouvez copier ce code dans un fichier .py dédié ou dans une cellule dans [Jupyter Notebook/Lab](https://jupyter.org/).

Vous devrez mettre à jour le code ci-dessous et fournir vos propres valeurs pour les variables suivantes.

|Nom de la variable | Valeur |
|--------------------------|-------------|
| `endpoint`               | Cette valeur se trouve dans la section **Clés et point de terminaison** quand vous examinez votre ressource à partir du portail Azure. Vous pouvez également trouver la valeur dans **Language Studio** > **Réponses aux questions** > **Déployer la base de connaissances** > **Obtenir une URL de prédiction**. Voici un exemple de point de terminaison : `https://southcentralus.api.cognitive.microsoft.com/`|
| `credential` | Cette valeur se trouve dans la section **Clés et point de terminaison** quand vous examinez votre ressource à partir du portail Azure. Vous pouvez utiliser Key1 ou Key2. Toujours avoir deux clés valides pour la permutation de clés sécurisée sans temps d’arrêt. Vous pouvez également trouver la valeur dans **Language Studio** > **Réponses aux questions** > **Déployer la base de connaissances** > **Obtenir une URL de prédiction**. La valeur de clé fait partie de l’exemple de demande.|
| `knowledge_base_project` | Nom de votre projet de réponses aux questions.|
| `deployment`             | Il existe deux valeurs possibles : `test` et `production`. `production` dépend de la façon dont vous avez déployé votre base de connaissances à partir de **Language Studio** > **Réponses aux questions** > **Déployer la base de connaissances**.|

```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.language.questionanswering import QuestionAnsweringClient

endpoint = "https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/"
credential = AzureKeyCredential("{YOUR-LANGUAGE-RESOURCE-KEY}")
knowledge_base_project = "{YOUR-PROJECT-NAME}"
deployment = "production"

def main():
    client = QuestionAnsweringClient(endpoint, credential)
    with client:
        question="How much battery life do I have left?"
        output = client.get_answers(
            question = question,
            project_name=knowledge_base_project,
            deployment_name=deployment
        )
    print("Q: {}".format(question))
    print("A: {}".format(output.answers[0].answer))

if __name__ == '__main__':
    main()
```

Tandis que nous programmons en dur les variables pour notre exemple. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../../key-vault/general/overview.md) fournit un stockage de clé sécurisé.

Quand vous exécutez le code ci-dessus, si vous utilisez la source de données à partir des prérequis, vous obtenez une réponse qui se présente comme suit :

```
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
```

Pour plus d’informations sur la façon de connaître le degré de confiance des réponses aux questions quant au fait qu’il s’agit de la réponse appropriée, ajoutez une instruction print sous les instructions print existantes :

```python
print("Q: {}".format(question))
print("A: {}".format(output.answers[0].answer))
print("Confidence Score: {}".format(output.answers[0].confidence_score)) # add this line 
```

Vous recevrez maintenant un résultat avec un score de confiance :

```
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
Confidence Score: 0.9185
```

Le score de confiance retourne une valeur comprise entre 0 et 1. Vous pouvez considérer cela comme un pourcentage et le multiplier par 100. Ainsi, un score de confiance de 0,9185 signifie que, d’après les réponses aux questions, il existe 91,85 % de chance qu’il s’agisse de la bonne réponse à la question en fonction de la base de connaissances.

Si vous souhaitez exclure les réponses où le score de confiance est inférieur à un certain seuil, vous pouvez modifier [AnswerOptions](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.models.html#azure.ai.language.questionanswering.models.AnswersOptions) pour ajouter le paramètre `confidence_threshold`.

```python
        output = client.get_answers(
            confidence_threshold = 0.95, #add this line
            question = question,
            project_name=knowledge_base_project,
            deployment_name=deployment
        )
```

Notre score de confiance étant de `.9185` d’après notre précédente exécution du code, la définition du seuil sur `.95` entraînera le retour de la [réponse par défaut](../how-to/change-default-answer.md).

```
Q: How much battery life do I have left?
A: No good match found in KB
Confidence Score: 0.0
```

## <a name="query-text-without-a-knowledge-base"></a>Interroger un texte sans base de connaissances

Vous pouvez également utiliser les réponses aux questions sans base de connaissances avec [get_answers_from_text](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers_from_text). Dans ce cas, vous fournissez aux réponses aux questions une question et les enregistrements de texte dans lesquels vous souhaitez rechercher une réponse au moment où la demande est envoyée.

Pour cet exemple, vous devez uniquement modifier les variables pour `endpoint` et `credential`.

```python
import os
from azure.core.credentials import AzureKeyCredential
from azure.ai.language.questionanswering import QuestionAnsweringClient
from azure.ai.language.questionanswering import models as qna

endpoint = "https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/"
credential = AzureKeyCredential("YOUR-LANGUAGE-RESOURCE-KEY")

def main():
    client = QuestionAnsweringClient(endpoint, credential)
    with client:
        question="How long does it takes to charge a surface?"
        input = qna.AnswersFromTextOptions(
            question=question,
            text_documents=[
                "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
                "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.",
                "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
                "The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface.",
            ]
        )


        output = client.get_answers_from_text(input)

    best_answer = [a for a in output.answers if a.confidence > 0.9][0]
    print(u"Q: {}".format(input.question))
    print(u"A: {}".format(best_answer.answer))
    print("Confidence Score: {}".format(output.answers[0].confidence))

if __name__ == '__main__':
    main()
```

Vous pouvez copier ce code dans un fichier .py dédié ou dans une nouvelle cellule dans [Jupyter Notebook/Lab](https://jupyter.org/). Cet exemple retourne le résultat suivant :

```
Q: How long does it takes to charge surface?
A: Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.
Confidence Score: 0.9254655838012695
```

Dans ce cas, nous parcourons toutes les réponses et retournons uniquement la réponse avec le score de confiance le plus élevé qui est supérieur à 0,9. Pour en savoir plus sur les options disponibles avec [get_answers_from_text](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers_from_text), passez en revue les [paramètres AnswersFromTextOptions](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.models.html#azure.ai.language.questionanswering.models.AnswersFromTextOptions).