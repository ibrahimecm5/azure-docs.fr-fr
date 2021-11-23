---
title: Utiliser des cartes d’intérieur dans le Créateur Azure Maps
description: Cet article présente les concepts qui s’appliquent aux services d’Azure Maps Creator.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 0e4f25e140c9b7d2693c94ead25b375e2298adbc
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400012"
---
# <a name="creator-for-indoor-maps"></a>Créateur pour cartes d’intérieur

Cet article présente les concepts et outils qui s’appliquent au Créateur Azure Maps. Nous vous recommandons de lire cet article avant de commencer à utiliser l’API Créateur Azure Maps et le Kit de développement logiciel (SDK).

Vous pouvez utiliser Creator pour développer des applications avec des fonctionnalités de carte basées sur des données cartographiques d’intérieur. Cet article décrit le processus de chargement, de conversion, de création et d’utilisation de vos données cartographiques.  En règle générale, le workflow est assuré par deux personnes différentes, avec des responsabilités et des domaines de compétences distincts :

- Le créateur de carte : chargé de l’organisation et de la préparation des données cartographiques.
- L’utilisateur des données cartographiques de Creator : il tire parti des données cartographiques clientes dans les applications.

Le schéma suivant illustre le workflow entier.

![Flux de travail des données cartographiques du Créateur](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>Créer un Créateur Azure Maps

Pour utiliser les services d’un Créateur, vous devez créer un Créateur Azure Maps dans un compte Azure Maps avec le niveau tarifaire Gen 2. Pour plus d’informations sur la création d’Azure Maps Creator dans Azure Maps, consultez [Gérer Azure Maps Creator](how-to-manage-creator.md).

> [!TIP]
> Pour plus d’informations sur la tarification, consultez la section *Créateur* dans [Tarification d’Azure Maps](https://aka.ms/CreatorPricing).

## <a name="creator-authentication"></a>Authentification de Creator

Le Créateur hérite des paramètres de Contrôle d’accès (IAM) d’Azure Maps. Tous les appels d’API pour l’accès aux données doivent être envoyés avec des règles d’authentification et d’autorisation.

Les données d’utilisation du Créateur sont incorporées dans vos graphiques d’utilisation et journal d’activité Azure Maps. Pour plus d’informations, consultez [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).

>[!Important]
>Nous vous recommandons d’utiliser :
>
> - Azure Active Directory (Azure AD) dans toutes les solutions créées avec un compte Azure Maps utilisant des services Creator. Pour plus d’informations sur Azure AD, consultez [Authentification Azure AD](azure-maps-authentication.md#azure-ad-authentication).
>
>- Paramètres du contrôle d’accès en fonction du rôle. À l’aide de ces paramètres, les créateurs de cartes peuvent prendre le rôle de Contributeur aux données Azure Maps, et les utilisateurs de données cartographiques Creator celui de Lecteur de données Azure Maps. Pour plus d’informations, consultez [Autorisation avec contrôle d’accès en fonction du rôle](azure-maps-authentication.md#authorization-with-role-based-access-control).

## <a name="creator-data-item-types"></a>Types d’éléments de données Creator

Les services Creator créent, stockent et utilisent différents types de données qui sont définis et traités dans les sections suivantes. Un élément de données Creator peut être de l’un des types suivants :

- Données converties
- Dataset
- Tileset
- Ensemble d’états de fonctionnalité

## <a name="upload-a-drawing-package"></a>Charger un package de dessin

Le créateur collecte des données de carte d’intérieur en convertissant un package de dessin chargé. Le package de dessin représente un bâtiment construit ou rénové. Pour plus d’informations, consultez [Exigences du package de dessin](drawing-requirements.md).

Utilisez l’[API de chargement de données Azure Maps](/rest/api/maps/data-v2/update-preview) pour charger un package de dessin. Une fois la compression de dessin chargée, l’API de chargement de données retourne un identificateur de données utilisateur (`udid`). `udid` peut ensuite être utilisé pour convertir le package chargé en données de carte d’intérieur.

## <a name="convert-a-drawing-package"></a>Convertir un package de dessin

Le [service de conversion d’Azure Maps](/rest/api/maps/v2/conversion) convertit un package de dessin chargé en données cartographiques d’intérieur. Le service de conversion valide également le package. Les problèmes de validation sont classés en deux types :

- Erreurs : si des erreurs sont détectées, le processus de conversion échoue. Quand une erreur se produit, le service de conversion fournit un lien vers l’application web autonome [Visualiseur d’erreurs de dessin d’Azure Maps](drawing-error-visualizer.md). Vous pouvez utiliser le Visualiseur d’erreurs de dessin pour inspecter les [Avertissements et erreurs du package de dessin](drawing-conversion-error-codes.md) qui se sont produits pendant le processus de conversion. Après avoir corrigé les erreurs, vous pouvez essayer de charger et de convertir le package.
- Avertissements : si des avertissements sont détectés, la conversion s’effectue correctement. Toutefois, nous vous recommandons d’examiner et de résoudre tous les avertissements. Un avertissement signifie qu’une partie de la conversion a été ignorée ou corrigée automatiquement. Ne pas résoudre les problèmes liés aux avertissements pourrait entraîner des erreurs dans les processus suivants.
Pour plus d’informations, consultez [Avertissements et erreurs du package de dessin](drawing-conversion-error-codes.md).

## <a name="create-indoor-map-data"></a>Créer des données cartographiques d’intérieur

Azure Maps Creator fournit les services suivants qui prennent en charge la création de cartes :

- [Service Jeu de données](/rest/api/maps/v2/dataset).
- [Service Tileset](/rest/api/maps/v2/tileset).
Utilisez le service Tileset pour créer une représentation vectorielle d’un jeu de données. Les applications peuvent utiliser un tileset pour présenter une vue basée sur des vignettes du jeu de données.
- [Service d’état de la fonctionnalité](/rest/api/maps/v2/feature-state). Le service d’état de la fonctionnalité permet de prendre en charge l’application de style de carte dynamique. Les applications peuvent utiliser l’application dynamique de style de carte pour refléter des événements en temps réel sur des espaces fournis par le système IoT.

### <a name="datasets"></a>Groupes de données

Un jeu de données est une collection de caractéristiques de carte d’intérieur. Les fonctionnalités de carte d’intérieur représentent des installations qui sont définies dans un package de dessin converti. Après avoir créé un jeu de données avec le [service Jeu de données](/rest/api/maps/v2/dataset), vous pouvez créer n’importe quel nombre de [tilesets](#tilesets) ou d’[ ensembles d’états de fonctionnalité](#feature-statesets).

À tout moment, les développeurs peuvent utiliser le [service Jeu de données](/rest/api/maps/v2/dataset) pour ajouter ou supprimer des installations dans un jeu de données existant. Pour plus d’informations sur la mise à jour d’un jeu de données à l’aide de l’API, consultez les options d’ajout dans [Service Jeu de données](/rest/api/maps/v2/dataset). Pour voir un exemple illustrant la façon de mettre à jour un jeu de données, consultez [Maintenance des données](#data-maintenance).

### <a name="tilesets"></a>Tilesets

Un tileset est une collection de données vectorielles qui représente un ensemble de vignettes de grille uniformes. Les développeurs peuvent utiliser le [Service Tileset](/rest/api/maps/v2/tileset) pour créer des tilesets à partir d’un jeu de données.

Pour refléter différentes étapes de contenu, vous pouvez créer plusieurs tilesets à partir du même jeu de données. Par exemple, vous pouvez créer un tileset avec du mobilier et de l’équipement, et en créer un autre sans mobilier ni équipement. Vous pouvez choisir de générer un tileset avec les mises à jour de données les plus récentes, et en générer un autre sans les mises à jour de données les plus récentes.

En plus des données vectorielles, le tileset fournit des métadonnées pour l’optimisation du rendu de la carte. Par exemple, les métadonnées de tileset contiennent un niveau de zoom minimum et maximum pour le tileset. Les métadonnées fournissent également un rectangle englobant qui définit l’étendue géographique du tileset. Une application peut utiliser un rectangle englobant pour définir par programme le point central approprié. Pour plus d’informations sur les métadonnées d’un tileset, consultez [API de liste de tileset](/rest/api/maps/v2/tileset/list).

Après sa création, un tileset peut être récupéré par le [service Render v2](#render-v2-get-map-tile-api).

Si un tileset devient obsolète et n’est plus utile, vous pouvez le supprimer. Pour plus d’informations sur la façon de supprimer un tileset, consultez [Maintenance des données](#data-maintenance).

>[!NOTE]
>Un tileset est indépendant du jeu de données à partir duquel il a été créé. Si vous créez un tileset à partir d’un jeu de données, et qu’ensuite vous mettez à jour ce jeu de données, le tileset n’est pas mis à jour. 
>
>Pour refléter les modifications apportées à un jeu de données, vous devez créer un nouveau tileset. De même, si vous supprimez un tileset, le jeu de données n’est pas affecté.

### <a name="feature-statesets"></a>Stateset de caractéristique

Les ensembles d’états de fonctionnalité sont des collections de propriétés dynamiques (des *états*) qui sont attribuées à des fonctionnalités de jeu de données, telles que des salles ou des équipements. Un exemple d’*état* peut être une température ou une occupation. Chaque *état* est une paire clé/valeur qui contient le nom de la propriété, la valeur et l’horodatage de la dernière mise à jour.

Vous pouvez utiliser le [service État de la fonctionnalité](/rest/api/maps/v2/feature-state/create-stateset) pour créer et gérer un ensemble d’états de fonctionnalité pour un jeu de données. Le stateset est défini par un ou plusieurs *états*. Chaque caractéristique, telle une salle, peut avoir un *état* associé.

La valeur de chaque *état* dans un stateset peut être mise à jour ou récupérée par des appareils IoT ou d’autres applications.  Par exemple, à l’aide de l’[API de mise à jour d’état de caractéristique](/rest/api/maps/v2/feature-state/update-states), des appareils mesurant l’occupation de l’espace peuvent publier systématiquement le changement d’état d’une salle.

Une application peut utiliser un stateset de caractéristique pour restituer de façon dynamique des caractéristiques dans un bâtiment en fonction de leur état et de leur style de carte respectif. Pour plus d’informations sur l’utilisation des ensembles d’états de fonctionnalité afin d’appliquer des styles à des fonctionnalités dans une carte de rendu, consultez [Module Cartes d’intérieur](#indoor-maps-module).

>[!NOTE]
>À l’instar des tilesets, la modification d’un jeu de données n’a pas d’incidence sur l’ensemble d’états de fonctionnalité existant, et la suppression d’un ensemble d’états de fonctionnalité n’a aucun effet sur le jeu de données auquel il est attaché.

## <a name="using-indoor-maps"></a>Utilisation des cartes d’intérieur

### <a name="render-v2-get-map-tile-api"></a>API Render V2-Get Map Tile

L’[API Render V2-Get Map Tile](/rest/api/maps/render-v2/get-map-tile) d’Azure Maps a été étendue pour prendre en charge les tilesets de Creator.

Les applications peuvent utiliser l’API Render V2-Get Map Tile pour demander des tilesets. Les tilesets peut ensuite être intégré dans un contrôle de carte ou un Kit de développement logiciel (SDK). Pour obtenir un exemple de contrôle de carte utilisant le service Render v2, consultez [Module Cartes d’intérieur](#indoor-maps-module).

### <a name="web-feature-service-api"></a>API de service de caractéristique web

Vous pouvez utiliser l’[API WFS (Web Feature Service)](/rest/api/maps/v2/wfs) pour interroger les jeux de données. L’API de service de caractéristique web suit les [caractéristiques de l’API Open Geospatial Consortium](http://docs.opengeospatial.org/DRAFTS/17-069r1.html). Vous pouvez utiliser l’API WFS pour interroger des fonctionnalités à l’intérieur même du jeu de données. Par exemple, vous pouvez utiliser WFS pour rechercher toutes les salles de réunion de taille moyenne d’un bâtiment et d’un étage particuliers.

### <a name="alias-api"></a>API d’alias

Les services Creator, tels que Conversion, Jeu de données, Tileset et État de fonctionnalité, retournent un identificateur pour chaque ressource créée à partir des API. L’[API Alias](/rest/api/maps/v2/alias) vous permet d’assigner un alias pour faire référence à un identificateur de ressource.

### <a name="indoor-maps-module"></a>Module Cartes d’intérieur

Le [Kit de développement logiciel (SDK) web Azure Maps](./index.yml) comprend le module Cartes d’intérieur. Ce module offre des fonctionnalités étendues à la bibliothèque *Map Control* d’Azure Maps. Le module Cartes d’intérieur affiche des cartes d’intérieur créées dans le Créateur. Il intègre des widgets, tels qu’un *sélecteur d’étage*, qui aide les utilisateurs à visualiser les différents étages.

Vous pouvez utiliser le module Cartes d’intérieur pour créer des applications web qui intègrent les données cartographiques d’intérieur à d’autres [services Azure Maps](./index.yml). Les configurations d’applications les plus courantes incluent l’ajout de connaissances provenant d’autres cartes, telles que la route, l’imagerie, la météo et le transit, à des cartes d’intérieur.

Le module Cartes d’intérieur prend également en charge l’application de style de carte dynamique. Pour une procédure pas à pas sur la façon d’implémenter dans une application l’application dynamique de style d’ensemble d’états de fonctionnalité, consultez [Utiliser le module Cartes d’intérieur](how-to-use-indoor-module.md).

### <a name="azure-maps-integration"></a>Intégration d’Azure Maps

Lorsque vous commencez à développer des solutions pour des cartes d’intérieur, vous pouvez découvrir des façons d’intégrer des fonctionnalités Azure Maps existantes. Par exemple, vous pouvez implémenter des scénarios de sécurité ou de suivi de ressource en utilisant l’API [Limite géographique d’Azure Maps](/rest/api/maps/spatial/postgeofence) avec des cartes d’intérieur de Creator. Par exemple, vous pouvez utiliser l’API Limite géographique pour déterminer si un employé pénètre dans des espaces intérieurs spécifiques, ou en sort. Pour plus d’informations sur la façon de connecter Azure Maps à la télémétrie IoT, consultez [ce tutoriel sur l’analytique spatiale IoT](tutorial-iot-hub-maps.md).

### <a name="data-maintenance"></a>Maintenance des données

 Vous pouvez utiliser l’API Azure Maps Creator Lister, Mettre à jour et Supprimer pour répertorier, mettre à jour et supprimer vos jeux de données, tilesets et ensembles d’états de fonctionnalité.

>[!NOTE]
>Lorsque vous examinez une liste d’éléments pour savoir si vous devez les supprimer, pensez à l’impact de cette suppression sur l’ensemble des applications ou API dépendantes. Par exemple, si vous supprimez un tileset utilisé par une application au moyen de l’[API Render V2-Get Map Tile ](/rest/api/maps/render-v2/get-map-tile), l’application ne parviendra pas à afficher ce tileset.

### <a name="example-updating-a-dataset"></a>Exemple : Mise à jour d’un jeu de données

L’exemple suivant montre comment mettre à jour un jeu de données, créer un tileset et supprimer un ancien tileset :

1. Pour charger et convertir le nouveau package de dessin, suivez les étapes des sections [Charger un package de dessin](#upload-a-drawing-package) et [Convertir un package de dessin](#convert-a-drawing-package).
2. Utilisez l’[API de création de jeu de données](/rest/api/maps/v2/dataset/create) pour ajouter les données converties au jeu de données existant.
3. Utilisez l’[API de création de tileset](/rest/api/maps/v2/tileset/create) pour générer un nouveau tileset à partir du jeu de données mis à jour.
4. Enregistrez le nouveau **tilesetId** pour l’étape suivante.
5. Pour activer la visualisation du jeu de données campus mis à jour, mettez à jour l’identificateur du tileset dans votre application. Si l’ancien tileset n’est plus utilisé, vous pouvez le supprimer.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Création d’un carte d’intérieur du Créateur](tutorial-creator-indoor-maps.md)
