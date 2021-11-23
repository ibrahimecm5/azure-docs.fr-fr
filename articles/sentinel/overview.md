---
title: Présentation de Microsoft Sentinel | Microsoft Docs
description: Découvrez Microsoft Sentinel, une solution native cloud et scalable de type SIEM (Security Information and Event Management) et SOAR (Security Orchestrated Automated Response).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 40cfdc1d23b57bbda16e79907d226134e0052134
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523483"
---
# <a name="what-is-microsoft-sentinel"></a>Présentation de Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel est une solution native Cloud et évolutive de type **SIEM (Security Information and Event Management)** et **SOAR (Security Orchestrated Automated Response)** . Microsoft Sentinel assure une analyse de sécurité intelligente et fournit des renseignements sur les menaces dans l’ensemble de l’entreprise. Elle constitue une solution unique pour la détection des alertes, la visibilité des menaces, la chasse proactive et la réponse aux menaces.

Microsoft Sentinel vous offre une vue d’ensemble de l’organisation, ce qui réduit le stress lié aux attaques de plus en plus sophistiquées, aux volumes croissants d’alertes et aux longs délais de résolution.

- **Collectez des données à l’échelle du cloud** sur l’ensemble des utilisateurs, appareils, applications et infrastructures, tant locaux que dans de multiples clouds.

- **Détectez les menaces non détectées précédemment** et [réduisez les faux positifs](false-positives.md) en vous appuyant sur l’analytique et les systèmes de renseignement incomparables sur les menaces fournis par Microsoft.

- **Investiguez les menaces à l’aide de l’intelligence artificielle** et recherchez les activités suspectes à grande échelle en profitant des années de travail que Microsoft a consacrées à la cybersécurité.

- **Répondez aux incidents rapidement** avec une orchestration et une automatisation intégrées des tâches courantes.

![Fonctionnalités principales de Microsoft Sentinel](./media/overview/core-capabilities.png)

S’appuyant sur la gamme complète des services Azure existants, Microsoft Sentinel intègre de façon native les éléments de base comme Log Analytics et Logic Apps. Microsoft Sentinel enrichit vos opérations d’investigation et de détection avec l’intelligence artificielle et fournit les flux d’informations sur les menaces venant de Microsoft et vous permet de mettre en place votre propre intelligence face aux menaces.

## <a name="connect-to-all-your-data"></a>Connexion à toutes vos données

Pour intégrer Microsoft Sentinel, vous devez d’abord [vous connecter à vos sources de sécurité](connect-data-sources.md). 

Microsoft Sentinel est fourni avec un certain nombre de connecteurs pour solutions Microsoft, prêts à l’emploi et offrant une intégration en temps réel, comme les solutions Microsoft 365 Defender (anciennement Protection Microsoft contre les menaces), les sources Microsoft 365, dont Office 365, Azure AD, Microsoft Defender pour Identity (anciennement Azure ATP) et Microsoft Cloud App Security. En outre, il existe des connecteurs intégrés pour la connexion aux écosystèmes de sécurité élargis pour les solutions non Microsoft. Vous pouvez également utiliser le format d’événement commun, Syslog ou l’API REST pour connecter vos sources de données à Microsoft Sentinel. 

Pour plus d’informations, consultez [Rechercher votre connecteur de données](data-connectors-reference.md).

![Collecteurs de données](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Workbooks

Après avoir [connecté vos sources de données](quickstart-onboard.md) à Microsoft Sentinel, vous pouvez surveiller les données à l’aide de l’intégration Microsoft Sentinel avec les classeurs Azure Monitor, ce qui offre une grande souplesse lors de la création de classeurs personnalisés.

Même si les classeurs s’affichent différemment dans Microsoft Sentinel, il peut être utile de voir comment [créer des rapports interactifs avec les classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md). Microsoft Sentinel vous permet de créer des classeurs personnalisés sur l’ensemble de vos données. Il est également fourni avec des modèles de classeurs intégrés avec lesquels vous obtenez rapidement des insights sur vos données dès que vous connectez une source de données.

![Tableaux de bord](./media/tutorial-monitor-data/access-workbooks.png)

- Les classeurs sont destinés aux ingénieurs et analystes SOC de tous niveaux pour visualiser les données.

- Bien que les classeurs soient particulièrement adaptés pour être utilisés pour des vues d’ensemble des données Microsoft Sentinel et ne nécessitent aucune connaissance du codage, vous ne pouvez pas intégrer des classeurs à des données externes.

## <a name="analytics"></a>Analytics

Pour vous aider à réduire le niveau de bruit et à réduire le nombre d’alertes que vous devez examiner, Microsoft Sentinel utilise l’[analytique pour mettre en corrélation les alertes et les incidents](detect-threats-built-in.md). Les **incidents** sont des groupes d’alertes liées qui, prises ensemble, constituent une menace exploitable que vous pouvez examiner et résoudre. Utilisez les règles de corrélation intégrées telles quelles ou utilisez-les comme point de départ pour créer vos propres règles. Microsoft Sentinel fournit également des règles d’apprentissage machine pour mapper le comportement de votre réseau et rechercher les possibles anomalies sur vos ressources. Ces analyses connectent ensuite les informations en transformant les alertes basse fidélité sur différentes entités en incidents de sécurité potentiels de haute fidélité.

![Incidents](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Automatisation et orchestration de la sécurité

Automatisez les tâches courantes et [simplifiez l’orchestration de la sécurité avec des playbooks](tutorial-respond-threats-playbook.md) qui s’intègrent aux services Azure et à vos outils.

Basée sur Azure Logic Apps, la solution d’automatisation et d’orchestration de Microsoft Sentinel offre une architecture hautement extensible qui permet une automatisation scalable lorsque de nouvelles technologies et menaces émergent. Pour créer des playbooks avec Azure Logic Apps, vous pouvez faire votre choix dans une galerie croissante de playbooks intégrés. Ceux-ci incluent [plus de 200 connecteurs](../connectors/apis-list.md) pour différents services comme Azure Functions. Ces connecteurs vous permettent d’appliquer une logique personnalisée dans votre code, ServiceNow, Jira, Zendesk, les requêtes HTTP, Microsoft Teams, Slack, Windows Defender ATP et Defender for Cloud Apps.

Par exemple, si vous utilisez le système de tickets ServiceNow, vous pouvez utiliser les outils fournis pour utiliser Azure Logic Apps et automatiser vos flux de travail et ouvrir un ticket dans ServiceNow chaque fois qu’un événement en particulier est détecté.

![Playbooks](./media/tutorial-respond-threats-playbook/logic-app.png)

- Les playbooks sont destinés aux ingénieurs et analystes SOC de tous niveaux, pour automatiser et simplifier les tâches, notamment l’ingestion, l’enrichissement, l’investigation et la correction des données.

- Les playbooks fonctionnent le mieux avec des tâches uniques et reproductibles, et ne nécessitent aucune connaissance du codage. Les playbooks ne conviennent pas pour les chaînes de tâches ad hoc ou complexes, ni pour la documentation et le partage des preuves.


## <a name="investigation"></a>Investigation

Actuellement en préversion, les outils d’[investigation approfondie](investigate-cases.md) Microsoft Sentinel vous permettent de mieux comprendre l’étendue d’une menace de sécurité potentielle et d’en identifier la cause racine. Vous pouvez choisir une entité sur le graphique interactif pour poser des questions sur une entité spécifique et approfondir cette entité et ses connexions pour arriver à la cause racine de la menace. 

![Investigation](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Chasse

Utilisez les [puissants outils de recherche et de requête](hunting.md) de Microsoft Sentinel, basés sur l’infrastructure MITRE, et qui vous permettent de façon proactive de rechercher les menaces de sécurité sur toutes les sources de données de votre organisation avant même le déclenchement d’une alerte. Une fois que vous avez découvert quelle requête de recherche fournit des informations d’importance sur les attaques possibles, vous pouvez également créer des règles de détection personnalisées basées sur cette requête et communiquer ces informations sous forme d’alertes au personnel en charge de la gestion des incidents de sécurité. Lors de la recherche, vous pouvez créer des signets pour les événements intéressants, ce qui vous permet d’y revenir plus tard, de les partager avec d’autres utilisateurs et de les regrouper avec d’autres événements en corrélation pour créer un incident d’investigation solide.

![Vue d'ensemble de la fonctionnalité de repérage](./media/overview/hunting.png)

## <a name="notebooks"></a>Notebooks

Microsoft Sentinel prend en charge les notebooks Jupyter dans les espaces de travail Azure Machine Learning, y compris des bibliothèques complètes pour le Machine Learning, la visualisation et l’analyse des données.

[Utilisez des notebooks dans Microsoft Sentinel](notebooks.md) pour élargir l’étendue de ce que vous pouvez faire avec les données Microsoft Sentinel. Par exemple, effectuez des analyses qui ne sont pas intégrées à Microsoft Sentinel, telles que des fonctionnalités de Machine Learning Python, créez des visualisations de données qui ne sont pas intégrées à Microsoft Sentinel, telles que des chronologies personnalisées et des arbres de processus, ou intégrez des sources de données en dehors de Microsoft Sentinel, comme un jeu de données local.

:::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="Capture d’écran d’un ,notebook Sentinel dans un espace de travail AML":::

- Les notebooks Microsoft Sentinel sont destinés aux chasseurs de menaces ou aux analystes de niveau 2-3, aux enquêteurs d’incident, aux scientifiques de données et aux chercheurs en sécurité.

- Les notebooks fournissent des requêtes pour les données Microsoft Sentinel et externes, ainsi que des fonctionnalités d’enrichissement des données, d’investigation, de visualisation, de chasse, de Machine Learning et d’analytique du Big Data.

- Les notebooks sont parfaitement adaptés à des chaînes plus complexes de tâches reproductibles, à des contrôles de procédure ad hoc, au Machine Learning et à l’analyse personnalisée. Ils prennent en charge de riches bibliothèques Python pour la manipulation et la visualisation des données, et sont utiles pour la documentation et le partage des preuves d’analyse.

- Les notebooks nécessitent une courbe d’apprentissage plus élevée et des connaissances en matière de codage, et bénéficient d’une prise en charge d’automatisation limitée.


## <a name="community"></a>Communauté

La communauté Microsoft Sentinel est une ressource importante pour l’automatisation et la détection des menaces. Nos analystes de sécurité Microsoft créent et ajoutent constamment de nouveaux classeurs, de nouveaux playbooks, de nouvelles requêtes de recherche, entre autres, et les publient dans la communauté pour que vous puissiez les utiliser dans votre environnement. Vous pouvez télécharger l’exemple de contenu à partir du [dépôt](https://aka.ms/asicommunity) GitHub de la communauté privée pour créer des classeurs, requêtes de recherche, notebooks et playbooks personnalisés pour Microsoft Sentinel.

![Explorer la communauté des utilisateurs](./media/overview/community.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour utiliser Microsoft Sentinel, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Découvrez comment [intégrer vos données à Microsoft Sentinel](quickstart-onboard.md) et [obtenir une visibilité de vos données et des menaces](get-visibility.md).
