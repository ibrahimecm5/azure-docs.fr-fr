---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3e07cbe5b8b90ff236b9cdc0d12f31af601a253a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842681"
---
## <a name="register-your-device"></a>Inscrire votre appareil

Vous avez le choix d’utiliser le **portail Azure**, **Visual Studio Code** ou **Azure CLI** pour inscrire votre appareil.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans votre hub IoT dans le portail Azure, les appareils IoT Edge sont créés et managés séparément des appareils IoT qui ne sont pas compatibles avec Edge.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.

1. Dans le volet de gauche, sélectionnez **IoT Edge** dans le menu, puis sélectionnez **Ajouter un appareil IoT Edge**.

   ![Ajouter un appareil IoT Edge à partir du portail Azure](./media/iot-edge-register-device-x509/portal-add-iot-edge-device.png)

1. Dans la page **Créer un appareil**, fournissez les informations suivantes :

   * Créez un ID d’appareil descriptif. Notez cet ID d’appareil, car vous en aurez besoin plus tard.
   * Sélectionnez **X.509 autosigné** comme type d’authentification.
   * Fournissez les empreintes numériques des certificats d’identité principal et secondaire. Les valeurs d’empreinte numérique sont constituées de 40 caractères hexadécimaux pour les hachages SHA-1 ou de 64 caractères hexadécimaux pour les hachages SHA-256.

1. Sélectionnez **Enregistrer**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Actuellement, l’extension Azure IoT pour Visual Studio Code ne prend pas en charge l’inscription des appareils avec des certificats X.509.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) pour créer une identité d’appareil dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity create --device-id device_id_here --hub-name hub_name_here --edge-enabled --auth-method x509_thumbprint --primary-thumbprint primary_SHA_thumbprint_here --secondary-thumbprint secdonary_SHA_thumbprint_here
   ```

Cette commande comprend plusieurs paramètres :

* `--device-id` ou `-d` : fournissez un nom descriptif unique à votre hub IoT. Notez cet ID d’appareil, car vous en aurez besoin dans la prochaine section.
* `hub-name` ou `-n` : indiquez le nom de votre hub IoT.
* `--edge-enabled` ou `--ee` : déclarez que l’appareil est un appareil IoT Edge.
* `--auth-method` ou `--am` : déclarez le type d’autorisation que l’appareil utilisera. Ici, nous utilisons des empreintes numériques de certificat X.509.
* `--primary-thumbprint` ou `--ptp` : fournissez une empreinte numérique de certificat X.509 à utiliser comme clé primaire.
* `--secondary-thumbprint` ou `--stp` : fournissez une empreinte numérique de certificat X.509 à utiliser comme clé secondaire.

---

Maintenant que vous avez un appareil inscrit dans IoT Hub, récupérez les informations à utiliser pour terminer l’installation et le provisionnement du runtime IoT Edge.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>Afficher les appareils inscrits et récupérer les informations de provisionnement

Pour les appareils qui utilisent l’authentification par certificat X.509, vous devez récupérer le nom de leur hub IoT, leur nom d’appareil et leurs fichiers de certificat pour terminer l’installation et le provisionnement du runtime IoT Edge.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Tous les appareils compatibles avec Edge qui se connectent à votre hub IoT sont répertoriés dans la page **IoT Edge**.

![Utiliser le portail Azure pour voir tous les appareils IoT Edge dans votre hub IoT](./media/iot-edge-register-device-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Bien qu’il n’y ait pas de prise en charge de l’inscription des appareils avec des certificats X.509 via Visual Studio Code, vous pouvez toujours afficher vos appareils IoT Edge si nécessaire.

Tous les appareils qui se connectent à votre hub IoT sont listés dans la section **Azure IoT Hub** de l’Explorateur Visual Studio Code. Les appareils IoT Edge se distinguent des appareils non Edge par leur icône et par le fait que les modules **$edgeAgent** et **$edgeHub** sont développés sur chacun d’eux.

![Utiliser VS Code pour voir tous les appareils IoT Edge dans votre hub IoT](./media/iot-edge-register-device-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az iot hub device-identity list](/cli/azure/iot/hub/device-identity) pour voir tous les appareils dans votre hub IoT. Par exemple :

   ```azurecli
   az iot hub device-identity list --hub-name hub_name_here
   ```

Tout appareil inscrit en tant qu’appareil IoT Edge a la propriété **capabilities.iotEdge** définie sur **true**.

---
