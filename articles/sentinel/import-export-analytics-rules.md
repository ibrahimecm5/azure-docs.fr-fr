---
title: Importer et exporter des règles d’analyse de Microsoft Sentinel | Microsoft Docs
description: Exporter et importer des règles analytiques vers et depuis des modèles ARM afin de faciliter le déploiement
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
ms.openlocfilehash: 97eb1145e8eebaed91019fbd68330c1399522002
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518543"
---
# <a name="export-and-import-analytics-rules-to-and-from-arm-templates"></a>Exporter et importer des règles analytiques vers et depuis des modèles ARM

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - L’exportation et l’importation de règles sont disponibles en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="introduction"></a>Introduction

Vous pouvez désormais exporter vos règles d’analyse vers des fichiers de modèle Azure Resource Manager (ARM) et importer des règles à partir de ces fichiers dans le cadre de la gestion et du contrôle de vos déploiements Microsoft Sentinel en tant que code. L’action d’exportation crée un fichier JSON (nommé *Azure_Sentinel_analytic_rule.json*) dans l’emplacement de téléchargement de votre navigateur, que vous pouvez ensuite renommer, déplacer et gérer comme tout autre fichier.

Puisque le fichier JSON exporté est indépendant de l’espace de travail, il peut être importé dans d’autres espaces de travail, voire d’autres locataires. En tant que code, il peut également être contrôlé par version, mis à jour et déployé dans une infrastructure CI/CD managée.

Le fichier comprend tous les paramètres définis dans la règle analytique. Par conséquent, pour les règles **planifiées**, il comprend la requête sous-jacente et les paramètres de planification qui l’accompagnent, la gravité, la création d’incident, les paramètres de regroupement d’événement et d’alerte, les tactiques MITRE ATT&CK attribuées et bien plus encore. Tout type de règle analytique, pas uniquement les règles **planifiées**, peut être exporté vers un fichier JSON.

## <a name="export-rules"></a>Exporter des règles

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Analyse**.

1. Sélectionnez la règle que vous souhaitez exporter, puis cliquez sur **Exporter** dans la barre en haut de l’écran.

    :::image type="content" source="./media/import-export-analytics-rules/export-rule.png" alt-text="Exporter des règles analytiques" lightbox="./media/import-export-analytics-rules/export-rule.png":::

    > [!NOTE]
    > - Vous pouvez sélectionner plusieurs règles analytiques à la fois pour l’exportation en cochant les cases en regard des règles et en cliquant sur **Exporter** à la fin.
    >
    > - Vous pouvez exporter toutes les règles sur une seule page de la grille d’affichage en une fois en cochant la case dans la ligne d’en-tête (en regard de **GRAVITÉ**) avant de cliquer sur **Exporter**. Toutefois, vous ne pouvez pas exporter plus d’une page complète de règles à la fois.
    >
    > - Notez que dans ce scénario, un seul fichier (nommé *Azure_Sentinel_analytic_ **rules**.json*) est créé et contient du code JSON pour toutes les règles exportées.

## <a name="import-rules"></a>Importer des règles

1. Préparez un fichier JSON de modèle ARM de règle analytique.

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Analyse**.

1. Cliquez sur **Importer** dans la barre en haut de l’écran. Dans la boîte de dialogue qui s’affiche, accédez au fichier JSON représentant la règle que vous souhaitez importer et sélectionnez-le, puis cliquez sur **Ouvrir**.

    :::image type="content" source="./media/import-export-analytics-rules/import-rule.png" alt-text="Importer des règles analytiques" lightbox="./media/import-export-analytics-rules/import-rule.png":::

    > [!NOTE]
    > Vous pouvez importer **jusqu’à 50** règles analytiques à partir d’un seul fichier de modèle ARM.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à exporter et importer des règles analytiques vers et à partir de modèles ARM.
- En savoir plus sur les [règles analytiques](detect-threats-built-in.md), notamment les [règles planifiées personnalisées](detect-threats-custom.md).
- En savoir plus sur les [modèles ARM](../azure-resource-manager/templates/overview.md).
