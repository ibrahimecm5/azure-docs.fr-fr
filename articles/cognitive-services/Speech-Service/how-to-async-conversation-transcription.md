---
title: Transcription de conversation asynchrone – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser la transcription de conversation asynchrone à l’aide du service Speech. Disponible pour Java et C# uniquement.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 9dbee96d6b277ac76b07e3fd8b2b72b1c84b32f9
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546402"
---
# <a name="asynchronous-conversation-transcription"></a>Transcription de conversation asynchrone

Dans cet article, la transcription de conversation asynchrone est illustrée à l’aide de l’API **RemoteConversationTranscriptionClient**. Si vous avez configuré la transcription de conversation pour effectuer une transcription asynchrone et que vous disposez d’un `conversationId`, vous pouvez associer la transcription à ce `conversationId` à l’aide de l’API **RemoteConversationTranscriptionClient**.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Comparaison entre asynchrone et en temps réel + asynchrone

Avec la transcription asynchrone, vous diffusez l’audio de la conversation, mais une transcription ne doit pas être nécessairement retournée en temps réel. Au lieu de cela, une fois que l’audio est envoyé, utilisez `conversationId` de `Conversation` pour interroger l’état de la transcription asynchrone. Lorsque la transcription asynchrone est prête, vous obtenez un `RemoteConversationTranscriptionResult`.

Avec la transcription en temps réel plus asynchrone, vous obtenez la transcription en temps réel, mais vous pouvez également obtenir la transcription en interrogeant le `conversationId` (similaire au scénario asynchrone).

Deux étapes sont nécessaires pour effectuer une transcription asynchrone. La première étape consiste à charger l’audio, en choisissant asynchrone uniquement ou en temps réel plus asynchrone. La seconde étape consiste à obtenir les résultats de la transcription.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer nos exemples sur GitHub](https://aka.ms/csspeech/samples)
