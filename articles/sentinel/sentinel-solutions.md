---
title: À propos du contenu et des solutions Microsoft Sentinel | Microsoft Docs
description: Cet article décrit les solutions et le contenu Microsoft Sentinel, que les clients peuvent utiliser pour rechercher des outils d’analyse de données fournis avec des connecteurs de données.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 940acf043711c7645423a9e52d8c4dabc2592e3f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516914"
---
# <a name="about-microsoft-sentinel-content-and-solutions"></a>À propos du contenu et des solutions Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Les solutions et le **hub de contenu** Microsoft Sentinel sont en **PRÉVERSION**, tout comme les packages des solutions individuelles. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Le *contenu* Microsoft Sentinel est du contenu SIEM (Informations de sécurité et gestion d’événements) qui permet aux clients d’effectuer des opérations d’ingestion de données, de surveillance, de génération d’alertes, de chasse, d’investigation, de réponse et de connexion avec différents produits, plateformes et services dans Microsoft Sentinel.

Le contenu dans Microsoft Sentinel comprend les types suivants :

- Les **[connecteurs de données](connect-data-sources.md)** permettent l’ingestion de journaux provenant de différentes sources dans Microsoft Sentinel.
- Les **[analyseurs](normalization-about-parsers.md)** assurent la mise en forme ou la transformation des journaux dans les formats [ASIM](normalization.md), en prenant en charge l’utilisation dans différents scénarios et types de contenu Microsoft Sentinel.
- Les **[classeurs](get-visibility.md)** assurent la supervision des données, leur visualisation et l’interactivité avec celles-ci dans Microsoft Sentinel, en mettant en évidence les insights pertinents pour les utilisateurs.
- Les **[règles d’analytique](detect-threats-built-in.md)** fournissent des alertes qui pointent vers des actions SOC pertinentes par le biais d’incidents.
- Les **[requêtes de chasse](hunting.md)** sont utilisées par les équipes SOC pour repérer de manière proactive les menaces dans Microsoft Sentinel.
- Les **[notebooks](notebooks.md)** aident les équipes SOC à utiliser des fonctionnalités de chasse avancées dans Jupyter et Azure Notebooks.
- Les **[watchlists](watchlists.md)** prennent en charge l’ingestion de données *spécifiques* en vue d’améliorer la détection des menaces et de réduire la fatigue des alertes.
- Les **[playbooks et les connecteurs personnalisés Azure Logic Apps](automate-responses-with-playbooks.md)** fournissent des fonctionnalités pour les investigations automatisées, les corrections et les scénarios de réponse dans Microsoft Sentinel.

Les *solutions* Microsoft Sentinel sont des packages de contenu Microsoft Sentinel ou des intégrations de l’API Microsoft Sentinel qui prennent en charge un scénario de bout en bout de produits, de domaines ou de secteurs dans Microsoft Sentinel.

> [!TIP]
> Vous pouvez personnaliser le contenu prêt à l’emploi en fonction de vos besoins ou créer votre propre solution avec un contenu à partager avec d’autres personnes de la communauté. Pour plus d’informations, consultez le [guide de génération de solutions Microsoft Sentinel](https://aka.ms/sentinelsolutionsbuildguide) pour la création et la publication de solutions.
>
## <a name="discover-and-manage-microsoft-sentinel-content"></a>Découvrir et gérer le contenu Microsoft Sentinel

Utilisez le **hub de contenu** Microsoft Sentinel pour découvrir et installer de manière centralisée du contenu prêt à l’emploi (intégré).

Le hub de contenu Microsoft Sentinel assure la détectabilité des produits, le déploiement en une seule étape et l’activation de contenu et de solutions de bout en bout prêts à l’emploi de produits, de domaines ou de secteurs dans Microsoft Sentinel.

- Dans le **hub de contenu**, filtrez par [catégories](#microsoft-sentinel-out-of-the-box-content-and-solution-categories) et autres paramètres, ou utilisez la recherche de texte puissante, pour trouver le contenu le mieux adapté aux besoins de votre organisation. Le **hub de contenu** indique également le [modèle de support](#microsoft-sentinel-out-of-the-box-content-and-solution-support-models) appliqué aux différents éléments de contenu, certains étant gérés par Microsoft, d’autres par des partenaires ou la communauté.

    Gérez les [mises à jour du contenu prêt à l’emploi](sentinel-solutions-deploy.md#install-or-update-a-solution) par le biais du **hub de contenu** Microsoft Sentinel et, pour le contenu personnalisé, par l’intermédiaire de la page **Référentiels**.

- Personnalisez le contenu prêt à l’emploi en fonction de vos besoins ou créez du contenu personnalisé, notamment des règles d’analytique, des requêtes de chasse, des notebooks, des workbooks, etc. Gérez votre contenu personnalisé directement dans votre espace de travail Microsoft Sentinel, par le biais de l’[API Microsoft Sentinel](/rest/api/securityinsights/) ou, dans votre propre référentiel de contrôle de code source, par l’intermédiaire de la page [Référentiels](ci-cd.md) de Microsoft Sentinel.

### <a name="why-content-hub-and-solutions"></a>Raison d’être du hub de contenu et des solutions

Les *solutions* Microsoft Sentinel sont constituées de contenu empaqueté ou d’intégrations qui fournissent une valeur de produit de bout en bout pour un ou plusieurs scénarios de domaines ou de secteurs.

L’expérience des solutions repose sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace) pour la détectabilité et le déploiement des solutions. Pour plus d’informations, consultez le [guide de génération de solutions Microsoft Sentinel](https://aka.ms/sentinelsolutionsbuildguide) pour la création et la publication de solutions.

Les solutions Microsoft Sentinel assurent la détectabilité des produits, le déploiement en une seule étape et l’activation de scénarios de bout en bout de produits, de domaines ou de secteurs dans Microsoft Sentinel. Cette expérience est basée sur  pour la détectabilité, le déploiement et l’activation des solutions ainsi que sur  pour la création et la publication de solutions.

- Le **contenu empaqueté** est un ensemble d’un ou plusieurs éléments de contenu Microsoft Sentinel, tels que des connecteurs de données, des classeurs, des règles d’analyse, des playbooks, des requêtes de chasse, des listes de surveillance, des analyseurs et bien plus encore.

- Les **intégrations** incluent des services ou des outils créés avec des API Microsoft Sentinel ou Azure Log Analytics qui prennent en charge les intégrations entre Azure et les applications clientes existantes ou migrent les données, les requêtes et bien plus encore de ces applications vers Microsoft Sentinel.

Vous pouvez également utiliser des solutions pour installer des packages de contenu prêts à l’emploi en une seule étape, où le contenu est souvent prêt à être utilisé immédiatement. Les fournisseurs et les partenaires peuvent utiliser les solutions pour rentabiliser les investissements en fournissant une valeur de produit, de domaine ou de secteur combinée.

Utilisez le hub de contenu pour découvrir et déployer de manière centralisée des solutions et du contenu prêt à l’emploi dans le cadre d’un scénario donné.

Pour plus d'informations, consultez les pages suivantes :

- [Découvrir et déployer de manière centralisée du contenu et des solutions Microsoft Sentinel prêts à l’emploi](sentinel-solutions-deploy.md)
- [Catalogue du hub de contenu Microsoft Sentinel](sentinel-solutions-catalog.md)

## <a name="microsoft-sentinel-out-of-the-box-content-and-solution-categories"></a>Catégories des solutions et du contenu prêt à l’emploi de Microsoft Sentinel

Le contenu prêt à l’emploi de Microsoft Sentinel peut être appliqué à une ou plusieurs des catégories suivantes. Dans le **hub de contenu**, sélectionnez les catégories que vous souhaitez voir pour changer le contenu affiché.

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
| **Plateforme**     | Composants génériques ou d’infrastructure Microsoft Sentinel, infrastructure cloud et plateforme|
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

## <a name="microsoft-sentinel-out-of-the-box-content-and-solution-support-models"></a>Modèles de prise en charge des solutions et du contenu prêt à l’emploi de Microsoft Sentinel

Microsoft et d’autres organisations créent des solutions et du contenu prêts à l’emploi Microsoft Sentinel. Chaque contenu ou solution prêt à l’emploi présente l’un des types de support suivants :

| Modèle de support  | Description |
| ---------- | ----------------------- |
| **Pris en charge par Microsoft**| S’applique à : <br>- Contenu/solutions où Microsoft est le fournisseur de données, le cas échéant, et l’auteur. <br> - Certains contenus/solutions créés par Microsoft pour des sources de données autres que Microsoft. <br><br>    \- Microsoft prend en charge et gère les solutions/contenus dans ce modèle de prise en charge conformément aux [Plans de support Microsoft Azure](https://azure.microsoft.com/support/options/#overview). <br>Les partenaires ou la communauté assurent la prise en charge des solutions/contenus créés par les tiers autres que Microsoft.|
|**Support par un partenaire** | S’applique au contenu/solutions créés par des tiers autres que Microsoft.  <br><br>   La société partenaire assure la prise en charge ou la maintenance de ces éléments de contenu/solutions. Cette société partenaire peut être un éditeur de logiciels indépendant, un fournisseur de services gérés (MSP/MSSP), un intégrateur de systèmes ou toute organisation dont les coordonnées sont fournies dans la page Microsoft Sentinel des solutions ou du contenu sélectionnés.<br><br>    Pour tout problème lié à une solution prise en charge par un partenaire, contactez le contact de support spécifié.|
|**Support par la communauté** |S’applique au contenu ou aux solutions créés par Microsoft ou les développeurs partenaires pour lesquels aucune coordonnée n’est répertoriée pour le support et la maintenance dans Microsoft Sentinel.<br><br>    Pour toute question ou tout problème liés à ces solutions, vous pouvez [signaler un problème](https://github.com/Azure/Azure-Sentinel/issues/new/choose) auprès de la [communauté GitHub de Microsoft Sentinel](https://aka.ms/threathunters). |
| | |

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez découvert le contenu Microsoft Sentinel, vous pouvez commencer à gérer du contenu et des solutions dans votre espace de travail Microsoft Sentinel.

Découvrez et installez des solutions à partir du **hub de contenu** Microsoft Sentinel. Pour plus d'informations, consultez les pages suivantes :

- [Découvrir et déployer de manière centralisée du contenu et des solutions prêts à l’emploi (préversion publique)](sentinel-solutions-deploy.md)
- [Catalogue du hub de contenu Microsoft Sentinel](sentinel-solutions-catalog.md)
- [Connecteurs de données Microsoft Sentinel](connect-data-sources.md)
- [Rechercher votre connecteur de données Microsoft Sentinel](data-connectors-reference.md)
