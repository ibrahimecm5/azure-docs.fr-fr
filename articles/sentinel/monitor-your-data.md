---
title: Visualisez vos données à l'aide des classeurs Azure Monitor dans Microsoft Sentinel | Microsoft Docs
description: Apprenez à visualiser vos données à l'aide de classeurs dans Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 525f67a7c9284a9ac78c388e52041d7895032104
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522609"
---
# <a name="use-azure-monitor-workbooks-to-visualize-and-monitor-your-data"></a>Utiliser des workbooks Azure Monitor pour visualiser et superviser vos données

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Une fois que vous avez [connecté vos sources de données](quickstart-onboard.md) à Microsoft Sentinel, vous pouvez visualiser et surveiller les données à l'aide de l'adoption par Microsoft Sentinel des classeurs Azure Monitor, qui offrent une grande souplesse dans la création de tableaux de bord personnalisés. Bien que les Workbooks soient affichés différemment dans Microsoft Sentinel, il peut être utile pour vous de voir comment [créer des rapports interactifs avec les Workbooks Azure Monitor](../azure-monitor/visualize/workbooks-overview.md). Microsoft Sentinel vous permet de créer des workbooks personnalisés à partir de vos données. Il est également fourni avec des modèles de classeurs intégrés qui vous permettent d'obtenir rapidement des informations sur vos données dès que vous connectez une source de données.

Cet article décrit comment visualiser vos données dans Microsoft Sentinel.

> [!div class="checklist"]
> * Utiliser des classeurs intégrés
> * Créer des classeurs

## <a name="prerequisites"></a>Prérequis

Vous devez avoir au moins les droits de **lecteur de Workbook** ou de **contributeur de Workbook** sur le groupe de ressources de l'espace de travail Microsoft Sentinel.

> [!NOTE]
> Les workbooks que vous pouvez voir dans Microsoft Sentinel sont enregistrés dans le groupe de ressources de l'espace de travail Microsoft Sentinel et sont marqués par l'espace de travail dans lequel ils ont été créés.

## <a name="use-built-in-workbooks"></a>Utiliser des classeurs intégrés

1. Allez à **Workbooks** et sélectionnez ensuite **Modèles** pour voir la liste complète des workbooks intégrés à Microsoft Sentinel. 

    Pour voir quels types de données sont pertinents pour les types de données que vous avez connectés, le champ **Types de données requis** de chaque workbook indiquera le type de données à côté d'une coche verte si vous transmettez déjà les données pertinentes à Microsoft Sentinel.

    [ ![Accédez aux classeurs.](media/tutorial-monitor-data/access-workbooks.png) ](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. Sélectionnez **Afficher le modèle** pour voir le modèle rempli avec vos données.

1. Pour modifier le classeur, sélectionnez **Enregistrer**, puis sélectionnez l’emplacement où vous souhaitez enregistrer le fichier JSON du modèle.

   > [!NOTE]
   > Cette opération crée une ressource Azure basée sur le modèle associé et enregistre le fichier JSON du modèle sans les données.


1. Sélectionnez **Afficher le classeur enregistré**. 

    [ ![Affichez les classeurs.](media/tutorial-monitor-data/workbook-graph.png) ](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    Sélectionnez le bouton **Modifier** de la barre d’outils du classeur pour personnaliser ce dernier en fonction de vos besoins. Lorsque vous avez terminé, sélectionnez **Enregistrer** pour enregistrer vos paramètres.

    Pour plus d’informations, consultez [Créer des rapports interactifs avec les classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md).

> [!TIP]
> Pour cloner votre classeur, sélectionnez **Modifier**, puis sélectionnez **Enregistrer sous**, en veillant à enregistrer le classeur sous un autre nom, dans le même abonnement et le même groupe de ressources.
> Les classeurs clonés sont affichés sous l’onglet **Mes classeurs**.
>
## <a name="create-new-workbook"></a>Créer un classeur

1. Accédez à **Classeurs**, puis sélectionnez **Ajouter un classeur** pour créer un classeur entièrement nouveau.

    [ ![Nouveau classeur.](media/tutorial-monitor-data/create-workbook.png) ](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. Pour modifier le classeur, sélectionnez **Modifier**, puis ajoutez du texte, des requêtes et des paramètres selon vos besoins. Pour plus d’informations sur la personnalisation du classeur, consultez [Créer des rapports interactifs avec les classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md). 

1. Quand vous créez une requête, assurez-vous que l’option **Source de données** est définie sur **Journaux** et **Type de ressource** sur **Log Analytics**, puis choisissez le ou les espaces de travail appropriés. 

1. Après avoir créé votre workbook, enregistrez-le en veillant à l'enregistrer sous le groupe d'abonnement et de ressources de votre espace de travail Microsoft Sentinel.

1. Si vous voulez autoriser d’autres personnes de votre organisation à utiliser le classeur, sous **Enregistrer dans**, sélectionnez **Rapports partagés**. Si vous souhaitez restreindre l’usage de ce classeur à vous-seul, sélectionnez **Mes rapports**.

1. Pour passer d’un classeur à un autre dans votre espace de travail, sélectionnez **Ouvrir** ![Icône d’ouverture d’un classeur.](./media/tutorial-monitor-data/switch.png) dans la barre d’outils de n’importe quel classeur. L’écran bascule vers une liste de classeurs vers lesquels vous pouvez basculer.

    Sélectionnez le classeur que vous souhaitez ouvrir :

    [ ![Basculez entre les classeurs.](media/tutorial-monitor-data/switch-workbooks.png) ](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>Actualiser les données de votre classeur

Actualisez votre classeur pour afficher les données mises à jour. Dans la barre d’outils, sélectionnez l’une des options suivantes :

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::  **Actualiser**, pour actualiser manuellement les données de votre classeur.

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false":::  **Actualisation automatique**, pour définir l’actualisation automatique de votre classeur à un intervalle configuré.

    - Les intervalles d’actualisation automatique s’échelonnent entre **5 minutes** et **1 jour**.

    - L’actualisation automatique est suspendue lorsque vous modifiez un classeur, et les intervalles sont redémarrés chaque fois que vous revenez en mode d’affichage à partir du mode d’édition.

    - Les intervalles d’actualisation automatique sont également redémarrés si vous actualisez manuellement vos données.

    > [!TIP]
    > Par défaut, l’actualisation automatique est désactivée. Pour optimiser les performances, l’actualisation automatique est également désactivée chaque fois que vous fermez un classeur, et elle ne s’exécute pas en arrière-plan. Activez l’actualisation automatique en fonction de vos besoins la prochaine fois que vous ouvrirez le classeur.
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>Imprimer un classeur ou enregistrer au format PDF

Pour imprimer un classeur ou l’enregistrer au format PDF, utilisez le menu Options à droite du titre du classeur.

1. Sélectionnez Options > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Imprimer le contenu**. 
2. Dans l’écran d’impression, ajustez vos paramètres d’impression si nécessaire ou sélectionnez **Enregistrer au format PDF** pour l’enregistrer localement.

Exemple :

[ ![Imprimez votre classeur ou enregistrez-le au format PDF.](media/whats-new/print-workbook.png) ](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>Comment supprimer des classeurs

Pour supprimer un classeur enregistré (soit un modèle enregistré ou un classeur personnalisé), dans la page Classeurs, sélectionnez le classeur enregistré à supprimer, puis sélectionnez **Supprimer**. Cette opération supprime le classeur enregistré.

> [!NOTE]
> Elle supprime le classeur, mais aussi toutes les modifications que vous avez apportées au modèle. Le modèle d’origine reste disponible.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à visualiser vos données dans Microsoft Sentinel, à l'aide des workbooks Azure.

Pour savoir comment automatiser vos réponses aux menaces, voir [Configurer des réponses automatisées aux menaces dans Microsoft Sentinel](tutorial-respond-threats-playbook.md).

Pour en savoir plus sur les workbooks intégrés les plus populaires, voir [Workbooks Microsoft Sentinel couramment utilisés](top-workbooks.md). 
