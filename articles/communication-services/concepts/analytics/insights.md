---
title: Azure Communication Services - fonctionnalité Insights en préversion
titleSuffix: An Azure Communication Services concept document
description: Descriptions des visualisations de données disponibles pour Communication Services via Workbooks
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 1a11cc68d197d53ab2348c7b6ae625c6659a6884
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138884"
---
# <a name="communications-services-insights-preview"></a>Communication Services - fonctionnalité Insights en préversion

## <a name="overview"></a>Vue d’ensemble
Dans votre ressource de communications, nous avons fourni une fonctionnalité **Insights en préversion** qui affiche un certain nombre de visualisations de données donnant un aperçu des journaux et des métriques Azure Monitor surveillés pour vos Communication Services. Dans Insights, les visualisations sont rendues possibles via [Azure Monitor Workbooks](/azure/azure-monitor/visualize/workbooks-overview). Pour tirer parti de Workbooks, suivez les instructions décrites dans [Activer Azure Monitor dans Paramètres de Diagnostic](enable-logging.md), et pour activer Workbooks, vous devez envoyer vos journaux à une destination d’[espace de travail Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) . 

:::image type="content" source="media\workbooks\insights-overview-2.png" alt-text="Insights Communication Services":::

## <a name="accessing-azure-insights-for-communication-services"></a>Accès à Azure Insights pour Communication Services

1. Dans la page d’accueil du **Portail Azure**, sélectionnez votre ressource **Communication Service** :

    :::image type="content" source="media\workbooks\azure-portal-home-browser.png" alt-text="Accueil du Portail Azure":::

2. Une fois à l’intérieur de votre ressource, faites défiler la barre de navigation gauche vers la catégorie **Analyse**, puis cliquez sur l’onglet **Insights** :

    :::image type="content" source="media\workbooks\acs-insights-nav.png" alt-text="Navigation dans les Insights":::

3. Cela doit afficher le tableau de bord **Insights** pour votre ressource Communication Service :

    :::image type="content" source="media\workbooks\acs-insights-tab.png" alt-text="Onglet Insights de Communication Services":::

## <a name="insights-dashboard-navigation"></a>Navigation dans le tableau de bord Insights

Les tableaux de bord Insights de Communications Service offrent aux utilisateurs un moyen intuitif et clair de naviguer dans les données des journaux de leurs ressources. La section **Vue d’ensemble** offre un affichage sur toutes les modalités, de sorte que l'utilisateur peut voir les différentes façons dont sa ressource a été utilisée lors d’un intervalle de temps spécifique :

:::image type="content" source="media\workbooks\overview.png" alt-text="Vue d’ensemble Insights":::

Les utilisateurs peuvent contrôler l’intervalle de temps et la granularité temporelle à afficher avec les paramètres affichés en haut :

:::image type="content" source="media\workbooks\time-range-param.png" alt-text="Paramètre d’intervalle de temps":::

Ces paramètres sont globaux, ce qui signifie qu’ils mettent à jour les données affichées dans l’ensemble du tableau de bord.

La section **Vue d’ensemble** contient un paramètre supplémentaire pour contrôler le type de visualisation affiché :

:::image type="content" source="media\workbooks\plot-type-param.png" alt-text="Paramètre de type de tracé":::

Ce paramètre est local, ce qui signifie qu’il affecte uniquement les tracés dans cette section.

Les autres onglets affichent les données de journal liées à une modalité spécifique.

:::image type="content" source="media\workbooks\main-nav.png" alt-text="Navigation principale":::

Étant donné que les journaux **voix et vidéo** sont les plus complexes par nature, cette modalité est divisée en quatre sous-sections :

:::image type="content" source="media\workbooks\voice-and-video-nav.png" alt-text="Navigation voix et vidéo":::

L’onglet **Résumé** contient des informations générales sur l’utilisation de la voix et de la vidéo, y compris les types de flux de données multimédias partagés, les types de points de terminaison qui participent à un appel (par exemple, VoIP, bot, application, réseau téléphonique commuté ou serveur), l’utilisation du système d’exploitation et les raisons de fin du participant :

:::image type="content" source="media\workbooks\voice-and-video-summary.png" alt-text="Résumé voix et vidéo":::

L’onglet **Volume** sous la modalité **Voix et vidéo** affiche le nombre d’appels et le nombre de participants dans une période spécifique (paramètre d’**intervalle de temps**), sous-divisés en emplacements temporels (paramètre de **granularité de temps**) :

:::image type="content" source="media\workbooks\voice-and-video-volume.png" alt-text="Volume voix et vidéo":::

L’onglet **Volume** contient un paramètre **Regroupement** qui permet de visualiser le nombre d’appels et de participants segmentés par type d’appel (appels P2P et appels de groupe) et les appels d’interopérabilité (services ACS (Azure Communication Services) purs et interopérabilité avec Teams) :

:::image type="content" source="media\workbooks\voice-and-video-volume-grouping.png" alt-text="Regroupement de volumes voix et vidéo":::

L’onglet **Qualité** sous **Voix et vidéo** permet aux utilisateurs d’inspecter la distribution de qualité des appels, où la qualité est définie à trois niveaux pour ce tableau de bord :

- Proportion de flux média de mauvaise qualité (tracé **qualité Stream**), où la qualité d’un flux est classée comme mauvaise lorsque celui-ci présente au moins une valeur de télémétrie non saine, où les plages non saines sont définies comme suit :
  - Gigue > 30 millisecondes
  - Taux de perte de paquets > 10 %
  - Durée d’aller-retour > 500 millisecondes

- Proportion d’**appels impactés**, où un appel impacté correspond à un appel qui possède au moins un flux de qualité médiocre

- **Raisons de fin du participant**, qui assurent le suivi de la raison pour laquelle un participant a quitté un appel. Les raisons de fin sont les [codes SIP](https://en.wikipedia.org/wiki/List_of_SIP_response_codes), c’est-à-dire des codes numériques qui décrivent l’état spécifique d’une requête de signalisation. Les codes SIP peuvent être regroupés en six catégories : *Success* (Réussite), *Client Failure* (Défaillance du client), *Server Failure* (Défaillance du serveur), *Global Failure* (Défaillance générale), *Redirection* et *Provisional* (Provisoire). La distribution des catégories de code SIP est indiquée dans le graphique à secteurs situé à gauche, tandis qu’une liste des codes SIP spécifiques pour les motifs de fin de participant est proposée à droite.

:::image type="content" source="media\workbooks\voice-and-video-quality.png" alt-text="Qualité voix et vidéo":::

La qualité peut également être filtrée en fonction des types de flux de média (paramètre **Type de média** ) utilisés dans l’appel, par exemple pour recevoir uniquement les appels impactés en termes de qualité de flux vidéo :

:::image type="content" source="media\workbooks\voice-and-video-quality-params.png" alt-text="Paramètre de type de média de qualité voix et vidéo":::

Et peuvent également être filtrés par types de points de terminaison (paramètre **Type de point de terminaison** ), p. ex., en obtenant les raisons de fin de participant pour les participants RTC. Ces filtres autorisent les sélections multiples :

:::image type="content" source="media\workbooks\voice-and-video-params-2.png" alt-text="Paramètre de type de point de terminaison de qualité voix et vidéo":::

L'onglet **Détails** offre un moyen rapide de naviguer dans les appels **Voix et vidéo** effectués dans une période donnée en regroupant les appels par dates et en affichant les détails de chaque appel effectué en termes de participants à cet appel et de flux sortants par participant, ainsi que la durée et les valeurs de télémétrie pour ce qui suit :

:::image type="content" source="media\workbooks\voice-and-video-details.png" alt-text="Détails sur la voix et la vidéo":::

Les détails d’un appel sont masqués initialement. La liste des participants s’affiche une fois que vous avez cliqué sur un appel :

:::image type="content" source="media\workbooks\voice-and-video-details-participants.png" alt-text="Détails du participant voix et vidéo":::

En cliquant sur un participant, vous affichez la liste des flux sortants pour celui-ci, ainsi que leur durée (proportionnelle à la durée de l’appel complet) et les valeurs de télémétrie, parmi lesquelles les valeurs non intègres sont affichées en rouge :

:::image type="content" source="media\workbooks\voice-and-video-details-streams.png" alt-text="Détails du flux voix et vidéo":::

L’onglet **Authentification** affiche les journaux d’authentification, qui sont créés par le biais d’opérations telles que l’émission d’un jeton d’accès ou la création d’une identité. Les données affichées incluent les types d’opérations effectuées et les résultats de ces opérations :

:::image type="content" source="media\workbooks\auth.png" alt-text="Onglet Authentification":::

L’onglet **Conversation** affiche les données pour toutes les opérations liées à la conversation et leurs types de résultats :

:::image type="content" source="media\workbooks\chat.png" alt-text="Onglet Conversation":::

L’onglet **SMS** affiche les opérations et les résultats de l’utilisation de SMS par le biais d’une ressource ACS (nous n’avons actuellement aucune donnée pour cette modalité) :

:::image type="content" source="media\workbooks\sms.png" alt-text="Onglet SMS":::

## <a name="editing-dashboards"></a>Modification de tableaux de bord

Vous pouvez personnaliser les tableaux de bord **Insights** fournis avec votre ressource **Communication Service** en cliquant sur le bouton **Modifier** dans la barre de navigation supérieure :

:::image type="content" source="media\workbooks\dashboard-editing.png" alt-text="Modification de tableau de bord":::

La modification de ces tableaux de bord ne modifie pas l’onglet **Insights**, mais crée un classeur distinct accessible dans l’onglet Workbooks de votre ressource :

:::image type="content" source="media\workbooks\workbooks-tab.png" alt-text="Onglet Workbooks":::

Pour une description détaillée des classeurs, reportez-vous à la documentation [Azure Monitor Workbooks](/azure/azure-monitor/visualize/workbooks-overview).