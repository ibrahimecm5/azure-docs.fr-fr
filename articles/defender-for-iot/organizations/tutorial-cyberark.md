---
title: Intégrer CyberArk à Microsoft Defender pour IoT
description: Dans ce tutoriel, vous allez découvrir comment intégrer Microsoft Defender pour IoT à CyberArk.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: cbfa178c2e5358503208773109fc53ab3c274be7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278376"
---
# <a name="tutorial-integrate-cyberark-with-microsoft-defender-for-iot"></a>Tutoriel : Intégrer CyberArk à Microsoft Defender pour IoT

Ce tutoriel va vous permettre de découvrir comment intégrer CyberArk à Microsoft Defender pour IoT et comment l’utiliser.

Defender pour IoT fournit une plateforme de cybersécurité ICS et IoT prenant en charge des fonctionnalités de machine learning et d’analytique des menaces ICS.

Les acteurs à l’origine des menaces utilisent des informations d’identification d’accès à distance compromises pour accéder aux réseaux d’infrastructures critiques via des connexions Bureau à distance et VPN. En utilisant des connexions approuvées, cette approche permet de contourner facilement la sécurité de périmètre OT (technologie opérationnelle). Les informations d’identification sont généralement volées aux utilisateurs disposant de privilèges d’accès (ingénieurs de contrôle et personnel de maintenance des partenaires, par exemple), qui leur permettent d’effectuer à distance les tâches quotidiennes.

L’intégration de Defender pour IoT à CyberARK vous permet d’effectuer les tâches suivantes :

- Réduire les risques OT liés à un accès à distance non autorisé

- Fournir une supervision continue et une sécurité d’accès basée sur les privilèges pour l’OT

- Améliorer la réponse aux incidents, la chasse aux menaces et la modélisation de ces dernières

L’appliance Defender pour IoT est connectée au réseau OT via un port SPAN (port miroir) sur les appareils réseau tels que les commutateurs et les routeurs via une connexion unidirectionnelle (entrante) aux interfaces réseau dédiées sur l’appliance Defender pour IoT.

Une interface réseau dédiée est également fournie dans l’appliance Defender pour IoT à des fins de gestion centralisée et d’accès aux API. Cette interface est également utilisée pour communiquer avec la solution PSM de CyberArk, qui est déployée dans le centre de données de l’organisation pour gérer les utilisateurs disposant de privilèges et sécuriser les connexions d’accès à distance.

:::image type="content" source="media/tutorial-cyberark/architecture.png" alt-text="Déploiement de la solution PSM de CyberArk":::

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Configurer PSM dans CyberArk
> - Activer l’intégration dans Defender pour IoT
> - Voir et gérer les détections
> - Arrêter l’intégration

## <a name="prerequisites"></a>Prérequis

- CyberARK version 2.0.

- Vérifiez que vous disposez d’un accès à l’interface de ligne de commande pour toutes les appliances Defender pour IoT de votre entreprise.

- Un compte Azure. Si vous n’avez pas encore de compte Azure, vous pouvez [créer un compte Azure gratuit dès aujourd’hui](https://azure.microsoft.com/free/).

## <a name="configure-psm-cyberark"></a>Configurer PSM dans CyberArk

CyberArk doit être configuré pour autoriser la communication avec Defender pour IoT. Cette communication s’effectue via la configuration de PSM.

**Pour configurer PSM** :

1. Recherchez, puis ouvrez le fichier `c:\Program Files\PrivateArk\Server\dbparam.xml`.

1. Ajoutez les paramètres suivants :

    `[SYSLOG]` <br>
    `UseLegacySyslogFormat=Yes` <br>
    `SyslogTranslatorFile=Syslog\CyberX.xsl` <br>
    `SyslogServerIP=<CyberX Server IP>` <br>
    `SyslogServerProtocol=UDP` <br>
    `SyslogMessageCodeFilter=319,320,295,378,380` <br>

1. Enregistrez le fichier, puis fermez-le.

1. Placez le fichier config Syslog de Defender pour IoT `CyberX.xsl` dans `c:\Program Files\PrivateArk\Server\Syslog\CyberX.xsl`.

1. Ouvrez **Server Central Administration**.

1. Sélectionnez le **feu rouge** pour arrêter le serveur.

    :::image type="content" source="media/tutorial-cyberark/server.png" alt-text="Capture d’écran du feu rouge dans la boîte de dialogue Server Central Administration.":::

1. Sélectionnez le **feu vert** pour démarrer le serveur.

## <a name="enable-the-integration-in-defender-for-iot"></a>Activer l’intégration dans Defender pour IoT

Pour permettre l’intégration, le serveur Syslog doit être activé dans la console de gestion de Defender pour IoT. Par défaut, le serveur Syslog écoute l’adresse IP du système à l’aide du port 514 UDP.

**Pour configurer Defender pour IoT** :

1. Dans la console de gestion de Defender pour IoT, accédez à **Paramètres système**.

1. Passez l’option relative au serveur Syslog à l’état **Activé**.

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="Capture d’écran du serveur Syslog activé.":::

1. (Facultatif) Changez le port en vous connectant au système via l’interface CLI, accédez à `/var/cyberx/properties/syslog.properties`, puis changez `listener: 514/udp`.

## <a name="view-and-manage-detections"></a>Voir et gérer les détections

L’intégration entre Microsoft Defender pour IoT et PSM de CyberArk est effectuée via les messages Syslog. Ces messages sont envoyés par la solution PSM à Defender pour IoT. Ils notifient Defender pour IoT en cas de session à distance ou d’échec de vérification.

Une fois que la plateforme Defender pour IoT reçoit ces messages de PSM, elle les met en corrélation avec les données visibles sur le réseau, ce qui lui permet de vérifier que les connexions d’accès à distance au réseau ont été générées par la solution PSM, et non par un utilisateur non autorisé.

### <a name="view-alerts"></a>Afficher les alertes

Chaque fois que la plateforme Defender pour IoT identifie des sessions à distance qui n’ont pas été autorisées par PSM, elle émet un message `Unauthorized Remote Session`. Pour faciliter l’investigation immédiate, l’alerte indique également les adresses IP et les noms des appareils source et de destination.

**Pour voir les alertes** :

1. Connectez-vous à la console de gestion.

1. Sélectionnez **Alerte** dans le panneau latéral gauche.

1. Dans la liste des alertes, sélectionnez l’alerte intitulée **Unauthorized Remote Session**.

    :::image type="content" source="media/tutorial-cyberark/unauthorized.png" alt-text="Alerte de session à distance non autorisée.":::

### <a name="event-timeline"></a>Chronologie des événements

Chaque fois que PSM autorise une connexion à distance, celle-ci est visible dans la page Chronologie des événements de Defender pour IoT. La page Chronologie des événements affiche une chronologie de l’ensemble des alertes et notifications.

**Pour voir la chronologie des événements** :

1. Connectez-vous au capteur Defender pour IoT.

1. Sélectionnez **Chronologie des événements** dans le panneau latéral gauche.

1. Recherchez un événement intitulé Session à distance PSM.

    :::image type="content" source="media/tutorial-cyberark/event.png" alt-text="Vue de l’écran du journal des événements.":::

### <a name="auditing--forensics"></a>Audit et forensique

Les administrateurs peuvent auditer et investiguer les sessions d’accès à distance en interrogeant la plateforme Defender pour IoT via son interface d’exploration de données intégrée. Ces informations peuvent être utilisées pour identifier toutes les connexions d’accès à distance qui ont eu lieu. Elles permettent notamment d’obtenir certains détails forensiques tels que la provenance ou la destination des connexions aux appareils, les protocoles (RDP ou SSH), les utilisateurs sources et de destination, les horodatages ainsi que le recensement des sessions autorisées ou non à l’aide de PSM.

**Pour auditer et investiguer** :

1. Connectez-vous au capteur Defender pour IoT.

1. Sélectionnez **Exploration de données** dans le panneau latéral gauche.

1. Sélectionnez **Accès à distance**.

    :::image type="content" source="media/tutorial-cyberark/data-mining.png" alt-text="Vue de l’interface d’exploration de données.":::

## <a name="stop-the-integration"></a>Arrêter l’intégration

À tout moment, vous pouvez arrêter la communication de l’intégration.

**Pour arrêter l’intégration** :

1. Dans la console de gestion de Defender pour IoT, accédez à l’écran **Paramètres système**.

1. Passez l’option relative au serveur Syslog à l’état **Désactivé**.

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="Vue de l’état du serveur.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Il n’y a pas de ressources à nettoyer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment bien démarrer avec l’intégration de CyberArk. Poursuivez pour en savoir plus sur l’intégration de Forescout.

> [!div class="nextstepaction"]
> [Bouton Étapes suivantes](./tutorial-forescout.md)
