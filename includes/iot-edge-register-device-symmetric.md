---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 11a89e1333ddcf3afd3594c3bd2ce46eda6513f1
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131577535"
---
## <a name="register-your-device"></a>Inscrire votre appareil

Vous avez le choix d’utiliser le **portail Azure**, **Visual Studio Code** ou **Azure CLI** pour inscrire votre appareil.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans votre hub IoT dans le portail Azure, les appareils IoT Edge sont créés et managés séparément des appareils IoT qui ne sont pas compatibles avec Edge.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.

1. Dans le volet de gauche, sélectionnez **IoT Edge** dans le menu, puis sélectionnez **Ajouter un appareil IoT Edge**.

   ![Ajouter un appareil IoT Edge à partir du portail Azure](./media/iot-edge-register-device-symmetric/portal-add-iot-edge-device.png)

1. Dans la page **Créer un appareil**, fournissez les informations suivantes :

   * Créez un ID d’appareil descriptif. Notez cet ID d’appareil, car vous en aurez besoin plus tard.
   * Sélectionnez **Clé symétrique** comme type d’authentification.
   * Utilisez les paramètres par défaut pour générer automatiquement les clés d’authentification et connecter le nouvel appareil à votre hub.

1. Sélectionnez **Enregistrer**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Se connecter pour accéder à votre hub ioT

Vous pouvez utiliser les extensions Azure IoT pour Visual Studio Code afin d’effectuer des opérations avec votre hub IoT. Pour que ces opérations fonctionnent, vous devez vous connecter à votre compte Azure et sélectionner votre hub.

1. Dans Visual Studio Code, ouvrez la vue **Explorateur**.
1. Au bas de l’Explorateur, développez la section **Azure IoT Hub**.

   ![Développer la section Appareils Azure IoT Hub](./media/iot-edge-register-device-symmetric/azure-iot-hub-devices.png)

1. Cliquez sur **...** dans l’en-tête de section **Azure IoT Hub**. Si vous ne voyez pas les points de suspension, cliquez ou pointez sur l’en-tête.
1. Choisissez **Sélectionner un hub IoT**.
1. Si vous n’êtes pas connecté à votre compte Azure, suivez les invites de connexion.
1. Sélectionnez votre abonnement Azure.
1. Sélectionnez votre hub IoT.

### <a name="register-a-new-device-with-visual-studio-code"></a>Inscrire un nouvel appareil avec Visual Studio Code

1. Dans l’explorateur Visual Studio Code, développez la section **Azure IoT Hub**.
1. Cliquez sur **...** dans l’en-tête de section **Azure IoT Hub**. Si vous ne voyez pas les points de suspension, cliquez ou pointez sur l’en-tête.
1. Sélectionnez **Créer un appareil IoT Edge**.
1. Dans la zone de texte qui s’ouvre, donnez un ID à votre appareil.

Dans l’écran de sortie, vous voyez le résultat de la commande. Les informations de l’appareil apparaissent, notamment l’ID d’appareil (**deviceId**) que vous avez fourni et la chaîne de connexion (**connectionString**) qui vous permet de connecter votre appareil physique à votre hub IoT.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) pour créer une identité d’appareil dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity create --device-id [device_id] --hub-name [hub_name] --edge-enabled
   ```

Cette commande inclut trois paramètres :

* `--device-id` ou `-d` : Fournissez un nom descriptif unique dans votre hub IoT.
* `--hub-name` ou `-n` : indiquez le nom de votre hub IoT.
* `--edge-enabled` ou `--ee` : déclarez que l’appareil est un appareil IoT Edge.

   ![az iot hub device-identity create output](./media/iot-edge-register-device-symmetric/create-edge-device-cli.png)

---

Maintenant que vous avez un appareil inscrit dans IoT Hub, récupérez les informations à utiliser pour terminer l’installation et le provisionnement du runtime IoT Edge.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Afficher les appareils inscrits et récupérer les informations de provisionnement

Les appareils qui utilisent l’authentification de clé symétrique ont besoin de leurs chaînes de connexion pour terminer l’installation et l’approvisionnement du runtime IoT Edge.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Tous les appareils compatibles avec Edge qui se connectent à votre hub IoT sont répertoriés dans la page **IoT Edge**.

![Utiliser le portail Azure pour voir tous les appareils IoT Edge dans votre hub IoT](./media/iot-edge-register-device-symmetric/portal-view-devices.png)

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

Les appareils qui s’authentifient avec des clés symétriques disposent des chaînes de connexion pouvant être copiées dans le portail.

1. Dans la page **IoT Edge** du portail, cliquez sur l’ID de l’appareil dans la liste des appareils IoT Edge.
2. Copiez la valeur de **Chaîne de connexion principale** ou **Chaîne de connexion secondaire**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Tous les appareils qui se connectent à votre hub IoT sont listés dans la section **Azure IoT Hub** de l’Explorateur Visual Studio Code. Les appareils IoT Edge se distinguent des appareils non Edge par leur icône et par le fait que les modules **$edgeAgent** et **$edgeHub** sont développés sur chacun d’eux.

![Utiliser VS Code pour voir tous les appareils IoT Edge dans votre hub IoT](./media/iot-edge-register-device-symmetric/view-devices.png)

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

1. Cliquez avec le bouton droit sur l’ID de votre appareil dans la section **Azure IoT Hub**.
1. Sélectionnez **Copier la chaîne de connexion de l’appareil**.

   La chaîne de connexion est copiée dans le Presse-papiers.

Vous pouvez également sélectionner **Obtenir les informations de l’appareil** dans le menu contextuel pour voir les informations de l’appareil, notamment la chaîne de connexion, dans la fenêtre de sortie.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) pour voir tous les appareils dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity list --hub-name [hub_name]
   ```

Tout appareil inscrit en tant qu’appareil IoT Edge a la propriété **capabilities.iotEdge** définie sur **true**.

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT. Utilisez la commande [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) pour retourner la chaîne de connexion d’un appareil unique :

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device_id] --hub-name [hub_name]
   ```

>[!TIP]
>La commande `connection-string show` a été introduite dans la version 0.9.8 de l’extension Azure IoT, remplaçant la commande `show-connection-string` déconseillée. Si une erreur survient lors de l’exécution de cette commande, assurez-vous que votre version de l’extension est mise à jour vers 0.9.8 ou une version ultérieure. Pour plus d’informations et pour connaître les dernières mises à jour, consultez [Extension Microsoft Azure IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

La valeur du paramètre `device-id` respecte la casse.

Quand vous copiez la chaîne de connexion à utiliser sur un appareil, n’incluez pas les guillemets autour de la chaîne de connexion.

---
