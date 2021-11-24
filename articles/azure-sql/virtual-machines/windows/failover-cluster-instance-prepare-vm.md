---
title: Préparer des machines virtuelles Azure pour une instance FCI
description: Préparer vos machines virtuelles Azure pour les utiliser avec une instance de cluster de basculement (FCI) et SQL Server.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: ac7b5617cf81b2845701360cf1d7af3fbc4858c0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156141"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Préparer des machines virtuelles pour une instance FCI (SQL Server sur des machines virtuelles Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article explique comment préparer des machines virtuelles Azure pour les utiliser avec une instance de cluster de basculement SQL Server (FCI). Les paramètres de configuration varient en fonction de la solution de stockage FCI. Par conséquent, vérifiez que vous avez choisi la configuration adaptée à votre environnement et à votre entreprise. 

Pour plus d’informations, consultez une présentation de [l’instance FCI avec SQL Server sur les machines virtuelles Azure](failover-cluster-instance-overview.md) et [les meilleures pratiques de cluster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Il est maintenant possible d’effectuer un lift-and-shift de votre solution d’instance de cluster de basculement vers SQL Server sur des machines virtuelles Azure à l’aide d’Azure Migrate. Pour en savoir plus, consultez [Migrer une instance de cluster de basculement](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md). 

## <a name="prerequisites"></a>Prérequis 

- Un abonnement Microsoft Azure. Démarrer [gratuitement](https://azure.microsoft.com/free/). 
- Un domaine Windows sur des machines virtuelles Azure ou une instance Active Directory locale étendue à Azure par jumelage de réseaux virtuels.
- Un compte qui dispose des autorisations nécessaires pour créer des objets sur les machines virtuelles Azure et dans Active Directory.
- Un réseau virtuel Azure et un ou plusieurs sous-réseaux avec suffisamment d’espace d’adressage IP pour ces composants :
   - Les deux machines virtuelles.
   - Une adresse IP pour le cluster de basculement Windows.
   - Une adresse IP pour chaque instance de cluster de basculement
- Un DNS configuré sur le réseau Azure, pointant vers les contrôleurs de domaine.



## <a name="choose-an-fci-storage-option"></a>Choisir une option de stockage FCI

Les paramètres de configuration de votre machine virtuelle varient en fonction de l’option de stockage que vous prévoyez d’utiliser pour votre instance de cluster de basculement SQL Server. Avant de préparer la machine virtuelle, passez en revue les [options de stockage FCI disponibles](failover-cluster-instance-overview.md#storage) et choisissez l’option la mieux adaptée aux besoins de votre environnement et de votre entreprise. Sélectionnez ensuite avec soin les options de configuration de machine virtuelle appropriées dans cet article en fonction du stockage sélectionné. 

## <a name="choose-vm-availability"></a>Choisir la disponibilité des machines virtuelles 

La fonctionnalité de cluster de basculement nécessite que les machines virtuelles soient placées dans un [groupe à haute disponibilité](../../../virtual-machines/linux/tutorial-availability-sets.md) ou dans une [zone de disponibilité](../../../availability-zones/az-overview.md#availability-zones).

Sélectionnez avec soin l’option de disponibilité de la machine virtuelle qui correspond à la configuration souhaitée pour votre cluster : 

- **Disques partagés Azure** : l’option de disponibilité varie selon que vous utilisez un disque SSD Premium ou un disque Ultra :
   - **Disque SSD Premium avec stockage redondant interzone (ZRS)**  : [zone de disponibilité](../../../availability-zones/az-overview.md#availability-zones) dans différentes zones. Un [disque SSD Premium avec stockage redondant interzone](../../../virtual-machines/disks-redundancy.md#zone-redundant-storage-for-managed-disks) réplique votre disque managé Azure de façon synchrone dans trois zones de disponibilité Azure au sein de la région sélectionnée. La partie des machines virtuelles du cluster de basculement peut être placée dans différentes zones de disponibilité, ce qui vous permet d’obtenir une instance FCI SQL Server avec redondance interzone offrant un contrat SLA de disponibilité des machines virtuelles de 99,99 %. La latence de disque du stockage ZRS est supérieure en raison de la copie de données interzone.
   - **Disque SSD Premium avec stockage localement redondant (LRS)**  : [groupe à haute disponibilité](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) dans différents domaines d’erreur/de mise à jour pour [disque SSD Premium LRS](../../../virtual-machines/disks-redundancy.md#locally-redundant-storage-for-managed-disks). Vous pouvez également choisir de placer les machines virtuelles au sein d’un [groupe de placement de proximité](../../../virtual-machines/windows/proximity-placement-groups-portal.md) pour les localiser plus près les unes des autres. La combinaison d’un groupe à haute disponibilité et d’un groupe de placement de proximité fournit la latence la plus faible pour les disques partagés, car les données sont répliquées localement dans un centre de données, et offre un contrat SLA de disponibilité des machines virtuelles de 99,95 %.
   - **Disque Ultra avec stockage localement redondant (LRS)**  : [zone de disponibilité](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address), mais les machines virtuelles doivent être placées dans la même zone de disponibilité. Les [disques Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md) offrent une latence de disque inférieure et conviennent aux charges de travail gourmandes en E/S. Toutes les machines virtuelles de l’instance FCI se trouvant dans la même zone de disponibilité, la disponibilité des machines virtuelles est de 99,9 %. 
- **Partages de fichiers Premium** : [groupe à haute disponibilité](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) ou [zone de disponibilité](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **Espaces de stockage direct** : [groupe à haute disponibilité](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> Vous ne pouvez pas définir ou modifier le groupe à haute disponibilité après avoir créé une machine virtuelle.

## <a name="subnets"></a>Sous-réseaux 

Pour SQL Server sur les machines virtuelles Azure, vous avez la possibilité de déployer vos machines virtuelles SQL Server sur un sous-réseau unique ou sur plusieurs sous-réseaux. 

Le déploiement de vos machines virtuelles sur plusieurs sous-réseaux s’appuie sur le cluster ou la dépendance des adresses IP et correspond à l’expérience locale lors d’une connexion à votre instance de cluster de basculement. L’approche à plusieurs sous-réseaux est recommandée pour SQL Server sur les machines virtuelles Azure afin de faciliter la gestion et d’accélérer les temps de basculement. 

Le déploiement de vos machines virtuelles sur un sous-réseau unique requiert une dépendance supplémentaire sur un équilibreur de charge Azure Load Balancer ou un nom de réseau distribué (DNN) afin d’acheminer le trafic vers votre instance FCI. 

Si vous déployez vos machines virtuelles SQL Server sur plusieurs sous-réseaux, suivez la procédure de cette section pour créer vos réseaux virtuels avec des sous-réseaux supplémentaires puis, une fois les machines virtuelles SQL Server créées, [attribuez des adresses IP secondaires](#assign-secondary-ip-addresses) aux machines virtuelles au sein de ces sous-réseaux. Le déploiement de vos machines virtuelles SQL Server sur un sous-réseau unique ne requiert aucune configuration réseau supplémentaire. 

# <a name="single-subnet"></a>[Sous-réseau unique](#tab/single-subnet)

Placez les deux machines virtuelles dans un sous-réseau unique doté de suffisamment d’adresses IP pour ces machines virtuelles et toutes les instances FCI que vous pourriez souhaiter installer sur le cluster. Cette approche requiert un composant supplémentaire afin d’acheminer les connexions vers votre instance FCI, par exemple un équilibreur de charge Azure Load Balancer ou un nom de réseau distribué (DNN). 

Si vous choisissez de déployer vos machines virtuelles SQL Server sur un sous-réseau unique, [passez en revue les différences entre les options de connectivité Azure Load Balancer et DNN](hadr-windows-server-failover-cluster-overview.md#distributed-network-name-dnn) et choisissez l’option qui vous convient le mieux avant de préparer le reste de votre environnement pour votre instance FCI.

Le déploiement de vos machines virtuelles SQL Server sur un sous-réseau unique ne requiert aucune configuration réseau supplémentaire. 

# <a name="multi-subnet"></a>[Plusieurs sous-réseaux](#tab/multi-subnet)

Si vous souhaitez acheminer des connexions directement vers votre instance FCI SQL Server, placez les deux machines virtuelles dans des sous-réseaux distincts au sein d’un réseau virtuel. Attribuez une adresse IP secondaire à la machine virtuelle SQL Server pour l’instance de cluster de basculement. Si vous utilisez Windows Server 2016 et versions antérieures, attribuez également une adresse IP secondaire supplémentaire pour le cluster de basculement Windows Server. Windows Server 2019 et versions ultérieures utilisent un nom de réseau distribué (DNN) pour le nom du cluster ; aucune adresse IP secondaire n’est donc requise pour le cluster. 

Cette approche élimine le besoin de recourir à un équilibreur de charge Azure Load Balancer ou un nom de réseau distribué (DNN) lors de la connexion à votre instance FCI SQL Server. 

Si vous choisissez de déployer vos machines virtuelles SQL Server sur plusieurs sous-réseaux, vous devez d’abord créer le réseau virtuel avec deux sous-réseaux supplémentaires, et une fois vos machines virtuelles SQL Server créées, [leur attribuer des adresses IP secondaires](#assign-secondary-ip-addresses).  Pour en savoir plus, consultez [Vue d’ensemble du réseau virtuel](../../../virtual-network/virtual-networks-overview.md). Les noms de sous-réseaux et les adresses IP de cette section sont fournis à titre d’exemple uniquement et peuvent varier au sein de votre environnement. Pour créer le réseau virtuel dans le portail Azure, procédez comme suit :

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez **+ Créer**

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="Créer une ressource dans votre groupe de ressources":::

1. Recherchez **réseau virtuel** dans la zone de recherche de la **Place de marché** et choisissez la vignette **Réseau virtuel** de Microsoft. Sélectionnez **Créer** dans la page **Réseau virtuel**.  
1. Dans la page **Créer un réseau virtuel**, entrez les informations suivantes sous l’onglet **Informations de base** : 
   1. Sous **Détails du projet**, choisissez l’**Abonnement** Azure approprié et le **Groupe de ressources** dans lequel vous envisagez de déployer vos machines virtuelles SDL Server. 
   1. Sous **Détails de l’instance**, fournissez un nom pour votre réseau virtuel, puis choisissez la même région que celle de votre groupe de ressources dans la liste déroulante.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/03-create-vnet-basics.png" alt-text="Choisissez le groupe de ressources que vous avez créé précédemment, puis fournissez un nom pour votre réseau virtuel":::

1. Sous l’onglet **Adresse IP**, sélectionnez **+ Ajouter un sous-réseau** pour ajouter un sous-réseau supplémentaire pour votre première machine virtuelle SQL Server et renseignez les valeurs suivantes : 
   1. Fournissez une valeur pour le **Nom du sous-réseau**, par exemple, **SQL-subnet-1**. 
   1. Spécifiez une plage d’adresses de sous-réseau unique dans l’espace d’adressage du réseau virtuel. Par exemple, vous pouvez ajouter 1 au troisième octet de la plage d’adresses DC-subnet. 
      - Par exemple, si votre plage **par défaut** est *10.38.0.0/24*, entrez la plage d’adresses IP `10.38.1.0/24` pour **SQL-subnet-1**. 
      - De même, si votre plage IP **par défaut** est *10.5.0.0/24*, entrez `10.5.1.0/24` pour le nouveau sous-réseau. 
   1. Sélectionnez **Ajouter** pour ajouter votre nouveau sous-réseau. 

     :::image type="content" source="./media/failover-cluster-instance-prepare-vm/05-create-vnet-ip-address-add-sql-subnet-1.png" alt-text="Nommez votre premier sous-réseau, par exemple, sql-subnet-1, puis ajoutez 1 au troisième octet, de sorte que si l’adresse IP de DC-subnet est 10.5.0.0, votre nouveau sous-réseau est 10.5.1.0":::

1. Répétez l’étape précédente pour ajouter une autre plage de sous-réseau unique pour votre deuxième machine virtuelle SQL Server, que vous nommez, par exemple, **SQL-subnet-2**. Vous pouvez également ajouter 1 au troisième octet. 
   - Par exemple, si votre plage IP **par défaut** est *10.38.0.0/24* et que votre **SQL-subnet-1** est *10.38.1.0/24*, entrez `10.38.2.0/24` pour le nouveau sous-réseau
   - De même, si votre plage IP **par défaut** est *10.5.0.0/24* et que votre **SQL-subnet-1** est *10.5.1.0/24*, entrez la plage d’adresses IP `10.5.2.0/24` pour **SQL-subnet-2**. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/06-create-vnet-ip-address-add-sql-subnet-2.png" alt-text="Nommez votre deuxième sous-réseau, par exemple, sql-subnet-2, puis ajoutez 2 au troisième octet, de sorte que si l’adresse IP de DC-subnet est 10.38.0.0/24, votre nouveau sous-réseau est 10.38.2.0/24":::

1. Après avoir ajouté le deuxième sous-réseau, vérifiez les noms et les plages de vos sous-réseaux (vos plages d’adresses IP peuvent différer de l’image). Si tout semble correct, sélectionnez **Vérifier + créer**, puis **Créer** pour créer votre réseau virtuel. 

   :::image type="content" source="./media/failover-cluster-instance-prepare-vm/07-create-vnet-ip-address.png" alt-text="Une fois que vous avez ajouté le deuxième sous-réseau, vérifiez les noms et les plages de votre sous-réseau, comme dans l’exemple d’image (vos adresses IP peuvent être différentes). Si tout semble correct, sélectionnez Vérifier + créer, puis Créer pour créer votre réseau virtuel.":::

   Vous revenez au tableau de bord du portail et Azure vous avertit lorsque le réseau est créé.

---

## <a name="configure-dns"></a>Configurer DNS

Configurez votre réseau virtuel pour qu’il utilise votre serveur DNS. Tout d’abord, identifiez l’adresse IP DNS, puis ajoutez-la à votre réseau virtuel. 

### <a name="identify-dns-ip-address"></a>Identifier l’adresse IP DNS

Identifiez l’adresse IP du serveur DNS, puis ajoutez-la à la configuration du réseau virtuel. Cette section montre comment identifier l’adresse IP DNS si le serveur DNS se trouve sur une machine virtuelle dans Azure. 

Pour identifier l’adresse IP de la machine virtuelle du serveur DNS dans le portail Azure, procédez comme suit : 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez la machine virtuelle du serveur DNS. 
1. Dans la page de la machine virtuelle, choisissez **Réseau** sous le volet **Paramètres**. 
1. Notez l’adresse **IP privée de carte réseau**, car il s’agit de l’adresse IP du serveur DNS. Dans l’exemple d’image, l’adresse IP privée est **10.38.0.4**. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-dc-vm-1-private-ip.png" alt-text="Dans la page DC-VM-1, choisissez Réseau sous le volet Paramètres, puis notez l’adresse IP privée de la carte réseau. Utilisez cette adresse IP comme serveur DNS. ":::

### <a name="configure-virtual-network-dns"></a>Configurer le DNS du réseau virtuel

Configurez le réseau virtuel pour qu’il utilise l’adresse IP du serveur DNS. 

Pour configurer votre réseau virtuel pour le DNS, procédez comme suit : 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez votre réseau virtuel. 
1. Sélectionnez **Serveurs DNS** dans le volet **Paramètres**, puis **Personnalisé**. 
1. Entrez l’adresse IP privée que vous avez identifiée précédemment dans le champ **Adresse IP**, par exemple `10.38.0.4`, ou fournissez l’adresse IP interne de votre serveur DNS interne. 
1. Sélectionnez **Enregistrer**. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-identify-dns-ip-address.png" alt-text=" Sélectionnez Serveurs DNS dans le volet Paramètres, puis Personnalisé. Entrez l’adresse IP privée que vous avez identifiée précédemment, 10.38.0.4, dans le champ Adresse IP. ":::

## <a name="create-the-virtual-machines"></a>Créer les machines virtuelles

Après avoir configuré le réseau virtuel de vos machines virtuelles ainsi que la disponibilité de ces dernières, vous êtes prêt à créer vos machines virtuelles. Vous pouvez choisir d’utiliser une image de la place de marché Azure sur laquelle SQL Server est déjà installé ou non. Toutefois, si vous choisissez une image pour SQL Server sur des machines virtuelles Azure, vous devez désinstaller SQL Server à partir de la machine virtuelle avant de configurer l’instance de cluster de basculement. 

### <a name="nic-considerations"></a>Considérations relatives à la carte réseau

Sur un cluster de basculement invité de machine virtuelle Azure, nous recommandons une seule carte réseau par serveur (nœud de cluster). Les réseaux Azure intègrent une redondance physique, ce qui rend inutile les cartes réseau supplémentaires sur un cluster invité de machine virtuelle IaaS Azure. Même si le rapport de validation de cluster émet un avertissement stipulant que les nœuds sont uniquement accessibles sur un seul réseau, vous pouvez ignorer ce dernier en toute sécurité sur les clusters de basculement invités de machine virtuelle IaaS Azure.

Placez les deux machines virtuelles :

- Dans le même groupe de ressources Azure que celui où se trouve le groupe à haute disponibilité si vous utilisez des groupes à haute disponibilité.
- Sur le même réseau virtuel que votre contrôleur de domaine et votre serveur DNS ou sur un réseau virtuel disposant d’une connectivité appropriée à votre contrôleur de domaine.
- Dans le groupe à haute disponibilité ou la zone de disponibilité Azure.

Vous pouvez créer une machine virtuelle Azure à l’aide d’une image [avec](sql-vm-create-portal-quickstart.md) ou [sans que ](../../../virtual-machines/windows/quick-create-portal.md) SQL Server soit préinstallé. Si vous choisissez l’image SQL Server, vous devez désinstaller manuellement l’instance SQL Server avant d’installer l’instance de cluster de basculement.  

### <a name="assign-secondary-ip-addresses"></a>Attribuer des adresses IP secondaires

Si vous avez déployé vos machines virtuelles SQL Server sur un sous-réseau unique, ignorez cette étape. Si vous avez déployé vos machines virtuelles SQL Server sur plusieurs sous-réseaux pour une meilleure connectivité à votre instance FCI, vous devez attribuer les adresses IP secondaires à chaque machine virtuelle. 

Attribuez des adresses IP secondaires à chaque machine virtuelle SQL Server à utiliser pour le nom de réseau de l’instance de cluster de basculement, et pour Windows Server 2016 et versions antérieures, attribuez également des adresses IP secondaires à chaque machine virtuelle SQL Server pour les utiliser comme nom de réseau de cluster. Cela vous évite de devoir utiliser équilibreur de charge Azure Load Balancer, comme c’est le cas dans un environnement avec un seul sous-réseau.  

Sur Windows Server 2016 et versions antérieures, vous devez attribuer une adresse IP secondaire supplémentaire à chaque machine virtuelle SQL Server pour l’utiliser comme adresse IP du cluster Windows, car le cluster utilise le **Nom réseau du cluster** au lieu du Nom de réseau distribué (DNN) par défaut introduit dans Windows Server 2019. Avec un DNN, l’objet nom de cluster (CNO) est automatiquement inscrit aux adresses IP de tous les nœuds du cluster, ce qui évite d’utiliser une adresse IP de cluster Windows dédiée.

Si vous utilisez Windows Server 2016 et versions antérieures, suivez les étapes de cette section pour attribuer une adresse IP secondaire à chaque machine virtuelle SQL Server pour le nom réseau de l’instance FCI *et* le cluster, *à la fois*. 

Si vous utilisez Windows Server 2019 ou version ultérieure, attribuez seulement une adresse IP secondaire pour le nom réseau de l’instance FCI et ignorez les étapes qui attribuent une IP de cluster Windows, sauf si vous envisagez de configurer votre cluster avec un nom de réseau virtuel (VNN), auquel cas attribuez les deux adresses IP à chaque machine virtuelle SQL Server comme vous le feriez dans Windows Server 2016. 

Pour attribuer des IP secondaires supplémentaires aux machines virtuelles, procédez comme suit : 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com/) et sélectionnez la première machine virtuelle SQL Server. 
1. Sélectionnez **Réseau** dans le volet **Paramètres**, puis l’**Interface réseau** : 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/19-sql-vm-network-interface.png" alt-text="Sélectionnez Réseau dans le volet Paramètres, puis l’Interface réseau":::

1. Dans la page **Interface réseau**, sélectionnez **Configurations IP** dans le volet **Paramètres**, puis choisissez **+ Ajouter** pour ajouter une adresse IP supplémentaire : 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/20-ip-configurations-add.png" alt-text="Configurations IP":::

1. Dans la page **Ajouter une configuration IP**, procédez comme suit : 
   1. Spécifiez le **Nom** de l’adresse IP du cluster Windows, par exemple, **windows-cluster-ip** pour Windows 2016 et versions antérieures. Ignorez cette étape si vous êtes sur Windows Server 2019 ou version ultérieure. 
   1. Définissez l’**Allocation** sur **Statique**.
   1. Entrez une **Adresse IP** inutilisée du même sous-réseau (**SQL-subnet-1**) que celui de la machine virtuelle SQL Server, par exemple, `10.38.1.10`. 
   1. Laissez la valeur par défaut **Dissocier** pour l’**Adresse IP publique**. 
   1. Sélectionnez **OK** pour terminer l’ajout de la configuration IP. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/21-add-ip-windows-cluster.png" alt-text="Ajoutez l’IP du cluster en entrant une adresse IP inutilisée du sous-réseau de la première machine virtuelle SQL Server":::

1. Resélectionnez **+ Ajouter** pour configurer une adresse IP supplémentaire pour le nom réseau de l’instance FCI (avec un nom tel que **FCI-network-name**), et spécifiez une adresse IP inutilisée dans **SQL-subnet-1**, par exemple, `10.38.1.11` : 

   :::image type="content" source="./media/failover-cluster-instance-prepare-vm/22-add-fci-ip-address.png" alt-text="Resélectionnez + Ajouter pour configurer une adresse IP supplémentaire pour l’écouteur du groupe de disponibilité (avec un nom de type availability-group-listener), et spécifiez une adresse IP inutilisée dans SQL-subnet-1, par exemple, 10.31.1.11":::

1. Répétez ces étapes pour la deuxième machine virtuelle SQL Server. Attribuez deux adresses IP secondaires inutilisées dans **SQL-subnet-2**. Utilisez les valeurs du tableau suivant pour ajouter la configuration IP (les adresses IP sont de simples exemples et les vôtres peuvent varier) : 

   
    | **Champ** | Entrée | Entrée | 
    | --- | --- | --- |
    | **Nom** |windows-cluster-ip | FCI-network-name |
    | **Allocation** | statique | statique |
    | **Adresse IP** | 10.38.2.10 | 10.38.2.11 | 
    |  |  |  |



## <a name="uninstall-sql-server"></a>Désinstaller SQL Server

Dans le cadre du processus de création de l’instance FCI, vous allez installer SQL Server en tant qu’instance en cluster dans le cluster de basculement. *Si vous avez déployé une machine virtuelle avec une image de la place de marché Azure sans SQL Server, vous pouvez sauter cette étape.* Si vous avez déployé une image avec SQL Server préinstallé, vous devez désinscrire la machine virtuelle SQL Server de l’extension SQL IaaS Agent, puis désinstaller SQL Server. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Désinscrire de l’extension SQL IaaS Agent

Les images de machine virtuelle SQL Server qui proviennent de Place de marché Azure sont automatiquement inscrites auprès de l’extension SQL IaaS Agent. Avant de désinstaller l’instance SQL préinstallée, vous devez d’abord [désinscrire chaque machine virtuelle SQL Server de l’extension SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

### <a name="uninstall-sql-server"></a>Désinstaller SQL Server

Une fois que vous avez désinscrit SQL Server de l’extension, vous pouvez le désinstaller. Procédez comme suit sur chaque machine virtuelle : 

1. Connectez-vous à la machine virtuelle à l’aide du protocole RDP (Remote Desktop Protocol). Lors de votre première connexion à une machine virtuelle avec le protocole RDP, une invite vous demande si vous souhaitez autoriser que cet ordinateur soit détecté sur le réseau. Sélectionnez **Oui**.
1. Ouvrez **Programmes et fonctionnalités** dans le **Panneau de configuration**. 
1. Dans **Programmes et fonctionnalités**, cliquez avec le bouton droit sur **Microsoft SQL Server 201_ (64 bits)** et sélectionnez **Désinstaller/Modifier**.
1. Sélectionnez **Supprimer**.
1. Sélectionnez l’instance par défaut.
1. Supprimez toutes les fonctionnalités sous **Services Moteur de base de données**, **Analysis Services** et **Reporting Services - Natif**. Ne supprimez rien sous **Fonctionnalités partagées**. Vous devez voir quelque chose de similaire à la capture d’écran suivante : ![Sélectionner les fonctionnalités](./media/failover-cluster-instance-prepare-vm/remove-features-updated.png)
1. Sélectionnez **Suivant**, puis **Supprimer**.
1. Une fois l’instance supprimée, redémarrez la machine virtuelle. 

## <a name="open-the-firewall"></a>Ouvrir le pare-feu 

Sur chaque machine virtuelle, ouvrez le port TCP du pare-feu Windows utilisé par SQL Server. Par défaut SQL Server utilise le port 1433, mais si vous l’avez modifié dans votre environnement, ouvrez le port que vous avez configuré pour utiliser votre instance SQL Server. Le port 1433 s’ouvre automatiquement sur les images SQL Server déployées à partir de la Place de marché Azure. 

Si vous utilisez un [équilibreur de charge](failover-cluster-instance-vnn-azure-load-balancer-configure.md) pour le scénario à sous-réseau unique, vous devez également ouvrir le port utilisé par la sonde d’intégrité. Par défaut, la sonde d’intégrité utilise le port 59999, mais il peut s’agir de n’importe quel port TCP que vous spécifiez lorsque vous créez l’équilibreur de charge. 

Ce tableau détaille les ports que vous devrez peut-être ouvrir, en fonction de la configuration de votre FCI : 

   | Objectif | Port | Notes
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Port normal pour les instances par défaut de SQL Server. Si vous avez utilisé une image de la galerie, ce port s’ouvre automatiquement. </br> </br> **Utilisé par** : toutes les configurations de l’instance FCI. |
   | Sonde d’intégrité | TCP 59999 | Tout port TCP ouvert. Configurez la [sonde d’intégrité](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) de l’équilibreur de charge et le cluster pour qu’ils utilisent ce port. </br> </br> **Utilisé par** : FCI avec équilibreur de charge dans un scénario à sous-réseau unique. |
   | Partage de fichiers | UDP 445 | Port utilisé par le service de partage de fichiers. </br> </br> **Utilisé par** : Instance FCI avec partage de fichiers Premium. |

## <a name="join-the-domain"></a>Joindre le domaine

Vous devez également joindre vos machines virtuelles au domaine. Pour ce faire, vous pouvez utiliser un [modèle de démarrage rapide](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain). 

## <a name="review-storage-configuration"></a>Passer en revue la configuration de stockage

Les machines virtuelles créées à partir de la place de marché Azure sont dotées d’un stockage attaché. Si vous envisagez de configurer votre stockage FCI à l’aide de partages de fichiers Premium ou de disques partagés Azure, vous pouvez supprimer le stockage attaché pour faire des économies, car le stockage local n’est pas utilisé pour l’instance de cluster de basculement. Toutefois, il est possible d’utiliser le stockage attaché pour les solutions FCI avec espaces de stockage direct. Par conséquent, sa suppression peut ne pas être utile dans ce cas. Passez en revue votre solution de stockage FCI pour déterminer si la suppression du stockage attaché est optimale pour faire des économies. 


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez préparé votre environnement de machine virtuelle, vous êtes prêt à configurer votre instance de cluster de basculement. 

Choisissez l’un des guides suivants pour configurer l’environnement FCI adapté à votre entreprise : 
- [Configurer FCI avec des disques partagés Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Configurer FCI avec un partage de fichiers Premium](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Configurer FCI avec des espaces de stockage direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md)


Pour en savoir plus, consultez :

- [Cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure](hadr-windows-server-failover-cluster-overview.md)
- [Instances de cluster de basculement avec SQL Server sur des machines virtuelles Azure](failover-cluster-instance-overview.md)
- [Vue d’ensemble des instances de cluster de basculement](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Paramètres HADR pour SQL Server sur les machines virtuelles Azure](hadr-cluster-best-practices.md)
