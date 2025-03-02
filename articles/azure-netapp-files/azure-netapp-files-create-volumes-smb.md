---
title: Créer un volume SMB pour Azure NetApp Files | Microsoft Docs
description: Cet article explique comment créer un volume SMB3 dans Azure NetApp Files. Découvrez la configuration requise pour les services de domaine et connexions Active Directory.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: 738e3d6cb3b27180cd8337e09a72fbe3a0639edf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240050"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Créer un volume SMB pour Azure NetApp Files

Azure NetApp Files prend en charge la création de volumes avec NFS (NFSv3 ou NFSv4.1), SMB3 ou le double protocole (NFSv3 et SMB ou NFSv4.1 et SMB). La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool. 

Cet article explique comment créer un volume SMB3. Pour les volumes NFS, voir [Créer un volume NFS](azure-netapp-files-create-volumes.md). Pour les volumes à deux protocoles, consultez [Créer un volume à deux protocoles](create-volumes-dual-protocol.md).

## <a name="before-you-begin"></a>Avant de commencer 

* Vous devez déjà avoir configuré un pool de capacité. Consultez [Créer un pool de capacités](azure-netapp-files-set-up-capacity-pool.md).     
* Un sous-réseau doit être délégué à Azure NetApp Files. Consultez [Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Configurer des connexions Azure Active Directory 

Avant de créer un volume SMB, vous devez créer une connexion Active Directory. Si vous n’avez pas configuré de connexions Active Directory pour des fichiers Azure NetApp, suivez les instructions décrites dans [Créer et gérer des connexions Active Directory](create-active-directory-connections.md).

## <a name="add-an-smb-volume"></a>Ajouter un volume SMB

1. Cliquez sur le panneau **Volumes** à partir du panneau Pools de capacités. 

    ![Accédez à Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    La fenêtre Créer un volume s’affiche.

3. Dans la fenêtre Créer un volume, cliquez sur **Créer** et renseignez les champs suivants sous l’onglet De base :   
    * **Nom du volume**      
        Spécifiez le nom du volume que vous créez.   

        Un nom de volume doit être unique au sein de chaque pool de capacité. Il doit comprendre au moins trois caractères. Le nom doit commencer par une lettre. Peut contenir des lettres, des chiffres, des traits de soulignement (_) et des traits d’union (-) uniquement. 

        Vous ne pouvez pas utiliser `default` ni `bin` comme nom de volume.

    * **Pool de capacités**  
        Spécifiez le pool de capacité dans lequel vous souhaitez que le volume soit créé.

    * **Quota**  
        Spécifiez la quantité de stockage logique allouée au volume.  

        Le champ **Quota disponible** indique la quantité d’espace inutilisé dans le pool de capacités choisi, que vous pouvez utiliser pour créer un volume. La taille du nouveau volume ne doit pas dépasser le quota disponible.  

    * **Débit (Mio/s)**    
        Si le volume est créé dans un pool de capacité avec Qualité de service manuelle, spécifiez le débit souhaité pour le volume.   

        Si le volume est créé dans un pool de capacité avec Qualité de service automatique, la valeur affichée dans ce champ est (quota x débit du niveau de service).   

    * **Réseau virtuel**  
        Spécifiez le réseau virtuel Azure (VNet) à partir duquel vous voulez accéder au volume.  

        Le réseau virtuel que vous spécifiez doit avoir un sous-réseau délégué à Azure NetApp Files. Le service Azure NetApp Files est accessible seulement à partir du même réseau virtuel ou d’un réseau virtuel qui figure dans la même région que le volume via le peering de réseau virtuel. Vous pouvez également accéder au volume à partir de votre réseau local via Express Route.   

    * **Sous-réseau**  
        Spécifiez le sous-réseau que vous souhaitez utiliser pour le volume.  
        Le sous-réseau que vous spécifiez doit être délégué à Azure NetApp Files. 
        
        Si vous n’avez pas délégué un sous-réseau, vous pouvez cliquer sur **Créer** dans la page Créer un volume. Ensuite, dans la page Créer un sous-réseau, fournissez les informations sur le sous-réseau, puis sélectionnez **Microsoft.NetApp/volumes** pour déléguer le sous-réseau à Azure NetApp Files. Dans chaque réseau virtuel, un seul sous-réseau peut être délégué à Azure NetApp Files.   
 
        ![Créer un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Créer un sous-réseau](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * **Fonctionnalités réseau**  
        Dans les régions prises en charge, vous pouvez préciser si vous souhaitez utiliser les fonctionnalités réseau **De base** ou **Standard** pour le volume. Consultez [Configurer les fonctionnalités réseau d’un volume](configure-network-features.md) et [Consignes pour planifier un réseau Azure NetApp Files](azure-netapp-files-network-topologies.md) pour plus de détails.

    * Si vous souhaitez appliquer une stratégie d’instantané existante au volume, cliquez sur **Afficher la section avancée** pour la développer, indiquez si vous souhaitez masquer le chemin d'accès de l’instantané, puis sélectionnez une stratégie d’instantané dans le menu déroulant. 

        Pour plus d’informations sur la création d’une stratégie d’instantané, consultez [Gérer les stratégies d’instantané](snapshots-manage-policy.md).

        ![Afficher la sélection avancée](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Cliquez sur **Protocole** et renseignez les informations suivantes :  
    * Sélectionnez **SMB** comme type de protocole pour le volume.  

    * Sélectionnez votre connexion **Active Directory** dans la liste déroulante.  
    
    * Spécifiez un **nom de partage** unique pour le volume. Ce nom de partage est utilisé lorsque vous créez des cibles de montage. Les exigences relatives à ce nom de partage sont les suivantes :   
        - Il doit être unique au sein de chaque sous-réseau de la région. 
        - Il doit commencer par un caractère alphabétique.
        - Il doit contenir uniquement des lettres, des chiffres ou des tirets (`-`). 
        - La longueur totale ne doit pas dépasser 80 caractères.   
        
    * <a name="smb3-encryption"></a>Si vous souhaitez activer le chiffrement pour SMB3, sélectionnez **Activer le chiffrement de protocole SMB3**.   
        Cette fonctionnalité active le chiffrement pour les données SMB3 à la volée. Les clients SMB qui n’utilisent pas le chiffrement SMB3 ne seront pas en mesure d’accéder à ce volume.  Les données au repos sont chiffrées, indépendamment de ce paramètre.  
        Pour plus d’informations, consultez [Chiffrement SMB](azure-netapp-files-smb-performance.md#smb-encryption). 

        La fonctionnalité **Chiffrement de protocole SMB3** est actuellement en préversion. Si vous utilisez cette fonctionnalité pour la première fois, inscrivez-la avant de l’utiliser : 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Vérifiez l’état d’inscription de la fonctionnalité : 

        > [!NOTE]
        > **RegistrationState** peut être à l’état `Registering` pendant plusieurs minutes, et jusqu’à 60 minutes, avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit `Registered`.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        Vous pouvez également utiliser les [commandes Azure CLI](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` et `az feature show` pour inscrire la fonctionnalité et afficher l’état de l’inscription.  
    * <a name="continuous-availability"></a>Si vous souhaitez activer la disponibilité continue pour le volume SMB, sélectionnez **Activer la disponibilité continue**.    

        > [!IMPORTANT]   
        > La fonctionnalité de disponibilité continue SMB est actuellement en préversion publique. Vous devez soumettre une demande d’inscription à la liste d’attente pour accéder à la fonctionnalité via la **[page de soumission de demande d’inscription à la liste d’attente de préversion publique de partages de disponibilité continue SMB d’Azure NetApp Files](https://aka.ms/anfsmbcasharespreviewsignup)** . Attendez de recevoir un e-mail de confirmation officiel de l’équipe Azure NetApp Files avant d’utiliser la fonctionnalité de disponibilité continue.   
        > 
        Vous devez activer la disponibilité continue uniquement pour les [conteneurs de profil utilisateur FSLogix](../virtual-desktop/create-fslogix-profile-container.md) et SQL Server. L’utilisation de partages de disponibilité continue SMB pour des charges de travail autres que les conteneurs de profil utilisateur FSLogix et SQL Server n’est *pas* prise en charge. Cette fonctionnalité est actuellement prise en charge sur Windows SQL Server. Linux SQL Server n’est pas pris en charge actuellement. Si vous utilisez un compte non-administrateur (domaine) pour installer SQL Server, assurez-vous que le compte dispose du privilège de sécurité requis. Si le compte de domaine ne dispose pas du privilège de sécurité requis (`SeSecurityPrivilege`) et que le privilège ne peut pas être défini au niveau du domaine, vous pouvez octroyer le privilège au compte à l’aide du champ **Utilisateurs avec privilège de sécurité** des connexions Active Directory. Consultez [Créer une connexion Active Directory](create-active-directory-connections.md#create-an-active-directory-connection).

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![Capture d’écran qui présente l’onglet Protocole de la création d’un volume SMB.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Cliquez sur **Vérifier et créer** pour passer en revue les informations du volume.  Cliquez ensuite sur **Créer** pour créer le volume SMB.

    Le volume créé s’affiche dans la page Volumes. 
 
    Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="control-access-to-an-smb-volume"></a>Contrôler l’accès à un volume SMB  

L’accès à un volume SMB est géré par le biais d’autorisations. 

### <a name="ntfs-file-and-folder-permissions"></a>Autorisations de fichier et de dossier NTFS  

Vous pouvez définir des autorisations pour un fichier ou un dossier à l’aide de l’onglet **Sécurité** des propriétés de l’objet dans le client SMB Windows.
 
![Définit des autorisations de fichier et de dossier](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Étapes suivantes  

* [Monter ou démonter un volume pour des machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configurer ADDS LDAP sur TLS pour Azure NetApp Files](configure-ldap-over-tls.md) 
* [Activer la disponibilité continue sur des volumes SMB existants](enable-continuous-availability-existing-SMB.md)
* [Chiffrement SMB](azure-netapp-files-smb-performance.md#smb-encryption)
* [Résoudre les erreurs liées au volume pour Azure NetApp Files](troubleshoot-volumes.md)
* [En savoir plus sur l’intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Installation d’une nouvelle forêt Active Directory à l’aide d’Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
