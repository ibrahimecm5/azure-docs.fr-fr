---
title: 'Démarrage rapide : Configuration de la plateforme du kit SDK Speech pour Java (Windows, Linux, macOS) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour configurer votre plateforme pour l’utilisation de Java (Windows, Linux, macOS) avec le kit SDK du service Speech.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: eur
ms.openlocfilehash: 9f0323d92a32938fd8850a06d0f86b034a59dd4b
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252212"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour le Java Runtime Environment (JRE) Java 8 64 bits. Si vous voulez simplement le nom du package pour commencer seul, le SDK Java n’est pas disponible dans le référentiel central Maven. Que vous utilisiez Gradle ou un fichier de dépendances `pom.xml`, vous devez ajouter un référentiel personnalisé pointant vers `https://csspeechstorage.blob.core.windows.net/maven/` (voir ci-dessous pour connaître le nom du package).

> [!NOTE]
> Pour le kit SDK Speech et l’appareil Roobo, consultez le [kit SDK Speech Devices](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

- Le package Java du Kit de développement logiciel (SDK) Speech est disponible pour les systèmes d’exploitation suivants :
  - Windows : 64 bits uniquement
  - Mac : macOS X version 10.14 ou ultérieure
  - Linux : consultez la liste des [architectures cibles et distributions Linux prises en charge](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Prérequis

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (nécessite Java déjà installé)

- Sur Windows, vous avez besoin de [Microsoft Redistributable Visual C++ pour Visual Studio 2019](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) pour votre plateforme. La première installation peut demander i, redémarrage.

- Sur Linux, consultez la [Configuration système requise et les instructions d’installation](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk).

## <a name="gradle-config"></a>Configuration de Gradle

Les configurations Gradle nécessitent à la fois un référentiel personnalisé et une référence explicite à l’extension de dépendance `.jar`.

```groovy
// build.gradle

repositories {
    maven {
        url "https://csspeechstorage.blob.core.windows.net/maven/"
    }
}

dependencies {
    implementation group: 'com.microsoft.cognitiveservices.speech', name: 'client-sdk', version: "1.18.0", ext: "jar"
}
```

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Créer un projet Eclipse et installer le Kit de développement logiciel (SDK) Speech

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list.md)]
