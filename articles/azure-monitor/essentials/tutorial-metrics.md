---
title: 'Tutoriel : Analyser les métriques d’une ressource Azure'
description: Découvrez comment analyser les métriques d’une ressource Azure avec Metrics Explorer dans Azure Monitor
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: fc996a635caea212d185b54c5493bf781d098f57
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350646"
---
# <a name="tutorial-analyze-metrics-for-an-azure-resource"></a>Tutoriel : Analyser les métriques d’une ressource Azure
Les métriques sont des valeurs numériques qui sont collectées automatiquement à intervalles réguliers et décrivent un certain aspect d’une ressource. Par exemple, une métrique peut vous indiquer l’utilisation du processeur d’une machine virtuelle, l’espace libre dans un compte de stockage ou le trafic entrant pour un réseau virtuel. Metrics Explorer est une fonctionnalité d’Azure Monitor dans le portail Azure qui vous permet de créer des graphiques à partir de valeurs de métriques, de corréler visuellement des tendances et d’examiner des pics et des creux dans des valeurs de métriques. Utilisez Metrics Explorer pour tracer des graphiques à partir de métriques créées par vos ressources Azure, et investiguez leur intégrité et leur utilisation. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Ouvrir Metrics Explorer pour une ressource Azure
> * Sélectionner une métrique pour un effectuer un tracé sur un graphique
> * Effectuer différentes agrégations de valeurs de métriques.
> * Modifier l’intervalle de temps et la granularité du graphique.


Voici une vidéo qui présente un scénario plus détaillé que la procédure décrite dans cet article. Si vous débutez avec les métriques, nous vous suggérons de d’abord lire cet article, puis de regarder la vidéo pour plus de détails. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Prérequis
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants : 

- Une ressource Azure à superviser. Vous pouvez utiliser n’importe quelle ressource de votre abonnement Azure qui prend en charge les métriques. Pour déterminer si une ressource prend en charge les métriques, accédez à son menu dans le portail Azure et vérifiez qu’une option **Métriques** figure dans la section **Supervision** du menu.


## <a name="open-metrics-explorer"></a>Ouvrir Metrics Explorer 
Sélectionnez **Métriques** dans la section **Monitoring** du menu de votre ressource. L’étendue est déjà remplie avec votre ressource. L’exemple ci-dessous concerne un compte de stockage, mais cela se présente de la même façon pour les autres services Azure.

:::image type="content" source="media/tutorial-metrics/metrics-menu.png" lightbox="media/tutorial-metrics/metrics-menu.png" alt-text="Élément de menu Métriques":::

Sélectionnez un **Espace de noms** si l’étendue en a plusieurs. L’espace de noms est simplement une méthode d’organisation des métriques pour que vous puissiez les retrouver facilement. Par exemple, les comptes de stockage ont des espaces de noms distincts pour le stockage des métriques de fichiers, tables, objets Blob et files d’attente. Plusieurs types de ressources n’ont qu’un seul espace de noms.

Sélectionnez une métrique dans la liste des métriques disponibles pour l’étendue et l’espace de noms sélectionnés.

:::image type="content" source="media/tutorial-metrics/metric-picker.png" lightbox="media/tutorial-metrics/metric-picker.png" alt-text="Sélectionner l’espace de noms et la métrique":::

Si vous le souhaitez, modifiez l’**Agrégation** des métriques. Cela définit la manière dont les valeurs des métriques sont agrégées en fonction de la granularité temporelle du graphique. Par exemple, si la granularité temporelle est définie sur 15 minutes et que l’agrégation est définie sur sum, chaque point du graphique correspond à la somme de toutes les valeurs collectées durant chaque segment de 15 minutes.

:::image type="content" source="media/tutorial-metrics/chart.png" lightbox="media/tutorial-metrics/chart.png" alt-text="Capture d’écran montrant un graphique intitulé Sum Ingress for contosoretailweb":::


Utilisez le bouton **Ajouter une métrique** et répétez ces étapes si vous souhaitez afficher plusieurs métriques tracées dans le même graphique. Pour afficher plusieurs graphiques dans une même vue, sélectionnez le bouton **Nouveau graphique**.

## <a name="select-a-time-range-and-granularity"></a>Sélectionner un intervalle de temps et une granularité

Par défaut, le graphique affiche les dernières 24 heures de données de métriques. Utilisez le sélecteur d’heure pour changer l’**Intervalle de temps** du graphique ou la **Granularité temporelle** qui définit l’intervalle de temps pour chaque point de données. Le graphique utilise l’agrégation spécifiée pour calculer toutes les valeurs échantillonnées durant la granularité temporelle spécifiée.

:::image type="content" source="media/tutorial-metrics/time-picker.png" lightbox="media/tutorial-metrics/time-picker.png" alt-text="Modifier le panneau d’intervalle de temps":::

Utilisez le curseur de **balayage temporel** pour étudier une zone intéressante du graphique, telle qu’un pic ou une baisse. Placez le pointeur de la souris au début de la zone, cliquez et maintenez le bouton gauche de la souris enfoncé, faites glisser de l’autre côté de la zone, puis relâchez le bouton. Cet intervalle de temps sera agrandi dans le graphique. 

:::image type="content" source="media/tutorial-metrics/time-brush.png" lightbox="media/tutorial-metrics/time-brush.png" alt-text="Curseur de balayage temporel":::

## <a name="apply-dimension-filters-and-splitting"></a>Appliquer des filtres de dimension et un fractionnement
Pour découvrir des fonctionnalités avancées qui vous permettent d’effectuer des analyses supplémentaires de vos métriques et d’identifier les valeurs hors norme potentielles dans vos données, consultez les références suivantes.

- Le [filtrage](../essentials/metrics-charts.md#filters) vous permet de choisir les valeurs de dimension à inclure dans le graphique. Par exemple, vous souhaiterez peut-être afficher uniquement les requêtes ayant réussi dans la représentation graphique d’une métrique de *temps de réponse du serveur*. 

- Le [fractionnement](../essentials/metrics-charts.md#apply-splitting) contrôle si le graphique affiche des lignes distinctes pour chaque valeur d’une dimension ou agrège les valeurs dans une seule ligne. Par exemple, vous souhaiterez peut-être voir une ligne pour le temps de réponse moyen sur toutes les instances de serveur, ou voir des lignes distinctes pour chaque serveur. 

Consultez les [exemples de graphiques](../essentials/metric-chart-samples.md) auxquels un filtrage et un fractionnement sont appliqués.

## <a name="advanced-chart-settings"></a>Paramètres de graphique avancés

Vous pouvez personnaliser le style et le titre du graphique, et modifier les paramètres de graphique avancés. Lorsque vous avez terminé la personnalisation, épinglez-le à un tableau de bord pour enregistrer votre travail. Vous pouvez également configurer des alertes de métriques. Pour en savoir plus sur ces fonctionnalités et d’autres fonctionnalités avancées d’Azure Monitor Metrics Explorer, consultez [Fonctionnalités avancées d’Azure Metrics Explorer](../essentials/metrics-charts.md#locking-the-range-of-the-y-axis).


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment utiliser les métriques dans Azure Monitor, découvrez comment créer une règle d’alerte de métrique pour être averti quand une valeur de métrique indique un problème potentiel.

> [!div class="nextstepaction"]
> [Créer une alerte de métrique dans Azure Monitor](../alerts/tutorial-metric-alert.md)

