---
title: Étendre Azure IoT Central avec une analyse personnalisée | Microsoft Docs
description: En tant que développeur de solutions, configurez une application IoT Central pour réaliser des visualisations et des analyses personnalisées. Cette solution utilise Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 5f046920ce711ac6b1e6b9a461d71d9d49b2ddc7
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597662"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Étendre Azure IoT Central avec des analyses personnalisées à l’aide d’Azure Databricks

Ce guide pratique vous montre comment étendre votre application IoT Central avec des visualisations et des analyses personnalisées. L’exemple utilise un espace de travail [Azure Databricks](/azure/azure-databricks/) pour analyser le flux de données de télémétrie IoT Central et pour générer des visualisations telles que des [diagrammes à surfaces](https://wikipedia.org/wiki/Box_plot).  

Ce guide pratique vous montre comment étendre IoT Central au-delà de ce qu’il peut déjà faire avec les [outils d’analytique intégrés](./howto-create-custom-analytics.md).

Dans ce guide pratique, vous allez apprendre à effectuer les opérations suivantes :

* Transmettre des données de télémétrie en continu à partir d’une application IoT Central à l’aide de l’*exportation de données continue*.
* Créez un environnement Azure Databricks pour analyser et tracer la télémétrie d’appareil.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes décrites dans ce guide pratique, vous avez besoin d’un abonnement Azure actif.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

### <a name="iot-central-application"></a>Application IoT Central

Créez une application IoT Central sur le site web [Gestionnaire d’applications Azure IoT Central](https://aka.ms/iotcentral) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Plan tarifaire | standard |
| Modèle d’application | Analytique en magasin – Monitorage des conditions |
| Nom de l'application | Acceptez la valeur par défaut ou choisissez votre propre nom |
| URL | Acceptez la valeur par défaut ou choisissez votre propre préfixe d’URL unique |
| Répertoire | Votre locataire Azure Active Directory |
| Abonnement Azure | Votre abonnement Azure |
| Région | Région la plus proche |

Les exemples et captures d’écran de cet article concernent la région **États-Unis**. Choisissez un emplacement proche de vous et assurez-vous de créer toutes vos ressources dans la même région.

Ce modèle d’application comprend deux appareils à thermostat simulés qui envoient des données de télémétrie.

### <a name="resource-group"></a>Resource group

Utilisez le [Portail Azure pour créer un groupe de ressources](https://portal.azure.com/#create/Microsoft.ResourceGroup) appelé **IoTCentralAnalysis** afin de contenir les autres ressources que vous créez. Créez vos ressources Azure dans le même emplacement que votre application IoT Central.

### <a name="event-hubs-namespace"></a>Espace de noms Event Hubs

Utilisez le [portail Azure pour créer un espace de noms Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Name    | Choisissez le nom de votre espace de noms |
| Niveau tarifaire | De base |
| Subscription | Votre abonnement |
| Resource group | IoTCentralAnalysis |
| Emplacement | USA Est |
| Unités de débit | 1 |

### <a name="azure-databricks-workspace"></a>Espace de travail Azure Databricks

Utilisez le [Portail Azure pour créer un service Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Nom de l’espace de travail    | Choisir le nom de votre espace de travail |
| Abonnement | Votre abonnement |
| Resource group | IoTCentralAnalysis |
| Emplacement | USA Est |
| Niveau de tarification | standard |

Une fois que vous avez créé les ressources nécessaires, votre groupe de ressources **IoTCentralAnalysis** doit ressembler à la capture d’écran suivante :

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="Image du groupe de ressources d’analyse IoT Central.":::

## <a name="create-an-event-hub"></a>Créer un hub d’événements

Vous pouvez configurer une application IoT Central pour exporter en continu des données de télémétrie vers un Event Hub. Dans cette section, vous allez créer un Event Hub pour recevoir des données de télémétrie depuis votre application IoT Central. L’Event Hub fournit les données de télémétrie à votre travail Stream Analytics afin qu’elles soient traitées.

1. Dans le portail Azure, accédez à votre espace de noms Event Hubs et sélectionnez **+ Event Hub**.
1. Nommez votre Event Hub **centralexport**.
1. Dans la liste de concentrateurs d’événements dans votre espace de noms, sélectionnez **centralexport**. Puis sélectionnez **Stratégies d’accès partagé**.
1. Sélectionnez **Ajouter**. Créez une stratégie nommée **SendListen** avec les revendications **Send** et **Listen**.
1. Lorsque la stratégie est prête, sélectionnez-la dans la liste, puis copiez la valeur de **Clé primaire de la chaîne de connexion**.
1. Notez de cette chaîne de connexion ; vous l’utiliserez ultérieurement lors de la configuration de votre notebook Databricks pour lire à partir du concentrateur d’événements.

Votre espace de noms Event Hubs se présente comme la capture d’écran suivante :

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="Image de l’espace de noms Event Hubs.":::

## <a name="configure-export-in-iot-central"></a>Configurer l’exportation dans IoT Central

Dans cette section, vous allez configurer l’application pour diffuser en continu la télémétrie de ses appareils simulés vers votre Event Hub.

Sur le site web [Gestionnaire d’applications Azure IoT Central](https://aka.ms/iotcentral), accédez à l’application IoT Central que vous avez créée précédemment. Pour configurer l’exportation, commencez par créer une destination :

1. Accédez à la page **Exportation de données**, puis sélectionnez **Destinations**.
1. Sélectionnez **+ Nouvelle destination**.
1. Utilisez les valeurs du tableau suivant pour créer une destination :

    | Paramètre | Valeur |
    | ----- | ----- |
    | Nom de la destination | Event Hub de télémétrie |
    | Type de destination | Hubs d'événements Azure |
    | Chaîne de connexion | Chaîne de connexion d’Event Hub que vous avez notée précédemment |

    L’**Event Hub** apparaît sous la forme **centralexport**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Capture d’écran montrant la destination de l’exportation des données.":::

1. Sélectionnez **Enregistrer**.

Pour créer la définition d’exportation :

1. Accédez à la page **Exportation de données** et sélectionnez **+ Nouvelle exportation**.

1. Utilisez les valeurs du tableau suivant pour configurer l’exportation :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de l’exportation | Exportation de l’Event Hub |
    | activé | Il en va |
    | Type de données à exporter | Télémétrie |
    | Destinations | Sélectionnez **+ Destination**, puis sélectionnez **Event Hub de télémétrie**. |

1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Capture d’écran montrant la définition de l’exportation des données.":::

Avant de continuer, attendez que l’état de l’exportation soit **Sain** dans la page **Exportation de données**.

## <a name="configure-databricks-workspace"></a>Configurer l’espace de travail Databricks

Accédez à votre service Azure Databricks dans le Portail Azure et sélectionnez **Initialiser l’espace de travail**. Un nouvel onglet s’ouvre dans votre navigateur et vous connecte à votre espace de travail.

### <a name="create-a-cluster"></a>Créer un cluster

Sur la page **Azure Databricks**, sous la liste des tâches courantes, sélectionnez **Nouveau cluster**.

Utilisez les informations du tableau suivant pour créer votre cluster :

| Paramètre | Valeur |
| ------- | ----- |
| Nom du cluster | centralanalysis |
| Mode de cluster | standard |
| Version de Databricks Runtime | 5.5 LTS (Scala 2.11, Spark 2.4.5) |
| Version Python | 3 |
| Activer la mise à l’échelle automatique | Non |
| Arrêter après quelques minutes d’inactivité | 30 |
| Type de Worker | Standard_DS3_v2 |
| Workers | 1 |
| Type de pilote | Identique au Worker |

La création d’un cluster peut prendre plusieurs minutes. Attendez que la création du cluster soit terminée avant de continuer.

### <a name="install-libraries"></a>Installation des bibliothèques

Sur la page **Clusters**, attendez que l’état du cluster soit **En cours d’exécution**.

Les étapes suivantes expliquent comment importer la bibliothèque dont votre exemple a besoin dans le cluster :

1. Sur la page **Clusters**, attendez que l’état du cluster interactif **centralanalysis** soit **En cours d’exécution**.

1. Sélectionnez le cluster, puis sélectionnez l’onglet **Bibliothèques**.

1. Sur l’onglet **Bibliothèques**, sélectionnez **Installer une nouvelle bibliothèque**.

1. Sur la page **Installer une bibliothèque**, choisissez **Maven** en tant que source de la bibliothèque.

1. Dans la zone de texte **Coordinates**, entrez la valeur suivante : `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Sélectionnez **Installer** pour installer la bibliothèque sur le cluster.

1. L’état de la bibliothèque est désormais **Installée** :

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Capture d’écran de la bibliothèque installée.":::

### <a name="import-a-databricks-notebook"></a>Importer un notebook Databricks

Utilisez les étapes suivantes pour importer un notebook Databricks qui contient le code Python nécessaire pour analyser et visualiser vos données de télémétrie IoT Central :

1. Accédez à la page **Espace de travail** dans votre environnement Databricks. Sélectionnez la liste déroulante à côté du nom de votre compte, puis choisissez **Importer**.

1. Choisissez d’importer à partir d’une URL et entrez l’adresse suivante : [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Pour importer le notebook, sélectionnez **Importer**.

1. Sélectionnez l’**espace de travail** pour afficher le notebook importé :

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Capture d’écran du notebook importé.":::

5. Modifiez le code dans la première cellule Python pour ajouter la chaîne de connexion Event Hubs que vous avez enregistrée précédemment :

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Exécuter l’analyse

Pour exécuter l’analyse, vous devez attacher le notebook au cluster :

1. Sélectionnez **Détaché**, puis le cluster **centralanalysis**.
1. Si le cluster n’est pas en cours d’exécution, démarrez-le.
1. Pour démarrer le notebook, sélectionnez le bouton d’exécution.

Vous pouvez rencontrer une erreur dans la dernière cellule. Dans ce cas, vérifiez que les cellules précédentes sont en cours d’exécution, patientez une minute pour que les données soient écrites dans le stockage, puis exécutez à nouveau la dernière cellule.

### <a name="view-smoothed-data"></a>Afficher les données lissées

Dans le notebook, faites défiler jusqu’à la cellule 14 pour voir un tracé de l’humidité moyenne mobile par type de périphérique. Ce tracé se met à jour continuellement au fur et à mesure que les données de télémétrie diffusées arrivent :

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Capture d’écran du tracé de données de télémétrie lissées.":::

Vous pouvez redimensionner le graphique dans le notebook.

### <a name="view-box-plots"></a>Afficher les diagrammes à surfaces

Dans le notebook, faites défiler jusqu’à la cellule 20 pour voir les [diagrammes à surfaces](https://en.wikipedia.org/wiki/Box_plot). Les diagrammes à surfaces sont basés sur des données statiques. Ainsi, pour les mettre à jour, vous devez réexécuter la cellule :

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Capture d’écran des diagrammes à surfaces.":::

Vous pouvez redimensionner les diagrammes dans le notebook.

## <a name="tidy-up"></a>Nettoyer

Pour nettoyer à la suite de cette procédure et éviter les coûts inutiles, supprimez le groupe de ressources **IoTCentralAnalysis** dans le Portail Azure.

Vous pouvez supprimer l’application IoT Central depuis la page **Gestion** au sein de l’application.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide pratique, vous avez appris à effectuer les opérations suivantes :

* Transmettre des données de télémétrie en continu à partir d’une application IoT Central à l’aide de l’*exportation de données continue*.
* Créez un environnement Azure Databricks pour analyser et tracer les données de télémétrie.

Maintenant que vous savez comment créer des analyses personnalisées, l’étape suivante suggérée consiste à apprendre à [Utiliser le pont de périphérique IoT Central pour connecter d’autres clouds IoT à IoT Central](howto-build-iotc-device-bridge.md).
