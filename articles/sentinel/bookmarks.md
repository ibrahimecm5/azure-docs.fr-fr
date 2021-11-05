---
title: Utiliser des signets de repérage pour les investigations de données dans Azure Sentinel
description: Cet article décrit comment utiliser les signets de repérage d’Azure Sentinel pour effectuer le suivi des données.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/01/2021
ms.openlocfilehash: 36f81136a4fd3f836c925c2cc44fa4413aaba341
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463584"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Suivi des données lors du repérage avec Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Le repérage des menaces nécessite généralement la vérification de montagnes de données de journal à la recherche de preuves de comportements malveillants. Pendant ce processus, les enquêteurs détectent des événements qu’ils souhaitent mémoriser, réexaminer et analyser afin de valider des hypothèses potentielles et de comprendre d’une compromission dans sa globalité.

Dans Azure Sentinel, les signets de chasse vous y aident en conservant les requêtes que vous avez exécutées dans **Azure Sentinel – Logs**, ainsi que les résultats de requête que vous jugez pertinents. Vous pouvez également enregistrer vos observations contextuelles et référencer vos découvertes en ajoutant des balises et des notes. Les données avec signet sont visibles par vous et vos collègues pour faciliter la collaboration.

Vous pouvez désormais identifier et résoudre les lacunes dans la couverture des techniques MITRE ATT&CK, sur toutes les requêtes de chasse, en mappant vos requêtes personnalisées à des techniques MITRE ATT&CK.

> [!IMPORTANT]
>
> Le mappage des techniques MITRE ATT&CK aux signets est actuellement disponible en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Vous pouvez également investiguer d’autres types d’entités tout en faisant la chasse aux signets, en mappant l’ensemble des types d’entités et des identificateurs pris en charge par Microsoft Sentinel Analytics dans vos requêtes personnalisées. Cela vous permet d’utiliser des signets pour explorer les entités retournées dans les résultats de la recherche de chasse à l’aide des [pages d’entité](entities-in-azure-sentinel.md#entity-pages), des [incidents](investigate-cases.md) et du [graphe d’investigation](investigate-cases.md#use-the-investigation-graph-to-deep-dive). Si un signet capture les résultats d’une requête de chasse, il hérite automatiquement de la technique MITRE ATT&CK et des mappages d’entité de la requête.

> [!IMPORTANT]
>
> Le mappage d’un ensemble développé de types d’entités et d’identificateurs à des signets est actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.
>

Si vous trouvez quelque chose qui doit être résolu en urgence au cours de la recherche dans vos journaux, vous pouvez facilement créer un signet et le promouvoir en incident ou l’ajouter à un incident existant. Pour plus d’informations sur les incidents, consultez [Examiner les incidents avec Azure Sentinel](investigate-cases.md).

Si vous avez trouvé quelque chose qui vaut la peine de se voir affecter un signet, mais qui n’est pas absolument urgent, vous pouvez créer un signet et revisiter vos données de signet à tout moment sous l’onglet **Signets** du volet **Chasse**. Vous pouvez utiliser les options de filtrage et de recherche pour trouver rapidement des données spécifiques dans le cadre de l’examen en cours. 

Vous pouvez visualiser vos données avec signet en cliquant sur **Examiner** dans les détails du signet. Cette opération lance l’expérience d’examen dans laquelle vous pouvez afficher, examiner et communiquer visuellement vos résultats à l’aide d’un diagramme d’entité-graphique interactif et d’une chronologie.

Vous pouvez également afficher vos données marquées d’un directement dans la table **HuntingBookmark** de votre espace de travail Log Analytics. Par exemple :

:::image type="content" source="media/bookmarks/bookmark-table.png" alt-text="Capture d’écran de l’affichage du tableau des signets de chasse." lightbox="media/bookmarks/bookmark-table.png":::

L’affichage des signets de la table vous permet de filtrer et de synthétiser les données marquées d’un signet, ainsi que de les joindre à d’autres sources de données, ce qui facilite la recherche de preuves pour la confirmation.

## <a name="add-a-bookmark"></a>Ajouter un signet

1. Dans le portail Azure, accédez à **Azure Sentinel** > **Gestion des menaces** > **Chasse** pour exécuter des requêtes liées à un comportement suspect et anormal.

1. Sélectionnez une requête de repérage et à droite, dans les détails de la requête repérage, sélectionnez **Exécuter la requête**. 

1. Sélectionnez **Afficher les résultats de la requête**. Par exemple :

    :::image type="content" source="media/bookmarks/new-processes-observed-example.png" alt-text="Capture d’écran de l’affichage des résultats de requête de la chasse Azure Sentinel.":::

    Cette action ouvre les résultats de la requête dans le volet **Journaux**.

1. Dans la liste des résultats de la requête de journal, utilisez les cases à cocher pour sélectionner une ou plusieurs lignes contenant des informations que vous trouvez intéressantes.

1. Sélectionnez **Ajouter un signet** :

    :::image type="content" source="media/bookmarks/add-hunting-bookmark.png" alt-text="Capture d’écran de l’ajout d’un signet de chasse à la requête." lightbox="media/bookmarks/add-hunting-bookmark.png":::

1. À droite, dans le volet **Ajouter un signet**, vous pouvez mettre à jour le nom du signet et ajouter des balises et des notes pour identifier facilement les informations intéressantes à propos de l’élément.

1. **(Préversion)** Les signets peuvent éventuellement être mappés à des techniques ou des sous-techniques MITRE ATT&CK. Les mappages MITRE ATT&CK sont hérités des valeurs mappées dans les requêtes de chasse, mais vous pouvez également les créer manuellement. Sélectionnez la tactique MITRE ATT&CK associée à la technique souhaitée dans le menu déroulant de la section **Tactiques et techniques (préversion)** du volet **Ajouter un signet**. Le menu se développe pour afficher toutes les techniques MITRE ATT&CK, et vous pouvez sélectionner plusieurs techniques et sous-techniques dans ce menu.

    :::image type="content" source="media/bookmarks/mitre-attack-mapping.png" alt-text="Capture d’écran montrant comment mapper des tactiques et des techniques Mitre Attack à des signets.":::

1. **(Préversion)** Désormais, un ensemble étendu d’entités peut être extrait des résultats de la requête avec signet pour une investigation plus poussée. Dans la section **Mappage d’entités (préversion)** , utilisez les listes déroulantes pour sélectionner les [types d’entités et les identificateurs](entities-reference.md). Mappez ensuite la colonne dans les résultats de la requête contenant l’identificateur correspondant. Par exemple :

    :::image type="content" source="media/bookmarks/map-entity-types-bookmark.png" alt-text="Capture d’écran du mappage de types d’entité pour les signets de chasse.":::

    Pour afficher le signet dans le graphe d’investigation, vous devez mapper au moins une entité. Les mappages d’entités aux types d’entité Compte, Hôte, Adresse IP et URL créés avant cette préversion sont toujours pris en charge, ce qui permet de préserver la compatibilité descendante. 

1. Cliquez sur **Enregistrer** pour valider vos modifications et ajouter le signet. Toutes les données avec signet sont partagées avec d’autres analystes, et constituent une première étape vers une expérience d’investigation collaborative.

 
> [!NOTE]
> Les résultats de la requête de journal prennent en charge les signets chaque fois que ce volet est ouvert à partir d’Azure Sentinel. Par exemple, vous sélectionnez **Général** > **Journaux** dans la barre de navigation, sélectionnez les liens d’événements dans le graphe d'examen ou sélectionnez un ID d’alerte dans les détails complets d’un incident (actuellement en préversion). Vous ne pouvez pas créer de signets lorsque le volet **Journaux** est ouvert à partir d’autres emplacements, par exemple directement depuis Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Afficher et mettre à jour des signets 

1. Dans le portail Azure, accédez à **Azure Sentinel** > **Gestion des menaces** > **Chasse**. 

2. Sélectionnez l’onglet **Signets** pour afficher la liste des signets.

3. Utilisez les options de filtre ou de la zone de recherche pour trouver un signet spécifique.

4. Sélectionnez des signets individuels et affichez les détails de ce signet dans le volet de détails de droite.

5. Apportez les modifications nécessaires. Celles-ci sont automatiquement enregistrées.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Exploration des signets dans le graphe d’examen

1. Dans le portail Azure, accédez à **Azure Sentinel** > **Gestion des menaces** > **Chasse** > **Signets**, puis sélectionnez le(s) signet(s) que vous souhaitez investiguer.

2. Dans les détails du signet, assurez-vous qu’au moins une entité est mappée.

3. Sélectionnez **Examiner** pour afficher le signet dans le graphe d’investigation.

Pour savoir comment utiliser le graphe d’examen, consultez [Utiliser le graphe d’examen pour approfondir les recherches](investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Ajouter des signets à un incident nouveau ou existant

1. Dans le portail Azure, accédez à **Azure Sentinel** > **Gestion des menaces** > **Chasse** > **Signets**, puis sélectionnez le(s) signet(s) que vous souhaitez ajouter à un incident.

2. Sélectionnez **Actions d’incident** dans la barre de commandes :

    :::image type="content" source="media/bookmarks/incident-actions.png" alt-text="Capture d’écran de l’ajout de signets à un incident.":::

3. Sélectionnez **Créer un incident** ou **Ajouter à un incident existant**, selon le cas. Ensuite :
    
    - Pour un nouvel incident : Vous pouvez également mettre à jour les détails de l'incident, puis sélectionner **Créer**.
    - Pour ajouter un signet à un incident existant : Sélectionnez un incident, puis **Ajouter**. 

Pour afficher le signet dans l’incident, accédez à **Azure Sentinel** > **Gestion des menaces** > **Incidents**, puis sélectionnez l’incident avec votre signet. Sélectionnez **Afficher les détails**, puis l'onglet **Signets**.

> [!TIP]
> En guise d’alternative à l’option **Actions d’incident** dans la barre de commandes, vous pouvez utiliser le menu contextuel ( **...** ) pour un ou plusieurs signets afin de sélectionner des options pour **Créer un incident**, **Ajouter à un incident existant** et **Supprimer de l’incident**. 

## <a name="view-bookmarked-data-in-logs"></a>Afficher les données avec signet dans les journaux

Pour afficher les requêtes avec signet, les résultats ou l’historique, sélectionnez le signet dans l'onglet **Repérage** > **Signets** et utilisez les liens fournis dans le volet de détails : 

- Utilisez **Afficher la requête source** pour afficher la requête source dans le volet **Journaux**.

- **Afficher les journaux des signets** pour afficher toutes les métadonnées du signet, notamment l’auteur de la mise à jour, les valeurs mises à jour et l’heure de la mise à jour.

Vous pouvez également afficher les données de signets brutes pour tous les signets en sélectionnant **Journaux de signets** dans le barre de commandes de l'onglet **Repérage** > **Signets** :

:::image type="content" source="media/bookmarks/bookmark-logs.png" alt-text="Capture d’écran de la commande des journaux de signets.":::

Cet affichage montre tous vos signets avec les métadonnées associées. Vous pouvez utiliser des requêtes [Kusto Query Language](/azure/data-explorer/kql-quick-reference) (KQL) pour filtrer jusqu’à la dernière version du signet spécifique que vous recherchez.

> [!NOTE]
> Il peut y avoir un délai important (mesuré en minutes) entre la création d’un signet et le moment où il apparaît dans l'onglet **Signets**.

## <a name="delete-a-bookmark"></a>Supprimer un signet
 
1.  Dans le portail Azure, accédez à **Azure Sentinel** > **Gestion des menaces** > **Chasse** > **Signets**, puis sélectionnez le(s) signet(s) que vous souhaitez supprimer. 

2. Cliquez avec le bouton droit sur vos sélections, puis sélectionnez l’option permettant de supprimer le nombre de signets que vous avez sélectionnés.
    
La suppression du signet supprime le signet de la liste dans l’onglet **Signet**. La table **HuntingBookmark** pour votre espace de travail Log Analytics contient toujours les précédentes entrées de signet, mais la valeur de la dernière entrée pour **SoftDelete** devient true, ce qui vous permet de filtrer aisément les anciens signets. La suppression d’un signet ne supprime pas les entités de l’expérience d’examen qui sont associées à d’autres alertes ou signets. 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à exécuter un examen de repérage à l’aide de signets dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :


- [Hunt for threats with in Azure Sentinel Preview](hunting.md) (Rechercher des menaces dans Azure Sentinel en préversion)
- [Use notebooks to run automated hunting campaigns](notebooks.md) (Utiliser des notebooks pour exécuter des campagnes de repérage automatisées)
- [Chasser les menaces avec Azure Sentinel (Module Learn)](/learn/modules/hunt-threats-sentinel/)
