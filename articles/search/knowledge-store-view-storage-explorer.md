---
title: Affichage d’une base de connaissances
titleSuffix: Azure Cognitive Search
description: Affichez une base de connaissances avec le navigateur de stockage sur le Portail Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: d3a66bacfe746b73d269cd8a36fedf0c6b821cdd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563660"
---
# <a name="view-a-knowledge-store-with-storage-browser"></a>Affichage d’une base de connaissances avec le navigateur de stockage

Un [magasin de connaissances](knowledge-store-concept-intro.md) est un contenu créé par des compétences Recherche cognitive Azure et enregistré dans Stockage Azure. Cet article explique comment afficher le contenu d’une base de connaissances avec le navigateur de stockage sur le Portail Azure.

Commencez avec un magasin de connaissances existant créé dans le [Portail Azure](knowledge-store-create-portal.md) ou à l’aide des [API REST](knowledge-store-create-rest.md). Les procédures pas à pas pour le portail et REST créent une base de connaissances dans Stockage Table Azure.

## <a name="start-storage-browser"></a>Lancement du navigateur de stockage

1. Dans le portail Azure, [ouvrez le compte de stockage](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que vous avez utilisé pour créer la base de connaissances.

1. Dans le volet de navigation gauche du compte de stockage, sélectionnez **Navigateur de stockage**.

## <a name="view-and-edit-tables"></a>Affichage et modification des tables

1. Développez **Tables** pour rechercher les projections de table de votre base de connaissances. Si vous avez utilisé l’article de démarrage rapide ou REST pour créer le magasin de connaissances, les tables contiennent du contenu relatif aux révisions client d’un hôtel européen.

   :::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Capture d’écran du navigateur de stockage" border="true":::

1. Sélectionnez une table dans la liste pour afficher son contenu.

1. Pour réorganiser l’ordre des colonnes ou en supprimer une, sélectionnez **Modifier les colonnes** en haut de la page.

Dans le navigateur de stockage, vous ne pouvez interroger qu’une seule table à la fois suivant la [syntaxe de requête prise en charge](/rest/api/storageservices/Querying-Tables-and-Entities). Pour interroger plusieurs tables, envisagez d’utiliser Power BI à la place.

## <a name="next-steps"></a>Étapes suivantes

Connectez ce magasin de connaissances pour Power BI afin de créer des visualisations qui incluent plusieurs tables.

> [!div class="nextstepaction"]
> [Connexion avec Power BI](knowledge-store-connect-power-bi.md)
