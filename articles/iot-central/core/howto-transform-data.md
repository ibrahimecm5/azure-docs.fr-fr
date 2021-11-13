---
title: Transformation de données pour Azure IoT Central | Microsoft Docs
description: Les appareils IoT envoient des données dans différents formats que vous devrez peut-être transformer. Cet article explique comment transformer des données entrant et sortant d’IoT Central. Les scénarios décrits utilisent IoT Edge et Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e05cbd17dbfe4f61562856f9244f5b4d0fa65dc9
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577381"
---
# <a name="transform-data-externally-for-iot-central"></a>Transformer des données en externe pour IoT Central

Les appareils IoT envoient des données dans différents formats. Pour utiliser les données d’appareil avec votre application IoT Central, vous devrez peut-être utiliser une transformation pour :

- Rendre le format des données compatible avec votre application IoT Central.
- Convertir des unités.
- Calculer de nouvelles métriques.
- Enrichir les données à partir d’autres sources.

Cet article explique comment transformer des données d’appareil en dehors d’IoT Central en entrée ou en sortie.

Le diagramme suivant illustre trois itinéraires pour les données qui incluent des transformations :

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="Résumé des itinéraires de transformation des données à la fois entrantes et sortantes" border="false":::

Le tableau suivant présente trois exemples de types de transformation :

| Transformation | Description | Exemple  | Notes |
|------------------------|-------------|----------|-------|
| Format de message         | Convertit ou manipule des messages JSON. | CSV vers JSON  | À l’entrée. IoT Central accepte uniquement les messages JSON avec valeur. Pour en savoir plus, consultez [Charges utiles de télémétrie, de propriétés et de commandes](concepts-telemetry-properties-commands.md). |
| Calculs           | Fonctions mathématiques qu’[Azure Functions](../../azure-functions/index.yml) peuvent exécuter. | Conversion d’unités de Fahrenheit en Celsius.  | Transformation à l’aide du modèle de sortie pour tirer parti de l’entrée de l’appareil scalable via une connexion directe à IoT Central. La transformation des données vous permet d’utiliser des fonctionnalités d’IoT Central telles que les visualisations et les travaux. |
| Enrichissement de message     | Enrichissements provenant de sources de données externes introuvables dans les propriétés ou la télémétrie de l’appareil. Pour en savoir plus sur les enrichissements internes, consultez [Exporter des données IoT vers des destinations cloud à l’aide des fonctionnalités d’exportation de données](howto-export-data.md) | Ajoutez des informations météorologiques aux messages à l’aide des [données d’emplacement](howto-use-location-data.md) des appareils. | Transformation à l’aide du modèle de sortie pour tirer parti de l’entrée de l’appareil scalable via une connexion directe à IoT Central. |

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans ce guide pratique, vous devez disposer des éléments suivants :

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="data-transformation-at-ingress"></a>Transformation des données à l’entrée

Pour transformer les données des appareils à l’entrée, deux options existent :

- **IoT Edge** : utilisez un module IoT Edge pour transformer des données à partir d’appareils en aval avant d’envoyer les données à votre application IoT Central.

- **Pont d’appareil IoT Central** : le [pont d’appareil IoT Central](howto-build-iotc-device-bridge.md) connecte d’autres clouds d’appareils IoT, tels que Sigfox, Particle et The Things Network à IoT Central. Le pont d’appareil utilise une fonction Azure pour transférer les données et vous pouvez personnaliser la fonction pour transformer les données de l’appareil.

### <a name="use-iot-edge-to-transform-device-data"></a>Utiliser IoT Edge pour transformer des données d’appareil

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="Transformation de données en entrée à l’aide d’IoT Edge" border="false":::

Dans ce scénario, un module IoT Edge transforme les données des appareils en aval avant de les transférer à votre application IoT Central. À un haut niveau, voici les étapes générales à suivre pour configurer ce scénario :

1. **Configurer un appareil IoT Edge** : Installez et configurez un appareil IoT Edge en tant que passerelle et connectez la passerelle à votre application IoT Central.

1. **Connectez l’appareil en aval à l’appareil IoT Edge :** Connectez les appareils en aval à l’appareil IoT Edge et configurez-les dans votre application IoT Central.

1. **Transformer les données de l’appareil dans IoT Edge :** Créez un module IoT Edge pour transformer les données. Déployez le module sur l’appareil de passerelle IoT Edge qui transmet les données de l’appareil transformées à votre application IoT Central.

1. **Vérifier** : Envoyez des données d’un appareil en aval à la passerelle et vérifiez que les données de l’appareil transformées atteignent votre application IoT Central.

Dans l’exemple décrit dans les sections suivantes, l’appareil en aval envoie des données CSV au format suivant à l’appareil de passerelle IoT Edge :

```csv
"<temperature >, <pressure>, <humidity>"
```

Vous souhaitez utiliser un module IoT Edge pour transformer les données au format JSON suivantes avant leur envoi à IoT Central :

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

[![Parcourir le code](media/common/browse-code.svg)](https://github.com/iot-for-all/iot-central-transform-with-iot-edge)

Les étapes suivantes vous montrent comment préparer et configurer ce scénario :

### <a name="build-the-custom-module"></a>Générer le module personnalisé

Dans ce scénario, l’appareil IoT Edge exécute un module personnalisé qui transforme les données de l’appareil en aval. Avant de déployer et de configurer l’appareil IoT Edge, vous devez :

- Générer le module personnalisé.
- Ajouter le module personnalisé à un registre de conteneurs.

Le runtime IoT Edge télécharge des modules personnalisés à partir d’un registre de conteneurs, comme un registre de conteneurs Azure ou Docker Hub. [Azure Cloud Shell](../../cloud-shell/overview.md) dispose de tous les outils dont vous avez besoin pour créer un registre de conteneurs, générer le module et charger le module dans le registre :

Pour créer un registre de conteneurs :

1. Ouvrez [Azure Cloud Shell](https://shell.azure.com/) et connectez-vous à votre abonnement Azure.

1. Exécutez les commandes suivantes pour créer un registre de conteneurs Azure :

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    Prenez note des valeurs `username` et `password`, vous les utiliserez plus tard.

Pour générer le module personnalisé dans [Azure Cloud Shell](https://shell.azure.com/) :

1. Dans [Azure Cloud Shell](https://shell.azure.com/), accédez à un dossier approprié.
1. Pour cloner le référentiel GitHub qui contient le code source du module, exécutez la commande suivante :

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Pour générer le module personnalisé, exécutez les commandes suivantes dans Azure Cloud Shell :

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    L’exécution des commandes précédentes peut prendre plusieurs minutes.

### <a name="set-up-an-iot-edge-device"></a>Configurer un appareil IoT Edge

Ce scénario utilise une passerelle IoT Edge pour transformer les données de n’importe quel appareil en aval. Cette section décrit comment créer des modèles d’appareil IoT Central pour la passerelle et les appareils en aval dans votre application IoT Central. Les appareils IoT Edge utilisent un manifeste de déploiement pour configurer leurs modules.

Pour créer un modèle d’appareil pour l’appareil en aval, ce scénario utilise un modèle d’appareil à thermostat simple :

1. Téléchargez le [modèle d’appareil de thermostat](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json) sur votre ordinateur local.

1. Connectez-vous à votre application IoT Central et accédez à la page **Modèles d’appareil**.

1. Sélectionnez **+ Nouveau**, puis **Appareil IoT** et **Suivant : Personnaliser**.

1. Entrez *Thermostat* comme nom de modèle, puis sélectionnez **Suivant : Vérifier**. Sélectionnez ensuite **Créer**.

1. Sélectionnez **Importer un modèle**, puis importez le fichier *thermostat-2.json* que vous avez téléchargé précédemment.

1. Sélectionnez **Publier** pour publier le nouveau modèle d’appareil.

Pour créer un modèle d’appareil pour l’appareil de passerelle IoT Edge :

1. Enregistrez une copie du manifeste de déploiement sur votre ordinateur de développement local : [moduledeployment.json](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json).

1. Ouvrez votre copie locale du fichier manifeste *moduledeployment.json* dans un éditeur de texte.

1. Recherchez la section `registryCredentials` et remplacez les espaces réservés par les valeurs que vous avez notées lorsque vous avez créé le registre de conteneurs Azure. La valeur `address` ressemble à `<username>.azurecr.io`.

1. Recherchez la section `settings` pour le `transformmodule`. Remplacez `<acr or docker repo>` par la valeur `address` que vous avez utilisée à l’étape précédente. Enregistrez les changements.

1. Accédez à la page **Modèles d’appareil** dans votre application IoT Central.

1. Sélectionnez **+ Nouveau**, **Azure IoT Edge**, puis sélectionnez **Suivant : Personnaliser**.

1. Entrez *Appareil de passerelle IoT Edge* comme nom de modèle d’appareil. Sélectionnez **Il s’agit d’un appareil de passerelle**. Sélectionnez **Parcourir** pour charger le fichier de manifeste de déploiement *moduledeployment.json* que vous avez modifié précédemment.

1. Une fois le manifeste de déploiement validé, sélectionnez **Suivant : Vérifier**, puis **Créer**.

1. Sous **Modèle**, sélectionnez **Relations**. Sélectionnez **+ Ajouter une relation**. Saisissez *Appareil en aval* comme nom d’affichage et sélectionnez  **Thermostat** comme cible. Sélectionnez **Enregistrer**.

1. Sélectionnez **Publier** pour publier le modèle d’appareil.

Vous avez maintenant deux modèles d’appareil dans votre application IoT Central. Le modèle **Appareil de passerelle IoT Edge** et le modèle **Thermostat** comme appareil en aval.

Pour inscrire un appareil de passerelle dans IoT Central :

1. Accédez à la page **Appareils** de votre application IoT Central.

1. Sélectionnez **Appareil de passerelle IoT Edge**, puis **Créer un appareil**. Entrez *Appareil de passerelle IoT Edge* comme nom d’appareil, *Gateway-01* comme ID d’appareil, et assurez-vous qu’**Appareil de passerelle IoT Edge** est sélectionné comme modèle d’appareil. Sélectionnez **Créer**.

1. Dans la liste des appareils, cliquez sur **Appareil de passerelle IoT Edge**, puis sélectionnez **Se connecter**.

1. Prenez note de l’**Étendue d’ID**, de l’**ID de l’appareil** et des valeurs de **Clé primaire** pour l’**appareil de passerelle IoT Edge**. Vous les utiliserez plus tard.

Pour inscrire un appareil en aval dans IoT Central :

1. Accédez à la page **Appareils** de votre application IoT Central.

1. Sélectionnez **Thermostat**, puis **Créer un appareil**. Entrez *Thermostat* comme nom d’appareil, *aval-01* comme ID d’appareil, et assurez-vous que **Thermostat** est sélectionné comme modèle d’appareil. Sélectionnez **Créer**.

1. Dans la liste des appareils, sélectionnez **Thermostat**, puis **Attacher à la passerelle**. Sélectionnez le modèle **Appareil de passerelle IoT Edge** et l’instance d’**appareil de passerelle IoT Edge**. Sélectionnez **Attacher**.

1. Dans la liste des appareils, cliquez sur **Thermostat**, puis sélectionnez **Se connecter**.

1. Prenez note de l’**Étendue d’ID**, de l’**ID de l’appareil** et des valeurs de **Clé primaire** pour **Thermostat**. Vous les utiliserez plus tard.

### <a name="deploy-the-gateway-and-downstream-devices"></a>Déployer la passerelle et les appareils en aval

Pour plus de commodité, cet article utilise des machines virtuelles Azure pour exécuter la passerelle et les appareils en aval. Pour créer les deux machines virtuelles Azure, sélectionnez le bouton **Déployer sur Azure** ci-dessous et utilisez les informations du tableau suivant pour terminer le formulaire **Déploiement personnalisé** :

| Champ | Valeur |
| ----- | ----- |
| Groupe de ressources | `ingress-scenario` |
| Passerelle de préfixe d’étiquette DNS | Un nom DNS unique pour cet ordinateur, tel que `<your name>edgegateway` |
| Préfixe d’étiquette DNS en aval | Un nom DNS unique pour cet ordinateur, tel que `<your name>downstream` |
| ID d’étendue | Étendue d’ID que vous avez notée précédemment |
| ID d’appareil de passerelle IoT Edge | `gateway-01` |
| Clé d’appareil de passerelle IoT Edge | La valeur de clé primaire que vous avez notée précédemment |
| Type d’authentification | Mot de passe |
| Mot de passe ou clé d’administrateur | Votre choix de mot de passe pour le compte **AzureUser** sur les deux machines virtuelles. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Sélectionnez **Vérifier + créer**, puis **Créer**. La création des machines virtuelles dans le groupe de ressources **ingress-scenario** prend quelques minutes.

Pour vérifier que l’appareil IoT Edge s’exécute correctement :

1. Ouvrez votre application IoT Central. Ensuite, accédez à **Appareil de passerelle IoT Edge** dans la liste des appareils sur la page **Appareils** .

1. Sélectionnez l’onglet **Modules** et vérifiez l’état des trois modules. Le démarrage de l’exécution d’IoT Edge sur la machine virtuelle prend quelques minutes. Lorsqu’il est démarré, l’état des trois modules est **En cours d’exécution**. Si me runtime IoT Edge ne démarre pas, consultez [Résoudre les problèmes liés à votre appareil IoT Edge](../../iot-edge/troubleshoot.md).

Pour que votre appareil IoT Edge fonctionne comme une passerelle, il a besoin de certificats pour prouver son identité à tous les appareils en aval. Cet article utilise des certificats de démonstration. Dans un environnement de production, utilisez des certificats fournis par votre autorité de certification.

Pour générer les certificats de démonstration et les installer sur votre appareil de passerelle :

1. Utilisez SSH pour vous connecter à la machine virtuelle de votre appareil de passerelle. Vous pouvez trouver le nom DNS de cette machine virtuelle dans le portail Azure. Accédez à la machine virtuelle **edgegateway** dans le groupe de ressources **ingress-scenario**.

    > [!TIP]
    > Vous devrez peut-être ouvrir le port 22 pour l’accès SSH sur vos machines virtuelles avant de pouvoir utiliser SSH pour vous connecter à partir de votre ordinateur local ou d’Azure Cloud Shell.

1. Exécutez les commandes suivantes pour cloner le dépôt IoT Edge et générer vos certificats de démonstration :

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Après avoir exécuté les commandes précédentes, les fichiers ci-dessous sont prêts à être utilisés dans les étapes suivantes :

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* - Le certificat d’autorité de certification racine utilisé pour créer tous les autres certificats de démonstration en vue de tester un scénario IoT Edge.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* - Un certificat d’autorité de certification d’appareil, référencé à l’aide du fichier *config.yaml*. Dans un scénario de passerelle, ce certificat de l'autorité de certification précise comment l'appareil IoT Edge vérifie son identité auprès des appareils en aval.
    - *~/certs/private/iot-edge-device-mycacert.key.pem* - La clé privée associée au certificat de l’autorité de certification de l’appareil.

    Pour en savoir plus sur ces certificats de démonstration, consultez [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](../../iot-edge/how-to-create-test-certificates.md).

1. Ouvrez le fichier *config.yaml* dans un éditeur de texte. Par exemple :

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Recherchez les paramètres `Certificate settings`. Supprimez les marques de commentaire et modifiez les paramètres du certificat comme suit :

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    L’exemple ci-dessus suppose que vous êtes connecté en tant que **AzureUser** et que vous avez créé un certificat d’autorité de certification d’appareil appelé « mycacert ».

1. Enregistrez les modifications, puis redémarrez le runtime IoT Edge :

    ```bash
    sudo systemctl restart iotedge
    ```

Si le runtime IoT Edge démarre correctement après vos modifications, l’état des modules **$edgeAgent** et **$EdgeHub** passe à **Running**. Vous pouvez voir ces valeurs d’état sur la page **Modules** de votre appareil de passerelle dans IoT Central.

Si le runtime ne démarre pas, vérifiez les modifications que vous avez apportées dans le fichier *config.yaml*, puis consultez [Résoudre les problèmes de votre appareil IoT Edge](../../iot-edge/troubleshoot.md).

### <a name="connect-downstream-device-to-iot-edge-device"></a>Connecter un appareil en aval à un appareil IoT Edge

Pour connecter un appareil en aval à la passerelle Azure IoT Edge :

1. Utilisez SSH pour vous connecter à la machine virtuelle de votre appareil en aval. Vous pouvez trouver le nom DNS de cette machine virtuelle dans le portail Azure. Accédez à la machine virtuelle **leafdevice** dans le groupe de ressources **ingress-scenario**.

    > [!TIP]
    > Vous devrez peut-être ouvrir le port 22 pour l’accès SSH sur vos machines virtuelles avant de pouvoir utiliser SSH pour vous connecter à partir de votre ordinateur local ou d’Azure Cloud Shell.

1. Pour cloner le référentiel GitHub avec le code source de l’exemple d’appareil en aval, exécutez la commande suivante :

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Pour copier le certificat requis à partir de l’appareil de passerelle, exécutez les commandes `scp` suivantes. Cette commande `scp` utilise le nom d’hôte `edgegateway` pour identifier la machine virtuelle de la passerelle. Vous serez invité à entrer votre mot de passe :

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. Accédez au dossier *leafdevice* et installez les packages requis. Exécutez ensuite les scripts `build` et `start` et pour approvisionner et connecter l’appareil à la passerelle :

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. Entrez l’ID de l’appareil, l’ID de l’étendue et la clé SAS de l’appareil en aval que vous avez créé précédemment. Pour le nom d’hôte, entrez `edgegateway`. La sortie de la commande ressemble à ceci :

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>Vérifier

Pour vérifier que le scénario est en cours d’exécution, accédez à votre **appareil de passerelle IoT Edge** dans IoT Central :

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="Capture d’écran montrant les données transformées sur la page Appareils.":::

- Sélectionnez **Modules**. Vérifiez que les trois modules IoT Edge **$edgeAgent**, **$edgeHub** et **transformmodule** sont en cours d’exécution.
- Sélectionnez les **appareils en aval** et vérifiez que l’appareil en aval est configuré.
- Sélectionnez **Données brutes**. Les données de télémétrie de la colonne **Données non modélisées** ont l’aspect suivant :

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

Étant donné que l’appareil IoT Edge transforme les données de l’appareil en aval, les données de télémétrie sont associées à l’appareil de passerelle dans IoT Central. Pour visualiser les données de télémétrie, créez une nouvelle version du modèle **Appareil de passerelle IoT Edge** avec les définitions des types de télémétrie.

## <a name="data-transformation-at-egress"></a>Transformation des données en sortie

Vous pouvez connecter vos appareils à IoT Central, exporter les données de l’appareil vers un moteur de calcul pour les transformer, puis renvoyer les données transformées à IoT Central pour la gestion et l’analyse des appareils. Par exemple :

- Vos appareils envoient des données d’emplacement à IoT Central.
- IoT Central exporte les données vers un moteur de calcul qui améliore les données d’emplacement avec des informations météorologiques.
- Le moteur de calcul renvoie les données améliorées à IoT Central.

Vous pouvez utiliser le [pont d’appareil IoT Central](https://github.com/Azure/iotc-device-bridge) en tant que moteur de calcul pour transformer les données exportées à partir d’IoT Central.

L’un des avantages de la transformation des données en sortie est que vos appareils se connectent directement à IoT Central, ce qui facilite l’envoi des commandes aux appareils ou la mise à jour des propriétés de l’appareil. Toutefois, avec cette méthode, vous pourriez utiliser plus de messages que votre allocation mensuelle et augmenter le coût d’utilisation d’Azure IoT Central.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>Utiliser le pont d’appareil IoT Central pour transformer les données d’appareil

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="Transformation de données en sortie à l’aide d’IoT Edge" border="false":::

Dans ce scénario, un moteur de calcul transforme les données des appareils exportés à partir d’IoT Central avant de les renvoyer à votre application IoT Central. À un haut niveau, voici les étapes générales à suivre pour configurer ce scénario :

1. **Configurer le moteur de calcul :** Créez un pont d’appareil IoT Central pour agir en tant que moteur de calcul pour la transformation des données.

1. **Transformer les données d’appareil dans le pont d’appareil :** Transformez les données dans le pont d’appareil en modifiant le code de la fonction de pont d’appareil pour votre cas d’utilisation de transformation de données.

1. **Activer le flux de données d’IoT Central vers le pont d’appareil :** Exportez les données d’IoT Central vers le pont d’appareil pour transformation. Ensuite, transférez les données transformées vers IoT Central. Lorsque vous créez l’exportation de données, utilisez des filtres de propriétés de message pour exporter uniquement des données transformées.

1. **Vérifier :** Connectez votre appareil à l’application IoT Central et vérifiez les données brutes de l’appareil et les données transformées dans IoT Central.

<!-- To Do - doesn't the device send JSON data? -->
Dans l’exemple décrit dans les sections suivantes, l’appareil envoie des données CSV au format suivant à l’appareil de passerelle IoT Edge :

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

Vous utilisez le pont d’appareil pour transformer les données de l’appareil en :

- Modifiant l’unité de température de centigrade en Fahrenheit.
- Enrichissant des données d’appareil avec les données météorologiques tirées du service [Open Weather](https://openweathermap.org/) pour les valeurs de latitude et de longitude.

Le pont d’appareil envoie ensuite les données transformées à IoT Central au format suivant :

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

Les étapes suivantes vous montrent comment préparer et configurer ce scénario :

### <a name="retrieve-your-iot-central-connection-settings"></a>Récupérer vos paramètres de connexion IoT Central

Avant de configurer ce scénario, vous devez vous procurer des paramètres de connexion à partir de votre application IoT Central :

1. Connectez-vous à votre application IoT Central.

1. Accédez à **Administration > Connexion de l’appareil**.

1. Notez l’**étendue de l’ID**. Vous utiliserez cette valeur plus tard.

1. Sélectionnez le groupe d’inscriptions **SaS-IoT-Devices**. Notez la valeur clé primaire de la signature d’accès partagé. Vous utiliserez cette valeur plus tard.

### <a name="set-up-a-compute-engine"></a>Configurer un moteur de calcul

Ce scénario utilise le même déploiement Azure Functions que le pont d’appareil IoT Central. Pour déployer le pont d’appareil, sélectionnez le bouton **Déployer sur Azure** ci-dessous et utilisez les informations du tableau suivant pour terminer le formulaire **Déploiement personnalisé** :

| Champ | Valeur |
| ----- | ----- |
| Groupe de ressources | Créez un groupe de ressources nommé `egress-scenario` |
| Région | Sélectionnez la région la plus proche de vous. |
| ID d’étendue | Utilisez l’**étendue d’ID** que vous avez notée précédemment. |
| Clé SAS IoT Central | Utilisez la clé primaire de signature d’accès partagé pour le groupe d’inscription **SaS-IoT-Devices**. Vous avez noté cette valeur précédemment. |

[![Effectuez le déploiement dans Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json).

Sélectionnez **Vérifier + créer**, puis **Créer**. Il faut quelques minutes pour créer la fonction Azure et les ressources associées dans le groupe de ressources de **egress-scenario**.

### <a name="transform-device-data-in-the-device-bridge"></a>Transformer les données d’appareil dans le pont d’appareil

Pour configurer le pont d’appareil afin de transformer les données d’appareil exportées :

1. Obtenez une clé d’API d’application à partir du service Open Weather. Un compte est gratuit avec une utilisation limitée du service. Pour créer une clé d’API d’application, créez un compte dans le [portail de service Open Weather](https://openweathermap.org/) et suivez les instructions. Vous utilisez votre clé d’API Open Weather ultérieurement.

1. Dans le portail Azure, accédez à l’application de fonction dans le groupe de ressources **egress-scenario**.

1. Dans le volet de navigation de gauche, dans **Outils de développement**, sélectionnez **Éditeur App Service (préversion)** .

1. Sélectionnez **Aller&rarr;** pour ouvrir la page **Éditeur App Service**. Effectuez les modifications suivantes :

    1. Ouvrez le fichier *wwwroot/IoTCIntegration/index.js*. Remplacez tout le code de ce fichier par celui d’[index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js).

    1. Dans le nouveau fichier *index.js*, mettez à jour le fichier de variable `openWeatherAppId` avec la clé d’API Open Weather que vous avez obtenue précédemment.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. Ajoutez une propriété de message aux données envoyées par la fonction à IoT Central. IoT Central utilise cette propriété pour empêcher l’exportation des données transformées. Pour effectuer cette modification, ouvrez le fichier *wwwroot/IoTCIntegration/lib/engine.js*. Recherchez le code suivant :

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        Ajoutez le code suivant juste après le code de l’extrait de code précédent :

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        Pour référence, vous pouvez consulter un exemple complet du fichier [engine.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js).

1. Dans l’**Éditeur App Service**, sélectionnez **Console** dans le volet de navigation de gauche. Exécutez les commandes suivantes pour installer les packages nécessaires :

    ```bash
    cd IoTCIntegration
    npm install
    ```

    L’exécution de cette commande peut prendre quelques minutes.

1. Revenez à la page **Vue d’ensemble de la fonction Azure** et redémarrez la fonction :

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="Redémarrer la fonction":::

1. Sélectionnez **Fonctions** dans le volet de navigation gauche. Sélectionnez ensuite **IoTCIntegration**. Sélectionnez **Code + test**.

1. Prenez note de l’URL de la fonction, vous aurez besoin de cette valeur plus tard :

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="Obtenir l’URL de la fonction":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>Activer le flux de données d’IoT Central vers le pont d’appareil

Cette section décrit comment configurer l’application Azure IoT Central.

Tout d’abord, enregistrez le fichier de [modèle d’appareil](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) sur votre ordinateur local.

Pour ajouter un modèle d’appareil à votre application IoT Central, accédez à votre application IoT Central, puis :

1. Connectez-vous à votre application IoT Central et accédez à la page **Modèles d’appareil**.

1. Sélectionnez **+ Nouveau**, **Appareil IoT**, puis **Suivant : Personnaliser**, et entrez *Modèle de calcul* comme nom de modèle. Sélectionnez **Suivant : Vérification**). Sélectionnez ensuite **Créer**.

1. Sélectionnez **Importer un modèle** et accédez au fichier *model.json* que vous avez téléchargé précédemment.

1. Une fois le modèle importé, sélectionnez **Publier** pour publier le modèle d’appareil **Modèle de calcul**.

Pour configurer l’exportation de données afin d’envoyer des données à votre pont d’appareil :

1. Dans votre application IoT Central, sélectionnez **Exportation de données**.

1. Sélectionnez **+ Nouvelle destination** pour créer une destination à utiliser avec le pont d’appareil. Appelez la *Fonction de calcul* de destination. Pour **Type de destination**, sélectionnez **Webhook**. Pour l’URL de rappel, collez l’URL de fonction que vous avez notée précédemment. Laissez **Autorisation** sur **Pas d’authentification**.

1. Enregistrez les changements.

1. Sélectionnez **+ Nouvelle exportation**, puis créez une exportation de données appelée *Exportation de calcul*.

1. Ajoutez un filtre pour exporter uniquement les données d’appareil pour le modèle d’appareil que vous utilisez. Sélectionnez **+ Filtre**, l’élément **Modèle d’appareil**, l’opérateur **Est égal à**, puis choisissez le modèle d’appareil **Modèle de calcul** que vous venez de créer.

1. Ajoutez un filtre de messages pour faire la différence entre les données transformées et non transformées. Ce filtre empêche l’envoi de valeurs transformées au pont d’appareil. Sélectionnez **+ Filtre de propriétés de message**, entrez la valeur de nom *calculé*, puis choisissez l’opérateur **N’existe pas**. La chaîne `computed` est utilisée en tant que mot clé dans le code d’exemple de pont d’appareil.

1. Pour la destination, sélectionnez la destination de la **Fonction de calcul** que vous avez créée précédemment.

1. Enregistrez les changements. Après une minute, l’**État de l’exportation** est alors **Sain**.

### <a name="verify"></a>Vérifier

L’exemple d’appareil que vous utilisez pour tester le scénario est écrit en Node.js. Vérifiez que Node.js et NPM sont installés sur votre ordinateur local. Si vous ne souhaitez pas installer ces composants requis, utilisez [Azure Cloud Shell](https://shell.azure.com/), qui les a préinstallés.

Pour exécuter un exemple d’appareil qui teste le scénario :

1. Clonez le référentiel GitHub qui contient le code source du module avec la commande suivante :

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. Pour connecter l’exemple d’appareil à votre application IoT Central, modifiez les paramètres de connexion dans le fichier *iot-central-compute/device/device.js*. Remplacez l’ID d’étendue et la clé SAS du groupe par les valeurs que vous avez notées précédemment :

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    Enregistrez les changements.

1. Utilisez les commandes suivantes pour installer les packages requis et exécutez l’appareil :

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. Le résultat de cette commande ressemble à la sortie suivante :

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. Dans votre application IoT Central, accédez à l’appareil **computeDevice**. Sur la vue **Données brutes**, il existe deux flux de télémétrie différents qui s’affichent toutes les cinq secondes. Le flux avec des données non modélisées est la télémétrie d’origine, et le flux avec des données modélisées est celui des données transformées par la fonction :

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="Capture d’écran montrant les données brutes d’origine et transformées.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources Azure que vous avez créées en suivant les étapes décrites dans ce guide, supprimez les [groupes de ressources dans le portail Azure](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups).

Les deux groupes de ressources que vous avez utilisés dans ce guide sont **ingress-scenario** et **egress-scenario**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris les différentes options de transformation des données d’appareil pour IoT Central, à la fois en entrée et en sortie. L’article inclut des procédures pas à pas pour deux scénarios spécifiques :

- Utiliser un module IoT Edge pour transformer des données à partir d’appareils en aval avant d’envoyer les données à votre application IoT Central.
- Utiliser Azure Functions pour transformer des données en dehors d’IoT Central. Dans ce scénario, IoT Central utilise une exportation de données pour envoyer des données entrantes à une fonction Azure pour transformation. La fonction renvoie les données transformées à votre application IoT Central.

Maintenant que vous avez appris à transformer les données d’appareil en dehors de votre application Azure IoT Central, vous pouvez consulter le [Guide pratique pour utiliser une analytique permettant d’analyser les données des appareils dans IoT Central](howto-create-analytics.md).
