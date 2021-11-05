---
title: Azure Virtual Desktop pour Azure Stack HCI (version préliminaire) - Azure
description: Brève présentation d’Azure Virtual Desktop pour Azure Stack HCI (version préliminaire).
author: Heidilohr
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: helohr
manager: femila
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8b456b534dd176fd8dc6bcd3b31b2a713fd8d7a2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096288"
---
# <a name="azure-virtual-desktop-for-azure-stack-hci-preview"></a>Azure Virtual Desktop pour Azure Stack HCI (version préliminaire)

> [!IMPORTANT]
> Azure Virtual Desktop pour Azure Stack HCI est actuellement en version préliminaire.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Virtual Desktop pour Azure Stack HCI (version préliminaire), vous permet d’utiliser des hôtes de session d’Azure Virtual Desktop dans votre infrastructure HCI Azure Stack locale. Vous pouvez également utiliser Azure Virtual Desktop pour Azure Stack HCI pour gérer vos hôtes de session à partir du Portail Azure. Si vous disposez déjà d’un déploiement d’infrastructure VDI (Virtual Desktop Infrastructure) local, Azure Virtual Desktop pour Azure Stack HCI peut améliorer l’expérience de l’administrateur et de l’utilisateur final. Si vous utilisez déjà Azure Virtual Desktop dans le Cloud, vous pouvez étendre votre déploiement à votre infrastructure locale pour mieux répondre à vos besoins en matière de performances ou de localité des données.

Azure Virtual Desktop pour Azure Stack HCI est actuellement en version préliminaire publique. Azure Stack HCI ne prend actuellement pas en charge certaines fonctionnalités importantes d’Azure Virtual Desktop. En raison de ces limitations, nous vous déconseillons d’utiliser cette fonctionnalité pour les charges de travail de production.

## <a name="key-benefits"></a>Principaux avantages

Nous avons établi Azure Virtual Desktop pour Azure Stack HCI. La question reste : comment pouvez-vous l’utiliser ?

Avec Azure Virtual Desktop pour Azure Stack HCI, vous pouvez :

- Améliorez les performances pour les utilisateurs d’Azure Virtual Desktop dans des domaines avec une connectivité médiocre au cloud public Azure en leur donnant des hôtes de session plus proches de leur emplacement.

- Respectez les exigences de la localité des données en conservant les données de l’application et de l’utilisateur localement.  Pour plus d’informations, consultez [Emplacements des données pour Azure Virtual Desktop](data-locations.md).

- Améliorez l’accès aux applications locales héritées et aux sources de données en conservant des bureaux virtuels et des applications au même emplacement.

- Réduisez les coûts et améliorez l’expérience utilisateur grâce aux bureaux virtuels multisessions Windows 10 et Windows 11 Entreprise.

- Simplifiez le déploiement et la gestion de l’infrastructure VDI par rapport aux solutions VDI locales traditionnelles en utilisant le Portail Azure.

## <a name="pricing"></a>Tarifs

Les éléments suivants affectent la somme des coûts d’exécution d’Azure Virtual Desktop pour Azure Stack HCI :
 - Coûts de l’infrastructure. Vous payez des frais de service mensuels pour Azure Stack HCI. Pour en savoir plus, consultez [Tarification Azure Stack HCI](https://azure.microsoft.com/pricing/details/azure-stack/hci/).
 
- Droits d’accès utilisateur pour Azure Virtual Desktop. Les licences qui accordent l’accès à Azure Virtual Desktop dans le Cloud s’appliquent également à Azure Virtual Desktop pour Azure Stack HCI. En savoir plus sur [la tarification d’Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

- Frais liés au service hybride d’Azure Virtual Desktop. Ces frais vous obligent à payer pour chaque UC virtuelle active des hôtes de session d’Azure Virtual Desktop que vous exécutez sur Azure Stack HCI. Ces frais seront activés une fois la période d’évaluation terminée.

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Nous avons pris en compte les problèmes suivants qui affectent la version préliminaire publique d’Azure Virtual Desktop pour Azure Stack HCI :

- Les déploiements d’Azure Virtual Desktop utilisant des [Shortpath RDP](shortpath.md) ne peuvent pas se connecter actuellement aux hôtes de session s’exécutant sur Azure Stack HCI.

- Les pools d’hôtes HCI Azure Stack ne prennent pas actuellement en charge les fonctionnalités d’Azure Virtual Desktop suivantes :
    
    - [Azure Monitor pour Azure Virtual Desktop](azure-monitor.md)
    - [Mise à l’échelle de l’hôte de session avec Azure Automation](set-up-scaling-script.md)
    - [Mise à l’échelle automatique (préversion)](autoscale-scaling-plan.md)
    - [Démarrer la machine virtuelle à la connexion](start-virtual-machine-connect.md)
    - [Redirection multimédia (préversion)](multimedia-redirection.md)
    - [Tarification de l’accès par utilisateur](./remote-app-streaming/licensing.md)

- L’onglet Azure Virtual Desktop dans le Portail Azure ne peut pas créer de nouvelles machines virtuelles directement sur Azure Stack HCI. Au lieu de cela, les administrateurs doivent créer des machines virtuelles locales séparément, puis les inscrire auprès d’un pool d’hôtes d’Azure Virtual Desktop.

- Lors de la connexion à un bureau virtuel multisession Windows 10 ou 11 Enterprise, les utilisateurs peuvent voir un message indiquant « activation nécessaire », même s’ils disposent d’une licence éligible.

- Azure Virtual Desktop pour Azure Stack HCI ne prend actuellement pas en charge les pools d’hôtes contenant à la fois des hôtes de session Cloud et locaux. Chaque pool hôte dans le déploiement ne doit avoir qu’un seul type de pool hôte.

- Les hôtes de session sur Azure Stack HCI ne prennent pas en charge certains services Azure Cloud uniquement.

- Étant donné qu’Azure Stack HCI prend en charge un grand nombre de types de matériel et de fonctionnalités de mise en réseau locale, les performances et la densité des utilisateurs peuvent varier considérablement entre les hôtes de session s’exécutant dans le Cloud Azure. Les [règles de dimensionnement des machines virtuelles](/windows-server/remote/remote-desktop-services/virtual-machine-recs) Azure Virtual Desktop sont larges. Vous devez donc les utiliser uniquement pour les estimations de performances initiales.

Si vous rencontrez des problèmes lors de la version préliminaire qui ne figurent pas dans cette liste, nous vous encourageons à les signaler.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous êtes familiarisé avec Azure Virtual Desktop pour Azure Stack HCI, Découvrez comment déployer cette fonctionnalité dans [Configurer Azure Virtual Desktop pour Azure Stack HCI (version préliminaire)](azure-stack-hci.md).
