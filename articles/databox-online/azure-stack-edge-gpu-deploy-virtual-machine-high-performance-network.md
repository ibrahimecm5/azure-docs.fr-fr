---
title: Déployer des machines virtuelles réseau hautes performances sur votre appareil Azure Stack Edge Pro avec GPU
description: Découvrez comment déployer des machines virtuelles réseau hautes performances sur votre appareil Azure Stack Edge Pro avec GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/29/2021
ms.author: alkohli
ms.openlocfilehash: 4b3f2a2b232bec60edbce204008e245bcabee09c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097984"
---
# <a name="deploy-high-performance-network-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Déployer des machines virtuelles réseau hautes performances sur votre appareil Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Vous pouvez créer et gérer des machines virtuelles sur un appareil Azure Stack Edge Pro GPU à l’aide du portail Azure, de modèles et de cmdlets Azure PowerShell, ainsi que via des scripts Azure CLI ou Python. Cet article explique comment créer et gérer une machine virtuelle réseau hautes performances (HPN) sur votre appareil Azure Stack Edge Pro avec GPU. 

## <a name="about-hpn-vms"></a>À propos des machines virtuelles HPN

Une architecture NUMA (accès mémoire non uniforme) permet d’augmenter la vitesse du processeur. Dans un système NUMA, les processeurs sont organisés en systèmes plus petits appelés nœuds. Chaque nœud a ses propres processeurs et sa propre mémoire. Les processeurs se voient généralement allouer de la mémoire située à proximité, ce qui permet d’accélérer l’accès. Pour plus d’informations, consultez [Prise en charge de NUMA](/windows/win32/procthread/numa-support).  

Sur votre appareil Azure Stack Edge, les processeurs logiques sont distribués sur des nœuds NUMA, et des interfaces réseau haut débit peuvent être attachées à ces nœuds. Une machine virtuelle HPN (réseau hautes performances) dispose d’un ensemble dédié de processeurs logiques. Ces processeurs sont d’abord choisis dans le nœud NUMA auquel une interface réseau haut débit est attachée, puis ils sont choisis parmi d’autres nœuds. Une machine virtuelle HPN peut uniquement utiliser la mémoire du nœud NUMA affecté à ses processeurs.  

Pour exécuter des applications réseau à faible latence et à haut débit sur les machines virtuelles HPN déployées sur votre appareil, veillez à réserver les processeurs virtuels qui résident sur le nœud NUMA 0. Ce nœud dispose d’interfaces réseau Mellanox à haute vitesse attachées sur les ports 5 et 6.   

        
## <a name="hpn-vm-deployment-workflow"></a>Workflow de déploiement de machines virtuelles HPN

Le récapitulatif général du workflow de déploiement de machines virtuelles HPN est le suivant :

1. Activez une interface réseau pour le calcul sur votre appareil Azure Stack Edge. Cette étape crée un commutateur virtuel sur l’interface réseau spécifiée.
1. Activez la gestion cloud des machines virtuelles à partir du portail Azure.
1. Chargez un disque dur virtuel dans un compte Stockage Azure à l’aide de l’Explorateur de stockage Azure. 
1. Utilisez le disque dur virtuel chargé pour télécharger le disque dur virtuel sur l’appareil et l’utiliser pour créer une image de machine virtuelle. 
1. Réservez des processeurs virtuels sur l’appareil pour les machines virtuelles HPN.
1. Utilisez les ressources créées durant les étapes précédentes :
    1. Image de machine virtuelle que vous avez créée.
    1. Commutateur virtuel associé à l’interface réseau sur laquelle vous avez activé le calcul.
    1. Sous-réseau associé au commutateur virtuel.

    Créez ou spécifiez les ressources suivantes en ligne :
    1. Nom de la machine virtuelle, taille de la machine virtuelle HPN prise en charge et informations d’identification de connexion de la machine virtuelle. 
    1. Créez des disques de données ou attachez des disques de données existants.
    1. Configurez une adresse IP statique ou dynamique pour la machine virtuelle. Si vous fournissez une adresse IP statique, choisissez à partir d’une adresse IP libre dans la plage de sous-réseau de l’interface réseau activée pour le calcul.

    Utilisez les ressources précédentes pour créer une machine virtuelle HPN.

## <a name="prerequisites"></a>Prérequis

Avant de commencer à créer et à gérer des machines virtuelles sur votre appareil via le portail Azure, assurez-vous de ce qui suit :

- Vous avez appliqué les paramètres réseau sur votre appareil Azure Stack Edge Pro comme décrit dans [Étape 1 : Configurer un appareil Azure Stack Edge Pro GPU](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device).

    1. Vous avez activé une interface réseau pour le calcul. Cette adresse IP d’interface réseau sert à créer un commutateur virtuel pour le déploiement de la machine virtuelle. Dans l’interface utilisateur locale de votre appareil, accédez à **Calcul**. Sélectionnez l’interface réseau que vous allez utiliser pour créer un commutateur virtuel.

        > [!IMPORTANT] 
        > Vous ne pouvez configurer qu’un seul port pour le calcul.

    1. Activez le calcul sur l’interface réseau. Le GPU Azure Stack Edge Pro crée et gère un commutateur virtuel correspondant à cette interface réseau.

-  Vous avez accès à un disque dur virtuel Windows ou Linux que vous utiliserez pour créer l’image de la machine virtuelle que vous souhaitez créer.

En plus des prérequis ci-dessus utilisés pour la création des machines virtuelles, vous devez également configurer les prérequis suivants spécifiquement pour les machines virtuelles HPN :

- Réservez des processeurs virtuels pour les machines virtuelles HPN sur l’interface Mellanox. Procédez comme suit :

    1. [Connectez-vous à l'interface PowerShell de l'appareil](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

    1. Identifiez toutes les machines virtuelles s’exécutant sur votre appareil. Cela inclut les machines virtuelles Kubernetes ou toutes les charges de travail de machine virtuelle que vous avez déployées.

        ```powershell
        get-vm -force
        ```
    1. Arrêtez toutes les machines virtuelles en cours d’exécution.
    
        ```powershell
        stop-vm -force
        ```
    1. Obtenez le `hostname` de votre appareil. Une chaîne correspondant au nom d’hôte de l’appareil doit être retournée.

        ```powershell
        hostname
        ```
    1. Obtenez les index de processeur logique à réserver pour les machines virtuelles HPN.
    
        ```powershell
        Get-HcsNumaLpMapping -MapType HighPerformanceCapable -NodeName <Output of hostname command>
        ```
        Voici un exemple de sortie :
    
        ```powershell    
        [dbe-1csphq2.microsoftdatabox.com]: PS>hostname
        1CSPHQ2
        [dbe-1csphq2.microsoftdatabox.com]: P> Get-HcsNumaLpMapping -MapType HighPerformanceCapable -NodeName 1CSPHQ2
         { Numa Node #0 : CPUs [4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19] }
         { Numa Node #1 : CPUs [24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39] }
        
        [dbe-1csphq2.microsoftdatabox.com]: PS>
        ```
    
    1. Réservez des processeurs virtuels pour les machines virtuelles HPN. Le nombre de processeurs virtuels réservés ici détermine les processeurs virtuels disponibles qui peuvent être affectés aux machines virtuelles HPN. Pour connaître le nombre de cœurs utilisés par chaque taille de machine virtuelle HPN, consultez les [tailles de machines virtuelles HPN prises en charge](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes). Sur votre appareil, les ports Mellanox 5 et 6 se trouvent sur le nœud NUMA 0.
    
        ```powershell
        Set-HcsNumaLpMapping -CpusForHighPerfVmsCommaSeperated <Logical indexes from the Get-HcsNumaLpMapping cmdlet> -AssignAllCpusToRoot $false
        ```
    
        Une fois cette commande exécutée, l’appareil redémarre automatiquement.

        Voici un exemple de sortie : 
    
        ```powershell
        [dbe-1csphq2.microsoftdatabox.com]: PS>Set-HcsNumaLpMapping -CpusForHighPerfVmsCommaSeperated "4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39" -AssignAllCpusToRoot $false
        Requested Configuration requires a reboot...
        Machine will reboot in some time. Please be patient.
        [dbe-1csphq2.microsoftdatabox.com]: PS>    
        ```
    
        > [!NOTE]
        > - Vous pouvez choisir de réserver tous les index logiques des deux nœuds NUMA indiqués dans l’exemple ou seulement un sous-ensemble des index. Si vous décidez de réserver un sous-ensemble des index, choisissez les index dans le nœud d’appareil auquel une interface réseau Mellanox est attachée, pour obtenir des performances optimales. Pour Azure Stack Edge Pro avec GPU, le nœud NUMA ayant l’interface réseau Mellanox est le n° 0.  
        > - La liste des index logiques doit contenir une séquence appariée associant un nombre impair et un nombre pair. Par exemple ((4,5)(6,7)(10,11)). Toute tentative de définition d’une liste de nombres telle que `5,6,7` ou de paires telles que `4,6` est vouée à l’échec.
        > - L’utilisation consécutive de deux commandes `Set-HcsNuma` pour affecter des processeurs virtuels entraîne la réinitialisation de la configuration. De plus, ne libérez pas les processeurs à l’aide de l’applet de commande Set-HcsNuma si vous avez déployé une machine virtuelle HPN.
  
    1. Attendez la fin du redémarrage de l’appareil. Une fois l’appareil en cours d’exécution, ouvrez une nouvelle session PowerShell. [Connectez-vous à l'interface PowerShell de l'appareil](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
    
    1. Validez la réservation du processeur virtuel.
    
        ```powershell
        Get-HcsNumaLpMapping -MapType MinRootAware -NodeName <Output of hostname command> 
        ```
        La sortie ne doit pas afficher les index que vous avez définis. Si vous voyez les index que vous avez définis dans la sortie, cela signifie que la commande `Set` ne s’est pas exécutée correctement. Réessayez d’exécuter la commande. Si le problème persiste, contactez le Support Microsoft. 
    
        Voici un exemple de sortie :
    
        ```powershell
        [dbe-1csphq2.microsoftdatabox.com]: PS> Get-HcsNumaLpMapping -MapType MinRootAware -NodeName 1CSPHQ2
        { Numa Node #0 : CPUs [0, 1, 2, 3] }
        { Numa Node #1 : CPUs [20, 21, 22, 23] }
        [dbe-1csphq2.microsoftdatabox.com]: PS>
        ```
    
    1. Redémarrez les machines virtuelles que vous avez arrêtées à l’étape précédente.
    
        ```powershell
        start-vm 
        ```
    <!-- Start-vm doesn't seem to work alone. Get-vm alone doesn't seem to return my running VM"VmId"--> 



## <a name="deploy-a-vm"></a>Déployer une machine virtuelle

Suivez ces étapes pour créer une machine virtuelle HPN sur votre appareil.

1. Dans le portail Azure de votre ressource Azure Stack Edge, accédez à [Ajouter une image de machine virtuelle](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#add-a-vm-image). Vous allez utiliser cette image de machine virtuelle pour créer une machine virtuelle au cours de la prochaine étape.

1. Suivez toutes les étapes décrites dans [Ajouter une machine virtuelle](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#add-a-vm) avec la configuration demandée. 

    Sous l’onglet Informations de base, sélectionnez une taille de machine virtuelle dans [Séries DSv2 ou F prises en charge pour HPN](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes).

    ![Capture d’écran montrant l’onglet Informations de base dans l’Assistant Ajouter une machine virtuelle pour Azure Stack Edge. L’onglet Informations de base et le bouton Suivant : Disques sont mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-1.png)

1. Suivez les étapes restantes de la création de machine virtuelle. La création de la machine virtuelle prend environ 30 minutes. 

    ![Capture d’écran montrant l’onglet Vérifier + créer de l’Assistant Ajout de machine virtuelle pour Azure Stack Edge. Le bouton Créer est mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-2.png)

1. Une fois la machine virtuelle créée, vous verrez votre nouvelle machine virtuelle dans le volet **Vue d’ensemble**. Sélectionnez la machine virtuelle que vous venez de créer pour accéder à **Machines virtuelles**.

    ![Capture d’écran montrant le volet Machines virtuelles d’un appareil Azure Stack Edge. L’étiquette Machines virtuelles et une entrée de machine virtuelle sont mises en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-3.png)

    Sélectionnez la machine virtuelle pour afficher les détails correspondants.

    ![Capture d’écran montrant l’onglet Détails du volet Vue d’ensemble d’une machine virtuelle dans Azure Stack Edge. La taille de machine virtuelle et l’adresse IP de la section Réseau sont mises en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-4.png)

    Vous allez utiliser l’adresse IP de l’interface réseau pour vous connecter à la machine virtuelle.

    > [!NOTE]
    > Si les processeurs virtuels ne sont pas réservés pour les machines virtuelles HPN avant le déploiement, celui-ci échoue avec l’erreur `FabricVmPlacementErrorInsufficientNumaNodeCapacity`.
                                                                                                                                                                         
## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de déploiement des machines virtuelles](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Superviser l’activité des machines virtuelles sur votre appareil](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Superviser l’utilisation du processeur et de la mémoire sur une machine virtuelle](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

