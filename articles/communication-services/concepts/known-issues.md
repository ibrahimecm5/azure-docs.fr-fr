---
title: Azure Communication Services – Problèmes connus
description: En savoir plus sur Azure Communication Services
author: rinarish
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: a32b462b17a96eacb3858e7a22a27262046b8589
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491259"
---
# <a name="known-issues-in-the-sdks-and-apis"></a>Problèmes connus dans les kits SDK et les API

Cet article fournit des informations sur les limites et les problèmes connus concernant les kits SDK pour les appels Azure Communication Services et les API d’automatisation des appels Communication Services.

> [!IMPORTANT]
> Plusieurs facteurs peuvent affecter la qualité de votre expérience d’appel. Pour en savoir plus sur la configuration du réseau et les meilleures pratiques en matière de tests pour Communication Services, consultez [Recommandations réseau](./voice-video-calling/network-requirements.md).

## <a name="javascript-sdk"></a>Kit de développement logiciel (SDK) JavaScript

Les sections suivantes fournissent des informations sur les problèmes connus associés aux kits SDK pour les appels audio et vidéo JavaScript Communication Services.

### <a name="ios-with-safari-crashes-and-refreshes-the-page-if-a-user-tries-to-send-video-in-a-call"></a>iOS avec Safari se bloque et actualise la page si un utilisateur tente d’envoyer du contenu vidéo dans un appel

iOS 15.1 a introduit un bogue qui affecte la majorité des appels Communication Services avec du contenu vidéo qui sont passés dans iOS avec Safari. Plus spécifiquement, le problème se produit lorsqu’un utilisateur rejoint un appel ou une réunion Communication Services dans Microsoft Teams via Communication Services sur iOS 15.1 sur un navigateur avec la vidéo activée. Cet ensemble de circonstances provoque le blocage du navigateur Safari.

Il s’agit d’un [bogue connu sur iOS 15.1 avec Safari](https://bugs.webkit.org/show_bug.cgi?id=231505). Vous devez empêcher les utilisateurs d’utiliser (d’activer) la vidéo lorsqu’ils utilisent iOS 15.1 avec Safari pour les appels vidéo Communication Services. Vous devez également empêcher cette activité pour les réunions d’appel vidéo entre Microsoft Teams et Communication Services.

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>L’actualisation d’une page ne retire pas immédiatement l’utilisateur de l’appel

Si un utilisateur participe à un appel et décide d’actualiser la page, le service multimédia de Communication Services ne retira pas cet utilisateur immédiatement de l’appel. Il va attendre que l’utilisateur se reconnecte. L’utilisateur sera supprimé de l’appel après l’expiration du délai d’attente du service multimédia.

Il est préférable de créer des expériences utilisateur qui ne nécessitent pas que les utilisateurs finaux actualisent la page de votre application pendant un appel. Si un utilisateur actualise la page, réutilisez le même ID utilisateur de Communication Services lorsque cet utilisateur retourne à l’application. En se joignant avec le même ID utilisateur, l’utilisateur est représenté comme le même objet existant dans la collection `remoteParticipants`. Du point de vue des autres participants à l’appel, l’utilisateur reste connecté à l’appel pendant le temps nécessaire à l’actualisation de la page, jusqu’à une minute ou deux.

Si l’utilisateur envoie une vidéo avant l’actualisation, la collection `videoStreams` conserve les informations de flux précédentes jusqu’à ce que le service expire et le supprime. Dans ce scénario, l’application peut décider d’observer les nouveaux flux ajoutés à la collection et d’en afficher une avec l’`id` le plus élevé. 

### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Il n’est pas possible d’afficher plusieurs aperçus à partir de plusieurs appareils sur le web

Il s'agit d'une limitation connue. Pour en savoir plus, consultez [Vue d’ensemble du kit SDK Appel](./voice-video-calling/calling-sdk-features.md).

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>L’énumération des appareils ne peut s’effectuer dans Safari lorsque l’application s’exécute sur iOS ou iPados

Les applications ne peuvent pas énumérer ou sélectionner des appareils de catégorie micro ou haut-parleur (comme Bluetooth) via le navigateur Safari sur iOS ou iPadOS. Il s’agit d’une limite connue de ces systèmes d’exploitation.

Si vous utilisez Safari sur macOS, votre application ne sera pas en mesure d’énumérer ou de sélectionner des haut-parleurs via le gestionnaire d’appareils de Communication Services. Dans ce scénario, vous devez sélectionner les appareils via le système d’exploitation. Si vous utilisez Chrome sur macOS, l’application peut énumérer ou sélectionner des appareils via le gestionnaire d’appareils de Communication Services.

### <a name="device-mutes-and-incoming-video-stops-rendering-when-certain-interruptions-occur"></a>Le son de l’appareil est coupé et la vidéo entrante cesse de s’afficher quand certaines interruptions se produisent

Ce problème peut se produire si une autre application ou le système d’exploitation prend le contrôle du microphone ou de la caméra. Voici quelques exemples qui peuvent se produire alors qu’un utilisateur participe à un appel :

- Un appel entrant est reçu via RTC (réseau téléphonique commuté) et il capture l’accès à l’appareil de catégorie micro.
- Un utilisateur visionne une vidéo YouTube, par exemple, ou démarre un appel FaceTime. Le basculement vers une autre application native peut capturer l’accès au microphone ou à la caméra.
- Un utilisateur active Siri, qui capture l’accès au microphone.

Dans tous ces cas, l’utilisateur doit revenir à l’application pour rétablir le son. En ce qui concerne la vidéo, l’utilisateur doit lancer la vidéo afin que les flux audio et vidéo reprennent après l’interruption.

Parfois, les appareils de catégorie micro ou caméra ne sont pas libérés à temps, ce qui peut entraîner des problèmes avec l’appel d’origine. Par exemple, si l’utilisateur tente de rétablir le son tout en regardant une vidéo YouTube, ou si un appel RTC est actif simultanément. 

L’environnement dans lequel ce problème se produit est le suivant :

- Bibliothèque de client : Appel (JavaScript)
- Navigateur : Safari
- Système d'exploitation : iOS

### <a name="repeatedly-switching-video-devices-might-cause-video-streaming-to-stop-temporarily"></a>La commutation répétée entre différents appareils vidéo peut entraîner l’arrêt temporaire du streaming vidéo

La commutation entre différents appareils vidéo peut entraîner la suspension de votre streaming vidéo pendant l’acquisition du flux à partir de l’appareil sélectionné. La commutation fréquente entre plusieurs appareils risque de détériorer les performances. Il est recommandé aux développeurs d’arrêter le streaming sur un appareil avant de le démarrer sur un autre.

### <a name="bluetooth-headset-microphone-isnt-detected-or-audible-during-the-call-on-safari-on-ios"></a>Le microphone du casque Bluetooth n’est pas détecté ou audible lors d’un appel via Safari sur iOS

Les casques Bluetooth ne sont pas pris en charge par Safari sur iOS. Votre appareil Bluetooth ne figurera pas dans les options de microphone disponibles et les autres participants ne seront pas en mesure de vous entendre si vous essayez d’utiliser Bluetooth sur Safari.

Il s'agit d'une limitation de système d’exploitation connue. Avec Safari sur macOS et iOS/iPadOS, il n’est pas possible d’énumérer ou de sélectionner des appareils de haut-parleur via le gestionnaire d’appareils de Communication Services. En effet, Safari ne prend pas en charge l’énumération ou la sélection de haut-parleurs. Dans ce scénario, utilisez le système d’exploitation pour mettre à jour la sélection de votre appareil.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>La rotation d’un appareil risque de détériorer fortement la qualité de la vidéo

Lorsque les utilisateurs font pivoter un appareil, ce mouvement peut dégrader la qualité de la vidéo en cours de streaming.

L’environnement dans lequel ce problème se produit est le suivant :

- Appareils affectés : Google Pixel 5, Google Pixel 3A, Apple iPad 8 et Apple iPad X
- Bibliothèque de client : Appel (JavaScript)
- Navigateurs : Safari, Chrome
- Systèmes d’exploitation : iOS, Android

### <a name="camera-switching-makes-the-screen-freeze"></a>La commutation d’appareil photo gèle l’affichage sur l’écran 

Lorsqu’un utilisateur Communication Services rejoint un appel à l’aide du kit SDK pour les appels JavaScript, puis sélectionne le bouton du bouton de commutation de la caméra, l’interface utilisateur peut se bloquer. L’utilisateur doit alors actualiser l’application ou envoyer (push) le navigateur en arrière-plan.

L’environnement dans lequel ce problème se produit est le suivant :

- Appareils affectés : Google Pixel 4a
- Bibliothèque de client : Appel (JavaScript)
- Navigateur : Chrome
- Systèmes d’exploitation : iOS, Android

### <a name="video-signal-problem-when-the-call-is-in-connecting-state"></a>Problème de signal vidéo lorsque l’appel est en état Connexion en cours 

Si un utilisateur active et désactive rapidement la vidéo pendant que l’appel est à l’état *Connexion en cours*, cela peut entraîner un problème avec le flux acquis pour l’appel. Il est conseillé aux développeurs de créer leurs applications d’une manière qui ne nécessite pas l’activation ou la désactivation de la vidéo pendant que l’appel est à l’état *Connexion en cours*. Des performances vidéo dégradées peuvent se produire dans les scénarios suivants :

 - Si l’utilisateur commence par l’audio, puis démarre et arrête la vidéo pendant que l’appel est à l’état *Connexion en cours*.
 - Si l’utilisateur commence par l’audio, puis démarre et arrête la vidéo pendant que l’appel est à l’état *Salle d’attente*.

### <a name="enumerating-or-accessing-devices-for-safari-on-macos-and-ios"></a>Accès aux appareils ou énumération de ces appareils pour Safari sur macOS et iOS 

Dans certains environnements, vous remarquerez peut-être que les autorisations de l’appareil sont réinitialisées après un certain temps. Sur macOS et iOS, Safari ne conserve pas les autorisations pendant une longue période, sauf si un flux est acquis. La façon la plus simple de contourner cela consiste à appeler l’API `DeviceManager.askDevicePermission()` avant d’appeler les API d’énumération d’appareils du gestionnaire d’appareils. Ces API d’énumération incluent `DeviceManager.getCameras()`, `DeviceManager.getSpeakers()` et `DeviceManager.getMicrophones()`. Si les autorisations sont définies, l’utilisateur ne verra rien. Si les autorisations ne sont pas définies, l’utilisateur sera invité à entrer à nouveau les autorisations.

L’environnement dans lequel ce problème se produit est le suivant :

- Appareil affecté : iPhone
- Bibliothèque de client : Appel (JavaScript)
- Navigateur : Safari
- Système d'exploitation : iOS

### <a name="delay-in-rendering-remote-participant-videos"></a>Délai de rendu de la vidéo des participants à distance

Pendant un appel de groupe en cours, supposons que l’_utilisateur A_ envoie du contenu vidéo, puis que l’_utilisateur B_ rejoint l’appel. Parfois, l’utilisateur B ne voit pas la vidéo de l’utilisateur A, ou le rendu de la vidéo de l’utilisateur A ne commence qu’après un long délai. Un problème de configuration de l’environnement réseau peut être à l’origine de ce délai. Pour plus d’informations, consultez [Recommandations réseau](./voice-video-calling/network-requirements.md).

### <a name="using-third-party-libraries-during-the-call-might-result-in-audio-loss"></a>L’utilisation de bibliothèques tierces pendant l’appel peut entraîner une perte audio

Si vous utilisez `getUserMedia` séparément dans l’application, le flux audio est perdu. Cela est dû au fait qu’une bibliothèque tierce prend le contrôle de l’accès à l’appareil à la place de la bibliothèque Azure Communication Services.

- N’utilisez pas de bibliothèques tierces qui utilisent l’API `getUserMedia` en interne pendant l’appel.
- Si vous avez toujours besoin d’utiliser une bibliothèque tierce, la seule façon de récupérer le flux audio est de changer l’appareil sélectionné (si l’utilisateur en possède plusieurs) ou de redémarrer l’appel.

L’environnement dans lequel ce problème se produit est le suivant :

- Navigateur : Safari
- Système d'exploitation : iOS

La cause de ce problème peut être que l’acquisition de votre propre flux à partir du même appareil aura pour effet secondaire de créer des conditions de concurrence. L’acquisition de flux à partir d’autres appareils peut conduire à une bande passante d’E/S USB insuffisante, et le taux `sourceUnavailableError` montera en flèche.  

### <a name="support-for-simulcast"></a>Prise en charge de simulcast

simulcast est une technique par laquelle un client encode le même flux vidéo deux fois, avec des résolutions et des débits différents. Le client laisse ensuite Communication Services décider du flux qu’un client doit recevoir. Le kit SDK de la bibliothèque d’appels Communication Services pour Windows, Android ou iOS prend en charge l’envoi de flux simulcast. Actuellement, le kit SDK web Communication Services ne prend pas en charge l’envoi de flux simulcast.

## <a name="communication-services-call-automation-apis"></a>API d’automatisation des appels Communication Services

Voici les problèmes connus dans les API d’automatisation des appels Communication Services :

- La seule authentification actuellement prise en charge pour les applications serveur consiste à utiliser une chaîne de connexion.

- Effectuez des appels uniquement entre des entités de la même ressource Communication Services. La communication entre les ressources est bloquée.

- Les appels entre les utilisateurs locataires de Microsoft Teams et des entités d’application serveur ou des utilisateurs Communication Services ne sont pas autorisés.

- Si une application se connecte à deux identités RTC ou plus, puis quitte l’appel, l’appel entre les autres entités RTC est abandonné.

