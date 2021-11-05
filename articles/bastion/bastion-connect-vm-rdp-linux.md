---
title: Se connecter à une machine virtuelle Linux avec RDP
titleSuffix: Azure Bastion
description: Découvrez comment utiliser Azure Bastion pour vous connecter à une machine virtuelle Linux avec RDP.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8704ade5def1938e789ad4e092cca804cb9af62b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033510"
---
# <a name="create-an-rdp-connection-to-a-linux-vm-using-azure"></a>Créer une connexion RDP à une machine virtuelle Linux avec Azure 

Cet article vous montre comment créer de manière sécurisée et fluide une connexion RDP à des machines virtuelles Linux situées dans un réseau virtuel Azure, directement par le biais du portail Azure. Quand vous utilisez Azure Bastion, vos machines virtuelles n’ont pas besoin de client, d’agent ni de logiciel supplémentaire. Vous pouvez également vous connecter à une machine virtuelle Linux avec SSH. Pour plus d’informations, consultez [Créer une connexion SSH à une machine virtuelle Linux](bastion-connect-vm-ssh-linux.md).

Azure Bastion fournit une connectivité sécurisée à toutes les machines virtuelles du réseau virtuel dans lequel il est provisionné. Azure Bastion protège vos machines virtuelles contre l’exposition des ports RDP/SSH au monde extérieur, tout en fournissant un accès sécurisé à l’aide de RDP/SSH. Pour plus d’informations, consultez la page [Présentation d’Azure Bastion](bastion-overview.md).

> [!NOTE]
> L’utilisation du protocole RDP pour se connecter à une machine virtuelle Linux nécessite la référence SKU Azure Bastion Standard.
>

Lorsque vous utilisez Azure Bastion pour vous connecter à une machine virtuelle Linux à l’aide de RDP, vous devez utiliser le nom d’utilisateur/mot de passe pour l’authentification.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous respectez les critères suivants :

Assurez-vous que vous avez configuré un hôte Azure Bastion pour le réseau virtuel dans lequel réside la machine virtuelle. Pour plus d’informations, consultez [Créer un hôte Azure Bastion](tutorial-create-host-portal.md). Une fois que le service Bastion est approvisionné et déployé dans votre réseau virtuel, vous pouvez l’utiliser pour vous connecter à toutes les machines virtuelles dans ce réseau virtuel.

Pour établir une connexion RDP à une machine virtuelle Linux, vous devez également vous assurer que xrdp est installé et configuré sur votre machine virtuelle Linux. Pour savoir comment procéder, consultez [Utiliser xrdp avec Linux](../virtual-machines/linux/use-remote-desktop.md).

### <a name="required-roles"></a>Rôles nécessaires

Pour établir une connexion, les rôles suivants sont nécessaires :

* Rôle de lecteur sur la machine virtuelle
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle
* Rôle de lecteur sur la ressource Azure Bastion

### <a name="ports"></a>Ports

Pour vous connecter à la machine virtuelle Linux par le biais du protocole RDP, les ports suivants doivent être ouverts sur votre machine virtuelle :

* Port d’entrée : RDP (3389) *ou*
* Port d’entrée : valeur personnalisée (vous devez spécifier ce port personnalisé quand vous vous connectez à la machine virtuelle avec Azure Bastion)

### <a name="supported-configurations"></a>Configurations prises en charge

À l’heure actuelle, Azure Bastion nécessite **xrdp** pour prendre en charge la connexion à des machines virtuelles Linux par le biais du protocole RDP.

## <a name="connect"></a><a name="rdp"></a>Connexion

[!INCLUDE [Connect to a Linux VM using RDP](../../includes/bastion-vm-rdp-linux.md)]
 
## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
