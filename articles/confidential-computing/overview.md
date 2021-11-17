---
title: Vue d’ensemble de l’informatique confidentielle Azure
description: Vue d’ensemble de l’informatique confidentielle Azure (ACC, Azure Confidential Computing)
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 77c65a7c5418ebefd7b2414d04bec614eccdc5ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284357"
---
# <a name="what-is-confidential-computing"></a>Qu’est-ce que l’informatique confidentielle ?

L’informatique confidentielle vous permet d’isoler vos données sensibles pendant qu’elles sont traitées. De nombreux secteurs utilisent l’informatique confidentielle afin de protéger leurs données pour :

- Sécuriser des données financières
- Protéger les informations des patients
- Exécuter des processus de machine learning sur des informations sensibles
- Exécuter des algorithmes sur des jeux de données chiffrés provenant de plusieurs sources


## <a name="overview"></a>Vue d’ensemble
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Nous savons que la sécurisation de vos données cloud est importante. Nous sommes conscients de vos préoccupations. Voici quelques questions que nos clients peuvent se poser lors de la migration de charges de travail sensibles vers le cloud : 

- Comment m’assurer que Microsoft ne peut pas accéder aux données qui ne sont pas chiffrées ?
- Comment éviter les menaces de sécurité provenant d’administrateurs privilégiés dans mon entreprise ?
- Par quels autres moyens est-ce que je peux empêcher des tiers d’accéder aux données sensibles des clients ?

Azure vous aide à réduire la surface d’attaque pour renforcer la protection de vos données. Azure propose déjà de nombreux outils pour protéger les [**données au repos**](../security/fundamentals/encryption-atrest.md), par le biais de modèles tels que le chiffrement côté client et le chiffrement côté serveur. Azure permet également de chiffrer les [**données en transit**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) au moyen de mécanismes reposant sur des protocoles sécurisés tels que TLS et HTTPS. Cette page introduit une troisième forme de chiffrement, à savoir le chiffrement des **données en cours d’utilisation**.

## <a name="introduction-to-confidential-computing"></a>Présentation de l’informatique confidentielle  

L’informatique confidentielle est un terme spécialisé du [Confidential Computing Consortium](https://confidentialcomputing.io/) (CCC), une fondation dédiée à la définition de l’informatique confidentielle et à l’accélération de son adoption. Le CCC définit l’informatique confidentielle comme suit : La protection des données utilisées en effectuant des traitements dans un environnement d’exécution approuvé (TEE, Trusted Execution Environment) matériel.

Un TEE est un environnement dans lequel seul le code autorisé est exécuté. Aucune donnée dans le TEE ne peut être lue ou falsifiée par du code en dehors de cet environnement. 

### <a name="lessen-the-need-for-trust"></a>Limiter le besoin d’approbation
L’exécution de charges de travail sur le cloud demande une approbation. Vous donnez cette approbation à différents fournisseurs en activant différents composants de votre application.


**Fournisseurs de logiciels d’application** : Approuvez un logiciel en déployant localement, en utilisant de l’open source ou en créant un logiciel d’application en interne.

**Fournisseurs de matériel** : Approuvez du matériel en utilisant du matériel local ou du matériel d’origine interne. 

**Fournisseurs d’infrastructure** : Approuvez des fournisseurs de cloud ou gérez vos propres centres de données locaux.

L’informatique confidentielle Azure facilite l’approbation du fournisseur de cloud, en réduisant le besoin d’approbation de différents aspects de l’infrastructure de cloud computing. L’informatique confidentielle Azure minimise l’approbation pour le noyau du système d’exploitation hôte, l’hyperviseur, l’administrateur de la machine virtuelle et l’administrateur de l’hôte.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en plus sur tous les produits Azure en lien avec l’informatique confidentielle.

> [!div class="nextstepaction"]
> [Présentation des services d’informatique confidentielle Azure](overview-azure-products.md)
