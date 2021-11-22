---
title: Installer et déployer l’agent Linux C
description: Découvrez comment installer et déployer sur Linux l’agent de sécurité Defender pour IoT basé sur C.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 1b1c81b73c6e7b0c32031de256b029a1a1164f4d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306108"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Déployer l’agent de sécurité Defender pour IoT basé sur C pour Linux

Ce guide explique comment installer et déployer sur Linux l’agent de sécurité Defender pour IoT basé sur C.

- Installer
- Vérifier le déploiement
- Désinstaller l’agent
- Dépanner

## <a name="prerequisites"></a>Prérequis

Pour d’autres plateformes et versions de l’agent, consultez [Choisir l’agent de sécurité adéquat](how-to-deploy-agent.md).

1. Pour déployer l’agent de sécurité, les droits d’administrateur local sont requis sur l’ordinateur sur lequel vous souhaitez effectuer l’installation (sudo).

1. [Créez un micro-agent Defender-IoT](quickstart-create-security-twin.md) pour l’appareil.

## <a name="installation"></a>Installation

Pour installer et déployer l’agent de sécurité, procédez comme suit :

1. Téléchargez la version la plus récente sur votre machine, depuis [GitHub](https://aka.ms/iot-security-github-c).

1. Extrayez le contenu du package et accédez au dossier _/src/installation_.

1. Ajoutez des autorisations en cours d’exécution au **script InstallSecurityAgent** en exécutant la commande suivante :

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Ensuite, exécutez la commande suivante :

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Pour plus d’informations sur les paramètres d’authentification, consultez [Guide pratique pour configurer l’authentification](concept-security-agent-authentication-methods.md).

Le script effectue l’opération suivante :

1. Installation des composants requis.

1. Ajout d’un utilisateur de service (avec connexion interactive désactivée).

1. Installation de l’agent en tant que **démon**, ce qui suppose que l’appareil utilise **systemd** pour la gestion des services.

1. Configuration de l’agent avec les paramètres d’authentification fournis.

Pour obtenir de l’aide, exécutez le script avec le paramètre –help :

`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Désinstaller l’agent

Pour désinstaller l’agent, exécutez le script avec le paramètre –-uninstall :

`./InstallSecurityAgent.sh -–uninstall`

## <a name="troubleshooting"></a>Dépannage

Vérifiez l’état du déploiement en exécutant la commande suivante :

`systemctl status ASCIoTAgent.service`

## <a name="next-steps"></a>Étapes suivantes

- Lire la [vue d’ensemble](overview.md) du service Defender pour IoT
- Pour en savoir plus sur Defender pour IoT, consultez [Présentation de la solution basée sur un agent pour les fabricants d’appareils](architecture-agent-based.md)
- Activer le [service](quickstart-onboard-iot-hub.md)
- Consultez le [Forum aux questions sur Microsoft Defender pour IoT](resources-agent-frequently-asked-questions.md)
- Comprendre les [alertes de sécurité](concept-security-alerts.md)
