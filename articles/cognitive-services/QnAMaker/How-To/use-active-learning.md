---
title: Utiliser l’apprentissage actif avec la base de connaissances – QnA Maker
description: Apprenez à améliorer la qualité de votre base de connaissances avec l'apprentissage actif. Examinez, acceptez ou refusez, ajoutez sans supprimer ou modifier les questions existantes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9d7a7eee1540690cf5d18516eeb602318e6d2c76
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005882"
---
# <a name="active-learning"></a>Apprentissage actif

La fonctionnalité _Suggestions d’apprentissage actif_ vous permet d’améliorer la qualité de votre base de connaissances en suggérant d’autres questions à vos paires de question-réponse en fonction des soumissions d’utilisateurs. Après avoir passé en revue ces suggestions, ajoutez-les aux questions existantes ou rejetez-les.

Votre base de connaissances ne change pas automatiquement. Vous devez accepter les suggestions afin que toute modification prenne effet. Ces suggestions ajoutent des questions, mais elles ne modifient pas les questions existantes pas plus qu’elles ne les suppriment.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

## <a name="what-is-active-learning"></a>Présentation de l’apprentissage actif

QnA Maker apprend de nouvelles variantes de question à l’aide des commentaires implicites et explicites.

* [Commentaires implicites](#how-qna-makers-implicit-feedback-works) : l’outil de classement comprend lorsqu’une question d’utilisateur a plusieurs réponses dont les scores sont très proches et la considère comme un commentaire. Vous n’avez pas besoin de faire quoi que ce soit pour que cela se produise.
* [Commentaires explicites](#how-you-give-explicit-feedback-with-the-train-api) : si plusieurs réponses avec peu de variations dans les scores sont renvoyées à partir de la base de connaissances, l’application cliente demande à l’utilisateur quelle question est appropriée. Les commentaires explicites de l’utilisateur sont envoyés à QnA Maker avec [l’API Train](../How-To/improve-knowledge-base.md#train-api).

Les deux méthodes fournissent à l’outil de classement des requêtes similaires qui sont ordonnées en cluster.

## <a name="how-active-learning-works"></a>Fonctionnement de l’apprentissage actif

L’apprentissage actif est déclenché en fonction des scores des principales réponses renvoyées par QnA Maker. Si les différences de scores entre les paires de Q/R qui correspondent à la requête s’inscrivent dans une petite plage, la requête est considérée comme suggestion possible (en tant que question alternative) pour chacune des paires de Q/R possibles. Une fois que vous acceptez la question suggérée pour une paire de question-réponse spécifique, elle est rejetée pour les autres paires. Vous devez penser à enregistrer et effectuer l’apprentissage, après avoir accepté les suggestions.

L’apprentissage actif offre les meilleures suggestions possible dans les cas où les points de terminaison reçoivent une quantité raisonnable de différentes requêtes d’utilisation. Lorsque cinq requêtes similaires ou plus sont ordonnées en cluster, toutes les 30 minutes, QnA Maker suggère les questions de l’utilisateur au concepteur de la base de connaissances pour qu’il les accepte ou les rejette. Toutes les suggestions sont regroupées par similarité, et les principales suggestions pour les questions alternatives sont affichées en fonction de la fréquence des requêtes particulières des utilisateurs finals.

Une fois les questions suggérées dans le portail QnA Maker, vous pouvez examiner, puis vérifier et accepter, ou rejeter, ces suggestions. Il n’existe pas d’API permettant de gérer des suggestions.

## <a name="how-qna-makers-implicit-feedback-works"></a>Fonctionnement des commentaires implicites de QnA Maker

Les commentaires implicites de QnA Maker utilisent un algorithme pour déterminer la proximité de score, puis pour faire des suggestions d’apprentissage actif. L’algorithme permettant de déterminer la proximité n’est pas un calcul simple. Les plages indiquées dans l’exemple suivant ne sont pas censées être fixes, mais doivent servir de guide pour comprendre l’effet de l’algorithme uniquement.

Lorsque le niveau de confiance du score d’une question est élevé, par exemple 80 %, la plage des scores pris en compte pour l’apprentissage actif est large, environ 10 %. À mesure que le score de confiance diminue, par exemple 40 %, la plage des scores décroît également et passe à environ 4 %.

Dans la réponse JSON suivante issue d’une requête à l’API GenerateAnswer de QnA Maker, les scores pour A, B et C sont proches et sont considérés comme des suggestions.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker ne saura pas quelle est la meilleure réponse. Utilisez la liste de suggestions du portail QnA Maker pour sélectionner la meilleure réponse et effectuer à nouveau l’apprentissage.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Comment donner des commentaires explicites avec l’API Train

QnA Maker a besoin de commentaires explicites pour savoir quelles réponses sont les meilleures. La façon dont la meilleure réponse est déterminée dépend de vous et peut inclure :

* Les commentaires des utilisateurs, en sélectionnant l’une des réponses.
* La logique métier, par exemple déterminer une plage de score acceptable.
* Une combinaison des commentaires des utilisateurs et de la logique métier.

Utilisez l’[API Train](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) pour envoyer la réponse correcte à QnA Maker une fois que l’utilisateur l’a sélectionnée.

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Mettre à niveau votre version de runtime pour utiliser l’apprentissage actif

L’apprentissage actif est pris en charge dans la version 4.4.0 du runtime et dans les versions ultérieures. Si votre base de connaissances a été créée dans une version antérieure, [mettez à niveau votre runtime](configure-QnA-Maker-resources.md#get-the-latest-runtime-updates) pour utiliser cette fonctionnalité.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Activer l’apprentissage actif pour les questions alternatives

L’apprentissage actif est désactivé par défaut. Activez-le pour afficher les suggestions de questions. Une fois l’apprentissage actif activé, vous devez envoyer des informations de l’application cliente vers QnA Maker. Pour plus d’informations, consultez [Flux architectural pour utiliser les API Train et GenerateAnswer à partir d’un bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Sélectionnez **Publier** pour publier la base de connaissances. Les requêtes d’apprentissage actif sont collectées à partir du point de terminaison de prédiction de l’API GenerateAnswer uniquement. Les requêtes vers le volet Test du portail QnA Maker n’affectent pas l’apprentissage actif.

1. Pour activer l’apprentissage actif, cliquez sur votre **Nom**, puis accédez à [**Paramètres du service**](https://www.qnamaker.ai/UserSettings) dans le portail QnA Maker, dans l’angle supérieur droit.

    ![Activez les alternatives de question suggérées de l’apprentissage actif à partir de la page Paramètres du service. Sélectionnez votre nom d’utilisateur dans le menu en haut à droite, puis sélectionnez Paramètres du service.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Recherchez le service QnA Maker, puis activez la stratégie **Apprentissage actif**.

    > [!div class="mx-imgBorder"]
    > [![Activation de la fonctionnalité d’apprentissage actif sur la page Paramètres du service. Si vous n’êtes pas en mesure d’activer/de désactiver la fonctionnalité, vous devrez peut-être mettre à niveau votre service.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > La version exacte sur l’image précédente est présentée à titre d’exemple uniquement. Votre version peut être différente.

    Une fois que la fonctionnalité **Apprentissage actif** est activée, la base de connaissances suggère de nouvelles questions à intervalles réguliers en fonction des questions soumises par l’utilisateur. Vous pouvez désactiver la stratégie **Apprentissage actif** en basculant de nouveau le paramètre.

## <a name="review-suggested-alternate-questions"></a>Passer en revue les questions alternatives suggérées

[Passez en revue les questions alternatives suggérées](improve-knowledge-base.md) dans la page **Edition** de chaque base de connaissances.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une base de connaissances](./manage-knowledge-bases.md)
