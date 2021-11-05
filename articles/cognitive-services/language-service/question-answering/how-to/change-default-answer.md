---
title: 'Recevoir la réponse par défaut : réponses aux questions personnalisées'
description: La réponse par défaut est retournée quand il n’existe aucune correspondance avec la question. Vous souhaiterez peut-être modifier la réponse par défaut dans vos réponses aux questions personnalisées.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 94925a2a6cadc80a576470fc1d0a585bd4bb4a01
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097737"
---
# <a name="change-default-answer-for-question-answering"></a>Modifier la réponse par défaut pour les réponses aux questions

La réponse par défaut d’un projet doit être retournée quand aucune réponse n’a été trouvée. Si vous utilisez une application cliente, telle que le [service Azure Bot](/azure/bot-service/bot-builder-howto-qna), celle-ci peut également avoir sa propre réponse par défaut, indiquant qu’aucune réponse n’a atteint le seuil de score.

## <a name="types-of-default-answer"></a>Types de réponses par défaut

Il existe deux types de réponses par défaut dans votre base de connaissances. Il est important de comprendre comment et quand chacune d’entre elles est retournée par une requête de prédiction :

|Types de réponses par défaut|Description de la réponse|
|--|--|
|Réponse de la base de connaissances lorsqu’aucune réponse n’est déterminée|`No good match found in KB.`Lorsque l’API de réponses aux questions ne trouve aucune réponse correspondante à la question, elle affiche une réponse textuelle par défaut. Dans Réponses aux questions personnalisées, vous pouvez définir ce texte dans les **paramètres** de votre projet. |
|Texte d’instruction de l’invite de suivi|Lorsque vous utilisez une invite de suivi dans un flux de conversation, il se peut que vous n’ayez pas besoin d’une réponse dans la paire question/réponse, car vous souhaitez que l’utilisateur choisisse parmi les invites de suivi. Dans ce cas, paramétrez un texte spécifique en définissant le texte de réponse par défaut, qui est retourné avec chaque prédiction pour les invites de suivi. Le texte est destiné à s’afficher sous la forme d’un texte d’instruction pour la sélection des invites de suivi. Un exemple de ce texte de réponse par défaut est `Please select from the following choices`. Cette configuration est expliquée dans les sections suivantes de ce document. Vous pouvez aussi le définir dans le cadre d’une définition de base de connaissances avec `defaultAnswerUsedForExtraction` à l’aide de l’API REST.|

### <a name="client-application-integration"></a>Intégration d’applications clientes

Pour une application cliente, telle qu’un bot avec le **service bot Azure**, vous pouvez choisir parmi les scénarios courants suivants :

* Utiliser le paramètre de votre projet/de la base de connaissances
* Utiliser un autre texte dans l’application cliente pour distinguer les cas où une réponse est retournée, mais n’atteint pas le seuil de score. Ce texte peut être du texte statique stocké dans le code ou il peut être stocké dans la liste des paramètres de l’application cliente.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Définir la réponse par défaut de l’invite de suivi lors de la création de la base de connaissances

Lorsque vous créez une base de connaissances, le texte de la réponse par défaut correspond à un paramètre. Si vous choisissez de ne pas la définir pendant le processus de création, vous pourrez la modifier ultérieurement à l’aide de la procédure suivante.

## <a name="change-follow-up-prompts-default-answer-in-language-studio"></a>Modifier la réponse par défaut de l’invite de suivi dans le portail Language Studio

La réponse par défaut de la base de connaissances est retournée quand aucune réponse n’est retournée à partir du service réponses aux questions.

1. Connectez-vous au [Language Studio](https://language.azure.com) et sélectionnez votre projet dans la liste.
1. Dans la barre de navigation gauche, sélectionnez **Paramètres**.
1. Modifiez la valeur de la **Réponse par défaut quand aucune réponse n’a été trouvée** > sélectionnez **Enregistrer**.

> [!div class="mx-imgBorder"]
> ![Capture d’écran des paramètres de projet avec une zone rouge autour de la réponse par défaut](../media/change-default-answer/settings.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer une base de connaissances](manage-knowledge-base.md)
