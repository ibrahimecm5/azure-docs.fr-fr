---
title: Intégrer Azure NetApp Files avec Azure VMware Solution
description: Utilisez Azure NetApp Files avec des machines virtuelles Azure VMware Solution pour migrer et synchroniser des données entre des serveurs locaux, des machines virtuelles Azure VMware Solution et des infrastructures cloud.
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 7335434c30b88e54fbfd15b18f3604954cbdef0f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226770"
---
# <a name="integrate-azure-netapp-files-with-azure-vmware-solution"></a>Intégrer Azure NetApp Files avec Azure VMware Solution

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) est un service Azure dédié à la migration et à l'exécution des charges de travail de fichiers d'entreprise les plus exigeantes dans le cloud : bases de données, SAP et applications informatiques hautes performances, sans aucune modification du code. Dans cet article, vous allez configurer, tester et vérifier le volume Azure NetApp Files en tant que partage de fichiers pour des charges de travail Azure VMware Solution à l’aide du protocole NFS (Network File System). Le système d’exploitation invité s’exécute à l’intérieur des machines virtuelles qui accèdent aux volumes Azure NetApp Files. 

Azure NetApp Files et Azure VMware Solution sont créées dans la même région Azure. Azure NetApp Files est disponible dans de nombreuses [régions Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all) et prend en charge la réplication entre les régions. Pour plus d’informations sur les méthodes de configuration d’Azure NetApp Files, consultez [Hiérarchie de stockage d’Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

Services où Azure NetApp Files est utilisé :

- **Connexions Active Directory** : Azure NetApp Files prend en charge [Active Directory Domain Services (ADDS) et Azure Active Directory Domain Services (AADDS)](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use).

- **Protocole de partage** : Azure NetApp Files prend en charge les protocoles SMB (Server Message Block) et NFS (Network File System). Cette prise en charge signifie que les volumes peuvent être montés sur le client Linux et peuvent être mappés sur le client Windows.

- **Azure VMware Solution** : Les partages Azure NetApp Files peuvent être montés à partir de machines virtuelles créées dans l’environnement de solution VMware Azure.


Le diagramme montre une connexion par le biais d’Azure ExpressRoute à un cloud privé Azure VMware Solution. L'environnement Azure VMware Solution accède au partage Azure NetApp Files monté sur des machines virtuelles Azure VMware Solution.

:::image type="content" source="media/netapp-files/netapp-files-topology.png" alt-text="Diagramme représentant NetApp Files pour l’architecture Azure VMware Solution." border="false":::


## <a name="prerequisites"></a>Prérequis 

> [!div class="checklist"]
> * Un abonnement Azure avec Azure NetApp Files activé
> * Un sous-réseau pour Azure NetApp Files
> * Une machine virtuelle Linux sur Azure VMware Solution
> * Des machines virtuelles Windows sur Azure VMware Solution


## <a name="create-and-mount-azure-netapp-files-volumes"></a>Créer et monter des volumes Azure NetApp Files

Vous allez créer et monter des volumes Azure NetApp Files sur des machines virtuelles Azure VMware Solution.

1. [Créer un compte NetApp](../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

1. [Configurer un pool de capacité](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

1. [Créer un volume SMB pour Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md).

1. [Créer un volume NFS pour Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md).

1. [Déléguer un sous-réseau à Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md).


## <a name="verify-pre-configured-azure-netapp-files"></a>Vérifier les volumes Azure NetApp Files préconfigurés 

Vous allez vérifier les volumes Azure NetApp Files préconfigurés créés dans Azure au niveau de service Azure NetApp Files Premium.

1. Dans le portail Azure, sous **STOCKAGE**, sélectionnez **Azure NetApp Files**. Une liste de vos instances Azure NetApp Files configurées s’affiche. 

   :::image type="content" source="media/netapp-files/azure-netapp-files-list.png" alt-text="Capture d’écran montrant la liste des volumes Azure NetApp Files préconfigurés."::: 

2. Sélectionnez un compte de fichiers NetApp configuré pour afficher ses paramètres. Par exemple, sélectionnez **Contoso-anf2**. 

3. Sélectionnez **Pools de capacités** pour vérifier le pool configuré. 

   :::image type="content" source="media/netapp-files/netapp-settings.png" alt-text="Capture d’écran montrant les options permettant d’afficher les pools de capacité et les volumes d’un compte NetApp Files configuré.":::

   La page Pools de capacité s’ouvre et affiche la capacité et le niveau de service. Dans cet exemple, le pool de stockage est configuré avec 4 Tio et un niveau de service Premium.

4. Sélectionnez **Volumes** pour voir les volumes créés sous le pool de capacités. (Voir la capture d’écran précédente.)

5. Sélectionnez un volume pour voir sa configuration.  

   :::image type="content" source="media/netapp-files/azure-netapp-volumes.png" alt-text="Capture d’écran montrant les volumes créés sous le pool de capacités.":::

   La fenêtre qui s’ouvre affiche les détails de configuration du volume.

   :::image type="content" source="media/netapp-files/configuration-of-volume.png" alt-text="Capture d’écran montrant les détails de configuration d’un volume.":::

   Vous pouvez voir que la taille du volume anfvolume est de 200 Gio et qu'il se trouve dans le pool de capacité anfpool1. Il est exporté en tant que partage de fichiers NFS via 10.22.3.4:/ANFVOLUME. Une adresse IP privée du réseau virtuel Azure (VNet) a été créée pour Azure NetApp Files et le chemin NFS à monter sur la machine virtuelle.

   Pour découvrir les performances de volume Azure NetApp Files par taille ou « quota », consultez [Considérations sur les performances pour Azure NetApp Files](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Vérifier le mappage préconfiguré du partage de la machine virtuelle Azure VMware Solution

Pour rendre votre partage Azure NetApp Files accessible à votre machine virtuelle Azure VMware Solution, vous devez comprendre le mappage de partages SMB et NFS. Une fois les volumes SMB ou NFS configurés, vous pouvez les monter comme indiqué ici.

- **Partage SMB** : créez une connexion Active Directory avant de déployer un volume SMB. Les contrôleurs de domaine spécifiés doivent être accessibles par le sous-réseau délégué d’Azure NetApp Files pour une connexion réussie. Une fois Active Directory configuré dans le compte Azure NetApp Files, il apparaît comme élément sélectionnable lors de la création de volumes SMB.

- **Partage NFS** : Azure NetApp Files contribue à la création des volumes à l’aide de NFS ou du protocole double (NFS et SMB). La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool. Vous pouvez monter NFS sur le serveur Linux à l’aide des lignes de commande ou des entrées /etc/fstab.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment intégrer Azure NetApp Files avec vos charges de travail Azure VMware Solution, vous pouvez découvrir ce qui suit :

- [Limitations des ressources pour Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Consignes pour planifier un réseau Azure NetApp Files](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Réplication inter-région des volumes Azure NetApp Files](../azure-netapp-files/cross-region-replication-introduction.md) 
- [FAQ sur NFS pour Azure NetApp Files](../azure-netapp-files/faq-nfs.md)
- [FAQ sur SMB pour Azure NetApp Files](../azure-netapp-files/faq-smb.md)
