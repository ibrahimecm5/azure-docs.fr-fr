---
title: Personnaliser des détails d’alerte dans Microsoft Sentinel | Microsoft Docs
description: Personnalisez la façon dont les alertes sont nommées et décrites, ainsi que leur gravité et les tactiques qui leur sont attribuées, en fonction de leur contenu.
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
ms.openlocfilehash: 6ce94549c4e58e0a19fff40e944ece22f948f953
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521716"
---
# <a name="customize-alert-details-in-microsoft-sentinel"></a>Personnaliser des détails d’alerte dans Microsoft Sentinel 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - La fonctionnalité des détails d’alerte est en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="introduction"></a>Introduction

Lorsque vous définissez un nom et une description pour vos règles analytiques planifiées et leur affectez des gravités et des tactiques MITRE ATT&CK, toutes les alertes générées par une règle particulière, et tous les incidents créés en conséquence, s’affichent avec le même nom, la même description, etc., sans tenir compte du contenu particulier d’une instance spécifique de l’alerte.

La fonctionnalité des **détails d’alerte** vous permet d’adapter l’apparence d’une alerte à son contenu. Vous pouvez sélectionner ici des paramètres dans votre alerte, qui peuvent être représentés dans le nom ou la description de chaque instance de l’alerte, ou qui peuvent contenir la tactique et la gravité attribuées à cette instance de l’alerte. Si le paramètre sélectionné n’a pas de valeur (ou une valeur non valide dans le cas de la tactique et de la gravité), les détails d’alerte reviennent aux valeurs par défaut spécifiées dans la première page de l’Assistant.

La procédure détaillée ci-dessous fait partie de l’Assistant Création de règles analytiques. Elle est traitée ici de manière indépendante pour aborder le scénario d’ajout ou de modification de détails d’alerte dans une règle analytique existante.

## <a name="how-to-customize-alert-details"></a>Comment personnaliser des détails d’alerte

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Analytique**.

1. Sélectionnez une règle de requête planifiée, puis cliquez sur **Modifier**. Ou créez une règle en cliquant sur **Créer > Règle de requête planifiée** en haut de l’écran.

1. Cliquez sur l’onglet **Définir la logique de la règle**.

1. Dans la section **Enrichissement d’alertes (préversion)** , développez **Détails d’alerte**.

    :::image type="content" source="media/customize-alert-details/alert-enrichment.png" alt-text="Personnaliser des détails d’alerte":::

1. Dans la section **Détails de l’alerte** à présent développée, saisissez un texte libre incluant les paramètres correspondant aux détails que vous souhaitez afficher dans l’alerte :

    1. Dans le champ **Format de nom d’alerte**, entrez le texte à afficher comme nom de l’alerte (texte d’alerte), et incluez, entre accolades, les paramètres que vous souhaitez intégrer dans le texte de l’alerte.

        Exemple : `Alert from {{ProviderName}}: {{AccountName}} failed to log on to computer {{ComputerName}} with IP address {{IPAddress}}.`

    1. Procédez de la même façon pour le champ **Format de description d’alerte**.
    
    1. N’utilisez les champs **Colonne Tactique** et **Colonne Gravité** que si les résultats de votre requête comprennent des colonnes contenant ces informations. Pour chacun d’entre eux, choisissez la colonne contenant les informations correspondantes.

    Si vous changez d’avis ou si vous avez commis une erreur, vous pouvez supprimer un détail d’alerte en cliquant sur l’icône de la corbeille en regard des champs des **colonnes Tactique ou Gravité**, ou supprimer le texte libre saisi dans les champs des **formats de nom ou de description d’alerte**.

1. Une fois que vous avez fini de personnaliser les détails de votre alerte, passez à l’onglet suivant de l’Assistant. Si vous modifiez une règle existante, cliquez sur l’onglet **Examiner et créer**. Une fois la règle validée, cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
Ce document vous a montré comment personnaliser des détails d’alerte dans les règles analytiques de Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Obtenez une vue d’ensemble complète des [règles analytiques de requête planifiée](detect-threats-custom.md).
- Apprenez-en davantage sur les [entités de Microsoft Sentinel](entities-in-azure-sentinel.md).
