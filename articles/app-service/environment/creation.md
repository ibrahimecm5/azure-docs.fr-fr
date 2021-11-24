---
title: Créer un environnement App Service Environment
description: Découvrez comment créer un environnement App Service Environment.
author: madsd
ms.topic: article
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: ae243842f5201b7d8f2bfa3adcb42c5146c885d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525022"
---
# <a name="create-an-app-service-environment"></a>Créer un environnement App Service Environment
> [!NOTE]
> Cet article concerne la fonctionnalité App Service Environment v3 qui est utilisée avec les plans App Service Isolé v2.
> 

L’environnement [ASE (App Service Environment)][Intro] est un déploiement monolocataire d’App Service, qui s’injecte dans votre réseau virtuel Azure. Le déploiement d'un environnement ASE requiert l'utilisation d'un sous-réseau. Ce sous-réseau ne peut pas être utilisé à d’autres fins que pour l’environnement ASE. 

## <a name="before-you-create-your-ase"></a>Avant de créer votre ASE

Une fois votre environnement ASE créé, vous ne pouvez plus modifier les éléments suivants :

- Emplacement
- Abonnement
- Resource group
- Réseau virtuel Azure utilisé
- Sous-réseaux utilisés
- Taille du sous-réseau
- Nom de votre environnement ASE

Le sous-réseau doit être suffisamment grand pour contenir la taille maximale à laquelle votre environnement ASE sera mis à l'échelle. Choisissez un sous-réseau suffisamment grand pour prendre en charge vos besoins maximum de mise à l’échelle, car il ne peut pas être modifié après la création. La taille recommandée est /24 avec 256 adresses.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Il y a deux choses importantes qui doivent être envisagées avant de déployer votre ASE.

- Le type d’adresse IP virtuelle (VIP)
- type de déploiement

Il existe deux types d’adresses IP virtuelles différentes : interne et externe. Avec une adresse IP virtuelle interne, vos applications seront atteintes sur l’ASE à une adresse de votre sous-réseau ASE et vos applications ne sont pas sur un DNS public. Lors de la création dans le portail, il existe une option pour créer une zone DNS privée Azure pour votre ASE. Avec une adresse IP virtuelle externe, vos applications se trouvent sur une adresse Internet publique et vos applications sont dans un DNS public. 

Trois types de déploiement sont disponibles :

- une seule zone
- redondance interzone
- groupe hôte

L’ASE à une seule zone est disponible dans toutes les régions où ASEv3 est disponible. Si vous disposez d’un ASE à une seule zone, des frais minimum de charge d’instance de plan App Service pour une instance de Windows Isolated v2 s’appliquent. Dès que vous disposez d’une ou de plusieurs instances, ces frais ne s’appliquent plus. Il ne s’agit pas de frais supplémentaires.

Dans un ASE redondant interzone, vos applications sont réparties sur trois zones de la même région. L’ASE redondant interzone est disponible dans un sous-ensemble de régions prenant en charge l’ASE et principalement limité par les régions qui prennent en charge les zones de disponibilité. Lorsque vous disposez d’un ASE redondant interzone, la taille minimale de votre plan App Service est de trois instances. Cela garantit qu’il existe une instance dans chaque zone de disponibilité. Il est possible d’effectuer un scale-up des plans App Service d’une ou plusieurs instances à la fois. La mise à l’échelle ne s’effectue pas nécessairement par trois unités, mais l’application n’est équilibrée que dans toutes les zones de disponibilité lorsque le nombre total d’instances est égal à un multiple de trois. Un ASE redondant interzone a trois fois l’infrastructure et est constitué de composants redondants dans une zone. Ainsi, même si deux des trois zones tombent en panne pour une raison quelconque, vos charges de travail restent disponibles. En raison de l’augmentation du besoin du système, les frais minimum d’un ASE redondant dans une zone équivalent à ceux de neuf instances. Si vous avez moins de neuf instances de plan App Service dans votre ASEv3, la différence est facturée comme pour une instance de Windows I1v2. Si vous disposez de neuf instances ou plus, le fait d’avoir un ASE redondant interzone n’entraîne pas de frais supplémentaires. Pour en savoir plus sur la redondance de zone, consultez l’article [Régions et zones de disponibilité dans Azure](./overview-zone-redundancy.md).

Dans un déploiement de groupe hôte, vos applications sont déployées sur un groupe hôte dédié. Le groupe hôte dédié n’est pas redondant interzone. Le déploiement d’un groupe hôte dédié permet de déployer votre ASE sur du matériel dédié. Aucun frais d’instance minimum ne s’appliquent pour l’utilisation d’un ASE sur un groupe hôte dédié. Cependant, vous devez payer pour le groupe hôte lors de la configuration de l’ASE. En plus de cela, le tarif de votre plan App Service sera réduit à mesure que vous créez vos plans et effectuez votre scale-out. Le nombre de cœurs disponibles avec un déploiement d’hôte dédié utilisés par les plans App Service et les rôles d’infrastructure est limité. Les déploiements d’hôtes dédiés de l’ASE ne peuvent pas atteindre le nombre total d’instances de 200 normalement disponible dans un environnement ASE. Le nombre total d’instances possible est corollaire du nombre total d’instances de plan App Service auquel on additionne le nombre de rôles d’infrastructure en fonction de la charge.

## <a name="creating-an-ase-in-the-portal"></a>Création d’un environnement ASE dans le portail

1. Pour créer un environnement ASE, recherchez **App Service Environment v3** sur la Place de marché.

2. Paramètres de base :  Sélectionnez l’abonnement, sélectionnez ou créez le groupe de ressources, puis entrez le nom de votre environnement ASE.  Sélectionnez le type d'adresse IP virtuelle. Si vous sélectionnez Interne, votre adresse ASE entrante sera une adresse de votre sous-réseau ASE. Si vous sélectionnez Externe, votre adresse ASE entrante sera une adresse publique accessible sur Internet. Le nom de l’environnement ASE sera également utilisé pour le suffixe de domaine de votre environnement ASE. Si le nom de votre environnement ASE est *contoso* et que vous disposez d'une adresse IP virtuelle interne, le suffixe du domaine sera *contoso.appserviceenvironment.net*. Si le nom de votre environnement ASE est *contoso* et que vous disposez d'une adresse IP virtuelle externe, le suffixe du domaine sera *contoso.p.azurewebsites.net*. 

    ![App Service Environment - Créer un onglet de base](./media/creation/creation-basics.png)

3. Hébergement : sélectionnez *Activé* ou *Désactivé* pour le déploiement du groupe hôte. Le déploiement du groupe hôte est utilisé pour sélectionner le déploiement du matériel dédié. Si vous sélectionnez Activé, votre environnement ASE sera déployé sur du matériel dédié. Lorsque vous optez pour un déploiement sur du matériel dédié, vous êtes facturé pour l'intégralité de l'hôte dédié lors de la création de l'environnement ASE, puis un prix réduit est appliqué pour vos instances de plan App Service.

    ![App Service Environment - Sélections d’hébergement](./media/creation/creation-hosting.png)

4. Mise en réseau : sélectionnez ou créez votre Réseau virtuel, sélectionnez ou créez votre sous-réseau. Si vous créez un environnement ASE avec adresse IP virtuelle interne, vous pourrez configurer des zones privées Azure DNS pour que le suffixe de votre domaine pointe vers votre ASE. Pour en savoir plus sur la configuration manuelle de DNS, consultez la section DNS sous [Utiliser une instance d'App Service Environment][UsingASE].

    ![App Service Environment - Sélections réseau](./media/creation/creation-networking.png)

5. Vérifier et créer : Vérifiez que votre configuration est correcte, puis sélectionnez Créer. Comptez près de deux heures pour la création de votre environnement ASE. 

Une fois la création de l’environnement ASE terminée, vous pouvez le sélectionner comme emplacement lors de la création de vos applications. Pour en savoir plus sur la création d'applications dans votre nouvel environnement ASE ou sur la gestion de celui-ci, consultez [Utiliser une instance d'App Service Environment][UsingASE]

## <a name="dedicated-hosts"></a>Hôtes dédiés

L’environnement ASE est normalement déployé sur des machines virtuelles provisionnées sur un hyperviseur multilocataire. Si vous devez déployer sur des systèmes dédiés, notamment le matériel, vous pouvez provisionner votre environnement ASE sur des hôtes dédiés. Les hôtes dédiés sont fournis par paire pour assurer la redondance. Les déploiements ASE basés sur un hôte dédié ne sont pas facturés au tarif normal. Des frais sont facturés pour l’hôte dédié, puis pour chaque instance de plan App Service. Les déploiements sur des groupes hôtes ne sont pas redondants d'une zone à l'autre. Pour effectuer un déploiement sur des hôtes dédiés, sélectionnez l'option **Activer** correspondant au déploiement du groupe hôte sous l'onglet Hébergement

<!--Links-->
[Intro]: ./overview.md
[UsingASE]: ./using.md
