---
title: Provisionner des appareils avec un TPM virtuel sur Windows – Azure IoT Edge | Microsoft Docs
description: Utiliser un TPM simulé sur un appareil Windows afin de tester le service de provisionnement des appareils Azure pour Azure IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 10/06/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0b3d6bba1e99131b5a03b8ba5acfb760566437b1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234752"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Créer et provisionner des appareils IoT Edge à grande échelle avec un module TPM sur Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions concernant le provisionnement automatique d’un appareil Azure IoT Edge pour Windows en utilisant un TPM (module de plateforme sécurisée). Les appareils IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Azure IoT Hub Device Provisioning](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la [présentation du provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Cet article décrit deux méthodologies. Sélectionnez votre préférence en fonction de l’architecture de votre solution :

- Provisionnement automatique d’un appareil Windows avec du matériel TPM physique.
- Provisionnement automatique d’un appareil Windows avec un TPM simulé. Nous vous recommandons d’utiliser cette méthode uniquement dans un scénario de test. Un module TPM simulé n’offre pas la même sécurité qu’un module TPM physique.

Les instructions varient en fonction de la méthode choisie. Vérifiez que vous vous trouvez dans l’onglet approprié.

Voici les tâches à effectuer :

# <a name="physical-tpm"></a>[TPM physique](#tab/physical-tpm)

* Récupérez les informations d’approvisionnement de votre appareil.
* Création d’une inscription individuelle pour l’appareil.
* Installation du runtime IoT Edge et connexion de l’appareil à IoT Hub.

# <a name="simulated-tpm"></a>[TPM simulé](#tab/simulated-tpm)

* Configurez votre TPM simulé et récupérez ses informations de configuration.
* Création d’une inscription individuelle pour l’appareil.
* Installation du runtime IoT Edge et connexion de l’appareil à IoT Hub.

---

## <a name="prerequisites"></a>Prérequis

Les prérequis sont les mêmes pour les solutions de TPM physique et de TPM virtuel.

* Une machine de développement Windows. Cet article utilise Windows 10.
* Un hub IoT actif.
* Une instance du service IoT Hub Device Provisioning dans Azure, liée à votre hub IoT.
  * Si vous n’avez pas d’instance du service de provisionnement des appareils, suivez les instructions fournies dans ces deux sections du guide de démarrage rapide consacré au service IoT Hub Device Provisioning :
    - [Créer un service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
    - [Lier le hub IoT et votre service Device Provisioning](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * Après avoir démarré le service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

> [!NOTE]
> TPM 2.0 est requis lorsque vous utilisez l’attestation TPM avec le service de provisionnement des appareils.
>
> Vous pouvez uniquement créer des inscriptions de service de provisionnement des appareils, et non de groupe, lorsque vous utilisez un module de plateforme sécurisée.

## <a name="set-up-your-tpm"></a>Configurer votre TPM

# <a name="physical-tpm"></a>[TPM physique](#tab/physical-tpm)

Dans cette section, vous créez un outil permettant de récupérer l’ID d’inscription et la paire de clés de type EK (Endorsement key) de votre module TPM.

1. Suivez les étapes décrites dans [Configurer un environnement de développement Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) pour installer et générer l’Azure IoT device SDK pour C.

1. Exécutez les commandes suivantes dans une session PowerShell avec élévation de privilèges pour créer l’outil Kit de développement logiciel (SDK) qui récupère les informations d’approvisionnement de votre appareil pour votre TPM.

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. La fenêtre Sortie affiche **l’ID d’inscription** et la **Paire de clés de type EK (Endorsement Key)** de l’appareil. Copiez ces valeurs. Vous vous en servirez plus tard, au moment de créer une inscription individuelle pour votre appareil dans le service de provisionnement des appareils.

> [!TIP]
> Si vous ne souhaitez pas utiliser l’outil SDK pour récupérer les informations, vous devez trouver un autre moyen d’obtenir les informations de provisionnement. La paire de clés de type EK, propre à chaque puce TPM, est fournie par le fabricant de la puce TPM associée. Vous pouvez dériver un ID d’inscription unique pour votre appareil TPM. Par exemple, vous pouvez créer un hachage SHA-256 de la paire de clés de type EK.

Une fois que vous avez votre ID d’inscription et votre paire de clés de type EK, vous pouvez continuer.

# <a name="simulated-tpm"></a>[TPM simulé](#tab/simulated-tpm)

Si vous n’avez pas de module de plateforme sécurisée (TPM) physique disponible et que vous souhaitez tester cette méthode d’approvisionnement, vous pouvez simuler un module de plateforme sécurisée sur votre appareil.

Le service IoT Hub Device Provisioning fournit des exemples qui simulent un module TPM et retournent la paire de clés de type EK (Endorsement Key) et l’ID d’inscription.

1. Choisissez l’un des exemples de la liste suivante, en fonction de votre langage par défaut.
1. Ne suivez plus les étapes de l’exemple de service de provisionnement des appareils une fois que le TPM simulé est en cours d’exécution et que vous avez collecté la **paire de clés de type EK** et l’**ID d’inscription**. N’appuyez pas sur **Entrée** pour exécuter l’inscription dans l’exemple d’application.
1. Laissez la fenêtre hébergeant le TPM en cours d’exécution jusqu’à ce que vous ayez terminé le test de ce scénario.
1. Revenez à cet article pour créer une inscription du service de provisionnement des appareils et configurer votre appareil.

Exemples de TPM simulés :

* [C](../iot-dps/quick-create-simulated-device-tpm.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm.md)

---

## <a name="create-a-device-provisioning-service-enrollment"></a>Créer une inscription dans le service de provisionnement des appareils

Utilisez les informations de provisionnement de votre TPM pour créer une inscription individuelle dans le service de provisionnement des appareils.

Lorsque vous créez une inscription dans le service de provisionnement des appareils, vous avez la possibilité de déclarer un **état initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

> [!TIP]
> Les étapes décrites dans cet article concernent le portail Azure, mais vous pouvez également créer des inscriptions individuelles en utilisant Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne un appareil IoT Edge.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle**, puis suivez cette procédure pour configurer l’inscription :

   1. Pour **Mécanisme**, sélectionnez **TPM**.

   1. Spécifiez la **paire de clés de type EK (Endorsement Key)** et l’**ID d’inscription** que vous avez copiés de votre machine virtuelle (VM) ou de votre appareil physique.

   1. Fournissez un ID pour votre appareil si vous le souhaitez. Si vous ne fournissez pas un ID d’appareil, l’**ID d’inscription** est utilisé.

   1. Sélectionnez **True** pour déclarer que votre machine virtuelle ou votre appareil physique est un appareil IoT Edge.

   1. Choisissez le hub IoT lié auquel vous voulez connecter votre appareil, ou sélectionnez **Lier à un nouveau hub IoT**. Vous pouvez choisir plusieurs hubs : l’appareil sera affecté à l’un d’entre eux en fonction de la stratégie d’attribution sélectionnée.

   1. Ajoutez une valeur de balise à l’**État initial du jumeau d’appareil** si vous le souhaitez. Vous pouvez utiliser des balises pour cibler des groupes d’appareils lors du déploiement de module. Pour plus d’informations, consultez [Déploiement de modules IoT Edge à grande échelle](how-to-deploy-at-scale.md).

   1. Sélectionnez **Enregistrer**.

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut provisionner automatiquement l’appareil lors de l’installation.

## <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

Dans cette section, vous préparez votre machine virtuelle ou votre appareil physique Windows pour IoT Edge. Ensuite, vous installez IoT Edge.

Vous devez effectuer une étape sur votre appareil pour qu’il soit prêt à installer le runtime IoT Edge. Votre appareil a besoin d’un moteur de conteneur installé.

### <a name="install-iot-edge"></a>Installer IoT Edge

Le démon de sécurité IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique comme une préversion, ou faire une installation en mode hors connexion, suivez les étapes d’installation hors connexion ou propres à une version.

1. Exécutez PowerShell ISE en tant qu’administrateur.

   Utilisez une session AMD64 de PowerShell, et non PowerShell (x86). Si vous ne savez pas quel type de session vous utilisez, exécutez la commande suivante :

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Exécutez la commande [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) qui effectue les tâches suivantes :

   * Vérifie que votre ordinateur Windows est sous une version prise en charge.
   * Active la fonctionnalité des conteneurs.
   * Télécharge le moteur Moby et le runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Redémarrez votre appareil si vous y êtes invité.

Quand vous installez IoT Edge sur un appareil, vous pouvez définir d’autres paramètres afin de modifier le processus pour :

* Diriger le trafic pour qu’il transite par un serveur proxy.
* Pointer le programme d’installation vers un répertoire local pour une installation hors connexion.

Pour plus d’informations sur ces autres paramètres, consultez [Scripts PowerShell pour IoT Edge sur conteneurs Windows](reference-windows-scripts.md).

## <a name="configure-the-device-with-provisioning-information"></a>Configurer l’appareil avec des informations de provisionnement

Une fois le runtime installé sur votre appareil, configurez ce dernier avec les informations qu’il utilise pour se connecter au service de provisionnement des appareils et à IoT Hub.

1. Récupérez les valeurs **Étendue de l’ID** du service de provisionnement des appareils et **ID d’inscription** de l’appareil qui ont été collectées dans les sections précédentes.

1. Ouvrez une fenêtre PowerShell en mode administrateur. Veillez à utiliser une session AMD64 de PowerShell, et pas PowerShell (x86), quand vous installez IoT Edge.

1. La commande `Initialize-IoTEdge` configure le runtime IoT Edge sur votre ordinateur. Par défaut, la commande est une commande d’approvisionnement manuel avec des conteneurs Windows. Ajoutez l’indicateur `-Dps` pour utiliser le service de provisionnement des appareils à la place du provisionnement manuel.

   Remplacez les valeurs d'espace réservé de `{scope_id}` et `{registration_id}` par les données que vous avez collectées précédemment.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, accédez à votre IoT Hub et commencez à déployer des modules IoT Edge sur votre appareil. Utilisez les commandes suivantes sur votre appareil pour vérifier que le runtime a été installé et démarré correctement.

1. Vérifiez l’état du service IoT Edge.

    ```powershell
    Get-Service iotedge
    ```

1. Examinez les journaux d’activité du service des 5 dernières minutes.

    ```powershell
    . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
    ```

1. Répertoriez les modules en cours d’exécution.

    ```powershell
    iotedge list
    ```

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription dans le service de provisionnement des appareils vous permet de définir les ID d’appareil et les étiquettes de jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique.

Découvrez comment [déployer et superviser des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou d’[Azure CLI](how-to-deploy-cli-at-scale.md).