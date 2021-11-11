---
title: Activer l’accès du navigateur aux machines virtuelles de laboratoire
description: Découvrez comment vous connecter à vos machines virtuelles à l’aide d’un navigateur.
ms.topic: how-to
ms.date: 10/29/2021
ms.openlocfilehash: f712d0090defa28f673807b835dbe3642cfdface
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464360"
---
# <a name="connect-to-your-lab-virtual-machines-through-a-browser"></a>Se connecter à vos machines virtuelles de laboratoire à l’aide d’un navigateur 

DevTest Labs s’intègre avec [Azure Bastion](../bastion/index.yml) qui vous permet de vous connecter à vos machines virtuelles de laboratoire via un navigateur. Une fois l’option **Connexion du navigateur** activée, les utilisateurs du laboratoire peuvent accéder à leurs machines virtuelles via un navigateur.  

Dans ce guide pratique, vous allez vous connecter à une machine virtuelle de laboratoire à l’aide de la commande **Connexion du navigateur**.

## <a name="prerequisites"></a>Prérequis

- Une machine virtuelle de laboratoire, avec un [réseau virtuel configuré pour Bastion et le paramètre **Connexion du navigateur** activé](enable-browser-connection-lab-virtual-machines.md).

- Un navigateur web configuré pour autoriser l’affichage des fenêtres contextuelles de `https://portal.azure.com:443`.

## <a name="launch-virtual-machine-in-a-browser"></a>Lancez la machine virtuelle dans un navigateur

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre laboratoire dans **DevTest Labs**.

1. Sélectionnez une machine virtuelle.

1. Dans le menu supérieur, sélectionnez **Connexion du navigateur**.

1. Dans la section **Connexion du navigateur**, entrez vos informations d’identification, puis sélectionnez **Connecter**.

    :::image type="content" source="./media/connect-virtual-machine-through-browser/lab-vm-browser-connect.png" alt-text="Capture d’écran du bouton Connecter du navigateur.":::

## <a name="next-steps"></a>Étapes suivantes

[Ajouter une machine virtuelle à un laboratoire dans Azure DevTest Labs](devtest-lab-add-vm.md)
