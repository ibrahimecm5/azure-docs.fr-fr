---
title: Présentation de la reconnaissance de l’intention – Service Speech
titleSuffix: Azure Cognitive Services
description: La reconnaissance de l’intention vous permet de reconnaître les objectifs de l’utilisateur que vous avez prédéfinis. Cet article est une présentation des avantages et des capacités du service de reconnaissance de l’intention.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
keywords: reconnaissance de l’intention
ms.openlocfilehash: 221f5f0f022cba85df72979ed47b4033b2c3fdd1
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988480"
---
# <a name="what-is-intent-recognition"></a>Qu’est-ce que la reconnaissance de l’intention ?

Dans cette vue d’ensemble, vous découvrez les avantages et les capacités de la reconnaissance de l’intention. Le kit de développement logiciel (SDK) des Cognitive Services Speech fournit deux méthodes pour reconnaître les intentions, lesquelles sont décrites ci-dessous. Une intention est quelque chose que l’utilisateur souhaite faire : réserver un vol, vérifier la météo ou effectuer un appel. Grâce à la reconnaissance de l’intention, vos applications, outils et appareils peuvent déterminer ce que l’utilisateur souhaite lancer ou faire en fonction des options que vous définissez dans le Module de reconnaissance de l’intention ou dans LUIS.

## <a name="pattern-matching"></a>Critères spéciaux
Le kit de développement logiciel (SDK) fournit un matching de modèle incorporé que vous pouvez utiliser pour reconnaître les intentions de manière très stricte. Cela est utile lorsque vous avez besoin d’une solution hors connexion rapide. Cela fonctionne particulièrement bien lorsque l’utilisateur sera formé d’une certaine façon ou qu’il est supposé qu’il utilisera des expressions spécifiques pour déclencher des intentions. Par exemple : « Aller au 7e étage » ou « Allumer la lampe », etc. Nous vous recommandons de commencer ici et, si cela ne répond plus à vos besoins, de passer à l’utilisation de LUIS ou une combinaison des deux. 

## <a name="luis-language-understanding-intent-service"></a>LUIS (Language Understanding Intent Service)
Le service Microsoft LUIS est disponible sous la forme d’un service d’intention d’IA complet qui fonctionne bien lorsque votre domaine d’intentions possibles est important et que vous ne savez pas vraiment ce que l’utilisateur va dire. Il prend en charge de nombreux scénarios complexes, des intentions et des entités.

### <a name="luis-key-required"></a>Clé LUIS obligatoire

* LUIS s’intègre au service Speech pour reconnaître les intentions à partir de la reconnaissance vocale. Vous n’avez besoin que de LUIS, aucun abonnement au service Speech n’est nécessaire.
* La reconnaissance de l’intention de Speech est intégrée au Kit de développement logiciel (SDK). Vous pouvez utiliser une clé LUIS avec le service Speech.
* La reconnaissance de l’intention par le biais du Kit de développement logiciel (SDK) Speech est [proposée dans un sous-ensemble de régions pris en charge par LUIS](./regions.md#intent-recognition).

## <a name="get-started"></a>Bien démarrer
Consultez ce [guide](how-to-use-simple-language-pattern-matching.md) pour la prise en main des critères spéciaux.

Consultez ce [démarrage rapide](get-started-intent-recognition.md) pour prendre en main la reconnaissance de l’intention LUIS.

## <a name="sample-code"></a>Exemple de code

Exemple de code pour la reconnaissance de l’intention :

* [Démarrage rapide : Utiliser une application domotique prédéfinie](../luis/luis-get-started-create-app.md)
* [Effectuer une reconnaissance des intentions vocales à l’aide du kit SDK Speech pour C#](./how-to-recognize-intents-from-speech-csharp.md)
* [Reconnaissance de l’intention et autres services Speech utilisant Unity en C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Reconnaître des intentions à l’aide du Kit de développement logiciel (SDK) Speech pour Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Reconnaissance de l’intention et autres services Speech utilisant le Kit de développement logiciel (SDK) Speech pour C++ sur Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Reconnaissance de l’intention et autres services Speech utilisant le Kit de développement logiciel (SDK) Speech pour Java sur Windows ou Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Reconnaissance de l’intention et autres services Speech utilisant le Kit de développement logiciel (SDK) Speech pour JavaScript sur un navigateur web](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](./speech-sdk.md)

## <a name="next-steps"></a>Étapes suivantes

* Suivre le [démarrage rapide](get-started-intent-recognition.md) sur la reconnaissance de l’intention
* [Obtenir gratuitement une clé d’abonnement au service Speech](overview.md#try-the-speech-service-for-free)
* [Obtenir le kit SDK Speech](speech-sdk.md)
