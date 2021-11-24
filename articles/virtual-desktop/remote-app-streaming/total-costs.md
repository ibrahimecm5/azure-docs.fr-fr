---
title: Estimer le coût total d'un déploiement Azure Virtual Desktop - Azure
description: Apprenez à estimer le coût total d'un déploiement Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3daabe070d0745c95bc16582262897da9f4c5aae
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399501"
---
# <a name="understanding-total-azure-virtual-desktop-deployment-costs"></a>Estimer le coût total d'un déploiement Azure Virtual Desktop

Les coûts liés à Azure Virtual Desktop reposent à la fois sur la consommation des ressources Azure sous-jacentes et sur les licences. Ces coûts sont facturés à l'organisation propriétaire du déploiement Azure Virtual Desktop, et non aux utilisateurs finaux qui accèdent aux ressources du déploiement. Certains frais de licence doivent être payés d'avance. Les autres frais liés aux licences et à la consommation des ressources sous-jacentes sont basés sur des compteurs qui enregistrent votre utilisation.

Dans cet article, nous allons vous présenter les coûts liés à la consommation et aux licences, et vous apprendre à estimer les coûts de service à l'aide de la Calculatrice de prix Azure avant de déployer Azure Virtual Desktop. Cet article comprend également des instructions sur l'utilisation d'Azure Cost Management pour afficher les coûts après le déploiement d'Azure Virtual Desktop.

>[!NOTE]
>Le client qui paie le déploiement Azure Virtual Desktop est responsable de la gestion des ressources et des coûts liés à ce déploiement pendant toute la durée de vie de celui-ci. Si le propriétaire n'a plus besoin des ressources connectées à son déploiement Azure Virtual Desktop, il doit veiller à ce qu'elles soient correctement supprimées. Pour plus d'informations, consultez [Gérer les ressources Azure à l'aide du portail Azure](../../azure-resource-manager/management/manage-resources-portal.md).

## <a name="consumption-costs"></a>Coûts de consommation

Les coûts de consommation correspondent à la somme de tous les frais d'utilisation des ressources Azure pour les utilisateurs accédant à un pool d'hôtes Azure Virtual Desktop. Ces frais proviennent des machines virtuelles hôtes de session situées à l'intérieur des pools d'hôtes, notamment des ressources partagées par d'autres produits dans Azure et tout système de gestion des identités qui nécessite l'exécution d'une infrastructure supplémentaire pour que le service reste disponible, comme un contrôleur de domaine pour Active Directory Domain Services (AD DS).

### <a name="session-host-vm-costs"></a>Coûts des machines virtuelles hôtes de session

Dans Azure Virtual Desktop, les machines virtuelles hôtes de session utilisent les trois services Azure suivants :

- Machines virtuelles (calcul)
- Stockage pour les disques managés (y compris le stockage du système d'exploitation par machine virtuelle et tous les disques de données pour les bureaux personnels)
- Bande passante (mise en réseau)

Ces frais peuvent être consultés au niveau du groupe de ressources Azure où sont attribuées les ressources spécifiques au pool d'hôtes, y compris les machines virtuelles hôtes de session. Si un ou plusieurs pools d'hôtes sont également configurés pour utiliser le service payant Log Analytics afin d'envoyer des données de machines virtuelles à la fonctionnalité facultative Azure Virtual Desktop Insights, votre facture englobera également le service Log Analytics pour les groupes de ressources Azure correspondants. Pour plus d'informations, vous pouvez consulter [Surveiller les estimations de tarification des coûts de Windows Virtual Desktop](../azure-monitor-costs.md).

Parmi les trois principaux coûts d'utilisation des hôtes de session de machine virtuelle énumérés au début de cette section, le calcul arrive généralement en tête. Pour réduire les coûts de calcul et optimiser la demande de ressources en fonction de la disponibilité, de nombreux clients choisissent de [mettre automatiquement à l'échelle les hôtes de session](../set-up-scaling-script.md).

### <a name="domain-controller-costs-for-active-directories"></a>Coûts des contrôleurs de domaine pour les instances d'Active Directory

Les machines virtuelles contrôleurs de domaine utilisent au minimum les quatre services Azure suivants :

- Machines virtuelles (calcul)
- Stockage pour les disques managés (y compris le stockage du système d'exploitation par machine virtuelle et tous les disques de données pour les bureaux personnels)
- Bande passante (mise en réseau)
- Réseaux virtuels

Si votre déploiement Azure Virtual Desktop dépend d'un contrôleur de domaine pour exécuter Active Directory, vous devez l'inclure dans le coût total du déploiement Azure Virtual Desktop. Les contrôleurs de domaine hébergés dans Azure partageront également les trois services Azure pour les machines virtuelles hôtes de session décrites dans la section [Coûts des machines virtuelles hôtes de session](#session-host-vm-costs), car une machine virtuelle Azure standard doit également maintenir à disposition les identités d'Active Directory.

Les contrôleurs de domaine présentent toutefois quelques différences clés par rapport aux machines virtuelles hôtes de session :

- Les contrôleurs de domaine génèrent des frais de réseau virtuel supplémentaires car ils doivent communiquer avec d'autres services en dehors du déploiement.
- La mise à l'échelle de la disponibilité des contrôleurs de domaine peut poser des problèmes car vos déploiements requièrent que vos contrôleurs de domaine soient toujours disponibles.

### <a name="shared-service-costs"></a>Coûts des services partagés

Selon les fonctionnalités utilisées par votre déploiement Azure Virtual Desktop, vous devrez peut-être également payer du stockage Azure pour toute combinaison des fonctionnalités facultatives suivantes :

- [Attachement d’application MSIX](../what-is-app-attach.md)
- [Images de système d'exploitation personnalisées](../set-up-customize-master-image.md)
- [Profils FSLogix](../fslogix-containers-azure-files.md)

Ces fonctionnalités utilisent des options de stockage Azure comme [Azure Files](../../storage/files/storage-files-introduction.md) et [Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-introduction.md), ce qui signifie qu'elles peuvent partager leur stockage avec d'autres services Azure en plus d'Azure Virtual Desktop. Nous vous recommandons de créer un compte de stockage distinct dédié au stockage que vous achetez pour vos fonctionnalités Azure Virtual Desktop afin de pouvoir faire la différence entre les coûts de celui-ci et les coûts des autres services Azure que vous utilisez.

### <a name="user-access-costs"></a>Coûts d'accès utilisateur

Tous les mois, vous devez vous acquitter de frais d'accès pour chacun des utilisateurs qui se connectent à des applications ou à des bureaux au sein de votre déploiement Azure Virtual Desktop. Pour en savoir plus sur la tarification de l'accès par utilisateur d'Azure Virtual Desktop, consultez [Tarification des licences et de l'accès par utilisateur](licensing.md).

## <a name="predicting-costs-before-deploying-azure-virtual-desktop"></a>Prévoir les coûts avant de déployer Azure Virtual Desktop

Maintenant que vous connaissez les bases, passons à l'estimation. Pour ce faire, nous devons estimer les coûts de consommation et les coûts d'accès utilisateur.

### <a name="predicting-consumption-costs"></a>Prévoir les coûts de consommation

Avant de créer un déploiement, vous pouvez utiliser la [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts de consommation d'Azure Virtual Desktop en procédant comme suit :

1. Dans la Calculatrice de prix Azure, sélectionnez l'onglet **Calcul** pour afficher les options de calcul disponibles.

2. Sélectionnez **Azure Virtual Desktop**. Le module Calculatrice d'Azure Virtual Desktop doit apparaître.

3. Entrez les valeurs de votre déploiement dans les champs afin d'estimer votre facture Azure mensuelle en fonction de vos prévisions d'utilisation du calcul, du stockage et du réseau.

>[!NOTE]
>Actuellement, le module Calculatrice de prix Azure d'Azure Virtual Desktop permet uniquement d'estimer les coûts de consommation des machines virtuelles hôtes de session ainsi que le stockage supplémentaire agrégé de toutes les fonctionnalités Azure Virtual Desktop facultatives nécessitant un stockage. Vous pouvez toutefois ajouter des estimations relatives à d'autres fonctionnalités Azure Virtual Desktop dans des modules distincts sur la même page de la Calculatrice de prix Azure pour obtenir une estimation des coûts plus complète ou modulaire.
>
>Vous pouvez ajouter des modules Calculatrice de prix Azure supplémentaires pour estimer l'impact financier d'autres composants de votre déploiement, y compris, mais sans s'y limiter :
>
>- Contrôleurs de domaine
>- Autres fonctionnalités dépendantes du stockage, telles que les images de système d'exploitation personnalisées, l'attachement d'application MSIX et Azure Monitor

### <a name="predicting-user-access-costs"></a>Prévoir les coûts d'accès utilisateur

Les coûts d'accès utilisateur dépendent du nombre d'utilisateurs qui se connectent à votre déploiement tous les mois. Pour savoir comment estimer le coût total des accès utilisateur à attendre, consultez [Estimer les coûts de diffusion d'application par utilisateur pour Azure Virtual Desktop](streaming-costs.md).

## <a name="viewing-costs-after-deploying-azure-virtual-desktop"></a>Consulter les coûts après le déploiement d'Azure Virtual Desktop

Après avoir déployé Azure Virtual Desktop, vous pouvez utiliser [Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) pour consulter vos factures. Cette section indique comment rechercher les prix de vos services actuels.

### <a name="viewing-bills-for-consumption-costs"></a>Consulter les factures relatives aux coûts de consommation

Avec les autorisations [Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md) appropriées, les utilisateurs de votre organisation, tels que les administrateurs de facturation, peuvent utiliser des [outils d'analyse des coûts](../../cost-management-billing/costs/cost-analysis-common-uses.md) et rechercher des factures Azure via [Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) pour suivre les coûts de consommation mensuels d'Azure Virtual Desktop dans le cadre de vos abonnements Azure.

### <a name="viewing-bills-for-user-access-costs"></a>Consulter les factures relatives aux coûts d'accès utilisateur

À chaque cycle de facturation, les coûts d'accès utilisateur apparaîtront sur la facture Azure de tout abonnement inscrit, aux côtés des coûts de consommation et des autres frais Azure.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez vous faire une idée plus précise du coût de certaines parties de votre déploiement, consultez les articles suivants :

- [Compréhension de la tarification des licences et de l’accès par utilisateur](licensing.md)
- [Estimer les coûts de diffusion d’application par utilisateur pour Azure Virtual Desktop](streaming-costs.md)
- [Baliser les ressources Azure Virtual Desktop pour gérer les coûts](../tag-virtual-desktop-resources.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)