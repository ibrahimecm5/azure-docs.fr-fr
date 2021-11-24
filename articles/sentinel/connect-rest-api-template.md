---
title: Connecter votre source de données à l’API du Collecteur de données Microsoft Sentinel pour l’ingestion des données | Microsoft Docs
description: Découvrez comment connecter des systèmes externes à l’API du Collecteur de données Microsoft Sentinel pour ingérer leurs données de journal dans des journaux personnalisés au sein de votre espace de travail.
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
ms.openlocfilehash: 439bdac80e5fef514a7de7e9617be42738c6c420
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524528"
---
# <a name="connect-your-data-source-to-the-microsoft-sentinel-data-collector-api-to-ingest-data"></a>Connecter votre source de données à l’API du Collecteur de données Microsoft Sentinel pour l’ingestion des données

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Les intégrations d’API créées par des fournisseurs tiers extraient les données des sources de données de leurs produits et se connectent à l'[API du Collecteur de données Azure Monitor](../azure-monitor/logs/data-collector-api.md) de Microsoft Sentinel pour envoyer les données dans des tables de journaux personnalisés dans votre espace de travail Microsoft Sentinel.

Pour l’essentiel, vous trouverez toutes les informations dont vous avez besoin pour configurer ces sources de données de façon à ce qu’elles se connectent à Microsoft Sentinel dans la documentation de chaque fournisseur.

Consultez la section de votre produit sur la page de [référence des connecteurs de données](data-connectors-reference.md) pour obtenir des instructions supplémentaires qui peuvent y figurer, ainsi que des liens vers les instructions de votre fournisseur.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Microsoft Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Microsoft Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/agents/agent-windows.md).

## <a name="configure-and-connect-your-data-source"></a>Configurer et connecter votre source de données

1. Dans le portail Microsoft Sentinel, sélectionnez **Connecteurs de données** dans le menu de navigation.

1. Sélectionnez l’entrée de votre produit dans la galerie de connecteurs de données, puis sélectionnez le bouton **Ouvrir la page du connecteur**.

1. Suivez les étapes qui s’affichent sur la page connecteur ou les liens vers les instructions du fournisseur qui s’y affichent.

1. Lorsque vous êtes invité à entrer l’ID de l’espace de travail et la clé primaire, copiez-les à partir de la page du connecteur de données et collez-les dans la configuration comme indiqué par les instructions de votre fournisseur. Reportez-vous à l’exemple ci-dessous.

    :::image type="content" source="media/connect-rest-api-template/workspace-id-primary-key.png" alt-text="ID d’espace de travail et clé primaire":::

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous la section **CustomLogs**. Visualisez la section de votre produit sur la page [référence des connecteurs de données](data-connectors-reference.md) pour les noms de tables.

Pour interroger les données de votre produit, utilisez ces noms de table dans votre requête.

Jusqu’à 20 minutes peuvent s’écouler avant que vos journaux commencent à apparaître dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris comment connecter des sources de données externes à l’API du Collecteur de données Microsoft Sentinel. Pour tirer pleinement parti des fonctionnalités intégrées à ces connecteurs de données, sélectionnez l’onglet **Étapes suivantes** de la page du connecteur de données. Vous y trouverez des exemples de requêtes prêtes à l’emploi, des classeurs ainsi que des modèles de règles analytiques pour commencer à trouver des informations utiles.

Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.
