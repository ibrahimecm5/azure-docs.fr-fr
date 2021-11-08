---
title: Microsoft defender pour SQL – Avantages et les fonctionnalités
description: Découvrez les avantages et les fonctionnalités de Microsoft Defender pour SQL.
author: memildin
ms.author: memildin
ms.date: 05/27/2021
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: e98c135d93e3dad247571af6b9c8ad35302252ff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445233"
---
# <a name="introduction-to-microsoft-defender-for-sql"></a>Présentation de Microsoft Defender pour SQL

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender pour le cloud inclut deux plans Microsoft Defender qui étendent le [package de sécurité des données](../azure-sql/database/azure-defender-for-sql.md) de Microsoft Defender pour le cloud afin de sécuriser vos bases de données et leurs données en tout lieu. Microsoft Defender pour SQL inclut des fonctionnalités permettant de découvrir et d’atténuer les vulnérabilités potentielles de vos bases de données, et de détecter les activités anormales susceptibles d’indiquer une menace ciblant vos bases de données.

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|**Microsoft Defender pour les serveurs de base de données Azure SQL** – Disponibilité générale<br>**Microsoft Defender pour les serveurs SQL sur machines** – Disponibilité générale |
|Prix :|Les deux plans qui constituent **Microsoft Defender pour SQL** sont facturées conformément aux indications de la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).|
|Versions de SQL protégées :|[SQL sur machines virtuelles Azure](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Serveurs SQL Server avec Azure Arc](/sql/sql-server/azure-arc/overview)<br>Serveurs SQL locaux sur machines Windows sans Azure Arc<br>[Bases de données uniques](../azure-sql/database/single-database-overview.md) et [pools élastiques](../azure-sql/database/elastic-pool-overview.md) Azure SQL<br>[Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Pool SQL dédié Azure Synapse Analytics (anciennement SQL DW)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png":::Azure Government<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet (**partiellement** : sous-ensemble d’alertes et évaluation des vulnérabilités pour les serveurs SQL. Les protections contre les menaces comportementales ne sont pas disponibles.)|
|||

## <a name="what-does-microsoft-defender-for-sql-protect"></a>Qu’est-ce que Microsoft Defender pour SQL protège ?

**Microsoft Defender pour SQL** comprend deux plans Microsoft Defender distincts :

- **Microsoft Defender pour les serveurs de base de données Azure SQL** protège les ressources suivantes :
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Pool SQL dédié dans Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Microsoft Defender pour les serveurs SQL Server sur les machines** étend les protections de vos serveurs SQL Azure natifs pour prendre en charge les environnements hybrides et protéger les serveurs SQL (toutes les versions prises en charge) hébergés dans Azure, d’autres environnements cloud et même les machines locales :
    - [SQL Server sur les machines virtuelles](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - Serveurs SQL locaux :
        - [SQL Server avec Azure Arc (préversion)](/sql/sql-server/azure-arc/overview)
        - [SQL Server exécuté sur des machines Windows sans Azure Arc](../azure-monitor/agents/agent-windows.md)


## <a name="what-are-the-benefits-of-microsoft-defender-for-sql"></a>Quels sont les avantages de Microsoft Defender pour SQL ?

Ces deux plans incluent des fonctionnalités permettant d’identifier et d’atténuer les vulnérabilités potentielles de vos bases de données, et de détecter les activités anormales susceptibles d’indiquer des menaces ciblant vos bases de données.

Un service d’évaluation des vulnérabilités découvre les vulnérabilités potentielles de vos bases de données, en assure le suivi et vous aide à les corriger. Les analyses d’évaluation fournissent une vue d’ensemble de l’état de sécurité de vos machines SQL, ainsi que des détails sur les résultats de la sécurité.

- En savoir plus sur [l’évaluation des vulnérabilités pour Azure SQL Database](../azure-sql/database/sql-vulnerability-assessment.md).
- En savoir plus sur [l’évaluation des vulnérabilités pour les serveurs Azure SQL sur les ordinateurs](defender-for-sql-on-machines-vulnerability-assessment.md).

Un service de protection avancée contre les menaces effectue un monitoring continu de vos serveurs SQL pour repérer les menaces telles que l’injection de code SQL, les attaques par force brute et les abus de privilèges. Ce service fournit des alertes de sécurité orientées sur l’action dans Microsoft Defender pour le cloud avec des détails sur l’activité suspecte, des conseils sur la façon de limiter les menaces et des options pour poursuivre vos investigations avec Microsoft Sentinel. En savoir plus sur la [protection avancée contre les menaces](../azure-sql/database/threat-detection-overview.md).

 > [!TIP]
 > Affichez la liste des alertes de sécurité pour les serveurs SQL [dans la page de référence des alertes](alerts-reference.md#alerts-sql-db-and-warehouse).


## <a name="is-there-a-performance-impact-from-deploying-microsoft-defender-for-sql-on-machines"></a>Le déploiement de Microsoft Defender pour SQL sur des ordinateurs a-t-il un impact sur le niveau de performance ?

L’objectif de **Microsoft Defender pour SQL sur les ordinateurs** est évidemment la sécurité. Cependant, nous nous soucions également de votre entreprise. Nous avons donc donné la priorité au niveau de performance pour garantir un impact minimal sur vos serveurs SQL. 

Le service possède une architecture fractionnée pour trouver un juste équilibre entre le chargement des données et la vitesse d’un côté, et le niveau de performance de l’autre : 

- Certains de nos détecteurs s’exécutent sur l’ordinateur pour offrir des avantages en termes de vitesse en temps réel.
- D’autres s’exécutent dans le cloud pour éviter à l’ordinateur des charges de calcul lourdes.

Les tests en laboratoire de notre solution ont montré une utilisation moyenne du processeur de 3 % pour les tranches de pointe par rapport aux charges de référence. Une analyse de la télémétrie pour nos utilisateurs actuels indique un impact négligeable sur l’utilisation du processeur et de la mémoire.

Bien entendu, le niveau de performance varie toujours selon les environnements, les ordinateurs et les charges. Les déclarations et les chiffres ci-dessus sont fournis à titre d’indications générales, et non de garantie pour un déploiement en particulier.


## <a name="what-kind-of-alerts-does-microsoft-defender-for-sql-provide"></a>Quels sont les types d’alertes fournis par Microsoft Defender pour SQL ?

Les alertes de sécurité enrichies par le renseignement sur les menaces sont déclenchées dans les cas suivants :

- **Attaques potentielles par injection de code SQL**, y compris les vulnérabilités détectées lorsque les applications génèrent une instruction SQL défectueuse dans la base de données
- **Modèles de requête et d’accès à la base de données anormaux**, par exemple, un nombre anormalement élevé d’échecs de tentatives de connexion avec des informations d’identification différentes (tentative d’attaque par force brute)
- **Activité suspecte de la base de données**, par exemple lorsqu’un utilisateur légitime accède à un ordinateur SQL Server à partir d’un ordinateur compromis qui a communiqué avec un serveur C&C à exploration crypto.

Les alertes fournissent des détails sur l’incident qui les a déclenchées, ainsi que des suggestions pour enquêter et contrer les menaces.



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Microsoft Defender pour SQL. Pour utiliser les services décrits :

- Utilisez Microsoft Defender pour les serveurs SQL Server sur des machines afin d’[analyser vos serveurs SQL Server à la recherche de vulnérabilités](defender-for-sql-usage.md).
- Pour une présentation de Microsoft Defender pour SQL, consultez [Comment Microsoft Defender pour SQL peut protéger les serveurs SQL en tout lieu](https://www.youtube.com/watch?v=V7RdB6RSVpc).
