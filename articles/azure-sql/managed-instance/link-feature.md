---
title: Fonctionnalité de liaison
titleSuffix: Azure SQL Managed Instance
description: Découvrez la fonctionnalité de liaison pour Azure SQL Managed Instance grâce à laquelle vous pouvez répliquer en continu des données de SQL Server vers le cloud ou migrer des bases de données SQL Server avec le temps d’arrêt le plus court possible.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, danil
ms.date: 11/02/2021
ms.openlocfilehash: fcae82f988ed019717f6693d0d3a63a025113fb4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096774"
---
# <a name="link-feature-for-azure-sql-managed-instance-limited-preview"></a>Fonctionnalité de liaison pour Azure SQL Managed Instance (préversion limitée)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

La nouvelle fonctionnalité de liaison d’Azure SQL Managed Instance connecte vos serveurs SQL, où qu’ils soient hébergés, à SQL Managed Instance, offrant un modèle hybride flexible et la mobilité des bases de données. Moyennant une approche qui utilise la réplication des données en quasi-temps réel dans le cloud, vous pouvez déplacer des charges de travail vers un réplica secondaire en lecture seule dans Azure pour tirer parti des fonctionnalités, des performances et de la scalabilité propres à Azure. 

Après un événement désastreux, vous pouvez continuer à exécuter vos charges de travail en lecture seule sur SQL Managed Instance dans Azure. Vous pouvez également choisir de migrer une ou plusieurs applications de SQL Server vers SQL Managed Instance en même temps, à votre rythme et avec le temps d’arrêt le plus court de toutes les solutions Azure actuellement disponibles.

> [!NOTE]
> Publiée en préversion publique limitée, la fonctionnalité de liaison n’est pour l’instant prise en charge qu’avec SQL Server 2019 Édition Entreprise CU13 (ou ultérieur). [Inscrivez-vous sans plus attendre](https://aka.ms/mi-link-signup) pour participer à la préversion publique limitée. 

## <a name="overview"></a>Vue d’ensemble

La technologie sous-jacente de la réplication des données en quasi-temps réel entre SQL Server et SQL Managed Instance repose sur les groupes de disponibilité distribués, qui font partie de la pile technologique bien connue et éprouvée des groupes de disponibilité Always On. Étendez votre groupe de disponibilité local SQL Server à SQL Managed Instance dans Azure de manière sûre et sécurisée. 

Il n’est pas nécessaire d’avoir un groupe de disponibilité existant ou plusieurs nœuds. La liaison prend en charge les instances SQL Server mononœuds sans groupes de disponibilité existants, ainsi que les instances SQL Server multinœuds avec des groupes de disponibilité existants. Grâce à la liaison, vous pouvez tirer parti des avantages modernes d’Azure sans migrer l’intégralité de vos données SQL Server vers le cloud.

Vous pouvez maintenir cette liaison aussi longtemps que vous le voulez, de plusieurs mois à plusieurs années à la fois. En ce qui concerne votre parcours de modernisation, si/quand vous êtes prêt à migrer vers Azure, la liaison propose une expérience de migration considérablement améliorée avec le temps d’arrêt le plus court de toutes les options actuellement disponibles, offrant une véritable migration en ligne vers SQL Managed Instance.

## <a name="supported-scenarios"></a>Scénarios pris en charge

Les données répliquées par le biais de la fonctionnalité de liaison de SQL Server vers Azure SQL Managed Instance peuvent être utilisées dans divers scénarios, notamment : 

- **Utiliser les services Azure sans migrer vers le cloud** 
- **Déplacer des charges de travail en lecture seule vers Azure** 
- **Migrer vers Azure**

![Scénario principal de liaison avec Managed Instance](./media/managed-instance-link/mi-link-main-scenario.png)


### <a name="use-azure-services"></a>Utiliser les services Azure 

Utilisez la fonctionnalité de liaison pour tirer parti des services Azure avec des données SQL Server sans migrer vers le cloud. Citons par exemple la création de rapports, l’analytique, les sauvegardes, le Machine Learning et d’autres tâches qui envoient des données à Azure. 

### <a name="offload-workloads-to-azure"></a>Déplacer des charges de travail vers Azure 

Vous pouvez également utiliser la fonctionnalité de liaison pour déplacer des charges de travail vers Azure. Par exemple, une application peut utiliser SQL Server pour des charges de travail en lecture/écriture tout en déplaçant des charges de travail en lecture seule vers SQL Managed Instance dans l’une des quelques 60 régions Azure dans le monde. Une fois la liaison établie, la base de données primaire sur SQL Server est accessible en lecture/écriture, tandis que les données répliquées sur SQL Managed Instance dans Azure sont accessibles en lecture seule. Cela permet de prendre en charge divers scénarios dans lesquels les bases de données répliquées sur SQL Managed Instance peuvent être utilisées pour un scale-out en lecture (échelle lecture) et le déplacement des charges de travail en lecture seule vers Azure. SQL Managed Instance, en parallèle, peut également héberger des bases de données en lecture/écriture indépendantes. Il est alors possible de copier la base de données répliquée vers une autre base de données en lecture/écriture sur la même instance managée pour d’autres opérations de traitement des données.

La liaison est délimitée à la base de données (une liaison par base de données), ce qui permet la consolidation et la déconsolidation des charges de travail dans Azure. Par exemple, vous pouvez répliquer les bases de données de plusieurs serveurs SQL sur une seule instance managée SQL dans Azure (consolidation) ou répliquer les bases de données d’un seul serveur SQL sur plusieurs instances managées dans une relation 1:1 (une base de données et une instance managée) dans l’une des régions Azure du monde (déconsolidation). Cette deuxième option vous offre un moyen efficace de rapprocher rapidement vos charges de travail de vos clients dans n’importe quelle région du monde et la possibilité de les utiliser comme réplicas en lecture seule.

### <a name="migrate-to-azure"></a>Migrer vers Azure 

La fonctionnalité de liaison facilite également la migration de SQL Server vers SQL Managed Instance, avec les avantages suivants : 

- Migration la plus performante avec un temps d’arrêt minimum par rapport aux autres solutions actuellement disponibles
- Véritable migration en ligne vers SQL Managed Instance dans n’importe quel niveau de service 

Compte tenu du temps d’arrêt minimum occasionné par la fonctionnalité de liaison, vous pouvez effectuer une migration vers une instance managée tout en conservant la charge de travail primaire en ligne. S’il était auparavant possible d’effectuer une migration en ligne vers le niveau de service Usage général avec d’autres solutions, la fonctionnalité de liaison permet à présent d’effectuer de véritables migrations en ligne vers le niveau de service Critique pour l’entreprise. 

## <a name="how-it-works"></a>Fonctionnement

La fonctionnalité de liaison pour SQL Managed Instance repose sur la technologie des groupes de disponibilité distribués. La solution prend en charge les systèmes mononœuds sans groupes de disponibilité existants et les systèmes multinœuds avec des groupes de disponibilité existants.  

![Principe de la fonctionnalité de liaison pour SQL Managed Instance](./media/managed-instance-link/mi-link-ag-dag.png)

Une connectivité sécurisée, par exemple VPN ou ExpressRoute, est utilisée entre un réseau local et Azure. Si SQL Server est hébergé sur une machine virtuelle Azure, le réseau principal (« backbone ») d’Azure peut être utilisé entre la machine virtuelle et l’instance managée, par exemple pour l’appairage global de réseaux virtuels. L’approbation entre les deux systèmes est établie en utilisant l’authentification basée sur les certificats, dans laquelle SQL Server et SQL Managed Instance échangent leurs clés publiques.

Vous pouvez avoir jusqu’à 100 liaisons entre une ou plusieurs sources SQL Server et une seule instance managée SQL. À l’heure actuelle, cette limite est régie par le nombre de bases de données pouvant être hébergées sur une instance managée. De même, un même serveur SQL peut établir plusieurs liaisons de réplication de base de données parallèles avec plusieurs instances managées dans différentes régions Azure dans une relation 1:1 (une base de données et une instance managée). La fonctionnalité nécessite l’installation de CU13 ou ultérieur sur SQL Server 2019.

## <a name="sign-up-for-link"></a>S’inscrire à la fonctionnalité de liaison

Pour utiliser la fonctionnalité de liaison, voici ce dont vous avez besoin :

- SQL Server 2019 Édition Entreprise avec [CU13 (ou ultérieur)](https://support.microsoft.com/topic/kb5005679-cumulative-update-13-for-sql-server-2019-5c1be850-460a-4be4-a569-fe11f0adc535) installé localement ou sur une machine virtuelle Azure.
- Une connectivité réseau entre le serveur SQL et l’instance managée est requise. Si votre serveur SQL s’exécute localement, utilisez une liaison VPN ou ExpressRoute. Si votre serveur SQL s’exécute sur une machine virtuelle Azure, vous pouvez soit déployer votre machine virtuelle sur le même sous-réseau que votre instance managée, soit utiliser l’appairage global de réseaux virtuels pour connecter deux sous-réseaux distincts. 
- Azure SQL Managed Instance provisionné sur n’importe quel niveau de service.

Utilisez le lien suivant pour vous inscrire à la préversion limitée de la fonctionnalité de liaison. Les clients sont intégrés au fur et à mesure, car le nombre de sièges actuellement disponibles est limité. Vous serez intégré à la première occasion. Les instances managées déployées à partir du 2/11/2021 sont prioritaires, et bien que les instances managées déployées avant cette date soient prises en charge, l’intégration peut prendre plus de temps. 

> [!div class="nextstepaction"]
> [S’inscrire à la préversion de la fonctionnalité de liaison](https://aka.ms/mi-link-signup)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la fonctionnalité de liaison, consultez :

- [Fonctionnalité de liaison pour SQL Managed Instance – refonte de la mobilité des données entre SQL Server et Azure](https://aka.ms/mi-link-techblog).

Pour d’autres scénarios de réplication, considérez : 

- [Réplication transactionnelle avec Azure SQL Managed Instance (préversion)](replication-transactional-overview.md)
