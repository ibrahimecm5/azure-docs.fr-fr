---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: eur
ms.custom: devx-track-csharp
ms.openlocfilehash: a9e5a7e08c6f33e75d43028bad3cd8ab86775b44
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156662"
---
Pour configurer le SDK Speech afin d’accepter l’entrée audio compressée, créez `PullAudioInputStream` ou `PushAudioInputStream`. Ensuite, créez un `AudioConfig` à partir d’une instance de votre classe de flux en spécifiant le format de compression du flux. Recherchez des exemples d’extraits de code connexes dans [À propos de l’API de flux d’entrée audio du Kit de développement logiciel (SDK) Speech](../../../../how-to-use-audio-input-streams.md).

Supposons que vous disposez d’une classe de flux d’entrée appelée `pullStream` et que vous utilisez OPUS/OGG. Voici à quoi peut ressembler votre code :

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

// ... omitted for brevity

var speechConfig =
    SpeechConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
var audioFormat =
    AudioStreamFormat.GetCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
var audioConfig =
    AudioConfig.FromStreamInput(
        pullStream,
        audioFormat);

using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
var result = await recognizer.RecognizeOnceAsync();

var text = result.Text;
```
