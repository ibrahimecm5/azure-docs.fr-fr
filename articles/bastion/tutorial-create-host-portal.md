---
title: 'Tutoriel : Créer un hôte Azure Bastion : Machine virtuelle Windows : portail'
description: Découvrez comment créer un hôte Azure Bastion et se connecter à une machine virtuelle Windows.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d2e071fd7dc368df867b17a53f9e994ee5c16a6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430968"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm"></a>Tutoriel : Configurer Bastion et se connecter à une machine virtuelle Windows

Ce tutoriel vous explique comment vous connecter à une machine virtuelle via votre navigateur à l’aide d’Azure Bastion et du portail Azure. Dans ce tutoriel, avec le portail Azure, vous déployez Bastion sur votre réseau virtuel. Une fois le service approvisionné, l’expérience RDP/SSH est disponible pour toutes les machines virtuelles du même réseau virtuel. Quand vous utilisez Bastion pour vous connecter, la machine virtuelle n’a pas besoin d’une adresse IP publique ou d’un logiciel spécial. Après avoir déployé Bastion, vous pouvez supprimer l’adresse IP publique de votre machine virtuelle si elle n’est pas nécessaire pour d’autres choses. Ensuite, vous vous connectez à une machine virtuelle via son adresse IP privée à l’aide du portail Azure. Pour plus d’informations sur Azure Bastion, consultez [Présentation d’Azure Bastion](bastion-overview.md).

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un hôte bastion pour votre réseau virtuel.
> * Supprimer l’adresse IP publique d’une machine virtuelle.
> * Se connecter à une machine virtuelle Windows.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Un [réseau virtuel](../virtual-network/quick-create-portal.md).
* Une machine virtuelle Windows hébergée sur le réseau virtuel. Si vous n’avez pas de machine virtuelle, créez-en une à l’aide du guide de [Démarrage rapide : Créer une machine virtuelle](../virtual-machines/windows/quick-create-portal.md).
* Les rôles requis suivants pour vos ressources :
   * Rôles de machine virtuelle nécessaires :
     * Rôle de lecteur sur la machine virtuelle.
     * Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle.

* Ports : Pour vous connecter à la machine virtuelle Windows, les ports suivants doivent être ouverts sur votre machine virtuelle Windows :
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
| Nombre d’instances (mise à l’échelle de l’hôte)| Au moins 3 |
| Adresse IP publique |  Création |
| Nom de l’adresse IP publique | VNet1-ip  |
| Référence SKU d’adresse IP publique |  standard  |
| Affectation  | statique |

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Créer un hôte Bastion

Cette section vous aide à créer l’objet bastion dans votre réseau virtuel. Cela est nécessaire pour créer une connexion sécurisée à une machine virtuelle dans le réseau virtuel.

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com).
1. Tapez **Bastion** dans le champ de recherche.
1. Sous services, cliquez sur **Bastions**.
1. Dans la page Bastions, cliquez sur **+ Créer** pour ouvrir la page **Créer un bastion**.
1. Dans la page **Create a bastion** (Créer un bastion), configurez une nouvelle ressource Bastion.

   :::image type="content" source="./media/tutorial-create-host-portal/review-create.png" alt-text="Capture d’écran de la page Créer un portail Bastion." lightbox="./media/tutorial-create-host-portal/create.png":::

### <a name="project-details"></a>Détails du projet

* **Abonnement** : abonnement Azure que vous souhaitez utiliser.

* **Groupe de ressources** : Le groupe de ressources Azure dans lequel la nouvelle ressource Bastion est créée. Si vous n’avez pas de groupe de ressources, vous pouvez en créer un.

### <a name="instance-details"></a>Détails de l’instance

* **Nom** : Nom de la nouvelle ressource Bastion.

* **Région** : région publique Azure dans laquelle est créée la ressource. Choisissez la région dans laquelle réside votre réseau virtuel.

* **Niveau :** également appelé **Référence SKU**. Pour ce tutoriel, nous sélectionnons la référence SKU **Standard** dans la liste déroulante. La sélection de la référence SKU Standard vous permet de configurer le nombre d’instances pour la mise à l’échelle de l’hôte. La référence SKU De base ne prend pas en charge la mise à l’échelle de l’hôte. Pour plus d’informations, consultez [Paramètres de configuration – Références SKU](configuration-settings.md#skus).

* **Nombre d’instances :** paramètre de la **mise à l’échelle de l’hôte**, configuré en incréments d’unités d’échelle. Utilisez le curseur pour configurer le nombre d’instances. Si vous avez spécifié la référence SKU de niveau De base, vous ne pouvez pas configurer ce paramètre. Pour plus d’informations, consultez [Paramètres de configuration - Mise à l’échelle de l’hôte](configuration-settings.md#instance). Dans ce tutoriel, vous pouvez sélectionner le nombre d’instances que vous préférez, en gardant à l’esprit les considérations relatives aux [tarifs](https://azure.microsoft.com/pricing/details/azure-bastion) des unités d’échelle.

### <a name="configure-virtual-networks"></a>Configurer des réseaux virtuels

* **Réseau virtuel** : Réseau virtuel dans lequel la ressource Bastion est créée. Vous pouvez créer un réseau virtuel dans le portail au cours de ce processus ou utiliser un réseau virtuel existant. Si vous utilisez un réseau virtuel existant, vérifiez que ce dernier a suffisamment d’espace d’adresse disponible pour prendre en compte les exigences du sous-réseau Bastion. Si vous ne voyez pas votre réseau virtuel dans la liste déroulante, vérifiez que vous avez sélectionné le groupe de ressources approprié.

* **Sous-réseau** : Une fois que vous avez créé ou sélectionné un réseau virtuel, le champ sous-réseau s’affiche dans la page. Il s’agit du sous-réseau dans lequel vos instances Bastion seront déployées. Le nom doit être **AzureBastionSubnet**. Pour ajouter le sous-réseau, consultez les étapes suivantes.

#### <a name="manage-subnet-configuration"></a>Gérer la configuration du sous-réseau

En règle générale, vous ne disposez pas déjà d’un sous-réseau Azure Bastion configuré. Pour configurer le sous-réseau Bastion : 

1. Sélectionnez **Gérer la configuration du sous-réseau**. Vous accédez ainsi à la page **Sous-réseaux**.

   :::image type="content" source="./media/tutorial-create-host-portal/subnet.png" alt-text="Capture d’écran de Gérer la configuration du sous-réseau":::
1. Dans la page **Sous-réseaux**, sélectionnez **+Sous-réseau** pour ouvrir la page **Ajouter un sous-réseau**. 

1. Créez un sous-réseau en utilisant les instructions suivantes :

   * Le sous-réseau doit être nommé **AzureBastionSubnet**.
   * Le sous-réseau doit être au moins égal à /26. Pour la référence SKU Standard, nous vous recommandons d’utiliser un sous-réseau /26 ou supérieur pour prendre en charge les instances de mise à l’échelle de l’hôte supplémentaires futures.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-subnet.png" alt-text="Capture d’écran du sous-réseau AzureBastionSubnet":::

1. Vous n’avez pas besoin de renseigner des champs supplémentaires dans cette page. Sélectionnez **Enregistrer** en bas de la page pour enregistrer les paramètres et fermer la page **Ajouter un sous-réseau**.

1. En haut de la page **Sous-réseaux**, sélectionnez **Créer un bastion** pour revenir à la page de configuration du bastion.

   :::image type="content" source="./media/tutorial-create-host-portal/create-a-bastion.png" alt-text="Capture d’écran de l’option de création d’un bastion":::

### <a name="public-ip-address"></a>Adresse IP publique

Adresse IP publique de la ressource Bastion où RDP/SSH sera accessible (sur le port 443). Créez une **adresse IP publique**. L’adresse IP publique doit être située dans la même région que la ressource Bastion que vous créez. Cette adresse IP n’a rien à voir avec les machines virtuelles auxquelles vous voulez vous connecter. Il s’agit de l’adresse IP publique de la ressource d’hôte bastion.

   * **Nom de l’adresse IP publique** : Nom de la ressource de l’adresse IP publique. Pour ce tutoriel, vous pouvez laisser la valeur par défaut.
   * **Référence (SKU) d'adresse IP publique** : Par défaut, ce paramètre est prérempli avec la valeur **Standard**. Azure Bastion utilise/prend en charge uniquement la référence SKU d’IP publique Standard.
   * **Attribution** : Par défaut, ce paramètre est prérempli avec la valeur **Statique**.

### <a name="review-and-create"></a>Examiner et créer

1. Quand vous avez terminé de spécifier les paramètres, sélectionnez **Vérifier + créer**. Cela valide les valeurs. Une fois la validation réussie, vous pouvez créer la ressource Bastion.
1. Passez vos paramètres en revue. 
1. Au bas de la page, sélectionnez **Créer**.
1. Un message vous informe que votre déploiement est en cours. L’état s’affiche sur cette page à mesure que les ressources sont créées. Il faut environ 5 minutes pour que la ressource Bastion soit créée et déployée.

## <a name="remove-vm-public-ip-address"></a>Supprimer une adresse IP publique de machine virtuelle

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a>Se connecter à une machine virtuelle

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin d’utiliser cette application, supprimez vos ressources en effectuant les étapes suivantes :

1. Entrez le nom de votre groupe de ressources dans la zone **Recherche** en haut du portail. Quand vous voyez votre groupe de ressources dans les résultats de la recherche, sélectionnez-le.
1. Sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources dans **TAPER LE NOM DU GROUPE DE RESSOURCES :** , puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un hôte bastion et l’avez associé à un réseau virtuel. Vous avez ensuite supprimé l’adresse IP publique d’une machine virtuelle et vous y êtes connecté. Vous pouvez choisir d’utiliser des groupes de sécurité réseau avec votre sous-réseau Azure Bastion. Pour cela, consultez :

> [!div class="nextstepaction"]
> [Utiliser des groupes de sécurité réseau](bastion-nsg.md)
