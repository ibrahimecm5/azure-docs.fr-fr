---
title: Planifier les enclaves Intel SGX et l’attestation dans Azure SQL Database
description: Planifiez le déploiement d’Always Encrypted avec enclaves sécurisées dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: 18700a89b94a1fb739e279230ab6f2b172ab6671
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306507"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Planifier les enclaves Intel SGX et l’attestation dans Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/encryption/always-encrypted-enclaves) dans Azure SQL Database utilise des enclaves [Intel Software Guard Extensions (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) et requiert [Microsoft Azure Attestation](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation).

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Planifier Intel SGX dans Azure SQL Database

Intel SGX est une technologie d’environnement d’exécution de confiance basée sur le matériel. Intel SGX est disponible pour les bases de données qui utilisent le [modèle vCore](service-tiers-sql-database-vcore.md) et la génération matérielle de la [série DC](service-tiers-sql-database-vcore.md?#dc-series). Par conséquent, pour vous assurer de pouvoir utiliser Always Encrypted avec enclaves sécurisées dans votre base de données, vous devez soit sélectionner la génération matérielle de série DC lorsque vous créez la base de données, soit mettre à jour votre base de données actuelle pour utiliser la génération matérielle de la série DC.

> [!NOTE]
> Intel SGX n’est pas disponible dans les générations matérielles autres que la série DC. Par exemple, Intel SGX n’est pas disponible pour le matériel de 5e génération et n’est pas disponible pour les bases de données utilisant le [modèle DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Avant de configurer la génération matérielle de série DC pour votre base de données, vérifiez la disponibilité régionale de la série DC et assurez-vous que vous comprenez ses limites de performances. Pour plus d’informations, consultez [Série DC](service-tiers-sql-database-vcore.md#dc-series).

## <a name="plan-for-attestation-in-azure-sql-database"></a>Planifier l’attestation dans Azure SQL Database

[Microsoft Azure Attestation](../../attestation/overview.md) est une solution permettant d’attester les environnements d’exécution de confiance (TEE), y compris les enclaves Intel SGX dans les bases de données Azure SQL utilisant la génération de matériel de série DC.

Pour utiliser Azure Attestation pour l’attestation d’enclaves Intel SGX dans Azure SQL Database, vous devez créer un [fournisseur d’attestation](../../attestation/basic-concepts.md#attestation-provider) et le configurer avec la stratégie d’attestation fournie par Microsoft. Consultez [Configurer l’attestation pour Always Encrypted à l’aide d’Azure Attestation](always-encrypted-enclaves-configure-attestation.md).

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>Rôles et responsabilités lors de la configuration des enclaves SGX et de l’attestation

La configuration de votre environnement pour prendre en charge les enclaves Intel SGX et l’attestation pour Always Encrypted dans Azure SQL Database implique la configuration de composants de différents types : Microsoft Azure Attestation, Azure SQL Database et les applications qui déclenchent l’attestation d’enclave. La configuration des composants de chacun de ces types est effectuée par les utilisateurs qui disposent de l’un des rôles suivants :

- Administrateur d’attestation : crée un fournisseur d’attestation dans Microsoft Azure Attestation, crée la stratégie d’attestation, accorde l’accès au serveur logique Azure SQL au fournisseur d’attestation et partage l’URL d’attestation qui pointe vers la stratégie pour les administrateurs d’application.
- Administrateur Azure SQL Database : active les enclaves SGX dans les bases de données en sélectionnant la génération matérielle de série DC et fournit à l’administrateur d’attestation l’identité du serveur logique Azure SQL qui doit accéder au fournisseur d’attestation.
- Administrateur d’application : configure les applications avec l’URL d’attestation obtenue auprès de l’administrateur d’attestation.

Dans les environnements de production (qui gèrent des données sensibles réelles), il est important que votre organisation adhère à la séparation des rôles lors de la configuration de l’attestation, selon laquelle chaque rôle est assumé par une personne différente. En effet, si l’objectif du déploiement d’Always Encrypted au sein de votre organisation est de réduire la surface d’attaque en veillant à ce que les administrateurs Azure SQL Database ne puissent pas accéder à des données sensibles, les administrateurs Azure SQL Database ne doivent pas contrôler les stratégies d’attestation.

## <a name="next-steps"></a>Étapes suivantes

- [Activer Intel SGX pour votre base de données Azure SQL](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Voir aussi

- [Tutoriel : Bien démarrer avec Always Encrypted avec enclaves sécurisées dans Azure SQL Database](always-encrypted-enclaves-getting-started.md)
