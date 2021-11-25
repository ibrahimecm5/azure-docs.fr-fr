---
title: Étendre Microsoft Sentinel aux espaces de travail et aux locataires | Microsoft Docs
description: Comment utiliser Microsoft Sentinel pour interroger et analyser des données dans les espaces de travail et les locataires.
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
ms.openlocfilehash: 3b49f1bdbe14dd13417bfef0348e1fd53543d7ca
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521241"
---
# <a name="extend-microsoft-sentinel-across-workspaces-and-tenants"></a>Étendre Microsoft Sentinel dans les espaces de travail et les locataires

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="the-need-to-use-multiple-microsoft-sentinel-workspaces"></a>Nécessité d’utiliser plusieurs espaces de travail Microsoft Sentinel

Microsoft Sentinel est basé sur un espace de travail Log Analytics. Vous remarquerez que la première étape de l’intégration de Microsoft Sentinel consiste à sélectionner l’espace de travail Log Analytics que vous souhaitez utiliser à cet effet.

Vous pouvez bénéficier de tous les avantages de l’expérience Microsoft Sentinel en utilisant un seul espace de travail. Toutefois, dans certains cas, il peut être nécessaire d’avoir plusieurs espaces de travail. Le tableau suivant répertorie quelques-unes de ces situations et, lorsque cela est possible, suggère comment la condition requise peut être satisfaite avec un seul espace de travail :

| Condition requise | Description | Comment réduire le nombre d’espaces de travail |
|-------------|-------------|--------------------------------|
| Souveraineté et conformité réglementaire | Un espace de travail est lié à une région spécifique. Si les données doivent être conservées dans différentes [zones géographiques Azure](https://azure.microsoft.com/global-infrastructure/geographies/) pour satisfaire les exigences réglementaires, elles doivent être fractionnées en espaces de travail distincts. |  |
| Propriétaire des données | Les limites de la propriété des données, par exemple par les filiales ou les sociétés affiliées, sont mieux définies à l’aide d’espaces de travail distincts. |  |
| Plusieurs locataires Azure | Microsoft Sentinel prend en charge la collecte de données à partir de ressources Microsoft et SaaS Azure uniquement dans sa propre limite de locataire Azure Active Directory (Azure AD). Par conséquent, chaque locataire Azure AD requiert un espace de travail distinct. |  |
| Contrôle d’accès granulaire aux données | Une organisation peut avoir besoin d’autoriser des groupes différents, au sein ou à l’extérieur de l’organisation, à accéder à certaines des données collectées par Microsoft Sentinel. Par exemple :<br><ul><li>Accès des propriétaires de ressources aux données relatives à leurs ressources</li><li>Accès des SOC régionaux ou des filiales aux données relatives à leurs parties de l’organisation</li></ul> | Utilisez le contrôle [RBAC Azure des ressources](resource-context-rbac.md) ou [Azure RBAC au niveau de la table](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| Paramètres de rétention granulaires | Historiquement, disposer de plusieurs espaces de travail était la seule solution pour définir des périodes de rétention différentes pour différents types de données. Cela n’est plus nécessaire dans de nombreux cas, grâce à l’introduction des paramètres de rétention au niveau table. | Utilisez les [paramètres de rétention au niveau table](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) ou automatisez la [suppression des données](../azure-monitor/logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) |
| Facturation fractionnée | Placer des espaces de travail dans des abonnements distincts permet de les facturer à différentes parties. | Rapports d’utilisation et facturation interne |
| Architecture héritée | L’utilisation de plusieurs espaces de travail peut provenir d’une conception historique qui a pris en compte des limitations ou des pratiques recommandées qui ne sont plus valables. Il peut également s’agir d’un choix de conception arbitraire qui peut être modifié pour mieux prendre en charge Microsoft Sentinel.<br><br>Voici quelques exemples :<br><ul><li>Utilisation d’un espace de travail par défaut par abonnement lors du déploiement de Microsoft Defender pour le cloud</li><li>La nécessité d’un contrôle d’accès granulaire ou de paramètres de rétention, pour lesquels les solutions sont relativement nouvelles</li></ul> | Restructurer les espaces de travail |

### <a name="managed-security-service-provider-mssp"></a>Fournisseur de services de sécurité managés (MSSP)

Un cas d’usage particulier qui impose plusieurs espaces de travail est un service MSSP Microsoft Sentinel. Dans ce cas, beaucoup, pour ne pas dire toutes les exigences ci-dessus s’appliquent ; ainsi, avoir plusieurs espaces de travail parmi les locataires constitue la meilleure pratique. Le MSSP peut utiliser [Azure Lighthouse](../lighthouse/overview.md) pour étendre les capacités interespaces de travail Microsoft Sentinel entre les locataires.

## <a name="microsoft-sentinel-multiple-workspace-architecture"></a>Architecture d’espaces de travail multiples Microsoft Sentinel

Comme nous l’avons vu dans les exigences ci-dessus, il existe des cas où plusieurs espaces de travail Microsoft Sentinel, potentiellement dans plusieurs locataires Azure Active Directory (Azure AD), doivent être surveillés et gérés de manière centralisée par un seul SOC.

- Un service Microsoft Sentinel de MSSP.

- Un SOC international desservant plusieurs filiales, chacune ayant son propre SOC local.

- Un SOC surveillant plusieurs locataires Azure AD au sein d’une organisation.

Pour répondre à cette exigence, Microsoft Sentinel propose des capacités d’espaces de travail multiples qui permettent une surveillance, une configuration et une gestion centralisées, en fournissant un seul et même volet pour tous les éléments couverts par le SOC, comme indiqué dans le schéma ci-dessous.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="Architecture inter-espaces de travail":::

Ce modèle offre des avantages significatifs par rapport à un modèle entièrement centralisé dans lequel toutes les données sont copiées dans un espace de travail unique :

- Attribution de rôle flexible aux SOC internationaux et locaux, ou aux MSSP de leurs clients.

- Moins de défis concernant la propriété des données, la confidentialité des données et la conformité réglementaire.

- Facturation et latence minimales du réseau.

- Intégration et débarquement faciles de nouvelles filiales ou de nouveaux clients.

Dans les sections suivantes, nous expliquerons comment utiliser ce modèle, et notamment comment :

- Surveiller de manière centralisée plusieurs espaces de travail, potentiellement entre les locataires, en fournissant au SOC un seul volet de transparence.

- Configurer et gérer de manière centralisée plusieurs espaces de travail, potentiellement entre les locataires, à l’aide de l’automatisation.

## <a name="cross-workspace-monitoring"></a>Surveillance inter-espaces de travail

### <a name="manage-incidents-on-multiple-workspaces"></a>Gérer les incidents dans plusieurs espaces de travail

Microsoft Sentinel prend en charge un [affichage des incidents dans plusieurs espaces de travail](./multiple-workspace-view.md) qui facilite la surveillance et la gestion centralisées des incidents au sein de plusieurs espaces de travail. L’affichage des incidents centralisé vous permet de gérer les incidents directement ou de descendre dans la hiérarchie en toute transparence pour connaître les détails de l’incident dans le cadre de l’espace de travail d’origine.

### <a name="cross-workspace-querying"></a>Interrogation de plusieurs espaces de travail

Microsoft Sentinel prend en charge l’interrogation de[plusieurs espaces de travail dans une seule requête](../azure-monitor/logs/cross-workspace-query.md), ce qui vous permet de rechercher et de mettre en corrélation les données de plusieurs espaces de travail dans une seule requête.

- Utilisez l’[expression workspace](../azure-monitor/logs/workspace-expression.md) pour référence à une table dans un espace de travail différent.

- Utilisez l’[opérateur union](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) avec l’expression workspace() pour appliquer une requête sur plusieurs tables dans plusieurs espaces de travail.

Vous pouvez utiliser des fonctions [enregistrées](../azure-monitor/logs/functions.md) pour simplifier les requêtes inter-espaces de travail. Par exemple, si une référence à un espace de travail est longue, vous pouvez enregistrer l’expression `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` en tant que fonction nommée `SecurityEventCustomerA`. Vous pouvez ensuite écrire des requêtes comme `SecurityEventCustomerA | where ...`.

Une fonction peut également simplifier un union couramment utilisé. Par exemple, vous pouvez enregistrer l’expression suivante en tant que fonction nommée `unionSecurityEvent` :

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

Vous pouvez ensuite écrire une requête sur les deux espaces de travail en commençant par `unionSecurityEvent | where ...`.

#### <a name="cross-workspace-analytics-rules"></a>Règles d’analytique applicables à plusieurs espaces de travail<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
Des requêtes portant sur plusieurs espaces de travail peuvent désormais être incluses dans des règles analytiques planifiées. Vous pouvez utiliser des règles analytiques portant sur plusieurs espaces de travail dans un SOC central et entre les locataires (à l’aide d’Azure Lighthouse) comme dans le cas d’un MSSP, selon les restrictions suivantes :

- **Jusqu’à 20 espaces de travail** peuvent être inclus dans une même requête.
- Microsoft Sentinel doit être **déployé sur chaque espace de travail** référencé dans la requête.
- Les alertes générées par une règle analytique portant sur plusieurs espaces de travail et les incidents créés à partir de celles-ci existent **uniquement dans l’espace de travail où la règle a été définie**. Ils ne seront pas affichés dans les autres espaces de travail référencés dans la requête.

Les alertes et les incidents créés par des règles analytiques portant sur plusieurs espaces de travail contiendront toutes les entités associées, dont celles de tous les espaces de travail référencés ainsi que l’espace de travail « d’origine » (où la règle a été définie). Cela permettra aux analystes d’avoir une vue d’ensemble des alertes et des incidents.

> [!NOTE]
> Interroger plusieurs espaces de travail dans la même requête peut nuire au niveau de performance ; par conséquent, cette méthode est recommandée uniquement lorsque la logique requiert cette fonctionnalité.

#### <a name="cross-workspace-workbooks"></a>Classeurs pour plusieurs espaces de travail<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
Les [classeurs](./overview.md#workbooks) fournissent des tableaux de bord et des applications à Microsoft Sentinel. Lorsque vous travaillez avec plusieurs espaces de travail, ceux-ci fournissent des analyses et permettent d’effectuer des actions parmi les espaces de travail.

Les classeurs peuvent fournir des requêtes inter-espaces de travail de l’une des trois méthodes suivantes, chacune d’entre elles étant basée sur différents niveaux d’expertise de l’utilisateur final :

| Méthode  | Description | Quand utiliser cette méthode ? |
|---------|-------------|--------------------|
| Écrire des requêtes dans plusieurs espaces de travail | Le créateur du classeur peut écrire des requêtes dans plusieurs espaces de travail (voir ci-dessus) dans le classeur. | Cette option permet aux créateurs de classeurs de protéger entièrement l’utilisateur de la structure de l’espace de travail. |
| Ajouter un sélecteur d’espace de travail au classeur | Le créateur du classeur peut implémenter un sélecteur d’espace de travail dans le cadre du classeur, comme décrit [ici](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357). | Cette option permet à l’utilisateur de contrôler les espaces de travail affichés par le classeur, au moyen d’une zone de liste déroulante facile à utiliser. |
| Modifier le classeur de manière interactive | Un utilisateur expérimenté qui modifie un classeur existant peut modifier les requêtes qu’il contient, en sélectionnant les espaces de travail cibles à l’aide du sélecteur d’espace de travail dans l’éditeur. | Cette option permet à un utilisateur avancé de modifier facilement des classeurs existants pour travailler avec plusieurs espaces de travail. |
|

#### <a name="cross-workspace-hunting"></a>Chasse dans plusieurs espaces de travail

Microsoft Sentinel fournit des exemples de requêtes préchargés conçus pour vous aider à prendre en main les tables et le langage de requête et à vous familiariser avec eux. Ces requêtes de chasse intégrées sont élaborées en continu par les chercheurs en sécurité de Microsoft, qui ajoutent de nouvelles requêtes et affinent celles existantes, de manière à vous offrir un point d’entrée pour la recherche de nouvelles détections et identifier les signes d’intrusion qui pourraient ne pas avoir été détectés par vos outils de sécurité.  

Les capacités de chasse dans plusieurs espaces de travail permettent à vos chasseurs de menaces de créer de nouvelles requêtes de chasse ou d’adapter celles existantes pour couvrir plusieurs espaces de travail en utilisant l’opérateur Union et l’expression workspace() comme indiqué ci-dessus.

## <a name="cross-workspace-management-using-automation"></a>Gestion inter-espaces de travail à l’aide de l’automatisation

Pour configurer et gérer plusieurs espaces de travail Microsoft Sentinel, vous devez automatiser l’utilisation de l’API de gestion Microsoft Sentinel. Pour plus d’informations sur l’automatisation du déploiement des ressources Microsoft Sentinel, notamment les règles d’alerte, les requêtes de chasse, les classeurs et les playbooks, consultez [Extending Microsoft Sentinel: APIs, Integration and management automation](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885).

Consultez également [Deploying and Managing Microsoft Sentinel as Code](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) et [Combining Azure Lighthouse with Microsoft Sentinel’s DevOps capabilities](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) pour une méthodologie consolidée et proposée par la communauté permettant de gérer Microsoft Sentinel en tant que code et de déployer et configurer des ressources à partir d’un référentiel GitHub privé.

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Gérer les espaces de travail parmi les locataires avec Azure Lighthouse

Comme indiqué ci-dessus, dans de nombreux scénarios, les différents espaces de travail Microsoft Sentinel peuvent se trouver dans différents locataires Azure AD. Vous pouvez utiliser [Azure Lighthouse](../lighthouse/overview.md) pour étendre toutes les activités interespaces de travail au-delà des limites des locataires, ce qui permet aux utilisateurs de votre locataire gérant de travailler sur des espaces de travail Microsoft Sentinel dans tous les locataires. Une fois qu’Azure Lighthouse est [intégré](../lighthouse/how-to/onboard-customer.md), utilisez le [sélecteur de répertoire et d’abonnement](./multiple-tenants-service-providers.md#how-to-access-microsoft-sentinel-in-managed-tenants) sur le Portail Azure pour sélectionner tous les abonnements contenant les espaces de travail que vous souhaitez gérer, afin de vous assurer qu’ils seront tous disponibles dans les différents sélecteurs d’espace de travail dans le portail.

Quand vous utilisez Azure Lighthouse, il est recommandé de créer un groupe pour chaque rôle Microsoft Sentinel et de déléguer les autorisations de chaque locataire à ces groupes.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à étendre les capacités de Microsoft Sentinel à plusieurs espaces de travail et locataires. Pour obtenir des conseils pratiques sur l’implémentation de l’architecture interespace de travail de Microsoft Sentinel, consultez les articles suivants :

- Découvrez comment [travailler avec plusieurs locataires](./multiple-tenants-service-providers.md) dans Microsoft Sentinel, à l’aide d’Azure Lighthouse.
- Découvrez comment [afficher et gérer les incidents dans plusieurs espaces de travail](./multiple-workspace-view.md) en toute transparence.
