---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2f4c83e7bb3976d7ac4eba245a921d01d40f3a92
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505475"
---
## <a name="install-iot-edge"></a>Installer IoT Edge

Dans cette section, vous préparez votre machine virtuelle ou votre appareil physique Linux pour IoT Edge. Ensuite, vous installez IoT Edge.

Vous devez effectuer deux étapes sur votre appareil pour qu’il soit prêt à installer le runtime IoT Edge. Votre appareil a besoin d’un accès aux packages d’installation Microsoft, et un moteur de conteneur doit être installé.

### <a name="access-the-microsoft-installation-packages"></a>Accéder aux packages d’installation Microsoft

1. Installez la configuration du référentiel qui correspond au système d’exploitation de votre appareil.

   * **Ubuntu Server 18.04** :

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch** :

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. Copiez la liste générée dans le répertoire sources.list.d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Installez la clé publique Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trust.gpg.d/
   ```

> [!NOTE]
> Les packages logiciels Azure IoT Edge sont soumis aux termes du contrat de licence situés dans chaque package (`usr/share/doc/{package-name}` ou dans le répertoire `LICENSE`). Lisez les termes du contrat de licence avant d’utiliser un package. Le fait d’installer et d’utiliser un package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package en question.

### <a name="install-a-container-engine"></a>Installer un moteur de conteneur

Azure IoT Edge s’appuie sur un runtime de conteneur compatible avec OCI. Dans les scénarios de production, nous vous recommandons d’utiliser le moteur Moby. Le moteur Moby est le seul moteur de conteneur officiellement pris en charge avec IoT Edge. Les images conteneur Docker CE/EE sont compatibles avec le runtime Moby.

1. Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

1. Installez le moteur Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Si des erreurs surviennent quand vous installez le moteur de conteneur Moby, vérifiez la compatibilité de votre noyau Linux avec Moby. Certains fabricants d’appareils embarqués livrent des images d’appareils qui contiennent des noyaux Linux personnalisés sans les fonctionnalités nécessaires à la compatibilité du moteur de conteneur. Exécutez la commande suivante, qui utilise le [script check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) fourni par Moby pour vérifier la configuration de votre noyau :
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > Dans la sortie du script, vérifiez que tous les éléments sous `Generally Necessary` et `Network Drivers` sont activés. S’il vous manque des fonctionnalités, activez-les en regénérant votre noyau à partir de la source et en sélectionnant les modules associés à inclure dans le fichier .config du noyau approprié. De la même façon, si vous utilisez un générateur de configuration du noyau comme `defconfig` ou `menuconfig`, recherchez et activez les fonctionnalités respectives, et regénérez votre noyau en conséquence. Une fois que vous avez déployé votre nouveau noyau modifié, réexécutez le script check-config pour vérifier que toutes les fonctionnalités requises ont été activées avec succès.

### <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Le démon de sécurité IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, comme une préversion, ou si vous devez installer en mode hors connexion, suivez les étapes d’**installation d’une version hors connexion ou spécifique** plus loin dans cet article.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Installez IoT Edge version 1.1.* avec le package **libiothsm-std** :

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge version 1.1 est la branche de support à long terme d’IoT Edge. Si vous exécutez une version antérieure, nous vous recommandons d’installer ou de mettre à jour vers le correctif le plus récent, car les versions antérieures ne sont plus prises en charge.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Le service IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le service se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

Le service IoT Identity a été introduit avec la version 1.2 d’IoT Edge. Ce service gère l’approvisionnement et la gestion des identités pour IoT Edge et pour d’autres composants d’appareil qui doivent communiquer avec IoT Hub.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, comme une préversion, ou si vous devez installer en mode hors connexion, suivez les étapes d’**installation d’une version hors connexion ou spécifique** plus loin dans cet article.

>[!NOTE]
>Les étapes décrites dans cette section vous indiquent la marche à suivre pour installer IoT Edge version 1.2.
>
>Si vous disposez déjà d’un appareil IoT Edge exécutant une version plus ancienne et que vous souhaitez effectuer une mise à niveau vers la version 1.2, suivez les étapes décrites dans [Mettre à jour le runtime et le démon de sécurité IoT Edge](../articles/iot-edge/how-to-update-iot-edge.md). La version 1.2 est si différente des versions précédentes d’IoT Edge que des étapes spécifiques sont nécessaires pour la mise à niveau.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Vérifiez les versions d’IoT Edge et le service d’identité IoT disponibles.

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

Pour installer la dernière version d’IoT Edge et le package du service d’identité IoT, utilisez la commande suivante :

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- end 1.2 -->
::: moniker-end
