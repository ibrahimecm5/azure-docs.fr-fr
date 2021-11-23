---
title: 'Tutoriel : Prérequis pour configurer un groupe de disponibilité dans plusieurs sous-réseaux'
description: 'Ce tutoriel montre comment configurer les prérequis pour la création d’un groupe de disponibilité Always On dans plusieurs sous-réseaux sur des machines virtuelles Azure. '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 4d1ec223a745f49518a7936bc85d40f470739c80
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159565"
---
# <a name="tutorial-prerequisites-for-availability-groups-in-multiple-subnets-sql-server-on-azure-vms"></a>Tutoriel : Prérequis pour configurer des groupes de disponibilité dans plusieurs sous-réseaux (SQL Server sur des machines virtuelles Azure) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Pour éviter d’utiliser un équilibreur de charge Azure ou un nom de réseau distribué (DNN) pour votre groupe de disponibilité Always On, créez vos machines virtuelles SQL Server dans plusieurs sous-réseaux au sein du même réseau virtuel Azure.

Dans ce tutoriel, vous configurez les prérequis afin de créer un [groupe de disponibilité Always On pour SQL Server sur des machines virtuelles Azure dans plusieurs sous-réseaux](availability-group-manually-configure-tutorial-multi-subnet.md). À la fin de ce tutoriel, vous avez un contrôleur de domaine sur deux machines virtuelles Azure, deux machines virtuelles SQL Server dans plusieurs sous-réseaux et un compte de stockage dans un seul groupe de ressources. 

**Durée estimée** : ce tutoriel crée plusieurs ressources dans Azure et peut prendre jusqu’à 30 minutes. 

Le diagramme suivant illustre les ressources que vous déployez dans ce tutoriel : 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/multi-subnet-availability-group-diagram.png" alt-text="Le diagramme suivant illustre les ressources que vous déployez dans ce tutoriel":::


## <a name="prerequisites"></a>Prérequis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

- Un abonnement Azure. Vous pouvez [ouvrir un compte Azure gratuit](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic) ou [activer les avantages de l’abonnement à Visual Studio](/visualstudio/subscriptions/subscriber-benefits).
- Être familiarisé avec les [Groupes de disponibilité Always On dans SQL Server](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) et connaître les notions de base. 


## <a name="create-resource-group"></a>Créer un groupe de ressources

Pour créer le groupe de ressources dans le portail Azure, suivez ces étapes : 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **+ Créer une ressource** pour créer une ressource dans le portail.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-portal-plus.png" alt-text="Sélectionnez +Créer une ressource pour créer une ressource dans le portail.":::

1. Recherchez **groupe de ressources** dans la zone de recherche de la **Place de marché** et choisissez la vignette **Groupe de ressources** de Microsoft. Sélectionnez **Créer** dans la page **Groupe de ressources**. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-resource-group-search.png" alt-text="Recherchez groupe de ressources dans la Place de marché, puis choisissez de créer le groupe de ressources. ":::

1. Dans la page **Créer un groupe de ressources**, renseignez les valeurs pour créer le groupe de ressources :
   1. Choisissez l’abonnement Azure approprié dans la liste déroulante. 
   1. Fournissez un nom pour votre groupe de ressources, par exemple, **SQL-HA-RG**.
   1. Choisissez une région dans la liste déroulante, par exemple, **USA Ouest 2**. Veillez à déployer toutes les ressources suivantes sur cette localisation également. 
   1. Sélectionnez **Vérifier + créer** pour passer en revue vos paramètres de ressource, puis sélectionnez **Créer** pour créer votre groupe de ressources.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-resource-group-create-complete.png" alt-text="Renseignez les valeurs pour créer votre groupe de ressources dans le portail Azure. ":::


## <a name="create-network-and-subnets"></a>Créer un réseau et des sous-réseaux

Ensuite, créez le réseau virtuel et trois sous-réseaux. Pour en savoir plus, consultez [Vue d’ensemble du réseau virtuel](../../../virtual-network/virtual-networks-overview.md). 

Pour créer le réseau virtuel dans le portail Azure, suivez ces étapes :

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez **+ Créer**

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="Créer une ressource dans votre groupe de ressources":::

1. Recherchez **réseau virtuel** dans la zone de recherche de la **Place de marché** et choisissez la vignette **Réseau virtuel** de Microsoft. Sélectionnez **Créer** dans la page **Réseau virtuel**.  
1. Dans la page **Créer un réseau virtuel**, entrez les informations suivantes sous l’onglet **Informations de base** : 
   1. Sous **Détails du projet**, choisissez l’**Abonnement** Azure approprié et le **Groupe de ressources** que vous avez créé précédemment, **SQL-HA-RG**. 
   1. Sous **Détails de l’instance**, fournissez un nom pour votre réseau virtuel, par exemple **SQLHAVNET**, puis choisissez la même région que celle de votre groupe de ressources dans la liste déroulante.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/03-create-vnet-basics.png" alt-text="Choisissez le groupe de ressources que vous avez créé précédemment, puis fournissez un nom pour votre réseau virtuel, par exemple, SQLHAVNET":::

1. Sous l’onglet **Adresses IP**, sélectionnez le sous-réseau **par défaut** pour ouvrir la page **Modifier le sous-réseau**. Remplacez le nom du sous-réseau du contrôleur de domaine par **DC-subnet**. Sélectionnez **Enregistrer**.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/04-create-vnet-ip-address-rename-default-subnet.png" alt-text="Sous l’onglet Adresses IP, sélectionnez le sous-réseau par défaut pour ouvrir la page Modifier le sous-réseau. Remplacez le nom du sous-réseau du contrôleur de domaine par DC-subnet. Sélectionnez Enregistrer":::

1. Sélectionnez **+ Ajouter un sous-réseau** pour ajouter un sous-réseau supplémentaire pour votre première machine virtuelle SQL Server et renseignez les valeurs suivantes : 
   1. Fournissez une valeur pour le **Nom du sous-réseau**, par exemple, **SQL-subnet-1**. 
   1. Spécifiez une plage d’adresses de sous-réseau unique dans l’espace d’adressage du réseau virtuel. Par exemple, vous pouvez ajouter 1 au troisième octet de la plage d’adresses DC-subnet. 
      - Par exemple, si votre plage **DC-subnet** est *10.38.0.0/24*, entrez la plage d’adresses IP `10.38.1.0/24` pour **SQL-subnet-1**. 
      - De même, si votre plage IP **DC-subnet** est *10.5.0.0/24*, entrez `10.5.1.0/24` pour le nouveau sous-réseau. 
   1. Sélectionnez **Ajouter** pour ajouter votre nouveau sous-réseau. 

     :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/05-create-vnet-ip-address-add-sql-subnet-1.png" alt-text="Nommez votre premier sous-réseau, par exemple, sql-subnet-1, puis ajoutez 1 au troisième octet, de sorte que si l’adresse IP de DC-subnet est 10.5.0.0, votre nouveau sous-réseau est 10.5.1.0":::

1. Répétez l’étape précédente pour ajouter une autre plage de sous-réseau unique pour votre deuxième machine virtuelle SQL Server, que vous nommez, par exemple, **SQL-subnet-2**. Vous pouvez également ajouter 1 au troisième octet. 
   - Par exemple, si la plage IP de votre **DC-subnet** est *10.38.0.0/24* et que votre **SQL-subnet-1** est *10.38.1.0/24*, entrez `10.38.2.0/24` pour le nouveau sous-réseau
   - De même, si la plage IP de votre **DC-subnet** est *10.5.0.0/24* et que votre **SQL-subnet-1** est *10.5.1.0/24*, entrez la plage d’adresses IP `10.5.2.0/24` pour **SQL-subnet-2**. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/06-create-vnet-ip-address-add-sql-subnet-2.png" alt-text="Nommez votre deuxième sous-réseau, par exemple, sql-subnet-2, puis ajoutez 2 au troisième octet, de sorte que si l’adresse IP de DC-subnet est 10.38.0.0/24, votre nouveau sous-réseau est 10.38.2.0/24":::

1. Une fois que vous avez ajouté le deuxième sous-réseau, vérifiez les noms et les plages de vos sous-réseaux (vos plages d’adresses IP peuvent différer de l’image). Si tout semble correct, sélectionnez **Vérifier + créer**, puis **Créer** pour créer votre réseau virtuel. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/07-create-vnet-ip-address.png" alt-text="Une fois que vous avez ajouté le deuxième sous-réseau, vérifiez les noms et les plages de votre sous-réseau, comme dans l’exemple d’image (vos adresses IP peuvent être différentes). Si tout semble correct, sélectionnez Vérifier + créer, puis Créer pour créer votre réseau virtuel.":::

   Vous revenez au tableau de bord du portail et Azure vous avertit lorsque le réseau est créé.


## <a name="create-domain-controllers"></a>Création de contrôleurs de domaine

Une fois que votre réseau et vos sous-réseaux sont prêts, créez une machine virtuelle (ou deux éventuellement, pour la haute disponibilité) et configurez-la comme contrôleur de domaine. 

### <a name="create-dc-virtual-machines"></a>Créer des machines virtuelles de contrôleur de domaine

Pour créer vos machines virtuelles de contrôleur de domaine dans le portail Azure, suivez ces étapes : 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez **+ Créer**

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="Créer une ressource dans votre groupe de ressources":::

1. Recherchez **Windows Server** dans la barre de recherche de la **Place de marché**. 
1. Dans la vignette **Windows Server** de Microsoft, sélectionnez la liste déroulante **Créer** et choisissez l’image **Windows Server 2016 Datacenter**. 
1. Renseignez les valeurs dans la page **Créer une machine virtuelle** pour créer votre machine virtuelle de contrôleur de domaine, par exemple, **DC-VM-1**. Vous pouvez aussi créer une machine virtuelle supplémentaire, par exemple, **DC-VM-2**, pour fournir une haute disponibilité à Active Directory Domain Services. Utilisez les valeurs du tableau suivant pour créer votre ou vos machines virtuelles :

    | **Champ** | Valeur |
    | --- | --- |
    | **Abonnement** |*Votre abonnement* |
    | **Groupe de ressources** |SQL-HA-RG |
    | **Nom de la machine virtuelle** |Premier contrôleur de domaine : *DC-VM-1*.</br>Deuxième contrôleur de domaine : *DC-VM-2*. |
    | **Région** |*Localisation où vous avez déployé votre groupe de ressources et votre réseau virtuel.* |
    | **Options de disponibilité** |Zone de disponibilité </br> *Pour les régions Azure qui ne prennent pas en charge les zones de disponibilité, utilisez plutôt des groupes à haute disponibilité. Créez un groupe à haute disponibilité et placez-y toutes les machines virtuelles créées dans ce tutoriel.* |
    | **Zone de disponibilité** |Spécifiez 1 pour DC-VM-1. </br> Spécifiez 2 pour DC-VM-2. |
    | **Taille** |D2s_v3 (2 processeurs virtuels, 8 Go de RAM) |
    | **Nom d'utilisateur** |DomainAdmin |
    | **Mot de passe** |Contoso!0000 |
    | **Ports d’entrée publics** | *Autoriser les ports sélectionnés* |
    | **Sélectionner des ports d’entrée** | *RDP (3389)* |
    | **Type de disque du système d’exploitation** | SSD Premium (stockage localement redondant) |
    | **Réseau virtuel** |SQLHAVNET |
    | **Sous-réseau** |DC-subnet |
    | **Adresse IP publique** |*Même nom que la machine virtuelle, DC-VM-1 ou DC-VM-2* |
    | **Groupe de sécurité réseau de la carte réseau**| De base |
    | **Ports d’entrée publics**| Autoriser les ports sélectionnés |
    | **Sélectionner des ports d’entrée**| RDP (3389) |
    | **Diagnostics de démarrage** |Activer avec un compte de stockage managé (recommandé). |

    Azure vous avertit quand vos machines virtuelles sont créées et prêtes à l’emploi. 


### <a name="configure-the-domain-controller"></a>Configurer le contrôleur de domaine

Une fois que vos machines virtuelles de contrôleur de domaine sont prêtes, configurez le contrôleur de domaine pour corp.contoso.com.

Pour configurer **DC-VM-1** comme contrôleur de domaine, suivez ces étapes : 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez la machine **DC-VM-1**. 
1. Dans la page **DC-VM-1**, sélectionnez **Se connecter** pour télécharger un fichier RDP pour l’accès au Bureau à distance, puis ouvrez le fichier.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/08-dc-vm-1-rdp-connect.png" alt-text="Connexion à une machine virtuelle":::

1. Connectez-vous à la session RDP avec votre compte Administrateur configuré (**DomainAdmin**) et votre mot de passe (**Contoso!0000**).
1. Ouvrez le tableau de bord **Gestionnaire de serveur** (qui peut s’ouvrir par défaut) et choisissez **Ajouter des rôles et des fonctionnalités**. 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="Gestionnaire de serveur - Ajouter des rôles":::

1. Sélectionnez **Suivant** jusqu’à ce que vous atteigniez la section **Rôles de serveur**.
1. Sélectionnez les rôles **Services de domaine Active Directory** et **Serveur DNS**. Lorsque vous y êtes invité, ajoutez les fonctionnalités supplémentaires requises par ces rôles.

   > [!NOTE]
   > Windows vous avertit qu’il n’y a aucune adresse IP statique. Si vous testez la configuration, sélectionnez **Continuer**. Pour les scénarios de production, définissez l’adresse IP comme statique dans le portail Azure ou [utilisez PowerShell pour définir l’adresse IP statique de la machine de contrôleur de domaine](/azure/virtual-network/virtual-networks-static-private-ip-arm-ps).
   >

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/10-add-roles.png" alt-text="Boîte de dialogue Ajouter des rôles":::

1. Sélectionnez **Suivant** jusqu’à ce que vous atteigniez la section **Confirmation**. Cochez la case **Redémarrer automatiquement le serveur de destination, si nécessaire**.
1. Sélectionnez **Installer**.
1. Une fois les fonctionnalités installées, retournez dans le tableau de bord **Gestionnaire de serveur** .
1. Sélectionnez la nouvelle option **AD DS** dans le volet gauche.
1. Sélectionnez le lien **Plus** dans la barre d’avertissement jaune.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/11-ad-ds-more.png" alt-text="Boîte de dialogue AD DS sur la machine virtuelle du serveur DNS":::
    
1. Dans la colonne **Action** de la boîte de dialogue **Tous les détails de la tâche serveur**, sélectionnez **Promouvoir ce serveur en contrôleur de domaine**.
1. Dans l' **Assistant de configuration des services de domaine Active Directory**, utilisez les valeurs suivantes :

    | **Page** | Paramètre |
    | --- | --- |
    | **Configuration du déploiement** |**Ajouter une nouvelle forêt**<br/> **Nom de domaine racine** = corp.contoso.com |
    | **Options de contrôleur de domaine :** |**Mot de passe DSRM** = Contoso!0000<br/>**Confirmer le mot de passe** = Contoso!0000 |

1. Sélectionnez **Suivant** pour parcourir les autres pages de l’Assistant. Sur la page **Vérification de la configuration requise**, vérifiez que vous voyez le message suivant : **Toutes les vérifications de la configuration requise ont donné satisfaction**. Vous pouvez examiner les messages d’avertissement applicables, mais il est possible de poursuivre l’installation.
1. Sélectionnez **Installer**. La machine virtuelle **DC-VM-1** redémarre automatiquement.

### <a name="identify-dns-ip-address"></a>Identifier l’adresse IP DNS

Utilisez le contrôleur de domaine principal pour DNS. Pour ce faire, identifiez l’adresse IP privée de la machine virtuelle utilisée pour le contrôleur de domaine principal. 

Pour identifier l’adresse IP privée de la machine virtuelle dans le portail Azure, suivez ces étapes : 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez le contrôleur de domaine principal **DC-VM-1**. 
1. Dans la page **DC-VM-1**, choisissez **Réseau** sous le volet **Paramètres**. 
1. Noter l’adresse **IP privée de la carte réseau**. Utilisez cette adresse IP comme serveur DNS pour les autres machines virtuelles.  Dans l’exemple d’image, l’adresse IP privée est **10.38.0.4**. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-dc-vm-1-private-ip.png" alt-text="Dans la page DC-VM-1, choisissez Réseau sous le volet Paramètres, puis notez l’adresse IP privée de la carte réseau. Utilisez cette adresse IP comme serveur DNS. ":::

### <a name="configure-virtual-network-dns"></a>Configurer le DNS du réseau virtuel

Après avoir créé le premier contrôleur de domaine et activé DNS, configurez le réseau virtuel pour utiliser cette machine virtuelle pour le DNS.

Pour configurer votre réseau virtuel pour le DNS, suivez ces étapes : 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez votre réseau virtuel, **SQLHAVNET**. 
1. Sélectionnez **Serveurs DNS** dans le volet **Paramètres**, puis **Personnalisé**. 
1. Entrez l’adresse IP privée que vous avez identifiée précédemment, `10.38.0.4`, dans le champ **Adresse IP**. 
1. Sélectionnez **Enregistrer**. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-identify-dns-ip-address.png" alt-text=" Sélectionnez Serveurs DNS dans le volet Paramètres, puis Personnalisé. Entrez l’adresse IP privée que vous avez identifiée précédemment, 10.38.0.4, dans le champ Adresse IP. ":::


### <a name="configure-second-domain-controller"></a>Configurer le deuxième contrôleur de domaine

Une fois le contrôleur de domaine principal redémarré, vous pouvez éventuellement configurer le deuxième contrôleur de domaine pour assurer la haute disponibilité. Si vous ne voulez pas configurer de deuxième contrôleur de domaine, ignorez cette étape. Toutefois, notez qu’un deuxième contrôleur de domaine est recommandé dans les environnements de production. 

Définissez l’adresse du serveur DNS préféré, joignez le domaine, puis configurez le contrôleur de domaine secondaire. 

#### <a name="set-preferred-dns-server-address"></a>Définir l’adresse du serveur DNS préféré

Tout d'abord, modifiez l'adresse de serveur DNS par défaut. Pour ce faire, procédez comme suit : 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez la deuxième machine de contrôleur de domaine, **DC-VM-2**. 
1. Dans la page **DC-VM-2**, sélectionnez **Se connecter** pour télécharger le fichier RDP pour l’accès au Bureau à distance, puis ouvrez le fichier. 
1. Connectez-vous à la session RDP avec votre compte Administrateur configuré (**DomainAdmin**) et votre mot de passe (**Contoso!0000**).
1. Ouvrez le **Centre Réseau et partage** et sélectionnez l’interface réseau. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/13-network-interface.png" alt-text="Interface réseau":::

1. Ouvrez la page **Propriétés**. 
1. Choisissez **Protocole Internet Version 4 (TCP/IPv4)** , puis **Propriétés**.
1. Sélectionnez **Utiliser les adresses de serveur DNS suivantes** et spécifiez l’adresse IP privée du contrôleur de domaine principal, `10.38.0.4`, sous **Serveur DNS préféré**. 
1. Sélectionnez **OK**, puis **Fermer** pour valider les changements. Si vous perdez la connexion à votre Bureau à distance après avoir changé l’adresse IP DNS, accédez à la machine virtuelle dans le [portail Azure](https://portal.azure.com) et redémarrez-la. 

### <a name="join-the-domain"></a>Joindre le domaine 

Ensuite, joignez le domaine **corp.contoso.com**. Pour ce faire, procédez comme suit : 

1. Connectez-vous à distance à la machine virtuelle avec le compte **BUILTIN\DomainAdmin**.
1. Ouvrez **Gestionnaire de serveur** et sélectionnez **Serveur local**.
1. Sélectionnez **WORKGROUP**.
1. Dans la section **Nom de l’ordinateur**, sélectionnez **Modifier**.
1. Cochez la case **Domaine** et tapez **corp.contoso.com** dans la zone de texte. Sélectionnez **OK**.
1. Dans la boîte de dialogue contextuelle **Sécurité Windows**, spécifiez les informations d’identification du compte d’administrateur de domaine par défaut (**CORP\DomainAdmin**) et le mot de passe (**Contoso!0000**).
1. Quand le message « Bienvenue dans le domaine corp.contoso.com » s’affiche, sélectionnez **OK**.
1. Sélectionnez **Fermer**, puis sélectionnez **Redémarrer maintenant** dans la boîte de dialogue contextuelle.


#### <a name="configure-domain-controller"></a>Configurer le contrôleur de domaine 

Une fois que votre serveur a joint le domaine, vous pouvez le configurer comme deuxième contrôleur de domaine. Pour ce faire, procédez comme suit : 

1. Si vous n’êtes pas déjà connecté, ouvrez une session RDP sur votre contrôleur de domaine secondaire et ouvrez **Tableau de bord Gestionnaire de serveur** (qui peut être ouvert par défaut).
1. Sélectionnez le lien du tableau de bord **Ajouter des rôles et fonctionnalités**.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="Gestionnaire de serveur - Ajouter des rôles":::

1. Sélectionnez **Suivant** jusqu’à ce que vous atteigniez la section **Rôles de serveur**.
1. Sélectionnez les rôles **Services de domaine Active Directory** et **Serveur DNS**. Lorsque vous y êtes invité, ajoutez les fonctionnalités supplémentaires requises par ces rôles.
1. Une fois les fonctionnalités installées, retournez dans le tableau de bord **Gestionnaire de serveur** .
1. Sélectionnez la nouvelle option **AD DS** dans le volet gauche.
1. Sélectionnez le lien **Plus** dans la barre d’avertissement jaune.
1. Dans la colonne **Action** de la boîte de dialogue **Tous les détails de la tâche serveur**, sélectionnez **Promouvoir ce serveur en contrôleur de domaine**.
1. Sous **Configuration du déploiement**, sélectionnez **Ajouter un contrôleur de domaine à un domaine existant**.
1. Cliquez sur **Sélectionner**.
1. Connectez-vous à l’aide du compte Administrateur (**CORP.CONTOSO.COM\domainadmin**) et du mot de passe (**Contoso!0000**).
1. Dans **Sélectionner un domaine dans la forêt**, choisissez votre domaine, puis sélectionnez **OK**.
1. Dans **Options du contrôleur de domaine**, utilisez les valeurs par défaut et définissez un mot de passe DSRM.

    >[!NOTE]
    >La page **Options DNS** peut vous informer de l’impossibilité de créer une délégation pour ce serveur DNS. Vous pouvez ignorer cet avertissement dans les environnements hors production.
    >

1. Sélectionnez **Suivant** jusqu’à ce que la boîte de dialogue affiche la page de vérification **Conditions préalables**. Sélectionnez **Installer**.

Une fois que le serveur a terminé de modifier la configuration, redémarrez-le.

### <a name="add-second-dc-ip-address-to-dns"></a>Ajouter l’adresse IP du deuxième contrôleur de domaine au DNS

Une fois votre deuxième contrôleur de domaine configuré, suivez les mêmes étapes que précédemment pour [identifier l’adresse IP privée de la machine virtuelle](#identify-dns-ip-address) et [ajouter l’adresse IP privée comme serveur DNS personnalisé secondaire](#configure-virtual-network-dns) dans le réseau virtuel de votre groupe de ressources. L’ajout du serveur DNS secondaire dans le portail Azure permet la redondance du service DNS. 



## <a name="configure-domain-accounts"></a>Configurer les comptes de domaine

Une fois que vous avez configuré votre ou vos contrôleurs de domaine et défini votre ou vos serveurs DNS dans le portail Azure, créez des comptes de domaine pour l’utilisateur qui installe SQL Server et pour le compte de service SQL Server. 

Configurez trois comptes au total, un compte d’installation pour les deux machines virtuelles SQL Server et un compte de service pour chaque machine virtuelle SQL Server. Par exemple, utilisez les valeurs du tableau suivant pour les comptes :

|Compte  | Machine virtuelle  |Nom de domaine complet  |Description   |
|---------|---------|---------|---------|
|Installer    |Les deux| Corp\Install        |Connectez-vous à l’une des machines virtuelles avec ce compte pour configurer le cluster et le groupe de disponibilité. |
|SQLSvc1     |SQL-VM-1 |Corp\SQLSvc1 | Utilisez ce compte pour le service SQL Server sur la première machine virtuelle SQL Server. |
|SQLSvc2     |SQL-VM2 |Corp\SQLSvc2| Utilisez ce compte pour le service SQL Server sur la deuxième machine virtuelle SQL Server.|

Suivez ces étapes pour créer chaque compte : 

1. Connectez-vous à votre machine de contrôleur de domaine principal, **DC-VM-1**. .
1. Dans **Gestionnaire de serveur**, sélectionnez **Outils**, puis sélectionnez **Centre d’administration Active Directory**.
1. Sélectionnez **corp (local)** dans le volet gauche.
1. Dans le volet droit **Tâches**, sélectionnez **Nouveau**, puis sélectionnez **Utilisateur**. 
1. Entrez dans le nouveau compte d’utilisateur et définissez un mot de passe complexe. Pour les environnements hors production, définissez le compte d’utilisateur afin qu'il n’expire jamais.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/14-ad-dc-new-user.png" alt-text="Centre d’administration Active Directory":::

1. Sélectionnez **OK** pour créer l’utilisateur.
1. Répétez ces étapes pour créer les trois comptes. 

### <a name="grant-installation-account-permissions"></a>Accorder des autorisations au compte d’installation

Une fois les comptes créés, accordez les autorisations de domaine nécessaires au compte d’installation pour qu’il puisse créer des objets dans AD. 

Pour accorder les autorisations au compte d’installation, suivez ces étapes : 

1. Ouvrez le **Centre d’administration Active Directory** à partir du **Gestionnaire de serveur**, s’il n’est pas déjà ouvert. 
1. Sélectionnez **corp (local)** dans le volet gauche. 
1. Dans le volet **Tâches** à droite, vérifiez que vous voyez **corp (local)** dans la liste déroulante, puis sélectionnez **Propriétés** dessous.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/15-ad-dc-properties.png" alt-text="Propriétés de l’utilisateur CORP":::

1. Sélectionnez **Extensions**, puis sélectionnez le bouton **Avancé** de l’onglet **Sécurité**.
1. Dans la boîte de dialogue **Paramètres de sécurité avancés pour corp**, sélectionnez **Ajouter**.
1. Sélectionnez **Sélectionner un principal**, recherchez **CORP\Install**, puis sélectionnez **OK**.
1. Cochez les cases à côté de **Lire toutes les propriétés** et **Créer des objets ordinateur**. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/16-add-permissions.png" alt-text="Autorisations de l’utilisateur Corp":::

1. Sélectionnez **OK**, puis de nouveau **OK**. Fermez la fenêtre des propriétés **corp**.

Maintenant que vous avez fini de configurer Active Directory et les objets utilisateur, vous êtes prêt à créer vos machines virtuelles SQL Server. 

## <a name="create-sql-server-vms"></a>Créer des machines virtuelles SQL Server

Une fois que vous avez configuré votre AD, DNS et vos comptes d’utilisateur, vous êtes prêt à créer vos machines virtuelles SQL Server. Pour faire simple, utilisez les images VM SQL Server de la Place de marché. 

Toutefois, avant de créer vos machines virtuelles SQL Server, tenez compte des choix de conception suivants : 

**Disponibilité - Zones de disponibilité**   

Pour atteindre le niveau le plus haut de redondance, de résilience et de disponibilité, déployez les machines virtuelles dans des zones de disponibilité distinctes. Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Pour les régions Azure qui ne prennent pas encore en charge les zones de disponibilité, utilisez plutôt des groupes à haute disponibilité. Placez toutes les machines virtuelles dans le même groupe à haute disponibilité. 

**Stockage - Azure Disques managés**   

Pour le stockage de la machine virtuelle, utilisez Azure Disques managés. Microsoft recommande les disques managés pour les machines virtuelles SQL Server, car ils gèrent le stockage en arrière-plan. Pour plus d’informations, consultez [Vue d’ensemble des disques managés Azure](../../../virtual-machines/managed-disks-overview.md). 

**Réseau - Adresses IP privées en production**   

Pour les machines virtuelles, ce didacticiel utilise des adresses IP publiques. Une adresse IP publique assure une connexion à distance directe à la machine virtuelle via Interne et simplifie les étapes de configuration. Dans les environnements de production, Microsoft recommande d’utiliser uniquement des adresses IP privées afin de réduire l’empreinte vulnérable de la ressource de machine virtuelle de l’instance SQL Server.

**Réseau - Une seule carte réseau par serveur**   

Utilisez une seule carte réseau (NIC) par serveur (nœud de cluster). Le réseau Azure a une redondance physique, ce qui rend inutiles les cartes réseau supplémentaires pour un cluster de basculement déployé sur une machine virtuelle Azure. Le rapport de validation du cluster vous avertit que les nœuds sont accessibles uniquement sur un seul réseau. Vous pouvez ignorer cet avertissement quand votre cluster de basculement se trouve sur des machines virtuelles Azure. 

Pour créer vos machines virtuelles, suivez ces étapes : 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez **+ Créer**.
1. Recherchez **Azure SQL** et sélectionnez la vignette **Azure SQL** de Microsoft. 
1. Dans la page **Azure SQL**, sélectionnez **Créer**, puis choisissez l’image **SQL Server 2016 SP2 Entreprise sur Windows Server 2016** dans la liste déroulante. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/18-select-sql-vm-image.png" alt-text="Dans la page Azure SQL du portail, sélectionnez Créer, puis choisissez l’image SQL Server 2016 SP2 Entreprise sur Windows Server 2016 dans la liste déroulante.":::

Utilisez le tableau suivant pour renseigner les valeurs de la page **Créer une machine virtuelle** afin de créer les deux machines virtuelles SQL Server, **SQL-VM-1** et **SQL-VM-2** (vos adresses IP peuvent être différentes des exemples du tableau) : 

| Configuration | SQL-VM-1 | SQL-VM-2 |
| --- | --- | --- |
| Image de galerie |**SQL Server 2016 SP2 Enterprise sur Windows Server 2016** |**SQL Server 2016 SP2 Enterprise sur Windows Server 2016** |
| **Informations de base de la machine virtuelle** |**Nom** = SQL-VM-1<br/>**Nom d’utilisateur** = DomainAdmin<br/>**Mot de passe** = Contoso!0000<br/>**Abonnement** = votre abonnement<br/>**Groupe de ressources** = SQL-HA-RG<br/>**Emplacement** = Votre localisation Azure |**Nom** = SQL-VM-2<br/>**Nom d’utilisateur** = DomainAdmin<br/>**Mot de passe** = Contoso!0000<br/>**Abonnement** = votre abonnement<br/>**Groupe de ressources** = SQL-HA-RG<br/>**Emplacement** = Votre localisation Azure |
| **Taille de machine virtuelle** |**TAILLE** = E2ds_v4 (2 processeurs virtuels, 16 GB de RAM)</br> |**TAILLE** = E2ds_v4 (2 processeurs virtuels, 16 GB de RAM) |
| **Paramètres de machine virtuelle** |**Options de disponibilité** = Zone de disponibilité<br/>**Zone de disponibilité** = 1<br/>**Ports d’entrée publics** = Autoriser les ports sélectionnés<br/>**Sélectionner les ports d’entrée** : RDP (3389)<br/>**Type de disque de système d’exploitation** = SSD Premium (stockage localement redondant)<br/>**Réseau virtuel** = SQLHAVNET<br/>**Sous-réseau** = SQL-subnet-1(10.38.1.0/24)<br/>**Adresse IP publique** = Générée automatiquement.<br/>**Groupe de sécurité réseau de la carte réseau** = De base<br/>**Ports d’entrée publics** = Autoriser les ports sélectionnés <br/> **Sélectionner les ports d’entrée** : RDP (3389)<br/>**Diagnostics de démarrage** = Activer avec un compte de stockage managé (recommandé)<br/>|**Options de disponibilité** = Zone de disponibilité<br/>**Zone de disponibilité** = 2<br/>**Ports d’entrée publics** = Autoriser les ports sélectionnés<br/>**Sélectionner les ports d’entrée** : RDP (3389)<br/>**Type de disque de système d’exploitation** = SSD Premium (stockage localement redondant)<br/>**Réseau virtuel** = SQLHAVNET<br/>**Sous-réseau** = SQL-subnet-2(10.38.2.0/24)<br/>**Adresse IP publique** = Générée automatiquement.<br/>**Groupe de sécurité réseau de la carte réseau** = De base<br/>**Ports d’entrée publics** = Autoriser les ports sélectionnés <br/> **Sélectionner les ports d’entrée** : RDP (3389)<br/>**Diagnostics de démarrage** = Activer avec un compte de stockage managé (recommandé)<br/> |
| **Paramètres de SQL Server** |**Connectivité SQL** = privée (dans le réseau virtuel)<br/>**Port** = 1433<br/>**Authentification SQL** = désactivée<br/>**Intégration Azure Key Vault** = Désactiver <br/>**Optimisation du stockage** = Traitement transactionnel<br/>**Données SQL** = 1024 Gio, 5000 IOPS, 200 Mo/s<br/>**Journal SQL** = 1024 Gio, 5000 IOPS, 200 Mo/s<br/>**Tempdb SQL** = Utiliser un lecteur SSD local<br/>**Mise à jour corrective automatisée** : dimanche à 2h00<br/>**Sauvegarde automatisée** = Désactiver |**Connectivité SQL** = privée (dans le réseau virtuel)<br/>**Port** = 1433<br/>**Authentification SQL** = désactivée<br/>**Intégration Azure Key Vault** = Désactiver <br/>**Optimisation du stockage** = Traitement transactionnel<br/>**Données SQL** = 1024 Gio, 5000 IOPS, 200 Mo/s<br/>**Journal SQL** = 1024 Gio, 5000 IOPS, 200 Mo/s<br/>**Tempdb SQL** = Utiliser un lecteur SSD local<br/>**Mise à jour corrective automatisée** : dimanche à 2h00<br/>**Sauvegarde automatisée** = Désactiver |

<br/>

> [!NOTE]
> Ces tailles de machine suggérées sont uniquement destinées au test des groupes de disponibilité dans les machines virtuelles Azure. Pour l’optimisation des charges de travail de production, consultez les recommandations de taille dans [Bonnes pratiques de performances pour SQL Server sur des machines virtuelles Azure](./performance-guidelines-best-practices-checklist.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

## <a name="configure-sql-server-vms"></a>Configurer les machines virtuelles SQL Server

Après la création des machines virtuelles, configurez vos machines virtuelles SQL Server en ajoutant une adresse IP secondaire à chacune d’entre elles et en les joignant au domaine. 


### <a name="add-secondary-ips-to-sql-server-vms"></a>Ajouter des IP secondaires aux machines virtuelles SQL Server

Dans un environnement avec plusieurs sous-réseaux, attribuez des adresses IP secondaires à chaque machine virtuelle SQL Server pour l’écouteur du groupe de disponibilité et, pour Windows Server 2016 et versions antérieures, attribuez également des adresses IP secondaires à chaque machine virtuelle SQL Server pour les utiliser comme adresse IP du cluster. Cela vous évite d’utiliser un équilibreur de charge Azure, comme c’est le cas dans un environnement avec un seul sous-réseau.  

Sur Windows Server 2016 et versions antérieures, vous devez attribuer une adresse IP secondaire supplémentaire à chaque machine virtuelle SQL Server pour l’utiliser comme adresse IP du cluster Windows, car le cluster utilise le **Nom réseau du cluster** au lieu du Nom de réseau distribué (DNN) par défaut introduit dans Windows Server 2019. Avec un DNN, l’objet nom de cluster (CNO) est automatiquement inscrit aux adresses IP de tous les nœuds du cluster, ce qui évite d’utiliser une adresse IP de cluster Windows dédiée.

Si vous êtes sur Windows Server 2016 et versions antérieures, suivez les étapes de cette section pour attribuer une adresse IP secondaire à chaque machine virtuelle SQL Server pour l’écouteur du groupe de disponibilité *et* le cluster, *à la fois*. 

Si vous êtes sur Windows Server 2019 ou version ultérieure, attribuez seulement une adresse IP secondaire pour l’écouteur du groupe de disponibilité et ignorez les étapes qui attribuent une IP de cluster Windows, sauf si vous envisagez de configurer votre cluster avec un nom de réseau virtuel (VNN), auquel cas attribuez les deux adresses IP à chaque machine virtuelle SQL Server comme vous le feriez dans Windows Server 2016. 

Pour attribuer des IP secondaires supplémentaires aux machines virtuelles, suivez ces étapes : 

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com/) et sélectionnez la première machine SQL Server, **DC-VM-1**. 
1. Sélectionnez **Réseau** dans le volet **Paramètres**, puis l’**Interface réseau** : 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/19-sql-vm-network-interface.png" alt-text="Sélectionnez Réseau dans le volet Paramètres, puis l’Interface réseau":::

1. Dans la page **Interface réseau**, sélectionnez **Configurations IP** dans le volet **Paramètres**, puis choisissez **+ Ajouter** pour ajouter une adresse IP supplémentaire : 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/20-ip-configurations-add.png" alt-text="Configurations IP":::

1. Dans la page **Ajouter une configuration IP**, effectuez les opérations suivantes : 
   1. Spécifiez l’IP du cluster Windows comme **Nom**, par exemple, **windows-cluster-ip** pour Windows 2016 et versions antérieures. Ignorez cette étape si vous êtes sur Windows Server 2019 ou version ultérieure.  
   1. Définissez l’**Allocation** sur **Statique**.
   1. Entrez une **Adresse IP** inutilisée du même sous-réseau (**SQL-subnet-1**) que celui de la machine virtuelle SQL Server (**SQL-VM-1**), par exemple, `10.38.1.10`. 
   1. Laissez la valeur par défaut **Dissocier** pour l’**Adresse IP publique**. 
   1. Sélectionnez **OK** pour terminer l’ajout de la configuration IP. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/21-add-ip-windows-cluster.png" alt-text="Ajoutez l’IP du cluster en entrant une adresse IP inutilisée du sous-réseau de la première machine virtuelle SQL Server":::

1. Resélectionnez **+ Ajouter** pour configurer une adresse IP supplémentaire pour l’écouteur du groupe de disponibilité (avec un nom de type **availability-group-listener**), et spécifiez une adresse IP inutilisée dans **SQL-subnet-1**, par exemple, `10.38.1.11` : 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/22-add-ip-ag-listener.png" alt-text="Resélectionnez + Ajouter pour configurer une adresse IP supplémentaire pour l’écouteur du groupe de disponibilité (avec un nom de type availability-group-listener), et spécifiez une adresse IP inutilisée dans SQL-subnet-1, par exemple, 10.31.1.11":::

1. Répétez ces étapes pour la deuxième machine virtuelle SQL Server, **SQL-VM-2**. Attribuez deux adresses IP secondaires inutilisées dans **SQL-subnet-2**. Utilisez les valeurs du tableau suivant pour ajouter la configuration IP : 

   
    | **Champ** | Entrée | Entrée | 
    | --- | --- | --- |
    | **Nom** |windows-cluster-ip | availability-group-listener |
    | **Allocation** | statique | statique |
    | **Adresse IP** | 10.38.2.10 | 10.38.2.11 | 
    |  |  |  |

Vous êtes maintenant prêt à joindre le domaine **corp.contoso.com**. 

### <a name="join-the-servers-to-the-domain"></a>Joindre les serveurs au domaine

Une fois que vos deux adresses IP secondaires ont été attribuées aux deux machines virtuelles SQL Server, joignez chaque machine virtuelle SQL Server au domaine **corp.contoso.com**. 

Pour joindre le domaine corp.contoso.com, suivez les mêmes étapes pour la machine virtuelle SQL Server que celles que vous avez utilisées pour [joindre le domaine](#join-the-domain) avec le contrôleur de domaine secondaire. 

Attendez que chaque machine virtuelle SQL Server redémarre, puis ajoutez vos comptes. 


## <a name="add-accounts"></a>Ajouter des comptes

Ajoutez le compte d’installation en tant qu’administrateur sur chaque machine virtuelle, accordez l’autorisation au compte d’installation et aux comptes locaux dans SQL Server et mettez à jour le compte de service SQL Server. 

### <a name="add-install-account"></a>Ajouter un compte d’installation

Une fois que les deux machines virtuelles SQL Server sont jointes au domaine, ajoutez **CORP\Install** comme membre du groupe Administrateurs local.

>[!TIP]
> Veillez à vous connecter avec le compte Administrateur de *domaine*. Dans les étapes précédentes, vous avez utilisé le compte Administrateur **BUILTIN**. Maintenant que le serveur fait partie du domaine, utilisez le compte de domaine. Dans votre session RDP, spécifiez *DOMAIN*\\*username*, par exemple, **CORP\DomainAdmin**.

Pour ajouter le compte comme administrateur, suivez ces étapes : 

1. Attendez que la machine virtuelle ait redémarré, puis relancez le fichier RDP à partir de la première machine virtuelle SQL Server pour vous connecter à **SQL-VM-1** en utilisant le compte **CORP\DomainAdmin**.
1. Dans **Gestionnaire de serveur**, sélectionnez **Outils**, puis sélectionnez **Gestion de l’ordinateur**.
1. Dans la fenêtre **Gestion de l’ordinateur**, développez **Utilisateurs et groupes locaux**, puis sélectionnez **Groupes**.
1. Double-cliquez sur le groupe **Administrateurs** .
1. Dans la boîte de dialogue **Propriétés de Administrateurs**, sélectionnez **Ajouter**.
1. Entrez l’utilisateur **CORP\Install**, puis sélectionnez **OK**.
1. Sélectionnez **OK** pour fermer la boîte de dialogue **Propriétés de Administrateurs**.
1. Répétez ces étapes sur **SQL-VM-2**. 

### <a name="add-account-to-sysadmin"></a>Ajouter un compte à sysadmin 

Le compte d’installation (CORP\install) utilisé pour configurer le groupe de disponibilité doit faire partie du rôle serveur fixe **sysadmin** sur chaque machine virtuelle SQL Server. 

Pour accorder les autorisations **sysadmin** au compte d’installation, suivez ces étapes : 

1. Connectez-vous au serveur avec le protocole Bureau à distance (RDP) en utilisant le compte *\<MachineName\>\DomainAdmin*, par exemple, `SQL-VM-1\DomainAdmin`.
1. Ouvrez SQL Server Management Studio et connectez-vous à l'instance locale de SQL Server.
1. Dans l’**Explorateur d’objets**, sélectionnez **Sécurité**.
1. Cliquez avec le bouton droit sur **Connexions**. Sélectionnez **Nouvelle connexion**.
1. Dans **Nouvelle connexion**, sélectionnez **Rechercher**.
1. Sélectionner **Emplacements**.
1. Entrez les informations d’identification du réseau de l’administrateur de domaine.
1. Utilisez le compte d’installation (CORP\install).
1. Définissez la connexion à un membre du rôle serveur fixe **sysadmin**.
1. Sélectionnez **OK**.
1. Répétez ces étapes sur la deuxième machine virtuelle SQL Server, **SQL-VM-2**, en vous connectant avec le compte du nom de la machine appropriée, `SQL-VM-2\DomainAdmin`. 


### <a name="add-system-account"></a>Ajouter un compte système 

Dans les dernières versions de SQL Server, le compte [NT AUTHORITY\SYSTEM] n’a pas d’autorisation sur SQL Server par défaut et doit en recevoir manuellement. 

Pour ajouter le compte [NT AUTHORITY\SYSTEM] et lui accorder les autorisations appropriées, suivez ces étapes : 

1. Connectez-vous à la première machine virtuelle SQL Server avec le protocole Bureau à distance (RDP) en utilisant le compte *\<MachineName\>\DomainAdmin*, par exemple, `SQL-VM-1\DomainAdmin`.
1. Ouvrez SQL Server Management Studio et connectez-vous à l'instance locale de SQL Server.
1. Créez un compte pour `[NT AUTHORITY\SYSTEM]` sur chaque instance SQL Server à l’aide de la commande Transact-SQL (T-SQL) suivante : 

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Accordez les autorisations suivantes à `[NT AUTHORITY\SYSTEM]` sur chaque instance de SQL Server :

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Pour accorder ces autorisations, utilisez la commande Transact-SQL (T-SQL) suivante : 

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

1. Répétez ces étapes sur la deuxième machine virtuelle SQL Server, **SQL-VM-2**, en vous connectant avec le compte du nom de la machine appropriée, `SQL-VM-2\DomainAdmin`. 

### <a name="set-the-sql-server-service-accounts"></a>Définir les comptes de service SQL Server

Le service SQL Server sur chaque machine virtuelle doit utiliser un compte de domaine dédié.  Utilisez les comptes de domaine que vous avez créés précédemment : **Corp\SQLSvc1** pour **SQL-VM-1** et **Corp\SQLSvc2** pour **SQL-VM-2**. 

Pour définir le compte de service, suivez ces étapes : 

1. Connectez-vous à la première machine virtuelle SQL Server avec le protocole Bureau à distance (RDP) en utilisant le compte *\<MachineName\>\DomainAdmin*, par exemple, `SQL-VM-1\DomainAdmin`.
1. Ouvrez le **Gestionnaire de configuration SQL Server**.
1. Cliquez avec le bouton droit sur le service SQL Server, puis sélectionnez **Propriétés**.
1. Indiquez le compte (**Corp\SQLSvc1**) et le mot de passe.
1. Sélectionnez **Appliquer** pour valider votre changement et redémarrez le service SQL Server. 
1. Répétez ces étapes sur l’autre machine virtuelle SQL Server (SQL-VM-1), en vous connectant avec le compte de domaine de la machine, `SQL-VM-2\DomainAdmin`, et en fournissant le deuxième compte de service (**Corp\SQLSvc2**). 


## <a name="create-azure-storage-account"></a>Créer un compte de stockage Azure

Pour déployer un cluster de basculement Windows Server à deux nœuds, un troisième membre est nécessaire afin d’établir le quorum. Sur les machines virtuelles Azure, le témoin cloud est l’option de quorum recommandée. Pour configurer un témoin cloud, vous avez besoin d’un compte stockage Azure. Pour plus d’informations, consultez [Déployer un témoin cloud pour un cluster de basculement](/windows-server/failover-clustering/deploy-cloud-witness).

Pour créer le compte de stockage Azure dans le portail :

1. Dans le portail, ouvrez le groupe de ressources **SQL-HA-RG** et sélectionnez **+ Créer**
1. Recherchez **compte de stockage**.
1. Sélectionnez **Compte de stockage** et **Créer**, en le configurant avec les valeurs suivantes : 

    1. Sélectionnez votre abonnement et le groupe de ressources **SQL-HA-RG.**
    1. Entrez un **Nom de compte de stockage** pour votre compte de stockage.
       Les noms de compte de stockage doivent avoir entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Le nom du compte de stockage doit être unique dans Azure.    
    1. Sélectionnez votre **Région.**
    1. Pour optimiser les **Performances**, sélectionnez **Standard : recommandé pour la plupart des scénarios (compte universel v2)** . Le Stockage Premium Azure n’est pas pris en charge pour un témoin cloud.
    1. Pour assurer la **Redondance**, sélectionnez **Stockage localement redondant (LRS).**
       Le clustering de basculement utilise le fichier blob comme point d’arbitrage, ce qui nécessite des garanties de cohérence lors de la lecture des données. Par conséquent, vous devez sélectionner Stockage localement redondant comme type de réplication. 
    1. Sélectionner **Vérifier + créer**

## <a name="configure-the-firewall"></a>Configurer le pare-feu

La fonctionnalité de groupe de disponibilité dépend du trafic sur les ports TCP suivants : 

- **Machine virtuelle SQL Server** : le port 1433 pour une instance SQL Server par défaut.
- **Point de terminaison de mise en miroir de bases de données :** N’importe quel port disponible. Les exemples utilisent souvent le port 5022.

Ouvrez ces ports de pare-feu sur les deux machines virtuelles SQL Server. La méthode d’ouverture des ports dépend de la solution de pare-feu que vous utilisez et peut varier de l’exemple de pare-feu Windows fourni dans cette section. 

Pour ouvrir ces ports sur un pare-feu Windows, suivez ces étapes : 

1. Sur l'écran de **démarrage** du premier serveur SQL, lancez le **Pare-feu Windows avec fonctions de sécurité avancées**.
1. Dans le volet gauche, sélectionnez **Règles de trafic entrant**. Dans le volet droit, sélectionnez **Nouvelle règle**.
1. Dans **Type de règle**, choisissez **Port**.
1. Pour le port, spécifiez **TCP** et entrez les numéros de port correspondants. Voir l’exemple suivant :

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/17-firewall-tcp-ports.png" alt-text="Pare-feu SQL":::

1. Sélectionnez **Suivant**.
1. Dans la page **Action**, sélectionnez **Autoriser la connexion**, puis **Suivant**.
1. Dans la page **Profil**, acceptez les paramètres par défaut puis sélectionnez **Suivant**.
1. Dans la page **Nom**, spécifiez un nom de règle (par exemple, **SQL Inbound**) dans la zone de texte **Nom**, puis sélectionnez **Terminer**.
1. Répétez ces étapes sur la seconde machine virtuelle SQL Server.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré les prérequis, commencez à [configurer votre groupe de disponibilité](availability-group-manually-configure-tutorial-multi-subnet.md) dans plusieurs sous-réseaux. 

Pour en savoir plus, consultez :

- [Cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure](hadr-windows-server-failover-cluster-overview.md)
- [Groupes de disponibilité Always On avec SQL Server sur les machines virtuelles Azure](availability-group-overview.md)
- [Vue d’ensemble des groupes de disponibilité Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Paramètres HADR pour SQL Server sur les machines virtuelles Azure](hadr-cluster-best-practices.md)