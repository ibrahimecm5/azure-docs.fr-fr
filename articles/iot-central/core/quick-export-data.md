---
title: Démarrage rapide - Exporter des données à partir d’Azure IoT Central
description: 'Démarrage rapide : Découvrez comment utiliser la fonctionnalité d’exportation de données dans IoT Central à des fins d’intégration avec d’autres services cloud.'
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 0728eed5a1deeee20554998512edd70f5e983491
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492305"
---
# <a name="quickstart-export-data-from-an-iot-central-application"></a>Démarrage rapide : Exporter des données à partir d’une application IoT Central

Ce démarrage rapide vous montre comment exporter en continu des données à partir de votre application Azure IoT Central vers un autre service cloud. Pour vous faciliter la tâche, ce guide de démarrage rapide utilise [Azure Data Explorer](/azure/data-explorer/data-explorer-overview), service d’analytique données complètement managé pour l’analyse en temps réel. Azure Data Explorer vous permet de stocker, d’interroger et de traiter les données de télémétrie à partir d’appareils tels que l’application de smartphone **IoT Plug-and-Play**.

Dans ce guide de démarrage rapide, vous :

- Utilisez la fonctionnalité d’exportation de données de IoT Central pour exporter les données de télémétrie envoyées par l’application de smartphone vers une base de données Azure Data Explorer.
- Utilisez Azure Data Explorer pour exécuter des requêtes sur les données de télémétrie.

## <a name="prerequisites"></a>Prérequis

- Avant de commencer, vous devez effectuer le premier démarrage rapide [Créer une application Azure IoT Central](./quick-deploy-iot-central.md). Le scond démarrage rapide, [Configurer des règles et des actions pour votre appareil](quick-configure-rules.md), est facultatif.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="install-azure-services"></a>Installer des services Azure

Avant de pouvoir exporter des données à partir de votre application IoT Central, vous devez disposer d’un cluster et d’une base de données Azure Data Explorer. Dans ce guide de démarrage rapide, vous utilisez l’environnement bash dans [Azure Cloud Shell](https://shell.azure.com) pour les créer et les configurer.

Exécutez le script suivant dans Azure Cloud Shell. Remplacez la valeur `clustername` par un nom unique pour votre cluster avant d’exécuter le script :

> [!IMPORTANT]
> L’exécution du script prend au moins 10 minutes.

```azurecli
clustername="<A unique name for your cluster>"
databasename="phonedata"
location="eastus"
resourcegroup="IoTCentralExportData"

az extension add -n kusto

# Create a resource group for the Azure Data Explorer cluster
az group create --location $location \
    --name $resourcegroup

# Create the Azure Data Explorer cluster
# This command takes at least 10 minutes to run
az kusto cluster create --cluster-name $clustername \
    --sku name="Standard_D11_v2"  tier="Standard" \
    --enable-streaming-ingest=true \
    --enable-auto-stop=true \
    --resource-group $resourcegroup --location $location

# Crete a database in the cluster
az kusto database create --cluster-name $clustername \
    --database-name $databasename \
    --read-write-database location=$location soft-delete-period=P365D hot-cache-period=P31D \
    --resource-group $resourcegroup

# Create a service principal to use when authenticating from IoT Central
SP_JSON=$(az ad sp create-for-rbac --skip-assignment --name spforiotcentral)

az kusto database-principal-assignment create --cluster-name $clustername \
                                              --database-name $databasename \
                                              --principal-id $(jq -r .appId <<< $SP_JSON) \
                                              --principal-assignment-name spforiotcentral \
                                              --resource-group $resourcegroup \
                                              --principal-type App \
                                              --role Admin

echo "Azure Data Explorer URL: $(az kusto cluster show --name $clustername --resource-group $resourcegroup --query uri -o tsv)"
echo "Client ID: $(jq -r .appId <<< $SP_JSON)"
echo "Tenant ID: $(jq -r .tenant <<< $SP_JSON)"
echo "Client secret: $(jq -r .password <<< $SP_JSON)" 
```

Notez l’**URL Azure Data Explorer**, l’**ID client**, l’**ID de locataire** et le **Secret client**. Vous utiliserez ces valeurs plus loin dans ce guide de démarrage rapide.

## <a name="configure-the-database"></a>Configurer la base de données

Pour ajouter une table dans la base de données afin de stocker les données de l’accéléromètre issues de l’application de smartphone **IoT Plug-and-Play** :

1. Utilisez l’**URL Azure Data Explorer** de la section précédente pour accéder à votre environnement Azure Data Explorer.

1. Développez le nœud de cluster et sélectionnez la base de données **phonedata**. La portée de la fenêtre de requête devient `Scope:yourclustername.eastus/phonedata`.

1. Collez le script Kusto suivant dans l’éditeur de requête, puis sélectionnez **Exécuter** :

    ```kusto
    .create table acceleration (
      EnqueuedTime:datetime,
      Device:string,
      X:real,
      Y:real,
      Z:real
    );
    ```

    Le résultat ressemble à la capture d’écran suivante :

    :::image type="content" source="media/quick-export-data/azure-data-explorer-create-table.png" alt-text="Capture d’écran qui montre les résultats de la création de la table dans Azure Data Explorer":::

1. Dans Azure Data Explorer, ouvrez un nouvel onglet et collez le script Kusto suivant. Le script active l’entrée en streaming pour la table **acceleration** :

    ```kusto
    .alter table acceleration policy streamingingestion enable;
    ```

Laissez la page Azure Data Explorer ouverte dans votre navigateur. Après avoir configuré l’exportation de données dans votre application IoT Central, vous allez exécuter une requête pour afficher les données de télémétrie de l’accéléromètre stockées dans la table **acceleration**.

## <a name="configure-data-export"></a>Configurer une exportation de données

Pour configurer la destination de l’exportation des données à partir d’IoT Central :

1. Accédez à la page **Exportation de données** de votre application IoT Central.
1. Sélectionnez l’onglet **Destinations**, puis **Ajouter une destination**.
1. Entrez *Azure Data Explorer* comme nom de destination. Sélectionnez **Azure Data Explorer** comme type de destination.
1. Dans **URL du cluster**, entrez l’**URL Azure Data Explorer** que vous avez notée.
1. Dans **Nom de la base de données**, entrez *phonedata*.
1. Dans **Nom de la table**, entrez *acceleration*.
1. Dans **ID client**, entrez l’**ID client** que vous avez noté.
1. Dans **ID de locataire**, entrez l’**ID de locataire** que vous avez noté.
1. Dans **Secret client**, entrez le **Secret client** que vous avez noté.
1. Sélectionnez **Enregistrer**.

Pour configurer l’exportation de données :

1. Dans la page **Exportation de données**, sélectionnez l’onglet **Exportations**, puis **Ajouter une exportation**.
1. Entrez *Accéléromètre du téléphone* comme nom d’exportation.
1. Sélectionnez **Données de télémétrie** en tant que type de données à exporter.
1. Utilisez les informations du tableau suivant pour ajouter deux filtres :

    | Name | Opérateur | Valeur |
    | ---- | -------- | ----- |
    | Modèle d’appareil | Égal à | IoT Plug and Play mobile |
    | Capteurs/Accélération/X | Exists | N/A |

    Assurez-vous que l’option permettant d’exporter les données si toutes les conditions sont vraies est définie.

1. Ajoutez **Azure Data Explorer** comme destination.
1. Ajoutez une transformation de données à la destination. Ajoutez la requête suivante dans le champ **2. Générer la requête de transformation** de la **page Transformation de données** :

    ```json
    import "iotc" as iotc;
    {
        Device: .device.id,
        EnqueuedTime: .enqueuedTime,
        X: .telemetry | iotc::find(.name == "accelerometer").value.x,
        Y: .telemetry | iotc::find(.name == "accelerometer").value.y,
        Z: .telemetry | iotc::find(.name == "accelerometer").value.z
    }
    ```

    :::image type="content" source="media/quick-export-data/data-transformation-query.png" alt-text="Capture d’écran montrant la requête de transformation de données pour l’exportation.":::

    Si vous souhaitez voir comment la transformation fonctionne et faire des essais avec la requête, collez l’exemple de message de télémétrie suivant dans **1. Ajouter votre message d’entrée** :

    ```json
    {
      "messageProperties": {},
      "device": {
        "id": "8hltz8xa7n",
        "properties": {
          "reported": []
        },
        "approved": true,
        "types": [],
        "name": "8hltz8xa7n",
        "simulated": false,
        "provisioned": true,
        "modules": [],
        "templateId": "urn:modelDefinition:vlcd3zvzdm:y425jkkpqzeu",
        "templateName": "IoT Plug and Play mobile",
        "organizations": [],
        "cloudProperties": [],
        "blocked": false
      },
      "component": "sensors",
      "applicationId": "40a97c91-50cc-44f0-9f63-71386613facc",
      "messageSource": "telemetry",
      "telemetry": [
        {
          "id": "dtmi:azureiot:PhoneSensors:__accelerometer;1",
          "name": "accelerometer",
          "value": {
            "x": 0.09960123896598816,
            "y": 0.09541380405426025,
            "z": 9.907781600952148
          }
        }
      ],
      "enqueuedTime": "2021-11-12T10:01:30.588Z",
      "enrichments": {}
    }
    ```

1. Enregistrez la transformation. Enregistrez ensuite la définition de l’exportation de données.

Attendez que l’état de l’exportation indique **Sain** :

:::image type="content" source="media/quick-export-data/healthy-export.png" alt-text="Capture d’écran montrant une exportation de données en cours d’exécution avec l’état sain.":::

## <a name="query-exported-data"></a>Interroger les données exportées

Dans Azure Data Explorer, ouvrez un nouvel onglet et collez la requête Kusto suivante, puis sélectionnez **Exécuter** pour tracer les données de télémétrie de l’accéléromètre :

```kusto
['acceleration'] 
    | project EnqueuedTime, Device, X, Y, Z
    | render timechart 
```

Vous devrez peut-être attendre quelques minutes pour collecter suffisamment de données. Essayez de maintenir votre téléphone dans différentes orientations pour voir les valeurs de télémétrie changer :

:::image type="content" source="media/quick-export-data/acceleration-plot.png" alt-text="Capture d’écran des résultats de la requête pour la télémétrie de l’accéléromètre.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

Pour supprimer l’instance Azure Data Explorer de votre abonnement et éviter des frais inutiles, supprimez le groupe de ressources **IoTCentralExportData** du [portail Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) ou exécutez la commande suivante dans Azure Cloud Shell :

```azurecli
az group delete --name IoTCentralExportData
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à exporter en continu des données entre IoT Central et un autre service Azure.

Vous savez désormais exporter vos données et voici l’étape suivane que nous vous suggérons :

> [!div class="nextstepaction"]
> [Créer et gérer un modèle d'appareil](howto-set-up-template.md).
