---
title: Vue d’ensemble d’App Service Environment
description: Vue d’ensemble de l’environnement App Service Environment
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 09/07/2021
ms.author: ccompy
ms.custom: references_regions
ms.openlocfilehash: 99700ec8b58a1eb9ab1465cca915216748a74dec
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435640"
---
# <a name="app-service-environment-overview"></a>Vue d’ensemble d’App Service Environment 
> [!NOTE]
> Cet article concerne la fonctionnalité App Service Environment v3 qui est utilisée avec les plans App Service v2 Isolé
> 

L’environnement Azure App Service est une fonctionnalité d’Azure App Service qui fournit un environnement totalement isolé et dédié pour l’exécution sécurisée de vos applications App Service à grande échelle. Cette fonctionnalité peut héberger vos :

- Applications web Windows
- Applications web Linux
- Conteneurs Docker (Windows et Linux)
- Fonctions

Les environnements App Service (ASE) conviennent aux charges de travail d’application qui nécessitent :

- Scalabilité élevée.
- Une isolation et un accès réseau sécurisé.
- Une utilisation élevée de la mémoire.
- Nombre élevé de demandes par seconde (RPS). Vous pouvez créer plusieurs environnements ASE au sein d’une même région Azure ou dans plusieurs régions Azure. Grâce à cette souplesse, un environnement ASE est parfait pour la scalabilité horizontale des applications sans état avec des exigences RPS élevées.

L’environnement ASE héberge des applications d’un seul client, et le fait sur l’un de ses réseaux virtuels. Les clients peuvent contrôler précisément le trafic réseau entrant et sortant des applications. Les applications peuvent établir des connexions sécurisées à haute vitesse aux ressources d’entreprise locales par le biais de réseaux privés virtuels.

## <a name="usage-scenarios"></a>Scénarios d’usage

L’environnement App Service Environment peut être utilisé dans de nombreux cas, notamment :

- Applications métier internes
- Applications qui ont besoin de plus de 30 instances ASP
- Système monolocataire pour respecter les exigences de conformité ou de sécurité interne
- Hébergement d’application isolée du réseau
- Applications multicouches

Il existe de nombreuses fonctionnalités de mise en réseau qui permettent aux applications dans l’instance App Service multilocataire d’atteindre des ressources isolées du réseau ou d’être isolées du réseau elles-mêmes. Ces fonctionnalités sont activées au niveau de l’application.  Avec un environnement ASE, aucune configuration supplémentaire n’est nécessaire pour que les applications soient incluses dans le réseau virtuel. Les applications sont déployées dans un environnement isolé du réseau qui se trouve déjà dans un réseau virtuel. Non seulement l’environnement ASE héberge des applications isolées du réseau, mais il s’agit également d’un système monolocataire. Aucun autre client n’utilise l’environnement ASE. Si vous avez vraiment besoin d’un scénario d’isolation complet, vous pouvez également déployer votre environnement ASE sur du matériel dédié. 

## <a name="dedicated-environment"></a>Environnement dédié

L’environnement ASE est un déploiement monolocataire d’Azure App Service, qui s’exécute dans votre réseau virtuel. 

Les applications sont hébergées dans des plans App Service, qui sont créés dans un environnement ASE (App Service Environment). Le plan App Service est essentiellement un profil de provisionnement pour un hôte d’application. Lorsque vous mettez à l’échelle votre plan App Service, vous créez davantage d’hôtes d’application avec toutes les applications de ce plan App Service sur chaque ordinateur hôte. Un ASEv3 unique peut avoir jusqu’à un total de 200 instances de plan App Service sur l’ensemble des plans App Service combinés. Un plan App Service v2 isolé unique peut avoir jusqu’à 100 instances. 

## <a name="virtual-network-support"></a>Prise en charge des réseaux virtuels

La fonctionnalité ASE est un déploiement d’Azure App Service dans le sous-réseau d’un réseau virtuel (VNet) Azure Resource Manager d’un client. Lorsque vous déployez une application dans un environnement ASE, l’application est exposée sur l’adresse entrante assignée à l’ASE. Si votre environnement ASE est déployé avec une adresse IP virtuelle interne, l’adresse entrante de toutes les applications sera une adresse dans le sous-réseau de l’environnement ASE. Si votre environnement ASE est déployé avec une adresse IP virtuelle externe, l’adresse entrante est une adresse Internet adressable et vos applications se trouvent dans le DNS public. 

Le nombre d’adresses utilisées par un ASEv3 dans son sous-réseau varie selon le nombre d’instances dont vous disposez avec la quantité de trafic. Il existe des rôles d’infrastructure qui sont mis à l’échelle automatiquement en fonction du nombre de plans App Service et de la charge. La taille recommandée pour votre sous-réseau ASEv3 est un bloc CIDR/24 avec 256 adresses dans celui-ci, car il peut héberger un ASEv3 mis à l’échelle en fonction de sa limite.

Les applications d’un environnement ASE n’ont pas besoin de fonctionnalités activées pour accéder aux ressources dans le même réseau virtuel que l’ASE. Si le réseau virtuel de l’ASE est connecté à un autre réseau, les applications de l’ASE peuvent accéder aux ressources de ces réseaux étendus. Le trafic peut être bloqué par la configuration de l’utilisateur sur le réseau. 

La version multi-locataire de Azure App Service contient de nombreuses fonctionnalités permettant à vos applications de se connecter à vos différents réseaux. Ces fonctionnalités de mise en réseau permettent à vos applications d’agir comme si elles étaient déployées dans un réseau virtuel. Les applications d’un ASEv3 n’ont pas besoin d’être configurées dans le réseau virtuel. L’un des avantages de l’utilisation d’un environnement ASE par rapport au service multi-locataire réside dans le fait que tout contrôle d’accès réseau aux applications hébergées par l’ASE est externe à la configuration de l’application. Avec les applications du service multi-locataire, vous devez activer les fonctionnalités sur une application par application et utiliser RBAC ou la stratégie pour empêcher toute modification de la configuration. 

## <a name="feature-differences"></a>Différences de fonctionnalités

Par rapport aux versions antérieures de l’environnement ASE, il existe quelques différences avec ASEv3. Avec ASEv3 :

- Il n’existe aucune dépendance de mise en réseau dans le réseau virtuel client. Vous pouvez sécuriser toutes les transactions entrantes et sortantes en fonction de vos besoins. Le trafic sortant peut également être routé comme vous le souhaitez. 
- Vous pouvez le déployer pour la redondance de zone. La redondance de zone peut uniquement être définie lors de la création de ASEv3 et uniquement dans les régions où toutes les dépendances ASEv3 sont redondantes dans une zone. 
- Vous pouvez effectuer le déploiement sur un groupe hôte dédié. Les déploiements de groupe hôte ne sont pas redondants dans une zone. 
- La mise à l’échelle est beaucoup plus rapide qu’avec ASEv2. Même si la mise à l’échelle n’est pas immédiate comme dans le service multi-locataire, elle est beaucoup plus rapide.
- Les réglages de mise à l’échelle du serveur frontal ne sont plus nécessaires. Les serveurs frontaux ASEv3 sont automatiquement mis à l’échelle pour répondre aux besoins et sont déployés sur de meilleurs hôtes. 
- La mise à l’échelle ne bloque plus les autres opérations de mise à l’échelle au sein de l’instance ASEv3. Une seule opération de mise à l’échelle peut être appliquée pour une combinaison de système d’exploitation et de taille. Par exemple, si votre plan App Service Windows Small a été mis à l’échelle, vous pouvez lancer une opération de mise à l’échelle pour qu’elle s’exécute en même temps sur un support Windows ou autre chose que Windows Small. 
- Les applications d’un ASEV3 d’adresse IP virtuelle interne peuvent être atteintes sur le peering mondial. L’accès via le peering mondial n’était pas possible avec ASEv2. 

Certaines fonctionnalités ne sont pas disponibles dans ASEv3 alors qu’elles l’étaient dans les versions antérieures d’ASE. Dans ASEv3, vous ne pouvez pas :

- envoyer de trafic SMTP Vous pouvez toujours bénéficier des alertes déclenchées par e-mail, mais votre application ne peut pas envoyer de trafic sortant sur le port 25
- déployer vos applications avec FTP
- utiliser le débogage à distance avec vos applications
- effectuer la mise à niveau à partir d’ASEv2
- surveiller votre trafic avec Network Watcher ou NSG Flow
- configurer une liaison TLS/SSL basée sur IP avec vos applications

## <a name="pricing"></a>Tarifs 

Avec ASEv3, il existe un modèle de tarification différent selon le type de déploiement d’ASE. Les trois modèles de tarification sont les suivants : 

- **ASEv3** : si l’environnement ASE est vide, les coûts correspondent à un ASP avec une instance de Windows I1v2. La facturation d’une instance n’est pas un coût supplémentaire, mais elle est appliquée uniquement si ASE est vide.
- **Zone de disponibilité ASEv3** : il existe des frais minimum pour neuf instances Windows I1v2. Aucun frais n’est facturé pour la prise en charge des zones de disponibilité si vous disposez d’au moins neuf instances de plan App Service. Tous les plans App Service dans un AZ ASEv3 ont également un nombre d’instances minimal de 3 pour s’assurer qu’il existe une instance dans chaque zone de disponibilité. À mesure du scale-out des plans, ceux-ci sont répartis entre les zones de disponibilité. 
- **Hôte dédié ASEv3** : avec un déploiement d’hôte dédié, deux hôtes dédiés vous sont facturés, conformément à notre tarification à la création de ASEv3, puis un petit pourcentage du tarif Isolé v2 par cœur est facturé quand vous effectuez la mise à l’échelle.

La tarification des instances réservées Isolé v2 est disponible et est décrite dans [Comment les remises de réservation s’appliquent à Azure App Service][reservedinstances]. Des informations sur la tarification, notamment sur la tarification des instances réservées, sont disponibles sous [Tarification d’App Service][pricing] sous **Plan Isolé v2**. 

## <a name="regions"></a>Régions

ASEv3 est disponible dans les régions suivantes. 

|Régions ASEv3 d’hôte normales et dédiées|   Régions ASEv3 de zones de disponibilité|
|---------------------------------------|------------------|
|Australie Est|    Australie Est|
|Australie Sud-Est|Brésil Sud|
|Brésil Sud   |Centre du Canada|
|Centre du Canada|USA Centre|
|Inde centrale  |USA Est|
|USA Centre |USA Est 2|
|Asie Est  | France Centre|
|USA Est    | Allemagne Centre-Ouest|
|USA Est 2| Japon Est|
|France Centre | Europe Nord|
|Allemagne Centre-Ouest   |   États-Unis - partie centrale méridionale|
|Japon Est | Asie Sud-Est|
|Centre de la Corée  | Sud du Royaume-Uni|
|Centre-Nord des États-Unis   | Europe Ouest|
|Europe Nord   | USA Ouest 2|
|Norvège Est    | |
|Afrique du Sud Nord | |
|États-Unis - partie centrale méridionale   | |
|Asie Sud-Est| |
|Suisse Nord  | | 
|Émirats arabes unis Nord| |   
|Sud du Royaume-Uni| |    
|Ouest du Royaume-Uni| |
|Centre-USA Ouest    | | 
|Europe Ouest    | |
|USA Ouest    | | 
|USA Ouest 2| |
|USA Ouest 3| |

<!--Links-->
[reservedinstances]: ../../cost-management-billing/reservations/reservation-discount-app-service.md
[pricing]: https://azure.microsoft.com/pricing/details/app-service/windows/
