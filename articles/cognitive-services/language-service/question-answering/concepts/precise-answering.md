---
title: Réponse précise à l’aide de la détection de l’étendue des réponses - Réponses aux questions
description: Découvrez la fonctionnalité Réponse précise disponible dans les réponses aux questions.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 432e65d3d2f3dc3d4bc71157f165311e7fad87e9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476658"
---
# <a name="precise-answering"></a>Réponse précise

La fonctionnalité Réponse précise vous permet d’obtenir la réponse brève précise du meilleur passage candidat de la réponse présent dans la base de connaissances pour toute requête utilisateur. Cette fonctionnalité utilise un modèle deep learning qui, au moment de l’exécution, comprend l’intention de la requête utilisateur et détecte la réponse brève précise dans le passage de la réponse, s’il existe une réponse brève présente en tant que fait dans le passage de la réponse.

Cette fonctionnalité est précieuse pour les développeurs de contenus et les utilisateurs finaux. Maintenant, les développeurs de contenus n’ont pas besoin d’organiser manuellement des paires de questions-réponses spécifiques pour chaque fait présent dans la base de connaissances, et l’utilisateur final n’a pas besoin de parcourir l’ensemble du passage de la réponse retourné par le service pour trouver le fait réel qui répond à sa requête.

## <a name="precise-answering-via-the-portal"></a>Réponse précise via le portail

Dans le [portail Language Studio](https://aka.ms/languageStudio), lorsque vous ouvrez le volet test, vous voyez une option permettant d'**inclure une réponse rapide** dans la partie supérieure de afficher les options avancées.

Quand vous entrez une requête dans le volet de test, vous voyez une réponse brève, ainsi que le passage de la réponse, s’il existe une réponse brève dans le passage de la réponse.

>[!div class="mx-imgBorder"]
>[![Capture d’écran du volet test avec une réponse brève et une question contenant une réponse brève.](../media/precise-answering/short-answer.png)](../media/precise-answering/short-answer.png#lightbox)

Vous pouvez désélectionner l’option **Inclure une réponse brève**, si vous ne souhaitez voir que la **longue réponse** dans le volet test.

Le service retourne également le score de confiance de la réponse exacte sous la forme d’un score de confiance à l'**intervalle de réponse** que vous pouvez vérifier en sélectionnant l’option **Inspecter**, puis en sélectionnant **Informations supplémentaires**.

>[!div class="mx-imgBorder"]
>[![Capture d’écran du volet « Inspecter » avec le score de confiance de l’étendue de réponse affiché.](../media/precise-answering/answer-confidence-score.png)](../media/precise-answering/answer-confidence-score.png#lightbox)

## <a name="deploying-a-bot"></a>Déploiement d’un bot

Quand vous publiez un bot, vous obtenez l’expérience de réponse précise par défaut dans votre application, qui vous permet de voir une réponse brève en plus du passage de la réponse. Reportez-vous à la référence d’API pour l’API REST pour voir comment utiliser la réponse précise (appelée AnswerSpan) dans la réponse. L’utilisateur a la possibilité de choisir d’autres expériences en mettant à jour le modèle par le biais du service d’application Bot.
