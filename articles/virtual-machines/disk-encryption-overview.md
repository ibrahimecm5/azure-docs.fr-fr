---
title: Vue d’ensemble des options de chiffrement des disques managés
description: Vue d’ensemble des options de chiffrement des disques managés
author: msmbaldwin
ms.date: 06/05/2021
ms.topic: conceptual
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 989a7fcc058284c1c706cbfc30c97a77200de111
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315309"
---
# <a name="overview-of-managed-disk-encryption-options"></a>Vue d’ensemble des options de chiffrement des disques managés

Il existe plusieurs types de chiffrement disponibles pour vos disques managés, y compris Azure Disk Encryption (ADE), le chiffrement côté serveur (SSE) et le chiffrement sur l’hôte.

- **Azure Disk Encryption** vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. ADE fournit un chiffrement de volume pour les disques de système d’exploitation et de données des machines virtuelles Azure en utilisant la fonctionnalité [DM-Crypt](https://wikipedia.org/wiki/Dm-crypt) de Linux ou la fonctionnalité [BitLocker](https://wikipedia.org/wiki/BitLocker) de Windows. ADE est intégré à Azure Key Vault pour faciliter le contrôle et la gestion des clés et des secrets de chiffrement des disques.  Pour obtenir tous les détails, consultez [Azure Disk Encryption pour les machines virtuelles Linux](./linux/disk-encryption-overview.md) ou [Azure Disk Encryption pour les machines virtuelles Windows](./windows/disk-encryption-overview.md).

- Le **chiffrement côté serveur** (également appelé chiffrement au repos ou chiffrement de stockage Azure) chiffre automatiquement les données stockées sur les disques managés Azure (disques de système d’exploitation et de données) pour assurer leur persistance dans le cloud.  Pour obtenir tous les détails, consultez [Chiffrement côté serveur de stockage sur disque Azure](./disk-encryption.md).

- Le **chiffrement sur l’hôte** garantit que les données stockées sur l’hôte de machine virtuelle seront chiffrées au repos et chemineront chiffrées jusqu’au service de stockage. Les disques pour lesquels le chiffrement sur l’hôte est activé ne sont pas chiffrés avec SSE. Au lieu de cela, le serveur hébergeant votre machine virtuelle assure le chiffrement de vos données et garantit que les données chiffrées seront transmises au stockage Azure. Pour obtenir tous les détails, consultez [Chiffrement sur l’hôte : chiffrement de bout en bout pour vos données de machine virtuelle](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="comparison"></a>Comparaison

Voici une comparaison entre les chiffrements SSE, ADE et sur l’hôte.

| | Chiffrement au repos (disques de système d’exploitation et de données) | Chiffrement de disque temporaire | Chiffrement des caches | Données transmises chiffrées entre le calcul et le stockage | Contrôle des clés par le client | État de chiffrement de disque de Microsoft Defender pour le cloud |
|--|--|--|--|--|--|--|
| **Chiffrement au repos avec clé gérée par la plateforme (SSE+PMK)** | &#x2705; | &#10060; | &#10060; | &#10060; | &#10060; | Non sain, non applicable en cas d’exemption |
| **Chiffrement au repos avec clé gérée par le client (SSE+CMK)** | &#x2705; | &#10060; | &#10060; | &#10060; | &#x2705; | Non sain, non applicable en cas d’exemption |
| **Azure Disk Encryption** | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | Healthy |
| **Chiffrement sur l’hôte**  | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | Non sain, non applicable en cas d’exemption |

> [!Important]
> Pour le chiffrement sur l’hôte, Microsoft Defender pour le cloud ne détecte pas l’état de chiffrement.

## <a name="next-steps"></a>Étapes suivantes

- [Azure Disk Encryption pour les machines virtuelles Linux](./linux/disk-encryption-overview.md)
- [Azure Disk Encryption pour les machines virtuelles Windows](./windows/disk-encryption-overview.md)
- [Chiffrement côté serveur de stockage sur disque Azure](./disk-encryption.md)
- [Chiffrement sur l’hôte](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Notions de base de la sécurité Azure – Présentation du chiffrement Azure](../security/fundamentals/encryption-overview.md)
