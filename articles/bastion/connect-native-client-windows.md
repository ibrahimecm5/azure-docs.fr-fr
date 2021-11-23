---
title: Se connecter à une machine virtuelle à l’aide du client Windows natif et d’Azure Bastion
titleSuffix: Azure Bastion
description: Découvrez comment vous connecter à une machine virtuelle à partir d’un ordinateur Windows à l’aide de Bastion et du client Windows natif.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 11/01/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6332b7443e3abc36810fc3085f3f2c413a5d7d1c
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137802"
---
# <a name="connect-to-a-vm-using-bastion-and-the-native-client-on-your-windows-computer-preview"></a>Se connecter à une machine virtuelle à l’aide de Bastion et du client natif sur votre ordinateur Windows (préversion)

Azure Bastion offre à présent la prise en charge de la connexion aux machines virtuelles cibles dans Azure à l’aide d’un client natif sur votre station de travail Windows. Cette fonctionnalité vous permet de vous connecter à vos machines virtuelles cibles via Bastion en utilisant Azure CLI et étend vos options de connexion pour inclure la paire de clés SSH locale et Azure Active Directory (Azure AD). Cet article vous aide à configurer Bastion avec les paramètres requis, puis à vous connecter à une machine virtuelle dans le réseau virtuel. Pour plus d’informations, consultez la page [Présentation d’Azure Bastion](bastion-overview.md).

> [!NOTE]
> Cette configuration nécessite la référence SKU Standard pour Azure Bastion.
>

> [!IMPORTANT]
> Cette fonctionnalité est encore en cours de déploiement global. Si vous ne pouvez pas y accéder dans le portail Azure, veuillez patienter quelques jours, puis réessayez.


Actuellement, cette fonctionnalité présente les limitations suivantes :

* La connexion à votre machine virtuelle cible à l’aide d’un port ou d’un protocole personnalisé n’est pas encore disponible avec la prise en charge du client natif. Si vous souhaitez utiliser un port ou un protocole personnalisé pour vous connecter à votre machine virtuelle cible par le biais de Bastion, utilisez l’expérience Portail Azure.

* La connexion à votre machine virtuelle cible en utilisant un nom d’utilisateur et un mot de passe locaux n’est pas encore prise en charge. Si vous souhaitez utiliser un nom d’utilisateur et un mot de passe locaux en guise d’informations d’identification pour vous connecter à votre machine virtuelle cible via Bastion, optez pour l’expérience du portail Azure.

* La connexion à l’aide d’une clé privée SSH stockée dans Azure Key Vault n’est pas prise en charge avec cette fonctionnalité. Téléchargez votre clé privée dans un fichier sur votre ordinateur local avant de vous connecter à votre machine virtuelle Linux à l’aide d’une paire de clés SSH.

## <a name="prerequisites"></a><a name="prereq"></a>Configuration requise

Avant de commencer, vérifiez que vous respectez les critères suivants :

* La dernière version des commandes CLI (version 2.30 ou ultérieure) est installée. Pour plus d’informations sur l’installation des commandes CLI, consultez [Installer l’interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli) et [Prise en main d’Azure CLI](/cli/azure/get-started-with-azure-cli).
* Un réseau virtuel Azure.
* Une machine virtuelle Azure se trouvant dans le réseau virtuel/

## <a name="configure-bastion"></a>Configurer Bastion

Suivez les instructions relatives à votre environnement.

### <a name="to-modify-an-existing-bastion-host"></a>Pour modifier un hôte bastion existant

Si vous avez déjà configuré Bastion pour votre réseau virtuel, modifiez les paramètres suivants :

1. Accédez à la page **Configuration** de votre ressource Bastion. Vérifiez que la référence (SKU) est **Standard**. Si ce n’est pas le cas, remplacez-la par **standard** dans la liste déroulante.
1. Cochez la case pour **Prise en charge du client natif** et appliquez vos modifications.

    :::image type="content" source="./media/connect-native-client-windows/update-host.png" alt-text="Paramètres de la mise à jour d’un hôte existant avec la zone Prise en charge du client natif sélectionnée." lightbox="./media/connect-native-client-windows/update-host-expand.png":::

### <a name="to-configure-a-new-bastion-host"></a>Pour configurer un nouvel hôte bastion

Si vous n’avez pas encore configuré d’hôte bastion, consultez [Créer un hôte bastion](tutorial-create-host-portal.md#createhost). Lors de la configuration de l’hôte bastion, spécifiez les paramètres suivants :

1. Sous l’onglet **Notions de base**, dans **Détails de l’instance -> Niveau**, sélectionnez **Standard** pour créer un hôte bastion à l’aide de la référence SKU Standard.

   :::image type="content" source="./media/connect-native-client-windows/standard.png" alt-text="Paramètres pour un nouvel hôte bastion avec la référence SKU Standard sélectionnée." lightbox="./media/connect-native-client-windows/standard.png":::
1. Sous l’onglet **Avancé**, activez la case à cocher **Prise en charge du client natif**.

   :::image type="content" source="./media/connect-native-client-windows/new-host.png" alt-text="Paramètres pour un nouvel hôte bastion avec la zone Prise en charge du client natif sélectionnée." lightbox="./media/connect-native-client-windows/new-host-expand.png":::

## <a name="verify-roles-and-ports"></a>Vérifier les rôles et les ports

Vérifiez que les rôles et les ports suivants sont configurés pour la connexion.

### <a name="required-roles"></a>Rôles nécessaires

* Rôle de lecteur sur la machine virtuelle.
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle.
* Rôle de lecteur sur la ressource Azure Bastion.
* Rôle Connexion de l’administrateur aux machines virtuelles ou Connexion de l’utilisateur aux machines virtuelles, si vous utilisez la méthode de connexion Azure AD.

### <a name="ports"></a>Ports

Pour vous connecter à une machine virtuelle Linux à l’aide de la prise en charge du client natif, vous devez ouvrir les ports suivants sur votre machine virtuelle Linux :

* Port d’entrée : SSH (22)

Pour vous connecter à une machine virtuelle Windows à l’aide de la prise en charge du client natif, vous devez ouvrir les ports suivants sur votre machine virtuelle Windows :

* Port d’entrée : RDP (3389)

Si vous souhaitez utiliser un port personnalisé pour vous connecter à votre machine virtuelle cible, utilisez plutôt les instructions correspondant au portail Azure.

## <a name="connect-to-a-vm"></a><a name="connect"></a>Se connecter à une machine virtuelle

Cette section vous aide à vous connecter à votre machine virtuelle. Utilisez les étapes correspondant au type de machine virtuelle à laquelle vous souhaitez vous connecter.

1. Connectez-vous à votre compte Azure et sélectionnez votre abonnement contenant votre ressource Bastion.

   ```azurecli-interactive
   az login
   az account list
   az account set --subscription "<subscription ID>"
   ```

### <a name="connect-to-a-linux-vm"></a>Se connecter à une machine virtuelle Linux

1. Connectez-vous à votre machine virtuelle Linux cible à l’aide de l’une des options suivantes :

   * Si vous vous connectez à une machine virtuelle avec connexion Azure AD, utilisez la commande suivante. Pour en savoir plus sur l’utilisation d’Azure AD pour vous connecter à vos machines virtuelles Linux Azure, consultez [Machines virtuelles Linux Azure et Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md).

     ```azurecli-interactive
     az network bastion ssh --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type  "AAD"
     ```

   * Si vous vous connectez à l’aide d’une paire de clés SSH, utilisez la commande suivante.

      ```azurecli-interactive
      az network bastion ssh "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "ssh-key" --username "<Username>" --ssh-key "<Filepath>"
      ```

### <a name="connect-to-a-windows-vm"></a>Se connecter à une machine virtuelle Windows

1. Connectez-vous à votre machine virtuelle Windows cible à l’aide de la commande suivante via une connexion Azure AD. Pour en savoir plus sur l’utilisation d’Azure AD pour vous connecter à vos machines virtuelles Windows Azure, consultez [Machines virtuelles Windows Azure et Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md).

      ```azurecli-interactive
      az network bastion rdp --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>"
      ```

1. Une fois que vous êtes connecté à votre machine virtuelle cible, le client natif sur votre station de travail s’ouvre avec votre session de machine virtuelle (**mstsc** pour les sessions RDP et l’**extension CLI ssh** pour les sessions SSH).

## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
