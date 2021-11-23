---
title: Considérations sur la sécurité | Microsoft Docs
description: Cette rubrique fournit des instructions générales pour la sécurisation de SQL Server en cours d’exécution sur une machine virtuelle Azure.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2021
ms.author: pamela
ms.reviewer: mathoma
ms.openlocfilehash: 327c2fa71fc8c95da654e7fca9450a8d0372e1ab
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301780"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cette rubrique comprend des instructions de sécurité générales pour créer un accès sécurisé aux instances SQL Server dans une machine virtuelle Azure.

Azure est conforme à plusieurs règlements et normes de l’industrie et vous permet de créer une solution conforme avec SQL Server exécuté sur une machine virtuelle. Pour plus d’informations sur les exigences réglementaires relatives à Azure, voir [Centre de confidentialité Azure](https://azure.microsoft.com/support/trust-center/).

Outre les pratiques décrites dans cette rubrique, nous vous recommandons de consulter et d’implémenter les bonnes pratiques en matière de sécurité, à la fois les pratiques de sécurité locales traditionnelles et les bonnes pratiques en matière de sécurité des machines virtuelles. 

## <a name="microsoft-defender-for-sql"></a>Microsoft Defender pour SQL 

[Azure Defender pour SQL](../../../security-center/defender-for-sql-introduction.md) permet d’utiliser les fonctionnalités de sécurité de Microsoft Defender pour le cloud, telles que les évaluations de vulnérabilité et les alertes de sécurité. Pour en savoir plus, consultez [Activer Microsoft Defender pour SQL](../../../security-center/defender-for-sql-usage.md). 

## <a name="portal-management"></a>Gestion du portail

Après avoir [inscrit votre machine virtuelle SQL Server avec l’extension IaaS SQL](sql-agent-extension-manually-register-single-vm.md), vous pouvez configurer un certain nombre de paramètres de sécurité à l’aide de la [ressource de machines virtuelles SQL](manage-sql-vm-portal.md) dans le portail Azure, comme l’activation de l’intégration d’Azure Key Vault ou l’authentification SQL. 

En outre, après avoir activé [Microsoft Defender for SQL](../../../security-center/defender-for-sql-usage.md), vous pouvez afficher les fonctionnalités de Defender pour le cloud directement dans la [ressource de machines virtuelles SQL](manage-sql-vm-portal.md) dans le portail Azure, telles que les évaluations de vulnérabilité et les alertes de sécurité. 

Pour en savoir plus, consultez [Gérer la machine virtuelle SQL Server dans le portail](manage-sql-vm-portal.md). 

## <a name="azure-key-vault-integration"></a>Intégration du coffre de clés Azure 

Il existe plusieurs fonctionnalités de chiffrement SQL Server, telles que le chiffrement transparent des données (TDE), le chiffrement au niveau des colonnes (CLE) et le chiffrement de sauvegarde. Ces types de chiffrement nécessitent que vous gériez et stockiez les clés de chiffrement que vous utilisez pour le chiffrement. Le service Azure Key Vault est conçu pour optimiser la sécurité et la gestion de ces clés dans un emplacement sécurisé et hautement disponible. Le connecteur SQL Server permet à SQL Server d’utiliser ces clés depuis Azure Key Vault.
Pour plus de détails, consultez les autres articles de cette série : [Liste de contrôle](performance-guidelines-best-practices-checklist.md), [Taille de machine virtuelle](performance-guidelines-best-practices-vm-size.md), [Stockage](performance-guidelines-best-practices-storage.md), [Configuration HADR](hadr-cluster-best-practices.md), [Collecter la ligne de base](performance-guidelines-best-practices-collect-baseline.md). 

Pour en savoir plus, consultez [Intégration d’Azure Key Vault](azure-key-vault-integration-configure.md).


## <a name="access-control"></a>Contrôle d’accès 

Lorsque vous créez votre machine virtuelle SQL Server, déterminez comment contrôler attentivement les personnes qui ont accès à la machine et à SQL Server. En règle générale, vous devez systématiquement :

- Restreindre l’accès à SQL Server aux applications et aux clients qui en ont besoin.
- Suivre les meilleures pratiques de gestion des comptes d’utilisateur et des mots de passe.

Les sections suivantes fournissent des suggestions sur ces différents points.

## <a name="secure-connections"></a>Sécuriser les connexions

Lorsque vous créez une machine virtuelle SQL Server avec une image de galerie, l’option **Connectivité SQL Server** vous donne la possibilité de choisir **Local (sur la machine virtuelle uniquement)** , **Privé (dans un réseau virtuel)** ou **Public (Internet)** .

![Connectivité SQL Server](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

Pour une sécurité optimale, choisissez l’option la plus restrictive dans votre scénario. Par exemple, si vous exécutez une application qui accède à SQL Server sur la même machine virtuelle, **Local** constitue l’option la plus sûre. Si vous exécutez une application Azure qui requiert un accès à SQL Server, l’option **Privé** sécurise les communications vers SQL Server uniquement dans le [réseau virtuel Azure](../../../virtual-network/virtual-networks-overview.md) spécifié. Si vous avez besoin d’un accès **Public** (Internet) à la machine virtuelle SQL Server, assurez-vous de bien suivre les autres meilleures pratiques présentées dans cette rubrique afin de réduire votre surface d’exposition aux attaques.

Les options sélectionnées dans le portail utilisent les règles de sécurité du trafic entrant sur les machines virtuelles [Groupe de sécurité réseau](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) (NSG) pour autoriser ou refuser le trafic réseau vers votre machine virtuelle. Vous pouvez modifier ou créer de nouvelles règles de groupe de sécurité réseau entrantes pour autoriser le trafic vers le port SQL Server (1433, par défaut). Vous pouvez également spécifier des adresses IP spécifiques qui sont autorisées à communiquer sur ce port.

![Règles de groupe de sécurité réseau](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

Outre les règles de groupe de sécurité réseau pour limiter le trafic réseau, vous pouvez utiliser le Pare-feu Windows sur la machine virtuelle.

Lorsque vous utilisez des points de terminaison avec le modèle de déploiement classique, supprimez-les sur la machine virtuelle si vous n’en avez pas besoin. Pour obtenir des instructions sur l’utilisation de listes ACL avec des points de terminaison, voir [Gestion de l’ACL sur un point de terminaison](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint) Cela n’est pas nécessaire pour les machines virtuelles qui utilisent Azure Resource Manager.

Enfin, pensez à activer l’option de connexion chiffrée pour l’instance du moteur de base de données SQL Server dans votre machine virtuelle Azure. Configurez l’instance SQL Server avec un certificat signé. Pour plus d’informations, voir [Activation des connexions chiffrées dans le moteur de base de données](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) et [Syntaxe de la chaîne de connexion](/dotnet/framework/data/adonet/connection-string-syntax).

## <a name="encryption"></a>Chiffrement

Les disques managés offrent un chiffrement côté serveur et Azure Disk Encryption. Le [chiffrement côté serveur](../../../virtual-machines/disk-encryption.md) assure le chiffrement au repos et la protection de vos données pour assurer le respect des engagements de votre organisation en matière de sécurité et de conformité. [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) utilise la technologie BitLocker ou DM-Crypt et s’intègre à Azure Key Vault pour chiffrer les disques de données et de système d’exploitation. 

## <a name="non-default-port"></a>Port non défini comme port par défaut

Par défaut, SQL Server écoute un port bien connu, le port 1433. Pour une sécurité renforcée, configurez SQL Server de sorte que l’écoute se fasse sur un port non défini par défaut, comme le port 1401. Si vous approvisionnez une image de galerie SQL Server dans le portail Azure, vous pouvez spécifier ce port dans le panneau **Paramètres SQL Server**.

Pour configurer ce paramètre après l’approvisionnement, vous avez deux options :

- Pour les machines virtuelles Resource Manager, vous pouvez sélectionner **Sécurité** à partir de la [ressource de machines virtuelles SQL](manage-sql-vm-portal.md#access-the-resource). Cela vous permet de modifier le port.

  ![Modification du port TCP dans le portail](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- Vous pouvez configurer manuellement le port pour les machines virtuelles Classic ou pour les machines virtuelles SQL Server qui n’étaient pas approvisionnées avec le portail, en vous connectant à distance à la machine virtuelle. Pour obtenir les étapes de configuration, consultez [Configurer un serveur pour écouter un port TCP spécifique](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Si vous utilisez cette technique manuelle, vous devez également ajouter une règle de Pare-feu Windows pour autoriser le trafic entrant sur ce port TCP.

> [!IMPORTANT]
> Il est judicieux de spécifier un port qui n’est pas défini par défaut si votre port SQL Server est ouvert aux connexions Internet publiques.

Lorsque SQL Server écoute un port non défini par défaut, vous devez spécifier le port au moment où vous vous connectez. Par exemple, imaginons un scénario dans lequel l’adresse IP du serveur est 13.55.255.255 et SQL Server écoute le port 1401. Pour vous connecter à SQL Server, vous devez spécifier `13.55.255.255,1401` dans la chaîne de connexion.

## <a name="manage-accounts"></a>Gérer les comptes

Il n’est pas souhaitable que les intrus puissent deviner facilement les noms de compte ou les mots de passe. Suivez les conseils ci-après :

- Créez un seul compte d’administrateur local avec un nom différent de **Administrator**.

- Utilisez des mots de passe forts complexes pour tous vos comptes. Pour plus d’informations sur la création d’un mot de passe sécurisé, consultez l’article [Créer un mot de passe sécurisé](https://support.microsoft.com/account-billing/how-to-create-a-strong-password-for-your-microsoft-account-f67e4ddd-0dbe-cd75-cebe-0cfda3cf7386).

- Par défaut, Azure sélectionne l’authentification Windows pendant l’installation de la machine virtuelle SQL Server. Par conséquent, la connexion **SA** est désactivée et un mot de passe est affecté par l’installation. Nous recommandons que la connexion **SA** ne soit ni utilisée ni activée. Si vous devez avoir une connexion SQL, utilisez l’une des méthodes suivantes :

  - Créez un compte SQL avec un nom unique et qui est membre de **sysadmin**. Vous pouvez le faire dans le portail en activant l’**authentification SQL** lors de l’approvisionnement.

    > [!TIP] 
    > Si vous n’activez pas l’authentification SQL lors de l’approvisionnement, vous devez modifier manuellement le mode d’authentification pour définir le mode **Mode d’authentification SQL Server et Windows**. Pour plus d’informations, consultez [Modifier le mode d’authentification du serveur](/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Si vous devez utiliser la connexion **SA**, activez la connexion après l’approvisionnement et assignez un nouveau mot de passe sécurisé.



## <a name="next-steps"></a>Étapes suivantes

Si vous vous intéressez également à l’optimisation des performances, consultez [Bonnes pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](./performance-guidelines-best-practices-checklist.md).

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](frequently-asked-questions-faq.yml).


Pour en savoir plus, consultez les autres articles de cette série :

- [Liste de vérification rapide](performance-guidelines-best-practices-checklist.md)
- [Taille de la machine virtuelle](performance-guidelines-best-practices-vm-size.md)
- [Stockage](performance-guidelines-best-practices-storage.md)
- [Sécurité](security-considerations-best-practices.md)
- [Paramètres HADR](hadr-cluster-best-practices.md)
- [Collecter une ligne de base](performance-guidelines-best-practices-collect-baseline.md)
