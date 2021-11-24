---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 642fa02cf8f40d7cc6a39a1951499cb7921ed100
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252234"
---
Lors du développement pour macOS, les kits de développement logiciel (SDK) Speech suivants sont disponibles.

- Le kit de développement logiciel (SDK) Objective-C/Swift est disponible en mode natif en tant que package CocoaPod pour les processeurs Mac x64 et ARM. Pour plus d’informations sur l’utilisation du kit de développement logiciel (SDK) Speech Objective-C avec Swift, consultez <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importation d’Objective-C dans Swift</a>.
- Le kit de développement logiciel (SDK) Speech .NET est disponible via le package NuGet et peut être utilisé avec les infrastructures d’application **Xamarin.Mac** et **Unity**.
- Le kit de développement logiciel (SDK) Python Speech est disponible sous la forme d’un module PyPI pour les versions 3.7 et ultérieures de Python.
- Le kit de développement logiciel (SDK) Speech Java est disponible via le référentiel Maven en tant que package JAR.

### <a name="system-requirements"></a>Configuration système requise

- macOS version 10.14 ou ultérieure

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Le package CocoaPod macOS peut être téléchargé et utilisé avec l’IDE <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (ou ultérieur) </a>. Tout d’abord, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">téléchargez le fichier binaire de CocoaPod</a>. Extrayez le pod dans le répertoire où vous prévoyez de l’utiliser, créez un *Podfile* et répertoriez le `pod` en tant que `target`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :osx, 10.14
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac expose le SDK macOS complet pour permettre aux développeurs .NET de créer des applications Mac natives en utilisant C#. Pour plus d’informations, consultez <a href="/xamarin/mac/" target="_blank">Xamarin.Mac </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Ressources supplémentaires

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">Code source Objective-C du guide de démarrage rapide du SDK Speech pour macOS</a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">Code source Swift du guide de démarrage rapide du SDK Speech pour macOS</a>
