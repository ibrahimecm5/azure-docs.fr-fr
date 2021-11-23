---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: eur
ms.openlocfilehash: 6900e9ab4356e57a48dcfd90670e43b802305b93
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529734"
---
L’une des principales fonctionnalités du service de reconnaissance vocale est la possibilité de reconnaître et de transcrire la voix humaine (souvent appelée « reconnaissance vocale »). Dans ce guide de démarrage rapide, vous allez apprendre à utiliser le SDK de reconnaissance vocale dans vos applications et produits afin d’effectuer une conversion de voix en texte de qualité.

## <a name="skip-to-samples-on-github"></a>Passer aux exemples sur GitHub

Si vous souhaitez passer directement à l’exemple de code, consultez les [exemples de démarrage rapide Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) sur GitHub.

## <a name="prerequisites"></a>Prérequis

Cet article suppose :

* Vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-and-import-the-speech-sdk"></a>Installer et importer le SDK Speech

Avant de pouvoir faire quoi que ce soit, vous devez installer le SDK Speech.

```Python
pip install azure-cognitiveservices-speech
```

Si vous utilisez macOS et rencontrez des problèmes d’installation, vous devrez peut-être exécuter cette commande en premier.

```Python
python3 -m pip install --upgrade pip
```

Une fois le SDK Speech installé, importez-le dans votre projet Python.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Pour appeler le service Speech à l’aide du SDK Speech, vous devez créer une classe [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig). Celle-ci comprend des informations sur votre abonnement, telles que votre clé et la localisation/région, le point de terminaison, l’hôte ou le jeton d’autorisation associés. Créez une [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) à l’aide de votre clé et de votre région/localisation. Consultez la page [Rechercher les clés et la localisation/région](../../../overview.md#find-keys-and-locationregion) pour rechercher votre paire clé-localisation/région.

```Python
speech_config = speechsdk.SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
```

Vous pouvez initialiser une [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) de plusieurs autres façons :

* Avec un point de terminaison : transmettez un point de terminaison de service Speech. Une clé ou un jeton d’autorisation est facultatif.
* Avec un hôte : transmettez une adresse d’hôte. Une clé ou un jeton d’autorisation est facultatif.
* Avec un jeton d’autorisation : transmettez un jeton d’autorisation et la région associée.

> [!NOTE]
> Quand vous procédez à une reconnaissance vocale, une synthèse vocale, une traduction ou une reconnaissance intentionnelle, vous devez toujours créer une configuration.

## <a name="recognize-from-microphone"></a>Reconnaître la voix provenant d’un microphone

Pour reconnaître la voix à l’aide du microphone de votre appareil, créez simplement un `SpeechRecognizer` sans transmettre de `AudioConfig` et transmettez votre `speech_config`.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_mic():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
    
    print("Speak into your microphone.")
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_mic()
```

Si vous souhaitez utiliser un périphérique d’entrée audio *spécifique*, vous devez spécifier son ID dans une `AudioConfig` et le transmettre au paramètre `audio_config` du constructeur `SpeechRecognizer`. Découvrez [comment obtenir l’ID](../../../how-to-select-audio-input-devices.md) de votre périphérique d’entrée audio.

## <a name="recognize-from-file"></a>Reconnaître la voix à partir d’un fichier

Si vous souhaitez reconnaître la voix à partir d’un fichier audio au lieu d’utiliser un microphone, créez un objet [`AudioConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig) et utilisez le paramètre `filename`.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_file():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
    audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
    
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_file()
```

## <a name="error-handling"></a>Gestion des erreurs

Les exemples précédents récupèrent simplement le texte reconnu de `result.text`, mais pour gérer les erreurs et autres réponses, vous devez écrire du code afin de gérer le résultat. Le code suivant évalue la propriété [`result.reason`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason) et :

* Imprime le résultat de la reconnaissance : `speechsdk.ResultReason.RecognizedSpeech`
* S’il n’existe aucune correspondance de reconnaissance, informez l’utilisateur : `speechsdk.ResultReason.NoMatch`
* Si une erreur se produit, imprimez le message d’erreur : `speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

## <a name="continuous-recognition"></a>Reconnaissance continue

Les exemples précédents utilisent la reconnaissance au début, qui reconnaît un énoncé unique. La fin d’un énoncé unique est déterminée par la détection du silence à la fin, ou après que 15 secondes d’audio ont été traitées.

En revanche, la reconnaissance continue est utilisée quand vous souhaitez **contrôler** à quel moment arrêter la reconnaissance. Vous êtes obligé de vous connecter à `EventSignal` pour obtenir les résultats de la reconnaissance. Pour arrêter la reconnaissance, vous devez appeler [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition--) ou [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition-async--). Voici un exemple de reconnaissance continue sur un fichier d’entrée audio.

Commençons par définir l’entrée et initialiser un [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer) :

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Ensuite, nous allons créer une variable pour gérer l’état de la reconnaissance vocale. Pour commencer, nous allons lui affecter la valeur `False`, car au début de la reconnaissance nous pouvons supposer sans risque qu’elle n’est pas terminée.

```Python
done = False
```

Maintenant, nous allons créer un rappel pour arrêter la reconnaissance continue lors de la réception d’un `evt`. Il y a quelques éléments à garder à l’esprit.

* Lors de la réception d’un `evt`, le message `evt` est imprimé.
* Après la réception d’un `evt`, [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition--) est appelé pour arrêter la reconnaissance.
* L’état de la reconnaissance devient `True`.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Cet exemple de code montre comment connecter des rappels aux événements envoyés à partir du [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#start-continuous-recognition--).

* [`recognizing`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#recognizing) : signal pour les événements contenant des résultats de reconnaissance intermédiaires.
* [`recognized`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#recognized) : signal pour les événements contenant des résultats de reconnaissance finaux (indiquant une tentative de reconnaissance réussie).
* [`session_started`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-started) : signal pour les événements indiquant le début d’une session de reconnaissance (opération).
* [`session_stopped`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-stopped) : signal pour les événements indiquant la fin d’une session de reconnaissance (opération).
* [`canceled`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#canceled) : signal pour les événements contenant des résultats de reconnaissance annulés (indiquant une tentative de reconnaissance qui a été annulée suite une requête d’annulation directe ou à un échec de transport ou de protocole).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Tout étant configuré, nous pouvons maintenant appeler [start_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Mode de dictée

Quand vous utilisez la reconnaissance continue, vous pouvez activer le traitement de la dictée en utilisant la fonction « enable dictation » correspondante. Dans ce mode, l’instance de configuration vocale interprète les descriptions verbales de structures de phrase comme la ponctuation. Par exemple, l’énoncé « Habitez-vous en ville point d’interrogation » donne le texte « Habitez-vous en ville ? ».

Pour activer le mode dictée, utilisez la méthode [`enable_dictation()`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#enable-dictation--) sur votre [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Changer la langue source

Une tâche courante pour la reconnaissance vocale consiste à spécifier la langue d’entrée (ou source). Examinons comment choisir l’allemand comme langue d’entrée. Dans votre code, recherchez votre SpeechConfig, puis ajoutez cette ligne directement en dessous.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-recognition-language) est un paramètre qui prend une chaîne comme argument. Vous pouvez fournir n’importe quelle valeur figurant dans la liste des [paramètres régionaux/langues](../../../language-support.md) pris en charge.

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

Pour utiliser une liste d’expressions, commencez par créer un objet [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar), puis ajoutez des mots et des expressions spécifiques avec [`addPhrase`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar#addphrase-phrase--str-).

Les modifications apportées à [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar) prennent effet lors de la reconnaissance suivante ou après une reconnexion au service Speech.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Si vous devez effacer votre liste d’expressions : 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Autres options pour améliorer la justesse de la reconnaissance

Les listes d’expressions ne sont qu’une option parmi d’autres permettant d’améliorer la justesse de la reconnaissance. Vous pouvez également : 

* [Améliorer la justesse avec Custom Speech](../../../custom-speech-overview.md).
* [Améliorer la justesse avec des modèles de locataire](../../../tutorial-tenant-model.md).
