---
title: Exemples de conceptions d’espace de travail Microsoft Sentinel | Microsoft Docs
description: Découvrez des exemples de conceptions d’architecture Microsoft Sentinel avec plusieurs locataires, clouds ou régions.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cb2332a4c9854aafe1ef8193da9ca76e9aaaed95
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520063"
---
# <a name="microsoft-sentinel-sample-workspace-designs"></a>Exemples de conceptions d’espace de travail Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article décrit des modèles d’espace de travail suggérés pour les organisations avec les exemples d’exigences suivants :

- Plusieurs locataires et régions, avec des exigences de souveraineté des données
- Locataire unique avec plusieurs clouds
- Plusieurs locataires, avec plusieurs régions et une sécurité centralisée

Les exemples de cet article utilisent l'[arbre de décision de conception d’espace de travail Microsoft Sentinel](design-your-workspace-architecture.md) pour déterminer la meilleure conception d’espace de travail pour chaque organisation. Pour plus d’informations, consultez [Meilleures pratiques pour l’architecture de l’espace de travail Microsoft Sentinel](best-practices-workspace-architecture.md).

## <a name="sample-1-multiple-tenants-and-regions"></a>Exemple 1 : plusieurs locataires et régions

Contoso Corporation est une entreprise multinationale dont le siège social est situé à Londres. Contoso a des bureaux dans le monde entier, avec des hubs importants à New York et Tokyo. Récemment, Contoso a migré sa suite de productivité vers Office 365, avec de nombreuses charges de travail migrées vers Azure.

### <a name="contoso-tenants"></a>Locataires contoso

Suite à une acquisition il y a plusieurs années, Contoso dispose de deux locataires Azure AD : `contoso.onmicrosoft.com` et `wingtip.onmicrosoft.com`. Chaque locataire a sa propre instance Office 365 et plusieurs abonnements Azure, comme illustré dans l’image suivante :

:::image type="content" source="media/best-practices/contoso-tenants.png" alt-text="Diagramme des locataires Contoso, chacun avec des ensembles distincts d’abonnements." border="false":::

### <a name="contoso-compliance-and-regional-deployment"></a>Conformité et déploiement régional de Contoso

Contoso possède actuellement des ressources Azure hébergées dans trois régions différentes : USA Est, Europe Nord et Japon Ouest, et l’exigence stricte de conserver toutes les données générées en Europe dans les régions d’Europe.

Les deux locataires Azure AD de Contoso ont des ressources dans les trois régions : USA Est, Europe Nord et Japon Ouest

### <a name="contoso-resource-types-and-collection-requirements"></a>Types de ressources et exigences de collection de Contoso

Contoso doit collecter des événements à partir des sources de données suivantes :

-   Office 365
-   Journaux de connexion et d’audit Azure AD
-   Activité Azure
-   Événements de sécurité Windows, des sources locales et de machines virtuelles Azure
-   Syslog, des sources locales et de machines virtuelles Azure
-   CEF, de plusieurs appareils réseau locaux, tels que Palo Alto, Cisco ASA et Cisco Meraki
-   Plusieurs ressources PaaS Azure, telles que le pare-feu Azure, AKS, Key Vault, Stockage Azure et Azure SQL
-   Cisco Umbrella

Les machines virtuelles Azure sont principalement situées dans la région Europe Nord, avec seulement quelques-unes dans les régions USA Est et Japon Ouest. Contoso utilise Microsoft Defender pour les serveurs sur toutes leurs machines virtuelles Azure.

Contoso s’attend à ingérer environ 300 Go/jour de toutes leurs sources de données.

### <a name="contoso-access-requirements"></a>Exigences d’accès de Contoso

L’environnement Azure de Contoso possède déjà un espace de travail Log Analytics existant et utilisé par l’équipe des opérations pour surveiller l’infrastructure. Cet espace de travail se trouve dans le locataire Contoso AAD, dans la région Europe Nord, et est utilisé pour collecter les journaux des machines virtuelles Azure dans toutes les régions. Ils ingèrent actuellement environ 50 Go/jour.

L’équipe des opérations de Contoso doit accéder à tous les journaux dont ils disposent actuellement dans l’espace de travail, qui incluent plusieurs types de données non nécessaires au SOC, tels que **Perf**, **InsightsMetrics**, **ContainerLog**, et d’autres encore. L’équipe des opérations ne doit *pas* avoir accès aux nouveaux journaux qui seront collectés dans Microsoft Sentinel.

### <a name="contosos-solution"></a>Solution de Contoso

Les étapes suivantes appliquent l'[arbre de décision de conception d’espace de travail Microsoft Sentinel](design-your-workspace-architecture.md) pour déterminer la meilleure conception d’espace de travail pour Contoso :

1. Contoso dispose déjà d’un espace de travail existant. Nous pouvons donc explorer l’activation de Microsoft Sentinel dans ce même espace de travail.

    L’ingestion de données non-SOC est inférieure à 100 Go/jour. Nous pouvons donc passer à l'[étape 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies) et veiller à sélectionner l’option appropriée à l'[étape 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data).

1.  Contoso a des exigences réglementaires. Nous avons donc besoin d’au moins un espace de travail Microsoft Sentinel en Europe.

1.  Contoso possède deux locataires Azure AD différents et collecte à partir de sources de données au niveau du locataire, comme des journaux Office 365 et des journaux de connexion et d’audit Azure AD. Nous avons donc besoin d’au moins un espace de travail par locataire.

1.  Contoso n’a pas besoin de [réimputer](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back). Nous pouvons donc passer à l’[étape 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data).

1.  Contoso doit collecter des données non-SOC, bien qu’il n’y ait pas de chevauchement entre les données SOC et non-SOC. En outre, les données SOC sont estimées à 250 Go/jour environ. Ils doivent donc utiliser des espaces de travail distincts pour des raisons de rentabilité.

1.  La majorité des machines virtuelles de Contoso se trouvent dans la région Europe Nord, où elles disposent déjà d’un espace de travail. Dans ce cas, les coûts de bande passante ne sont donc pas un problème.

1.  Contoso dispose d’une équipe SOC unique qui utilisera Microsoft Sentinel. Aucune séparation supplémentaire n’est donc nécessaire.

1.  Tous les membres de l’équipe SOC de Contoso auront accès à toutes les données. Aucune séparation supplémentaire n’est donc nécessaire.

La conception d’espace de travail Microsoft Sentinel obtenue pour Contoso est illustrée dans l’image suivante :

:::image type="content" source="media/best-practices/contoso-solution.png" alt-text="Diagramme de la solution de Contoso, avec un espace de travail distinct pour l’équipe des opérations." border="false":::

La solution suggérée inclut :

- Un espace de travail Log Analytics distinct pour l’équipe des opérations de Contoso. Cet espace de travail ne contiendra que les données non nécessaires à l’équipe SOC de Contoso, telles que les tables **Perf**, **InsightsMetrics** et **ContainerLog**.

- Deux espaces de travail Microsoft Sentinel, un dans chaque locataire Azure AD, pour ingérer des données à partir d’Office 365, d’une activité Azure, d’Azure AD et de tous les services PaaS Azure.

- Toutes les autres données, provenant de sources de données locales, peuvent être acheminées vers l’un des deux espaces de travail Microsoft Sentinel.


## <a name="sample-2-single-tenant-with-multiple-clouds"></a>Exemple 2 : locataire unique avec plusieurs clouds

Fabrikam est une organisation dont le siège social est situé à New York et disposant de bureaux dans tous les États-Unis. Fabrikam commence son parcours cloud et doit encore déployer sa première zone d’atterrissage Azure et migrer ses premières charges de travail. Fabrikam a déjà des charges de travail sur AWS, qu’ils envisagent de surveiller à l’aide de Microsoft Sentinel.

### <a name="fabrikam-tenancy-requirements"></a>Exigences de location de Fabrikam

Fabrikam a un seul locataire Azure AD.

### <a name="fabrikam-compliance-and-regional-deployment"></a>Conformité et déploiement régional de Fabrikam

Fabrikam n’a pas d’exigences de conformité. Fabrikam possède des ressources dans plusieurs régions Azure situées aux États-Unis, mais les coûts de bande passante entre les régions ne sont pas un problème majeur.

### <a name="fabrikam-resource-types-and-collection-requirements"></a>Types de ressources et exigences de collection de Fabrikam

Fabrikam doit collecter des événements à partir des sources de données suivantes :

-   Journaux de connexion et d’audit Azure AD
-   Activité Azure
-   Événements de sécurité, des sources locales et de machines virtuelles Azure
-   Événements Windows, des sources locales et de machines virtuelles Azure
-   Données de performances, des sources locales et de machines virtuelles Azure
-   AWS CloudTrail
-   Journaux d’audit et de performances AKS

### <a name="fabrikam-access-requirements"></a>Exigences d’accès de Fabrikam

L’équipe des opérations de Fabrikam doit accéder aux informations suivantes :

-   Événements de sécurité et événements Windows, des sources locales et de machines virtuelles Azure
-   Données de performances, des sources locales et de machines virtuelles Azure
-   Journaux de performances (Container Insights) et d’audit AKS
-   Toutes les données d’activité Azure

L’équipe SOC de Fabrikam doit accéder aux informations suivantes :
-   Journaux de connexion et d’audit Azure AD
-   Toutes les données d’activité Azure
-   Événements de sécurité, des sources locales et de machines virtuelles Azure
-   Journaux AWS CloudTrail
-   Journaux d’audit AKS
-   Le portail Microsoft Sentinel complet

### <a name="fabrikams-solution"></a>Solution de Fabrikam

Les étapes suivantes appliquent l'[arbre de décision de conception d’espace de travail Microsoft Sentinel](design-your-workspace-architecture.md) pour déterminer la meilleure conception d’espace de travail pour Fabrikam :

1.  Fabrikam n’a aucun espace de travail existant. Par conséquent, passez à l’[étape 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies).

1.  Fabrikam n’a aucune exigence réglementaire. Par conséquent, passez à l’[étape 3](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants).

1.  Fabrikam possède un environnement à locataire unique. Par conséquent, passez à l’[étape 4](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back).

1.  Fabrikam n’a pas besoin de fractionner les frais. Par conséquent, passez à l'[étape 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data).

1.  Fabrikam aura besoin d’espaces de travail distincts pour ses équipes SOC et des opérations :

    L’équipe des opérations de Fabrikam doit collecter les données de performances, des machines virtuelles et d’AKS. AKS étant basé sur les paramètres de diagnostic, ils peuvent sélectionner des journaux spécifiques à envoyer vers des espaces de travail spécifiques. Fabrikam peut choisir d’envoyer les journaux d’audit AKS vers l’espace de travail Microsoft Sentinel et tous les journaux AKS vers un espace de travail distinct, où Microsoft Sentinel n’est pas activé. Dans l’espace de travail où Microsoft Sentinel n’est pas activé, Fabrikam activera la solution Container Insights.

    Pour les machines virtuelles Windows, Fabrikam peut utiliser l'[agent de surveillance Azure (AMA)](connect-windows-security-events.md#connector-options) pour fractionner les journaux, envoyer des événements de sécurité vers l’espace de travail Microsoft Sentinel et des événements de performances et Windows vers l’espace de travail sans Microsoft Sentinel.

    Fabrikam choisit de considérer leurs données qui se chevauchent, telles que les événements de sécurité et les événements d’activité Azure, comme des données SOC uniquement, et envoie ces données vers l’espace de travail avec Microsoft Sentinel.

1.  Les coûts de bande passante ne sont pas un problème majeur pour Fabrikam. Par conséquent, passez à l'[étape 7](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership).

1.  Fabrikam a déjà décidé d’utiliser des espaces de travail distincts pour les équipes SOC et des opérations. Aucune séparation supplémentaire n'est nécessaire.

1.  Fabrikam doit contrôler l’accès pour les données qui se chevauchent, notamment les événements de sécurité et les événements d’activité Azure, mais il n’y a pas d’exigence au niveau des lignes.

    Les événements de sécurité et les événements d’activité Azure ne sont pas des journaux personnalisés. Fabrikam peut donc utiliser RBAC au niveau de la table pour accorder l’accès à ces deux tables à l’équipe des opérations.

La conception d’espace de travail Microsoft Sentinel obtenue pour Fabrikam est illustrée dans l’image suivante, avec les sources de journaux principales seulement pour simplifier la conception :

:::image type="content" source="media/best-practices/fabrikam-solution.png" alt-text="Diagramme de la solution de Fabrikam, avec un espace de travail distinct pour l’équipe des opérations." border="false" :::

La solution suggérée inclut :

- Deux espaces de travail distincts dans la région des États-Unis : un pour l’équipe SOC avec Microsoft Sentinel activé, et un autre pour l’équipe des opérations, sans Microsoft Sentinel.

- L'[agent de surveillance Azure (AMA)](connect-windows-security-events.md#connector-options), utilisé pour déterminer les journaux qui sont envoyés à chaque espace de travail à partir d’Azure et de machines virtuelles locales.

- Les paramètres de diagnostic, utilisés pour déterminer les journaux qui sont envoyés à chaque espace de travail à partir de ressources Azure telles que AKS.

- Les données qui se chevauchent sont envoyées vers l’espace de travail Microsoft Sentinel, avec un RBAC au niveau de la table pour accorder l’accès à l’équipe des opérations en fonction des besoins.

## <a name="sample-3-multiple-tenants-and-regions-and-centralized-security"></a>Exemple 3 : plusieurs locataires et régions et une sécurité centralisée

Adventure Works est une multinationale dont le siège social est situé à Tokyo. Adventure Works comprend 10 sous-entités différentes, basées dans divers pays du monde entier.

Adventure Works est un client Microsoft 365 E5 et possède déjà des charges de travail dans Azure.

### <a name="adventure-works-tenancy-requirements"></a>Exigences de location d’Adventure Works

Adventure Works possède trois locataires Azure AD différents, un pour chacun des continents où ils possèdent des sous-entités : Asia, Europe et Afrique. Les pays des différentes sous-entités ont leurs identités dans le locataire du continent auquel ils appartiennent. Par exemple, les utilisateurs japonais se trouvent dans le locataire *Asie*, les utilisateurs allemands dans le locataire *Europe* et les utilisateurs égyptiens dans le locataire *Afrique*.

### <a name="adventure-works-compliance-and-regional-requirements"></a>Exigences de conformité et régionales d’Adventure Works

Adventure Works utilise actuellement trois régions Azure, chacune alignée avec le continent dans lequel les sous-entités se trouvent. Adventure Works n’a pas d’exigences de conformité strictes.

### <a name="adventure-works-resource-types-and-collection-requirements"></a>Types de ressources et exigences de collection d’Adventure Works

Adventure Works doit collecter les sources de données suivantes pour chaque sous-entité :

-   Journaux de connexion et d’audit Azure AD
-   Journaux Office 365
-   Journaux bruts Microsoft 365 Defender pour point de terminaison
-   Activité Azure
-   Microsoft Defender pour le cloud
-   Ressources PaaS Azure, telles que le pare-feu Azure, Stockage Azure, Azure SQL et Azure WAF
-   Événements de sécurité et Windows de machines virtuelles Azure
-   Journaux CEF d’appareils réseau locaux

Les machines virtuelles Azure sont disséminées sur les trois continents, mais les coûts de bande passante ne sont pas un problème.

### <a name="adventure-works-access-requirements"></a>Exigences d’accès d’Adventure Works

Adventure Works dispose d’une équipe SOC centralisée unique qui supervise les opérations de sécurité pour toutes les sous-entités.

Adventure Works dispose également de trois équipes SOC indépendantes, une pour chacun des continents. L’équipe SOC de chaque continent doit pouvoir accéder aux données générées dans sa région uniquement, sans voir les données d’autres continents. Par exemple, l’équipe SOC d’Asie doit accéder aux données des ressources Azure déployées en Asie uniquement, aux connexions AAD à partir du locataire Asie et aux journaux de Defender pour point de terminaison de son locataire Asie.

L’équipe SOC de chaque continent doit accéder à l’expérience complète du portail Microsoft Sentinel.

L’équipe des opérations d’Adventure Works est indépendante et possède ses propres espaces de travail sans Microsoft Sentinel.

### <a name="adventure-works-solution"></a>Solution d’Adventure Works

Les étapes suivantes appliquent l'[arbre de décision de conception d’espace de travail Microsoft Sentinel](design-your-workspace-architecture.md) pour déterminer la meilleure conception d’espace de travail pour Adventure Works :

1.  L’équipe des opérations d’Adventure Works possède ses propres espaces de travail. Par conséquent, passez à l'[étape 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies).

1.  Adventure Works n’a aucune exigence réglementaire. Par conséquent, passez à l’[étape 3](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants).

1.  Adventure Works possède trois locataires Azure AD et doit collecter des sources de données au niveau du locataire, telles que des journaux Office 365. Par conséquent, Adventure Works doit créer au minimum des espaces de travail Microsoft Sentinel, un pour chaque locataire.

1.  Adventure Works n’a pas besoin de fractionner les frais. Par conséquent, passez à l'[étape 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data).

1.  L’équipe des opérations d’Adventure Works disposant de ses propres espaces de travail, toutes les données prises en compte dans cette décision seront utilisées par l’équipe SOC d’Adventure Works.

1.  Les coûts de bande passante ne sont pas un problème majeur pour Adventure Works. Par conséquent, passez à l'[étape 7](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership).

1.  Adventure Works doit séparer les données par propriété, car l’équipe SOC de chaque contenu a besoin d’accéder aux données pertinentes pour ce contenu uniquement. Toutefois, l’équipe SOC de chaque continent doit également accéder au portail Microsoft Sentinel complet.

1.  Adventure Works n’a pas besoin de contrôler l’accès aux données par table.

La conception d’espace de travail Microsoft Sentinel obtenue pour Adventure Works est illustrée dans l’image suivante, avec les sources de journaux principales seulement pour simplifier la conception :

:::image type="content" source="media/best-practices/adventure-works-solution.png" alt-text="Diagramme de la solution d’Adventure Works, avec des espaces de travail distincts pour chaque locataire Azure AD." border="false":::

La solution suggérée inclut :

- Un espace de travail Microsoft Sentinel distinct pour chaque locataire Azure AD. Chaque espace de travail collecte les données relatives à son locataire pour toutes les sources de données.

- L’équipe SOC de chaque continent a accès à l’espace de travail de son propre locataire uniquement, ce qui garantit que seuls les journaux générés dans la limite du locataire sont accessibles à chaque équipe SOC.

- L’équipe SOC centrale peut toujours fonctionner à partir d’un locataire Azure AD distinct, à l’aide d’Azure Lighthouse pour accéder à chacun des différents environnements Microsoft Sentinel. S’il n’y a pas d’autre locataire, l’équipe SOC centrale peut toujours utiliser Azure Lighthouse pour accéder aux espaces de travail distants.

- L’équipe SOC centrale peut également créer un espace de travail supplémentaire si elle doit stocker des artefacts qui restent cachés pour les équipes SOC de continent, ou si elle souhaite ingérer d’autres données qui ne sont pas pertinentes pour les équipes SOC de continent.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Microsoft Sentinel intégré](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtenir une visibilité des alertes](get-visibility.md)
