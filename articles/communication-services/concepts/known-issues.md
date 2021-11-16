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
ms.openlocfilehash: 9213d5fa18e2156962deb1e9cd6e49f592192728
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055452"
---
# <a name="known-issues"></a>Problèmes connus
Cet article fournit des informations sur les limitations et problèmes connus concernant les Kits de développement logiciel (SDK) Appel pour Azure Communication Services et Automatisation des appels Azure Communication Services.

> [!IMPORTANT]
> Plusieurs facteurs peuvent affecter la qualité de votre expérience d’appel. Pour en savoir plus sur la configuration de votre réseau et les meilleures pratiques en matière de tests pour Communication Services, veuillez consulter l’article **[Garantir un contenu multimédia de qualité supérieure dans Azure Communication Services](./voice-video-calling/network-requirements.md)** .

## <a name="azure-communication-services-calling-sdks"></a>Des SDK d’appel avec Azure Communication Services

### <a name="javascript-sdk"></a>Kit de développement logiciel (SDK) JavaScript

Cette section fournit des informations sur les problèmes connus associés aux Kits de développement logiciel (SDK) JavaScript pour les appels audio et vidéo Azure Communication Services.

#### <a name="ios-151-regression---iossafari-will-crash-and-refresh-the-page-if-acs-user-tries-to-send-video-in-a-call"></a>Régression iOS 15.1 : iOS/Safari se bloque et actualise la page si l’utilisateur ACS tente d’envoyer une vidéo dans un appel

---
**Important**

iOS 15.1 a introduit un bogue qui a un impact sur la majorité des appels ACS avec vidéo effectués dans iOS/Safari.

---

**Description du problème** Si vous participez à des appels ACS ou à une réunion Teams à l’aide d’ACS sur iOS 15.1 sur n’importe quel navigateur sur lequel la vidéo est activée, cela entraîne le blocage du navigateur Safari.

**Détails** Il s’agit d’un [bogue connu sur iOS 15.1 Safari](https://bugs.webkit.org/show_bug.cgi?id=231505) qui a été introduit avec la publication de la version iOS 15.1. Ce problème devrait être résolu avec la publication de la version iOS 15.2. Actuellement, la seule solution de contournement connue consiste à ne pas désactiver la vidéo lors de l’utilisation des services ACS sur iOS 15.1 Safari.

**Versions** iOS 15.1 et toute version du kit SDK Web ACS

**Atténuations** Empêchez les utilisateurs d’utiliser (d’activer) la vidéo lors de l’utilisation du navigateur iOS 15.1 Safari pour les appels vidéo ACS et les réunions vidéo de Microsoft Teams sur ACS.
Nous évaluons les solutions de contournement et travaillons avec Apple pour déterminer les étapes suivantes

La version suivante iOS 15.2 résout ce problème


#### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>L’actualisation d’une page ne retire pas immédiatement l’utilisateur de l’appel

Si un utilisateur participe à un appel et décide d’actualiser la page, le service multimédia de Communication Services ne retira pas cet utilisateur immédiatement de l’appel. Il va attendre que l’utilisateur se reconnecte. L’utilisateur sera supprimé de l’appel après l’expiration du délai d’attente du service multimédia.

Il est préférable de créer des expériences utilisateur qui ne nécessitent pas que les utilisateurs finaux actualisent la page de votre application pendant un appel. Si un utilisateur actualise la page, réutilisez le même identifiant utilisateur de Communication Services lorsqu’il se reconnecte à l’application.

Du point de vue des autres participants à l’appel, l’utilisateur restera dans l’appel pendant une minute ou deux.

Si l’utilisateur se reconnecte avec le même identifiant utilisateur de Communication Services, il est représenté sous la forme du même objet dans la collection `remoteParticipants`.

Si l’utilisateur envoie une vidéo avant l’actualisation, la collection `videoStreams` conserve les informations de flux précédentes jusqu’à ce que le service expire et le supprime. Dans ce scénario, l’application peut décider d’observer les nouveaux flux ajoutés à la collection et d’en afficher une avec l’`id` le plus élevé. 


#### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Il n’est pas possible d’afficher plusieurs aperçus à partir de plusieurs appareils sur le web
Il s'agit d'une limitation connue. Pour en savoir plus, consultez [Vue d’ensemble du kit SDK Appel](./voice-video-calling/calling-sdk-features.md).

#### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>L’énumération des appareils ne peut s’effectuer dans Safari lorsque l’application s’exécute sur iOS ou iPados

Les applications ne peuvent pas énumérer/sélectionner des appareils de catégorie « micro »/« haut-parleur » (comme Bluetooth) via le navigateur Safari sur iOS/iPad. Il s'agit d'une limitation de système d’exploitation connue.

Si vous utilisez Safari sur macOS, votre application ne sera pas en mesure d’énumérer/sélectionner des haut-parleurs via le Gestionnaire d’appareils de Communication Services. Dans ce scénario, les appareils doivent être sélectionnés via le système d’exploitation. Si vous utilisez Chrome sur macOS, l’application peut énumérer/sélectionner des appareils via le Gestionnaire d’appareils de Communication Services.

#### <a name="device-will-get-muted-and-incoming-video-will-stop-rendering-when-an-interruption-occurs-that-takes-over-device-access"></a>Le son de l’appareil est coupé et la vidéo entrante cesse de s’afficher quand une interruption se produit et prend le contrôle de l’accès à l’appareil.
Ce problème peut provenir principalement d’une autre application ou du système d’exploitation qui prend le contrôle du microphone ou de la caméra. Voici quelques exemples :

- Pendant que l’utilisateur est en communication, un appel RTC entrant arrive et monopolise l’accès au microphone.
- Pendant l’appel, l’utilisateur accède à une autre application native qui monopolise l’accès au microphone ou à la caméra, par exemple, pour lire une vidéo YouTube ou lancer un appel FaceTime.
- En cours d’appel, l’utilisateur active Siri qui monopolise à nouveau l’accès au microphone.

Dans tous ces cas, l’utilisateur doit revenir à l’application pour rétablir le son et lancer la vidéo afin que les flux audio et vidéo reprennent après l’interruption.

Dans certains cas, les dispositifs (microphone ou caméra) ne sont pas libérés à temps, ce qui peut occasionner des problèmes avec l’appel d’origine, par exemple, si l’utilisateur essaie de rétablir le son pendant qu’il regarde une vidéo YouTube ou si un appel RTC est actif simultanément. 

<br/>Bibliothèque de client : Appel (JavaScript)
<br/>Navigateurs : Safari
<br/>Système d'exploitation : iOS

#### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>La commutation répétée de différents appareils vidéo peut entraîner l’arrêt temporaire du streaming vidéo

La commutation de différents appareils vidéo peut entraîner la suspension de votre streaming vidéo pendant l’acquisition du flux à partir de l’appareil sélectionné.

##### <a name="possible-causes"></a>Causes possibles
La commutation fréquente entre plusieurs appareils risque de détériorer les performances. Il est recommandé aux développeurs d’arrêter le streaming sur un appareil avant de le démarrer sur un autre.

#### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>Le microphone du casque Bluetooth n’est pas détecté, les sons qu’il capte sont donc inaudibles lors d’un appel via Safari sur iOS
Les casques Bluetooth ne sont pas pris en charge par Safari sur iOS. Votre appareil Bluetooth ne figurera pas dans les options de microphone disponibles et les autres participants ne seront pas en mesure de vous entendre si vous essayez d’utiliser Bluetooth sur Safari.

##### <a name="possible-causes"></a>Causes possibles
Il s’agit d’une limitation du système d’exploitation macOS/iOS/iPadOS connue. 

Le navigateur Safari sur **macOS** et **iOS/iPad** ne permet pas d’énumérer/sélectionner des appareils de catégorie « haut-parleur » via le gestionnaire d'appareils de Communication Services, car l’énumération/sélection des haut-parleurs n’est pas prise en charge par Safari. Dans ce scénario, la sélection de votre appareil doit être mise à jour via le système d’exploitation.

#### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>La rotation d’un appareil risque de détériorer fortement la qualité de la vidéo
Les utilisateurs risquent de subir une détérioration de la qualité de leurs vidéos en cas de rotation de leurs appareils.

<br/>Appareils affectés : Google Pixel 5, Google Pixel 3A, Apple iPad 8 et Apple iPad X
<br/>Bibliothèque de client : Appel (JavaScript)
<br/>Navigateurs : Safari, Chrome
<br/>Système d’exploitation : iOS, Android


#### <a name="camera-switching-makes-the-screen-freeze"></a>La commutation d’appareil photo gèle l’affichage sur l’écran 
Lorsqu’un utilisateur de Communication Services rejoint un appel à l’aide du Kit de développement logiciel (SDK) JavaScript pour les appels, puis appuie sur le bouton du commutateur d’appareil photo, l’interface utilisateur peut cesser de répondre tant que l’application n’est pas actualisée ou que le navigateur n’est pas placé en arrière-plan par l’utilisateur.

<br/>Appareils affectés : Google Pixel 4a
<br/>Bibliothèque de client : Appel (JavaScript)
<br/>Navigateurs : Chrome
<br/>Système d’exploitation : iOS, Android


##### <a name="possible-causes"></a>Causes possibles
En cours d’examen.

#### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Si le signal vidéo a été arrêté alors que l’appel est à l’état « Connexion », la vidéo ne sera pas envoyée après le démarrage de l’appel 
Si les utilisateurs décident d’activer ou de désactiver rapidement une vidéo alors que l’appel est à l’état `Connecting`, cela peut entraîner un problème avec le stream acquis pour l’appel. Nous conseillons aux développeurs de créer leurs applications d’une manière qui ne nécessite pas l’activation ou la désactivation de la vidéo pendant que l’appel est à l’état `Connecting`. Ce problème peut entraîner une dégradation des performances de la vidéo dans les scénarios suivants :

 - Si l’utilisateur commence par l’audio, puis démarre et arrête la vidéo pendant que l’appel est à l’état `Connecting`.
 - Si l’utilisateur commence par l’audio, puis démarre et arrête la vidéo pendant que l’appel est à l’état `Lobby`.

##### <a name="possible-causes"></a>Causes possibles
En cours d’examen.

#### <a name="enumeratingaccessing-devices-for-safari-on-macos-and-ios"></a>Énumération des appareils et accès à ceux-ci via Safari sur macOS et iOS 
Si l’accès à un appareil est autorisé, après un certain temps, cette autorisation est réinitialisée. Safari sur macOS et iOS ne conserve pas les autorisations pendant très longtemps, sauf si un stream est acquis. La façon la plus simple de contourner ce problème consiste à appeler l’API DeviceManager.askDevicePermission() avant d’appeler les API d’énumération d’appareil du gestionnaire d’appareils (DeviceManager.getCameras(), DeviceManager.getSpeakers(), et DeviceManager.getMicrophones()). Si les autorisations sont valides, l’utilisateur ne verra rien. Dans le cas contraire, il sera réinvité à se connecter.

<br/>Appareils affectés : iPhone
<br/>Bibliothèque de client : Appel (JavaScript)
<br/>Navigateurs : Safari
<br/>Système d'exploitation : iOS

####  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Parfois, le rendu des vidéos des participants à une visioconférence prend beaucoup de temps
Pendant un appel de groupe en cours, l’_utilisateur A_ envoie une vidéo, puis l’_utilisateur B_ rejoint l’appel. Parfois, l’utilisateur B ne voit pas la vidéo de l’utilisateur A, ou le rendu de la vidéo de l’utilisateur A ne commence qu’après un long délai. Ce problème peut être dû à un environnement réseau qui requiert une configuration supplémentaire. Pour obtenir des conseils sur la configuration de votre réseau, veuillez consulter l’article [Garantir un contenu multimédia de qualité supérieure dans Azure Communication Services](./voice-video-calling/network-requirements.md).

#### <a name="using-3rd-party-libraries-to-access-gum-during-the-call-may-result-in-audio-loss"></a>L’utilisation de bibliothèques tierces pour accéder à GUM pendant l’appel peut entraîner une perte audio
L’utilisation de getUserMedia séparément dans l’application entraîne la perte du flux audio, car une bibliothèque tierce prend en charge l’accès à l’appareil à partir de la bibliothèque ACS.
Les développeurs sont encouragés à effectuer les opérations suivantes :
- N’utilisez pas les bibliothèques tierces qui utilisent l’API GetUserMedia en interne pendant l’appel.
- Si vous avez toujours besoin d’utiliser une bibliothèque tierce, la seule façon de récupérer le flux audio est de changer l’appareil sélectionné (si l’utilisateur en possède plusieurs) ou de redémarrer l’appel.

<br/>Navigateurs : Safari
<br/>Système d'exploitation : iOS

##### <a name="possible-causes"></a>Causes possibles
Dans certains navigateurs (par exemple, Safari), l’acquisition de votre propre flux à partir du même appareil aura pour effet secondaire de créer des conditions de concurrence. L’acquisition de flux à partir d’autres appareils peut conduire à une bande passante d’E/S USB insuffisante, et le taux sourceUnavailableError montera en flèche.  

#### <a name="support-for-simulcast"></a>Prise en charge de Simulcast
Le terme Simulcast désigne une technique par laquelle un client encode le même flux vidéo deux fois dans des résolutions et des débits différents et permet à l’infrastructure ACS de décider le flux qu’un client doit recevoir. Le kit SDK de la bibliothèque d’appels ACS pour Windows, Android ou iOS prend en charge l’envoi de flux Simulcast. Le kit SDK web ACS ne prend actuellement pas en charge l’envoi de flux Simulcast.

## <a name="azure-communication-services-call-automation-apis"></a>API d’automatisation des appels Azure Communication Services

Voici les problèmes connus dans les API d’automatisation des appels Azure Communication Services

- La seule authentification prise en charge pour le moment pour les applications serveur utilise une chaîne de connexion.

- Les appels doivent être effectués uniquement entre des entités de la même ressource Azure Communication Service. La communication entre les ressources est bloquée.

- Les appels entre les utilisateurs du locataire Teams et les entités d’utilisateur ou d’application serveur Azure Communication Service ne sont pas autorisés.

- Si une application se connecte à deux identités RTPC ou plus, puis quitte l’appel, l’appel entre les autres entités RTPC est abandonné.

