---
title: 'Démarrage rapide : Installer le micro-agent Defender pour IoT (préversion)'
description: Suivez ce guide de démarrage rapide pour apprendre à installer et authentifier le micro-agent Defender pour le cloud.
ms.date: 11/09/2021
ms.topic: quickstart
ms.openlocfilehash: 5e1ef76bbaf3b4eedae31f08d6cf39efdfaec3df
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343740"
---
# <a name="quickstart-install-defender-for-iot-micro-agent-preview"></a>Démarrage rapide : Installer le micro-agent Defender pour IoT (préversion)

Cet article explique comment installer et authentifier le micro-agent Defender pour le cloud.

## <a name="prerequisites"></a>Prérequis

Avant d’installer le module Defender pour IoT, vous devez créer une identité de module dans l’IoT Hub. Pour plus d’informations sur la manière de créer une identité de module, consultez [Créer un jumeau de module pour le micro-agent Defender pour le cloud pour IoT (préversion)](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>Installer le package

**Pour ajouter le référentiel de packages Microsoft approprié** :

1. Téléchargez la configuration du référentiel correspondant au système d’exploitation de votre appareil.  

    - Pour Ubuntu 18.04

        ```bash
        curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
        ```

    - Pour Ubuntu 20.04

        ```bash
            curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list > ./microsoft-prod.list
        ```

    - Pour Debian 9 (AMD64 et ARM64)

        ```bash
        curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
        ```

1. Copiez la configuration du référentiel dans le répertoire `sources.list.d`.

    ```bash
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```

1. Installez la clé publique Microsoft GPG :

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

Pour installer le package du micro-agent Defender pour le cloud sur Debian et sur les distributions Linux basées sur Ubuntu, utilisez la commande suivante :

```bash
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Méthodes d’authentification du micro-agent

Les deux options utilisées pour authentifier le micro-agent Defender pour IoT sont les suivantes :

- Chaîne de connexion d’identité de module.

- Certificat.

### <a name="authenticate-using-a-module-identity-connection-string"></a>Authentifier à l’aide d’une chaîne de connexion d’identité de module

Veillez à ce que les [conditions préalables](#prerequisites) pour cet article soient réunies et à créer une identité de module avant de commencer ces étapes.

#### <a name="get-the-module-identity-connection-string"></a>Obtient la chaîne de connexion d’identité de module

Pour récupérer la chaîne de connexion d’identité de module à partir de l’IoT Hub :

1. Accédez à l’IoT Hub, puis sélectionnez votre hub.

1. Dans le menu de gauche, dans la section **Explorateurs** , sélectionnez **Appareils IOT**.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="Dans le menu de gauche, sélectionnez appareils IoT.":::

1. Sélectionnez un appareil dans la liste d’ID d’appareils pour afficher la page **Détails de l’appareil**.

1. Sélectionnez l’onglet  **Identités de module** .

1. Sélectionnez le module  **DefenderIotMicroAgent**  dans la liste des identités de module associées à l’appareil.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="Sélectionnez l’onglet Identités de module.":::

1. Dans la page **Détails d’identité de module**, copiez la chaîne de connexion (clé primaire) en sélectionnant le bouton **Copier**.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="Sélectionnez le bouton Copier pour copier la chaîne de connexion (clé primaire).":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>Configurer l’authentification à l’aide d’une chaîne de connexion d’identité de module

Pour configurer l’agent de sorte qu’il s’authentifie à l’aide d’une chaîne de connexion d’identité de module :

1. Placez un fichier nommé `connection_string.txt` contenant la chaîne de connexion codée au format UTF-8 dans le chemin `/var/defender_iot_micro_agent` du répertoire de l’agent Defender pour le cloud en entrant la commande suivante :

    ```bash
    sudo bash -c 'echo "<connection string>" > /var/defender_iot_micro_agent/connection_string.txt'
    ```

    Le fichier `connection_string.txt` doit se trouver dans l’emplacement ayant pour chemin d’accès `/var/defender_iot_micro_agent/connection_string.txt`.

1. Redémarrez le service à l’aide de cette commande :  

    ```bash
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>S’authentifier à l’aide d’un certificat

Pour s’authentifier à l’aide d’un certificat :

1. Procurez-vous un certificat en suivant [ces instructions](../../iot-hub/tutorial-x509-scripts.md).

1. Placez la partie publique codée en PEM du certificat et la clé privée dans le répertoire de l’agent Defender pour le cloud, dans les fichiers nommés `certificate_public.pem` et `certificate_private.pem`.

1. Placez la chaîne de connexion appropriée dans le fichier `connection_string.txt`. La chaîne de connexion doit ressembler à ceci :

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true`

    Cette chaîne alerte l’agent Defender pour le cloud, afin de demander qu’un certificat soit fourni pour l’authentification.

1. Redémarrez le service à l’aide de la commande suivante :  

    ```bash
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Valider votre installation

Pour valider votre installation :

1. Vérifiez que le micro-agent s’exécute correctement avec la commande suivante :  

    ```bash
    systemctl status defender-iot-micro-agent.service
    ```

1. Vérifiez que le service est stable en contrôlant qu’il est actif (`active`) et que la durée du bon fonctionnement du processus est appropriée

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Vérifiez que votre service est stable et actif.":::

## <a name="testing-the-system-end-to-end"></a>Test du système de bout en bout

Vous pouvez tester le système de bout en bout en créant un fichier déclencheur sur l’appareil. Le fichier déclencheur fait en sorte que l’analyse de ligne de base effectuée dans l’agent détecte le fichier comme une violation de la ligne de base.

Créez un fichier sur le système de fichiers avec la commande suivante :

```bash
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```

Une recommandation d’échec de validation de ligne de base se produit dans le hub, avec CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0 comme valeur `CceId` :

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="Recommandation d’échec de validation de ligne de base qui se produit dans le hub." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Patientez jusqu’à une heure pour que la recommandation apparaisse dans le hub.

## <a name="micro-agent-versioning"></a>Gestion de versions du micro-agent

Pour installer une version spécifique du micro-agent Defender pour le cloud pour IoT, exécutez la commande suivante :

```bash
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un jumeau de module pour le micro-agent Defender pour le cloud pour IoT (préversion)](quickstart-create-micro-agent-module-twin.md)
