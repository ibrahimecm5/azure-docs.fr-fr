---
title: Solutions d’IA métier
description: Solutions d’IA métier dans Azure Synapse Analytics
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: garye
ms.date: 11/02/2021
author: nelgson
ms.author: negust
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0a4476b1324938acd2c753368f26bc7aafbed62b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096682"
---
# <a name="industry-ai-solutions"></a>Solutions d’IA métier

Tous les secteurs d’activité rencontrent des problèmes spécifiques à résoudre. Cet article aborde les solutions métier qui sont disponibles dans les espaces de travail Azure Synapse et qui permettent de résoudre rapidement les problèmes courants liés au secteur d’activité. Actuellement, la solution d’IA proposée est une solution de vente au détail pour les recommandations de produits. Vous trouverez ci-dessous plus d’informations sur la façon d’utiliser cette solution en tant que détaillant.

## <a name="target-user"></a>Utilisateur cible

La solution de recommandation s’adresse aux scientifiques des données existants et émergents qui sont à l’aise avec le code et qui sont familiarisés avec les concepts du machine learning. Cette solution est destinée à accélérer la productivité de ces utilisateurs lors de la résolution d’un problème spécifique dans le domaine de la vente au détail.

## <a name="retail-product-recommendation-solution"></a>Solution de vente au détail pour les recommandations de produits

La solution **Vente au détail -Recommandations de produits** constitue un moteur de recommandations robuste et scalable pour le développement prêt à l’emploi dans Synapse. Lorsque vous utilisez cette solution, un notebook entraîne un modèle Machine Learning pour la recommandation de produits.

Vous devrez peut-être personnaliser le notebook préconfiguré pour faire en sorte qu’il réponde aux besoins de votre entreprise.

Cette solution de recommandation de vente au détail peut être déployée dans deux modes différents. Vous pouvez l’essayer avec des exemples de données ou utiliser une base de données modélisée à l’aide du modèle de base de données de vente au détail dans Synapse.

La solution **Vente au détail - Recommandations de produits** fournit un pipeline pour les recommandations basées sur le contenu. Le pipeline de recommandations basées sur le contenu utilise l’algorithme LightGBM pour entraîner un modèle permettant de prédire les préférences de l’utilisateur en fonction des caractéristiques d’utilisateurs et d’éléments. Les caractéristiques peuvent être statiques comme le profil utilisateur et le profil d’élément, ou dynamiques comme les modèles de comportements utilisateur agrégés. Le système de recommandations basées sur le contenu est souvent utilisé pour les recommandations personnalisées ou les recommandations concernant de nouveaux produits susceptibles d’intéresser l’utilisateur.

## <a name="get-started"></a>Bien démarrer

1. Ouvrir votre espace de travail Synapse
1. Dans votre écran d’accueil, sélectionnez **Centre des connaissances** dans la section **En savoir plus**.
1. Dans le Centre des connaissances, sélectionnez **Parcourir la galerie**.
1. Dans la galerie, sélectionnez l’onglet **Modèles de base de données**, faites défiler jusqu’à la section **Solutions d’IA**, puis sélectionnez la solution « Vente au détail - Recommandation de produit ». Cliquez sur **Continuer**.
1. Vous avez le choix entre deux options :
   * « Utiliser des exemples de données » 
   * « Utiliser mes propres données à partir d’une base de données d’espace de travail ». Cette option peut correspondre, par exemple, à une base de données modélisée à l’aide du modèle de base de données de vente au détail.
   
    En cliquant sur **Déployer**, vous ouvrez un notebook dans votre espace de travail Synapse.

1. Un notebook est désormais ouvert dans votre espace de travail. Vous pouvez joindre ce notebook à un pool Spark pour commencer à l’explorer. N’oubliez pas que ce notebook est destiné à être personnalisé en fonction de vos besoins.

> [!NOTE]
> Si vous choisissez votre propre base de données, vous devez personnaliser le notebook afin d’utiliser vos propres noms de table et de colonne.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Azure Synapse Analytics](../get-started.md)
* [Créer un espace de travail](../get-started-create-workspace.md)
