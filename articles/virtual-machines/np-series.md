---
title: Série NP - Machines virtuelles Azure
description: Spécifications pour les machines virtuelles de la série NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 4a366d704e942c74bfa32e9a0819cfda2126b2d1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456610"
---
# <a name="np-series"></a>Série NP 

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les machines virtuelles de la série NP sont alimentées par des FPGA [Xilinx U250](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) pour accélérer les charges de travail, notamment l’inférence machine learning, le transcodage vidéo et la recherche de base de données et Analytics. Les machines virtuelles de la série NP sont également alimentées par des processeurs Intel Xeon 8171M (Skylake) avec une vitesse d’horloge de Turbo de 3,2 GHz.

[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Non pris en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Non pris en charge<br>
Génération de machine virtuelle prise en charge : Génération 1<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge<br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | FPGA | Mémoire FPGA : Gio | Disques de données max. | Nombre max. de cartes réseau/Bande passante réseau attendue (Mbits/s) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Forum aux questions

**Q :** Comment demander un quota pour les machines virtuelles NP ?

**R :** Veuillez suivre cette page pour [augmenter les limites par série de machines virtuelles](../azure-portal/supportability/per-vm-quota-requests.md). Les machines virtuelles NP sont disponibles dans les régions USA Est, USA Ouest 2, Europe Ouest et Asie Sud-Est.

**Q :** Quelle version de Vitis dois-je utiliser ? 

**R :** Xilinx recommande [Vitis 2020.2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html). Vous pouvez également utiliser les options de la place de marché de machines virtuelles de développement (machine virtuelle de développement Vitis 2020.2 pour Ubuntu 18.04 et CentOS 7.8).

**Q :** Ai-je besoin d’utiliser des machines virtuelles NP pour développer ma solution ? 

**R :** Non, vous pouvez la développer en local, puis la déployer sur le cloud. Veillez à suivre la [documentation d'attestation](./field-programmable-gate-arrays-attestation.md) pour déployer sur des machines virtuelles NP. 

**Q :** Quel fichier renvoyé par l'attestation dois-je utiliser lors de la programmation de mon FPGA dans une machine virtuelle NP ?

**R :** L'attestation renvoie deux fichiers, **design.bit.xclbin** et **design.azure.xclbin**. Utilisez **design.azure.xclbin**.

**Q :** Où puis-je trouver tous les fichiers XRT/Platform ?

**R :** Pour accéder à l'ensemble des fichiers, visitez le site [Microsoft-Azure](https://www.xilinx.com/microsoft-azure.html) de Xilinx.

**Q :** Quelle version de XRT dois-je utiliser ?

**R :** xrt_202020.2.8.832 

**Q :** Quelle est la plateforme de déploiement cible ?

**R :** Utilisez les plateformes suivantes.
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**Q :** Quelle plateforme dois-je cibler pour le développement ?

**R :** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**Q :** Quels sont les systèmes d’exploitation pris en charge ? 

**R :** Xilinx et Microsoft ont validé Ubuntu 18.04 LTS and CentOS 7.8.

 Xilinx a créé les images de la place de marché suivantes pour simplifier le déploiement de ces machines virtuelles. 

[Machine virtuelle de déploiement Xilinx Alveo U250 – Ubuntu 18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Machine virtuelle de déploiement Xilinx Alveo U250 – CentOS 7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**Q :** Puis-je déployer mes propres machines virtuelles Ubuntu/CentOS et installer la plateforme cible XRT/Deployment ? 

**R :** Oui.

**Q :** Si je déploie ma propre machine virtuelle Ubuntu 18.04, quels sont les packages requis et la procédure à suivre ?

**R :** Utilisez Kernel 4.1X comme indiqué dans la [documentation de Xilinx XRT](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Installez les packages suivants :
- xrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all.deb  

**Q :** Sur Ubuntu, après le redémarrage de ma machine virtuelle, je ne trouve pas mon ou mes FPGA : 

**R :** Vérifiez que votre noyau n’a pas été mis à niveau (uname-a). Dans ce cas, passez à une version antérieure à Kernel 4.1 X. 

**Q :** Si je déploie ma propre machine virtuelle CentOS 7.8, quels sont les packages requis et la procédure à suivre ?

**R :** Utilisez la version de Kernel 3.10.0-1160.15.2.el7.x86_64

 Installez les packages suivants :
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-validate-2.1-3005608.1.noarch.rpm  

**Q :** Lors de l’exécution de xbutil Validate sur CentOS, j’obtiens le message d’avertissement suivant : « WARNING: Kernel version 3.10.0-1160.15.2.el7.x86_64 is not officially supported. 4.18.0-193 is the latest supported version. » (« ATTENTION : la version 3.10.0-1160.15.2.el7.x86_64 de Kernel n’est pas officiellement prise en charge, la dernière version prise en charge est la 4.18.0-193. ») 

**R :** Vous pouvez ignorer ce message. 

**Q :** Quelles sont les différences entre les machines virtuelles locales (OnPrem) et les machines virtuelles NP ?

**R :**  
<br>
<b>- À propos de XOCL/XCLMGMT : </b>
<br>
Sur des machines virtuelles Azure NP, seul le point de terminaison de rôle (ID de périphérique 5005), qui utilise le pilote XOCL, est présent.

Sur un FPGA local, tant le point de terminaison de gestion (ID de périphérique 5004) que le point de terminaison de rôle (ID de périphérique 5005), qui utilisent respectivement les pilotes XCLMGMT et XOCL, sont présents.

<br>
<b>- À propos de XRT : </b>
<br>
Sur des machines virtuelles Azure, la plateforme XDMA 2.1 prend en charge uniquement les fonctionnalités de conservation des données Host_Mem(SB) et DDR. 
<br>
Pour activer Host_Mem(SB) (jusqu’à 1 Go de RAM) : sudo xbutil host_mem --enable --size 1g 

Pour désactiver Host_Mem(SB) : sudo xbutil host_mem --disable 

**Q :** Puis-je exécuter des commandes xbmgmt ? 

**R :** Non, sur les machines virtuelles Azure, il n’existe pas de prise en charge de la gestion directement à partir de la machine virtuelle Azure. 

 **Q :** Dois-je charger un PLP ? 

**R :** Non, le PLP est chargé automatiquement pour vous, il n’est donc pas nécessaire de le charger via des commandes xbmgmt. 

 
**Q :** Azure prend-il en charge différents PLP ? 

**R :** Pas pour l’instant. Nous prenons uniquement en charge les PLP fournis dans les packages de plateforme de déploiement. 

**Q :** Comment puis-je interroger les informations de PLP ? 

**R :** Vous devez exécuter la requête xbutil et examiner la partie inférieure. 



## <a name="other-sizes-and-information"></a>Autres tailles et informations

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

Calculatrice de prix : [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

Pour plus d’informations sur les types de disques, consultez [Quels sont les types de disque disponibles dans Azure ?](disks-types.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
