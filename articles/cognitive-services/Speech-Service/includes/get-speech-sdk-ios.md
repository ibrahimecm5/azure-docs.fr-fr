---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 942bf206454047f54765d096a2ac97fcabb895ab
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252442"
---
:::row:::
    :::column span="3":::
        Lors du développement pour iOS, les kits de développement logiciel (SDK) Speech suivants sont disponibles. Le Kit de développement logiciel (SDK) Speech Objective-C/Swift est disponible en natif sous forme de package CocoaPod iOS. Vous pouvez également utiliser le kit de développement logiciel (SDK) Speech .NET avec les infrastructures d’application Xamarin.iOS et Unity.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Pour plus d’informations sur l’utilisation du Kit de développement logiciel (SDK) Speech Objective-C avec Swift, consultez <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importation d’Objective-C dans Swift</a>.

### <a name="system-requirements"></a>Configuration système requise

- macOS version 10.3 ou ultérieure
- cibler iOS 9.3 ou version ultérieure

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Le package CocoaPod iOS peut être téléchargé et utilisé avec l’environnement de développement intégré (IDE) <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (ou ultérieur)</a>. Tout d’abord, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">téléchargez le fichier binaire de CocoaPod</a>. Extrayez le pod dans le répertoire où vous prévoyez de l’utiliser, créez un *Podfile* et répertoriez le `pod` en tant que `target`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS expose l’intégralité du Kit de développement logiciel (SDK) iOS pour les développeurs .NET. Générez des applications iOS entièrement natives à l’aide de C# dans Visual Studio. Pour plus d’informations, consultez <a href="/xamarin/ios/" target="_blank">Xamarin.iOS</a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp; ❤️ &nbsp;         <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Ressources supplémentaires

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">Code source Objective-C du guide de démarrage rapide du Kit de développement logiciel (SDK) Speech pour iOS</a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">Code source Swift du guide de démarrage rapide du Kit de développement logiciel (SDK) Speech pour iOS</a>
