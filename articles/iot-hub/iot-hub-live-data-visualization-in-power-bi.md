---
title: Visualisation des données en temps réel depuis Azure IoT Hub – Power BI
description: Power BI permet d’afficher des données sur les températures et l’humidité collectées par le capteur et envoyées à votre instance Azure IoT Hub.
author: eross-msft
keywords: visualisation de données en temps réel, visualisation de données en direct, visualisation de données de capteurs
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 7/23/2021
ms.author: lizross
ms.openlocfilehash: 15e297f65aad93cd3999ae44953a3410c7281788
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555047"
---
# <a name="tutorial-visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Tutoriel : Visualiser des données de capteur en temps réel depuis Azure IoT Hub, à l’aide de Power BI

Vous pouvez utiliser Microsoft Power BI pour visualiser les données de capteur en temps réel que votre instance Azure IoT Hub reçoit. Pour ce faire, vous configurez un travail d’Azure Stream Analytics pour consommer les données d’IoT Hub et les acheminer vers un jeu de données dans Power BI. 

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png" alt-text="Diagramme de bout en bout" border="false":::

 [Microsoft Power BI](https://powerbi.microsoft.com/) est un outil de visualisation des données que vous pouvez utiliser pour effectuer des opérations décisionnelles en libre-service et en entreprise sur des jeux de données volumineux. [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) est un service d’analytique en temps réel entièrement managé, qui est conçu pour vous aider à analyser et à traiter des flux de données rapides pouvant servir à obtenir des insights, créer des rapports ou déclencher des alertes et des actions. 

Dans ce didacticiel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer un groupe de consommateurs dans votre hub IoT.
> * Créer et configurer un travail d’Azure Stream Analytics pour lire la télémétrie de température de votre groupe de consommateurs et l’envoyer à Power BI.
> * Créer un rapport des données de température dans Power BI et le partager sur le web.

## <a name="prerequisites"></a>Configuration requise

* Suivez un des démarrages rapides sur l’[envoi de données de télémétrie](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) dans le langage de développement de votre choix. Vous pouvez aussi utiliser n’importe quel appareil qui envoie des données de télémétrie de température, par exemple le [simulateur Raspberry Pi en ligne](iot-hub-raspberry-pi-web-simulator-get-started.md) ou un des [appareils intégrés](../iot-develop/quickstart-devkit-mxchip-az3166.md) des démarrages rapides. Ces articles demandent les éléments suivants :
  
  * Un abonnement Azure actif.
  * Un hub Azure IoT dans votre abonnement.
  * Une application cliente qui envoie des messages à votre instance Azure IoT Hub.

* Un compte Microsoft Power BI. ([Essayez Power BI gratuitement](https://powerbi.microsoft.com/)).

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Créer, configurer et exécuter un travail Stream Analytics

Commençons par la création d’une tâche Stream Analytics. Une fois la tâche créée, vous définissez les entrées, les sorties et la requête utilisée pour récupérer les données.

### <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**. Tapez *Tâche Stream Analytics* dans la zone de recherche et sélectionnez-la dans la liste déroulante. Dans le volet de vue d’ensemble de la **tâche Stream Analytics**, sélectionnez **Créer**.

2. Saisissez les informations ci-après concernant le travail.

   **Nom du travail** : Nom du travail. Le nom doit être globalement unique.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre hub IoT.

   **Emplacement** : utilisez le même emplacement que votre groupe de ressources.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png" alt-text="Créer un travail Stream Analytics dans Azure":::

3. Sélectionnez **Create** (Créer).

### <a name="add-an-input-to-the-stream-analytics-job"></a>Ajouter une entrée à la tâche Stream Analytics

1. Ouvrez le travail Stream Analytics.

2. Sous **Topologie de la tâche**, sélectionnez **Entrées**.

3. Dans le volet **Entrées**, sélectionnez **Ajouter une entrée de flux**, puis sélectionnez **IoT Hub** dans la liste déroulante. Dans le volet de la nouvelle entrée, indiquez les informations suivantes :

   **Alias d’entrée** : indiquez un alias unique pour l’entrée.

   **Sélectionner un hub IoT dans vos abonnements** : sélectionnez cette case d’option.

   **Abonnement**: Sélectionnez l’abonnement Azure que vous utilisez pour ce tutoriel.

   **Hub IoT** : sélectionnez le hub IoT que vous utilisez pour ce tutoriel.

   **Point de terminaison** : sélectionnez **Messagerie**.

   **Nom de la stratégie d'accès partagé** : sélectionnez le nom de la stratégie d’accès partagé que vous souhaitez voir utiliser par le travail Stream Analytics. Pour ce tutoriel, vous pouvez sélectionner *service*. La stratégie *service* est créée par défaut sur les nouveaux hubs IoT ; elle accorde l’autorisation d’envoyer et de recevoir sur des points de terminaison côté cloud qui sont exposés par le hub IoT. Pour plus d’informations, consultez [Contrôle d’accès et autorisations](iot-hub-dev-guide-sas.md#access-control-and-permissions).

   **Clé de la stratégie d’accès partagé** : ce champ est automatiquement renseigné par rapport à votre sélection pour le nom de stratégie d’accès partagé.

   **Groupe de consommateurs** : sélectionnez le groupe de consommateurs que vous avez créé précédemment.

   Laissez tous les autres champs sur leurs valeurs par défaut.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png" alt-text="Ajouter une entrée à un travail Stream Analytics dans Azure":::

4. Sélectionnez **Enregistrer**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Ajouter une sortie à la tâche Stream Analytics

1. Sous **Topologie de la tâche**, sélectionnez **Sorties**.

2. Dans le volet **Sorties**, sélectionnez **Ajouter**, puis sélectionnez **Power BI** dans la liste déroulante.

3. Dans le volet **Power BI - Nouvelle sortie**, sélectionnez **Autoriser** et suivez les invites pour vous connecter à votre compte Power BI.

4. Une fois connecté à Power BI, entrez les informations suivantes :

   **Alias de sortie** : alias unique de la sortie.

   **Espace de travail de groupe** : sélectionnez l’espace de travail de votre groupe cible.

   **Nom du jeu de données** : entrez un nom de jeu de données.

   **Nom de la table** : Saisissez le nom de la table.

   **Mode d'authentification** : Conservez la valeur par défaut.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png" alt-text="Ajouter une sortie à un travail Stream Analytics dans Azure":::

5. Sélectionnez **Enregistrer**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurer la requête de la tâche Stream Analytics

1. Sous **Topologie de la tâche**, sélectionnez **Requête**.

2. Remplacez `[YourInputAlias]` par l’alias d’entrée du travail.

3. Remplacez `[YourOutputAlias]` par l’alias de sortie du travail.

1. Ajoutez la clause `WHERE` suivante comme dernière ligne de la requête. Cette ligne s’assure que seuls les messages avec une propriété **température** seront transférés à Power BI.

    ```sql
    WHERE temperature IS NOT NULL
    ```
1. Votre requête doit ressembler à la capture d’écran suivante. Sélectionnez **Enregistrer la requête**.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png" alt-text="Ajouter une requête à un travail Stream Analytics":::

### <a name="run-the-stream-analytics-job"></a>Exécuter la tâche Stream Analytics

Dans la tâche Stream Analytics, sélectionnez **Vue d’ensemble**, puis **Démarrer** > **Maintenant** > **Démarrer**. Une fois la tâche lancée, l’état correspondant passe de **Arrêté** à **Exécution**.

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png" alt-text="Exécuter un travail Stream Analytics dans Azure":::

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Créer et publier un rapport Power BI pour visualiser les données

Les étapes suivantes vous montrent comment créer et publier un rapport à l’aide du service Power BI. Vous pouvez suivre ces étapes, avec quelques modifications, si vous souhaitez utiliser la « nouvelle apparence » dans Power BI. Pour comprendre les différences et savoir comment naviguer dans la « nouvelle apparence », consultez [La « nouvelle apparence » du service Power BI](/power-bi/fundamentals/desktop-latest-update).

1. Assurez-vous que l’application cliente s’exécute sur votre appareil.

2. Connectez-vous à votre compte [Power BI](https://powerbi.microsoft.com/) et sélectionnez **Service Power BI** dans le menu du haut.

3. Sélectionnez l’espace de travail que vous avez utilisé dans le menu latéral, **Mon espace de travail**.

4. Dans l’onglet **Tout** ou l’onglet **Jeux de données + flux de données**, vous devez voir le jeu de données que vous avez spécifié lors de la création de la sortie pour la tâche Stream Analytics.

5. Pointez sur le jeu de données que vous avez créé, sélectionnez le menu **plus d’options** (les trois points à droite du nom du jeu de données), puis sélectionnez **Créer un rapport**.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png" alt-text="Créer un rapport Microsoft Power BI":::

6. Créez un graphique en courbes pour afficher la température en temps réel et au fil du temps.

   1. Dans le volet **Visualisations** de la page de création de rapport, sélectionnez l’icône du graphique en courbes pour ajouter un graphique de ce type. Utilisez les repères situés sur les côtés et les angles du graphique pour en ajuster la taille et la position.

   2. Sur le volet **Champs**, développez la table que vous avez indiquée lors de la création de la sortie du travail Stream Analytics.

   3. Faites glisser **EventEnqueuedUtcTime** vers **Axe** dans le volet **Visualisations**.

   4. Faites glisser **Température** vers **Valeurs**.

      Un graphique en courbes est créé. L’axe des abscisses affiche la date et l’heure du fuseau horaire UTC. Quant à l’axe des ordonnées, il affiche la température fournie par le capteur.

      :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png" alt-text="Ajouter un graphique en courbes sur la température dans un rapport Microsoft Power BI":::

     > [!NOTE]
     > En fonction de l’appareil ou de l’appareil simulé que vous utilisez pour envoyer des données de télémétrie, votre liste de champs peut être légèrement différente.
     >

8. Sélectionnez **Enregistrer** pour enregistrer le rapport. Lorsque vous y êtes invité, entrez un nom pour votre rapport. Lorsque vous êtes invité à indiquer une étiquette de confidentialité, vous pouvez sélectionner **Public**, puis sélectionner **Enregistrer**.

10. Toujours dans le volet du rapport, sélectionnez **Fichier** > **Intégrer le rapport** > **Site web ou portail**.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-embed-report.png" alt-text="Sélectionner le site web du rapport d’incorporation pour le rapport de Power BI Microsoft":::

    > [!NOTE]
    > Si vous recevez une notification pour contacter votre administrateur, vous devrez peut-être le faire pour pouvoir créer du code incorporé. La création de code incorporé doit être activée avant que vous puissiez effectuer cette étape.
    >
    > :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png" alt-text="Notification vous invitant à contacter votre administrateur":::


11. Vous obtenez le lien d’accès au rapport, que vous pouvez partager avec les utilisateurs pour leur permettre d’y accéder, ainsi qu’un extrait de code, qui permet d’intégrer le rapport dans votre blog ou site web. Copiez le lien dans la fenêtre **Code d’intégration sécurisé**, puis fermez cette fenêtre.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/copy-secure-embed-code.png" alt-text="Copier le lien de rapport incorporé":::

12. Ouvrez un navigateur web et collez le lien dans la barre d’adresse.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png" alt-text="Publier un rapport Microsoft Power BI":::

Microsoft propose également des [applications mobiles Power BI](https://powerbi.microsoft.com/documentation/powerbi-power-bi-apps-for-mobile-devices/), qui permettent d’afficher les tableaux de bord et rapports Power BI sur votre appareil mobile et d’interagir avec eux.

## <a name="cleanup-resources"></a>Nettoyer les ressources

Dans ce tutoriel, vous avez créé un groupe de ressources, un hub IoT, un travail Stream Analytics et un jeu de données dans Power BI. 

Si vous envisagez de suivre les autres tutoriels, conservez le groupe de ressources et le hub IoT afin de les réutiliser plus tard. 

Si vous n’avez plus besoin du hub IoT ou des autres ressources que vous avez créées, vous pouvez supprimer le groupe de ressources dans le portail. Pour ce faire, sélectionnez le groupe de ressources, puis sélectionnez **Supprimer le groupe de ressources**. Si vous souhaitez conserver le hub IoT, vous pouvez supprimer les autres ressources dans le volet **Vue d’ensemble** du groupe de ressources. Pour ce faire, cliquez avec le bouton droit sur la ressource, sélectionnez **Supprimer** dans le menu contextuel et suivez les invites. 

### <a name="use-the-azure-cli-to-clean-up-azure-resources"></a>Utiliser Azure CLI pour nettoyer des ressources Azure

Pour supprimer le groupe de ressources et toutes ses ressources, utilisez la commande [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name {your resource group}
```

### <a name="clean-up-power-bi-resources"></a>Nettoyer des ressources Power BI

Vous avez créé le jeu de données **PowerBiVisualizationDataSet** dans Power BI. Pour le supprimer, connectez-vous à votre compte [Power BI](https://powerbi.microsoft.com/). Dans le menu de gauche, sous **Espaces de travail**, sélectionnez **Mon espace de travail**. Dans la liste des jeux de données de l’onglet **DataSets + dataflows**, pointez sur le jeu de données **PowerBiVisualizationDataSet**. Sélectionnez les trois points verticaux qui s’affichent à droite du nom du jeu de données pour ouvrir le menu **Plus d’options**, puis sélectionnez **Supprimer** et suivez les invites. Lorsque vous supprimez le jeu de données, le rapport est également supprimé.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à utiliser Power BI pour visualiser les données de capteur en temps réel à partir de votre hub Azure IoT en effectuant les tâches suivantes :

> [!div class="checklist"]
> * Créer un groupe de consommateurs dans votre hub IoT.
> * Créer et configurer un travail d’Azure Stream Analytics pour lire la télémétrie de température de votre groupe de consommateurs et l’envoyer à Power BI.
> * Configurer un rapport sur les données de température dans Power BI et le partager sur le web.

Pour découvrir une autre façon de visualiser des données d’Azure IoT Hub, consultez l’article suivant.

> [!div class="nextstepaction"]
> [Visualiser les données de capteur en temps réel à partir de votre Azure IoT Hub dans une application web](iot-hub-live-data-visualization-in-web-apps.md).