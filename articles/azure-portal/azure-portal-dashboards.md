---
title: Créer un tableau de bord dans le portail Azure
description: Cet article décrit comment créer et personnaliser un tableau de bord dans le portail Azure.
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 57de040263fdc6ae7a3aaa366b7cabc4f98235b3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219846"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Créer un tableau de bord dans le portail Azure

Les tableaux de bord sont une vue ciblée et organisée de vos ressources cloud dans le portail Azure. Servez-vous des tableaux de bord comme d’un espace de travail où vous pouvez superviser vos ressources et lancer rapidement des tâches pour vos opérations quotidiennes. Créez des tableaux de bord personnalisés en fonction des projets, des tâches ou des rôles utilisateur, par exemple.

Le portail Azure propose un tableau de bord par défaut comme point de départ. Vous pouvez modifier le tableau de bord par défaut et créer et personnaliser des tableaux de bord supplémentaires.

> [!NOTE]
> Chaque utilisateur peut créer jusqu’à 100 tableaux de bord privés. Si vous [publiez et partagez le tableau de bord](azure-portal-dashboard-share-access.md), il sera implémenté en tant que ressource Azure dans votre abonnement et ne sera pas comptabilisé dans le cadre de cette limite.

Cet article explique comment créer un nouveau tableau de bord et le personnaliser. Pour plus d’informations sur le partage des tableaux de bord, consultez [Partager des tableaux de bord Azure en utilisant le contrôle d’accès en fonction du rôle Azure](azure-portal-dashboard-share-access.md).

## <a name="create-a-new-dashboard"></a>Créer un tableau de bord

Cet exemple montre comment créer un nouveau tableau de bord privé avec un nom attribué. Tous les tableaux de bord sont privés lors de leur création, bien que vous puissiez choisir de publier et de partager votre tableau de bord avec d’autres utilisateurs de votre organisation si vous le souhaitez.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu du portail Azure, sélectionnez **Tableau de bord**. Votre vue par défaut peut déjà être définie sur le tableau de bord.

    :::image type="content" source="media/azure-portal-dashboards/portal-menu-dashboard.png" alt-text="Capture d’écran du portail Azure avec un tableau de bord sélectionné.":::

1. Sélectionnez **Nouveau tableau de bord**, puis **Tableau de bord vide**.

    :::image type="content" source="media/azure-portal-dashboards/create-new-dashboard.png" alt-text="Capture d’écran des options de nouveau tableau de bord.":::

    Cette action affiche la **Galerie de vignettes**, dans laquelle vous pouvez sélectionner les vignettes, ainsi qu’une grille vide sur laquelle vous disposerez les vignettes.

1. Sélectionnez le texte **Mon tableau de bord** dans l’étiquette du tableau de bord et entrez un nom permettant d’identifier facilement le tableau de bord personnalisé.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="Capture d’écran d’une grille vide avec la Galerie de vignettes.":::

1. Pour enregistrer le tableau de bord tel quel, sélectionnez **Personnalisation terminée** dans l’en-tête de page. Vous pouvez aussi passer à l’étape 2 de la section suivante pour ajouter des vignettes et enregistrer votre tableau de bord.

La vue du tableau de bord affiche maintenant votre nouveau tableau de bord. Sélectionnez la flèche en regard du nom du tableau de bord pour afficher les tableaux de bord disponibles. La liste peut inclure des tableaux de bord que d’autres utilisateurs ont créés et partagés.

## <a name="edit-a-dashboard"></a>Modifier un tableau de bord

Maintenant, nous allons modifier le tableau de bord pour ajouter, redimensionner et réorganiser les vignettes qui représentent vos ressources Azure.

### <a name="add-tiles-from-the-tile-gallery"></a>Ajouter des vignettes à partir de la Galerie de vignettes

Pour ajouter des vignettes à un tableau de bord, effectuez les étapes suivantes :

1. Sélectionnez ![icône d’édition](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Modifier** dans l’en-tête de page du tableau de bord.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-edit.png" alt-text="Capture d’écran du tableau de bord montrant l’icône Modifier.":::

1. Parcourez la **Galerie de vignettes** ou utilisez le champ de recherche pour trouver une vignette spécifique. Sélectionnez la vignette que vous souhaitez ajouter à votre tableau de bord.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="Capture d’écran de la Galerie de vignettes.":::

1. Sélectionnez **Ajouter** pour ajouter au tableau de bord la vignette de la taille et à l’emplacement par défaut. Vous pouvez aussi faire glisser la vignette dans la grille jusqu’à l’emplacement voulu. Ajoutez les vignettes de votre choix. Voici quelques idées :

    - Ajoutez **Toutes les ressources** pour afficher toutes les ressources que vous avez déjà créées.

    - Si vous travaillez avec plusieurs organisations, ajoutez la vignette **Identité d’organisation** au tableau de bord pour montrer clairement à quelle organisation les ressources appartiennent.

1. Au besoin, [redimensionnez ou réorganisez](#resize-or-rearrange-tiles) vos vignettes.

1. Pour enregistrer le modifications, sélectionnez **Enregistrer** dans l’en-tête de page. Vous pouvez également afficher un aperçu des modifications sans les enregistrer en sélectionnant **Aperçu** dans l’en-tête de page. Ce mode Aperçu vous permet également de voir l’effet des [filtres](#set-and-override-dashboard-filters) sur vos vignettes. Dans l’écran d’aperçu, vous pouvez sélectionner **Enregistrer** pour conserver les modifications, **Ignorer** pour les annuler, ou **Modifier** pour revenir aux options d’édition et apporter d’autres modifications.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Capture d’écran des options d’aperçu, d’enregistrement et d’annulation.":::

> [!NOTE]
> Utilisez une étiquette Markdown pour afficher un contenu statique personnalisé dans votre tableau de bord. Par exemple, vous pouvez afficher des instructions de base, une image, un ensemble de liens hypertexte ou même des informations de contact. Pour plus d’informations sur l’utilisation d’une vignette Markdown, voir [Utiliser une vignette Markdown sur les tableaux de bord Azure pour afficher un contenu personnalisé](azure-portal-markdown-tile.md).

### <a name="pin-content-from-a-resource-page"></a>Épingler du contenu à partir d’une page de ressource

Vous pouvez également ajouter des vignettes à votre tableau de bord directement à partir d’une page de ressource.

Bon nombre de pages de ressources contiennent une icône d’épingle dans l’en-tête de page. Vous pouvez donc épingler une vignette qui représente la page source. Dans certains cas, une icône d’épingle peut également apparaître en fonction du contenu spécifique présent dans une page, ce qui vous permet d’épingler une vignette représentant ce contenu spécifique plutôt que la page entière.

:::image type="content" source="media/azure-portal-dashboards/dashboard-pin-blade.png" alt-text="Capture d’écran de la barre de commandes d’une page avec l’icône d’épingle.":::

Si vous sélectionnez cette icône, vous pouvez épingler la vignette à un tableau de bord privé ou partagé existant. Vous pouvez aussi créer un autre tableau de bord avec cette épingle en sélectionnant **Créer**.

:::image type="content" source="media/azure-portal-dashboards/dashboard-pin-pane.png" alt-text="Capture d’écran des options Épingler au tableau de bord.":::

### <a name="copy-a-tile-to-a-new-dashboard"></a>Copier une vignette dans un nouveau tableau de bord

Si vous souhaitez réutiliser une vignette sur un autre tableau de bord, vous pouvez la copier d’un tableau de bord vers un autre. Pour ce faire, sélectionnez le menu contextuel dans l’angle supérieur droit, puis sélectionnez **Copier**.

:::image type="content" source="media/azure-portal-dashboards/copy-dashboard.png" alt-text="Capture d’écran montrant comment copier une vignette dans le portail Azure.":::

Vous pouvez ensuite choisir de copier la vignette dans un tableau de bord privé ou partagé existant, ou de créer une copie de la vignette dans le tableau de bord que vous utilisez déjà. Vous pouvez aussi créer un autre tableau de bord qui inclura une copie de la vignette en sélectionnant **Créer**.

### <a name="resize-or-rearrange-tiles"></a>Redimensionner ou réorganiser des vignettes

Pour changer la taille d’une vignette ou réorganiser les vignettes dans un tableau de bord, effectuez les étapes suivantes :

1. Sélectionnez ![icône d’édition](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Modifier** dans l’en-tête de page.

1. Sélectionnez le menu contextuel dans le coin supérieur droit d’une vignette. Ensuite, choisissez une taille de vignette. Les vignettes qui prennent en charge toutes les tailles présentent aussi une « poignée » dans le coin inférieur droit, que vous pouvez faire glisser pour changer la taille de la vignette.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="Capture d’écran d’un tableau de bord avec le menu des tailles de vignette ouvert.":::

1. Sélectionnez une vignette et faites-la glisser vers un nouvel emplacement sur la grille pour réorganiser votre tableau de bord.

### <a name="set-and-override-dashboard-filters"></a>Définir et remplacer des filtres de tableau de bord

Vers le haut de votre tableau de bord se trouvent des options qui vous permettent de configurer l’**actualisation automatique** et les **paramètres d’heure** pour les données affichées dans le tableau de bord, ainsi qu’une option vous permettant d’ajouter des filtres supplémentaires.

:::image type="content" source="media/azure-portal-dashboards/dashboard-global-filters.png" alt-text="Capture d’écran montrant les filtres globaux d’un tableau de bord.":::

Par défaut, les données sont actualisées toutes les heures. Pour changer ce paramètre, sélectionnez **Actualisation automatique** et choisissez un autre intervalle d’actualisation. Après avoir fait votre sélection, sélectionnez **Appliquer**.

Les paramètres d’heure sont définis par défaut sur **Heure UTC** : les données affichées sont celles des **Dernières 24 heures**. Pour changer ce paramètre, sélectionnez le bouton et choisissez un autre intervalle de temps, une autre granularité temporelle et/ou un autre fuseau horaire, puis sélectionnez **Appliquer**.

Pour appliquer des filtres supplémentaires, sélectionnez **Ajouter des filtres**. Les options proposées varient en fonction des vignettes présentes dans votre tableau de bord. Par exemple, vous verrez peut-être uniquement les données d’un abonnement ou d’un emplacement spécifique. Sélectionnez le filtre qui vous intéresse et effectuez vos sélections. Le filtre sera ensuite appliqué à vos données. Pour supprimer un filtre, sélectionnez la lettre **X** dans le bouton associé.

Les vignettes qui prennent en charge le filtrage comportent une icône de filtre ![icône de filtre](./media/azure-portal-dashboards/dashboard-filter.png) dans leur coin supérieur gauche. Pour certaines vignettes, vous pouvez remplacer les filtres globaux par des filtres qui sont propres à ces vignettes. Pour cela, sélectionnez **Configurer les données de vignette** dans le menu contextuel, ou sélectionnez l’icône de filtre, puis appliquez les filtres souhaités.

Si vous définissez des filtres pour une vignette particulière, celle-ci contient une icône de filtre double dans son coin gauche, ce qui indique que les données affichées dans cette vignette reflètent les filtres propres à la vignette.

:::image type="content" source="media/azure-portal-dashboards/dashboard-filter-override.png" alt-text="Capture d’écran montrant l’icône d’une vignette avec un remplacement de filtre.":::

## <a name="modify-tile-settings"></a>Modifier des paramètres de vignette

Certaines vignettes peuvent nécessiter une configuration supplémentaire pour afficher les informations dont vous avez besoin. Par exemple, la vignette **Graphique des métriques** doit être configurée pour afficher une métrique d’Azure Monitor. Vous pouvez également personnaliser les données des vignettes pour remplacer les filtres et les paramètres d’heure par défaut du tableau de bord.

### <a name="complete-tile-configuration"></a>Terminer la configuration des vignettes

Une bannière reste affichée sur chaque vignette qui n’a pas encore été personnalisée. Par exemple, dans le **graphique des métriques**, la bannière indique **Modifier dans Métriques**. D’autres bannières peuvent contenir du texte différent, par exemple **Configurer la vignette**.

Pour personnaliser la vignette :

1. Dans l’en-tête de page, sélectionnez **Enregistrer** pour quitter le mode d’édition.

1. Sélectionnez la bannière, puis effectuez la configuration requise.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-configure-tile.png" alt-text="Capture d’écran d’une vignette à configurer.":::

### <a name="customize-time-span-for-a-tile"></a>Personnaliser l’intervalle de temps pour une vignette

Le tableau de bord affiche des données d’activité qui sont actualisées selon les filtres globaux. Pour certaines vignettes, vous pouvez changer l’intervalle de temps d’une vignette spécifique. Pour ce faire, procédez comme suit :

1. Sélectionnez **Personnaliser les données de vignette** en utilisant le menu contextuel ou l’![icône de filtre](./media/azure-portal-dashboards/dashboard-filter.png) située dans le coin supérieur gauche de la vignette.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-customize-tile-data.png" alt-text="Capture d’écran du menu contextuel de la vignette.":::

1. Cochez la case **Remplacer les paramètres d’heure du tableau de bord au niveau de la vignette**.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-override-time-settings.png" alt-text="Capture d’écran de la boîte de dialogue où configurer les paramètres d’heure de la vignette.":::

1. Choisissez l’intervalle de temps des données à afficher sur cette vignette. Vous pouvez choisir un intervalle allant des 30 dernières minutes aux 30 derniers jours, ou définir une plage personnalisée.

1. Choisissez la granularité temporelle de l’affichage.  Vous pouvez choisir d’afficher les données par incréments d’une minute à un mois.

1. Sélectionnez **Appliquer**.

### <a name="change-the-title-and-subtitle-of-a-tile"></a>Modifier le titre et le sous-titre d’une vignette

Certaines vignettes vous permettent de modifier le titre et le sous-titre associés. Pour ce faire, sélectionnez **Configurer les paramètres de la vignette** dans le menu contextuel.

:::image type="content" source="media/azure-portal-dashboards/dashboard-tile-rename.png" alt-text="Capture d’écran montrant l’option Configurer les paramètres de la vignette":::

Apportez des modifications au titre et/ou au sous-titre de la vignette, puis sélectionnez **Appliquer**.

:::image type="content" source="media/azure-portal-dashboards/dashboard-title-subtitle.png" alt-text="Capture d’écran montrant comment modifier le titre et le sous-titre d’une vignette":::
 
## <a name="delete-a-tile"></a>Supprimer une vignette

Pour supprimer une vignette d’un tableau de bord, effectuez l’une des opérations suivantes :

- Sélectionnez le menu contextuel dans le coin supérieur droit de la vignette, puis sélectionnez **Supprimer du tableau de bord**.

- Sélectionnez ![icône d’édition](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Modifier** pour passer en mode de personnalisation. Pointez sur le coin supérieur droit de la vignette, puis sélectionnez l’icône de suppression ![icône de suppression](./media/azure-portal-dashboards/dashboard-delete-icon.png) pour supprimer la vignette du tableau de bord.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-delete-tile.png" alt-text="Capture d’écran montrant comment supprimer une vignette d’un tableau de bord.":::

## <a name="clone-a-dashboard"></a>Cloner un tableau de bord

Pour utiliser un tableau de bord existant comme modèle dans un nouveau tableau de bord, effectuez les étapes suivantes :

1. Assurez-vous que la vue de tableau de bord affiche le tableau de bord que vous souhaitez copier.

1. Dans l’en-tête de page, sélectionnez ![icône de clonage](./media/azure-portal-dashboards/dashboard-clone.png) **Cloner**.

1. Une copie du tableau de bord nommée **Clone de** *nom de votre tableau de bord* s’ouvre en mode d’édition. Suivez les étapes précédentes dans cet article pour renommer et personnaliser le tableau de bord.

## <a name="publish-and-share-a-dashboard"></a>Publier et partager un tableau de bord

Quand vous créez un tableau de bord, celui-ci est privé par défaut, ce qui signifie que vous êtes la seule personne à pouvoir le voir. Pour rendre des tableaux de bord visibles par d’autres, vous pouvez les publier et les partager. Pour plus d’informations, consultez [Partager des tableaux de bord Azure en utilisant le contrôle d’accès en fonction du rôle Azure](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Ouvrir un tableau de bord partagé

Pour rechercher et ouvrir un tableau de bord partagé, effectuez les étapes suivantes :

1. Sélectionnez la flèche en regard du nom du tableau de bord.

1. Effectuez une sélection dans la liste des tableaux de bord affichée. Si le tableau de bord que vous souhaitez ouvrir n’est pas listé :

    1. Sélectionnez **Parcourir tous les tableaux de bord**.

        :::image type="content" source="media/azure-portal-dashboards/dashboard-browse.png" alt-text="Capture d’écran du menu de sélection du tableau de bord":::

    1. Dans le champ **Type**, sélectionnez **Tableaux de bord partagés**.

        :::image type="content" source="media/azure-portal-dashboards/dashboard-browse-all.png" alt-text="Capture d’écran du menu de sélection de tous les tableaux de bord":::

    1. Sélectionnez un ou plusieurs abonnements. Vous pouvez également entrer du texte pour filtrer les tableaux de bord par nom.

    1. Sélectionnez un tableau de bord dans la liste des tableaux de bord partagés.

## <a name="delete-a-dashboard"></a>Supprimer un tableau de bord

Pour supprimer définitivement un tableau de bord privé ou partagé, procédez comme suit :

1. Sélectionnez le tableau de bord à supprimer dans la liste en regard du nom de tableau de bord.

1. Sélectionnez ![icône de suppression](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Supprimer** dans l’en-tête de page.

1. S’il s’agit d’un tableau de bord privé, sélectionnez **OK** dans la boîte de dialogue de confirmation pour le supprimer. Si c’est un tableau de bord partagé, dans la boîte de dialogue de confirmation, cochez la case pour confirmer que le tableau de bord publié ne sera plus visible par d’autres utilisateurs. Ensuite, sélectionnez **OK**.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-delete-dash.png" alt-text="Capture d’écran de la confirmation de la suppression.":::

## <a name="recover-a-deleted-dashboard"></a>Récupérer un tableau de bord supprimé

Si vous êtes dans le cloud Azure global et que vous supprimez un tableau de bord _publié_ dans le portail Azure, vous pouvez le récupérer dans les 14 jours suivant la suppression. Pour plus d’informations, consultez [Récupérer un tableau de bord supprimé dans le portail Azure](recover-shared-deleted-dashboard.md).

## <a name="next-steps"></a>Étapes suivantes

- [Partager des tableaux de bord Azure à l’aide du contrôle d’accès en fonction du rôle Azure](azure-portal-dashboard-share-access.md)
- [Créer par programmation des tableaux de bord Azure](azure-portal-dashboards-create-programmatically.md)
