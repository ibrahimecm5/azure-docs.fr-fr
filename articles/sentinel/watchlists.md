---
title: Utiliser les listes de surveillance Microsoft Sentinel
description: Cet article explique comment utiliser les listes Microsoft Sentinel pour créer des listes blanches et des listes noires, enrichir les données d’événement et contribuer à examiner les menaces.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 54fd6bb3b1d2c937364db4f1883f0919342e116d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522077"
---
# <a name="use-microsoft-sentinel-watchlists"></a>Utiliser les listes de surveillance Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les listes de surveillance Microsoft Sentinel activent la collecte de données à partir de sources de données externes pour la corrélation avec les événements dans votre environnement Microsoft Sentinel. Une fois créées, vous pouvez utiliser les Watchlists dans vos playbooks de recherche, de détection, de recherche de menace et de réponse. Les listes de surveillance sont stockées dans votre espace de travail Microsoft Sentinel en tant que paires nom-valeur et sont mises en cache pour des performances de requête optimales et une faible latence.

> [!IMPORTANT]
> Les fonctionnalités indiquées sont disponibles en préversion. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

Les scénarios courants pour l’utilisation des Watchlists sont notamment les suivants :

- **L’examen des menaces** et la réponse rapide aux incidents avec l’importation rapide d’adresses IP, de hachages de fichiers et d’autres données à partir de fichiers CSV. Une fois importées, vous pouvez utiliser des paires nom-valeur de Watchlist pour les jointures et les filtres dans les règles d’alerte, la recherche de menaces, les classeurs, les notebook et les requêtes générales.

- **Importation de données métier** en tant que Watchlist. Par exemple, importez des listes d’utilisateurs avec un accès privilégié au système, ou des employés arrêtés, puis utilisez la Watchlist pour créer des listes d’autorisation et de blocage utilisées pour détecter ou empêcher ces utilisateurs de se connecter au réseau.

- **Réduction de fatigue des alertes**. Créer des listes d’autorisation pour supprimer des alertes d’un groupe d’utilisateurs, tels que les utilisateurs d’adresses IP autorisées qui effectuent des tâches qui déclencheraient normalement l’alerte, et empêchent les événements bénins de devenir des alertes.

- **Enrichissement des données d'événement**. Utilisez les Watchlists pour enrichir vos données d’événement avec des combinaisons nom-valeur dérivées de sources de données externes.

> [!NOTE]
> - L’utilisation de ces listes doit être limitée aux données de référence, car elles ne sont pas conçues pour les volumes de données importants.
>
> - Le **nombre total d’éléments Watchlist actifs** dans l’ensemble des watchlists d’un même espace de travail est actuellement limité à **10 millions**. Les éléments supprimés ne sont pas comptabilisés dans ce total. Si vous avez besoin de la possibilité de faire référence à de grands volumes de données, envisagez plutôt l’ingestion de [journaux personnalisés](../azure-monitor/agents/data-sources-custom-logs.md).
>
> - Les Watchlists ne peuvent être référencées qu’à partir du même espace de travail. Les scénarios entre espaces de travail et/ou Lighthouse ne sont pas pris en charge pour le moment.

## <a name="create-a-new-watchlist"></a>Création d’une nouvelle Watchlist

1. À partir du Portail Azure, accédez à **Microsoft Sentinel** > **Configuration** > **Watchlist**, puis sélectionnez **+ Ajouter une nouvelle**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new.png" alt-text="nouvelle Watchlist" lightbox="./media/watchlists/sentinel-watchlist-new.png":::

1. Dans la page **Général**, indiquez le nom, la description et l’alias de la Watchlist, puis sélectionnez **Suivant : Source**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-general.png" alt-text="page général de la Watchlist":::

1. Sur la page **Source**, sélectionnez le type de jeu de données (actuellement seul le volume partagé de cluster est disponible), entrez le nombre de lignes **avant la ligne d’en-tête** dans votre fichier de données, puis choisissez un fichier à charger de l’une des deux manières suivantes :
    1. Cliquez sur le lien **Rechercher des fichiers** dans la zone **Charger un fichier**, puis sélectionnez le fichier de données à charger.
    1. Glissez-déplacez votre fichier de données dans la zone **Charger le fichier** .

    Vous verrez un aperçu des 50 premières lignes de résultats dans l’écran de l’Assistant.

1. Dans le champ **SearchKey**, entrez le nom d’une colonne de votre Watchlist que vous souhaitez utiliser en tant que jointure avec d’autres données ou un objet de recherche fréquent. Par exemple, si votre serveur Watchlist contient des noms de pays et leurs codes de pays respectifs à deux lettres et que vous prévoyez d’utiliser souvent les codes de pays pour la recherche ou les jointures, utilisez la colonne **code** en tant que SearchKey.

1. <a name="review-and-create"></a>Sélectionnez **Suivant : Vérifier et créer**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="page source de la Watchlist" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > Les chargements de fichiers sont actuellement limités à des fichiers d’une taille maximale de 3,8 Mo.

1. Passez en revue les informations, vérifiez qu’elles sont correctes, attendez que le message *Validation réussie*, puis sélectionnez **Créer**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-review.png" alt-text="page de vérification de la Watchlist":::

    Une notification s’affiche une fois que la Watchlist est créée.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="notification de création réussie de la Watchlist" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="create-a-new-watchlist-using-a-template-public-preview"></a>Créer un nouveau Watchlist à l’aide d’un modèle (version préliminaire publique)

1. Dans le portail Azure, accédez à **Microsoft Sentinel** > **Configuration** > **Watchlist** > **Modèles (Préversion)** .

1. Sélectionnez un modèle dans la liste pour voir les détails à droite, puis sélectionnez **Créer à partir d’un modèle** pour créer votre Watchlist.

    :::image type="content" source="./media/watchlists/create-watchlist-from-template.png" alt-text="Créer une Watchlist à partir d’un modèle intégré." lightbox="./media/watchlists/create-watchlist-from-template.png":::

1. Continuez dans l’**Assistant Watchlist** :

    - Lorsque vous utilisez un modèle de watchlist, les valeurs **Nom**, **Description** et **Alias de la watchlist** sont toutes en lecture seule.

    - Sélectionnez **Télécharger le schéma** pour télécharger un fichier CSV qui contient le schéma approprié attendu pour le modèle Watchlist sélectionné.

    Chaque modèle Watchlist intégré possède son propre jeu de données qui figure dans le fichier CSV attaché au modèle. Pour plus d’informations, consultez [Schémas de watchlists intégrés](watchlist-schemas.md).

1.  Remplissez votre version locale du fichier CSV, puis chargez-le à nouveau dans l’Assistant.

1. Continuez comme si vous alliez [créer complètement une watchlist](#review-and-create), puis utilisez votre watchlist avec des [requêtes](#use-watchlists-in-queries) et des [règles d’analyse](#use-watchlists-in-analytics-rules).

## <a name="use-watchlists-in-queries"></a>Utilisation des Watchlists dans les requêtes

> [!TIP]
> Pour optimiser les performances des requêtes, utilisez **SearchKey** (représentant le champ que vous avez défini lors de la création du Watchlist) comme clé pour les jointures dans vos requêtes. Reportez-vous à l’exemple ci-dessous.

1. Dans le Portail Azure, accédez à **Microsoft Sentinel** > **Configuration** > **Watchlist**, sélectionnez la Watchlist que vous voulez utiliser, puis sélectionnez **Afficher dans Log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="utilisation des Watchlists dans les requêtes" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png" :::

1. Les éléments de votre Watchlist sont automatiquement extraits pour votre requête et s’affichent sous l’onglet **Résultats**. L’exemple ci-dessous montre les résultats de l’extraction des champs **Nom** et **Adresse IP** . Le **SearchKey** est indiqué comme sa propre colonne.

    > [!NOTE]
    > Le timestamp de vos requêtes sera ignoré à la fois dans l’interface utilisateur de la requête et dans les alertes planifiées.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="requêtes avec les champs de la Watchlist" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::

1. Vous pouvez comparer les données de n’importe quelle table avec celles d’une Watchlist en traitant la Watchlist comme une table pour les jointures et les recherches. Utilisez **SearchKey** comme clé pour votre jointure.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('mywatchlist') 
     on $left.RemoteIPCountry == $right.SearchKey
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="requêtes par rapport à la Watchlist en tant que recherche" lightbox="./media/watchlists/sentinel-watchlist-queries-join.png":::

## <a name="use-watchlists-in-analytics-rules"></a>Utilisation des Watchlists dans les règles d’analyse

> [!TIP]
> Pour optimiser les performances des requêtes, utilisez **SearchKey** (représentant le champ que vous avez défini lors de la création du Watchlist) comme clé pour les jointures dans vos requêtes. Reportez-vous à l’exemple ci-dessous.

Pour utiliser des Watchlists dans les règles d’analyse, à partir du Portail Azure, accédez à **Microsoft Sentinel** > **Configuration** > **Analyse**, puis créez une règle à l’aide de la fonction `_GetWatchlist('<watchlist>')` de la requête.

1. Dans cet exemple, créez une Watchlist appelée « ipwatchlist » avec les valeurs suivantes :

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="liste de quatre éléments pour Watchlist":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-other.png" alt-text="créer une Watchlist avec quatre éléments":::

1. Ensuite, créez la règle d’analyse.  Dans cet exemple, nous incluons uniquement les événements des adresses IP figurant dans la Watchlist :

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    //Use SearchKey for the best performance
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project SearchKey)
    )
    ```

    :::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="utilisation des Watchlists dans les règles d’analyse":::

## <a name="view-list-of-watchlists-aliases"></a>Afficher la liste des alias de Watchlists

Pour obtenir une liste d’alias de Watchlist, à partir du Portail Azure, accédez à **Microsoft Sentinel** > **Général** > **Journaux**, puis exécutez la requête suivante : `_GetWatchlistAlias`. Vous pouvez voir la liste des alias dans l’onglet **Résultats**.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-alias.png" alt-text="liste de Watchlists" lightbox="./media/watchlists/sentinel-watchlist-alias.png":::

## <a name="manage-your-watchlist-in-the-microsoft-sentinel-portal"></a>Gérer vos listes de surveillance dans le portail Microsoft Sentinel

Vous pouvez également voir, modifier et créer des éléments watchlist directement à partir du panneau Watchlist dans le portail Microsoft Sentinel.

1. Pour modifier votre watchlist, accédez à **Microsoft Sentinel > Configuration > Watchlist**, sélectionnez la watchlist à modifier, puis sélectionnez **Modifier les éléments de la watchlist** dans le volet des détails.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit.png" alt-text="Capture d’écran montrant comment modifier une watchlist" lightbox="./media/watchlists/sentinel-watchlist-edit.png":::

1. Pour modifier un élément watchlist existant, activez la case à cocher de cet élément, modifiez-le, puis sélectionnez **Enregistrer**. Cliquez sur **Oui** à l’invite de confirmation.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-change.png" alt-text="Capture d’écran montrant comment marquer et modifier un élément Watchlist.":::

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-confirm.png" alt-text="Capture d’écran de confirmation des modifications.":::

1. Pour ajouter un élément à votre watchlist, sélectionnez **Ajouter nouveau** dans l’écran **Modifier les éléments de la watchlist**, remplissez les champs dans le volet **Ajouter un élément à la watchlist**, puis sélectionnez **Ajouter** en bas de ce volet.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-add.png" alt-text="Capture d’écran montrant comment ajouter un nouvel élément à votre Watchlist.":::

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à utiliser les Watchlists dans Microsoft Sentinel pour enrichir les données et améliorer les investigations. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](./detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.
