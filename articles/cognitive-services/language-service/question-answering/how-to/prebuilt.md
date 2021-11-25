---
title: API prédéfinie - réponses aux questions
titleSuffix: Azure Cognitive Services
description: Utilisez l’API prédéfinie de réponses aux questions pour demander et recevoir des réponses aux questions sans avoir à créer un projet ou une base de connaissances.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: 9fe4b80220ccb94e2b89bcdeaacf8cacac624ed0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159609"
---
# <a name="prebuilt-api"></a>API prédéfinie

L’**API prédéfinie** de réponses aux questions vous permet de répondre aux questions basées sur un passage de texte sans devoir créer de projets/bases de connaissances, conserver les paires questions-réponse ou engendrer de coûts pour une infrastructure sous-exploitée. Cette fonctionnalité fournie en tant qu’API peut être utilisée pour satisfaire des besoins en matière de questions et de réponses sans avoir à apprendre les détails des réponses aux questions.

À partir d’une requête utilisateur et d’un bloc ou passage de texte, l’API retourne une réponse précise (si disponible).

## <a name="example-api-usage"></a>Exemple d’utilisation de l’API

Imaginez que vous disposez d’un ou plusieurs blocs de texte dans lesquels vous souhaitez trouver des réponses à une question donnée. Normalement, vous auriez dû créer autant de sources qu’il y a de blocs de texte. En revanche, désormais, l’API prédéfinie vous permet d’interroger les blocs de texte sans avoir à définir des sources de contenu dans un projet/une base de connaissances.

Voici d’autres scénarios dans lesquels cette API peut être utilisée :

* Application de lecteur de livre électronique permettant aux utilisateurs de mettre du texte en surbrillance, d’entrer une question et d’obtenir des réponses en relation avec un passage de texte sélectionné.
* Extension de navigateur permettant aux utilisateurs de poser une question sur le contenu de la page affichée dans le navigateur.
* Robot dédié à la santé qui accepte des requêtes d’utilisateurs et fournit des réponses basées sur le contenu médical que le bot identifie comme le plus pertinent.

Voici un exemple de demande :

## <a name="sample-request"></a>Exemple de requête

```
POST https://{Unique-to-your-endpoint}.api.cognitive.microsoft.com/language/:query-text
```

### <a name="sample-query-over-a-single-block-of-text"></a>Exemple de requête sur un seul bloc de texte

Corps de la demande

```json
{
  "parameters": {
    "Endpoint": "{Endpoint}",
    "Ocp-Apim-Subscription-Key": "{API key}",
    "Content-Type": "application/json",
    "api-version": "2021-10-01",
    "stringIndexType": "TextElements_v8",
    "textQueryOptions": {
      "question": "how long it takes to charge surface?",
      "records": [
        {
          "id": "1",
          "text": "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it."
        },
        {
          "id": "2",
          "text": "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."
        }
      ],
      "language": "en"
    }
  }
}
```

## <a name="sample-response"></a>Exemple de réponse

Dans le corps de la demande ci-dessus, nous effectuons une requête sur un bloc de texte. Un exemple de réponse reçue à cette requête figure ci-dessous.

```json
{
"responses": {
    "200": {
      "headers": {},
      "body": {
        "answers": [
          {
            "answer": "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.93,
            "id": "1",
            "answerSpan": {
              "text": "two to four hours",
              "confidenceScore": 0,
              "offset": 28,
              "length": 45
            },
            "offset": 0,
            "length": 224
          },
          {
            "answer": "It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.92,
            "id": "1",
            "answerSpan": {
              "text": "two to four hours",
              "confidenceScore": 0,
              "offset": 8,
              "length": 25
            },
            "offset": 20,
            "length": 224
          },
          {
            "answer": "It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.05,
            "id": "1",
            "answerSpan": null,
            "offset": 110,
            "length": 244
          }
        ]
      }
    }
  }
```

Nous voyons que l’API propose plusieurs réponses. Chaque réponse est associée à un score de confiance spécifique qui permet d’en comprendre la pertinence. L’étendue de la réponse indique si une réponse rapide potentielle a été détectée. Les utilisateurs peuvent utiliser ce score de confiance pour déterminer les réponses à fournir en réponse à la requête.

## <a name="prebuilt-api-limits"></a>Limites de l’API prédéfinie

Si vous devez utiliser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. Dans ce contexte, un document est une chaîne de caractères de texte unique définie.

Ces chiffres représentent les **limites de chaque appel d’API** :

* Nombre de documents : 5.
* Taille maximale d’un seul document : 5 120 caractères.
* Trois réponses maximum par document.

## <a name="prebuilt-api-reference"></a>Référence de l’API prédéfinie

Pour comprendre les paramètres d’entrée et de sortie requis pour appeler l’API, consultez la documentation regroupant les [exemples de l’API prédéfinie](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/Language/stable/2021-10-01/examples/questionanswering/SuccessfulQueryText.json).