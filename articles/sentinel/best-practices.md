---
title: Meilleures pratiques pour Microsoft Sentinel
description: Découvrez les meilleures pratiques à employer pour gérer votre espace de travail Microsoft Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 15543989655bf094f221a4259ad0b3e765338329
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524699"
---
# <a name="best-practices-for-microsoft-sentinel"></a>Meilleures pratiques pour Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cette collection de meilleures pratiques fournit des conseils à utiliser lors du déploiement, de la gestion et de l’utilisation de Microsoft Sentinel, y compris des liens vers d’autres articles pour plus d’informations.

> [!IMPORTANT]
> Avant de déployer Microsoft Sentinel, examinez et suivez les [prérequis et les activités de prédéploiement](prerequisites.md).
>


## <a name="best-practice-references"></a>Références des meilleures pratiques

La documentation de Microsoft Sentinel contient une aide relative aux meilleures pratiques, disséminée dans nos articles. En plus du contenu fourni dans cet article, consultez les éléments suivants pour plus d’informations :

- **Utilisateurs administrateurs** :

    - [Activités de prédéploiement et prérequis pour le déploiement de Microsoft Sentinel](prerequisites.md)
    - [Meilleures pratiques pour l’architecture de l’espace de travail Microsoft Sentinel](best-practices-workspace-architecture.md)
    - [Concevoir votre architecture d’espace de travail Microsoft Sentinel](design-your-workspace-architecture.md)
    - [Exemples de conceptions d’espace de travail Microsoft Sentinel](sample-workspace-designs.md)
    - [Meilleures pratiques de collecte de données](best-practices-data.md)
    - [Coûts et facturation de Microsoft Sentinel](azure-sentinel-billing.md)
    - [Autorisations dans Microsoft Sentinel](roles.md)
    - [Protection de la propriété intellectuelle des MSSP dans Microsoft Sentinel](mssp-protect-intellectual-property.md)
    - [Intégration du renseignement sur les menaces dans Microsoft Azure Sentinel](threat-intelligence-integration.md)
    - [Auditer les requêtes et les activités Microsoft Sentinel](audit-sentinel-data.md)

- **Analystes** :

    - [Playbooks recommandés](automate-responses-with-playbooks.md#recommended-playbooks)
    - [Gérer les faux positifs dans Microsoft Azure Sentinel](false-positives.md)
    - [Recherche de menaces avec Microsoft Sentinel](hunting.md)
    - [Classeurs Microsoft Sentinel couramment utilisés](top-workbooks.md)
    - [Détection des menaces prête à l’emploi](detect-threats-built-in.md)
    - [Créer des règles d’analytique personnalisées pour détecter des menaces](detect-threats-custom.md)
    - [Utiliser Jupyter Notebook pour rechercher des menaces de sécurité](notebooks.md)

Pour plus d’informations, consultez également notre vidéo : [Architecting SecOps for Success: Best Practices for Deploying Microsoft Sentinel](https://youtu.be/DyL9MEMhqmI)

## <a name="regular-soc-activities-to-perform"></a>Activités normales du centre des opérations de sécurité à effectuer

Planifiez régulièrement les activités Microsoft Sentinel suivantes pour garantir le maintien des meilleures pratiques en matière de sécurité :

### <a name="daily-tasks"></a>Tâches quotidiennes

- **Tri et investigation des incidents**.  Consultez la page **Incidents** de Microsoft Sentinel pour vérifier les nouveaux incidents générés par les règles d’analyse actuellement configurées, et commencez à enquêter sur tout nouvel incident. Pour plus d’informations, consultez le didacticiel :[Examiner les incidents avec Microsoft Sentinel](investigate-cases.md).

- **Exploration des requêtes de chasse et des signets.** . Explorez les résultats de toutes les requêtes intégrées et mettez à jour les requêtes de chasse et les signets existants. Générez manuellement de nouveaux incidents ou mettez à jour d’anciens incidents, le cas échéant.  Pour plus d'informations, consultez les pages suivantes :

    - [Créer automatiquement des incidents à partir d’alertes de sécurité Microsoft](create-incidents-from-alerts.md)
    - [Recherche de menaces avec Microsoft Sentinel](hunting.md)
    - [Effectuer le suivi des données pendant la chasse avec Microsoft Sentinel](bookmarks.md)

- **Règles analytiques**.  Examinez et activez les nouvelles règles d’analyse le cas échéant, y compris les règles nouvellement publiées ou nouvellement disponibles à partir de connecteurs de données récemment connectés.

- **Connecteurs de données**. Vérifiez l’état, la date et l’heure du dernier journal reçu de chaque connecteur de données pour vous assurer que les données sont transmises. Recherchez la présence de nouveaux connecteurs et vérifiez l’ingestion pour vous assurer que les limites définies n’ont pas été dépassées. Pour plus d’informations, consultez [Meilleures pratiques de collecte de données](best-practices-data.md) et [Connecter des sources de données](connect-data-sources.md).

- **Agent Log Analytics**. Vérifiez que les serveurs et les stations de travail sont connectés activement à l’espace de travail, puis résolvez les problèmes et corrigez tout échec de connexion.   Pour plus d’informations, consultez [Présentation de l’agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md).

- **Échecs de playbook.** . Vérifiez les états d’exécution des playbooks et résolvez tout échec.   Pour plus d’informations, consultez [Tutoriel : Utiliser des playbooks avec des règles d’automatisation dans Microsoft Sentinel](tutorial-respond-threats-playbook.md).

### <a name="weekly-tasks"></a>Tâches hebdomadaires

- **Mises à jour des classeurs**. Vérifiez si des classeurs ont des mises à jour qui doivent être installées. Pour plus d’informations, consultez [Classeurs Microsoft Sentinel courants](top-workbooks.md).

- **Analyse du dépôt Github Microsoft Sentinel**. Passez en revue le [référentiel GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel) pour voir s’il existe des ressources nouvelles ou mises à jour utiles pour votre environnement, telles que des règles d’analyse, des classeurs, des requêtes de chasse ou des playbooks.

- **Audit Microsoft Sentinel**. Examinez l’activité de Microsoft Sentinel pour savoir qui a mis à jour ou supprimé des ressources, telles que des règles d’analyse, des signets, etc. Pour plus d’informations, consultez [Audit des requêtes et des activités Microsoft Sentinel](audit-sentinel-data.md).

### <a name="monthly-tasks"></a>Tâches mensuelles

- **Vérification de l’accès des utilisateurs**. Examinez les autorisations de vos utilisateurs et vérifiez si des utilisateurs sont inactifs. Pour plus d’informations, consultez [Autorisations dans Microsoft Sentinel](roles.md).

- **Examen de l’espace de travail Log Analytics**. Vérifiez que la stratégie de rétention de l’espace de travail Log Analytics est toujours conforme à la stratégie de votre organisation.  Pour plus d’informations, consultez [Stratégie de rétention](/workplace-analytics/privacy/license-expiration) et [Intégrer Azure Data Explorer pour la conservation des journaux à long terme](store-logs-in-azure-data-explorer.md).


## <a name="integrate-with-microsoft-security-services"></a>Intégration à des services de sécurité Microsoft

Microsoft Sentinel doit ses capacités aux composants qui envoient des données à votre espace de travail et est rendu plus puissant grâce à des intégrations à d’autres services Microsoft. Tous les journaux ingérés dans des produits tels que Microsoft Defender pour les applications Cloud, Microsoft Defender pour point de terminaison et Microsoft Defender pour Identity permettent à ces services de créer des détections et de les transmettre à Microsoft Sentinel. Les journaux peuvent également être ingérés directement dans Microsoft Sentinel pour fournir une image plus complète des événements et des incidents.

Par exemple, l’image suivante illustre la manière dont Microsoft Sentinel ingère des données provenant d’autres services Microsoft et de plateformes multiclouds et partenaires pour assurer la couverture de votre environnement :

:::image type="content" source="media/best-practices/azure-sentinel-and-other-services.png" alt-text="Intégration de Microsoft Sentinel à d’autres services Microsoft et partenaires":::

En plus d’ingérer des alertes et des journaux provenant d’autres sources, Microsoft Sentinel :

- **utilise les informations qu’il ingère avec l’[apprentissage automatique](bring-your-own-ml.md)** , ce qui permet une meilleure corrélation des événements, l’agrégation des alertes, la détection des anomalies, et plus encore ;
- **construit et présente des contrôles interactifs via des [classeurs](get-visibility.md)** , montrant les tendances, les informations connexes et les données clés utilisées pour les tâches d’administration et les investigations ;
- **exécute des [playbooks](tutorial-respond-threats-playbook.md) pour agir sur les alertes**, en rassemblant des informations, en effectuant des actions sur des éléments et en envoyant des notifications à diverses plateformes ;
- **s’intègre à des plateformes partenaires**, telles que ServiceNow et Jira, afin de fournir des services essentiels aux équipes du centre des opérations de sécurité ;
- **ingère et récupère des flux d’enrichissement** à partir de [plateformes de renseignement sur les menaces](threat-intelligence-integration.md) afin d’apporter des données précieuses pour les investigations.

##  <a name="manage-and-respond-to-incidents"></a>Gérer les incidents et y répondre

L’image suivante montre les étapes recommandées dans un processus de gestion des incidents et de réponse.

:::image type="content" source="media/best-practices/incident-handling.png" alt-text="Processus de gestion des incidents : activités Triage, Préparation, Correction, Éradication, Post-incident.":::

Les sections suivantes fournissent des descriptions basiques sur la manière d’utiliser les fonctionnalités de Microsoft Sentinel pour la gestion des incidents et la réponse tout au long du processus. Pour plus d’informations, consultez le didacticiel :[Examiner les incidents avec Microsoft Sentinel](investigate-cases.md).

### <a name="use-the-incidents-page-and-the-investigation-graph"></a>Utiliser la page Incidents et le graphique Investigation

Démarrez tout processus de triage pour les nouveaux incidents sur la page **Incidents** de Microsoft Sentinel et le **graphique Investigation**. 

Découvrez les entités clés, telles que les comptes, les URL, les adresses IP, les noms d’hôte, les activités, la chronologie et bien plus encore. Utilisez ces données pour comprendre si vous avez devant vous un [faux positif](false-positives.md), auquel cas vous pouvez fermer l’incident directement.

Tous les incidents générés s’affichent sur la page **Incidents**, qui sert d’emplacement central pour le triage et les premières investigations. La page **Incidents** répertorie le titre, la gravité, les alertes, les journaux et toute entité pertinente. Les incidents permettent également d’accéder rapidement aux journaux collectés et à tout outil lié à l’incident.

La page **Incidents** fonctionne avec le **graphique Investigation**, un outil interactif qui permet aux utilisateurs d’explorer et de parcourir dans les détails une alerte pour voir toute l’étendue d’une attaque. Les utilisateurs peuvent alors construire une chronologie des événements et découvrir l’étendue d’une chaîne de menaces.

Si vous découvrez que l’incident est un vrai positif, prenez des mesures directement à partir de la page **Incidents** pour examiner les journaux, les entités et explorer la chaîne de menaces. Après avoir identifié la menace et créé un plan d’action, utilisez d’autres outils de Microsoft Sentinel et d’[autres services de sécurité Microsoft](best-practices.md#integrate-with-microsoft-security-services) pour poursuivre l’enquête.


### <a name="handle-incidents-with-workbooks"></a>Traiter les incidents avec des classeurs

Outre la [visualisation et l’affichage des informations et des tendances](get-visibility.md), les classeurs Microsoft Sentinel sont de précieux outils d’investigation.

Par exemple, utilisez le classeur [Investigation Insights](top-workbooks.md#investigation-insights) pour étudier des incidents spécifiques ainsi que toutes les entités et alertes associées. Ce classeur vous permet d’explorer plus en détail les entités en présentant les journaux, les actions et les alertes associés.

### <a name="handle-incidents-with-threat-hunting"></a>Traiter les incidents avec la chasse aux menaces

Tout en enquêtant et en recherchant les causes racine, exécutez des requêtes intégrées de chasse aux menaces et vérifiez les résultats pour tout indicateur de compromission.

Pendant une investigation, ou après avoir pris des mesures pour remédier à la menace et l’éradiquer, utilisez le [livestream](livestream.md) pour surveiller, en temps réel, si des événements malveillants persistent ou si des événements malveillants se poursuivent.

### <a name="handle-incidents-with-entity-behavior"></a>Traiter les incidents avec le comportement des entités

Le comportement des entités dans Microsoft Sentinel permet aux utilisateurs d’examiner les actions et les alertes pour des entités spécifiques et d’enquêter sur celles-ci, par exemple en enquêtant sur les comptes et les noms d’hôtes. Pour plus d'informations, consultez les pages suivantes :

- [Activer l’analyse comportementale des utilisateurs et des entités (UEBA) dans Microsoft Sentinel](enable-entity-behavior-analytics.md)
- [Enquêter sur les incidents à l’aide des données UEBA](investigate-with-ueba.md)
- [Informations de référence sur les enrichissements UEBA Microsoft Sentinel](ueba-enrichments.md)

### <a name="handle-incidents-with-watchlists-and-threat-intelligence"></a>Traiter les incidents à l’aide de listes de surveillance et du renseignement sur les menaces

Pour optimiser les détections basées sur le renseignement sur les menaces, veillez à utiliser des [connecteurs de données de renseignement sur les menaces](connect-threat-intelligence-tip.md) pour ingérer les indicateurs de compromission :

- Connectez les sources de données requises par les [alertes Fusion](fusion.md) et [TI Map](./understand-threat-intelligence.md).
- Intégrez les indicateurs des [plateformes TAXII et TIP](./connect-threat-intelligence-tip.md).

Utilisez les indicateurs de compromission dans les règles d’analyse, lors de la chasse aux menaces, de l’examen des journaux ou de la génération d’autres incidents.

Utilisez une liste de surveillance qui combine des données provenant de données ingérées et de sources externes, telles que des données d’enrichissement. Par exemple, créez des listes de plages d’adresses IP utilisées par votre organisation ou d’employés récemment licenciés. Utilisez des listes de surveillance avec des playbooks pour recueillir des données d’enrichissement, comme l’ajout d’adresses IP malveillantes aux listes de surveillance à utiliser pendant la détection, la chasse aux menaces et les enquêtes.

Pendant un incident, utilisez les listes de surveillance pour contenir les données d’enquête, puis supprimez-les lorsque votre enquête est terminée afin de vous assurer que les données sensibles ne restent pas visibles.

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec Microsoft Sentinel, voir :

- [Microsoft Sentinel intégré](quickstart-onboard.md)
- [Obtenir une visibilité des alertes](get-visibility.md)
