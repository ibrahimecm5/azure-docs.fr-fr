---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: eur
ms.custom: devx-track-js
ms.openlocfilehash: 6606ff523330b314e3a0593557b159c4915cd33c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529662"
---
L’une des principales fonctionnalités du service de reconnaissance vocale est la possibilité de reconnaître et de transcrire la voix humaine (souvent appelée « reconnaissance vocale »). Dans ce guide de démarrage rapide, vous allez apprendre à utiliser le SDK de reconnaissance vocale dans vos applications et produits afin d’effectuer une conversion de voix en texte de qualité.

## <a name="skip-to-samples-on-github"></a>Passer aux exemples sur GitHub

Si vous souhaitez passer directement à l’exemple de code, consultez les [exemples de démarrage rapide JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) sur GitHub.

Vous pouvez également consulter l’[exemple React](https://github.com/Azure-Samples/AzureSpeechReactSample) pour apprendre à utiliser le SDK Speech dans un environnement basé sur un navigateur.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

Avant de pouvoir faire quoi que ce soit, vous devez installer le SDK Speech pour Node.js. Si vous voulez simplement le nom du package pour effectuer l’installation, exécutez `npm install microsoft-cognitiveservices-speech-sdk`. Pour obtenir des instructions d’installation, consultez l’article de [démarrage rapide](../../../quickstarts/setup-platform.md?pivots=programming-language-javascript&tabs=dotnet%2clinux%2cjre%2cnodejs).

Utilisez l’instruction `require` suivante pour importer le SDK.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Pour plus d’informations sur `require`, consultez la [documentation sur require](https://nodejs.org/en/knowledge/getting-started/what-is-require/).

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Pour appeler le service Speech à l’aide du SDK Speech, vous devez créer une classe [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig). Celle-ci comprend des informations sur votre abonnement, telles que votre clé et la localisation/région, le point de terminaison, l’hôte ou le jeton d’autorisation associés. Créez une [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) à l’aide de votre clé et de votre région/localisation. Consultez la page [Rechercher les clés et la localisation/région](../../../overview.md#find-keys-and-locationregion) pour rechercher votre paire clé-localisation/région.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
```

Vous pouvez initialiser une [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) de plusieurs autres façons :

* Avec un point de terminaison : transmettez un point de terminaison de service Speech. Une clé ou un jeton d’autorisation est facultatif.
* Avec un hôte : transmettez une adresse d’hôte. Une clé ou un jeton d’autorisation est facultatif.
* Avec un jeton d’autorisation : transmettez un jeton d’autorisation et la localisation/la région qui lui correspond.

> [!NOTE]
> Quand vous procédez à une reconnaissance vocale, une synthèse vocale, une traduction ou une reconnaissance intentionnelle, vous devez toujours créer une configuration.

## <a name="recognize-from-microphone-browser-only"></a>Reconnaître la voix provenant d’un microphone (navigateur uniquement)

La reconnaissance de la parole à partir d’un microphone **n’est pas prise en charge dans Node.js**. Elle est uniquement prise en charge dans un environnement JavaScript basé sur un navigateur. Consultez l’[exemple React](https://github.com/Azure-Samples/AzureSpeechReactSample) sur GitHub pour voir l’[implémentation de la reconnaissance vocale à partir d’un microphone](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29).

> [!NOTE]
> Si vous souhaitez utiliser un périphérique d’entrée audio *spécifique*, vous devez spécifier son ID dans l’`AudioConfig`. Découvrez [comment obtenir l’ID](../../../how-to-select-audio-input-devices.md) de votre périphérique d’entrée audio.

## <a name="recognize-from-file"></a>Reconnaître la voix à partir d’un fichier 

Pour reconnaître le message d’un fichier audio, créez un `AudioConfig` à l’aide de `fromWavFileInput()` qui accepte un objet `Buffer`. Initialisez ensuite un [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer), en transmettant vos `audioConfig` et `speechConfig`.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

function fromFile() {
    let audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync("YourAudioFile.wav"));
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);

    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

## <a name="recognize-from-in-memory-stream"></a>Reconnaître la voix à partir d’un flux en mémoire

Dans de nombreux cas d’usage, il est probable que vos données audio proviennent d’un stockage d’objets blob, ou qu’elles soient déjà en mémoire comme un [`ArrayBuffer`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) ou une structure de données brutes similaire. Le code suivant :

* crée un flux d’envoi (push) à l’aide de `createPushStream()` ;
* lit un fichier `.wav` à l’aide d’un `fs.createReadStream` à des fins de démonstration, mais si vous avez déjà des données audio dans un `ArrayBuffer`, vous pouvez passer directement à l’écriture du contenu dans le flux d’entrée ;
* crée une configuration audio à l’aide du flux d’envoi (push).

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

function fromStream() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromStream();
```

L’utilisation d’un flux d’envoi comme entrée suppose que les données audio sont au format PCM brut (par exemple, les en-têtes sont ignorés).
L’API fonctionne toujours dans certains cas si l’en-tête n’a pas été ignoré, mais pour obtenir les meilleurs résultats, envisagez d’implémenter une logique permettant de lire les en-têtes afin que `fs` démarre au *début des données audio*.

## <a name="error-handling"></a>Gestion des erreurs

Les exemples précédents récupèrent simplement le texte reconnu de `result.text`, mais pour gérer les erreurs et autres réponses, vous devez écrire du code afin de gérer le résultat. Le code suivant évalue la propriété [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) et :

* Imprime le résultat de la reconnaissance : `ResultReason.RecognizedSpeech`
* S’il n’existe aucune correspondance de reconnaissance, informez l’utilisateur : `ResultReason.NoMatch`
* Si une erreur se produit, imprimez le message d’erreur : `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case sdk.ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case sdk.ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case sdk.ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == sdk.CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the key and location/region info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Reconnaissance continue

Les exemples précédents utilisent la reconnaissance au début, qui reconnaît un énoncé unique. La fin d’un énoncé unique est déterminée par la détection du silence à la fin, ou après que 15 secondes d’audio ont été traitées.

En revanche, la reconnaissance continue est utilisée quand vous souhaitez **contrôler** à quel moment arrêter la reconnaissance. Pour obtenir les résultats de la reconnaissance, vous devez vous abonner aux événements `Recognizing`, `Recognized` et `Canceled`. Pour arrêter la reconnaissance, vous devez appeler [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync). Voici un exemple de reconnaissance continue sur un fichier d’entrée audio.

Commencez par définir l’entrée et initialiser un [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) :

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

Ensuite, abonnez-vous aux événements envoyés à partir du [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer).

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing) : signal pour les événements contenant des résultats de reconnaissance intermédiaires.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized) : signal pour les événements contenant des résultats de reconnaissance finaux (indiquant une tentative de reconnaissance réussie).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped) : signal pour les événements indiquant la fin d’une session de reconnaissance (opération).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled) : signal pour les événements contenant des résultats de reconnaissance annulés (indiquant une tentative de reconnaissance qui a été annulée suite une requête d’annulation directe ou à un échec de transport ou de protocole).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == sdk.ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == sdk.ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == sdk.CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the key and location/region info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Quand tout est configuré, appelez [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) pour commencer la reconnaissance.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.stopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Mode de dictée

Quand vous utilisez la reconnaissance continue, vous pouvez activer le traitement de la dictée en utilisant la fonction « enable dictation » correspondante. Dans ce mode, l’instance de configuration vocale interprète les descriptions verbales de structures de phrase comme la ponctuation. Par exemple, l’énoncé « Habitez-vous en ville point d’interrogation » donne le texte « Habitez-vous en ville ? ».

Pour activer le mode dictée, utilisez la méthode [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) sur votre [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Changer la langue source

Une tâche courante pour la reconnaissance vocale consiste à spécifier la langue d’entrée (ou source). Examinons comment choisir l’italien comme langue d’entrée. Dans votre code, recherchez votre [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig), puis ajoutez cette ligne directement en dessous.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

La propriété [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage) attend une chaîne au format langue-paramètres régionaux. Vous pouvez fournir n’importe quelle valeur figurant dans la colonne **Paramètres régionaux** dans la liste des [paramètres régionaux/langues](../../../language-support.md) pris en charge.

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

Pour utiliser une liste d’expressions, commencez par créer un objet [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar), puis ajoutez des mots et des expressions spécifiques avec [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-).

Les modifications apportées à [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) prennent effet lors de la reconnaissance suivante ou après une reconnexion au service Speech.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Si vous devez effacer votre liste d’expressions :

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Autres options pour améliorer la justesse de la reconnaissance

Les listes d’expressions ne sont qu’une option parmi d’autres permettant d’améliorer la justesse de la reconnaissance. Vous pouvez également : 

* [Améliorer la justesse avec Custom Speech](../../../custom-speech-overview.md).
* [Améliorer la justesse avec des modèles de locataire](../../../tutorial-tenant-model.md).
