---
title: Utiliser Azure Digital Twins Explorer
titleSuffix: Azure Digital Twins
description: Comprendre comment utiliser les fonctionnalités d’Azure Digital Twins Explorer
author: baanders
ms.author: baanders
ms.date: 10/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 087af92ce70ccb8db37073b6aadb8cf9b83bc57c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454282"
---
# <a name="use-azure-digital-twins-explorer-preview"></a>Utiliser Azure Digital Twins Explorer (préversion)

[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) est un outil permettant de visualiser et de travailler avec Azure Digital Twins. Cet article décrit les fonctionnalités d’Azure Digital Twins Explorer et explique comment les utiliser pour gérer les données dans votre instance Azure Digital Twins. Vous pouvez interagir avec Azure Digital Twins Explorer à l’aide de clics ou de [raccourcis clavier](#accessibility-and-advanced-settings).

>[!NOTE]
>Cet outil est actuellement en **préversion publique**.

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

### <a name="switch-contexts-within-the-app"></a>Changer de contexte dans l’application

Une fois dans l’application, vous avez aussi la possibilité de changer l’instance actuellement connectée à l’explorateur, en sélectionnant le nom de l’instance dans la barre d’outils du haut.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/instance-url-1.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. Le nom de l’instance dans la barre d’outils du haut est mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/instance-url-1.png":::

Ceci va faire apparaître la boîte de dialogue modale **URL d’Azure Digital Twins**, où vous pouvez entrer le nom d’hôte d’une autre instance Azure Digital Twins après *https://* pour vous connecter à cette instance.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/instance-url-2.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. La boîte de dialogue modale URL d’Azure Digital Twins affiche une zone modifiable contenant https:// et un nom d’hôte." lightbox="media/how-to-use-azure-digital-twins-explorer/instance-url-2.png":::

>[!NOTE]
>Pour le moment, la possibilité de changer de contexte dans l’application n’est **pas** disponible pour les comptes Microsoft personnels (MSA). Les utilisateurs de compte MSA devront accéder à l’explorateur à partir de l’instance appropriée dans le portail Azure ou se connecter à une instance spécifique via un [lien direct vers l’environnement](#link-to-your-environment).


## <a name="query-your-digital-twin-graph"></a>Interroger votre graphe des jumeaux numériques

Vous pouvez utiliser le panneau **Explorateur de requêtes** pour exécuter des [requêtes](concepts-query-language.md) sur votre graphe.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. Le panneau Explorateur de requêtes est mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel.png":::

Entrez la requête que vous voulez exécuter, puis sélectionnez le bouton **Exécuter la requête**. Ceci va charger les résultats de la requête dans le panneau **Graphe des jumeaux**.

>[!NOTE]
> Les résultats de la requête contenant des relations peuvent être restitués dans le panneau **Graphe des jumeaux** seulement si les résultats incluent également au moins un jumeau. Si les requêtes retournant seulement des relations sont possibles dans Azure Digital Twins, vous pouvez les visualiser seulement dans Azure Digital Twins Explorer en utilisant le [panneau Sortie](#accessibility-and-advanced-settings).

### <a name="overlay-query-results"></a>Superposer les résultats des requêtes

Vous pouvez cocher la case **Superposer les résultats** avant d’exécuter votre requête si vous voulez que les résultats soient mis en évidence par rapport à ce qui est actuellement affiché dans le panneau **Graphe des jumeaux**, au lieu de remplacer complètement le contenu du panneau par les résultats de la nouvelle requête.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-overlay-results.png" alt-text="Capture d’écran du panneau Explorateur de requêtes d’Azure Digital Twins Explorer. La case Superposer les résultats est cochée et deux jumeaux sont mis en évidence dans le graphe plus grand affiché dans le panneau Graphe des jumeaux." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-overlay-results.png":::

Si le résultat de la requête inclut quelque chose qui n’est pas actuellement affiché dans le panneau **Graphe des jumeaux**, l’élément est ajouté dans la vue existante.

### <a name="save-and-rerun-queries"></a>Enregistrer et réexécuter des requêtes

Les requêtes peuvent être enregistrées dans le stockage local de votre navigateur, ce qui permet de les sélectionner et de les réexécuter facilement.

>[!NOTE]
>Le stockage local du navigateur signifie que les requêtes enregistrées ne seront pas disponibles dans les navigateurs autres que celui où vous les avez enregistrées, et qu’elles resteront indéfiniment dans le stockage du navigateur jusqu’à ce que le stockage local soit effacé.

Pour enregistrer une requête, entrez-la dans la zone de requête et sélectionnez l’icône **Enregistrer** à droite du panneau. Entrez un nom pour la requête enregistrée quand vous y êtes invité.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-save.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. L’icône Enregistrer est mise en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-save.png":::

Une fois la requête enregistrée, elle peut être sélectionnée dans le menu déroulant **Requêtes enregistrées**, ce qui permet de la réexécuter facilement.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-saved-queries.png" alt-text="Capture d’écran du panneau Azure Digital Twins Explorer. Le menu déroulant Requêtes enregistrées est mis en évidence et montre deux exemples de requêtes." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-saved-queries.png":::

Pour supprimer une requête enregistrée, sélectionnez l’icône **X** en regard du nom de la requête avec le menu déroulant **Requêtes enregistrées** ouvert.

>[!TIP]
>Pour les grands graphes, il est suggéré d’interroger seulement un sous-ensemble limité, puis de charger le reste du graphe en fonction des besoins. Vous pouvez double-cliquer sur un jumeau dans le panneau **Graphe des jumeaux** pour récupérer des nœuds connexes supplémentaires.

## <a name="explore-the-twin-graph"></a>Explorer le graphe des jumeaux

Le panneau **Graphe des jumeaux** vous permet d’explorer les jumeaux et les relations de votre instance.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. Le panneau Graphe des jumeaux est mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel.png":::

Vous pouvez utiliser ce panneau pour [visualiser vos jumeaux et vos relations](#view-twins-and-relationships).

Le panneau Graphe des jumeaux offre également plusieurs possibilités de personnalisation de l’expérience de visualisation de votre graphe :
* [Modifier la disposition du graphe des jumeaux](#edit-twin-graph-layout)
* [Contrôler l’expansion du graphe des jumeaux](#control-twin-graph-expansion)
* [Afficher et masquer les éléments du graphe des jumeaux](#show-and-hide-twin-graph-elements)
* [Filtrer et mettre en évidence des éléments du graphe des jumeaux](#filter-and-highlight-twin-graph-elements)

### <a name="view-twins-and-relationships"></a>Visualiser des jumeaux et des relations

Exécutez une requête en utilisant l’[Explorateur de requêtes](#query-your-digital-twin-graph) pour voir les jumeaux et les relations dans le résultat de la requête affiché dans le panneau **Graphe des jumeaux**.

>[!TIP]
>La requête pour afficher toutes les jumeaux et les relations est `SELECT * FROM digitaltwins`.

#### <a name="view-twin-and-relationship-properties"></a>Afficher les propriétés des jumeaux et des relations

Pour afficher les valeurs des propriétés d’un jumeau ou d’une relation, sélectionnez le jumeau ou la relation dans le **Graphe des jumeaux** et utilisez le bouton **Passer à l’inspecteur de propriétés** pour développer le panneau **Propriétés du jumeau** ou le panneau **Propriétés de la relation**. Ce panneau va afficher toutes les propriétés associées à l’élément ainsi que leurs valeurs. Il montre aussi les valeurs par défaut pour les propriétés qui n’ont pas encore été définies.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le jumeau FactoryA est sélectionné et le panneau Propriétés du jumeau est développé, montrant les propriétés du jumeau." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png":::

##### <a name="data-type-icons"></a>Icônes de type de données

Les propriétés affichées dans les panneaux **Propriétés du jumeau** et **Propriétés de la relation** sont affichées avec une icône, indiquant le type du champ du modèle DTDL. Vous pouvez pointer sur une icône pour afficher le type associé.

Le tableau ci-dessous présente les types de données possibles et leurs icônes correspondantes. La table contient également des liens de chaque type de données vers sa description de schéma dans la [spécification DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

| Icône | Type de données |
| --- | --- |
| ![icône de booléen](./media/how-to-use-azure-digital-twins-explorer/data-icons/boolean.svg) | [boolean](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![icône de composant](./media/how-to-use-azure-digital-twins-explorer/data-icons/component.svg) | [component](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) |
| ![icône de date](./media/how-to-use-azure-digital-twins-explorer/data-icons/date.svg) | [date](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![icône de dateHeure](./media/how-to-use-azure-digital-twins-explorer/data-icons/datetime.svg) | [dateTime](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![icône de durée](./media/how-to-use-azure-digital-twins-explorer/data-icons/duration.svg) | [duration](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![icône d’énumération](./media/how-to-use-azure-digital-twins-explorer/data-icons/enum.svg) | [enum](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#enum) |
| ![icône de carte](./media/how-to-use-azure-digital-twins-explorer/data-icons/map.svg) | [map](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#map) |
| ![icône de numérique](./media/how-to-use-azure-digital-twins-explorer/data-icons/numeric.svg) | Types numériques, dont [double, float, integer et long](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![Icône d’objet](./media/how-to-use-azure-digital-twins-explorer/data-icons/object.svg) | [object](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#object) |
| ![icône de chaîne](./media/how-to-use-azure-digital-twins-explorer/data-icons/string.svg) | [string](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![icône d’heure](./media/how-to-use-azure-digital-twins-explorer/data-icons/time.svg) | [time](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |

##### <a name="errors"></a>Errors

Le volet Propriétés du jumeau affiche des messages d’erreur si le jumeau ou certaines de ses propriétés ne correspondent plus à son modèle. 

Il existe deux scénarios d’erreur possibles qui donnent chacun leur propre message d’erreur :
* **L’un des nombreux modèles que le jumeau utilise est manquant**. Par conséquent, toutes les propriétés associées à ce modèle sont marquées comme « manquantes » dans le panneau Propriétés du jumeau. Cela peut se produire si le modèle a été supprimé depuis la création du jumeau.
* **Certaines propriétés sur le jumeau ne font pas partie du modèle du jumeau.** Seules ces propriétés sont signalées comme « manquantes » dans le panneau Propriétés du jumeau. Ceci peut se produire si le modèle pour le jumeau a été remplacé ou modifié depuis la définition des propriétés, et si les propriétés n’existent plus dans la version la plus récente du modèle.

Les deux messages d’erreur sont affichés dans la capture d’écran ci-dessous :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/properties-errors.png" alt-text="Capture d’écran du panneau des propriétés du jumeau Azure Digital Twins Explorer, montrant deux messages d’erreur. Une erreur indique que des modèles sont manquants, et l’autre qu’un modèle est manquant pour les propriétés. " lightbox="media/how-to-use-azure-digital-twins-explorer/properties-errors.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="view-a-twins-relationships"></a>Visualiser les relations d’un jumeau

Vous pouvez aussi visualiser rapidement le code de toutes les relations qui impliquent un certaine jumeau (y compris les relations entrantes et sortantes).

Pour cela, cliquez avec le bouton droit sur un jumeau dans le graphe, puis choisissez **Obtenir les relations**. Ceci affiche une boîte de dialogue modale **Informations sur les relations** montrant la [représentation JSON](concepts-twins-graph.md#relationship-json-format) de toutes les relations entrantes et sortantes.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-get-relationships.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le centre de l’écran contient une boîte de dialogue modale montrant les relations entrantes et sortantes." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-get-relationships.png":::

### <a name="edit-twin-graph-layout"></a>Modifier la disposition du graphe des jumeaux

Vous pouvez réorganiser les jumeaux selon différentes configurations en cliquant sur ceux-ci et en les faisant glisser dans l’écran Graphe des jumeaux.

Vous pouvez également appliquer un des nombreux algorithmes de disposition au graph à partir des options du menu **Choisir une disposition**. 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-layout.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le bouton Choisir une disposition est mis en évidence et montre un menu avec les options de disposition Cola, Dagre, fCoSE et Klay." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-layout.png":::

### <a name="control-twin-graph-expansion"></a>Contrôler l’expansion du graphe des jumeaux

Quand vous visualisez le résultat d’une requête dans le panneau **Graphe des jumeaux**, vous pouvez double-cliquer sur un jumeau pour que le graphe récupère ses relations et les jumeaux associés, puis les affiche s’ils ne sont pas déjà présents dans la vue. Vous pouvez personnaliser cette expansion en définissant une taille et une direction pour déterminer le nombre de jumeaux à récupérer.

Pour définir le nombre de couches à développer, utilisez l’option **Niveau d’expansion**. Ce nombre indique le nombre de couches de relations à récupérer pour le jumeau sélectionné.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-level.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le bouton Niveau d’expansion est mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-level.png":::

Pour indiquer les types de relations à suivre lors de l’expansion, utilisez le bouton **Direction d’expansion**. Ceci vous permet de choisir entre seulement les relations entrantes, seulement les relations sortantes, ou à la fois les relations entrantes et sortantes.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-direction.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le bouton Direction d’expansion est mis en évidence et montre un menu avec les options Entrantes, Sortantes et Entrantes/Sortantes." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-direction.png":::

### <a name="show-and-hide-twin-graph-elements"></a>Afficher et masquer les éléments du graphe des jumeaux

Vous pouvez activer ou désactiver l’option pour masquer des jumeaux ou des relations dans la vue du graphe. 

Pour masquer un jumeau ou une relation, cliquez dessus avec le bouton droit dans la fenêtre de **Graphe des jumeaux**. Ceci fait apparaître un menu avec des options pour masquer l’élément ou d’autres éléments associés.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-hide.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le jumeau FactoryA est sélectionné et il y a un menu contenant les options Masquer les éléments sélectionnés, Masquer les éléments sélectionnés + les enfants, Masquer tous les autres et Masquer les éléments non enfants." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-hide.png":::

Vous pouvez aussi masquer plusieurs jumeaux ou relations à la fois en utilisant les touches Ctrl/Cmd ou Maj pour sélectionner plusieurs éléments du même type dans le graphe. À partir de là, suivez le même processus de clic avec le bouton droit pour voir les options de masquage.

Pour revenir à l’affichage de tous les jumeaux après masquage, utilisez le bouton **Afficher tout**. Pour revenir à l’affichage de toutes les relations, utilisez le bouton **Afficher toutes les relations**.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-show.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Les boutons Afficher tout et Afficher toutes les relations sont mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-show.png":::

### <a name="filter-and-highlight-twin-graph-elements"></a>Filtrer et mettre en évidence des éléments du graphe des jumeaux

Vous pouvez filtrer les jumeaux et les relations qui apparaissent dans le graphe en fonction du texte, en sélectionnant cette icône **Filtrer** :

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-filter-text.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. L’icône de filtre de texte est sélectionnée et montre l’onglet Filtrer où vous pouvez entrer un terme à rechercher." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-filter-text.png":::

Vous pouvez aussi mettre en surbrillance les jumeaux et les relations qui apparaissent dans le graphe en fonction du texte en sélectionnant cette icône **Mettre en surbrillance** :

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-text.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. L’icône de filtre de texte est sélectionnée et montre l’onglet Mettre en surbrillance où vous pouvez entrer un terme à rechercher." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-text.png":::

## <a name="manage-twins-and-graph"></a>Gérer les jumeaux et le graphe

Azure Digital Twins Explorer offre plusieurs moyens de gérer les [jumeaux](concepts-twins-graph.md#digital-twins) et les [relations](concepts-twins-graph.md#relationships-a-graph-of-digital-twins) dans votre instance.

Cette section décrit comment effectuer les activités de gestion suivantes :
* [Afficher la liste plate des jumeaux et des relations](#view-flat-list-of-twins-and-relationships)
* [Créer des jumeaux](#create-twins), avec ou sans propriétés initiales
* [Créer des relations](#create-relationships) entre des jumeaux
* [Modifier les propriétés du jumeau et de la relation](#edit-twin-and-relationship-properties)
* [Supprimer des jumeaux et des relations](#delete-twins-and-relationships)

Pour plus d’informations sur l’expérience de visualisation pour les jumeaux et les relations, consultez [Explorer les jumeaux et le graphique des jumeaux](#explore-the-twin-graph).

### <a name="view-flat-list-of-twins-and-relationships"></a>Afficher la liste plate des jumeaux et des relations

Le volet **Jumeaux** affiche une liste plate de vos jumeaux et leurs relations associées. Vous pouvez rechercher des jumeaux par nom et les développer pour obtenir des détails sur leurs relations entrantes et sortantes.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twins-panel.png" alt-text="Capture d’écran du panneau Jumeaux d’Azure Digital Twins Explorer. Un jumeau est mis en surbrillance et ses relations sont affichées." lightbox="media/how-to-use-azure-digital-twins-explorer/twins-panel.png":::

### <a name="create-twins"></a>Créer des jumeaux

Vous pouvez créer un jumeau numérique à partir de sa définition de modèle dans le panneau **Modèles**.

Pour créer un jumeau à partir d’un modèle, recherchez ce modèle dans la liste et sélectionnez les points de menu en regard du nom du modèle. Sélectionnez ensuite **Créer un jumeau**. Vous êtes invité à entrer un **nom** pour le nouveau jumeau ; ce nom doit être unique. Enregistrez ensuite le jumeau, qui sera alors ajouté à votre graphe.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-create-a-twin.png" alt-text="Capture d’écran du panneau Modèles d’Azure Digital Twins Explorer. Les points de menu d’un modèle unique sont mis en surbrillance, et l’option de menu permettant de créer un jumeau est également mise en surbrillance." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-create-a-twin.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Pour ajouter des valeurs de propriété à votre jumeau, consultez [Modifier les propriétés du jumeau et de la relation](#edit-twin-and-relationship-properties).

### <a name="create-relationships"></a>Créer des relations

Pour créer une relation entre deux jumeaux, commencez par sélectionner le jumeau source pour la relation dans la fenêtre **Graphe des jumeaux**. Ensuite, maintenez la touche Ctrl/Cmd ou Maj enfoncée tout en sélectionnant un deuxième jumeau comme cible de la relation.

Une fois les deux jumeaux sélectionnés, cliquez avec le bouton droit sur le jumeau cible. Ceci fait apparaître un menu avec une option pour **Ajouter des relations** entre ces jumeaux.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-add-relationship.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Les jumeaux FactoryA et Consumer sont sélectionnés et un menu montre l’option permettant d’ajouter des relations." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-add-relationship.png":::

Ceci fait apparaître la boîte de dialogue **Créer une relation**, qui montre le jumeau source et le jumeau cible de la relation, suivie d’un menu déroulant **Relation** qui contient les types de relation que peut avoir le jumeau source (définis dans son modèle DTDL). Sélectionnez une option pour le type de relation, puis **enregistrez** la nouvelle relation.

### <a name="edit-twin-and-relationship-properties"></a>Modifier les propriétés du jumeau et de la relation

Pour afficher les valeurs des propriétés d’un jumeau ou d’une relation, sélectionnez l’élément dans le **Graphe des jumeaux** et utilisez le bouton **Passer à l’inspecteur de propriétés** pour développer le panneau **Propriétés du jumeau** ou le panneau **Propriétés de la relation**.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le jumeau FactoryA est sélectionné et le panneau Propriétés du jumeau est développé, montrant les propriétés du jumeau." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png":::

Vous pouvez utiliser ce panneau pour modifier directement les propriétés accessibles en écriture. Mettez à jour leurs valeurs incluses, puis sélectionnez le bouton **Enregistrer les modifications** en haut du panneau pour enregistrer. Quand la mise à jour est enregistrée, l’écran affiche une fenêtre modale montrant l’opération de correction JSON qui a été appliquée par l’[API de mise à jour](/rest/api/azure-digitaltwins/).

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties-save.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le centre de l’écran contient une boîte de dialogue modale Informations de correction montrant le code du correctif JSON." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties-save.png":::

>[!TIP]
> Les propriétés affichées dans les panneaux **Propriétés du jumeau** et **Propriétés de la relation** sont affichées avec une icône, indiquant le type du champ du modèle DTDL. Pour plus d’informations sur les icônes de type, consultez [Icônes de type de données](#data-type-icons).

### <a name="delete-twins-and-relationships"></a>Supprimer des jumeaux et des relations

Pour supprimer un jumeau ou une relation, cliquez dessus avec le bouton droit dans la fenêtre de **Graphe des jumeaux**. Un menu apparaît avec une option permettant de supprimer l’élément.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-delete.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le jumeau FactoryA est sélectionné et un menu contient une option permettant de supprimer un ou plusieurs jumeaux." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-delete.png":::

Vous pouvez supprimer plusieurs jumeaux ou relations à la fois en utilisant les touches Ctrl/Cmd ou Maj pour sélectionner plusieurs éléments du même type dans le graphe. À partir de là, suivez le même processus de clic avec le bouton droit pour supprimer les éléments.

Vous pouvez aussi choisir de supprimer en même temps tous les jumeaux de votre instance, en utilisant le bouton **Supprimer tous les jumeaux** dans la barre d’outils du haut.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/delete-all-twins.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. L’icône Supprimer tous les jumeaux est sélectionnée." lightbox="media/how-to-use-azure-digital-twins-explorer/delete-all-twins.png":::

## <a name="explore-models-and-the-model-graph"></a>Explorer les modèles et le graphe du modèle

Les modèles peuvent être visualisés à la fois dans le panneau **Modèles** sur le côté gauche de l’écran d’Azure Digital Twins Explorer et dans le panneau **Graphe des modèles** au milieu de l’écran.

Le panneau **Modèles** : :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. Le panneau Modèles est mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel.png":::

Le panneau **Graphe des modèles** : :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. Le panneau Graphe des modèles est mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

Vous pouvez utiliser ces panneaux pour [visualiser vos modèles](#view-models).

Le panneau Graphe des modèles offre également plusieurs possibilités de personnalisation de l’expérience de visualisation de votre graphe :
* [Modifier la disposition du graphe du modèle](#edit-model-graph-layout)
* [Filtrer et mettre en évidence des éléments du graphe du modèle](#filter-and-highlight-model-graph-elements)
* [Charger des images de modèle](#upload-model-images) pour représenter des modèles dans les graphes

### <a name="view-models"></a>Afficher les modèles

Vous pouvez visualiser une simple liste des modèles de votre instance dans le panneau **Modèles**. Vous pouvez faire des recherches dans cette liste en utilisant la barre **Rechercher** du panneau.

Vous pouvez utiliser le panneau **Graphe des modèles** pour visualiser une représentation graphique des modèles de votre instance ainsi que les relations, l’héritage et les composants qui les connectent les uns aux autres.

#### <a name="view-model-definition"></a>Visualiser la définition d’un modèle

Pour voir la définition complète d’un modèle, recherchez ce modèle dans le volet **Modèles**, puis sélectionnez les points de menu en regard du nom du modèle. Puis sélectionnez **Afficher le modèle**. Ceci va afficher une boîte de dialogue modale **Informations sur le modèle** montrant la définition DTDL brute du modèle.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-view.png" alt-text="Capture d’écran du panneau Modèles d’Azure Digital Twins Explorer. Les points de menu d’un modèle unique sont mis en surbrillance, et l’option de menu permettant d’afficher le modèle est également mise en surbrillance." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-view.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Vous pouvez aussi voir la définition complète d’un modèle en le sélectionnant dans le **Graphe des modèles** et en utilisant le bouton **Afficher/masquer les détails du modèle** pour développer le panneau **Détails du modèle**. Ce panneau affiche également le code DTDL complet pour le modèle.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-graph-details.png" alt-text="Capture d’écran du panneau Graphe des modèles d’Azure Digital Twins Explorer. Le modèle Floor est sélectionné et le panneau Détails du modèle est développé, montrant le code DTDL du modèle." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-graph-details.png":::

### <a name="edit-model-graph-layout"></a>Modifier la disposition du graphe du modèle

Vous pouvez réorganiser les modèles selon différentes configurations en cliquant sur ceux-ci et en les faisant glisser dans l’écran Graphe des modèles.

Vous pouvez aussi appliquer un des nombreux algorithmes de mise en page au graphe du modèle à partir des options du menu **Exécuter une disposition**. 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-layout.png" alt-text="Capture d’écran du panneau Graphe des modèles d’Azure Digital Twins Explorer. Le bouton Exécuter une disposition est mis en évidence et montre un menu avec les options de disposition Cola, Dagre, fCoSE, Klay et d3Force." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-layout.png":::

### <a name="filter-and-highlight-model-graph-elements"></a>Filtrer et mettre en évidence des éléments du graphe du modèle

Vous pouvez filtrer les types de connexions qui apparaissent dans le Graphe des modèles. La désactivation d’un des types de connexion via les commutateurs de ce menu va supprimer l’affichage de ce type de connexion dans le graphe.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-connections.png" alt-text="Capture d’écran du panneau Graphique du modèle d’Azure Digital Twins Explorer. Le menu Filtrer pour Relations, Héritage et Composants est mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-connections.png":::

Vous pouvez également filtrer les modèles et les connexions qui apparaissent dans le graphe en fonction du texte en sélectionnant cette icône **Filtrer** :

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-text.png" alt-text="Capture d’écran du panneau Graphe des modèles d’Azure Digital Twins Explorer. L’icône de filtre de texte est sélectionnée et montre l’onglet Filtrer où vous pouvez entrer un terme à rechercher." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-text.png":::

Vous pouvez mettre en surbrillance les modèles et les connexions qui apparaissent dans le graphe en fonction du texte en sélectionnant cette icône **Mettre en surbrillance** :

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-text.png" alt-text="Capture d’écran du panneau Graphe des modèles d’Azure Digital Twins Explorer. L’icône de filtre de texte est sélectionnée et montre l’onglet Mettre en surbrillance où vous pouvez entrer un terme à rechercher." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-text.png":::

### <a name="upload-model-images"></a>Charger des images de modèle

Vous pouvez charger des images personnalisées pour représenter différents modèles dans le Graphe des modèles et dans les vues du [Graphe des jumeaux](#explore-the-twin-graph). Vous pouvez charger des images pour des modèles individuels ou pour plusieurs modèles à la fois.

>[!NOTE]
>Ces images sont stockées dans le stockage local du navigateur. Cela signifie que les images ne seront pas disponibles dans les navigateurs autres que celui où vous les avez enregistrées, et qu’elles resteront indéfiniment dans le stockage du navigateur jusqu’à ce que le stockage local soit effacé.

Pour charger une image pour un seul modèle, recherchez ce modèle dans le panneau **Modèles**, puis sélectionnez les points de menu en regard du nom du modèle. Ensuite, sélectionnez **Charger l’image du modèle**. Dans la zone de sélection de fichier qui apparaît, accédez sur votre machine au fichier image que vous voulez charger pour ce modèle. Choisissez **Ouvrir** pour le charger.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-one-image.png" alt-text="Capture d’écran du panneau Modèles d’Azure Digital Twins Explorer. Les points de menu d’un modèle unique sont mis en surbrillance, et l’option de menu permettant de charger l’image du modèle est également mise en surbrillance." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-one-image.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Vous pouvez aussi charger des images de modèle en bloc.

Tout d’abord, suivez les instructions ci-dessous pour définir les noms de fichiers image avant le chargement. Ceci permet à Azure Digital Twins Explorer d’affecter automatiquement les images aux modèles appropriés après le chargement. 
1. Commencez par la valeur de l’ID du modèle (par exemple `dtmi:example:Floor;1`)
1. Remplacez les instances de « : » par « _ » (l’exemple devient `dtmi_example_Floor;1`)
1. Remplacez les instances de « ; » par « - » (l’exemple devient `dtmi_example_Floor-1`)
1. Vérifiez que le fichier a une extension d’image (l’exemple devient alors similaire à `dtmi_example_Floor-1.png`)

> [!NOTE]
> Si vous essayez de charger une image qui ne correspond à aucun modèle existant avec les critères ci-dessus, l’image n’est pas chargée ni stockée.

Ensuite, pour charger les images en même temps, utilisez l’icône **Charger les images de modèle** en haut du panneau Modèles. Dans la zone de sélection de fichiers, choisissez les fichiers image à charger. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-images.png" alt-text="Capture d’écran du panneau Modèles d’Azure Digital Twins Explorer. L’icône Charger les images de modèle est mise en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-images.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-models"></a>Gérer les modèles

Vous pouvez utiliser le panneau **Modèles** sur le côté gauche de l’écran d’Azure Digital Twins Explorer pour effectuer des activités de gestion sur l’ensemble des modèles ou sur des modèles individuels. 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. Le panneau Modèles est mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel.png":::

Avec ce panneau, vous pouvez effectuer les activités de gestion des modèles suivantes :
* [Charger des modèles](#upload-models) dans l’instance Azure Digital Twins
* [Supprimer des modèles](#delete-models) de votre instance
* [Actualiser les modèles](#refresh-models) pour recharger la liste de tous les modèles dans ce panneau

Pour plus d’informations sur l’expérience de visualisation des modèles, consultez [Explorer les modèles et le Graphe des modèles](#explore-models-and-the-model-graph).

### <a name="upload-models"></a>Charger des modèles

Vous pouvez charger des modèles depuis votre machine en les sélectionnant individuellement ou en chargeant un dossier entier de modèles en une seule fois.

Pour télécharger un ou plusieurs modèles qui sont sélectionnés individuellement, sélectionnez l’icône **Charger un modèle** qui représente une flèche pointant vers le haut.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload.png" alt-text="Capture d’écran du panneau Modèles d’Azure Digital Twins Explorer. L’icône Charger un modèle est mise en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Dans la zone de sélection de fichier qui apparaît, accédez sur votre machine aux modèles que vous voulez charger. Vous pouvez sélectionner un ou plusieurs fichiers de modèle JSON, puis sélectionnez **Ouvrir** pour les charger.

Pour charger un dossier de modèles, avec tout ce qui se trouve dans celui-ci, sélectionnez l’icône **Charger un répertoire de modèles**, qui représente un dossier de fichiers.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-directory.png" alt-text="Capture d’écran du panneau Modèles d’Azure Digital Twins Explorer. L’icône Charger un répertoire de modèles est mise en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-directory.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Dans la zone de sélection de fichier qui apparaît, accédez sur votre machine à un dossier contenant des fichiers de modèle JSON. Sélectionnez **Ouvrir** pour charger ce dossier de plus haut niveau et tout son contenu.

>[!IMPORTANT]
>Si un modèle référence un autre modèle dans sa définition, comme quand vous définissez des relations ou des composants, le modèle référencé doit être présent dans l’instance pour pouvoir charger le modèle qui l’utilise. Si vous chargez des modèles un par un, cela signifie que vous devez charger le modèle référencé **avant** de charger les modèles qui l’utilisent. Si vous chargez des modèles en bloc, vous pouvez les sélectionner dans la même importation : Azure Digital Twins va alors inférer la commande nécessaire pour les charger.

### <a name="delete-models"></a>Supprimer des modèles

Vous pouvez utiliser le panneau Modèles pour supprimer des modèles individuels ou tous les modèles de votre instance à la fois.

Pour supprimer un modèle unique, recherchez ce modèle dans la liste et sélectionnez les points de menu en regard du nom du modèle. Ensuite, sélectionnez **Supprimer le modèle**.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-one.png" alt-text="Capture d’écran du panneau Modèles d’Azure Digital Twins Explorer. Les points de menu d’un modèle unique sont mis en surbrillance, et l’option de menu permettant de supprimer le modèle est également mise en surbrillance." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-one.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Pour supprimer tous les modèles à la fois dans votre instance, choisissez l’icône **Supprimer tous les modèles** en haut du panneau Modèles.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-all.png" alt-text="Capture d’écran du panneau Modèles d’Azure Digital Twins Explorer. L’icône Supprimer tous les modèles est mise en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-all.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="refresh-models"></a>Actualiser les modèles

Quand vous ouvrez Azure Digital Twins Explorer, le panneau Modèles doit montrer automatiquement tous les modèles disponibles dans votre environnement. 

Cependant, vous pouvez actualiser manuellement le panneau à tout moment pour recharger la liste de tous les modèles dans votre instance Azure Digital Twins. Pour ce faire, sélectionnez l’icône **Actualiser les modèles**. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-refresh.png" alt-text="Capture d’écran du panneau Modèles d’Azure Digital Twins Explorer. L’icône Actualiser les modèles est mise en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-refresh.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="importexport-graph"></a>Importer/exporter un graphe

Dans le panneau **Graphe des jumeaux**, vous disposez d’options permettant d’[importer](#import-graph) et d’[exporter](#export-graph-and-models) un graphe.

### <a name="import-graph"></a>Importer un graphe

Vous pouvez utiliser la fonctionnalité d’importation pour ajouter des jumeaux, des relations et des modèles à votre instance. Ceci peut être utile pour créer plusieurs jumeaux, relations et/ou modèles à la fois.

#### <a name="create-import-file"></a>Créer un fichier d’importation

La première étape de l’importation d’un graphe est de créer un fichier représentant les jumeaux et les relations que vous voulez ajouter.

Le fichier d’importation peut être dans un de ces deux formats :
* Le **format personnalisé basé sur Excel**, décrit dans le reste de cette section. Ceci vous permet de charger des jumeaux et des relations.
* Le **format basé sur JSON** généré lors de l’[exportation d’un graphe](#export-graph-and-models). Ceci peut contenir des jumeaux, des relations et/ou des modèles.

Pour créer un graphe personnalisé dans Excel, utilisez le format suivant.

Chaque ligne représente un élément à créer : un jumeau, une relation, ou une combinaison d’un jumeau et de la relation correspondante.
Utilisez les colonnes suivantes pour structurer les données des jumeaux ou des relations. Les noms des colonnes peuvent être personnalisés, mais ils doivent rester dans cet ordre.

| ModelID | id | Relation (source) | Nom de la relation | Données d’initialisation |
| --- | --- | --- | --- | --- |
| *Facultatif*<br>ID de modèle DTMI pour un jumeau qui doit être créé.<br><br>Vous pouvez laisser cette colonne vide pour une ligne si vous voulez que cette ligne crée seulement une relation (pas de jumeaux). | *Obligatoire*<br>ID unique pour un jumeau.<br><br>Si un nouveau jumeau est créée dans cette ligne, il s’agit de l’ID du nouveau jumeau.<br>Si la ligne contient des informations sur une relation, cet ID est utilisé comme **cible** de la relation. | *Facultatif*<br>ID d’un jumeau qui doit être le jumeau **source** d’une nouvelle relation.<br><br>Vous pouvez laisser cette colonne vide pour une ligne si vous voulez que cette ligne crée seulement un jumeau (pas de relations). | *Facultatif*<br>Nom de la nouvelle relation à créer. La direction de la relation sera **du** jumeau de la colonne C **au** jumeau de la colonne B. | *Facultatif*<br>Chaîne JSON contenant les valeurs des propriétés pour le jumeau à créer. Les propriétés doivent correspondre à celles définies dans le modèle de la colonne A. |

Voici un exemple de fichier .xlsx qui crée un petit graphe de deux étages et de deux pièces.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/import-example.png" alt-text="Capture d’écran des données du graphe dans Excel. Les en-têtes de colonnes correspondent aux champs ci-dessus, dans l’ordre, et les lignes contiennent les valeurs de données correspondantes." lightbox="media/how-to-use-azure-digital-twins-explorer/import-example.png":::

Vous pouvez visualiser ce fichier et d’autres exemples de graphe .xlsx dans le [dépôt Azure Digital Twins Explorer](https://github.com/Azure-Samples/digital-twins-explorer/tree/main/client/examples) sur GitHub.

>[!NOTE]
>Les propriétés et les relations décrites dans le .xlsx doivent correspondre à ce qui est défini dans les définitions du modèle pour les jumeaux concernés.

#### <a name="import-file-to-azure-digital-twins-explorer"></a>Importer un fichier dans Azure Digital Twins Explorer

Une fois que vous avez un fichier sur votre machine prêt à être importé, sélectionnez l’icône **Importer un graphe** dans le panneau Graphe des jumeaux.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le bouton Importer un graphe est mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import.png":::

Dans la zone de sélection de fichier qui apparaît, accédez au fichier du graphe (.xlsx ou .json) que vous voulez charger, puis choisissez **Ouvrir** pour le charger.

Azure Digital Twins ouvre un panneau **Importer** qui montre un aperçu du graphe à importer. Pour confirmer l’importation, sélectionnez l’icône **Enregistrer** dans le coin supérieur droit du panneau.

Si l’importation réussit, une fenêtre modale va afficher le nombre de modèles, de jumeaux et de relations qui ont été chargés.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import-successful.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le centre de l’écran contient une fenêtre modale Importation réussie montrant quatre jumeaux importés et deux relations importées." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import-successful.png":::

### <a name="export-graph-and-models"></a>Exporter un graphe et des modèles

Vous pouvez utiliser la fonctionnalité d’exportation pour exporter des graphes partiels ou complets, y compris les modèles, les représentations et les relations. L’exportation sérialise les jumeaux et les relations des résultats de requête les plus récents ainsi que tous les modèles de l’instance à un format JSON que vous pouvez télécharger sur votre machine.

Pour commencer, utilisez le panneau [Explorateur de requêtes](#query-your-digital-twin-graph) pour exécuter une requête qui sélectionne les jumeaux et les relations que vous voulez télécharger. Ceci va les placer dans le panneau Graphe des jumeaux.

>[!TIP]
>La requête pour afficher toutes les jumeaux et les relations est `SELECT * FROM digitaltwins`.

Une fois que le Graphe des jumeaux montre la partie du graphe que vous voulez télécharger, sélectionnez l’icône **Exporter le graphe**.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-export.png" alt-text="Capture d’écran du panneau Graphe des jumeaux d’Azure Digital Twins Explorer. Le bouton Exporter un graphe est mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-export.png":::

Cette action active un lien **Télécharger** dans la zone Graphe des jumeaux. Sélectionnez-le pour télécharger sur votre ordinateur une représentation JSON du résultat de la requête et de tous les modèles de votre instance.

>[!TIP]
>Ce fichier peut être modifié et/ou rechargé dans Azure Digital Twins via la fonctionnalité d’[importation](#import-graph).

## <a name="link-to-your-environment"></a>Lien vers votre environnement

Vous pouvez partager votre environnement Azure Digital Twins Explorer avec d’autres personnes pour collaborer à des travaux. Cette section décrit comment envoyer votre environnement Azure Digital Twins Explorer à une autre personne et vérifier qu’il dispose des autorisations nécessaires pour y accéder.

Pour partager votre environnement, vous pouvez envoyer un lien au destinataire qui va ouvrir une fenêtre Azure Digital Twins Explorer connectée à votre instance. Utilisez le lien ci-dessous et remplacez les espaces réservés pour votre **ID de locataire** et le **nom d’hôte** de votre instance Azure Digital Twins. 

`https://explorer.digitaltwins.azure.net/?tid=<tenant-ID>&eid=<Azure-Digital-Twins-host-name>`

>[!NOTE]
> La valeur de l’espace réservé pour le nom d’hôte n’est **pas** précédée de *https://* ici.

Voici un exemple d’URL avec les valeurs d’espace réservé remplies :

`https://explorer.digitaltwins.azure.net/?tid=00a000aa-00a0-00aa-0a0aa000aa00&eid=ADT-instance.api.wcus.digitaltwins.azure.net`

Pour que le destinataire puisse voir l’instance dans la fenêtre Azure Digital Twins Explorer résultante, il doit se connecter à son compte Azure et disposer d’un accès **Lecteur de données Azure Digital Twins** à l’instance (pour en savoir plus sur les rôles Azure Digital Twins, consultez [Sécurité](concepts-security.md)). Pour que le destinataire puisse apporter des modifications au graphe et aux données, il doit avoir le rôle **Propriétaire de données Azure Digital Twins** sur l’instance.

### <a name="link-with-a-query"></a>Lien avec une requête

Vous pouvez partager un environnement et spécifier une requête à exécuter lors du lancement, pour mettre en évidence un sous-graphe ou une vue personnalisée pour un collègue. Pour cela, commencez avec l’URL de l’environnement et ajoutez le texte de la requête à l’URL en tant que paramètre de chaîne de requête :

`https://explorer.digitaltwins.azure.net/?tid=<tenant-ID>&eid=<Azure-Digital-Twins-host-name>&query=<query-text>`

Le texte de la requête doit être encodé URL. 

>[!TIP]
>Vous pouvez copier le texte de la requête depuis la fenêtre de l’**Explorateur de requêtes** et le coller dans la fenêtre d’URL, à l’emplacement approprié, à la fin de l’URL. L’envoi de cette URL doit convertir le texte de la requête pour utiliser l’encodage URL approprié.
>
> Vous pouvez aussi utiliser un encodeur d’URL indépendant pour convertir le texte de la requête.

Voici un exemple du paramètre pour une requête à `SELECT * FROM digitaltwins` :

`...&query=SELECT%20*%20FROM%20digitaltwins`

Vous pouvez ensuite partager l’URL complétée.

## <a name="accessibility-and-advanced-settings"></a>Accessibilité et paramètres avancés

Vous pouvez activer plusieurs paramètres avancés d’Azure Digital Twins Explorer pour personnaliser votre expérience ou le rendre plus accessible.

Vous pouvez utiliser l’icône **Raccourcis clavier** dans la barre d’outils en haut à droite pour afficher la liste des raccourcis clavier qui peuvent être utilisés pour naviguer dans Azure Digital Twins Explorer.

 :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/keyboard-shortcuts.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. L’icône Raccourcis clavier est mise en surbrillance dans la barre d’outils supérieure." lightbox="media/how-to-use-azure-digital-twins-explorer/keyboard-shortcuts.png":::

Vous pouvez accéder à plusieurs fonctionnalités avancées sous l’engrenage Paramètres dans la barre d’outils supérieure droite :
* **Chargement hâtif** : Quand une requête retourne des jumeaux qui ont des relations avec d’autres jumeaux qui **ne sont pas** inclus dans les résultats de la requête, cette fonctionnalité va charger les jumeaux « manquants » avant de rendre le graphe.
* **Mise en cache** : Quand cette fonctionnalité est activée, Azure Digital Twins Explorer conserve un cache local des relations et des modèles en mémoire pour améliorer les performances des requêtes. Ces caches sont effacés quand il y a des opérations d’écriture sur les éléments concernés et lors de l’actualisation du navigateur.
* **Console** : Cette fonctionnalité permet d’afficher une fenêtre de console qui permet d’utiliser des fonctions de shell simples pour travailler sur le graphe.
* **Sortie** : Cette fonctionnalité permet d’afficher une fenêtre de sortie, qui montre une trace de diagnostic des opérations.
* **Contraste élevé** : cette fonctionnalité modifie les couleurs d’Azure Digital Twins Explorer pour qu’elles apparaissent avec un contraste plus élevé.

Vous pouvez **personnaliser la position des panneaux** qui composent Azure Digital Twins Explorer (Explorateur de requêtes, Modèles, Graphe des jumeaux, Graphe des modèles). Pour déplacer un panneau à un autre emplacement, cliquez sur le nom du panneau et maintenez le bouton enfoncé, puis faites-le glisser à la nouvelle position souhaitée.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/panels.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer. Les noms des panneaux Explorateur de requêtes, Modèles, Graphe des jumeaux et Graphe des modèles sont mis en évidence." lightbox="media/how-to-use-azure-digital-twins-explorer/panels.png":::

Les positions des panneaux sont réinitialisées lors de l’actualisation de la fenêtre du navigateur.

## <a name="next-steps"></a>Étapes suivantes 

En savoir plus sur l’écriture de requêtes pour le graphe des jumeaux Azure Digital Twins : 
* [Langage de requête](concepts-query-language.md)
* [Interroger le graphe de jumeaux](how-to-query-graph.md)