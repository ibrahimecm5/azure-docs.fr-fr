---
title: SMB sur QUIC avec les bonnes pratiques des machines Azure Automanage
description: Vue d’ensemble de la gestion de SMB sur QUIC avec les bonnes pratiques des machines Azure Automanage
author: daniellee-microsoft
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/1/2021
ms.author: jol
ms.openlocfilehash: 2246b54b6831d0e88581c80aedc4e39388c6f377
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327383"
---
# <a name="smb-over-quic-with-automanage-machine-best-practices"></a>SMB sur QUIC avec les bonnes pratiques des machines Automanage

SMB sur QUIC offre un « VPN SMB » pour les télétravailleurs, les utilisateurs d’appareil mobile et les filiales qui offre une connectivité sécurisée et fiable aux serveurs de fichiers périphériques sur des réseaux non approuvés comme Internet. Pour en savoir plus sur SMB sur QUIC et comment le configurer, consultez [SMB sur QUIC](https://aka.ms/smboverquic).

Par ailleurs, SMB sur QUIC est intégré aux bonnes pratiques des machines Automanage pour faciliter la gestion de SMB sur QUIC. QUIC utilise des certificats pour fournir son chiffrement et les organisations éprouvent souvent des difficultés à gérer les infrastructures à clé publique complexes. Les bonnes pratiques des machines Automanage garantissent que les certificats n’expirent pas sans avertissement et que SMB sur QUIC reste activé pour assurer la continuité maximale du service.

## <a name="how-to-get-started"></a>Pour commencer

> [!NOTE]
> Pour connaître les prérequis de l’utilisation des bonnes pratiques des machines Automanage, consultez [Activer sur les machines virtuelles dans le portail Azure](quick-create-virtual-machines-portal.md).

> [!NOTE]
> Pendant la phase de préversion, vous pouvez démarrer dans le portail en utilisant [ce lien](https://aka.ms/automanage-ws-portal-preview).

## <a name="enable-automanage-best-practices-when-creating-a-new-vm"></a>Activer les bonnes pratiques Automanage pour créer une machine virtuelle

Pour activer les bonnes pratiques des machines Automanage pour SMB sur QUIC sur une machine virtuelle, suivez ces étapes :

1. Connectez-vous au portail Azure à l’aide du lien de préversion ci-dessus.

2. Créez une machine virtuelle Azure avec l’image _Windows server 2022 Datacenter : édition Azure_ pour obtenir les fonctionnalités d’Automanage pour Windows Server, notamment SMB sur QUIC.

3. Sous l’onglet **Gestion**, pour le paramètre Environnement Azure Automanage, choisissez **Dev/Test** ou **Production** afin d’activer les bonnes pratiques des machines Automanage.

    :::image type="content" source="media\automanage-smb-over-quic\create-vm-automanage-setting.png" alt-text="Activer Automanage pendant la création d’une machine virtuelle.":::

4. Configurez les paramètres supplémentaires nécessaires et créez la machine virtuelle.

## <a name="enable-automanage-best-practices-on-existing-vms"></a>Activer les bonnes pratiques Automanage sur les machines virtuelles existantes

Vous pouvez également activer les bonnes pratiques Automanage pour une machine virtuelle que vous avez créée précédemment. Notez que la machine virtuelle doit avoir été créée avec l’image _Windows server 2022 Datacenter : édition Azure_ pour obtenir les fonctionnalités d’Automanage pour Windows Server, notamment SMB sur QUIC.

1. Accédez à la machine virtuelle que vous avez créée précédemment.
2. Sélectionnez le menu Automanage, choisissez l’environnement **Dev/Test** ou **Production**, puis cliquez sur **Activer**.

    :::image type="content" source="media\automanage-smb-over-quic\vm-enable-automanage.png" alt-text="Activer Automanage pour une machine virtuelle existante.":::

## <a name="viewing-automanage-best-practice-compliance"></a>Vérification de la conformité aux bonnes pratiques Automanage

La configuration des bonnes pratiques des machines, puis l’attribution et l’évaluation des stratégies de bonnes pratiques sur la machine virtuelle peuvent prendre quelques heures. Une fois l’opération terminée, vous voyez les stratégies SMB sur QUIC et leur état, comme indiqué ci-dessous. Ces stratégies sont constamment et automatiquement évaluées pour garantir que SMB sur QUIC est configuré correctement, et que les certificats utilisés sont valides et sains.

:::image type="content" source="media\automanage-smb-over-quic\vm-automanage-configured.png" alt-text="Voir les stratégies SMB sur QUIC pour une machine virtuelle.":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur SMB sur QUIC](https://aka.ms/smboverquic)
