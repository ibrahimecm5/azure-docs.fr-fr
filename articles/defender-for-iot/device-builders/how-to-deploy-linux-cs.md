---
title: Installer et déployer l’agent Linux C#
description: Découvrez comment installer et déployer sur Linux l’agent de sécurité Defender pour IoT basé sur C#.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 5a403af7f5c0b6f2b8d5d497979be06fee404a19
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306089"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Déployer l’agent de sécurité Defender pour IoT basé sur C# pour Linux

Ce guide explique comment installer et déployer sur Linux l’agent de sécurité Defender pour IoT basé sur C#.

Dans ce guide, vous apprendrez comment :

- Installer
- Vérifier le déploiement
- Désinstaller l’agent
- Dépanner

## <a name="prerequisites"></a>Prérequis

Pour d’autres plateformes et versions de l’agent, consultez [Choisir l’agent de sécurité adéquat](how-to-deploy-agent.md).

1. Pour déployer l’agent de sécurité, les droits d’administrateur local sont requis sur l’ordinateur sur lequel vous souhaitez effectuer l’installation.

1. [Créez un micro-agent Defender-IoT](quickstart-create-security-twin.md) pour l’appareil.

## <a name="installation"></a>Installation

Pour déployer l’agent de sécurité, procédez comme suit :

1. Téléchargez la version la plus récente sur votre machine, depuis [GitHub](https://aka.ms/iot-security-github-cs).

1. Extrayez le contenu du package et accédez au dossier _/Install_.

1. Ajoutez des autorisations en cours d’exécution au script **InstallSecurityAgent** en exécutant `chmod +x InstallSecurityAgent.sh`.

1. Ensuite, exécutez la commande suivante avec des **privilèges racine** :

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   pour plus d’informations sur les paramètres d’authentification, consultez [Guide pratique sur la configuration de l’authentification](concept-security-agent-authentication-methods.md).

Le script effectue les opérations suivantes :

- Installation des composants requis.

- Ajout d’un utilisateur de service (avec connexion interactive désactivée).

- Installation de l’agent en tant que **démon**, ce qui suppose que l’appareil utilise **systemd** pour le modèle de déploiement classique.

- Configuration de **sudoers** pour permettre à l’agent d’effectuer certaines tâches en tant que racine.

- Configuration de l’agent avec les paramètres d’authentification fournis.

Pour obtenir de l’aide, exécutez le script avec le paramètre –help : `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Désinstaller l’agent

Pour désinstaller l’agent, exécutez le script avec le paramètre –u : `./InstallSecurityAgent.sh -u`.

> [!NOTE]
> La désinstallation ne supprime pas les prérequis manquants qui ont été installés pendant l’installation.

## <a name="troubleshooting"></a>Dépannage

1. Vérifiez l’état du déploiement en exécutant la commande suivante :

    `systemctl status ASCIoTAgent.service`

1. Activez la journalisation. Si l’agent ne parvient pas à démarrer, activez la journalisation pour obtenir plus d’informations.

   Pour activer la journalisation, effectuez les étapes suivantes :

   1. Ouvrez le fichier de configuration dans l’éditeur Linux de votre choix :

        `vi /var/ASCIoTAgent/General.config`

   1. Modifiez les valeurs suivantes :

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       La valeur **logFilePath** est configurable.

       > [!NOTE]
       > Nous vous recommandons de **désactiver** la journalisation une fois les problèmes résolus. Le fait de laisser la journalisation **active** augmente la taille du fichier journal et l’utilisation des données.

   1. Redémarrez l’agent en exécutant la commande suivante :

       `systemctl restart ASCIoTAgent.service`

   1. Pour plus d’informations sur l’échec, affichez le fichier journal.

       L’emplacement du fichier journal est : `/var/ASCIoTAgent/IotAgentLog.log`

       Changez le chemin du fichier en fonction du nom que vous avez choisi pour le paramètre **logFilePath** à l’étape 2.

## <a name="next-steps"></a>Étapes suivantes

- Lire la [vue d’ensemble](overview.md) du service Defender pour IoT
- Pour en savoir plus sur Defender pour IoT, consultez [Présentation de la solution basée sur un agent pour les fabricants d’appareils](architecture-agent-based.md)
- Activer le [service](quickstart-onboard-iot-hub.md)
- Consultez le [Forum aux questions sur Microsoft Defender pour IoT](resources-agent-frequently-asked-questions.md)
- Comprendre les [alertes](concept-security-alerts.md)
