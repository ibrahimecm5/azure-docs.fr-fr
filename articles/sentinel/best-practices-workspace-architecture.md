---
title: Meilleures pratiques pour l’architecture de l’espace de travail Microsoft Sentinel
description: Découvrez les meilleures pratiques pour la conception de votre espace de travail Microsoft Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 83909e0ef8667b4ee466b9e8f1ad04a717e8302a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524756"
---
# <a name="microsoft-sentinel-workspace-architecture-best-practices"></a>Meilleures pratiques pour l’architecture de l’espace de travail Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Lorsque vous planifiez le déploiement de votre espace de travail Microsoft Sentinel, vous devez également concevoir votre architecture d’espace de travail Log Analytics. Les décisions relatives à l’architecture de l’espace de travail sont généralement régies par des exigences techniques et commerciales. Cet article passe en revue les principaux facteurs de décision pour vous aider à déterminer l’architecture d’espace de travail adaptée à vos organisations, notamment :

- Utilisation d’un seul locataire ou de plusieurs locataires
- Toutes les exigences de conformité que vous avez pour la collecte et le stockage des données
- Mode de contrôle de l’accès aux données Microsoft Sentinel
- Conséquences sur le coût pour différents scénarios

Pour plus d’informations, consultez [Concevoir votre architecture d’espace de travail Microsoft Sentinel](design-your-workspace-architecture.md) et [Exemples de conceptions d’espace de travail](sample-workspace-designs.md) pour connaître les scénarios courants et [Activités de prédéploiement et prérequis pour le déploiement de Microsoft Sentinel](prerequisites.md).

Consultez notre vidéo : [Architecting SecOps for Success: Best Practices for Deploying Microsoft Sentinel](https://youtu.be/DyL9MEMhqmI)


## <a name="tenancy-considerations"></a>Considérations relatives à la location

Bien qu’un nombre réduit d’espaces de travail soient plus simples à gérer, vous pouvez avoir des besoins spécifiques pour plusieurs locataires et espaces de travail. Par exemple, de nombreuses organisations disposent d’un environnement cloud contenant plusieurs [locataires Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md), qui résultent de fusions et d’acquisitions ou en raison des exigences de séparation des identités.

Lorsque vous déterminez le nombre de locataires et d’espaces de travail à utiliser, considérez que la plupart des fonctionnalités Microsoft Sentinel fonctionnent à l’aide d’un seul espace de travail ou d’une seule instance Microsoft Sentinel, et Microsoft Sentinel ingère tous les journaux hébergés dans l’espace de travail.

> [!IMPORTANT]
> Les coûts sont l’une des principales considérations à prendre en compte lors de la détermination de l’architecture Microsoft Sentinel. Pour plus d’informations, consultez [Coûts et facturation Microsoft Sentinel](azure-sentinel-billing.md).
>
### <a name="working-with-multiple-tenants"></a>Utilisation de plusieurs locataires

Si vous avez plusieurs locataires, par exemple si vous êtes un fournisseur de services de sécurité gérée (MSSP), nous vous recommandons de créer au moins un espace de travail pour chaque locataire Azure AD pour prendre en charge les [connecteurs de données de service à service](connect-data-sources.md#service-to-service-integration) intégrés qui fonctionnent uniquement dans leur propre locataire Azure AD.

Tous les connecteurs basés sur les paramètres de diagnostic ne peuvent pas être connectés à un espace de travail qui ne se trouve pas dans le même locataire que celui de la ressource. Cela s’applique aux connecteurs tels que [Pare-feu Azure](./data-connectors-reference.md#azure-firewall), [Stockage Azure](./data-connectors-reference.md#azure-storage-account), [Azure Activity](./data-connectors-reference.md#azure-activity) ou [Azure Active Directory](connect-azure-active-directory.md).

Utilisez [Azure Lighthouse](../lighthouse/how-to/onboard-customer.md) pour gérer plusieurs instances Microsoft Sentinel dans divers locataires.

> [!NOTE]
> Les [connecteurs de données partenaires](data-connectors-reference.md) sont souvent basés sur des regroupements d’API ou d’agent, et ne sont donc pas attachés à un locataire Azure AD spécifique.
>



## <a name="compliance-considerations"></a>Considérations relatives à la conformité

Une fois que vos données sont collectées, stockées et traitées, la conformité peut devenir une exigence de conception importante, avec un impact significatif sur votre architecture Microsoft Sentinel. La possibilité de valider et de prouver qui a accès aux données dans toutes les conditions est une exigence de souveraineté des données critique dans de nombreux pays et régions, et l’évaluation des risques et l’obtention d’insights dans les flux de travail Microsoft Sentinel est une priorité pour de nombreux clients.

Dans Microsoft Sentinel, les données sont essentiellement stockées et traitées dans la même zone géographique ou région, à quelques exceptions près, par exemple lors de l’utilisation de règles de détection qui tirent parti de l’apprentissage automatique de Microsoft. Dans de tels cas, les données peuvent être copiées en dehors de la zone géographique de votre espace de travail pour traitement.

Pour plus d'informations, consultez les pages suivantes :

- [Disponibilité géographique et résidence des données](quickstart-onboard.md#geographical-availability-and-data-residency)
- [Résidence des données dans Azure](https://azure.microsoft.com/global-infrastructure/data-residency/)
- [Stockage et traitement des données EU dans l’Union européenne – Blog de la politique de l’Union européenne](https://blogs.microsoft.com/eupolicy/2021/05/06/eu-data-boundary/)

Pour commencer à valider votre conformité, évaluez vos sources de données et comment et où elles envoient des données.

> [!NOTE]
> L'[agent Log Analytics](connect-windows-security-events.md) prend en charge TLS 1.2 pour garantir la sécurité des données en transit entre l’agent et le service Log Analytics, ainsi que la norme FIPS 140. 
>
> Si vous envoyez des données à une zone géographique ou à une région différente de votre espace de travail Microsoft Sentinel, que la ressource d’envoi réside dans Azure ou non, utilisez un espace de travail dans la même région géographique ou la même région.
>

## <a name="region-considerations"></a>Aspects à prendre en compte au sujet des régions

Utilisez des instances Microsoft Sentinel distinctes pour chaque région. Bien que Microsoft Sentinel puisse être utilisé dans plusieurs régions, il est possible que vous ayez besoin de séparer les données par équipe, région ou site, ou par des réglementations et des contrôles qui rendent les modèles à plusieurs régions impossibles ou plus complexes que nécessaire. L’utilisation d’instances et d’espaces de travail distincts pour chaque région permet d’éviter des coûts de bande passante et de sortie pour déplacer des données entre les régions.

Prenez les points suivants en compte lorsque vous travaillez avec plusieurs régions :

- Les coûts de sortie s’appliquent généralement lorsque l'[agent Log Analytics ou Azure Monitor](connect-windows-security-events.md) est requis pour collecter des journaux, par exemple sur des machines virtuelles.

- La sortie Internet est également facturée, ce qui peut ne pas vous affecter, sauf si vous exportez des données en dehors de votre espace de travail Log Analytics. Par exemple, vous pouvez payer des frais de sortie Internet si vous exportez vos données Log Analytics sur un serveur local.

- Les coûts de bande passante varient en fonction de la région source et de la région de destination et de la méthode de collecte. Pour plus d'informations, consultez les pages suivantes :

    - [Tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/)
    - [Frais de transfert de données à l’aide de Log Analytics ](../azure-monitor/logs/manage-cost-storage.md).

- Utilisez des modèles pour vos règles d’analyse, des requêtes personnalisées, des classeurs et d’autres ressources pour améliorer l’efficacité de vos déploiements. Déployez les modèles au lieu de déployer manuellement chaque ressource dans chaque région.

- Les connecteurs basés sur les paramètres de diagnostic n’entraînent pas de coûts de bande passante. Pour plus d’informations, consultez [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](../azure-monitor/logs/manage-cost-storage.md#data-transfer-charges-using-log-analytics).

Par exemple, si vous décidez de collecter des journaux à partir de machines virtuelles dans la région USA Est et de les envoyer à un espace de travail Microsoft Sentinel dans la région USA Ouest, vous aurez des coûts d’entrée pour le transfert de données. Étant donné que l’agent Log Analytics compresse les données en transit, la taille facturée pour la bande passante peut être inférieure à la taille des journaux dans Microsoft Sentinel.

Si vous collectez des journaux Syslog et CEF à partir de plusieurs sources dans le monde, vous souhaiterez peut-être configurer un collecteur Syslog dans la même région que celle de votre espace de travail Microsoft Sentinel afin d’éviter des coûts de bande passante, à condition que la conformité ne soit pas un problème.

Le fait de savoir si les coûts de bande passante justifient des espaces de travail Microsoft Sentinel distincts dépend du volume de données que vous devez transférer entre les régions. Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/details/bandwidth/) pour estimer les coûts.

Pour plus d’informations, consultez la section [Résidence des données dans Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).

## <a name="access-considerations"></a>Aspects à prendre en compte au sujet de l'accès

Vous pouvez être amené à planifier des situations où différentes équipes auront besoin d’accéder aux mêmes données. Par exemple, votre équipe SOC doit avoir accès à toutes les données Microsoft Sentinel, tandis que les équipes des opérations et des applications auront besoin d’accéder uniquement à des parties spécifiques. Les équipes de sécurité indépendantes peuvent également avoir besoin d’accéder aux fonctionnalités Microsoft Sentinel, mais avec différents ensembles de données.

Combinez le [RBAC de contexte de ressource](resource-context-rbac.md) et le [RBAC au niveau de la table](../azure-monitor/logs/manage-access.md#table-level-azure-rbac) pour fournir à vos équipes une grande variété d’options d’accès qui devraient prendre en charge la plupart des cas d'usage.

Pour plus d’informations, consultez [Autorisations dans Microsoft Sentinel](roles.md).

### <a name="resource-context-rbac"></a>RBAC de contexte de ressource

L’illustration suivante montre une version simplifiée d’une architecture d’espace de travail dans laquelle les équipes de sécurité et d’opérations doivent accéder à différents ensembles de données, et le RBAC de contexte de ressource est utilisé pour fournir les autorisations requises.


[ ![Diagramme d’un exemple d’architecture pour le RBAC du contexte de ressource.](media/resource-context-rbac/resource-context-rbac-sample.png) ](media/resource-context-rbac/resource-context-rbac-sample.png#lightbox)

Dans cette image, l’espace de travail Microsoft Sentinel est placé dans un abonnement distinct pour mieux isoler les autorisations.

> [!NOTE]
> Une autre option consiste à placer Microsoft Sentinel dans un groupe d’administration distinct dédié à la sécurité, ce qui permet de s’assurer que seules les affectations d’autorisations minimales sont héritées. Au sein de l’équipe de sécurité, plusieurs groupes reçoivent des autorisations selon leurs fonctions. Étant donné que ces équipes ont accès à l’ensemble de l’espace de travail, elles ont accès à l’expérience Microsoft Sentinel complète, restreinte uniquement par les rôles Microsoft Sentinel auxquels elles sont attribuées. Pour plus d’informations, consultez [Autorisations dans Microsoft Sentinel](roles.md).
>

En plus de l’abonnement de sécurité, un abonnement distinct est utilisé pour que les équipes d’applications hébergent leurs charges de travail. Les équipes d’applications se voient accorder l’accès à leurs groupes de ressources respectifs, où elles peuvent gérer leurs ressources. Cet abonnement distinct et le RBAC du contexte de ressource permettent à ces équipes d’afficher les journaux générés par les ressources auxquelles elles ont accès, même lorsque les journaux sont stockés dans un espace de travail où elles *n’ont pas* d’accès direct. Les équipes d’applications peuvent accéder à leurs journaux via la zone **journaux d’activité** du portail Azure, pour afficher les journaux d’activité d’une ressource spécifique, ou via Azure Monitor, pour afficher tous les journaux d'activité auxquels elles peuvent accéder en même temps.

Les ressources Azure offrent une prise en charge intégrée du RBAC de contexte de ressource, mais elles peuvent nécessiter un réglage plus précis lors de l’utilisation de ressources non Azure. Pour plus d’informations, consultez [Configurer explicitement un RBAC dans le contexte de la ressource](resource-context-rbac.md#explicitly-configure-resource-context-rbac).

### <a name="table-level-rbac"></a>Table-level RBAC

Le RBAC au niveau de la table vous permet de définir des types de données spécifiques (tables) pour qu’ils soient accessibles uniquement à un ensemble spécifique d’utilisateurs.

Par exemple, considérez si l’organisation dont l’architecture est décrite dans l’image ci-dessus doit également accorder l’accès aux journaux Office 365 à une équipe d’audit interne. Dans ce cas, ils peuvent utiliser RBAC au niveau de la table pour accorder à l’équipe d’audit un accès à l’ensemble de la table **OfficeActivity**, sans octroyer d’autorisations à une autre table.

### <a name="access-considerations-with-multiple-workspaces"></a>Considérations relatives à l’accès avec plusieurs espaces de travail

Si vous avez des entités, des filiales ou des zones géographiques différentes au sein de votre organisation, chacune avec ses propres équipes de sécurité qui ont besoin d’accéder à Microsoft Sentinel, utilisez des espaces de travail distincts pour chaque entité ou filiale. Implémentez les espaces de travail distincts au sein d’un seul locataire Azure AD, ou sur plusieurs locataires à l’aide d’Azure Lighthouse. 

Votre équipe SOC centrale peut également utiliser un espace de travail Microsoft Sentinel facultatif supplémentaire pour gérer des artefacts centralisés, tels que les règles d’analyse ou les classeurs.

Pour plus d’informations, consultez [Simplifier l’utilisation de plusieurs espaces de travail](#simplify-working-with-multiple-workspaces).


## <a name="technical-best-practices-for-creating-your-workspace"></a>Meilleures pratiques techniques pour la création de votre espace de travail

Utilisez les meilleures pratiques suivantes lors de la création de l’espace de travail Log Analytics que vous utiliserez pour Microsoft Sentinel :

- **Lorsque vous nommez votre espace de travail**, incluez *Microsoft Sentinel* ou un autre indicateur dans le nom afin qu’il soit facilement identifiable parmi vos autres espaces de travail.

- **Utilisez le même espace de travail pour Microsoft Sentinel et Microsoft Defender pour le Cloud**, afin que tous les journaux collectés par Microsoft Defender pour le Cloud puissent également être ingérés et utilisés par Microsoft Sentinel. L’espace de travail par défaut créé par Microsoft Defender pour le Cloud n’apparaît pas en tant qu’espace de travail disponible pour Microsoft Sentinel.

- **Utilisez un cluster d’espace de travail dédié si l’ingestion de données projetée est d’environ 1 To par jour**. Un [cluster dédié](../azure-monitor/logs/logs-dedicated-clusters.md) vous permet de sécuriser les ressources de vos données Microsoft Sentinel, ce qui permet d’améliorer les performances des requêtes pour les jeux de données volumineux. Les clusters dédiés fournissent également une option permettant d’accroître le chiffrement et le contrôle des clés de votre organisation.

## <a name="simplify-working-with-multiple-workspaces"></a>Simplifier l’utilisation de plusieurs espaces de travail

Si vous avez besoin de travailler avec plusieurs espaces de travail, simplifiez la gestion et l’investigation des incidents en [condensant et en répertoriant tous les incidents de chaque instance Microsoft Sentinel dans un emplacement unique](multiple-workspace-view.md).

Pour référencer des données présentes dans d’autres espaces de travail Microsoft Sentinel, tels que dans les [classeurs pour plusieurs espaces de travail](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks), utilisez des [requêtes pour plusieurs espaces de travail](extend-sentinel-across-workspaces-tenants.md).

Le meilleur moment pour utiliser des requêtes entre les espaces de travail est lorsque des informations importantes sont stockées dans un espace de travail, un abonnement ou un locataire différent et peuvent fournir une valeur à votre action actuelle. Par exemple, le code suivant illustre un exemple de requête pour plusieurs espaces de travail :

```Kusto
union Update, workspace("contosoretail-it").Update, workspace("WORKSPACE ID").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

Pour plus d’informations, consultez [Étendre Microsoft Sentinel dans les espaces de travail et les locataires](extend-sentinel-across-workspaces-tenants.md).
## <a name="next-steps"></a>Étapes suivantes


> [!div class="nextstepaction"]
>[Concevoir votre architecture d’espace de travail Microsoft Sentinel](design-your-workspace-architecture.md)

> [!div class="nextstepaction"]
>[Exemples de conceptions d’espace de travail Microsoft Sentinel](sample-workspace-designs.md)

> [!div class="nextstepaction"]
>[Microsoft Sentinel intégré](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtenir une visibilité des alertes](get-visibility.md)
