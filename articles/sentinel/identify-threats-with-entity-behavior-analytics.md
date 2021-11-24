---
title: Identifier les menaces avancées avec l’analyse comportementale des utilisateurs et des entités (UEBA) dans Microsoft Sentinel | Microsoft Docs
description: Créez des lignes de base comportementales pour des entités (utilisateurs, noms d’hôte, adresses IP), et utilisez-les pour détecter des comportements anormaux et identifier des menaces persistantes avancées (advanced persistent threat, APT) zero-day.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 07a2f605c2cb0211d7aaa1dae37d3cf015e369db
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518600"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-microsoft-sentinel"></a>Identifier les menaces avancées avec l’analyse comportementale des utilisateurs et des entités (UEBA) dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - Les fonctionnalités UEBA et Pages d’entité sont désormais en **disponibilité générale** dans **_toutes_** les régions et zones géographiques de Microsoft Sentinel. 
>
> - L’**entité Adresse IP** est actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>Qu’est-ce que l’analyse du comportement des utilisateurs et des entités ?

L’identification des menaces au sein de votre organisation, ainsi que de leur impact potentiel, qu’il s’agisse d’une entité compromise ou d’une personne malveillante, a toujours été un processus laborieux et chronophage. Le criblage des alertes, la connexion des points et la chasse active des menaces nécessitent du temps et des efforts considérables pour très peu de résultats, avec la possibilité que des menaces sophistiquées échappent tout simplement à la détection. En particulier, des menaces insaisissables, telles que des menaces persistantes zero-day, ciblées et avancées, peuvent être des plus dangereuses pour votre organisation, ce qui rend leur détection d’autant plus critique.

La capacité d’analyse comportementale des utilisateurs et des entités de Microsoft Sentinel élimine la pénibilité des charges de travail de vos analystes et l’incertitude liée aux résultats de leurs efforts. Elle leur apporte également une intelligence hautement fiable et actionnable, qui leur permet de se concentrer sur les tâches d’investigation et de correction.

Au fur et à mesure que Microsoft Sentinel collecte les journaux et les alertes de toutes ses sources de données connectées, il les analyse et établit des profils comportementaux de base des entités de votre organisation (telles que les utilisateurs, hôtes, adresses IP et applications), ainsi qu’un horizon des groupes de pairs. À l’aide de diverses techniques et de capacités de Machine Learning, Microsoft Sentinel peut ensuite identifier l’activité anormale et vous aider à déterminer si une ressource a été compromise. Outre cela, il peut déterminer la sensibilité relative de ressources particulières, identifier des groupes homologues de ressources et évaluer l’impact potentiel (« rayon d’impact ») de la compromission de toute ressource. Ces informations vous permettent de hiérarchiser efficacement l’investigation et la gestion de l’incident. 

### <a name="ueba-analytics-architecture"></a>Architecture de l’analytique UEBA

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Architecture d’analyse du comportement des entités":::

### <a name="security-driven-analytics"></a>Analyse pilotée par la sécurité

Inspiré par le paradigme de Gartner pour les solutions d’analyse comportementale des utilisateurs et des entités, Microsoft Sentinel propose une approche extérieur-intérieur, basée sur trois images de référence :

- **Cas d’usage :** En hiérarchisant les vecteurs et scénarios d’attaque pertinents basés sur une recherche en matière de sécurité alignée sur l’infrastructure de tactiques, techniques et sous-techniques MITRE ATT&CK, ayant pour effet de classer les diverses entités en victimes, auteurs ou points pivots dans la chaîne de destruction, Microsoft Sentinel se concentre spécifiquement sur les journaux les plus précieux que chaque source de données peut fournir.

- **Sources de données :** Tout en prenant en charge d’abord et avant tout les sources de données Azure, Microsoft Sentinel sélectionne soigneusement des sources de données tierces afin de fournir des données correspondant à nos scénarios de menaces.

- **Analyse :** À l’aide de différents algorithmes d’apprentissage automatique, Microsoft Sentinel identifie les activités anormales et présente des preuves de manière claire et concise sous la forme d’enrichissements contextuels, dont certains exemples sont affichés ci-dessous.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Approche de l’extérieur vers l’intérieur de l’analyse du comportement":::

Microsoft Sentinel présente des artefacts qui aident vos analystes de sécurité à mieux comprendre les activités anormales en contexte et par rapport au profil de base de l’utilisateur. Les actions effectuées par un utilisateur (ou un hôte ou une adresse) sont évaluées dans leur contexte. Un résultat « true » indique une anomalie identifiée :
- dans des emplacements géographiques, appareils et environnements ;
- dans des horizons de temps et de fréquence (par rapport à l’historique de l’utilisateur) ;
- par rapport au comportement d’homologues ;
- par rapport au comportement de l’organisation.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Contexte d’entité":::


### <a name="scoring"></a>Notation

À chaque activité est attribué un « score de priorité d’investigation », qui détermine la probabilité qu’un utilisateur spécifique effectue une activité spécifique, en fonction de l’apprentissage comportemental de l’utilisateur et de ses pairs. Les activités identifiées comme les plus anormales reçoivent les scores les plus élevés (sur une échelle de 0-10).

Pour voir comment cela fonctionne, découvrez la manière dont l’analyse comportementale est utilisée dans [Microsoft Defender for Cloud Apps](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136).

## <a name="entity-pages"></a>Pages d’entité

Apprenez en davantage sur les [entités dans Microsoft Sentinel](entities-in-azure-sentinel.md) et consultez la liste complète des [entités et identificateurs pris en charge](entities-reference.md).

Lorsque vous rencontrez un utilisateur ou une entité hôte (les entités Adresse IP sont en préversion) dans une recherche d’entité, une alerte ou une investigation, vous pouvez sélectionner l’entité et être redirigé vers une **page d’entité**, à savoir une feuille de données remplie d’informations utiles sur cette entité. Les types d’informations que contient cette page incluent des faits de base sur l’entité, une chronologie d’événements notables liés à cette entité, ainsi que des insights sur le comportement de l’entité.
 
Les pages d’entité se composent de trois parties :
- Le volet gauche contient les informations d’identification de l’entité, collectées à partir de sources de données telles qu’Azure Active Directory, Azure Monitor, Microsoft Defender pour le cloud, CEF/Syslog et Microsoft 365 Defender.

- Le panneau central affiche une chronologie graphique et textuelle des événements notables liés à l’entité, tels que des alertes, des signets et des activités. Les activités sont des agrégations d’événements notables de Log Analytics. Les requêtes qui détectent ces activités sont développées par des équipes de recherche en matière de sécurité de Microsoft, et vous pouvez désormais [ajouter vos propres requêtes personnalisées pour détecter les activités](customize-entity-activities.md) de votre choix. 

- Le panneau de droite présente des insights comportementales sur l’entité. Ces insights permettent d’identifier rapidement des anomalies et autres menaces de sécurité. Les insights sont développés par des équipes de recherche en matière de sécurité Microsoft, et sont basées sur des modèles de détection d’anomalies.

> [!NOTE]
> La **page d’entité Adresse IP** (désormais en préversion) contient les **données de géolocalisation** fournies par le **service Microsoft Threat Intelligence**. Ce service combine les données de géolocalisation des solutions Microsoft et des fournisseurs et partenaires tiers. Les données sont ensuite disponibles à des fins d’analyse et d’investigation dans le contexte d’un incident de sécurité. Pour plus d’informations, consultez également [Enrichir des entités dans Microsoft Sentinel avec des données de géolocalisation via l’API REST (préversion publique)](geolocation-data-api.md).

### <a name="the-timeline"></a>La chronologie

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Chronologie des pages d’entité":::

La chronologie est une partie importante de la contribution de la page d’entité à l’analyse comportementale dans Microsoft Sentinel. Elle présente l’historique des événements liés à l’entité, qui vous aide à comprendre l’activité de celle-ci dans un délai d’exécution spécifique.

Vous pouvez choisir le **délai d’exécution** parmi plusieurs options prédéfinies (par exemple, *dernières 24 heures*), ou définir un délai d’exécution personnalisé. En outre, vous pouvez définir des filtres qui limitent les informations de la chronologie à des types spécifiques d’événements ou d’alertes.

Les types d’éléments suivants sont inclus dans la chronologie :

- Alertes – toute alerte dans laquelle l’entité est définie en tant qu’**entité mappée**. Notez que, si votre organisation a créé des [alertes personnalisées utilisant des règles d’analyse](./detect-threats-custom.md), vous devez vous assurer que le mappage d’entité des règles est correctement effectué.

- Signets – signets incluant l’entité affichée sur la page.

- Activités – agrégation d’événements notables liés à l’entité. Un large éventail d’activités sont automatiquement collectées, et vous pouvez désormais [personnaliser cette section en ajoutant les activités](customize-entity-activities.md) de votre choix.

### <a name="entity-insights"></a>Insights sur l’entité
 
Les insights d’entité sont des requêtes définie par les chercheurs en matière de sécurité de Microsoft pour aider vos analystes à investiguer plus efficacement. Les insights sont présentées dans la page de l’entité. Elles fournissent des informations de sécurité précieuses sur les hôtes et les utilisateurs, sous la forme de graphiques et de données tabulaires. Les informations étant disponibles ici, vous n’avez pas à passer par Log Analytics. Les insights contiennent des données relatives aux connexions, aux ajouts de groupes, aux événements anormaux, et ainsi de suite. Ils incluent également des algorithmes de Machine Learning avancés pour détecter les comportements anormaux. 

Les insights reposent sur les sources de données suivantes :
- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (UEBA Microsoft Sentinel)
- Heartbeat (Azure Monitor Agent)
- CommonSecurityLog (Microsoft Sentinel)
- ThreatIntelligenceIndicators (Microsoft Sentinel)

### <a name="how-to-use-entity-pages"></a>Comment utiliser les pages d’entité

Les pages d’entité sont conçues pour faire partie de plusieurs scénarios d’usage. Elles sont accessibles à partir de la gestion des incidents, du graphe d’investigation, des signets ou directement à partir de la page de recherche d’entité sous **Analyse du comportement des entités** dans le menu principal de Microsoft Sentinel.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Cas d’usage de page d’entité":::

Les informations de la page d’entité sont stockées dans la table **BehaviorAnalytics** et décrites en détail dans la section [Informations de référence sur les enrichissements UEBA Microsoft Sentinel](ueba-enrichments.md).

## <a name="querying-behavior-analytics-data"></a>Interrogation des données d’analyse du comportement

Une requête de mots clés [KQL](/azure/data-explorer/kusto/query/) nous permet d’interroger la table d’analyse du comportement.

Par exemple, si vous souhaitez rechercher tous les cas d’un utilisateur n’ayant pas réussi à se connecter à une ressource Azure, où il s’agissait de la première tentative de l’utilisateur de se connecter à partir d’un pays donné, alors que les connexions à partir de ce pays sont rares, même émanant de pairs de l’utilisateur, nous pouvons utiliser la requête suivante :

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>Métadonnées de pairs de l’utilisateur – table et bloc-notes

Les métadonnées de pairs de l’utilisateur fournissent un contexte important pour les détections de menaces, l’investigation sur un incident et la chasse de menace potentielle. Les analystes de la sécurité peuvent observer les activités normales des pairs d’un utilisateur pour déterminer si les activités de l’utilisateur sont inhabituelles par rapport à celles de ses pairs.

Microsoft Sentinel calcule et classe les pairs d’un utilisateur sur la base de l’appartenance à un groupe de sécurité Azure AD, de la liste de diffusion, etc. de l’utilisateur et stocke les pairs classés de 1 à 20 dans la table **UserPeerAnalytics**. La capture d’écran ci-dessous montre le schéma de la table UserPeerAnalytics, avec les huit premiers pairs classés de l’utilisateur Kendall Collins. Microsoft Sentinel utilise l’algorithme *TF-IDF (Term Frequency-Inverse Document Frequency, fréquence de terme-fréquence inverse dans le document)* afin de normaliser la pondération pour le calcul du classement : plus le groupe est petit, plus le poids est élevé. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Capture d’écran d’une table de métadonnées de pairs d’utilisateur":::

Vous pouvez utiliser le [notebook Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) fourni dans le référentiel GitHub de Microsoft Sentinel pour visualiser les métadonnées des pairs des utilisateurs. Pour obtenir des instructions détaillées sur l’utilisation du bloc-notes, consultez le bloc-notes [Analyse guidée – Métadonnées de sécurité utilisateur](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb).

### <a name="permission-analytics---table-and-notebook"></a>Analyse d’autorisation – table et bloc-notes

L’analyse d’autorisation permet de déterminer l’impact potentiel de la compromission d’une ressource d’organisation par un attaquant. Cet impact est également appelé « rayon d’impact » de la ressource. Les analystes de la sécurité peuvent utiliser ces informations pour hiérarchiser les investigations et la gestion des incidents.

Microsoft Sentinel détermine les droits d’accès direct et transitif aux ressources Azure détenus par un utilisateur donné en évaluant les abonnements Azure auxquels l’utilisateur peut accéder directement ou via des groupes ou des principaux de service. Ces informations, ainsi que la liste complète d’appartenance de groupe de sécurité Azure AD de l’utilisateur, sont ensuite stockées dans la table **UserAccessAnalytics**. La capture d’écran ci-dessous montre un exemple de ligne dans la table UserAccessAnalytics pour l’utilisateur Alex Johnson. L’**entité source** est le compte d’utilisateur ou de principal de service, et l’**entité cible** est la ressource à laquelle l’entité source a accès. Les valeurs de **niveau d’accès** et de **type d’accès** dépendent du modèle de contrôle d’accès de l’entité cible. Vous pouvez voir qu’Alex dispose d’un accès contributeur à l’abonnement Azure du *Locataire Contoso Hotels*. Le modèle de contrôle d’accès de l’abonnement est RBAC Azure.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Capture d’écran du tableau d’analyse de l’accès utilisateur":::

Vous pouvez utiliser le [notebook Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (le même que celui mentionné ci-dessus) à partir du référentiel GitHub de Microsoft Sentinel pour visualiser les données d’analyse des autorisations. Pour obtenir des instructions détaillées sur l’utilisation du bloc-notes, consultez le bloc-notes [Analyse guidée – Métadonnées de sécurité utilisateur](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb).

### <a name="hunting-queries-and-exploration-queries"></a>Requêtes de chasse et d’exploration

Microsoft Sentinel offre un ensemble prêt à l’emploi de requêtes de chasse, de requêtes d’exploration, ainsi que le classeur **Analyse comportementale des utilisateurs et des entités**, qui est basé sur la table **BehaviorAnalytics**. Ces outils présentent des données enrichies, axées sur des cas d’usage spécifiques, qui indiquent un comportement anormal.

Pour plus d'informations, consultez les pages suivantes :

- [Repérer des menaces avec Microsoft Sentinel](hunting.md)
- [Visualiser et superviser vos données](monitor-your-data.md)

Alors que les outils de défense hérités deviennent obsolètes, les organisations peuvent avoir un patrimoine numérique si vaste et si perméable qu’il devient ingérable d’obtenir une image complète du risque et de la posture que leur environnement peuvent rencontrer. Les acteurs malveillants s’appuient fortement sur des initiatives réactives, comme les analytiques et les règles, pour apprendre à y échapper. C’est là qu’intervient UEBA, en fournissant des méthodologies et des algorithmes de scoring des risques pour déterminer ce qui se passe vraiment.


## <a name="next-steps"></a>Étapes suivantes
Ce document vous a montré les capacités d’analyse comportementale des entités de Microsoft Sentinel. Pour obtenir des conseils pratiques sur l’implémentation et pour utiliser les insights que vous avez acquises, consultez les articles suivants :

- [Activer l’analyse comportementale des entités](./enable-entity-behavior-analytics.md) dans Microsoft Sentinel.
- [Examiner les incidents avec les données UEBA](investigate-with-ueba.md).
- [Chasse des menaces de sécurité](./hunting.md).

Pour plus d’informations, consultez également les [informations de référence sur les enrichissements UEBA Microsoft Sentinel](ueba-enrichments.md).
