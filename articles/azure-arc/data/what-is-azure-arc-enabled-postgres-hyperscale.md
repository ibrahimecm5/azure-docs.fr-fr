---
title: Présentation d’Azure Arc avec PostgreSQL Hyperscale
description: Présentation d’Azure Arc avec PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: fa2a244a0a8e2181f5e3d396980c8239ab868584
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552979"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Présentation d’Azure Arc avec PostgreSQL Hyperscale

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="what-is-azure-arc-vs-azure-arc-enabled-data-services-vs-azure-arc-enabled-postgresql-hyperscale"></a>Présentation et comparaison d’Azure Arc, des services de données avec Azure Arc et de PostgreSQL Hyperscale avec Azure Arc.

**Azure Arc** est l’un des piliers de la famille Azure Hybrid : Azure Arc, Azure Stack et Azure IoT. Azure Arc permet aux clients de gérer la complexité de leurs déploiements hybrides en simplifiant l’expérience client. Avec Azure Stack, Microsoft ou ses partenaires fournissent le matériel et le logiciel (appliance). Avec Azure Arc, Microsoft fournit le logiciel uniquement. Le client ou ses partenaires fournissent l’infrastructure sous-jacente et utilisent la solution. Azure Arc est pris en charge sur Azure Stack. Azure Arc vous permet d’exécuter des services Azure sur des infrastructures qui résident en dehors des centres de données Azure et permet l’intégration à d’autres services gérés Azure si vous le souhaitez.

**Les services de données avec Azure Arc** sont intégrés à Azure Arc. Il s’agit d’une suite de produits et de services qui permet aux clients de gérer leurs données. Il permet aux clients d’effectuer les opérations suivantes :

- Exécuter les services de données Azure sur n’importe quelle infrastructure physique
- Optimiser vos opérations à l’aide d’une technologie cloud unique partout
- Optimiser les développements de vos applications à l’aide de la même technologie sous-jacente, quel que soit l’emplacement d’hébergement de votre application ou de votre base de données (dans Azure PaaS ou dans Azure Arc)
- Utilisez les technologies cloud dans votre propre centre de données tout en satisfaisant aux exigences réglementaires (résidence des données et contrôle du client). En d’autres termes : « Si tu ne viens pas au cloud, le cloud ira à toi ! »

Parmi les valeurs fournies par les services de données Azure Arc, citons :
- Toujours à jour
- Mise à l’échelle élastique
- Provisionnement en libre-service
- Gestion unifiée
- Facturation cloud
- Prise en charge des scénarios connectés (vers Azure) et occasionnellement connectés (à Azure). (modes de connectivité directe et indirecte)

**PostgreSQL Hyperscale avec Azure Arc** est l’un des moteurs de base de données disponibles dans le cadre des services de données avec Azure Arc. 


## <a name="compare-postgres-solutions-provided-by-microsoft-in-azure"></a>Comparer les solutions Postgres fournies par Microsoft dans Azure

Microsoft offre des services de base de données Postgres dans Azure de deux manières :
- En tant que service géré dans Azure PaaS (platform as a service)
- En tant que service semi-géré avec Azure Arc, car il est utilisé par les clients ou leurs partenaires/fournisseurs

### <a name="in-azure-paas"></a>Dans Azure PaaS
**Dans le [Azure PaaS](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)** , Microsoft offre plusieurs options de déploiement pour Postgres en tant que service géré :

:::row:::
    :::column:::
        Azure Database pour PostgreSQL – Serveur unique et Azure Database pour PostgreSQL – Serveur flexible. Ces services sont des facteurs de forme Postgres à nœud unique/à instance unique gérés par Microsoft. Le serveur flexible Azure Database pour Postgres est l’évolution la plus récente de ce service.
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/azure-database-for-postgresql-bigger.png" alt-text="Base de données Azure pour PostgreSQL":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Azure Database pour PostgreSQL Hyperscale (Citus). Ce service est le facteur de forme Postgres à nœud multiples/à instances multiples géré par Microsoft. Il s’appuie sur l’extension Citus pour Postgres qui transforme le système Postgres à nœud unique en un système de base de données distribué. Quand vous effectuez le scale-out, il distribue les données et les requêtes susceptibles de permettre à votre charge de travail d’atteindre des niveaux de mise à l’échelle et de performances inégalés. L’application voit une seule instance Postgres, également appelée groupe de serveurs. Toutefois, ce groupe de serveurs est constitué en interne de plusieurs instances Postgres qui fonctionnent ensemble. Quand vous effectuez un scale-out, vous augmentez le nombre d’instances Postgres au sein du groupe de serveurs qui sont susceptibles d’améliorer les performances et la scalabilité de votre charge de travail. En fonction de vos besoins et des caractéristiques de la charge de travail, vous déterminez le nombre d’instances Postgres à ajouter au groupe de serveurs. 
        Pour plus d’insights, regardez les vidéos suivantes :
        - [DÉMO : HTAP haute performance avec Postgres Hyperscale (Citus) :](https://youtu.be/W_3e07nGFxY )
        - [DÉMO : création d’applications HTAP avec Python et Postgres sur Azure :](https://youtu.be/YDT8_riLLs0 )
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Hyperscale (Citus) - Azure Database pour PostgreSQL":::
    :::column-end:::
:::row-end:::



### <a name="with-azure-arc"></a>Avec Azure Arc

:::row:::
    :::column:::
        **Avec Azure Arc**, Microsoft offre **un** produit/service Postgres unique : **PostgreSQL Hyperscale avec Azure Arc**. Avec Azure Arc, nous avons simplifié la définition de produit et l’expérience client pour Postgres par rapport à Azure PaaS en fournissant **un produit Postgres** offrant les capacités suivantes :
        - déploiement de Postgres à nœud unique/à instance unique comme Azure Database pour Postgres serveur flexible/unique,
        - déploiement de Postgres à nœuds multiples/à instances multiples comme Azure Database pour PostgreSQL Hyperscale (Citus),
        - grande flexibilité permettant aux clients de transformer leurs déploiements Postgres à seul nœud en déploiements Postgres à nœuds multiples, et inversement s’ils le souhaitent. Ces opérations sont possibles sans migration des données, avec une expérience simple.
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="PostgreSQL Hyperscale avec Azure Arc":::
    :::column-end:::
:::row-end:::

Comme le service frère dans Azure PaaS, Postgres s’appuie dans sa forme nœuds multiples/instances multiples sur l’extension Citus qui transforme le système Postgres à nœud unique en système de base de données distribuée. Quand vous effectuez le scale-out, il distribue les données et les requêtes susceptibles de permettre à votre charge de travail d’atteindre des niveaux de mise à l’échelle et de performances inégalés. L’application voit une seule instance Postgres, également appelée groupe de serveurs. Toutefois, ce groupe de serveurs est constitué en interne de plusieurs instances Postgres qui fonctionnent ensemble. Quand vous effectuez un scale-out, vous augmentez le nombre d’instances Postgres au sein du groupe de serveurs, ce qui est susceptible d’améliorer les performances et la scalabilité de votre charge de travail. En fonction de vos besoins et des caractéristiques de la charge de travail, vous déterminez le nombre d’instances Postgres à ajouter au groupe de serveurs. Si vous le souhaitez, vous pouvez réduire le nombre d’instances Postgres dans le groupe de serveurs jusqu’à 1.  Avec Azure Arc, tous les déploiements Postgres fournis par Microsoft sont compatibles avec l’Hyperscale.


Avec le mode de connectivité direct proposé par les services de données Azure Arc, vous pouvez déployer PostgreSQL Hyperscale avec Azure Arc à partir du portail Azure. Si vous utilisez le mode de connexion indirect, vous allez déployer PostgreSQL Hyperscale avec Azure Arc à partir de l’infrastructure qui l’héberge.

**Avec PostgreSQL Hyperscale avec Azure Arc, vous pouvez :**
- Gérer Postgres simplement
    - Provisionner/déprovisionner des instances Postgres avec une seule commande
    - À n’importe quelle échelle : scale-up/scale-down/scale-out/scale-in 
- Simplifier la supervision, le basculement, la sauvegarde, la mise à jour corrective/la mise à niveau, le contrôle d’accès et plus
- Créer des applications Postgres à des niveaux de mise à l’échelle et de performances inégalés
    - Effectuer un scale-out du calcul horizontal sur plusieurs instances Postgres
    - Distribuer des données et des requêtes
    - Exécuter l’extension Citus
    - Transformer PostgreSQL standard en un système de base de données distribué
- Déployer Postgres sur n’importe quelle infrastructure
    - Local, multicloud (AWS, GCP, Azure), Edge
- Intégration à Azure (facultatif)
- Payer les services utilisés (facturation à l’utilisation)
- Accéder au support technique de Microsoft sur Postgres

**Considérations supplémentaires :**
- PostgreSQL Hyperscale avec Azure Arc n’est pas un nouveau moteur de base de données ni une version spécifique d’un moteur de base de données existant. C’est le moteur de base de données qui s’exécute dans Azure PaaS. N’oubliez pas qu’avec Azure Arc : si tu ne viens pas au cloud Microsoft, le cloud Microsoft ira à toi ! L’innovation avec Azure Arc réside dans la façon dont Microsoft propose ce moteur de base de données et dans les expériences fournies par Microsoft avec ce moteur de base de données. 

- PostgreSQL Hyperscale avec Azure Arc n’est pas non plus une solution de réplication de données. Vos données d’entreprise restent dans votre déploiement Arc. Les données ne sont pas répliquées dans le cloud Azure, sauf si vous avez choisi de configurer une fonctionnalité du moteur de base de données comme la réplication de données/les réplicas de lecture. Dans ce cas, vos données peuvent être répliquées en dehors de votre déploiement Postgres : pas en raison d’Azure Arc, mais parce que vous avez choisi de configurer une fonctionnalité de réplication des données.

- Vous n’avez pas besoin d’utiliser un pilote ni un fournisseur spécifique pour que votre charge de travail s’exécute sur PostgreSQL Hyperscale avec Azure Arc. Toute « application Postgres » doit pouvoir s’exécuter sur PostgreSQL Hyperscale avec Azure Arc.

- Les opérations de scale-out et scale-in ne sont pas automatiques. Ces actions sont contrôlées par les utilisateurs. Les utilisateurs peuvent générer des scripts pour ces opérations et automatiser l’exécution de ces scripts. Toutes les charges de travail ne peuvent pas tirer parti du scale-out. Pour plus d’informations à ce sujet, consultez les recommandations de la section « Étapes suivantes ».

**Pour en savoir plus sur ces fonctionnalités, vous pouvez également vous référer à cet épisode de Data Exposed :**
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]


## <a name="roles-and-responsibilities-azure-managed-services-platform-as-a-service-paas-_vs_-azure-arc-enabled-data-services"></a>Rôles et responsabilités : comparaison des services gérés Azure (PaaS, platform as a service)  _et_ des services de données avec Azure Arc
:::image type="content" source="media/postgres-hyperscale/rr-azure-paas-vs-azure-arc.png" alt-text="Rôles et responsabilités Azure PaaS par rapport à Azure Arc":::

## <a name="next-steps"></a>Étapes suivantes
- **Faites un essai.** Démarrez rapidement avec [Démarrage rapide d’Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) sur Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure. 

- **Déployez-le, créez-en un.** Pour créer sur votre propre cluster Kubernetes, procédez comme suit : 
   1. [Installer les outils clients](install-client-tools.md)
   2. [Créer le contrôleur de données Azure Arc](create-data-controller.md)
   3. [Créer un groupe de serveurs Azure Database pour PostgreSQL Hyperscale sur Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc](https://aka.ms/azurearc)
   - Services de données avec Azure Arc [ici](https://azure.microsoft.com/services/azure-arc/hybrid-data-services) et [ici](overview.md)
   - [Modes et exigences de connectivité](connectivity.md)



- **Lisez les concepts et les guides pratiques d’Azure Database pour PostgreSQL Hyperscale en vue de distribuer vos données sur plusieurs nœuds PostgreSQL Hyperscale et de bénéficier éventuellement d’une amélioration des performances** :
    * [Nœuds et tables](../../postgresql/concepts-hyperscale-nodes.md)
    * [Déterminer le type d’application](../../postgresql/concepts-hyperscale-app-type.md)
    * [Choisir une colonne de distribution](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocalisation de table](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuer et modifier des tables](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Concevoir une base de données multilocataire](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Concevoir un tableau de bord d’analytique en temps réel](../../postgresql/tutorial-design-database-hyperscale-realtime.md)* 

