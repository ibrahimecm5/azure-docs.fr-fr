---
title: Mapper des champs de données à des entités Microsoft Sentinel | Microsoft Docs
description: Mappez des champs de données dans des tables à des entités Microsoft Sentinel dans des règles d’analyse afin d’obtenir de meilleures informations sur les incidents.
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
ms.openlocfilehash: 48c08771fef5b18445d0a1b5268ea5ea2c535abf
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523426"
---
# <a name="map-data-fields-to-entities-in-microsoft-sentinel"></a>Mapper des champs de données à des entités dans Microsoft Sentinel 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - La nouvelle version de la fonctionnalité de mappage d’entités est en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

> [!IMPORTANT]
>
> - Pour obtenir des informations importantes sur la compatibilité descendante et sur les différences entre les versions nouvelles et anciennes du mappage d’entités, consultez les [Remarques sur la nouvelle version](#notes-on-the-new-version) à la fin de ce document.

## <a name="introduction"></a>Introduction

Le mappage d’entités fait partie intégrante de la configuration des [règles analytiques de requête planifiée](detect-threats-custom.md). Il enrichit la sortie des règles (alertes et incidents) avec des informations essentielles qui constituent la base de tout processus d’investigation et actions correctives ultérieures.

La procédure détaillée ci-dessous fait partie de l’Assistant Création de règles analytiques. Elle est traitée ici indépendamment afin de prendre en charge le scénario d’ajout ou de modification de mappages d’entités dans une règle analytique existante.

## <a name="how-to-map-entities"></a>Comment mapper des entités ?

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Analyse**.

1. Sélectionnez une règle de requête planifiée, puis cliquez sur **Modifier**. Ou créez une règle en cliquant sur **Créer > Règle de requête planifiée** en haut de l’écran.

1. Cliquez sur l’onglet **Définir la logique de la règle**. 

1. Dans la section **Enrichissement des alertes (préversion)** , développez **Mappage d’entités**.

    :::image type="content" source="media/map-data-fields-to-entities/alert-enrichment.png" alt-text="Développer le mappage d’entités":::

1. Dans la section **Mappage d’entités** maintenant développée, sélectionnez un type d’entité dans la liste déroulante **Type d’entité**.

    :::image type="content" source="media/map-data-fields-to-entities/choose-entity-type.png" alt-text="Choisir un type d’entité":::

1. Sélectionnez un **identificateur** pour l’entité. Les identificateurs sont des attributs d’une entité qui peuvent l’identifier suffisamment. Choisissez-en un dans la liste déroulante **Identificateur**, puis choisissez un champ de données dans la liste déroulante **Valeur** qui correspondra à l’identificateur. À quelques exceptions près, la liste **Valeur** est renseignée avec les champs de données de la table définie comme objet de la requête de règle.

    Vous pouvez définir **jusqu’à trois identificateurs** pour une entité donnée. Certains identificateurs sont obligatoires, d’autres facultatifs. Vous devez choisir au moins un identificateur obligatoire, sinon un message d’avertissement vous signale quels identificateurs sont obligatoires. Pour de meilleurs résultats (pour une identification unique maximale), vous devez utiliser des **identificateurs forts** dans la mesure du possible. Par ailleurs, l’utilisation de plusieurs identificateurs forts autorisera une plus grande corrélation entre les sources de données. Consultez la liste complète des [entités et identificateurs](entities-reference.md) disponibles.

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="Mapper des champs à des entités":::

1. Cliquez sur **Ajouter une nouvelle entité** pour mapper plus d’entités. Vous pouvez mapper **jusqu’à cinq entités** dans une même règle analytique. Vous pouvez également mapper plusieurs entités du même type. Par exemple, vous pouvez mapper deux entités **IP** ; une à partir d’un champ d’*adresse IP source* et une autre à partir d’un champ d’*adresse IP de destination*. De cette façon, vous pouvez les suivre toutes les deux.

    Si vous changez d’avis ou si vous avez commis une erreur, vous pouvez supprimer un mappage d’entités en cliquant sur l’icône Corbeille en regard de la liste déroulante d’entités.

1. Lorsque vous avez terminé de spécifier les paramètres, cliquez sur **Vérifier + créer**. Une fois la validation de la règle réussie, cliquez sur **Enregistrer**.

## <a name="notes-on-the-new-version"></a>Remarques sur la nouvelle version

- Si vous aviez déjà défini des mappages d’entités pour cette règle analytique à l’aide de l’ancienne version, ces mappages apparaissent dans le code de la requête. Les mappages d’entités définis dans la nouvelle version **n’apparaissent pas dans le code de la requête**. Les règles analytiques ne peuvent prendre en charge qu’une seule version de mappages d’entités à la fois, et la nouvelle version est prioritaire. Par conséquent, tout mappage que vous définissez ici entraîne la **non-prise en considération** de **tout** mappage défini dans le code de requête lors de l’exécution de celle-ci. 

- Si vous avez encore besoin d’utiliser l’**ancienne version** du mappage d’entités (tant que la nouvelle version est en préversion), vous pouvez toujours y accéder à l’aide d’un indicateur de fonctionnalité dans l’URL. Placez le curseur entre `https://portal.azure.com/` et `#blade`, puis insérez le texte `?feature.EntityMapping=false`.

  - Les limites de l’ancienne version continueront à s’appliquer. Vous pouvez mapper uniquement les entités utilisateur, hôte, adresse IP, URL et hachage de fichier, et une seule de chaque.

  - Vous devez **supprimer** tous les mappages d’entités créés à l’aide de la nouvelle version **avant** de revenir à l’ancienne version, autrement les mappages d’entités qui utilisent l’ancienne version **ne fonctionneront pas**.

- Une fois la nouvelle version du mappage d’entités en disponibilité générale, il ne sera plus possible d’utiliser l’ancienne version. Nous vous recommandons vivement de migrer vos anciens mappages d’entités vers la nouvelle version.


## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment mapper des champs de données à des entités dans des règles d’analyse de Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Obtenez une vue d’ensemble complète des [règles analytiques de requête planifiée](detect-threats-custom.md).
- Apprenez-en davantage sur les [entités de Microsoft Sentinel](entities-in-azure-sentinel.md).
