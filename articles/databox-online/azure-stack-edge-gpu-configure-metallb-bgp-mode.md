---
title: Configurer MetalLB via BGP sur Azure Stack Edge
description: Décrit comment configurer MetalLB via Border Gateway Protocol pour l’équilibrage de charge sur votre appareil Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/24/2021
ms.author: alkohli
ms.openlocfilehash: df4f0170f6dde0995576b0244733dea4ab01515e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096267"
---
# <a name="configure-load-balancing-with-metallb-on-your-azure-stack-edge"></a>Configurer l’équilibrage de charge avec MetalLB sur votre Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment configurer l’équilibrage de charge sur votre appareil Azure Stack Edge à l’aide de MetalLB via Border Gateway Protocol (BGP). 

## <a name="about-metallb-and-load-balancing"></a>À propos de MetalLB et de l’équilibrage de charge

MetalLB est une implémentation d’équilibrage de charge pour les clusters Kubernetes de matériel nu. MetalLB remplit deux fonctions : il affecte des adresses IP aux services d’équilibrage de charge Kubernetes à partir d’un pool configuré d’adresses IP, puis annonce l’adresse IP au réseau externe. MetalLB atteint ces fonctions par le biais de protocoles de routage standard tels que ARP (Address Resolution Protocol), NDP (voisin Discovery Protocol) ou Border Gateway Protocol (BGP). 

Pour plus d’informations, consultez [Mode BGP pour MetalLB](https://metallb.universe.tf/configuration/#bgp-configuratioN).

## <a name="metallb-on-azure-stack-edge"></a>MetalLB sur Azure Stack Edge

Plusieurs composants de mise en réseau tels que Calico, MetalLB et Core DNS sont installés sur votre appareil Azure Stack Edge. MetalLB se raccorde au cluster Kubernetes s’exécutant sur votre appareil Azure Stack Edge et vous permet de créer des services Kubernetes de type `LoadBalancer` dans le cluster.

En mode BGP, toutes les machines du cluster établissent des sessions d’homologation BGP avec des routeurs proches que vous contrôlez et indiquent à ces routeurs comment transférer le trafic vers les IP de service. MetalLB avec le Border Gateway Protocol (BGP) n’est pas le mode de mise en réseau par défaut pour le cluster Kubernetes en cours d’exécution sur votre appareil. Pour configurer MetalLB via BGP, vous devez désigner le commutateur Top-of-rack (TDR) comme équilibreur de charge et configurer des sessions homologues. 

MetalLB en mode BGP peut être configuré pour obtenir des temps de basculement faibles si vous utilisez des appareils à 2 nœuds. Cette configuration est plus complexe que la configuration standard, car vous n’avez peut-être pas accès au commutateur en haut du rack.

## <a name="configure-metallb"></a>Configurer MetalLB

Vous pouvez configurer MetalLB en mode BGP en se connectant à l’interface PowerShell de l’appareil, puis en exécutant des cmdlets spécifiques.

### <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :
- Le calcul est activé sur un port de l’appareil. Cela crée un commutateur virtuel sur ce port. 
    - Pour activer le calcul, dans l’interface utilisateur locale de votre appareil, accédez à la page **Mise en réseau avancée** et sélectionnez le port sur lequel vous souhaitez activer le calcul. 
    - Dans la page **Paramètres réseau**, activez le port pour le calcul. **Appliquez** les paramètres.
- Vous avez des adresses IP disponibles dans le même sous-réseau que le port que vous avez activé pour le calcul sur votre appareil. 

### <a name="configuration"></a>Configuration

Pour une configuration de base pour MetalLB à l’aide de la session BGP, vous avez besoin des informations suivantes :

- Adresse IP de l’homologue à laquelle MetalLB doit se connecter.
- Numéro de système autonome (ASN) de l’homologue. BGP exige que les itinéraires soient annoncés avec un ASN pour les sessions homologues.
- L’ASN que MetalLB doit utiliser. Les ASN sont des nombres de 16 bits compris entre 1 et 65534 et de 32 bits entre 131072 et 4294967294.

> [!IMPORTANT]
> Pour que MetalLB fonctionne en mode BGP, les homologues doivent être spécifiés. Si aucun homologue BGP n’est spécifié, MetalLB fonctionnera en mode couche 2 par défaut. Pour plus d'informations, consultez [Mode de couche 2 dans MetalLB](https://metallb.universe.tf/concepts/layer2/). 


Procédez comme suit pour configurer MetalLB en mode BGP :

1. [Connectez-vous à l'interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) de l'appareil.
 
1. Exécutez la cmdlet `Get-HcsExternalVirtualSwitch` pour obtenir le nom du commutateur virtuel externe que vous allez utiliser pour le mode BGP. Ce commutateur virtuel est créé lorsque vous avez activé le port pour le calcul.

    ```powershell
    Get-HcsExternalVirtualSwitch
    ```
1. Exécutez la cmdlet `Set-HcsBGPPeer` pour établir une session d’homologue BGP.

    ```powershell
    Set-HcsBGPPeer -PeerAddress <IP address of the port that you enabled for compute> -PeerAsn <ASN for the peer> -SelfAsn <Your ASN> -SwitchName <Name of virtual switch on the port enabled for compute> -HoldTimeInSeconds <Optional hold time in seconds> 
    ```
1. Une fois que vous avez établi la session, exécutez la cmdlet `Get-HcsBGPPeers` pour obtenir les sessions homologues qui existent sur un commutateur virtuel.

    ```powershell
    Get-HcsBGPPeers -SwitchName <Name of virtual switch that you enabled for compute>
    ```
1. Exécutez la cmdlet `Remove-HcsBGPPeer` pour supprimer la session d’homologue. 

    ```powershell
    Remove-HcsBGPPeer -PeerAddress <IP address of the port that you enabled for compute> -SwitchName <Name of virtual switch on the port enabled for compute>
    ```
1. Exécutez `Get-HcsBGPPeers` pour vérifier que la session d’homologue est supprimée.

Voici un exemple de sortie : 

```powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $Name = "dbe-1csphq2.microsoftdatabox.com"
PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
PS C:\WINDOWS\system32> $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions
WARNING: The Windows PowerShell interface of your device is intended to
be used only for the initial network configuration. Please
engage Microsoft Support if you need to access this interface
to troubleshoot any potential issues you may be experiencing.
Changes made through this interface without involving Microsoft
Support could result in an unsupported configuration.
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : False
MacAddressPools               :
IPAddressPools                : {}
BGPPeers                      :
ConfigurationSource           : Dsc
EnabledForCompute             : False
EnabledForStorage             : False
EnabledForMgmt                : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 3cb2d0ae-6a7b-44cc-8a5d-8eac2d1c0436
VirtualNetworks               : {}
EnableEmbeddedTeaming         : True
Vnics                         : {}
Type                          : External

Name                          : vSwitch2
InterfaceAlias                : {Port3, Port4}
EnableIov                     : False
MacAddressPools               :
IPAddressPools                : {}
BGPPeers                      :
ConfigurationSource           : Dsc
EnabledForCompute             : False
EnabledForStorage             : True
EnabledForMgmt                : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 8dd480c0-8f22-42b1-8621-d2a43f70690d
VirtualNetworks               : {}
EnableEmbeddedTeaming         : True
Vnics                         : {}
Type                          : External

[dbe-1csphq2.microsoftdatabox.com]: PS>Set-HcsBGPPeer -PeerAddress 10.126.77.125 -PeerAsn 64512 -SelfAsn 64513 -SwitchName vSwitch1 -HoldTimeInSeconds 15
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsBGPPeers -SwitchName vSwitch1

PeerAddress   PeerAsn SelfAsn HoldTime
-----------   ------- ------- --------
10.126.77.125 64512   64513         15

[dbe-1csphq2.microsoftdatabox.com]: PS>Remove-HcsBGPPeer -PeerAddress 10.126.77.125 -SwitchName vSwitch1
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsBGPPeers -SwitchName vSwitch1
[dbe-1csphq2.microsoftdatabox.com]: PS>
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [mise en réseau sur un cluster Kubernetes sur votre appareil Azure Stack Edge](azure-stack-edge-gpu-kubernetes-networking.md).