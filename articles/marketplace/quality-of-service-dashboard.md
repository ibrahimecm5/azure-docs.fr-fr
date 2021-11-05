---
title: Tableau de bord de qualité de service dans l’Espace partenaires
description: Affiche différents rapports disponibles pour la qualité de service dans l’Epace partenaires (Place de marché Azure).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: emuench
ms.author: smannepalle
ms.date: 09/27/2021
ms.openlocfilehash: c7df2a30d3de65966d289c0caad7bc6afaca6afd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088000"
---
# <a name="quality-of-service-qos-dashboard"></a>Tableau de bord de qualité de service (QoS)

Ce tableau de bord affiche la qualité des déploiements pour toutes vos offres. La réussite du déploiement d’une offre plus élevée signifie une qualité de service supérieure à celle offerte à vos clients.

Affichez des représentations graphiques des éléments suivants :

- [Qualité par offres](#quality-by-offers)
- [Qualité par durée de déploiement](#quality-by-deployment-duration)
- [Nombre de déploiements](#deployment-count)
- [Nombre de déploiements par état](#deployment-count-by-status)
- [Codes et ressources des erreurs de déploiement](#deployment-errors-codes-and-resources)
- [Erreurs de déploiement par plan d’offre](#deployment-errors-by-offer-plan)
- [Fiabilité du déploiement par emplacement](#deployment-reliability-by-location)

En outre, affichez les [détails du déploiement de l’offre](#offer-deployment-details) sous forme de tableau.

> [!IMPORTANT]
> Ce tableau de bord n’est actuellement disponible que pour les offres d’**application Azure** disponibles à tous (pas les offres privées).

Cette fonctionnalité est actuellement applicable à tous les partenaires qui effectuent le déploiement d’offres d’applications Azure à l’aide de modèles Azure Resource Manager (ARM) (mais pas pour les offres privées). Ce rapport n’affiche pas les données des autres offres de la place de marché.

## <a name="access-the-quality-of-service-dashboard"></a>Accéder au tableau de bord de qualité de service

[!INCLUDE [preview interface note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vue espaces de travail](#tab/workspaces-view)

1. Se connecter à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home)

1. Dans la page d’accueil, sélectionnez la vignette **Insights**.

    [ ![Illustre la vignette Insights dans l’Espace partenaires.](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. Dans le menu de gauche, sélectionnez **Qualité de service**.

    [ ![Illustre le menu de navigation de gauche sur le tableau de bord Insights. ](./media/quality-of-service/insights-overview-workspaces.png) ](./media/quality-of-service/insights-overview-workspaces.png#lightbox)

#### <a name="current-view"></a>[Affichage actuel](#tab/current-view)

1. Se connecter à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home)

1. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > [**Analyser**](https://partner.microsoft.com/dashboard/commercial-marketplace/quality-of-service/summary) > **Qualité de service**.

---

## <a name="elements-of-the-quality-of-service-dashboard"></a>Éléments du tableau de bord de qualité de service

Les sections suivantes expliquent comment utiliser le tableau de bord Qualité de service (QoS) sur la Place de marché et lire les données.

### <a name="month-range"></a>Plage de mois

#### <a name="workspaces-view"></a>[Vue des espaces de travail](#tab/workspaces-view)

Une sélection de plages de mois est disponible dans le coin supérieur droit de chaque page. Personnalisez la sortie des graphes de la page **Qualité de service** en sélectionnant la plage des 6 ou 12 derniers mois, ou une plage de mois personnalisée d’une durée maximale de 12 mois. La plage de mois (période de sélection) par défaut est de six mois.

[ ![Illustre les filtres sur le tableau de bord Qualité de service. ](./media/quality-of-service/qos-filters-workspaces.png) ](./media/quality-of-service/qos-filters-workspaces.png#lightbox)

#### <a name="current-view"></a>[Affichage actuel](#tab/current-view)

Une sélection de plages de mois est disponible dans le coin supérieur droit de chaque page. Personnalisez la sortie des graphes de la page **Qualité de service** en sélectionnant la plage des 6 ou 12 derniers mois, ou une plage de mois personnalisée d’une durée maximale de 12 mois. La plage de mois (période de sélection) par défaut est de six mois.

---

### <a name="quality-by-offers"></a>Qualité par offres

Ce graphique montre la qualité de service par offres et les références SKU correspondantes. Il fournit des mesures et des tendances pour les déploiements **Total**, **Réussi** et **Échec** sur une base mensuelle. Le graphique à barres représente le nombre de déploiements.

Le graphique en courbes représente le changement de pourcentage dans :

- Total des déploiements (ongle **Tous**)
- Déploiements réussis (onglet **Réussi**)
- Déploiements ayant échoué (onglet **Échec**).

Le graphique montre la mesure et les tendances de toutes les offres. Les premières offres s’affichent dans le graphe et les autres sont regroupées sous **Tout le reste**.

:::image type="content" source="media/quality-of-service/quality-by-offers-1.png" alt-text="Affiche un graphique de qualité par offre, version 1.":::

À propos de ce graphique :

- Sélectionnez des offres spécifiques dans la légende pour afficher uniquement ces offres et les références SKU associées dans le graphique.
- Passez la souris sur une tranche du graphique pour afficher le nombre de déploiements et le pourcentage de cette offre par rapport au nombre total de déploiements de toutes les offres.
- La tendance de la qualité par offre affiche la croissance mensuelle par mois ou les tendances de refus.

:::image type="content" source="media/quality-of-service/quality-by-offers-2.png" alt-text="Affiche un graphique de qualité par offre, version 2.":::

À propos de ce graphique :

- Sélectionnez les offres spécifiques et les références SKU associées dans la légende à afficher.
- La tendance de la qualité par offre affiche des mesures mois par mois.
- Lorsque vous affichez une tendance de mois sur mois pour une offre, sélectionnez un maximum de trois références SKU de cette offre.
- Le graphique en courbes représente les mêmes pourcentages de modification que ceux indiqués pour le graphique précédent.

### <a name="quality-by-deployment-duration"></a>Qualité par durée de déploiement

Ce graphique montre la mesure et la tendance de la durée moyenne d’un déploiement réussi et ayant échoué. Affichez les métriques en sélectionnant une offre dans le menu déroulant. Sélectionnez une référence SKU au format de tableau ou entrez-la dans la barre de recherche. Voici les différentes durées moyennes des déploiements (en minutes) :

- **Durée de réussite** : durée moyenne du déploiement avec l’état des déploiements d’offre marqué comme réussi. Cette mesure agrégée est calculée à l’aide de la durée écoulée entre les timestamps de début et de fin des déploiements marqués avec l’état réussite.
- **Durée d’échec** : durée moyenne du déploiement avec l’état des déploiements d’offre marqué comme ayant échoué. Cette mesure agrégée est calculée en utilisant la durée entre les timestamps de début et de fin des déploiements marqués par l’état d’échec.
- **Durée du premier déploiement réussi** : durée moyenne du déploiement avec un statut de déploiement de l'offre marqué comme réussi. Cette mesure agrégée est calculée à l’aide de la durée comprise entre le timestamp de début du premier déploiement et le timestamp de fin du déploiement final marqué avec l’état réussi. Il est calculé pour chaque déploiement marqué pour une référence SKU d’offre et un client spécifiques.

:::image type="content" source="media/quality-of-service/deployment-duration-quality.png" alt-text="Affiche un graphique de qualité de la durée du déploiement.":::

À propos de ce graphique :

- Sélectionnez Échec, Réussite ou Durée du premier déploiement réussi dans la légende à afficher.
- Le graphique linéaire présente la durée moyenne des déploiements marqués comme ayant réussi, échoué et réussi avec des tentatives précédentes ayant échoué.
- Le temps moyen du premier déploiement tient compte du temps consacré aux tentatives d’échec avant que le déploiement ne soit considéré comme réussi.

### <a name="deployment-count"></a>Nombre de déploiements

Ce graphique montre le déploiement total d’offres. Les métriques et les tendances de croissance sont représentées par un graphique en courbes. Affichez la valeur de chaque mois en pointant sur la ligne de graphe.

La valeur de pourcentage sous les mesures de déploiements représente la quantité de croissance ou de diminution au cours de la plage de mois sélectionnée.

:::image type="content" source="media/quality-of-service/deployment-count.png" alt-text="Affiche un graphique du nombre de déploiements.":::

À propos de ce graphique :

- Nombre total de déploiements d’offre pour la plage de dates sélectionnée.
- Modification du pourcentage de déploiements d’offre pendant la plage de dates sélectionnée.
- Tendance mensuelle du nombre total de déploiements d'offres.

### <a name="deployment-count-by-status"></a>Nombre de déploiements par état

Ce graphique montre la mesure et la tendance des déploiements d’offre réussis et ayant échoué par les clients pour la plage de mois sélectionnée. Les déploiements d’offre peuvent avoir deux états : **Réussi** ou **Échec**.

:::image type="content" source="media/quality-of-service/deployment-count-by-status.png" alt-text="Affiche un graphique de nombre de déploiements par état.":::

À propos de ce graphique :

- Nombre total de déploiements d’offres ayant réussi et échoués pour la plage de dates sélectionnée.
- Modification du pourcentage de déploiements d’offres réussis et échoués pour la plage de dates sélectionnée.
- Tendance mensuelle du nombre de déploiements d'offres réussis ou non.

### <a name="deployment-errors-codes-and-resources"></a>Codes et ressources des erreurs de déploiement

Ce graphique montre les mesures et les tendances des codes d’erreur de base et des ressources des déploiements d’offres. La partie tabulaire peut être pivotée sur les codes d’erreur et les ressources. Le premier sous-onglet fournit des codes d’erreur, une description et des nombres d’erreurs de base analytique. Le second fournit une base analytique pour les ressources de déploiement. Le graphique en courbes fournit le nombre total de codes d'erreur et de ressources.

Pour plus d’informations sur les codes d’erreur, voir [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md) et [Fournisseurs de ressources pour les services Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

:::image type="content" source="media/quality-of-service/deployment-error-codes-1.png" alt-text="Affiche un graphique des codes d’erreur de déploiement, exemple 1.":::
:::image type="content" source="media/quality-of-service/deployment-error-codes-2.png" alt-text="Affiche un graphique des codes d’erreur de déploiement, exemple 2.":::

À propos de ces graphiques :

- Sélectionnez des erreurs ou des ressources spécifiques dans la légende à afficher.
- Le widget de tendance affiche le nombre d’erreurs chaque mois.
- Lorsque vous visualisez une tendance d’un mois sur l'autre par codes d'erreur ou ressources, sélectionnez un maximum de trois éléments dans le tableau.
- Triez les codes d’erreur et les ressources pour les échecs de déploiement par nombre d’erreurs de base dans le tableau.

### <a name="deployment-errors-by-offer-plan"></a>Erreurs de déploiement par plan d’offre

Sur ce graphique, l’axe des Y représente le nombre d’erreurs de déploiement et l’axe des X représente le centile des principaux plans d’offre (par nombre d’erreurs).

:::image type="content" source="media/quality-of-service/deployment-error-by-offer-plan.png" alt-text="Affiche le graphique des erreurs de déploiement par plan d’offre.":::

À propos de ce graphique :

- Les graphiques à barres représentent le nombre d’erreurs de déploiement pour la plage de mois sélectionnée.
- Les valeurs du graphique en courbes représentent les pourcentages d’erreurs cumulés par plan d’offre.

### <a name="deployment-reliability-by-location"></a>Fiabilité du déploiement par emplacement

Ce graphique montre la carte thermique pour les nombres de déploiements réussis et ayant échoué pour la plage de mois sélectionnée. Il affiche également le pourcentage d’échec par rapport à chaque région. L’échelle de couleurs verte à rouge représente la valeur faible à élevée des taux d’échec. Sélectionnez un enregistrement du tableau pour zoomer sur un pays ou une région.

:::image type="content" source="media/quality-of-service/deployment-reliability.png" alt-text="Affiche un graphique de fiabilité du déploiement par emplacement.":::

À propos de ce graphique :

- Déplacez la carte pour afficher l’emplacement exact.
- Zoomez sur un emplacement spécifique.
- La carte thermique présente une grille supplémentaire pour afficher les détails du déploiement du nombre de réussites, du nombre d’échecs et du pourcentage d’échec dans un emplacement spécifique.
- Les régions rouges indiquent des taux d’échec plus élevés et les régions vertes des taux plus faibles.
- Recherchez et sélectionnez un pays/région dans la grille pour zoomer sur l’emplacement dans la carte. Revenez à la vue d’origine avec l’icône **Accueil**.

## <a name="offer-deployment-details"></a>Détails du déploiement de l’offre

Ce tableau montre tous les détails de déploiement de l’offre disponibles. Téléchargez le rapport pour afficher les données brutes sur les déploiements d’offre.

:::image type="content" source="media/quality-of-service/deployment-details.png" alt-text="Affiche un tableau sur les détails du déploiement.":::

À propos de ce tableau :

- Affiche une liste numérotée des 1 000 principaux déploiements triés par date de déploiement.
- Chaque colonne de la grille peut être triée.
- Développez le contrôle et exportez le tableau.
- La vue détaillée est paginée. Sélectionnez d’autres pages en bas.

### <a name="dictionary-of-data-terms"></a>Dictionnaire des termes relatifs aux données

| Nom de la colonne | Nom de l'attribut | Définition |
| --- | --- | --- |
| ID de l’offre | ID de l’offre | Nom de l’offre déployée |
| Sku | Sku | Nom du plan d’offre/de la référence SKU déployé(e) |
| État du déploiement | État du déploiement | L’état du déploiement de l’offre est marqué comme **ayant réussi** ou **échoué** |
| Identifiant d’abonnement | Identifiant d’abonnement | ID d’abonnement du client |
| ID de locataire du client | ID de locataire du client | ID de locataire du client |
| Nom du client | Nom du client | Nom du client |
| Type de modèle | Type de modèle | Type d’Azure App déployée. Il peut s’agir d’applications gérées ou de modèles de solution qui ne peuvent pas être privés. |
| Heure de début du déploiement | Heure de début du déploiement | Heure de début du déploiement |
| Heure de fin du déploiement | Heure de fin du déploiement | Heure de fin du déploiement |
| Durée du déploiement : | Durée du déploiement : | Durée totale d’exécution du déploiement de l’offre, en millisecondes. Elle est affichée en minutes dans le graphique. |
| Région du déploiement | Région du déploiement | Emplacement du déploiement d’Azure App |
| Fournisseur de ressources | Fournisseur de ressources | Fournisseur de ressources pour la ressource déployée particulière |
| URI de ressource | URI de ressource | URI de la ressource déployée |
| Groupe de ressources | Groupe de ressources | Groupe de ressources dans lequel la ressource est déployée |
| Type de ressource | Type de ressource | Type de ressource déployée |
| Nom de la ressource | Nom de la ressource | Nom de la ressource déployée |
| Code d'erreur | Code d'erreur | Code d’erreur de l’échec du déploiement |
| Nom de l’erreur | Nom de l’erreur | Nom d’erreur de l’échec du déploiement |
| Message d’erreur | Message d’erreur | Message d’erreur de l’échec du déploiement |
| Code d’erreur profond | Code d’erreur profond | Le cas échéant, contient des informations supplémentaires sur le code d’erreur |
| Code de message profond | Code de message profond | Le cas échéant, contient des informations supplémentaires sur le message d’erreur |
| ID de corrélation : | ID de corrélation : | Identificateur utilisé pour distinguer les différents déploiements. Une valeur identique signifie que toutes les ressources déployées sont destinées à un seul déploiement. |
|

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les erreurs de déploiement, consultez [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).
- Pour plus d’informations sur les fournisseurs de ressources, consultez [Fournisseurs de ressources pour les services Azure](../azure-resource-manager/management/azure-services-resource-providers.md).
- Pour consulter les graphes, tendances et valeurs des données agrégées qui résument l’activité de la place de marché pour vos offres, voir [Tableau de bord Résumé dans l’analytique de la place de marché commerciale](./summary-dashboard.md).
- Pour plus d’informations sur vos commandes dans un format graphique et téléchargeable, consultez [Tableau de bord Commandes dans l’analytique de la Place de marché commerciale](./orders-dashboard.md).
- Pour voir les métriques de facturation à l’utilisation des offres de machines virtuelles, consultez [Tableau de bord Utilisation dans l’analytique de la Place de marché commerciale](./usage-dashboard.md).
- Pour obtenir des informations détaillées sur vos clients, y compris des tendances de croissance, consultez [Tableau de bord Client dans l’analytique du marketplace commercial](./customer-dashboard.md).
- Pour obtenir des informations sur vos licences, consultez [Tableau de bord des licences dans l’analyse de la place de marché commerciale](./license-dashboard.md).
- Pour obtenir la liste de vos demandes de téléchargement des 30 derniers jours, voir [Tableau de bord des téléchargements dans l’analytique de la place de marché commerciale](./downloads-dashboard.md).
- Pour une vue centralisée des commentaires clients sur les offres de Microsoft AppSource et de la Place de marché Azure, consultez [Tableau de bord Évaluations et avis dans l’analytique de la Place de marché commerciale](./ratings-reviews.md).
- Pour voir les FAQ sur l’analytique de la place de marché commerciale et un dictionnaire complet des termes liés aux données, consultez [Questions fréquentes relatives à l’analytique de la place de marché commerciale](./analytics-faq.yml).