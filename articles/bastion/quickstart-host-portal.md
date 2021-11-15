---
title: 'Démarrage rapide : Configurer Bastion à partir des paramètres de la machine virtuelle'
titleSuffix: Azure Bastion
description: Découvrez comment créer un hôte Azure Bastion à partir des paramètres de la machine virtuelle et comment se connecter à cette dernière de manière sécurisée par le biais de votre navigateur à l’aide d’une adresse IP privée.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 243b8a068c5301c5545c2afe8567ae1fbf71284c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426712"
---
# <a name="quickstart-configure-azure-bastion-from-vm-settings"></a>Démarrage rapide : Configurer Azure Bastion à partir des paramètres de la machine virtuelle

Cet article de démarrage rapide vous explique comment configurer Azure Bastion en fonction des paramètres de votre machine virtuelle dans le portail Azure, puis comment vous connecter à une machine virtuelle via une adresse IP privée. Une fois le service approvisionné, l’expérience RDP/SSH est disponible pour toutes les machines virtuelles du même réseau virtuel. La machine virtuelle n’a pas besoin d’une adresse IP publique, d’un logiciel client, d’un agent ou d’une configuration spéciale. Si vous n’avez pas besoin de l’adresse IP publique sur votre machine virtuelle, vous pouvez la supprimer. Vous vous connecterez alors à votre machine virtuelle via le portail à l’aide de l’adresse IP privée. Pour plus d’informations sur Azure Bastion, consultez [Présentation d’Azure Bastion](bastion-overview.md). 

## <a name="prerequisites"></a><a name="prereq"></a>Configuration requise

* Compte Azure avec un abonnement actif. Si vous n’en avez pas, [créez-en une gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Pour pouvoir vous connecter à une machine virtuelle par le biais de votre navigateur avec Bastion, vous devez être en mesure de vous connecter au portail Azure.

* Une machine virtuelle Windows dans un réseau virtuel. Si vous n’avez pas de machine virtuelle, créez-en une à l’aide du guide de [Démarrage rapide : Créer une machine virtuelle](../virtual-machines/windows/quick-create-portal.md).

  * Si vous avez besoin d’exemples de valeurs, consultez les [exemples de valeurs](#values) fournis.
  * Si vous disposez déjà d’un réseau virtuel, veillez à le sélectionner sous l’onglet Réseau lors de la création de votre machine virtuelle.
  * Si vous ne disposez pas encore d’un réseau virtuel, vous pouvez en créer un en même temps que votre machine virtuelle.
  * Vous n’avez pas besoin d’avoir une adresse IP publique pour cette machine virtuelle pour pouvoir vous connecter par le biais d’Azure Bastion.

* Rôles de machine virtuelle nécessaires :
  * Rôle de lecteur sur la machine virtuelle.
  * Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle.
  
* Ports de machine virtuelle nécessaires :
  * Ports d’entrée : RDP (3389)

 >[!NOTE]
 >L'utilisation d'Azure Bastion avec Azure DNS Private Zones n'est pas prise en charge pour le moment. Avant de commencer, assurez-vous que le réseau virtuel sur lequel vous prévoyez de déployer votre ressource Bastion n'est pas lié à une zone DNS privée.
 >

### <a name="example-values"></a><a name="values"></a>Exemples de valeurs

Lors de la création de cette configuration, vous pouvez utiliser les exemples de valeurs suivants ou les remplacer par vos propres valeurs.

**Valeurs de machine virtuelle et de réseau virtuel de base :**

|**Nom** | **Valeur** |
| --- | --- |
| Machine virtuelle| TestVM |
| Resource group | TestRG1 |
| Région | USA Est |
| Réseau virtuel | VNet1 |
| Espace d’adressage | 10.1.0.0/16 |
| Sous-réseaux | Front-end : 10.1.0.0/24 |

**Valeurs Azure Bastion :**

|**Nom** | **Valeur** |
| --- | --- |
| Nom | VNet1-bastion |
| + Nom du sous-réseau | AzureBastionSubnet |
| Adresses AzureBastionSubnet | Un sous-réseau dans votre espace d’adressage de réseau virtuel avec un masque de sous-réseau /26 ou supérieur.<br> Par exemple, 10.1.1.0/26.  |
| Niveau/référence SKU | standard |
| Adresse IP publique |  Création |
| Nom de l’adresse IP publique | VNet1-ip  |
| Référence SKU d’adresse IP publique |  standard  |
| Affectation  | statique |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Créer un hôte Bastion

Il existe différentes manières de configurer un hôte bastion. Dans les étapes suivantes, vous allez créer un hôte bastion dans le portail Azure directement à partir de votre machine virtuelle. Quand vous créez un hôte à partir d’une machine virtuelle, différents paramètres sont automatiquement renseignés conformément à votre machine virtuelle et/ou votre réseau virtuel.

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com).
1. Accédez à la machine virtuelle à laquelle vous voulez vous connecter, puis sélectionnez **Se connecter**.

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="Capture d’écran des paramètres de la machine virtuelle." lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. Dans la liste déroulante, sélectionnez **Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="Capture d’écran de la liste déroulante Bastion." lightbox="./media/quickstart-host-portal/bastion.png":::
1. Dans la page **TestVM | Se connecter**, sélectionnez **Utiliser Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Capture d’écran de la rubrique Utiliser Bastion.":::

1. Sur la page **Se connecter à l’aide d’Azure Bastion**, **Étape 1**, les valeurs sont préremplies, car vous créez l’hôte bastion directement à partir de votre machine virtuelle.

   :::image type="content" source="./media/quickstart-host-portal/create-step-1.png" alt-text="Capture d’écran des paramètres préremplis à l’étape 1." lightbox="./media/quickstart-host-portal/create-step-1.png":::

1. Sur la page **Se connecter à l’aide d’Azure Bastion**, **Étape 2**, configurez les valeurs du sous-réseau. L’espace d’adressage AzureBastionSubnet est prérempli avec un espace d’adressage suggéré. AzureBastionSubnet doit avoir un espace d’adressage de /26 ou plus (/25, /24, etc.). Nous vous recommandons d’utiliser un /26 afin que la mise à l’échelle de l’hôte ne soit pas limitée. Lorsque vous avez terminé de configurer ce paramètre, cliquez sur **Créer un sous-réseau** pour créer AzureBastionSubnet.

     :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="Capture d’écran de la création du sous-réseau Bastion.":::

1. Une fois le sous-réseau créé, la page passe automatiquement à l’**étape 3**. Pour l’étape 3, utilisez les valeurs suivantes :

   * **Nom :** nommez l’hôte bastion.
   * **Niveau :** le niveau désigne la référence SKU. Pour cet exercice, sélectionnez **Standard** dans la liste déroulante. La sélection de la référence SKU Standard vous permet de configurer le nombre d’instances pour la mise à l’échelle de l’hôte. La référence SKU De base ne prend pas en charge la mise à l’échelle de l’hôte. Pour plus d’informations, consultez [Paramètres de configuration – Références SKU](configuration-settings.md#skus).
   * **Nombre d’instances :** il s’agit du paramètre de mise à l’échelle de l’hôte. Utilisez le curseur pour le configurer. Si vous spécifiez la référence SKU De base, vous êtes limité à deux instances et ne pouvez pas configurer ce paramètre. Pour plus d’informations, consultez [Paramètres de configuration – Mise à l’échelle de l’hôte](configuration-settings.md#instance). Le nombre d’instances repose sur la référence SKU Standard. Dans ce démarrage rapide, vous pouvez sélectionner le nombre d’instances de votre choix, en gardant à l’esprit les considérations relatives à la [tarification](https://azure.microsoft.com/pricing/details/azure-bastion) des unités d’échelle.
   * **Adresse IP publique** : sélectionnez **Créer**.
   * **Nom de l’IP publique :** nom de la ressource d’IP publique.
   * **SKU de l’IP publique :** préconfigurée sur **Standard**.
   * **Attribution :** préconfiguré sur **Statique**. Vous ne pouvez pas utiliser une attribution Dynamique pour Azure Bastion.
   * **Groupe de ressources** : le même groupe de ressources que la machine virtuelle.

   :::image type="content" source="./media/quickstart-host-portal/create-step-3.png" alt-text="Capture d’écran de l’étape 3.":::
1. Après avoir renseigné les valeurs, sélectionnez **Créer Azure Bastion à l’aide des valeurs par défaut**. Azure valide vos paramètres, puis crée l’hôte. La création et le déploiement de l’hôte et de ses ressources prennent environ 5 minutes.

## <a name="remove-vm-public-ip-address"></a><a name="remove"></a>Supprimer une adresse IP publique de machine virtuelle

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a><a name="connect"></a>Se connecter à une machine virtuelle

Une fois Bastion déployé sur le réseau virtuel, l’écran devient la page de connexion.

1. Entrez le nom d’utilisateur et le mot de passe pour votre machine virtuelle. puis **Connecter**.

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="Capture d’écran montrant la boîte de dialogue Se connecter à l’aide d’Azure Bastion":::
1. La connexion RDP à cette machine virtuelle avec Bastion s’ouvrira directement dans le portail Azure (en HTML5) via le port 443 et le service Bastion. Cliquez sur **Autoriser** quand vous êtes invité à entrer des autorisations sur le Presse-papiers. Cela vous permet d’utiliser les flèches du Presse-papiers à distance sur la gauche de l’écran.

   * Lorsque vous vous connectez, le bureau de la machine virtuelle peut être différent de celui présenté dans la capture d’écran. 
   * L’utilisation de touches de raccourci lorsque vous êtes connecté à une machine virtuelle peut ne pas s’accompagner du même comportement que les touches de raccourci sur un ordinateur local. Par exemple, lorsque vous êtes connecté à une machine virtuelle Windows à partir d’un client Windows, CTRL+ALT+FIN est le raccourci clavier pour CTRL+ALT+SUPPR sur un ordinateur local. Pour effectuer cette opération depuis un Mac alors que vous êtes connecté à une machine virtuelle Windows, le raccourci clavier est Fn+CTRL+ALT+Retour arrière.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="Connexion RDP":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous avez fini d’utiliser le réseau virtuel et les machines virtuelles, supprimez le groupe de ressources et toutes les ressources qu’il contient :

1. Entrez le nom de votre groupe de ressources dans la zone **Rechercher** en haut du portail et sélectionnez-le dans les résultats de la recherche.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez votre groupe de ressources dans **TAPER LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un hôte bastion pour votre réseau virtuel, puis vous vous êtes connecté à une machine virtuelle de manière sécurisée avec Bastion. Vous pouvez à présent passer à l’étape suivante si vous souhaitez vous connecter à un groupe de machines virtuelles identiques.

> [!div class="nextstepaction"]
> [Se connecter à un groupe de machines virtuelles identiques à l’aide d’Azure Bastion](bastion-connect-vm-scale-set.md)
