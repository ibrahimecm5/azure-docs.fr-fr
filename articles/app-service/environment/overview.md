---
title: Vue d’ensemble d’App Service Environment
description: Vue d’ensemble de l’environnement App Service Environment
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.custom: references_regions
ms.openlocfilehash: 7530925ac23d8b6a25d5d74cdf5a6531cddb0edf
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524015"
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
- Logic Apps (Standard)

Les environnements App Service (ASE) conviennent aux charges de travail d’application qui nécessitent :

- Scalabilité élevée.
- Une isolation et un accès réseau sécurisé.
- Une utilisation élevée de la mémoire.
- Nombre élevé de demandes par seconde (RPS). Vous pouvez créer plusieurs environnements ASE au sein d’une même région Azure ou dans plusieurs régions Azure. Grâce à cette souplesse, un environnement ASE est parfait pour la scalabilité horizontale des applications sans état avec des exigences RPS élevées.

Les ASE hébergent des applications d’un seul client, dans l’un de leurs réseaux virtuels. Les clients peuvent contrôler précisément le trafic réseau entrant et sortant des applications. Les applications peuvent établir des connexions sécurisées à haute vitesse aux ressources d’entreprise locales par le biais de réseaux privés virtuels.

## <a name="usage-scenarios"></a>Scénarios d’usage

L’environnement App Service Environment peut être utilisé dans de nombreux cas, notamment :

- Applications métier internes
- Applications qui nécessitent plus de 30 instances de plan App Service
- Système monolocataire pour respecter les exigences de conformité ou de sécurité interne
- Hébergement d’application isolée du réseau
- Applications multicouches

Il existe de nombreuses fonctionnalités de mise en réseau qui permettent aux applications dans l’instance App Service multilocataire d’atteindre des ressources isolées du réseau ou d’être isolées du réseau elles-mêmes. Ces fonctionnalités sont activées au niveau de l’application. Avec un ASE, aucune configuration supplémentaire n’est nécessaire pour ajouter les applications dans le réseau virtuel. Les applications sont déployées dans un environnement isolé du réseau qui se trouve déjà dans un réseau virtuel. Non seulement l’environnement ASE héberge des applications isolées du réseau, mais il s’agit également d’un système monolocataire. Aucun autre client n’utilise l’environnement ASE. Si vous avez vraiment besoin d’un scénario d’isolation complet, vous pouvez également déployer votre environnement ASE sur du matériel dédié.

## <a name="dedicated-environment"></a>Environnement dédié

L’environnement ASE est un déploiement monolocataire d’Azure App Service, qui s’exécute dans votre réseau virtuel. 

Les applications sont hébergées dans des plans App Service, qui sont créés dans un environnement ASE (App Service Environment). Le plan App Service est essentiellement un profil de provisionnement pour un hôte d’application. Lorsque vous mettez à l’échelle votre plan App Service, vous créez davantage d’hôtes d’application avec toutes les applications de ce plan App Service sur chaque ordinateur hôte. Un ASEv3 unique peut avoir jusqu’à un total de 200 instances de plan App Service sur l’ensemble des plans App Service combinés. Un plan App Service v2 isolé unique peut avoir jusqu’à 100 instances.

## <a name="virtual-network-support"></a>Prise en charge des réseaux virtuels

La fonctionnalité ASE est un déploiement d’Azure App Service dans le sous-réseau d’un réseau virtuel d’un client. Lorsque vous déployez une application dans un environnement ASE, l’application est exposée sur l’adresse entrante assignée à l’ASE. Si votre ASE est déployé avec une IP virtuelle (VIPA) interne, l’adresse entrante de toutes les applications est une adresse dans le sous-réseau ASE. Si votre environnement ASE est déployé avec une adresse IP virtuelle externe, l’adresse entrante est une adresse Internet adressable et vos applications se trouvent dans le DNS public.

Le nombre d’adresses utilisées par un ASEv3 dans son sous-réseau varie selon le nombre d’instances dont vous disposez avec la quantité de trafic. Il existe des rôles d’infrastructure qui sont mis à l’échelle automatiquement en fonction du nombre de plans App Service et de la charge. La taille recommandée de votre sous-réseau ASEv3 est un bloc CIDR `/24` qui contient 256 adresses, car il peut héberger un ASEv3 avec un scale-out maximum.

Les applications d’un ASE n’ont pas besoin d’activation de fonctionnalités pour accéder aux ressources dans le même réseau virtuel que l’ASE. Si le réseau virtuel de l’ASE est connecté à un autre réseau, les applications de l’ASE peuvent accéder aux ressources de ces réseaux étendus. Le trafic peut être bloqué par la configuration de l’utilisateur sur le réseau.

La version multi-locataire de Azure App Service contient de nombreuses fonctionnalités permettant à vos applications de se connecter à vos différents réseaux. Ces fonctionnalités réseau permettent à vos applications d’agir comme si elles étaient déployées dans un réseau virtuel. Les applications d’un ASEv3 n’ont pas besoin de configuration pour faire partie du réseau virtuel. L’un des avantages de l’utilisation d’un environnement ASE par rapport au service multi-locataire réside dans le fait que tout contrôle d’accès réseau aux applications hébergées par l’ASE est externe à la configuration de l’application. Avec les applications du service multi-locataire, vous devez activer les fonctionnalités sur une application par application et utiliser RBAC ou la stratégie pour empêcher toute modification de la configuration.

## <a name="feature-differences"></a>Différences de fonctionnalités

Par rapport aux versions antérieures de l’environnement ASE, il existe quelques différences avec ASEv3. Avec ASEv3 :

- Il n’existe aucune dépendance de réseau dans le réseau virtuel du client. Vous pouvez sécuriser toutes les transactions entrantes et sortantes en fonction de vos besoins. Le trafic sortant peut également être routé comme vous le souhaitez. 
- Vous pouvez le déployer pour la redondance de zone. La redondance de zone peut uniquement être définie lors de la création de ASEv3 et uniquement dans les régions où toutes les dépendances ASEv3 sont redondantes dans une zone. 
- Vous pouvez effectuer le déploiement sur un groupe hôte dédié. Les déploiements de groupe hôte ne sont pas redondants dans une zone. 
- La mise à l’échelle est beaucoup plus rapide qu’avec ASEv2. Même si la mise à l’échelle n’est pas immédiate comme dans le service multi-locataire, elle est beaucoup plus rapide.
- Les réglages de mise à l’échelle du serveur frontal ne sont plus nécessaires. Les serveurs frontaux ASEv3 sont automatiquement mis à l’échelle pour répondre aux besoins et sont déployés sur de meilleurs hôtes. 
- La mise à l’échelle ne bloque plus les autres opérations de mise à l’échelle au sein de l’instance ASEv3. Une seule opération de mise à l’échelle peut être appliquée pour une combinaison de système d’exploitation et de taille. Par exemple, si votre plan App Service Windows Small a été mis à l’échelle, vous pouvez lancer une opération de mise à l’échelle pour qu’elle s’exécute en même temps sur un support Windows ou autre chose que Windows Small. 
- Les applications d’un ASEV3 d’adresse IP virtuelle interne peuvent être atteintes sur le peering mondial. L’accès avec le peering mondial n’est pas possible avec les précédentes versions.

Certaines fonctionnalités ne sont pas disponibles dans ASEv3 alors qu’elles l’étaient dans les versions antérieures d’ASE. Dans ASEv3, vous ne pouvez pas :

- envoyer de trafic SMTP Vous pouvez toujours bénéficier des alertes déclenchées par e-mail, mais votre application ne peut pas envoyer de trafic sortant sur le port 25
- déployer vos applications avec FTP
- utiliser le débogage à distance avec vos applications
- effectuer la mise à niveau à partir d’ASEv2
- surveiller votre trafic avec Network Watcher ou NSG Flow
- configurer une liaison TLS/SSL basée sur IP avec vos applications
- configurer un suffixe de domaine personnalisé

## <a name="pricing"></a>Tarifs

Avec ASEv3, il existe un modèle de tarification différent selon le type de déploiement d’ASE. Les trois modèles de tarification sont les suivants :

- **ASEv3** : si l’ASE est vide, les frais correspondent à ceux d’une instance de Windows I1v2. La facturation d’une instance n’est pas un coût supplémentaire, mais elle est appliquée uniquement si ASE est vide.
- **ASEv3 redondant interzone** : il y a un coût minimal de neuf instances. Aucun frais n’est facturé pour la prise en charge des zones de disponibilité si vous disposez d’au moins neuf instances de plan App Service. Si vous avez moins de neuf instances (de toutes tailles) dans l’ensemble de vos plans App Service dans l’ASE redondant interzone, la différence entre neuf et le nombre d’instances exécutées est facturée comme des instances Windows I1v2 supplémentaires.
- **Hôte dédié ASEv3** : avec un déploiement d’hôte dédié, deux hôtes dédiés vous sont facturés, conformément à notre tarification à la création de ASEv3, puis un petit pourcentage du tarif Isolé v2 par cœur est facturé quand vous effectuez la mise à l’échelle.

La tarification des instances réservées Isolé v2 est disponible et est décrite dans [Comment les remises de réservation s’appliquent à Azure App Service](../../cost-management-billing/reservations/reservation-discount-app-service.md). Des informations sur la tarification, notamment sur la tarification des instances réservées, sont disponibles sous [Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) sous **Plan Isolé v2**.

## <a name="regions"></a>Régions

ASEv3 est disponible dans les régions suivantes.

|Régions ASEv3 d’hôte normales et dédiées|Régions ASEv3 de zones de disponibilité|
|---------------------------------------|------------------|
|Australie Est|Australie Est|
|Sud-Australie Est|Brésil Sud|
|Brésil Sud|Centre du Canada|
|Centre du Canada|USA Centre|
|Inde centrale|USA Est|
|USA Centre|USA Est 2|
|Asie Est|France Centre|
|USA Est|Allemagne Centre-Ouest|
|USA Est 2|Japon Est|
|France Centre|Europe Nord|
|Allemagne Centre-Ouest|États-Unis - partie centrale méridionale|
|Japon Est|Asie Sud-Est|
|Centre de la Corée|Sud du Royaume-Uni|
|Centre-Nord des États-Unis|Europe Ouest|
|Europe Nord|USA Ouest 2|
|Norvège Est| |
|Afrique du Sud Nord| |
|États-Unis - partie centrale méridionale| |
|Asie Sud-Est| |
|Suisse Nord| |
|Émirats arabes unis Nord| |
|Sud du Royaume-Uni| |
|Ouest du Royaume-Uni| |
|Centre-USA Ouest| |
|Europe Ouest| |
|USA Ouest| |
|USA Ouest 2| |
|USA Ouest 3| |
|Gouvernement des États-Unis – Texas| |
|Gouvernement des États-Unis – Arizona| |
|Gouvernement américain - Virginie| |

## <a name="app-service-environment-v2"></a>App Service Environment v2

App Service Environment a trois versions : ASEv1, ASEv2 et ASEv3. Les informations précédentes sont basées sur ASEv3. Pour plus d’informations sur ASEv2, consultez [Présentation d’App Service Environment v2](./intro.md).
