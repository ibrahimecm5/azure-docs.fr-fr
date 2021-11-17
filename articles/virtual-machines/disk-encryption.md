---
title: Chiffrement côté serveur de disques managés Azure
description: Le Stockage Azure protège vos données en les chiffrant au repos avant de les rendre persistantes dans des clusters de stockage. Vous pouvez utiliser des clés gérées par le client pour gérer le chiffrement avec vos propres clés, ou vous appuyer sur des clés gérées par Microsoft pour le chiffrement de vos disques managés.
author: roygara
ms.date: 09/03/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a7fcc0cf2783aa530f99836279d75aff61f85188
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488978"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Chiffrement côté serveur de stockage sur disque Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

La plupart des disques managés Azure sont chiffrés avec le chiffrement de Stockage Azure, qui utilise le chiffrement côté serveur (SSE) pour protéger vos données et vous aider à répondre aux engagements de votre organisation en matière de sécurité et de conformité. Le chiffrement Stockage Azure chiffre automatiquement vos données stockées sur des disques managés Azure (système d’exploitation et disques de données) au repos par défaut lors de leur conservation dans le cloud. Toutefois, les disques sur lesquels le chiffrement est activé sur l’hôte ne sont pas chiffrés par le biais du Stockage Azure. Pour les disques sur lesquels le chiffrement est activé sur l’hôte, le serveur qui héberge votre machine virtuelle fournit le chiffrement de vos données, et les données chiffrées sont transmises dans le Stockage Azure.

Les données dans les disque managés Azure sont chiffrées en toute transparence à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2. Pour plus d’informations sur les modules cryptographiques des disque managés Azure, consultez [API de chiffrement : nouvelle génération](/windows/desktop/seccng/cng-portal)

Le chiffrement Stockage Azure n’a pas d’impact sur les performances des disques managés et n’entraîne aucun coût supplémentaire. Pour plus d’informations sur le chiffrement du Stockage Azure, voir [Chiffrement du stockage Azure](../storage/common/storage-service-encryption.md).

> [!NOTE]
> Les disques temporaires ne sont pas des disques managés et ne sont pas chiffrés par SSE, sauf si vous activez le chiffrement à l’hôte.

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Vous pouvez vous appuyer sur les clés gérées par la plateforme pour le chiffrement de votre disque managé, ou vous pouvez gérer le chiffrement en utilisant vos propres clés. Si vous choisissez de gérer le chiffrement avec vos propres clés, vous pouvez spécifier une *clé gérée par le client* à utiliser pour le chiffrement et le déchiffrement de toutes les données dans les disques managés. 

Les sections suivantes décrivent en détail chacune des options de gestion des clés.

### <a name="platform-managed-keys"></a>Clés gérées par la plateforme

Par défaut, les disques managés utilisent des clés de chiffrement gérées par la plateforme. L’ensemble des nouveaux disques managés, des instantanés et des images ainsi que les données écrites sur des disques managés existants sont automatiquement chiffrés au repos avec des clés gérées par les plateformes.

### <a name="customer-managed-keys"></a>Clés gérées par le client

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Restrictions

Pour le moment, les clés gérées par le client sont soumises aux restrictions suivantes :

- Si cette fonctionnalité est activée pour votre disque, vous ne pouvez pas la désactiver.
    Pour contourner le problème, vous devez copier toutes les données en utilisant le [module Azure PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) ou l’[interface de ligne de commande Azure](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) sur un autre disque managé qui n’utilise pas de clés gérées par le client.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Régions prises en charge

Les clés gérées par le client sont disponibles dans toutes les régions où les disques managés le sont également.

> [!IMPORTANT]
> Les clés managées par le client s’appuient sur des identités managées pour ressources Azure, une fonctionnalité d’Azure Active Directory (Azure AD). Quand vous configurez des clés gérées par le client, une identité managée est automatiquement affectée à vos ressources à l’arrière plan. Si par la suite vous déplacez l’abonnement, le groupe de ressources ou le disque managé d’un répertoire Azure AD à un autre, l’identité managée associée aux disques gérés n’est pas transférée vers le nouveau locataire, de sorte que les clés gérées par le client peuvent ne plus fonctionner. Pour plus d’informations, consultez [Transfert d’un abonnement entre des répertoires Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

Pour activer les clés gérées par le client pour les disques managés, consultez nos articles traitant de la façon de les activer à l’aide du [module Azure PowerShell](windows/disks-enable-customer-managed-keys-powershell.md), d’[Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) ou du [portail Azure](disks-enable-customer-managed-keys-portal.md). 

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Chiffrement à l’hôte : chiffrement de bout en bout pour vos données de machine virtuelle

Lorsque vous activez le chiffrement sur l’hôte, il démarre sur l’hôte de machine virtuelle lui-même, le serveur Azure auquel votre machine virtuelle est allouée. Les données de votre disque temporaire et des caches du système d’exploitation/du disque de données sont stockées sur cet hôte de machine virtuelle. Après l’activation du chiffrement sur l’hôte, toutes ces données sont chiffrées au repos et les flux sont chiffrés dans le service de stockage, où elles sont conservées. En fait, le chiffrement sur l’hôte chiffre vos données de bout en bout. Le chiffrement sur l’hôte n’utilise pas le processeur de votre machine virtuelle et n’a pas d’impact sur ses performances. 

Les disques temporaires et disques de système d’exploitation éphémères sont chiffrés au repos avec des clés gérées par la plateforme lorsque vous activez le chiffrement de bout en bout. Les caches du système d’exploitation et du disque de données sont chiffrés au repos avec des clés gérées par le client ou par la plateforme, selon le type de chiffrement de disque sélectionné. Par exemple, si un disque est chiffré avec des clés gérées par le client, le cache du disque est chiffré avec les clés gérées par le client et, si un disque est chiffré à l’aide de clés gérées par la plateforme, le cache du disque est chiffré avec les clés gérées par la plateforme.

### <a name="restrictions"></a>Restrictions

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>Tailles des machines virtuelles prises en charge

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Vous pouvez également rechercher les tailles de machine virtuelle par programmation. Pour savoir comment les récupérer par programme, reportez-vous à la section Recherche des tailles de machine virtuelle prises en charge du [module Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes) ou des articles [Azure CLI](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes).

Pour activer le chiffrement de bout en bout à l’aide du chiffrement sur l’hôte, consultez nos articles traitant de la façon de l’activer à l’aide du [module Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md), d’[Azure CLI](linux/disks-enable-host-based-encryption-cli.md) et du [portail Azure](disks-enable-host-based-encryption-portal.md).

## <a name="double-encryption-at-rest"></a>Double chiffrement des données au repos

Les clients sensibles haute sécurité qui sont concernés par les risques associés à un algorithme de chiffrement particulier, une implémentation ou une clé compromise, peuvent désormais choisir une couche supplémentaire de chiffrement à l’aide d’un algorithme/mode de chiffrement différent au niveau de la couche d’infrastructure à l’aide de clés de chiffrement gérées par la plateforme. Cette nouvelle couche peut être appliquée systèmes d’exploitation et disques de données persistants, à des captures instantanées et à des images, qui seront toutes chiffrées au repos avec un double chiffrement.

### <a name="supported-regions"></a>Régions prises en charge

Le double chiffrement est disponible dans toutes les régions où les disques managés le sont également.

Pour activer le chiffrement double au repos pour les disques managés, consultez nos articles traitant de la façon de l’activer à l’aide du [module Azure PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md), d’[Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) ou du [portail Azure](disks-enable-double-encryption-at-rest-portal.md).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Chiffrement côté serveur et chiffrement de disque Azure

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) utilise la fonctionnalité [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux et la fonctionnalité [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) de Windows pour chiffrer les disques managés avec des clés gérées par le client au sein de la machine virtuelle invitée.  Le chiffrement côté serveur avec des clés gérées par le client améliore l’utilisation de Azure Disk Encryption en vous permettant d’utiliser des types et des images de système d’exploitation pour vos machines virtuelles en chiffrant les données dans le service de stockage.
> [!IMPORTANT]
> Les clés managées par le client s’appuient sur des identités managées pour ressources Azure, une fonctionnalité d’Azure Active Directory (Azure AD). Quand vous configurez des clés gérées par le client, une identité managée est automatiquement affectée à vos ressources à l’arrière plan. Si, par la suite, vous déplacez l’abonnement, le groupe de ressources ou le disque managé d’un répertoire Azure AD à un autre, l’identité managée associée aux disques gérés n’est pas transférée vers le nouveau locataire, de sorte que les clés gérées par le client peuvent ne plus fonctionner. Pour plus d’informations, consultez [Transfert d’un abonnement entre des répertoires Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Étapes suivantes

- Activer le chiffrement de bout en bout à l’aide du chiffrement sur l’hôte avec le [module Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md), l’[interface de ligne de commande Azure](linux/disks-enable-host-based-encryption-cli.md) et le [portail Azure](disks-enable-host-based-encryption-portal.md).
- Activez le double chiffrement au repos pour les disques managés avec le [module Azure PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md), l’[interface de ligne de commande Azure](linux/disks-enable-double-encryption-at-rest-cli.md) ou le [portail Azure](disks-enable-double-encryption-at-rest-portal.md).
- Activez les clés gérées par le client pour les disques managés avec le [module Azure PowerShell](windows/disks-enable-customer-managed-keys-powershell.md), l’[interface de ligne de commande Azure](linux/disks-enable-customer-managed-keys-cli.md) ou le [portail Azure](disks-enable-customer-managed-keys-portal.md).
- [Découvrez les modèles Azure Resource Manager permettant de créer des disques chiffrés avec des clés gérées par le client](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Qu’est-ce qu’Azure Key Vault ?](../key-vault/general/overview.md)
