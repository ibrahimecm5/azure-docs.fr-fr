---
title: Créer un rapport Power BI à partir de données Microsoft Sentinel
description: Découvrez comment créer un rapport Power BI à l’aide d’une requête exportée à partir de Microsoft Sentinel Log Analytics. Partagez votre rapport avec d’autres personnes dans le service Power BI et un canal Teams.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cd6c6de1fc1a83cecdf0ea96e912f989bdf6b904
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521317"
---
# <a name="tutorial-create-a-power-bi-report-from-microsoft-sentinel-data"></a>Tutoriel : Créer un rapport Power BI à partir de données Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Power BI](https://powerbi.microsoft.com/) est une plateforme de création de rapports et d’analyse qui transforme les données en visualisations interactives, cohérentes et immersives. Power BI vous permet de vous connecter facilement à des sources de données, de visualiser et de découvrir des relations et de partager des insights avec qui vous voulez.

Vous pouvez baser des rapports Power BI sur des données d’espaces de travail Microsoft Sentinel Log Analytics et partager ces rapports avec des personnes qui n’ont pas accès à Microsoft Sentinel. Par exemple, vous pouvez partager des informations sur les tentatives de connexion ayant échoué avec les propriétaires d’applications, sans leur octroyer un accès à Microsoft Sentinel. Les visualisations Power BI peuvent fournir les données en un clin d’œil.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Exporter une requête Kusto Log Analytics dans une requête de langage M Power BI.
> * Utilisez la requête M dans Power BI Desktop pour créer des visualisations et un rapport.
> * Publiez le rapport sur le service Power BI et partagez-le avec d’autres utilisateurs.
> * Ajoutez le rapport à un canal Teams.

Les personnes auxquelles vous avez accordé l’accès dans le service Power BI et les membres du canal Teams peuvent voir le rapport sans avoir besoin d’autorisations Microsoft Sentinel.

> [!NOTE]
> Ce tutoriel fournit une procédure basée sur un scénario pour répondre à une demande importante des clients : l’affichage des rapports d’analyse dans PowerBI pour vos données Microsoft Sentinel. Pour plus d’informations, consultez [Connecter des sources de données](connect-data-sources.md) et [Visualiser les données collectées](get-visibility.md).
>
## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Au minimum, accès en lecture à un espace de travail Microsoft Sentinel Log Analytics qui surveille les tentatives de connexion.
- Compte Power BI disposant d’un accès en lecture à l’espace de travail Log Analytics.
- [Power BI Desktop installé à partir du Microsoft Store](https://aka.ms/pbidesktopstore).

## <a name="export-a-query-from-log-analytics"></a>Exporter une requête à partir de Log Analytics

Créez, exécutez et exportez une requête Kusto dans votre espace de travail Microsoft Sentinel Log Analytics. 

1. Pour créer une requête simple, dans votre espace de travail Microsoft Sentinel Log Analytics, sélectionnez **Journaux**. Dans l’éditeur de requête, sous **Nouvelle requête 1**, entrez la requête Kusto suivante :
   
   ```kusto
   SigninLogs
   |  where TimeGenerated >ago(7d)
   | summarize Attempts = count(), Failed=countif(ResultType !=0), Succeeded = countif(ResultType ==0) by AppDisplayName
   |  top 10 by Failed
   | sort by Failed
   ```
   
   Vous pouvez également utiliser la requête Kusto Microsoft Sentinel Log Analytics de votre choix.
   
1. Sélectionnez **Exécuter** pour exécuter la requête et générer des résultats.
   
   :::image type="content" source="media/powerbi/query.png" alt-text="Capture d’écran montrant la requête et les résultats de la requête Kusto Log Analytics.":::
   
1. Pour exporter la requête au format de requête M Power BI, sélectionnez **Exporter**, puis **Exporter vers Power BI (requête M)** . Log Analytics exporte la requête vers un fichier texte intitulé *PowerBIQuery.txt*.
   
   :::image type="content" source="media/powerbi/export.png" alt-text="Capture d’écran montrant l’exportation de la requête au format M Power BI.":::
   
1. Copiez le contenu du fichier exporté.

## <a name="get-the-data-in-power-bi-desktop"></a>Obtenir les données dans Power BI Desktop

Exécutez la requête M exportée dans Power BI Desktop pour obtenir des données.

1. Ouvrez Power BI Desktop et connectez-vous à votre compte Power BI disposant d’un accès en lecture à l’espace de travail Log Analytics.
   
   :::image type="content" source="media/powerbi/sign-in.png" alt-text="Capture d’écran montrant la connexion à Power BI Desktop.":::
   
1. Dans le ruban Power BI, sélectionnez **Obtenir des données**, puis sélectionnez **Requête vide**. L’**Éditeur Power Query** s’ouvre.
   
   :::image type="content" source="media/powerbi/blank-query.png" alt-text="Capture d’écran montrant l’option Requête vide sélectionnée sous Obtenir des données dans Power BI Desktop.":::
   
1. Dans l’**Éditeur Power Query**, sélectionnez **Éditeur avancé**.
   
1. Collez le contenu copié du fichier *PowerBIQuery.txt* exporté dans la fenêtre de l’**Éditeur avancé**, puis sélectionnez **Terminé**.
   
   :::image type="content" source="media/powerbi/advanced-editor.png" alt-text="Capture d’écran montrant la requête M collée dans l’Éditeur avancé Power BI.":::
   
1. Dans l’**Éditeur Power Query**, renommez la requête *App_signin_stats*, puis sélectionnez **Fermer et appliquer**.
   
   :::image type="content" source="media/powerbi/close-apply.png" alt-text="Capture d’écran montrant la requête renommée et l’option Fermer et appliquer dans l’Éditeur Power Query.":::

## <a name="create-visualizations-from-the-data"></a>Créer des visualisations à partir des données

Maintenant que vos données se trouvent dans Power BI, vous pouvez créer des visualisations pour fournir des insights sur les données.

### <a name="create-a-table-visual"></a>Créer un visuel de table

Tout d’abord, créez une table qui affiche tous les résultats de la requête.

1. Pour ajouter une visualisation de table au canevas Power BI Desktop, sélectionnez l’icône de **table** sous **Visualisations**.
   
   :::image type="content" source="media/powerbi/table.png" alt-text="Capture d’écran montrant l’icône de table sous Visualisations dans Power BI Desktop.":::
   
1. Sous **Champs**, sélectionnez tous les champs de votre requête, afin qu’ils apparaissent tous dans la table. Si la table n’affiche pas toutes les données, agrandissez-la en faisant glisser ses poignées de sélection.
   
   :::image type="content" source="media/powerbi/select-fields.png" alt-text="Capture d’écran montrant tous les champs sélectionnés pour la visualisation de table.":::
   
### <a name="create-a-pie-chart"></a>Créer un graphique à secteurs

Ensuite, créez un graphique à secteurs qui indique les applications qui ont eu le plus de tentatives de connexion.

1. Désélectionnez le visuel de table en cliquant ou en appuyant dessus, puis sous **Visualisations**, sélectionnez l’icône de **graphique à secteurs**.
   
   :::image type="content" source="media/powerbi/pie-chart.png" alt-text="Capture d’écran montrant l’icône de graphique à secteurs sous Visualisations dans Power BI Desktop.":::
   
1. Sélectionnez **AppDisplayName** dans le compartiment **Légende** ou faites-le glisser à partir du volet **Champs**. Sélectionnez **Ayant échoué** dans le compartiment **Valeurs**, ou faites-le glisser à partir de **Champs**. Le graphique à secteurs affiche maintenant le nombre de tentatives de connexion ayant échoué par application.
   
   :::image type="content" source="media/powerbi/failed.png" alt-text="Capture d’écran montrant le graphique à secteurs avec le nombre de tentatives de connexion ayant échoué par application.":::
   
### <a name="create-a-new-quick-measure"></a>Créer une mesure rapide

Vous pouvez également afficher le pourcentage d’échecs de tentatives de connexion pour chaque application. Étant donné que votre requête n’a pas de colonne de pourcentage, vous pouvez créer une mesure pour afficher ces informations.

1. Sous **Visualisations**, sélectionnez l’icône d’**histogramme empilé** pour créer un histogramme empilé.
   
   :::image type="content" source="media/powerbi/column-chart.png" alt-text="Capture d’écran montrant l’icône d’histogramme empilé sous Visualisations dans Power BI Desktop.":::
   
1. Une fois la nouvelle visualisation sélectionnée, sélectionnez **Mesure rapide** dans le ruban.
   
1. Dans la fenêtre **Mesures rapides**, sous **Calcul**, sélectionnez **Division**. Faites glisser **Ayant échoué** depuis **Champs** dans le champ **Numérateur** et faites glisser **Tentatives** de **Champs** vers **Dénominateur**.
   
   :::image type="content" source="media/powerbi/quick-measures.png" alt-text="Capture d’écran montrant les paramètres dans la fenêtre Mesures rapides.":::
   
1. Sélectionnez **OK**. La nouvelle mesure s’affiche dans le volet **Champs**.
   
1. Sélectionnez la nouvelle mesure dans le volet **Champs**. Ensuite, sous **Mise en forme** dans le ruban, sélectionnez **Pourcentage**.
   
   :::image type="content" source="media/powerbi/percentage.png" alt-text="Capture d’écran montrant la nouvelle mesure sélectionnée dans le volet Champs et Pourcentage sélectionné sous Mise en forme dans le ruban.":::
   
1. Une fois la visualisation de l’histogramme sélectionnée sur le canevas, sélectionnez ou faites glisser le champ **AppDisplayName** dans le compartiment **Axe** et la nouvelle mesure **Ayant échoué divisé par Tentatives** dans le compartiment **Valeurs**. Le graphique affiche maintenant le pourcentage de tentatives de connexion ayant échoué pour chaque application.
   
   :::image type="content" source="media/powerbi/failed-percentage.png" alt-text="Capture d’écran montrant l’histogramme avec un pourcentage de tentatives ayant échoué pour chaque application.":::
   
### <a name="refresh-the-data-and-save-the-report"></a>Actualiser les données et enregistrer le rapport

1. Sélectionnez **Actualiser** pour récupérer les données les plus récentes de Microsoft Sentinel.
   
   :::image type="content" source="media/powerbi/refresh.png" alt-text="Capture d’écran montrant le bouton Actualiser dans le ruban.":::
   
1. Sélectionnez **Fichier** > **Enregistrer** et enregistrez votre rapport Power BI.

## <a name="create-a-power-bi-online-workspace"></a>Créer un espace de travail en ligne Power BI

Pour créer un espace de travail Power BI pour le partage du rapport :

1. Connectez-vous à [powerbi.com](https://powerbi.com) avec le compte utilisé pour Power BI Desktop et l’accès en lecture à Microsoft Sentinel.
   
1. Sous **Espaces de travail**, sélectionnez **Créer un espace de travail**. Nommez l’espace de travail *Rapports de gestion*, puis sélectionnez **Enregistrer**.
   
   :::image type="content" source="media/powerbi/create-workspace.png" alt-text="Capture d’écran montrant l’option Créer un espace de travail dans le service Power BI.":::
   
1. Pour accorder à des personnes et à des groupes un accès à l’espace de travail, sélectionnez les points **Autres options** en regard du nom de l’espace de travail, puis sélectionnez **Accès à l’espace de travail**.
   
   :::image type="content" source="media/powerbi/workspace-access.png" alt-text="Capture d’écran montrant l’option Accès à l’espace de travail dans le menu Autres options de l’espace de travail.":::
   
1. Dans le volet latéral **Accès à l’espace de travail**, vous pouvez ajouter les adresses e-mail des utilisateurs et attribuer un rôle à chaque utilisateur. Les rôles sont Administrateur, Membre, Contributeur et Lecteur.

## <a name="publish-the-power-bi-report"></a>Publier le rapport Power BI

Vous pouvez à présent utiliser Power BI Desktop pour publier votre rapport Power BI afin que d’autres personnes puissent le voir.

1. Dans votre nouveau rapport dans Power BI Desktop, sélectionnez **Publier**.
   
   :::image type="content" source="media/powerbi/publish.png" alt-text="Capture d’écran montrant l’option Publier dans le ruban Power BI Desktop.":::
   
1. Sélectionnez l’espace de travail **Rapports de gestion** dans lequel publier, puis choisissez **Sélectionner**.
   
   :::image type="content" source="media/powerbi/select-workspace.png" alt-text="Capture d’écran qui montre la sélection de l’espace de travail Rapports de gestion Power BI dans lequel publier.":::
   
## <a name="import-the-report-to-a-microsoft-teams-channel"></a>Importer le rapport dans un canal Microsoft Teams

Vous pouvez également faire en sorte que les membres du canal Équipes de gestion puissent afficher le rapport. Pour ajouter le rapport à un canal Teams :

1. Dans le canal Équipes de gestion, sélectionnez **+** pour ajouter un onglet, puis dans la fenêtre **Ajouter un onglet**, recherchez et sélectionnez **Power BI**.

   :::image type="content" source="media/powerbi/add-tab.png" alt-text="Capture d’écran qui montre la sélection de Power BI dans la fenêtre Ajouter un onglet dans Teams.":::
   
1. Sélectionnez votre nouveau rapport dans la liste des rapports Power BI, puis sélectionnez **Enregistrer**. Le rapport s’affiche dans un nouvel onglet dans le canal Teams.
   
   :::image type="content" source="media/powerbi/teams.png" alt-text="Capture d’écran montrant le rapport Power BI sous un onglet dans le canal Teams.":::

## <a name="schedule-report-refresh"></a>Planifier l’actualisation du rapport

Actualisez votre rapport Power BI selon une planification, afin que les données mises à jour apparaissent toujours dans le rapport.

1. Dans le service Power BI, sélectionnez l’espace de travail dans lequel vous avez publié votre rapport.
   
1. En regard du jeu de données du rapport, sélectionnez **Autres options** > **Paramètres**.
   
   :::image type="content" source="media/powerbi/settings.png" alt-text="Capture d’écran montrant l’option Paramètres sous Autres options dans le jeu de données de rapport Power BI.":::
   
1. Sélectionnez **Modifier les informations d’identification** pour fournir les informations d’identification d’un compte disposant d’un accès en lecture à l’espace de travail Log Analytics.
   
1. Sous **Actualisation planifiée**, définissez le curseur sur **Activé** et configurez une planification d’actualisation pour le rapport.
   
   :::image type="content" source="media/powerbi/schedule.png" alt-text="Capture d’écran montrant les paramètres Actualisation planifiée pour le jeu de données de rapport Power BI.":::

## <a name="next-steps"></a>Étapes suivantes

- [Importation de données de journal Azure Monitor dans Power BI](../azure-monitor/logs/log-powerbi.md)
- [Langage de formule Power Query M](/powerquery-m/)
