---
title: À propos du contenu et des solutions Azure Sentinel | Microsoft Docs
description: Cet article décrit les solutions et le contenu Azure Sentinel, que les clients peuvent utiliser pour rechercher des outils d’analyse de données empaquetés avec des connecteurs de données.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 38508b591d79e0dee910468f595fa144353574c9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064066"
---
# <a name="about-azure-sentinel-content-and-solutions"></a>À propos du contenu et des solutions Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Les solutions et le **hub de contenu** Azure Sentinel sont en **préversion**, tout comme les packages de solutions individuels. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Le *contenu* Azure Sentinel est du contenu SIEM (Security Information and Event Management) qui permet aux clients d’ingérer des données et d’effectuer des opérations de supervision, d’alerte, de chasse, d’examen, de réponse et de connexion avec différents produits, plateformes et services dans Azure Sentinel.

Le contenu dans Azure Sentinel comprend les types suivants :

- Les **[connecteurs de données](connect-data-sources.md)** fournissent l’ingestion des journaux à partir de différentes sources dans Azure Sentinel.
- Les **[analyseurs](normalization-about-parsers.md)** assurent la mise en forme/la transformation des journaux dans les formats [ASIM](normalization.md), en prenant en charge l’utilisation dans différents scénarios et types de contenu Azure Sentinel.
- Les **[workbooks](get-visibility.md)** assurent la supervision, la visualisation et l’interactivité avec les données dans Azure Sentinel, en mettant en évidence les insights pertinents pour les utilisateurs.
- Les **[règles d’analytique](detect-threats-built-in.md)** fournissent des alertes qui pointent vers des actions SOC pertinentes par le biais d’incidents.
- Les **[requêtes de chasse](hunting.md)** sont utilisées par les équipes SOC pour rechercher de manière proactive les menaces dans Azure Sentinel.
- Les **[notebooks](notebooks.md)** aident les équipes SOC à utiliser des fonctionnalités de chasse avancées dans Jupyter et Azure Notebooks.
- Les **[watchlists](watchlists.md)** prennent en charge l’ingestion de données *spécifiques* en vue d’améliorer la détection des menaces et de réduire la fatigue des alertes.
- Les **[playbooks et les connecteurs Azure Logic Apps personnalisés](automate-responses-with-playbooks.md)** fournissent des fonctionnalités pour les investigations, corrections automatisées et les scénarios de réponse dans Azure Sentinel.

Les *solutions* Azure Sentinel sont des packages de contenu Azure Sentinel ou des intégrations de l’API Azure Sentinel, qui prennent en charge un scénario de bout en bout de produits, de domaines ou de secteurs dans Azure Sentinel.

> [!TIP]
> Vous pouvez personnaliser le contenu prêt à l’emploi en fonction de vos besoins ou créer votre propre solution avec un contenu à partager avec d’autres personnes de la communauté. Pour plus d’informations, consultez le [Guide de génération de solutions Azure Sentinel](https://aka.ms/sentinelsolutionsbuildguide) pour la création et la publication de solutions.
>
## <a name="discover-and-manage-azure-sentinel-content"></a>Découvrir et gérer le contenu Azure Sentinel

Utilisez le **hub de contenu** Azure Sentinel pour découvrir et installer de manière centralisée du contenu prêt à l’emploi (intégré).

Le hub de contenu Azure Sentinel offre la capacité de découverte dans les produits, le déploiement en une seule étape et l’activation de contenu et de solutions prêts à l’emploi de produits, de domaines et/ou de secteurs de bout en bout dans Azure Sentinel.

- Dans le **hub de contenu**, filtrez par [catégories](#azure-sentinel-out-of-the-box-content-and-solution-categories) et autres paramètres, ou utilisez la recherche de texte puissante, pour trouver le contenu le mieux adapté aux besoins de votre organisation. Le **hub de contenu** indique également le [modèle de support](#azure-sentinel-out-of-the-box-content-and-solution-support-models) appliqué aux différents éléments de contenu, certains étant gérés par Microsoft, d’autres par des partenaires ou la communauté.

    Gérez les [mises à jour du contenu prêt à l’emploi](sentinel-solutions-deploy.md#install-or-update-a-solution) par le biais du **hub de contenu** Azure Sentinel et, pour le contenu personnalisé, par l’intermédiaire de la page **Dépôts**.

- Personnalisez le contenu prêt à l’emploi en fonction de vos besoins ou créez du contenu personnalisé, notamment des règles d’analytique, des requêtes de chasse, des notebooks, des workbooks, etc. Gérez votre contenu personnalisé directement dans votre espace de travail Azure Sentinel, par le biais de l’[API Azure Sentinel](/rest/api/securityinsights/) ou, dans votre propre dépôt de contrôle de code source, par l’intermédiaire de la page [Dépôts](ci-cd.md) Azure Sentinel.

### <a name="why-content-hub-and-solutions"></a>Raison d’être du hub de contenu et des solutions

Les *solutions* Azure Sentinel sont constituées de contenu empaqueté ou d’intégrations qui fournissent une valeur de produit de bout en bout pour un ou plusieurs scénarios de domaines ou de secteurs.

L’expérience des solutions repose sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace) pour la détectabilité et le déploiement des solutions. Pour plus d’informations, consultez le [Guide de génération de solutions Azure Sentinel](https://aka.ms/sentinelsolutionsbuildguide) pour la création et la publication de solutions.

Les solutions Azure Sentinel assurent la détectabilité des produits, le déploiement en une seule étape et l’activation de scénarios de produits, de domaines et/ou verticaux de bout en bout dans Azure Sentinel. Cette expérience est basée sur  pour la détectabilité, le déploiement et l’activation des solutions ainsi que sur  pour la création et la publication de solutions.

- Le **contenu empaqueté** est un ensemble d’un ou plusieurs éléments de contenu Azure Sentinel, tels que des connecteurs de données, des workbooks, des règles d’analytique, des playbooks, des requêtes de chasse, des watchlists, des analyseurs et bien plus encore.

- Les **intégrations** incluent des services ou des outils créés avec des API Azure Sentinel ou Azure Log Analytics qui prennent en charge les intégrations entre Azure et les applications clientes existantes ou migrent les données, les requêtes et bien plus encore depuis ces applications vers Azure Sentinel.

Vous pouvez également utiliser des solutions pour installer des packages de contenu prêts à l’emploi en une seule étape, où le contenu est souvent prêt à être utilisé immédiatement. Les fournisseurs et les partenaires peuvent utiliser les solutions pour rentabiliser les investissements en fournissant une valeur de produit, de domaine ou de secteur combinée.

Utilisez le hub de contenu pour découvrir et déployer de manière centralisée des solutions et du contenu prêt à l’emploi dans le cadre d’un scénario donné.

Pour plus d'informations, consultez les pages suivantes :

- [Découvrir et déployer de manière centralisée du contenu et des solutions Azure Sentinel prêts à l’emploi](sentinel-solutions-deploy.md)
- [Catalogue du hub de contenu Azure Sentinel](sentinel-solutions-catalog.md)

## <a name="azure-sentinel-out-of-the-box-content-and-solution-categories"></a>Catégories des solutions et du contenu prêt à l’emploi d’Azure Sentinel

Le contenu prêt à l’emploi d’Azure Sentinel peut être appliqué à une ou plusieurs des catégories suivantes. Dans le **hub de contenu**, sélectionnez les catégories que vous souhaitez voir pour changer le contenu affiché.

### <a name="domain-categories"></a>Catégories de domaine

| Nom de la catégorie  | Description |
| ---------- | ----------------------- |
| **Application**  | Charge de travail web, de serveur, SaaS, de base de données, de communications ou de productivité          |
| **Fournisseur de cloud**  | service cloud|
| **Conformité**   | Protocoles, services et produit de conformité  |
| **DevOps**       | Outils et services d’opérations de développement    |
| **Identité**     | Fournisseurs de service d’identité et intégrations     |
| **Internet des objets (IoT)**    | IoT, infrastructure et appareils OT, services de contrôle industriel                   |
| **Opérations informatiques**| Produits et services qui gèrent les opérations informatiques   |
| **Migration**    | Produits et services d’activation de la migration               |
| **Mise en réseau**   | Produits, services et outils réseau    |
| **Plateforme**     | Composants génériques ou de framework Azure Sentinel, infrastructure cloud et plateforme|
| **Sécurité - Autres**   | Autres produits et services de sécurité sans autre catégorie claire           |
| **Sécurité - Renseignement sur les menaces**  | Plateformes, flux, produits et services relatifs au renseignement sur les menaces        |
| **Sécurité - Protection contre les menaces**   | Protection contre les menaces, protection de la messagerie et services et produits XDR et de protection des points de terminaison     |
| **Sécurité – Vulnérabilité zero-day**   | Solutions spécialisées pour les attaques ciblant une vulnérabilité zero-day comme [Nobelium](/azure/security/fundamentals/recover-from-identity-compromise) |
| **Sécurité - Automatisation (SOAR)**   | Automatisations de la sécurité, SOAR (Security Operations and Automated Responses), opérations de sécurité et produits et services de réponse aux incidents.   |
| **Sécurité - Sécurité du cloud** | CASB (Cloud Access Service Broker), CWPP (plateformes de protection de la charge de travail cloud), CSPM (gestion de la posture de sécurité cloud) et autres produits et services de sécurité cloud |
| **Sécurité - Protection des informations**   | Produits et services de protection des informations et de protection des documents|
| **Sécurité - Menace interne**  | Menace interne et analyse comportementale des utilisateurs et des entités (UEBA) pour les produits et services de sécurité                |
| **Sécurité - Réseau**    | Appareils réseau de sécurité, pare-feu, NDR (détection et réponse réseau), NIDP (prévention des intrusions réseau et de la détection) et capture de paquets réseau   |
| **Sécurité - Gestion des vulnérabilités** | Produits et services de gestion des vulnérabilités                    |
| **Stockage**      | Magasins de fichiers et produits et services de partage de fichiers                 |
| **Formations et tutoriels**  | Formations, tutoriels et ressources d’intégration |
| **Comportement de l’utilisateur (UEBA)**    | Services et produits d’analyse du comportement de l’utilisateur|
| | |

### <a name="industry-vertical-categories"></a>Catégories de secteur d’activité

| Nom de la catégorie  | Description |
| ---------- | ----------------------- |
| **Aéronautique**  | Produits, services et contenu spécifiques pour le secteur de l’aéronautique |
| **Education**    | Produits, services et contenu spécifiques pour le secteur de l’éducation   |
| **Finances**      | Produits, services et contenu spécifiques pour le secteur des finances     |
| **Santé**   | Produits, services et contenu spécifiques pour le secteur de la santé  |
| **Fabrication** | Produits, services et contenu spécifiques pour le secteur de l’industrie |
| **Retail** (Commerce)       | Produits, services et contenu spécifiques pour le secteur de la vente au détail       |
| | |

## <a name="azure-sentinel-out-of-the-box-content-and-solution-support-models"></a>Modèles de support des solutions et du contenu prêt à l’emploi d’Azure Sentinel

Microsoft et d’autres organisations créent des solutions et du contenu prêts à l’emploi Azure Sentinel. Chaque contenu ou solution prêt à l’emploi présente l’un des types de support suivants :

| Modèle de support  | Description |
| ---------- | ----------------------- |
| **Pris en charge par Microsoft**| S’applique à : <br>- Contenu/solutions où Microsoft est le fournisseur de données, le cas échéant, et l’auteur. <br> - Certains contenus/solutions créés par Microsoft pour des sources de données autres que Microsoft. <br><br>    \- Microsoft prend en charge et gère les solutions/contenus dans ce modèle de prise en charge conformément aux [Plans de support Microsoft Azure](https://azure.microsoft.com/support/options/#overview). <br>Les partenaires ou la communauté assurent la prise en charge des solutions/contenus créés par les tiers autres que Microsoft.|
|**Support par un partenaire** | S’applique au contenu/solutions créés par des tiers autres que Microsoft.  <br><br>   La société partenaire assure la prise en charge ou la maintenance de ces éléments de contenu/solutions. Cette société partenaire peut être un éditeur de logiciels indépendant, un fournisseur de services gérés (MSP/MSSP), un intégrateur de systèmes ou toute organisation dont les coordonnées sont fournies dans la page Azure Sentinel des solutions/contenu sélectionnés.<br><br>    Pour tout problème lié à une solution prise en charge par un partenaire, contactez le contact de support spécifié.|
|**Support par la communauté** |S’applique au contenu/solutions créés par Microsoft ou les développeurs partenaires pour lesquels aucun contact n’est listé pour le support et la maintenance dans Azure Sentinel.<br><br>    Pour toute question ou tout problème liés à ces solutions, vous pouvez [entrer un problème](https://github.com/Azure/Azure-Sentinel/issues/new/choose) dans la [communauté Azure Sentinel GitHub](https://aka.ms/threathunters). |
| | |


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez découvert le contenu Azure Sentinel, vous pouvez commencer à gérer du contenu et des solutions dans votre espace de travail Azure Sentinel.

Découvrez et installez des solutions à partir du **hub de contenu** Azure Sentinel. Pour plus d'informations, consultez les pages suivantes :

- [Découvrir et déployer de manière centralisée du contenu et des solutions prêts à l’emploi (préversion publique)](sentinel-solutions-deploy.md)
- [Catalogue du hub de contenu Azure Sentinel](sentinel-solutions-catalog.md)
- [Connecteurs de données Azure Sentinel](connect-data-sources.md)
- [Rechercher votre connecteur de données Azure Sentinel](data-connectors-reference.md)
