---
title: Comment créer et gérer des collections
description: Cet article explique comment créer et gérer des regroupements dans Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: template-how-to
ms.openlocfilehash: 0c288971fefc95bfed7d5c4b7496dda628bb1f90
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845263"
---
# <a name="create-and-manage-collections-in-azure-purview"></a>Créer et gérer des regroupements dans Azure Purview

Dans Azure Purview, les collections permettent d’organiser les ressources et les sources par le biais du processus de votre entreprise, mais ils constituent également l’outil utilisé pour gérer l’accès dans l’ensemble de Purview. Ce guide vous guide tout au long de la création et de la gestion de ces collections, ainsi que les étapes à suivre pour inscrire des sources et ajouter des ressources à vos collections.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Votre propre [locataire Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Une [ressource Purview](create-catalog-portal.md) active.

### <a name="check-permissions"></a>Vérifiez les autorisations

Pour créer et gérer des collections dans Purview, vous devez être **administrateur de collection**. Nous pouvons vérifier ces autorisations dans [Purview Studio](https://web.purview.azure.com/resource/). Pour ouvrir le studio, accédez à votre ressource Purview dans le [Portail Azure](https://portal.azure.com), puis sélectionnez la mosaïque Open Purview Studio (Ouvrir Purview Studio) dans la page de présentation.

1. Sélectionnez Data Map > Collections dans le volet gauche pour ouvrir la page gestion de la collection.

    :::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur Data Map, avec l’onglet Collections sélectionné." border="true":::

1. Sélectionnez votre collection racine. Il s’agit de la collection supérieure de votre liste de collections qui porte le même nom que votre ressource Purview. Dans notre exemple ci-dessous, elle s’appelle Contoso Purview. Autrement, si des collections existent déjà, vous pouvez sélectionner n’importe quelle collection dans laquelle vous souhaitez créer un sous-groupe.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-root-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur le mappage de données, avec la collection racine mise en évidence." border="true":::

1. Sélectionnez l’onglet **Attributions de rôles** dans la fenêtre collection.

    :::image type="content" source="./media/how-to-create-and-manage-collections/role-assignments.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur le mappage de données, avec l’onglet d’attributions de rôle racine mise en évidence." border="true":::

1. Pour créer une collection, vous devez figurer dans la liste des administrateurs de collections sous attributions de rôle. Si vous avez créé la ressource Purview, vous devez être répertorié comme administrateur de collections sous la collection racine. Si ce n’est pas le cas, vous devez contacter l’administrateur de collections pour qu’il vous accorde l’autorisation.

    :::image type="content" source="./media/how-to-create-and-manage-collections/collection-admins.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur le mappage de données, avec la section administrateur de collections mise en évidence." border="true":::

## <a name="collection-management"></a>Gestion des collections

### <a name="create-a-collection"></a>Création d'une collection

Pour créer une collection, vous devez être administrateur de collections. En cas de doute suivez le [guide ci-dessus](#check-permissions) pour vérifier les autorisations.

1. Sélectionnez Data Map > Collections dans le volet gauche pour ouvrir la page gestion de la collection.

    :::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur le mappage de données, avec l’onglet Collections sélectionné et ouvert." border="true":::

1. Sélectionnez **+ Ajouter une collection**. Là encore, notez que seuls les [administrateurs de collection](#check-permissions) peuvent gérer des collections.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-add-a-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, montrant la fenêtre de la nouvelle collection, avec les boutons ajouter une collection mis en évidence." border="true":::

1. Dans le volet droit, entrez le nom et la description de la collection. Si nécessaire, vous pouvez également ajouter des utilisateurs ou des groupes en tant qu’administrateurs de collections à la nouvelle collection.
1. Sélectionnez **Créer**.

    :::image type="content" source="./media/how-to-create-and-manage-collections/create-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, montrant la fenêtre de la nouvelle collection, avec un nom d’affichage et les administrateurs de collections sélectionnés, ainsi que le bouton créer mis en évidence." border="true":::

1. Les informations de la nouvelle collection seront reflétées dans la page.

    :::image type="content" source="./media/how-to-create-and-manage-collections/created-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, montrant la fenêtre de la collection nouvellement créée." border="true":::

### <a name="edit-a-collection"></a>Modifier une collection

1. Sélectionnez **Modifier** dans la page de détails de la collection ou dans le menu déroulant de la collection.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur la fenêtre de la collection, avec le bouton modifier mis en évidence dans la fenêtre de la collection sélectionnée et sous le bouton points de suspension en regard du nom de la collection." border="true":::

1. La description de la collection et les administrateurs de collections peuvent être modifiés. Apportez les modifications, puis sélectionnez **Enregistrer** pour enregistrer vos modifications.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-description.png" alt-text="Capture d’écran de la fenêtre Purview Studio avec la fenêtre de modification de la collection ouverte, une description ajoutée à la collection et le bouton enregistrer mis en évidence." border="true":::

### <a name="view-collections"></a>Afficher les collections

1. Sélectionnez l’icône triangle en regard du nom de la collection pour développer ou réduire la hiérarchie de la collection. Sélectionnez les noms de collection à parcourir.

    :::image type="content" source="./media/how-to-create-and-manage-collections/subcollection-menu.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec le bouton en regard du nom de collection mis en évidence." border="true":::

1. Tapez dans la zone de filtre en haut de la liste pour filtrer les collections.

    :::image type="content" source="./media/how-to-create-and-manage-collections/filter-collections.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec le filtre mis en évidence au-dessus des collections." border="true":::

1. Sélectionnez **Actualiser** dans le menu contextuel de la collection racine pour recharger la liste des collections.

    :::image type="content" source="./media/how-to-create-and-manage-collections/refresh-collections.png" alt-text="Capture d’écran de la fenêtre de la collection dans Purview Studio, avec le bouton en regard du nom de la ressource sélectionné, et le bouton d’actualisation mis en évidence." border="true":::

1. Sélectionnez **Actualiser** dans la page de détails de la collection pour recharger la collection unique.

    :::image type="content" source="./media/how-to-create-and-manage-collections/refresh-single-collection.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec le bouton actualiser sous la fenêtre de collection mis en évidence." border="true":::

### <a name="delete-a-collection"></a>Supprimer une collection

Pour supprimer une collection, vous devez être administrateur de collections. En cas de doute suivez le guide ci-dessus pour vérifier les autorisations. Aucune collection enfant, ressource, source de données ou analyse ne doit être associée à la collection que vous souhaitez supprimer. 

1. Sélectionnez **Supprimer** dans la page de détails de la collection.
   
   :::image type="content" source="./media/how-to-create-and-manage-collections/delete-collections.png" alt-text="Capture d’écran de la fenêtre Purview Studio permettant de supprimer une collection" border="true":::

2. Sélectionnez **Confirmer** à l’invite **Voulez-vous vraiment supprimer cette collection ?**

   :::image type="content" source="./media/how-to-create-and-manage-collections/delete-collection-confirmation.png" alt-text="Capture d’écran de la fenêtre Purview Studio montrant le message de confirmation affiché avant la suppression d’une collection" border="true":::

3. Confirmez la suppression de la collection de votre Data Map Purview.

## <a name="add-roles-and-restrict-access-through-collections"></a>Ajouter des rôles et restreindre l’accès par le biais de collections

Étant donné que les autorisations sont gérées par le biais de collections dans Purview, il est important de comprendre les rôles et les autorisations qu’ils peuvent fournir à vos utilisateurs. Un utilisateur a accordé des autorisations sur une collection qui aura accès aux sources et aux ressources associées à ce regroupement, et héritera des autorisations sur les sous-groupes. L’héritage [peut être limité](#restrict-inheritance), mais il est autorisé par défaut.

Le guide ci-dessous décrit les rôles, comment les gérer et l’héritage des autorisations.

### <a name="roles"></a>Rôles

Tous les rôles attribués s’appliquent aux sources, aux ressources et aux autres objets de la collection à laquelle le rôle est appliqué.

* **Administrateurs de collections** : ils peuvent modifier la collection, ses détails et ajouter des sous-collections. Ils peuvent également ajouter des conservateurs de données, des lecteurs de données et d’autres rôles Purview à une étendue de collection. Les administrateurs de collection qui sont automatiquement hérités d’un regroupement parent ne peuvent pas être supprimés.
* **Administrateurs de sources de données** : ils peuvent gérer les sources de données et les analyses de données.
* **Curateurs de données** : ils peuvent créer, lire, modifier et supprimer des objets de données de catalogue et établir des relations entre les objets.
* **Lecteurs de données** : ils peuvent accéder aux objets de données du catalogue, mais pas les modifier.

### <a name="add-role-assignments"></a>Ajouter des attributions de rôle

1. Sélectionnez l’onglet **Attributions de rôles** pour afficher tous les rôles d’une collection. Seul un administrateur de collections peut gérer les attributions de rôles.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-role-assignments.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec l’onglet des attributions de rôle mis en surbrillance." border="true":::

1. Sélectionnez **Modifier les attributions de rôles** ou l’icône représentant une personne pour modifier chacun des membres de rôles.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-role-assignments.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec la liste déroulante Modifier les attributions de rôles sélectionnée." border="true":::

1. Tapez dans la zone de texte pour rechercher les utilisateurs que vous souhaitez ajouter au membre du rôle. Sélectionnez **X** pour supprimer les membres que vous ne souhaitez pas ajouter.

    :::image type="content" source="./media/how-to-create-and-manage-collections/search-user-permissions.png" alt-text="Capture d’écran de la fenêtre d’administrateur de collections de la collection dans Purview Studio, avec la barre de recherche mise en évidence." border="true":::

1. Sélectionnez **OK** pour enregistrer vos modifications, et vous constaterez que les nouveaux utilisateurs apparaissent dans la liste attributions de rôles.

### <a name="remove-role-assignments"></a>Supprimer les attributions de rôles

1. Sélectionnez le bouton **X** en regard du nom d’un utilisateur pour supprimer une attribution de rôle.

    :::image type="content" source="./media/how-to-create-and-manage-collections/remove-role-assignment.png" alt-text="Capture d’écran de la fenêtre de collecte Purview Studio, avec l’onglet des attributions de rôle sélectionné, et le bouton x à côté d’un des noms mis en évidence." border="true":::

1. Sélectionnez **Confirmer** si vous êtes sûr de vouloir supprimer l’utilisateur.

    :::image type="content" source="./media/how-to-create-and-manage-collections/confirm-remove.png" alt-text="Capture d’écran d’une fenêtre contextuelle de confirmation, avec le bouton confirmer mis en évidence." border="true":::

### <a name="restrict-inheritance"></a>Restreindre l’héritage

Les autorisations de collection sont automatiquement héritées de la collection parent. Par exemple, toutes les autorisations sur la collection racine (la collection située en haut de la liste qui porte le même nom que votre ressource Purview) sont héritées par toutes les collections situées au-dessous. Vous pouvez restreindre l’héritage à tout moment à partir d’une collection parent à l’aide de l’option Restreindre les autorisations héritées.

Une fois que vous avez restreint l’héritage, vous devez ajouter des utilisateurs directement à la collection restreinte pour leur accorder l’accès.

1. Accédez au regroupement dans lequel vous souhaitez restreindre l’héritage et sélectionnez l’onglet **Attributions de rôle**.
1. Sélectionnez **Restrict inherited permissions** (Restreindre les autorisations héritées) et sélectionnez **Restreindre l’accès** dans la boîte de dialogue contextuelle pour supprimer les autorisations héritées de cette collection et de tous les sous-groupes. Notez que les autorisations de l’administrateur de collections ne sont pas affectées.

    :::image type="content" source="./media/how-to-create-and-manage-collections/restrict-access-inheritance.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec l’onglet d’attributions de rôles sélectionné et le bouton à glissière de restriction des autorisations héritées mis en évidence." border="true":::

1. Après restriction, les membres hérités sont supprimés des rôles attendre l’administrateur de la collection.
1. Sélectionnez à nouveau le bouton bascule **Restrict inherited permissions** (Restreindre les autorisations héritées) pour revenir en arrière.

    :::image type="content" source="./media/how-to-create-and-manage-collections/remove-restriction.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec l’onglet d’attributions de rôles sélectionné et le bouton à glissière annuler la restriction des autorisations héritées mis en évidence." border="true":::
    
## <a name="register-source-to-a-collection"></a>Inscrire la source à une collection

1. Sélectionnez **Inscrire** ou l’icône inscrire sur le nœud collection pour inscrire une source de données. Notez que seul l’administrateur de sources de données peut inscrire des sources.

    :::image type="content" source="./media/how-to-create-and-manage-collections/register-by-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio du mappage de données avec le bouton inscrire mis en évidence en haut de la page et sous une collection."border="true":::

1. Renseignez le nom de la source de données, ainsi que d’autres informations sur la source.  Elle répertorie toutes les collections pour lesquelles vous disposez d’une autorisation d’analyse en bas du formulaire. Vous pouvez sélectionner un regroupement. Toutes les ressources sous cette source appartiendront au regroupement que vous sélectionnez.

    :::image type="content" source="./media/how-to-create-and-manage-collections/register-source.png" alt-text="Capture d’écran de la fenêtre d’inscription de la source."border="true":::

1. La source de données créée sera placée sous la collection sélectionnée. Sélectionnez **Afficher les détails** pour afficher la source de données.

    :::image type="content" source="./media/how-to-create-and-manage-collections/see-registered-source.png" alt-text="Capture d’écran de la fenêtre du mappage de données de Purview Studio avec la nouvelle carte source mise en évidence."border="true":::

1. Sélectionnez **Nouvelle analyse** pour créer une analyse sous la source de données.

    :::image type="content" source="./media/how-to-create-and-manage-collections/new-scan.png" alt-text="Capture d’écran de la fenêtre Purview Studio de la source avec le bouton nouvelle analyse mis en évidence."border="true":::

1. De même, en bas du formulaire, vous pouvez sélectionner une collection, et toutes les ressources analysées sont incluses dans la collection.
Notez que les collections répertoriées ici sont limitées aux sous-groupes de la collection de sources de données.

    :::image type="content" source="./media/how-to-create-and-manage-collections/scan-under-collection.png" alt-text="Capture d’écran d’une fenêtre de nouvelle analyse avec la liste déroulante des collections mise en évidence."border="true":::

1. De retour dans la fenêtre de collection, vous pouvez remarquer les sources de données liées à la collection sur la carte sources.

    :::image type="content" source="./media/how-to-create-and-manage-collections/source-under-collection.png" alt-text="Capture d’écran de la fenêtre du mappage de données de Purview Studio avec la nouvelle carte source mise en évidence dans le mappage."border="true":::

## <a name="add-assets-to-collections"></a>Ajouter des ressources à des collections

Les ressources et les sources sont également associées à des collections. Au cours d’une analyse, celle-ci a été associée à une collection, les ressources sont automatiquement ajoutées à cette collection, mais peuvent également être ajoutées manuellement à n’importe quel sous-groupe.

1. Vérifiez les informations sur la collection dans les informations détaillées de la ressource. Vous trouverez des informations sur le regroupement dans la section **Chemin d’accès de la collection**, dans le coin supérieur droit de la page des détails de la ressource.

    :::image type="content" source="./media/how-to-create-and-manage-collections/collection-path.png" alt-text="Capture d’écran de la fenêtre de ressources Purview Studio, avec le chemin d’accès de la collection mis en évidence." border="true":::

1. Autorisations dans la page des détails de la ressource :
    1. Vérifiez le modèle d’autorisation basé sur une collection en suivant le [guide d’ajout des rôles et de la restriction aux accès aux collections ci-dessus](#add-roles-and-restrict-access-through-collections).
    1. Si vous ne disposez pas de l’autorisation de lecture sur une collection, les ressources de cette dernière ne figurent pas dans les résultats de la recherche. Si vous obtenez l’URL directe d’une ressource et que vous l’ouvrez, la page Aucun accès s’affiche. Dans ce cas, contactez votre administrateur Purview pour lui accorder l’accès. Vous pouvez sélectionner le bouton **Actualiser** pour vérifier à nouveau l’autorisation.

        :::image type="content" source="./media/how-to-create-and-manage-collections/no-access.png" alt-text="Capture d’écran de la fenêtre de ressource Purview Studio dans laquelle l’utilisateur n’a pas d’autorisations et n’a accès ni aux informations ni aux options." border="true":::

    1. Si vous disposez de l’autorisation de lecture sur une collection mais que vous n’avez pas l’autorisation d’écriture, vous pouvez parcourir la page de détails de la ressource, mais les opérations suivantes sont désactivées :
        * Modifier la ressource. Le bouton **Modifier** est désactivé.
        * Supprimer la ressource. Le bouton **Supprimer** est désactivé.
        * Déplacer un élément multimédia vers une autre collection. Le bouton points de suspension dans le coin supérieur droit de la section du chemin d’accès de la collection est masqué.
    1. Les ressources de la section **Hiérarchie** est également affectée par les autorisations. Les ressources sans autorisation de lecture sont grisées.

        :::image type="content" source="./media/how-to-create-and-manage-collections/hierarchy-permissions.png" alt-text="Capture d’écran de la fenêtre de hiérarchie Purview Studio où l’utilisateur n’a que des autorisations de lecture, et n’a pas accès aux options." border="true":::

### <a name="move-asset-to-another-collection"></a>Déplacer une ressource vers une autre collection

1. Sélectionnez le bouton points de suspension dans le coin supérieur droit de la section Chemin d’accès de la collection.

    :::image type="content" source="./media/how-to-create-and-manage-collections/move-asset.png" alt-text="Capture d’écran de la fenêtre de ressources de Purview Studio avec le chemin d’accès de la collection mis en évidence et le bouton points de suspension sélectionné en regard du chemin d’accès de la collection." border="true":::

1. Sélectionnez le bouton **Déplacer vers une autre collection**.
1. Dans le volet droit, choisissez la collection cible vers laquelle vous souhaitez vous déplacer. Notez que vous ne pouvez voir que les collections pour lesquelles vous disposez d’autorisations en écriture. La ressource peut également être ajoutée uniquement aux sous-groupes de la collection de sources de données.

    :::image type="content" source="./media/how-to-create-and-manage-collections/move-select-collection.png" alt-text="Capture d’écran de la fenêtre contextuelle de Purview Studio avec le menu déroulant de sélection d’une collection mis en évidence." border="true":::

1. Sélectionnez le bouton **Déplacer** en bas de la fenêtre pour déplacer la ressource.

## <a name="search-and-browse-by-collections"></a>Rechercher et parcourir par collections

### <a name="search-by-collection"></a>Rechercher par collection

1. Dans Azure Purview, la barre de recherche se trouve en haut de l’expérience utilisateur du studio Purview.

   :::image type="content" source="./media/how-to-create-and-manage-collections/purview-search-bar.png" alt-text="Capture d’écran montrant l’emplacement de la barre de recherche dans Azure Purview" border="true":::

1. Lorsque vous sélectionnez la barre de recherche, vous pouvez voir l’historique de vos recherches récentes et les ressources récemment consultées. Sélectionnez **Afficher tout** pour voir toutes les ressources récemment consultées.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-no-keywords.png" alt-text="Capture d’écran montrant la barre de recherche avant l’entrée de mots clés" border="true":::

1. Entrez des mots clés qui vous aident à identifier votre ressource, notamment son nom, le type de données, des classifications et des termes du glossaire. Au fur et à mesure que vous saisissez des mots clés relatifs à la ressource souhaitée, Azure Purview affiche des suggestions sur les éléments à rechercher et les possibles correspondances de ressources. Pour terminer votre recherche, sélectionnez **Afficher les résultats de la recherche** ou appuyez sur la touche **Entrée**.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-keywords.png" alt-text="Capture d’écran montrant la barre de recherche lorsqu’un utilisateur entre des mots clés" border="true":::

1. La page des résultats de la recherche affiche une liste des ressources qui correspondent aux mots clés fournis par ordre de pertinence. Plusieurs facteurs peuvent influencer le score de pertinence d’une ressource. Vous pouvez filtrer davantage la liste en sélectionnant des collections, des magasins de données, des classifications, des contacts, des étiquettes et des termes de glossaire spécifiques qui s’appliquent à la ressource que vous recherchez.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-results.png" alt-text="Capture d’écran montrant les résultats d’une recherche" border="true":::

1. Sélectionnez la ressource de votre choix pour afficher la page des détails dans laquelle vous pouvez afficher les propriétés, notamment le schéma, la traçabilité des données et les propriétaires de la ressource.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-by-collection.png" alt-text="Capture d’écran montrant les résultats de la recherche avec des collections." border="true":::

### <a name="browse-by-collection"></a>Parcourir par collection

1. Pour parcourir les ressources de données, vous pouvez sélectionner **Parcourir les ressources** sur la page d’accueil.

    :::image type="content" source="./media/how-to-create-and-manage-collections/browse-by-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio du catalogue avec le bouton parcourir les ressources mis en évidence." border="true":::

1. Dans la page Parcourir la ressource, sélectionnez le tableau croisé dynamique **Par collection**. Les collections sont répertoriées avec l’affichage de table hiérarchique. Pour approfondir l’exploration des ressources de chaque collection, sélectionnez le nom de collection correspondant.

    :::image type="content" source="./media/how-to-create-and-manage-collections/by-collection-view.png" alt-text="Capture d’écran de la fenêtre Purview Studio de la ressource avec l’onglet par collection sélectionné."border="true":::

1. Sur la page suivante, les résultats de la recherche des ressources sous la collection sélectionnée s’affichent. Vous pouvez affiner les résultats en sélectionnant les filtres de facettes. Vous pouvez aussi voir les ressources sous d’autres collections en sélectionnant les noms de collections connexes ou les noms de sous-groupes.

    :::image type="content" source="./media/how-to-create-and-manage-collections/search-results-by-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio du catalogue avec l’onglet par collection sélectionné."border="true":::

1. Pour afficher les détails d’une ressource, sélectionnez le nom de celle-ci dans les résultats de la recherche. Vous pouvez également vérifier les ressources et les modifier en bloc.

    :::image type="content" source="./media/how-to-create-and-manage-collections/view-asset-details.png" alt-text="Capture d’écran de la fenêtre Purview Studio du catalogue avec l’onglet par collection sélectionné et les cases à cocher des ressources mises en évidence."border="true":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une collection, vous pouvez suivre les guides ci-dessous pour ajouter des ressources et des analyses.

* [Gérer les sources de données](manage-data-sources.md)

* [Sources de données prises en charge](purview-connector-overview.md)

* [Analyse et ingestion](concept-scans-and-ingestion.md)
