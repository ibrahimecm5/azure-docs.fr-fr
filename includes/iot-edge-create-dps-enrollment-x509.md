---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16338971721edfb68c93821ddf08c5cc36caf0b3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845991"
---
## <a name="create-a-dps-enrollment"></a>Créer une inscription au service Device Provisioning

Utilisez vos certificats et clés générés afin de créer une inscription dans le service Device Provisioning pour un ou plusieurs appareils IoT Edge.

Si vous envisagez de provisionner un seul appareil IoT Edge, créez une **inscription individuelle**. Si vous avez besoin de provisionner plusieurs appareils, suivez les étapes de création d’une **inscription de groupe** DPS.

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](../articles/iot-edge/how-to-deploy-at-scale.md).

Pour plus d’informations sur les inscriptions dans le service Device Provisioning, consultez [Guide pratique pour gérer les inscriptions d’appareils](../articles/iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Créer une inscription individuelle dans le service Device Provisioning

Les inscriptions individuelles prennent la partie publique du certificat d’identité d’un appareil et la font correspondre avec le certificat sur l’appareil.

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions individuelles à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne un appareil IoT Edge.

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle** et suivez ces étapes pour configurer l’inscription :  

   * **Mécanisme** : sélectionnez **X.509**.

   * **Fichier .pem ou .cer du certificat principal** : chargez le fichier public à partir du certificat d’identité d’appareil. Si vous avez utilisé les scripts pour générer un certificat de test, choisissez le fichier suivant :

      `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`

   * **ID de l’appareil IoT Hub** : Fournissez un ID pour votre appareil si vous le souhaitez. Vous pouvez utiliser l’ID d’appareil pour cibler un appareil individuel lors du déploiement de module. Si vous ne fournissez pas d’ID d’appareil, le nom commun (CN) dans le certificat X.509 est utilisé.

   * **Appareil IoT Edge** : Sélectionnez **Vrai** pour déclarer que cette inscription est un appareil IoT Edge.

   * **Sélectionnez les hubs IoT auxquels cet appareil peut être attribué** : choisissez le hub IoT lié auquel vous voulez connecter votre appareil. Vous pouvez choisir plusieurs hubs : l’appareil sera affecté à l’un d’entre eux en fonction de la stratégie d’allocation sélectionnée.

   * **État initial du jumeau d’appareil** : ajoutez une valeur d’étiquette destinée au jumeau d’appareil si vous le souhaitez. Vous pouvez utiliser des étiquettes pour cibler des groupes d’appareils lors du déploiement automatique. Par exemple :

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Sélectionnez **Enregistrer**.

Sous **Gérer les inscriptions**, vous pouvez voir **l’ID d’inscription** pour l’inscription que vous venez de créer. Notez-le, car il peut être utilisé quand vous provisionnez votre appareil.

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut provisionner automatiquement l’appareil lors de l’installation.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Créer une inscription de groupe dans le service Device Provisioning

Les inscriptions de groupe utilisent un certificat d’autorité de certification intermédiaire ou racine à partir de la chaîne de certificats de confiance utilisée pour générer les certificats d’identité d’appareil individuels.

#### <a name="verify-your-root-certificate"></a>Vérifier votre certificat racine

Quand vous créez un groupe d’inscriptions, vous avez la possibilité d’utiliser un certificat vérifié. Vous pouvez vérifier un certificat avec le service Device Provisioning en prouvant que vous êtes propriétaire du certificat racine. Pour plus d’informations, consultez [Guide pratique pour effectuer la preuve de possession de certificats d’autorité de certification X.509](../articles/iot-dps/how-to-verify-certificates.md).

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sélectionnez **Certificats** dans le menu de gauche.

1. Sélectionnez **Ajouter** pour ajouter un nouveau certificat.

1. Entrez un nom convivial pour votre certificat, puis accédez au fichier .cer ou .pem représentant la partie publique de votre certificat X.509.

   Si vous utilisez les certificats de démonstration, chargez le certificat `<wrkdir>\certs\azure-iot-test-only.root.ca.cert.pem`.

1. Sélectionnez **Enregistrer**.

1. Votre certificat doit maintenant être listé dans la page **Certificats**. Sélectionnez-le pour ouvrir les détails du certificat.

1. Sélectionnez **Générer le code de vérification**, puis copiez le code généré.

1. Que vous ayez fourni votre propre certificat d’autorité de certification ou utilisiez les certificats de démonstration, vous pouvez utiliser l’outil de vérification disponible dans le dépôt IoT Edge pour vérifier la preuve de possession. L’outil de vérification utilise votre certificat d’autorité de certification pour signer un nouveau certificat ayant le code de vérification fourni comme nom d’objet.

   ```powershell
   New-CACertsVerificationCert "<verification code>"
   ```

1. Dans le portail Azure, dans la même page des détails du certificat, chargez le certificat de vérification qui vient d’être généré.

1. Sélectionnez **Vérifier**.

#### <a name="create-enrollment-group"></a>Créer un groupe d’inscriptions

Pour plus d’informations sur les inscriptions dans le service Device Provisioning, consultez [Guide pratique pour gérer les inscriptions d’appareils](../articles/iot-dps/how-to-manage-enrollments.md).

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions de groupe à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne des appareils IoT Edge. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter un groupe d’inscriptions** et suivez les étapes ci-après pour configurer l’inscription :

   * **Nom du groupe** : fournissez un nom facile à retenir pour cette inscription de groupe.

   * **Type d’attestation** : Sélectionnez **Certificate**.

   * **Appareil IoT Edge** : sélectionnez **VRAI**. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

   * **Type de certificat** : sélectionnez **Certificat d’autorité de certification**.

   * **Certificat principal** : choisissez votre certificat dans la liste déroulante.

   * **Sélectionnez les hubs IoT auxquels cet appareil peut être attribué** : choisissez le hub IoT lié auquel vous voulez connecter votre appareil. Vous pouvez choisir plusieurs hubs : l’appareil sera affecté à l’un d’entre eux en fonction de la stratégie d’allocation sélectionnée.

   * **État initial du jumeau d’appareil** : ajoutez une valeur d’étiquette destinée au jumeau d’appareil si vous le souhaitez. Vous pouvez utiliser des étiquettes pour cibler des groupes d’appareils lors du déploiement automatique. Par exemple :

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Sélectionnez **Enregistrer**.

Sous **Gérer les inscriptions**, vous pouvez voir **l’ID d’inscription** pour l’inscription que vous venez de créer. Notez-le, car il peut être utilisé quand vous provisionnez vos appareils.

Maintenant qu’il existe une inscription pour ces appareils, le runtime IoT Edge peut les provisionner automatiquement lors de l’installation.

---
