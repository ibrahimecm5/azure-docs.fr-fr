---
title: Déployer Citrix sur Azure VMware Solution
description: Découvrez comment déployer VMware Citrix sur Azure VMware Solution.
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d3acd7ac8772a3d6e501b43f7277bcb57d8cd08
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096202"
---
# <a name="deploy-citrix-on-azure-vmware-solution"></a>Déployer Citrix sur Azure VMware Solution

Le service Citrix Virtual Apps and Desktops prend en charge Azure VMware Solution. Azure VMware Solution fournit une infrastructure cloud contenant des clusters vSphere créés par l’infrastructure Azure. Vous pouvez tirer parti du service Citrix Virtual Apps and Desktops afin d’utiliser Azure VMware Solution pour provisionner votre charge de travail [VDA (Virtual Delivery Agent)](https://www.citrix.com/downloads/xendesktop/components/xendesktop-and-xenapp-76-VDA.html) comme si vous utilisiez vSphere dans des environnements locaux. 

[En savoir plus sur Citrix Virtual Apps and Desktops](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/)

[Guide de déploiement](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-vmware-solution-avs-integration)

[Présentation de la solution](https://www.citrix.com/content/dam/citrix/en_us/documents/solution-brief/citrix-virtual-apps-and-desktop-service-on-azure-vmware-solution.pdf)

**Questions fréquentes (FAQ)**
 
- Q. Puis-je migrer mes bureaux et applications Citrix existants vers Azure VMware Solution ou utiliser un environnement hybride qui se compose de charges de travail Citrix locales et basées sur Azure VMware Solution ? 

    R. Oui. Vous pouvez utiliser les images de machine, packages d’application et processus que vous utilisez actuellement. Vous pouvez lier des environnements locaux et basés sur Azure VMware Solution indifféremment pour une migration.

- Q. Citrix peut-il être déployé comme environnement autonome au sein d’Azure VMware Solution ? 

    R. Oui. Vous pouvez librement migrer, utiliser un environnement hybride ou déployer un environnement autonome directement dans Azure VMware Solution. 

- Q. Azure VMware Solution prend-il en charge PVS et MCS ? 

    R. Oui 

- Q. Les charges de travail basées sur le GPU sont-elles prises en charge dans Citrix sur Azure VMware Solution ? 

    R. Pas pour l'instant. Toutefois, si ce cas d’utilisation est important pour vous, sachez que les charges de travail Citrix sur Microsoft Azure prennent en charge le GPU. 

- Q. Azure VMware Solution est-il pris en charge avec les déploiements Citrix locaux ou le programme LTSR ?  

    R. Non.  Azure VMware Solution est pris en charge uniquement avec les offres de service Citrix Virtual Apps and Desktops.  

- Q. Qui puis-je appeler pour bénéficier du support ? 

    R. Pour obtenir de l’aide, les clients doivent contacter le support Citrix en utilisant la page www.citrix.com/support. 

- Q. Puis-je utiliser mon avantage Azure Virtual Desktop de Microsoft avec Citrix sur Azure VMware Solution ?
 
    R. Non. Les avantages Azure Virtual Desktop s’appliquent uniquement aux charges de travail Microsoft Azure natives. Avec le service Citrix Virtual Apps and Desktops, offre Azure native, votre avantage Azure Virtual Desktop peut s’appliquer avec votre déploiement Azure VMware Solution. 

- Q. Comment puis-je acheter le service Citrix Virtual Apps and Desktops pour utiliser Azure VMware Solution ? 
    
    R. Vous pouvez acheter des offres Citrix par le biais de votre partenaire Citrix ou directement à partir de la Place de Marché Azure. 
