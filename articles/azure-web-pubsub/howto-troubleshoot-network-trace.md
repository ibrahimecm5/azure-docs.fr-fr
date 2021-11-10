---
title: Comment collecter une trace du réseau
description: Découvrez comment obtenir la trace du réseau pour faciliter la résolution des problèmes.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 48209fc5f4876c097d30ce6bcf464ba40ee94a7a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997606"
---
# <a name="how-to-collect-a-network-trace"></a>Comment collecter une trace du réseau

Si vous rencontrez un problème, une trace du réseau peut parfois fournir des informations très utiles. Ce guide pratique vous présente les options permettant de collecter une trace du réseau.

> [!WARNING]
> Une trace du réseau contient le contenu complet de chaque message envoyé par votre application. Ne publiez **jamais** les traces de réseau brutes des applications de production sur des forums publics comme GitHub.

## <a name="collect-a-network-trace-with-fiddler"></a>Collecter une trace du réseau avec Fiddler

Fiddler est un outil puissant de collecte de traces HTTP. Installez-le depuis l’adresse [telerik.com/fiddler](https://www.telerik.com/fiddler), lancez-le, puis exécutez votre application et reproduisez le problème. Fiddler est disponible pour Windows, macOS et Linux. 

Si vous vous connectez en utilisant HTTPS, vous devez effectuer quelques étapes supplémentaires pour que Fiddler puisse déchiffrer le trafic HTTPS. Pour plus d’informations, consultez la [documentation de Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Une fois que vous avez collecté la trace, vous pouvez l’exporter en choisissant **Fichier** > **Enregistrer** > **Toutes les sessions** dans la barre de menus.

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Collecter une trace du réseau avec tcpdump (macOS et Linux uniquement)

Cette méthode fonctionne pour toutes les applications.

Vous pouvez collecter des traces TCP brutes à l’aide de tcpdump en exécutant la commande suivante à partir d’un interpréteur de commandes. Vous devrez peut-être `root` ou préfixer la commande avec `sudo` si vous recevez une erreur d’autorisation :

```console
tcpdump -i [interface] -w trace.pcap
```

Remplacez `[interface]` par l’interface réseau sur laquelle vous souhaitez effectuer la capture. En règle générale, cela ressemble à `/dev/eth0` (pour votre interface Ethernet standard) ou à `/dev/lo0` (pour le trafic localhost). Pour plus d’informations, consultez la page man de `tcpdump` sur votre système hôte.

```console
man tcpdump
```

## <a name="collect-a-network-trace-in-the-browser-browser-based-apps-only"></a>Collecter une trace du réseau dans le navigateur (applications basées sur un navigateur uniquement)

La plupart des outils de développement des navigateurs possèdent un onglet « Réseau » qui vous permet de capturer l’activité réseau entre le navigateur et le serveur. 

### <a name="microsoft-edge-chromium"></a>Microsoft Edge (Chromium)

1. Ouvrez les [outils de développement](/microsoft-edge/devtools-guide-chromium/).
    * Sélectionnez `F12` 
    * Sélectionnez `Ctrl`+`Shift`+`I` \(Windows/Linux\) ou `Command`+`Option`+`I` \(macOS\).
    * Sélectionnez `Settings and more`, puis `More Tools > Developer Tools`.  
1. Sélectionnez l’onglet `Network`.
1. Actualisez la page (le cas échéant) et reproduisez le problème.
1. Sélectionnez `Export HAR...` dans la barre d’outils pour exporter la trace en tant que fichier « HAR ».

    :::image type="content" source="./media/howto-troubleshoot-network-trace/edge-export-network-trace.png" alt-text="Collecter la trace du réseau avec Microsoft Edge":::

### <a name="google-chrome"></a>Google Chrome

1. Ouvrez les [outils de développement de Chrome](https://developers.google.com/web/tools/chrome-devtools).
    * Sélectionnez `F12` 
    * Sélectionnez `Ctrl`+`Shift`+`I` \(Windows/Linux\) ou `Command`+`Option`+`I` \(macOS\).  
    * Sélectionnez `Customize and control Google Chrome`, puis `More Tools > Developer Tools`.
1. Sélectionnez l’onglet `Network`.
1. Actualisez la page (le cas échéant) et reproduisez le problème.
1. Sélectionnez `Export HAR...` dans la barre d’outils pour exporter la trace en tant que fichier « HAR ».

    :::image type="content" source="./media/howto-troubleshoot-network-trace/chrome-export-network-trace.png" alt-text="Collecter la trace du réseau avec Google Chrome":::

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Ouvrez les [outils de développement de Firefox](https://developer.mozilla.org/en-US/docs/Tools).
    * Sélectionnez `F12`
    * Sélectionnez `Ctrl`+`Shift`+`I` \(Windows/Linux\) ou `Command`+`Option`+`I` \(macOS\). 
    * Sélectionnez `Open menu`, puis `Web Developer > Toggle Tools`.
1. Sélectionnez l’onglet `Network`.
1. Actualisez la page (le cas échéant) et reproduisez le problème.
1. Cliquez avec le bouton droit n’importe où dans la liste des requêtes, puis choisissez « Tout enregistrer au format HAR ».

    :::image type="content" source="./media/howto-troubleshoot-network-trace/firefox-export-network-trace.png" alt-text="Collecter la trace du réseau avec Mozilla Firefox":::

### <a name="safari"></a>Safari

1. Ouvrez les [outils de développement web](https://developer.apple.com/safari/tools/).
    * Sélectionnez `Command`+`Option`+`I`.
    * Sélectionnez le menu `Developer`, puis `Show Web Inspector`. 
1. Sélectionnez l’onglet `Network`.
1. Actualisez la page (le cas échéant) et reproduisez le problème.
1. Cliquez avec le bouton droit n’importe où dans la liste des requêtes, puis choisissez « Tout enregistrer au format HAR ».