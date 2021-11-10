---
title: Vue d’ensemble du registre Azure SQL Database
description: Découvrez les principes de base de la fonctionnalité de registre d’Azure SQL Database.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 07e999f70effc7141d66e01c8fea8686c6c133c7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056983"
---
# <a name="azure-sql-database-ledger"></a>Registre Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

L’instauration d’une confiance dans l’intégrité des données stockées dans des systèmes de base de données est depuis longtemps un problème pour toutes les organisations qui gèrent des données financières, médicales ou autres données sensibles. La fonctionnalité de registre d[Azure SQL Database](sql-database-paas-overview.md) offre des capacités de protection contre la falsification dans votre base de données. Vous pouvez attester de manière chiffrée à d’autres parties, telles que des auditeurs ou autres tiers professionnels, que vos données n’ont pas été falsifiées.

Le registre vous aide à protéger les données contre les attaquants ou les utilisateurs dotés de privilèges élevés, tels que des administrateurs de base de données, des administrateurs système et des administrateurs cloud. Comme avec un registre traditionnel, la fonctionnalité préserve les données historiques. Si une ligne est mise à jour dans la base de données, sa valeur précédente est conservée et protégée dans une table d’historique. Le registre tient une chronique de toutes les modifications apportées à la base de données au fil du temps. 

Le registre et les données historiques sont gérés de manière transparente, offrant ainsi une protection sans aucune modification de l’application. La fonctionnalité conserve les données historiques sous forme relationnelle pour permettre la prise en charge de requêtes SQL à des fins d’audit, de forensique et autres. Elle fournit des garanties d’intégrité des données chiffrées tout en maintenant la puissance, la flexibilité et les performances d’Azure SQL Database.

:::image type="content" source="media/ledger/ledger-table-architecture.png" alt-text="Diagramme de l’architecture des tables du registre.":::

## <a name="use-cases-for-azure-sql-database-ledger"></a>Cas d’usage du registre d’Azure SQL Database 

### <a name="streamlining-audits"></a>Simplification des audits

La valeur de tout système de production repose sur la capacité à faire confiance aux données que le système consomme et génère. Si un utilisateur malveillant a falsifié les données de votre base de données, cela peut avoir des conséquences désastreuses sur les processus métier reposant sur ces données. 

Le maintien de la confiance dans vos données requiert une combinaison de contrôles de sécurité appropriés pour réduire les attaques potentielles, de pratiques de sauvegarde et de restauration, ainsi que de procédures de récupération d’urgence rigoureuses. Les audits réalisés par des parties externes garantissent que ces pratiques sont mises en place. 

Les processus d’audit sont des activités extrêmement laborieuses. Un audit nécessite une inspection sur place des pratiques mises en œuvre, comme l’examen des journaux d’audit, l’inspection de l’authentification et l’inspection des contrôles d’accès. Bien que ces processus manuels permettent de révéler certaines lacunes en matière de sécurité, ils ne permettent pas d’apporter de preuve irréfutable que les données n’ont pas été indûment modifiées. 

Le registre fournit aux auditeurs la preuve chiffrée de l’intégrité des données. Cette preuve peut aider à rationaliser le processus d’audit. Elle fournit également la non-répudiation concernant l’intégrité des données du système.

### <a name="multiple-party-business-processes"></a>Processus métier multipartites

Dans certains systèmes, tels que les systèmes de gestion de la chaîne d’approvisionnement, plusieurs organisations doivent partager entre elles l’état d’un processus métier. Ces systèmes éprouvent des difficultés à partager et à faire confiance aux données. De nombreuses organisations se tournent vers des blockchains traditionnelles, comme Ethereum ou Hyperledger Fabric, pour transformer numériquement leurs processus métier multipartites. 

Une blockchain est une solution idéale pour les réseaux multipartites où la confiance entre les parties prenantes est faible. Bon nombre de ces réseaux sont fondamentalement des solutions centralisées où la confiance est importante, mais où une infrastructure entièrement décentralisée constitue une solution lourde. 

Le registre fournit une solution pour ces réseaux. Les participants peuvent vérifier l’intégrité des données hébergées de manière centralisée, sans la complexité et les implications en matière de performances associées à l’obtention d’un consensus dans un réseau basé sur une blockchain.

### <a name="trusted-off-chain-storage-for-blockchain"></a>Stockage off-chain de confiance pour blockchain

Quand un réseau basé sur une blockchain est nécessaire pour un processus métier multipartite, la possibilité d’interroger les données sur la blockchain sans sacrifier les performances est un véritable défi. 

Les modèles classiques pour résoudre ce problème impliquent une réplication des données de la blockchain dans un magasin hors chaîne (off-chain), tel qu’une base de données. Mais après la réplication des données de la blockchain vers la base de données, la garantie d’intégrité des données qu’offre une blockchain est perdue. Un registre assure l’intégrité des données pour le stockage off-chain de réseaux basés sur une blockchain, ce qui permet de garantir une confiance totale dans les données de l’ensemble du système.

## <a name="how-it-works"></a>Fonctionnement

Chaque transaction que la base de données reçoit est hachée par chiffrement (SHA-256). La fonction de hachage utilise en entrée la valeur de la transaction, ainsi que le code de hachage de la transaction précédente. (La valeur comprend les codes de hachage des lignes contenues dans la transaction.) La fonction lie toutes les transactions par chiffrement, comme une blockchain. 

Le hachage par chiffrement ([bases de données](#database-digests)) représente l’état de la base de données. Ils sont générés périodiquement et stockés en dehors d’Azure SQL Database dans un emplacement de stockage infalsifiable. Un exemple d’emplacement de stockage est la [fonctionnalité de stockage immuable de Stockage Blob Azure](../../storage/blobs/immutable-storage-overview.md) ou [Registre confidentiel Azure](../../confidential-ledger/index.yml). Les synthèses de base de données sont utilisées ultérieurement pour vérifier l’intégrité de la base de données en comparant la valeur du code de hachage dans la synthèse aux codes de hachage calculés dans la base de données. 

La fonctionnalité de registre est introduite dans les tables d’Azure SQL Database sous deux formes :

- [Tables de registre pouvant être mises à jour](#updatable-ledger-tables), qui vous permettent de mettre à jour et de supprimer des lignes dans vos tables.
- [Tables de registre pour ajout uniquement](#append-only-ledger-tables), qui n’autorisent que des insertions dans vos tables.

Tant les tables de registre pouvant être mises à jour que les tables de registre pour ajout uniquement fournissent des fonctionnalités de preuve de fraude et d’investigation numérique. Il est important de comprendre quelles transactions soumises par quels utilisateurs ont entraîné des modifications dans la base de données, au cas où il s’avérerait nécessaire de corriger d’éventuelles falsifications ou de prouver à des tiers que des utilisateurs autorisés ont soumis des transactions au système. 

La fonctionnalité de registre permet aux utilisateurs, à leurs partenaires ou aux auditeurs d’analyser toutes les opérations historiques, ainsi que de détecter des falsifications potentielles. Chaque opération de ligne est accompagnée de l’ID de la transaction qui l’a effectuée. L’ID permet aux utilisateurs d’obtenir plus d’informations sur l’heure à laquelle la transaction s’est produite et l’identité de l’utilisateur qui l’a exécutée. Les utilisateurs peuvent ensuite corréler l’ID à d’autres opérations effectuées par la transaction.

Pour plus d’informations sur les limitations des tables de registre, consultez [Limitations relatives au registre Azure SQL Database](ledger-limits.md).

### <a name="ledger-database"></a>Base de données de registre

Dans une base de données de registre, toutes les données utilisateur sont infalsifiables et stockées dans des tables de registre. Une base de données de registre peut uniquement contenir des tables de registre. Par défaut, chaque table est créée comme une table de registre pouvant être mise à jour. Les bases de données de registre fournissent une solution facile à utiliser pour les applications qui exigent que l’intégrité de toutes les données soit protégée. 

### <a name="updatable-ledger-tables"></a>Tables de registre pouvant être mises à jour

Des [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md) sont idéales pour des modèles d’application qui doivent opérer des mises à jour et des suppressions dans les tables de votre base de données, telles que des applications de système d’enregistrement. Les modèles de données existants pour votre application n’ont pas besoin d’être modifiés pour activer la fonctionnalité de registre.  

Des tables de registre pouvant être mises à jour suivent l’historique des modifications apportées à toutes les lignes de votre base de données lorsque des transactions entraînent des mises à jour ou des suppressions. Une table de registre pouvant être mise à jour est une table faisant l’objet d’un suivi des versions par le système, qui contient une référence à une autre table présentant un schéma en miroir. 

L’autre table est appelée *table d’historique*. Le système utilise cette table pour stocker automatiquement la version précédente d’une ligne chaque fois qu’une ligne de la table de registre est mise à jour ou supprimée. La table d’historique est créée automatiquement lorsque vous créez une table de registre pouvant être mise à jour. 

Les valeurs de la table de registre pouvant être mise à jour et sa table d’historique correspondante fournissent une chronique des valeurs de votre base de données au fil du temps. Un affichage du registre généré par le système est joint à la table de registre pouvant être mise à jour et à la table d’historique afin que vous puissiez facilement interroger cette chronique de votre base de données.

Pour plus d’informations sur les tables de registre pouvant être mises à jour, consultez [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md).

### <a name="append-only-ledger-tables"></a>Tables de registre pour ajout uniquement

Des [tables de registre pour ajout uniquement](ledger-append-only-ledger-tables.md) sont idéales pour des modèles d’application qui n’opèrent que par insertions, telles que des applications d’informations de sécurité et de gestion d’événements (SIEM). Les tables de registre pour ajout uniquement bloquent les mises à jour et les suppressions au niveau de l’API. Ce blocage fournit une protection supplémentaire contre la falsification par des utilisateurs disposant de privilèges, tels que les administrateurs système et les administrateurs de base de donnes. 

Comme seules les insertions sont autorisées dans le système, les tables de registre pour ajout uniquement n’ont pas de table d’historique correspondante, car il n’y a pas d’historique à capturer. À l’instar des tables de registre pouvant être mises à jour, un affichage du registre fournit des insights sur les transactions ayant inséré des lignes dans la table pour ajout uniquement et sur les utilisateurs à l’origine des insertions.

Pour plus d’informations sur les tables de registre pour ajout uniquement, consultez [Créer et utiliser des tables de registre pour ajout uniquement](ledger-how-to-append-only-ledger-tables.md).

### <a name="database-ledger"></a>Registre de base de données

Le [registre de base de données](ledger-database-ledger.md) est constitué de tables système qui stockent les codes de hachage chiffrés des transactions traitées dans le système. Les transactions étant l’unité d’[atomicité](/windows/win32/cossdk/acid-properties) du moteur de base de données, il s’agit de l’unité de travail capturée par le registre de base de données. 

Plus précisément, lorsqu’une transaction est validée, le code de hachage SHA-256 de toutes les lignes modifiées par la transaction dans la table de registre est ajouté comme *entrée de transaction* dans le registre base de données. L’entrée de transaction comprend également certaines métadonnées relatives à la transaction, telles que l’identité de l’utilisateur qui l’a exécutée et le timestamp de sa validation. 

Toutes les 30 secondes, les transactions traitées par la base de données font l’objet d’un hachage SHA-256 à l’aide d’une structure de données arborescente Merkle. Le résultat est un code de hachage racine qui forme un bloc. Le bloc est ensuite soumis à un hachage SHA-256 utilisant le code de hachage racine du bloc, ainsi que le code de hachage racine du bloc précédent comme entrée de la fonction de hachage. Ce hachage forme une blockchain.

### <a name="database-digests"></a>Synthèses de base de données

Le code de hachage du dernier bloc dans le registre de base de données est appelé [synthèse de base de données](ledger-digest-management-and-database-verification.md). Il représente l’état de toutes les tables de registre dans la base de données au moment où le bloc a été généré. 

Quand un bloc est formé, la synthèse de base de données qui lui est associée est publiée et stockée en dehors d’Azure SQL Database dans un stockage infalsifiable. Comme les synthèses de base de données représentent l’état de la base de données au moment où elles ont été générées, leur protection contre la falsification est primordiale. Un attaquant qui a accès à la modification des synthèses peut :

1. Falsifier les données de la base de données.
2. Générer les codes de hachage qui représentent la base de données avec ces modifications.
3. Modifier les synthèses pour représenter le code de hachage mis à jour des transactions dans le bloc. 

Le registre offre la possibilité de générer et stocker automatiquement les synthèses de base de données dans un [stockage immuable](../../storage/blobs/immutable-storage-overview.md) ou dans [Registre confidentiel Azure](../../confidential-ledger/index.yml), afin d’empêcher toute falsification. Les utilisateurs peuvent également générer manuellement des synthèses de base de données et les stocker à l’emplacement de leur choix. Les synthèses de base de données sont utilisées ultérieurement pour vérifier que les données stockées dans les tables de registre n’ont pas été falsifiées.

### <a name="ledger-verification"></a>Vérification du registre

La fonctionnalité de registre ne permet pas aux utilisateurs de modifier son contenu. Toutefois, un attaquant ou un administrateur système contrôlant l’ordinateur peut contourner tous les contrôles du système et falsifier directement les données. Par exemple, un attaquant ou un administrateur système peut modifier les fichiers de base de données dans le stockage. Un registre ne peut pas empêcher de telles attaques, mais garantit que toute falsification sera détectée lors de la vérification des données qu’il contient. 

Le processus de [vérification du registre](ledger-digest-management-and-database-verification.md) prend en entrée une ou plusieurs synthèses de base de données précédemment générées et recalcule les codes de hachage stockés dans le registre de base de données en fonction de l’état actuel des tables de registre. Si les codes de hachage calculés ne correspondent pas aux synthèses d’entrée, la vérification échoue, indiquant que les données ont été falsifiées. Le registre signale alors toutes les incohérences détectées.

Étant donné que la vérification du registre recalcule tous les codes de hachage des transactions dans la base de données, il peut s’agir d’un processus gourmand en ressources pour des bases de données volumineuses. Les utilisateurs doivent exécuter la vérification du registre uniquement lorsqu’ils doivent vérifier l’intégrité de leur base de données plutôt que de l’exécuter en permanence. 

Dans l’idéal, les utilisateurs ne devraient exécuter une vérification du registre que lorsque l’organisation qui héberge les données fait l’objet d’un audit et doit fournir à une autre partie des preuves par chiffrement de l’intégrité des données. Pour réduire le coût de la vérification, la fonctionnalité offre des options permettant de vérifier des tables de registre individuellement ou seulement un sous-ensemble des tables du registre.

## <a name="next-steps"></a>Étapes suivantes
 
- [Démarrage rapide : Créer une base de données SQL avec un registre activé](ledger-create-a-single-database-with-ledger-enabled.md)
- [Accéder aux synthèses stockées dans Registre confidentiel Azure](ledger-how-to-access-acl-digest.md)
- [Vérifier une table de registre pour détecter une falsification](ledger-verify-database.md)
