---
title: Automanage pour Windows Server
description: Vue d’ensemble des fonctionnalités d’Azure Automanage pour Windows Server avec Windows Server édition Azure
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: efd5b643608b8d3de5cacbc1f810a9b1e9efb612
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501587"
---
# <a name="azure-automanage-for-windows-server"></a>Azure Automanage pour Windows Server

Azure Automanage pour Windows Server offre de nouvelles fonctionnalités propres à _Windows Server édition Azure_.  Ces fonctionnalités sont les suivantes :
- Patch à chaud (préversion)
- SMB sur QUIC
- Réseau étendu pour Azure

> [!IMPORTANT]
> Le patch à chaud est actuellement en préversion publique. Une procédure de consentement est requise pour utiliser la fonctionnalité de Patch à chaud (Hotpatch) décrite ci-dessous.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous trouverez les fonctionnalités Windows Server dans une ou plusieurs de ces images de _Windows Server Azure Edition_ : 

- Centre de données Windows Server 2022 : Azure Edition (expérience Bureau)
- Centre de données Windows Server 2022 : Azure Edition (Core)

Les fonctionnalités varient selon les images, consultez [démarrage](#getting-started-with-windows-server-azure-edition) pour plus d’informations.

## <a name="automanage-for-windows-server-capabilities"></a>Automanage pour les fonctionnalités Windows Server

### <a name="hotpatch-preview"></a>Patch à chaud (préversion)

Hotpatch est disponible en préversion publique sur les images suivantes :

- Centre de données Windows Server 2022 : Azure Edition (Core)

Hotpatch vous donne la possibilité d’appliquer des mises à jour de sécurité sur votre machine virtuelle sans redémarrage.  En outre, Automanage pour Windows Server automatise l’intégration, la configuration et l’orchestration de la mise à jour corrective à chaud.  Pour en savoir plus, consultez [Patch à chaud](automanage-hotpatch.md).  

### <a name="smb-over-quic"></a>SMB sur QUIC

SMB sur QUIC est disponible en préversion publique sur les images suivantes :

- Centre de données Windows Server 2022 : Azure Edition (expérience Bureau)
- Centre de données Windows Server 2022 : Azure Edition (Core)

SMB sur QUIC offre un « VPN SMB » pour les télétravailleurs, les utilisateurs d’appareil mobile et les filiales qui offre une connectivité sécurisée et fiable aux serveurs de fichiers périphériques sur des réseaux non approuvés comme Internet. [QUIC](https://datatracker.ietf.org/doc/rfc9000/) est un protocole standardisé IEFT utilisé dans HTTP/3, conçu pour une protection maximale des données avec TLS 1.3 et nécessitant un chiffrement non désactivable. SMB se comporte normalement dans le tunnel QUIC, ce qui signifie que l’expérience utilisateur ne change pas. Les fonctionnalités SMB, comme Multichannel, la signature, la compression, la disponibilité continue et le leasing de répertoire, fonctionnent normalement. 

SMB sur QUIC est aussi intégré aux [bonnes pratiques des machines Automanage pour Windows Server](automanage-windows-server.md) pour faciliter la gestion de SMB sur QUIC. QUIC utilise des certificats pour fournir son chiffrement et les organisations éprouvent souvent des difficultés à gérer les infrastructures à clé publique complexes. Les bonnes pratiques des machines Automanage garantissent que les certificats n’expirent pas sans avertissement et que SMB sur QUIC reste activé pour assurer la continuité maximale du service.

Pour plus d’informations, consultez [SMB sur QUIC](https://aka.ms/smboverquic) et [Gestion de SMB sur QUIC avec les bonnes pratiques des machines Automanage](automanage-smb-over-quic.md).
 

### <a name="extended-network-for-azure"></a>Réseau étendu pour Azure

Le réseau étendu pour Azure est disponible sur les images suivantes :

- Centre de données Windows Server 2022 : Azure Edition (expérience Bureau)
- Centre de données Windows Server 2022 : Azure Edition (Core)

Le réseau étendu Azure vous permet d’étirer un sous-réseau local dans Azure pour permettre aux machines virtuelles locales de conserver leurs adresses IP privées locales d’origine lors de la migration vers Azure. Pour en savoir plus, consultez [Réseau étendu Azure](/windows-server/manage/windows-admin-center/azure/azure-extended-network).  


## <a name="getting-started-with-windows-server-azure-edition"></a>Bien démarrer Windows Server Azure Edition

il est important de prendre en compte l’amont, quelles fonctionnalités d’Automanage pour Windows Server vous souhaitez utiliser, puis de choisir une image de machine virtuelle correspondante qui prend en charge toutes ces fonctionnalités.  Certaines des images de _Windows Server Azure Edition_ ne prennent en charge qu’un sous-ensemble de fonctionnalités, pour plus d’informations, consultez la table ci-dessous.

### <a name="deciding-which-image-to-use"></a>Décider quelle image utiliser 

|Image|Fonctionnalités|
|--|--|
|Centre de données Windows Server 2022 : Azure Edition (expérience Bureau) | SMB sur QUIC, réseau étendu pour Azure | 
| Centre de données Windows Server 2022 : Azure Edition (Core) | Patch à chaud, SMB sur QUIC, réseau étendu pour Azure | 

### <a name="creating-a-vm"></a>Création d’une machine virtuelle

Pour commencer à utiliser Automanage pour les fonctionnalités Windows Server sur une nouvelle machine virtuelle, utilisez la méthode de votre choix pour créer une machine virtuelle Azure, puis sélectionnez l’image _Windows Server Azure Edition_ qui correspond à l’ensemble de [fonctionnalités](#getting-started-with-windows-server-azure-edition) que vous souhaitez utiliser.  

> [!IMPORTANT]
> Certaines fonctionnalités ont des étapes de configuration spécifiques à effectuer pendant la création de la machine virtuelle, et certaines fonctionnalités en préversion ont des exigences spécifiques en matière d’adhésion et de consultation sur le portail.  Consultez les rubriques de chaque fonctionnalité ci-dessus pour en savoir plus sur l’utilisation de cette fonctionnalité avec votre machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur Azure Automanage](automanage-virtual-machines.md)