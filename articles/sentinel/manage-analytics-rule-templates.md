---
title: Gérer des versions de modèles pour vos règles d’analyse planifiée dans Microsoft Sentinel
description: Apprenez à gérer la relation entre vos modèles de règle d’analyse planifiée et les règles créées à partir de ces modèles. Fusionnez les mises à jour des modèles dans vos règles et rétablissez les modifications apportées aux règles dans le modèle d’origine.
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
ms.openlocfilehash: 5845dfd88e3377eacff5b1d27ab927df422e4556
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522951"
---
# <a name="manage-template-versions-for-your-scheduled-analytics-rules-in-microsoft-sentinel"></a>Gérer des versions de modèles pour vos règles d’analyse planifiée dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Cette fonctionnalité est en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="introduction"></a>Introduction

Microsoft Sentinel est fourni avec des [modèles de règle d’analyse](detect-threats-built-in.md) que vous transformez en règles actives en en créant une copie, ce qui se produit lorsque vous créez une règle à partir d’un modèle. Toutefois, à ce stade, la règle active n’est plus connectée au modèle. Si des modifications sont apportées à un modèle de règle par des ingénieurs Microsoft ou toute autre personne, les règles créées à partir de ce modèle ne sont ***pas*** mises à jour de manière dynamique pour correspondre au nouveau modèle.

Toutefois, les règles créées à partir de modèles ***se souviennent*** des modèles dont elles sont issues, ce qui présente deux avantages :

- Si vous avez apporté des modifications à une règle lors de sa création à partir d’un modèle (ou à tout moment par la suite), vous pouvez toujours rétablir la règle dans sa version originale (en tant que copie du modèle).

- Vous pouvez être averti de la mise à jour d’un modèle et vous aurez le choix de mettre à jour vos règles avec la nouvelle version de leurs modèles ou de les laisser telles quelles.

Cet article explique comment gérer ces tâches et ce qu’il convient de garder à l’esprit. Les procédures décrites ci-dessous s’appliquent à toutes les règles d’analyse **[planifiées](detect-threats-built-in.md#scheduled)** créées à partir de modèles.

## <a name="discover-your-rules-template-version-number"></a>Découvrir le numéro de version du modèle de votre règle

Avec l’implémentation de la gestion de version de modèle, vous pouvez afficher et suivre les versions de vos modèles de règle et les règles créées à partir de celles-ci. Les règles dont les modèles ont été mis à jour affichent un badge « *Mise à jour disponible* » à côté du nom de la règle.

1. Dans le panneau **Analyse**, sélectionnez l’onglet **Règles actives**.

1. Sélectionnez une règle de type **Planifiée**.  

    - Si la règle affiche le badge « *Mise à jour disponible* », son volet de détails contient un bouton **Vérifier et mettre à jour** en regard du bouton **Modifier** (voir l’image 1 à l’étape suivante).

    - Si la règle a été créée à partir d’un modèle, mais ne dispose pas du badge « *Mise à jour disponible* », le volet des détails contient un bouton **Comparer au modèle** en regard du bouton **Modifier** (voir les images 2 et 3 à l’étape suivante).

    - S’il n’existe qu’un seul bouton **Modifier**, la règle a été créée à partir de zéro, et non à partir d’un modèle.

        :::image type="content" source="media/manage-analytics-rule-templates/see-rules-with-updated-template.png" alt-text="Capture d’écran de la liste des règles actives, avec un badge indiquant qu’une mise à jour de modèle est disponible." lightbox="media/manage-analytics-rule-templates/see-rules-with-updated-template.png":::

1. Faites défiler la page jusqu’en bas du volet des détails, où vous verrez deux numéros de version : la version du modèle à partir duquel la règle a été créée et la dernière version disponible du modèle. 

    :::image type="content" source="media/manage-analytics-rule-templates/see-template-versions.png" alt-text="Capture d’écran du volet des détails. Faites défiler la page pour afficher les numéros de version de modèle." border="false":::

    Le nombre est au format « 1.0.0 », version principale, version mineure et build.  
    (Pour l’instant, le numéro de build n’est pas utilisé et sera toujours 0.)

    - Une différence dans le numéro de *version principale* indique qu’un élément essentiel du modèle a été modifié, ce qui peut affecter la façon dont la règle détecte des menaces ou même sa capacité à fonctionner en fin de compte. Il s’agit d’une modification que vous souhaitez inclure à vos règles.

    - Une différence dans le numéro de *version mineure* indique une amélioration mineure du modèle, c’est-à-dire une modification esthétique ou une opération similaire, qui serait « agréable à avoir », mais qui n’est pas essentielle pour maintenir les fonctionnalités, l’efficacité ou les performances de la règle. Il s’agit d’une modification que vous pouvez tout aussi facilement adopter ou ignorer.

    > [!NOTE]
    > Les images 2 et 3 ci-dessus montrent deux exemples de règles créées à partir de modèles, où le modèle n’a pas été mis à jour.
    > - L’image 2 affiche une règle qui a un numéro de version pour son modèle actuel. Cela indique que la règle a été créée après l’implémentation initiale de la gestion de version des modèles par Microsoft Sentinel en octobre 2021.
    > - L’image 3 affiche une règle qui n’a pas de version de modèle actuelle. Cela indique que la règle a été créée avant octobre 2021. Si la version de modèle la plus récente est disponible, il s’agit probablement d’une version plus récente du modèle que celle utilisée pour créer la règle.

## <a name="compare-your-active-rule-with-its-template"></a>Comparer votre règle active à son modèle

Choisissez l’un des onglets suivants en fonction de l’action que vous souhaitez effectuer, pour consulter les instructions relatives à cette action :

# <a name="update-template"></a>[Mettre à jour le modèle](#tab/update)

Après avoir sélectionné une règle et déterminé que vous souhaitez la mettre à jour, sélectionnez **Vérifier et mettre à jour** dans le volet des détails (voir ci-dessus). Vous verrez que l'**Assistant Règle d’analyse** comporte désormais un onglet **Comparer à la dernière version**.

Sous cet onglet, vous verrez une comparaison côte à côte entre les représentations YAML de la règle existante et la version la plus récente du modèle. 

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions.png" alt-text="Capture d’écran de l’onglet « Comparer à la dernière version » dans l’Assistant Règle d’analyse.":::

> [!NOTE]
> La mise à jour de cette règle remplace votre règle existante par la version la plus récente du modèle.

Toute étape ou logique d’automatisation qui fait référence à la règle existante doit être vérifiée, au cas où les noms référencés auraient changé. En outre, toutes les personnalisations effectuées lors de la création de la règle d’origine (modifications apportées à la requête, à la planification, au regroupement ou à d’autres paramètres) peuvent être remplacées.

### <a name="update-your-rule-with-the-new-template-version"></a>Mettre votre règle à jour avec la nouvelle version du modèle

- Si les modifications apportées à la nouvelle version du modèle sont acceptables pour vous et que rien d’autre dans votre règle d’origine n’a été affecté, sélectionnez **Vérifier et mettre à jour** pour valider et appliquer les modifications. 

- Si vous souhaitez personnaliser davantage la règle ou réappliquer les modifications qui seraient autrement remplacées, sélectionnez **Suivant : modifications personnalisées**. Si vous choisissez cette option, vous allez parcourir les autres onglets de l'[Assistant Règle d’analyse](detect-threats-custom.md) pour apporter ces modifications, après quoi vous allez valider et appliquer les modifications sous l’onglet **Vérifier et mettre à jour**.

- Si vous ne souhaitez pas apporter de modifications à votre règle existante, mais que vous souhaitez conserver la version existante du modèle, quittez simplement l’Assistant en sélectionnant le X dans le coin supérieur droit.

# <a name="revert-to-template"></a>[Rétablir un modèle](#tab/revert)

Après avoir sélectionné une règle et déterminé que vous souhaitez rétablir sa version d’origine, sélectionnez **Comparer au modèle** dans le volet des détails (voir ci-dessus). Vous verrez que l'**Assistant Règle d’analyse** comporte désormais un onglet **Comparer à la dernière version**.

Sous cet onglet, vous verrez une comparaison côte à côte entre les représentations YAML de la règle existante et la version la plus récente du modèle. Ces deux numéros de version peuvent être identiques, mais le côté gauche affiche la règle active, notamment toutes les modifications qui y ont été apportées pendant ou après sa création à partir du modèle, tandis que le côté droit affiche le modèle inchangé.

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions-2.png" alt-text="Capture d’écran de l’onglet « Comparer à la dernière version » dans l’Assistant Règle d’analyse.":::

> [!NOTE]
> La mise à jour de cette règle remplace votre règle existante par la version la plus récente du modèle.
Toute étape ou logique d’automatisation qui fait référence à la règle existante doit être vérifiée, au cas où les noms référencés auraient changé. En outre, toutes les personnalisations effectuées lors de la création de la règle d’origine (modifications apportées à la requête, à la planification, au regroupement ou à d’autres paramètres) peuvent être remplacées.

### <a name="revert-your-rule-to-its-original-template-version"></a>Rétablir la version de modèle d’origine de votre règle

- Si vous souhaitez rétablir complètement la version d’origine de cette règle (une copie propre du modèle), sélectionnez **Vérifier et mettre à jour** pour valider et appliquer les modifications. 

- Si vous souhaitez personnaliser la règle différemment ou réappliquer les modifications qui seraient autrement remplacées, sélectionnez **Suivant : modifications personnalisées**. Si vous choisissez cette option, vous allez parcourir les autres onglets de l'[Assistant Règle d’analyse](detect-threats-custom.md) pour apporter ces modifications, après quoi vous allez valider et appliquer les modifications sous l’onglet **Vérifier et mettre à jour**.

- Si vous ne souhaitez pas modifier votre règle existante, quittez simplement l’Assistant en sélectionnant le X dans le coin supérieur droit.

---

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à assurer le suivi des versions de vos modèles de règle d’analyse Microsoft Sentinel et à rétablir les versions de modèle existantes des règles actives ou à les mettre à jour. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- En savoir plus sur les [règles d’analyse](detect-threats-built-in.md).
- Détails supplémentaires sur l'[Assistant Règle d’analyse](detect-threats-custom.md).
