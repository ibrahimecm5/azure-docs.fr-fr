---
title: 'Démarrage rapide : Configuration de la plateforme du SDK Speech pour C# (Xamarin) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour configurer votre plateforme pour C# Xamarin avec le kit SDK du service Speech.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: eur
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: ce817142b3ff5e56dc99c39ad122355b720798f3
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502344"
---
Ce guide montre comment installer le [kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour [Xamarin](/xamarin/get-started/what-is-xamarin), une plateforme open source pour la création d’applications modernes et performantes pour iOS, Android et Windows avec .NET. Si vous voulez simplement le nom du package pour commencer seul, exécutez `Install-Package Microsoft.CognitiveServices.Speech` dans la console NuGet.

> [!NOTE]
> Le SDK Speech pour Xamarin prend en charge Windows Desktop (x86 et x64), ou la plateforme Windows universelle (x86, x64, ARM/ARM64), Android (x86, ARM32/64) et iOS (simulateur x64 et ARM64).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Sur Windows, vous avez besoin de [Microsoft Redistributable Visual C++ pour Visual Studio 2019](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) pour votre plateforme. La première installation peut demander i, redémarrage.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Créer un projet Visual Studio et installer le Kit de développement logiciel (SDK) Speech

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

Le kit de développement logiciel (SDK) Speech est maintenant installé. Vous pouvez maintenant supprimer ou réutiliser le projet « HelloWorld » que vous avez créé au cours des étapes précédentes.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list.md)]
