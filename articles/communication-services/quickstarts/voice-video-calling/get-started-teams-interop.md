---
title: Démarrage rapide - Interopérabilité de Teams sur Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: Dans ce guide de démarrage rapide, vous allez découvrir comment rejoindre une réunion Teams avec le SDK Azure Communication Calling.
author: chpalm
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 4ab25d1105f8dcad40599a8096ea114c31dfa61a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488328"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Démarrage rapide : Joindre votre application d’appel à une réunion Teams

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Calling with Windows](./includes/teams-interop/teams-interop-windows.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop/teams-interop-ios.md)]
::: zone-end

Les fonctionnalités décrites dans ce document utilisent la version de disponibilité générale des SDK Communication Services. L’interopérabilité Teams requiert la version bêta des SDK Communication Services. Les SDK bêta peuvent être explorés dans la [page des notes de publication](https://github.com/Azure/Communication/tree/master/releasenotes).

Lors de l’exécution de l’étape « Installer le package » avec les SDK bêta, remplacez la version de votre package par la dernière version bêta en spécifiant la version `@1.0.0-beta.10` (version au moment de la rédaction de cet article) dans le nom du package `communication-calling`. Vous n’avez pas besoin de modifier la commande de package `communication-common`. Par exemple :

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- Consultez notre [exemple de bannière d’appel](../../samples/calling-hero-sample.md).
- Apprenez-en davantage sur les [fonctionnalités du SDK Calling](./getting-started-with-calling.md)
- Apprenez-en davantage sur le [fonctionnement des appels](../../concepts/voice-video-calling/about-call-types.md)