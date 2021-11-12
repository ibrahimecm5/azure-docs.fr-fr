---
title: Tableaux de bord Azure Data Studio
description: Tableaux de bord Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: cea97bab303ce2d009cecc67ed30ec89b0992118
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554333"
---
# <a name="azure-data-studio-dashboards"></a>Tableaux de bord Azure Data Studio

[Azure Data Studio](/sql/azure-data-studio/what-is) fournit une expérience similaire à celle du portail Azure pour visualiser les informations sur vos ressources Azure Arc.  Ces vues sont appelées **tableaux de bord**, et si leur présentation et leurs options sont similaires à celles que vous pouvez voir sur une ressource donnée dans le portail Azure, elles vous donnent la possibilité de voir ces informations localement dans votre environnement au cas où vous n’auriez pas de connexion disponible à Azure.


## <a name="connecting-to-a-data-controller"></a>Connexion à un contrôleur de données

### <a name="prerequisites"></a>Prérequis

- Télécharger [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
- L’extension Azure arc est installée



### <a name="connect"></a>Se connecter

1. Ouvrir Azure Data Studio
2. Sélectionnez l’onglet **Connexions** sur la gauche.
3. Dans la partie inférieure, développez le panneau nommé **Contrôleurs Azure Arc**
4. Cliquez sur le bouton **Connecter un contrôleur**. Un panneau s’ouvre sur le côté droit
5. Par défaut, Azure Data Studio tentera de lire à partir du fichier kube.config dans votre répertoire par défaut et de répertorier les contextes de cluster Kubernetes disponibles et de présélectionner le contexte de cluster actuel. S’il s’agit du cluster approprié auquel se connecter, entrez l’espace de noms dans lequel le contrôleur de données Azure Arc est déployé dans l’entrée pour l’**Espace de noms**. Si vous devez récupérer l’espace de noms dans lequel le contrôleur de données Azure Arc est déployé, vous pouvez exécuter ```kubectl get datacontrollers -A``` sur votre cluster Kubernetes. 
6. Ajoutez éventuellement un nom complet pour le contrôleur de données Azure Arc dans l’entrée pour le **Nom**
7. Sélectionnez **Se connecter**.


Maintenant que vous êtes connecté à un contrôleur de données, vous pouvez visualiser les tableaux de bord pour le contrôleur de données, et toutes les instances SQL managées ou les ressources du groupe de serveurs PostgreSQL Hyperscale dont vous disposez.

## <a name="view-the-data-controller-dashboard"></a>Visualiser le tableau de bord du contrôleur de données

Cliquez avec le bouton droit sur le contrôleur de données dans le panneau Connexions du panneau extensible **Contrôleurs Arc** et choisissez **Gérer**.

Vous pouvez voir ici des informations détaillées sur la ressource de contrôleur de données, comme le nom, la région, le mode de connexion, le groupe de ressources, l’abonnement, le point de terminaison de contrôleur et l’espace de noms.  Vous pouvez aussi voir une liste de toutes les ressources de base de données managées gérées par le contrôleur de données.

Notez que la présentation est similaire à ce que vous pouvez voir dans le portail Azure.

Vous pouvez facilement lancer la création d’une instance SQL managée ou d’un groupe de serveurs PostgreSQL Hyperscale en cliquant sur le bouton + Nouvelle instance.

Vous pouvez également ouvrir le portail Azure dans le contexte de ce contrôleur de données en cliquant sur le bouton Ouvrir dans le portail Azure.

## <a name="view-the-sql-managed-instance-dashboards"></a>Visualiser les tableaux de bord des instances SQL managées

Si vous avez créé des instances SQL managées, vous pouvez les voir listées dans le panneau Connexions du panneau extensible Contrôleurs de données Azure, sous le contrôleur de données qui les gère.

Pour visualiser le tableau de bord des instances SQL managées pour une instance spécifique, cliquez avec le bouton droit sur l’instance, puis choisissez Gérer.

Le panneau Connexion apparaît sur la droite et vous invite à indiquer la connexion/le mot de passe pour se connecter à cette instance SQL. Si vous connaissez les informations de connexion, vous pouvez les entrer, puis cliquer sur Se connecter.  Si vous ne les connaissez pas, vous pouvez cliquer sur Annuler.  Dans les deux cas, vous accédez au tableau de bord quand le panneau Connexion se ferme.

Sous l’onglet Vue d’ensemble, vous pouvez voir des détails sur l’instance SQL managée, comme le groupe de ressources, le contrôleur de données, l’ID d’abonnement, l’état, la région, etc.  Vous pouvez également voir un lien sur lequel vous pouvez cliquer pour accéder aux tableaux de bord Grafana ou Kibana dans le contexte de cette instance SQL managée.

Si vous êtes en mesure de vous connecter à l’instance SQL managée, vous pouvez voir ici d’autres informations.

Vous pouvez supprimer l’instance SQL managée d’ici ou ouvrir le portail Azure pour afficher l’instance SQL managée dans le portail Azure.

Si vous cliquez sur l’onglet Chaînes de connexion sur la gauche, vous pouvez voir une liste de chaînes de connexion préconstruites pour cette instance SQL managée, ce qui facilite le copier/coller dans d’autres applications ou dans du code.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>Visualiser les tableaux de bord du groupe de serveurs PostgreSQL Hyperscale

Si vous avez créé des groupes de serveurs PostgreSQL Hyperscale, vous pouvez les voir listés dans le panneau Connexions du panneau extensible Contrôleurs de données Azure, sous le contrôleur de données qui les gère.

Pour visualiser le tableau de bord du groupe de serveurs PostgreSQL Hyperscale pour un groupe de serveurs spécifique, cliquez avec le bouton droit sur le groupe de serveurs, puis choisissez Gérer.

Sous l’onglet Vue d’ensemble, vous pouvez voir des détails sur le groupe de serveurs, comme le groupe de ressources, le contrôleur de données, l’ID d’abonnement, l’état, la région, etc.  Vous pouvez également voir un lien sur lequel vous pouvez cliquer pour accéder aux tableaux de bord Grafana ou Kibana dans le contexte de ce groupe de serveurs.

Vous pouvez supprimer le groupe de serveurs d’ici ou ouvrir le portail Azure pour afficher le groupe de serveurs dans le portail Azure.

Si vous cliquez sur l’onglet Chaînes de connexion sur la gauche, vous pouvez voir une liste de chaînes de connexion préconstruites pour ce groupe de serveurs, ce qui facilite le copier/coller dans d’autres applications ou dans du code.

Si vous cliquez sur l’onglet Propriétés à gauche, vous pouvez voir des détails supplémentaires.

Si vous cliquez sur l’onglet Intégrité des ressources à gauche, vous pouvez voir l’état général actuel de ce groupe de serveurs.

Si vous cliquez sur l’onglet Diagnostiquer et résoudre les problèmes sur la gauche, vous pouvez lancer le notebook de résolution des problèmes de PostgreSQL.

Si vous cliquez sur l’onglet Nouvelle demande de support sur la gauche, vous pouvez lancer le portail Azure dans le contexte du groupe de serveurs et créer une demande de support Azure à partir de là.