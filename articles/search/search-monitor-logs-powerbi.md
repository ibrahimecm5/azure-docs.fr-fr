---
title: Visualisez les journaux et les métriques avec Power BI
description: Visualisez les journaux et les métriques Recherche cognitive Azure avec Power BI.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 57dd31643560b54f6f54b8352966a4c05cb6a35b
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892890"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualiser les journaux et les métriques Recherche cognitive Azure avec Power BI

[Recherche cognitive Azure](./search-what-is-azure-search.md) peut envoyer des journaux d’opérations et des métriques de service vers un compte Stockage Azure, que vous pouvez ensuite visualiser dans Power BI. Cet article explique la démarche à suivre et la manière d’utiliser un modèle d’application Power BI pour visualiser les données. Le modèle peut vous aider à obtenir des informations détaillées sur votre service de recherche, notamment des informations sur les métriques de requête, d’indexation, d’opération et de service.

L’application modèle Power BI **Recherche cognitive Azure : Analyser les journaux et les métriques** est accessible dans le [marketplace d’applications Power BI](https://appsource.microsoft.com/marketplace/apps).

## <a name="set-up-the-app"></a>Configurer l’application

1. Activez la journalisation des métriques et des ressources pour votre service de recherche :

    1. Créez ou identifiez un [compte Stockage Azure](../storage/common/storage-account-create.md) existant dans lequel vous pouvez archiver les journaux.
    1. Accédez à votre service Recherche cognitive Azure dans le Portail Azure.
    1. Dans la section Supervision de la colonne gauche, sélectionnez **Paramètres de diagnostic**.

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="Capture d’écran montrant comment sélectionner les paramètres de diagnostic dans la section Surveillance du service Recherche cognitive Azure." border="false":::

    1. Sélectionnez **+ Ajouter un paramètre de diagnostic**.
    1. Activez la case **Archiver dans un compte de stockage**, fournissez les informations de votre compte de stockage, puis cochez les cases **OperationLogs** et **AllMetrics**.

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="Capture d’écran montrant comment opérer des sélections pour la journalisation de métriques et de ressources dans la page Paramètres de diagnostic.":::
    1. Sélectionnez **Enregistrer**.

1. Une fois la journalisation activée, utilisez votre service de recherche pour commencer à générer des journaux d’activité et des mesures. Cela prend jusqu’à une heure avant que les conteneurs n’apparaissent dans le Stockage Blob avec ces journaux. Vous verrez un conteneur **insights-logs-operationlogs** pour les journaux de trafic de recherche et un conteneur **insights-metrics-pt1m** pour les métriques.

1. Recherchez l’application Recherche cognitive Azure Power BI dans le [marketplace d’applications Power BI](https://appsource.microsoft.com/marketplace/apps) et installez-la dans un nouvel espace de travail ou un espace de travail existant. L’application s’appelle **Recherche cognitive Azure : Analyser les journaux et les métriques**.

1. Après avoir installé l’application, sélectionnez-la à partir de votre liste d’applications dans Power BI.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="Capture d’écran montrant l’application Recherche cognitive Azure à sélectionner dans la liste des applications.":::

1. Sélectionnez **Se connecter** pour connecter vos données.

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="Capture d’écran montrant comment se connecter à vos données dans l’application Recherche cognitive Azure.":::

1. Entrez le nom du compte de stockage qui contient vos journaux et métriques. Par défaut, l’application examine les 10 derniers jours de données, mais cette valeur peut être modifiée avec le paramètre **Jours**.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="Capture d’écran montrant comment entrer le nom du compte de stockage et le nombre de jours à interroger dans la page Se connecter à la Recherche cognitive Azure.":::

1. Sélectionnez **Clé** comme méthode d’authentification et indiquez votre clé de compte de stockage. Sélectionnez **Privé** comme niveau de confidentialité. Cliquez sur Se connecter pour commencer le processus de chargement.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="Capture d’écran montrant comment entrer la méthode d’authentification, la clé de compte et le niveau de confidentialité dans la page Se connecter à la Recherche cognitive Azure.":::

1. Attendez que les données soient actualisées. Cela peut prendre un certain temps en fonction de la quantité de données dont vous disposez. Vous pouvez voir si les données sont toujours en cours d’actualisation d’après l’indicateur ci-dessous.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="Capture d’écran montrant comment lire les informations dans la page d’actualisation des données.":::

1. Une fois l’actualisation des données terminée, sélectionnez **Rapport Recherche cognitive Azure** pour afficher le rapport.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="Capture d’écran montrant comment sélectionner le rapport de Recherche cognitive Azure dans la page d’actualisation des données.":::

1. Veillez à actualiser la page avant d’avoir ouvert le rapport afin qu’il s’ouvre avec vos données.

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="Capture d’écran du rapport Recherche cognitive Azure Power BI.":::

## <a name="modify-app-parameters"></a>Modifier les paramètres de l’application

Si vous souhaitez visualiser des données à partir d’un autre compte de stockage ou modifier le nombre de jours de données à interroger, suivez les étapes ci-dessous pour modifier les paramètres **Jours** et **Compte de stockage**.

1. Accédez à vos applications Power BI, recherchez votre application Recherche cognitive Azure et sélectionnez le bouton **Modifier l’application** pour afficher l’espace de travail.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="Capture d’écran montrant comment sélectionner le bouton Modifier l’application pour l’application Recherche cognitive Azure.":::

1. Sélectionnez **Paramètres** parmi les options du jeu de données.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="Capture d’écran montrant comment sélectionner Paramètres à partir des options de jeu de données de recherche cognitive Azure.":::

1. Dans l’onglet Jeux de données, modifiez les valeurs des paramètres et sélectionnez **Appliquer**. En cas de problème avec la connexion, mettez à jour les informations d’identification de la source de données sur la même page.

1. Revenez à l’espace de travail et sélectionnez **Actualiser** parmi les options du jeu de données.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="Capture d’écran montrant comment sélectionner Actualiser maintenant dans les options de jeu de données de recherche cognitive Azure.":::

1. Ouvrez le rapport pour afficher les données mises à jour. Vous devrez peut-être actualiser également le rapport pour afficher les données les plus récentes.

## <a name="troubleshooting-report-issues"></a>Résoudre les problèmes liés aux rapports

Si vous constatez que vous ne pouvez pas voir vos données, suivez ces étapes de résolution de problèmes :

1. Ouvrez le rapport et actualisez la page pour vous assurer de disposer des données les plus récentes. Une option du rapport permet d’actualiser les données. Sélectionnez-la pour récupérer les données les plus récentes.

1. Vérifiez que le nom du compte de stockage et la clé d’accès que vous avez fournis sont corrects. Le nom du compte de stockage doit correspondre au compte configuré avec les journaux de votre service de recherche.

1. Vérifiez que votre compte de stockage contient les conteneurs **insights-logs-operationlogs** et **insights-metrics-pt1m** et que chaque conteneur contient des données. Les journaux et les métriques se trouvent dans deux ou trois couches de dossiers.

1. Vérifiez si le jeu de données est toujours en cours d’actualisation. L’indicateur d’état d’actualisation est indiqué à l’étape 8 ci-dessus. S’il est toujours en cours d’actualisation, patientez jusqu’à la fin de l’actualisation pour ouvrir et actualiser le rapport.

## <a name="next-steps"></a>Étapes suivantes

+ [Surveiller les opérations et l’activité de recherche](search-monitor-usage.md)
+ [Qu’est-ce que Power BI ?](/power-bi/fundamentals/power-bi-overview)
+ [Concepts de base pour les concepteurs du service Power BI](/power-bi/service-basic-concepts)