---
title: Diffuser en streaming du contenu audio compressé par codec avec le SDK Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment diffuser en streaming du contenu audio compressé sur le service Speech avec le SDK Speech. Disponible pour C++, C#et Java pour Linux, Java dans Android et Objective-C dans iOS.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-eight
ms.openlocfilehash: 1f04c8c525e094d5e0980c1683632d34f3372580
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551916"
---
# <a name="use-codec-compressed-audio-input"></a>Utiliser une entrée audio compressée par codec

Le SDK Speech et l’interface de ligne de commande Speech peuvent accepter des formats audio compressés à l’aide de GStreamer. GStreamer décompresse l’audio avant de l’envoyer sur le réseau au service Speech sous forme de PCM brut.

Plateforme | Languages | Version de GStreamer prise en charge
| :--- | ---: | :---:
Linux  | C++, C#, Java, Python, Go | [Architectures cibles et distributions Linux prises en charge](~/articles/cognitive-services/speech-service/speech-sdk.md) 
Windows (sauf UWP) | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/msvc/gstreamer-1.0-msvc-x86_64-1.18.3.msi) 
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/) 

## <a name="installing-gstreamer-on-linux"></a>Installation de GStreamer sur Linux

Pour plus d’informations, consultez [Instructions d’installation Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c).  

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```
## <a name="installing-gstreamer-on-windows"></a>Installation de GStreamer sur Windows

Pour plus d’informations, consultez [Instructions d’installation Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). 

* Créez un dossier c:\gstreamer.
* Téléchargez le [programme d’installation](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/msvc/gstreamer-1.0-msvc-x86_64-1.18.3.msi). 
* Copiez le programme d’installation dans c:\gstreamer.
* Ouvrez PowerShell ISE en tant qu’administrateur.
* Exécutez la commande suivante dans PowerShell :

```powershell
cd c:\gstreamer
msiexec /passive INSTALLLEVEL=1000 INSTALLDIR=C:\gstreamer /i gstreamer-1.0-msvc-x86_64-1.18.3.msi
```
* Ajoutez les variables système GST_PLUGIN_PATH avec la valeur C:\gstreamer\1.0\msvc_x86_64\lib\gstreamer-1.0.
* Ajoutez les variables système GSTREAMER_ROOT_X86_64 avec la valeur C:\gstreamer\1.0\msvc_x86_64.
* Ajoutez une autre entrée dans la variable de chemin en tant que C:\gstreamer\1.0\msvc_x86_64\bin.
* Redémarrer l'ordinateur

## <a name="using-gstreamer-in-android"></a>Utilisation de GStreamer dans Android
Pour plus d’informations sur la création de libgstreamer_android.so, consultez l’onglet Java ci-dessus. 

Pour plus d’informations, consultez [Instructions d’installation Android](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c). 

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Version du SDK Speech requise pour l’entrée audio compressée
* Le Kit de développement logiciel (SDK) Speech version 1.10.0 ou ultérieure est requis pour RHEL 8 et CentOS 8.
* Le Kit de développement logiciel (SDK) Speech version 1.11.0 ou ultérieure est requis pour Windows.
* Le Kit de développement logiciel (SDK) Speech version 1.16.0 ou ultérieure pour la version la plus récente de GStreamer sur Windows et Android.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>GStreamer requis pour gérer l’audio compressé

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/go/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Exemple de code utilisant une entrée audio compressé par codec

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/go/examples.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrir comment effectuer la reconnaissance vocale](./get-started-speech-to-text.md)
