---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: eur
ms.openlocfilehash: ba09913faf523c2301228e305e3f26d28a02e237
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529479"
---
L’une des principales fonctionnalités du service de reconnaissance vocale est la possibilité de reconnaître et de transcrire la voix humaine (souvent appelée « reconnaissance vocale »). Dans ce guide de démarrage rapide, vous allez apprendre à utiliser le SDK de reconnaissance vocale dans vos applications et produits afin d’effectuer une conversion de voix en texte de qualité.

## <a name="skip-to-samples-on-github"></a>Passer aux exemples sur GitHub

Si vous souhaitez passer directement à l’exemple de code, consultez les [exemples de démarrage rapide C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) sur GitHub.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

Avant de pouvoir faire quoi que ce soit, vous devez installer le SDK Speech. Suivez les instructions ci-dessous, en fonction de votre plateforme :

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Pour appeler le service Speech à l’aide du SDK Speech, vous devez créer une classe [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig). Celle-ci comprend des informations sur votre abonnement, telles que votre clé et la localisation/région, le point de terminaison, l’hôte ou le jeton d’autorisation associés. Créez une [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) à l’aide de votre clé et de votre région. Consultez la page [Rechercher les clés et la localisation/région](../../../overview.md#find-keys-and-locationregion) pour rechercher votre paire clé-localisation/région.

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;

auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
```

Vous pouvez initialiser une [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) de plusieurs autres façons :

* Avec un point de terminaison : transmettez un point de terminaison de service Speech. Une clé ou un jeton d’autorisation est facultatif.
* Avec un hôte : transmettez une adresse d’hôte. Une clé ou un jeton d’autorisation est facultatif.
* Avec un jeton d’autorisation : transmettez un jeton d’autorisation et la région associée.

> [!NOTE]
> Quand vous procédez à une reconnaissance vocale, une synthèse vocale, une traduction ou une reconnaissance intentionnelle, vous devez toujours créer une configuration.

## <a name="recognize-from-microphone"></a>Reconnaître la voix provenant d’un microphone

Pour reconnaître la voix à l’aide du microphone de votre appareil, créez une `AudioConfig` en utilisant `FromDefaultMicrophoneInput()`. Initialisez ensuite un [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer), en transmettant vos `audioConfig` et `config`.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);

cout << "Speak into your microphone." << std::endl;
auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

Si vous souhaitez utiliser un périphérique d’entrée audio *spécifique*, vous devez spécifier son ID dans l’`AudioConfig`. Découvrez [comment obtenir l’ID](../../../how-to-select-audio-input-devices.md) de votre périphérique d’entrée audio.

## <a name="recognize-from-file"></a>Reconnaître la voix à partir d’un fichier

Si vous souhaitez reconnaître la voix à partir d’un fichier audio au lieu d’utiliser un microphone, vous devez quand même créer un objet `AudioConfig`. Toutefois, quand vous créez l’objet [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig), au lieu d’appeler `FromDefaultMicrophoneInput()`, vous appelez `FromWavFileInput()` et transmettez le chemin du fichier.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);

auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

## <a name="recognize-speech"></a>Reconnaissance vocale

La [classe SpeechRecognizer](/cpp/cognitive-services/speech/speechrecognizer) pour le SDK Speech pour C++ expose quelques méthodes que vous pouvez utiliser pour la reconnaissance vocale.

### <a name="at-start-recognition"></a>Reconnaissance au début

La reconnaissance au début reconnaît de manière asynchrone un énoncé unique. La fin d’un énoncé unique est déterminée par la détection du silence à la fin, ou après que 15 secondes d’audio ont été traitées. Voici un exemple de reconnaissance asynchrone au début avec [`RecognizeOnceAsync`](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) :

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Vous devrez écrire du code pour gérer le résultat. Cet exemple évalue [`result->Reason`](/cpp/cognitive-services/speech/recognitionresult#reason) :

* Imprime le résultat de la reconnaissance : `ResultReason::RecognizedSpeech`
* S’il n’existe aucune correspondance de reconnaissance, informez l’utilisateur : `ResultReason::NoMatch`
* Si une erreur se produit, imprimez le message d’erreur : `ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the speech key and location/region info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Reconnaissance continue

La reconnaissance continue est un peu plus complexe que la reconnaissance au début. Pour obtenir les résultats de la reconnaissance, vous devez vous abonner aux événements `Recognizing`, `Recognized` et `Canceled`. Pour arrêter la reconnaissance, vous devez appeler [StopContinuousRecognitionAsync](/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Voici un exemple de reconnaissance continue sur un fichier d’entrée audio.

Commençons par définir l’entrée et initialiser un [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer) :

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Ensuite, nous allons créer une variable pour gérer l’état de la reconnaissance vocale. Pour commencer, nous déclarons `promise<void>` parce qu’au début de la reconnaissance, nous pouvons supposer sans risque qu’elle n’est pas terminée.

```cpp
promise<void> recognitionEnd;
```

Nous allons nous abonner aux événements envoyés à partir du [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer).

* [`Recognizing`](/cpp/cognitive-services/speech/asyncrecognizer#recognizing) : signal pour les événements contenant des résultats de reconnaissance intermédiaires.
* [`Recognized`](/cpp/cognitive-services/speech/asyncrecognizer#recognized) : signal pour les événements contenant des résultats de reconnaissance finaux (indiquant une tentative de reconnaissance réussie).
* [`SessionStopped`](/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped) : signal pour les événements indiquant la fin d’une session de reconnaissance (opération).
* [`Canceled`](/cpp/cognitive-services/speech/asyncrecognizer#canceled) : signal pour les événements contenant des résultats de reconnaissance annulés (indiquant une tentative de reconnaissance qui a été annulée suite une requête d’annulation directe ou à un échec de transport ou de protocole).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the speech key and location/region info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Tout étant configuré, nous pouvons maintenant appeler [`StopContinuousRecognitionAsync`](/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync).

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Mode de dictée

Quand vous utilisez la reconnaissance continue, vous pouvez activer le traitement de la dictée en utilisant la fonction « enable dictation » correspondante. Dans ce mode, l’instance de configuration vocale interprète les descriptions verbales de structures de phrase comme la ponctuation. Par exemple, l’énoncé « Habitez-vous en ville point d’interrogation » donne le texte « Habitez-vous en ville ? ».

Pour activer le mode dictée, utilisez la méthode [`EnableDictation`](/cpp/cognitive-services/speech/speechconfig#enabledictation) sur votre [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig).

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Changer la langue source

Une tâche courante pour la reconnaissance vocale consiste à spécifier la langue d’entrée (ou source). Examinons comment choisir l’allemand comme langue d’entrée. Dans votre code, recherchez votre [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig), puis ajoutez cette ligne directement en dessous.

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) est un paramètre qui prend une chaîne comme argument. Vous pouvez fournir n’importe quelle valeur figurant dans la liste des [paramètres régionaux/langues](../../../language-support.md) pris en charge.

## <a name="improve-recognition-accuracy"></a>Améliorer la justesse de la reconnaissance

Les listes d’expressions permettent d’identifier des expressions connues dans les données audio, comme le nom d’une personne ou un lieu spécifique. En fournissant une liste d’expressions, vous améliorez le niveau de justesse de la reconnaissance vocale.

Par exemple, si vous disposez d’une commande « Move to » et que parmi les destinations susceptibles d’être prononcées figurent « Ward », vous pouvez ajouter l’entrée « Move to Ward ». Ainsi, quand le contenu audio est reconnu, l’ajout de cette expression augmente la probabilité que « Move to Ward » sera reconnu et non « Move toward ».

Il est possible d’ajouter des mots uniques ou des expressions entières à une liste d’expressions. Pendant le processus de reconnaissance, une entrée de la liste d’expressions est utilisée pour renforcer la reconnaissance des mots et des expressions de la liste, même lorsque les entrées apparaissent au milieu de l’énoncé. 

> [!IMPORTANT]
> La fonctionnalité de liste d’expressions est disponible pour les langues suivantes : en-US, de-DE, en-AU, en-CA, en-GB, en-IN, es-ES, fr-FR, it-IT, ja-JP, pt-BR, zh-CN
>
> La fonctionnalité Liste d’expressions ne doit pas être utilisée avec plus de quelques centaines d’expressions. Si vous disposez d’une liste plus grande ou pour des langues qui ne sont pas prises en charge actuellement, la [formation d’un modèle personnalisé](../../../custom-speech-overview.md) sera probablement le meilleur choix pour améliorer la précision.
>
> La fonctionnalité de liste d’expressions n’est pas prise en charge avec les points de terminaison personnalisés. Vous ne devez donc pas l’utiliser avec ce type de points de terminaison. Préférez plutôt l’entraînement d’un modèle personnalisé qui comprend les expressions.

Pour utiliser une liste d’expressions, commencez par créer un objet [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar), puis ajoutez des mots et des expressions spécifiques avec [`AddPhrase`](/cpp/cognitive-services/speech/phraselistgrammar#addphrase).

Les modifications apportées à [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar) prennent effet lors de la reconnaissance suivante ou après une reconnexion au service Speech.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Si vous devez effacer votre liste d’expressions : 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Autres options pour améliorer la justesse de la reconnaissance

Les listes d’expressions ne sont qu’une option parmi d’autres permettant d’améliorer la justesse de la reconnaissance. Vous pouvez également : 

* [Améliorer la justesse avec Custom Speech](../../../custom-speech-overview.md).
* [Améliorer la justesse avec des modèles de locataire](../../../tutorial-tenant-model.md).
