---
title: Outils de développement d’informatique confidentielle Azure
description: Utilisez des outils et bibliothèques pour développer des applications pour l’informatique confidentielle sur Intel SGX
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6300e0cee2659f767c2d765de5a24591fc962e6f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131081312"
---
# <a name="application-enclave-development"></a>Développement d’une enclave d’application 

Avec l’informatique confidentielle Azure, vous pouvez créer des enclaves d’application pour les machines virtuelles qui exécutent Intel SGX (Software Guard Extensions). Il est important de comprendre les outils et logiciels associés avant de commencer le développement.

> [!NOTE]
> Si vous n’avez pas encore lu la [présentation des machines virtuelles et enclaves Intel SGX](confidential-computing-enclaves.md), faites-le avant de continuer.

## <a name="application-enclaves"></a>Enclaves d’application

Les enclaves d’application sont des environnements isolés qui protègent du code et des données spécifiques. Lorsque vous créez des enclaves, vous devez déterminer quelle partie de l’application s’exécute dans l’enclave. Lorsque vous créez ou gérez des enclaves, veillez à utiliser des SDK et des frameworks compatibles pour la pile de déploiement choisie. 

L’informatique confidentielle propose actuellement des enclaves d’application. Plus précisément, vous pouvez effectuer des déploiements et des développements avec des enclaves d’application à l’aide de [machines virtuelles confidentielles pour lesquelles Intel SGX est activé](virtual-machine-solutions-sgx.md). 

## <a name="intel-sgx"></a>Intel SGX

Avec la technologie Intel SGX, vous pouvez chiffrer des enclaves d’application, ou des environnements d’exécution de confiance, avec une clé inaccessible stockée dans le processeur. Le déchiffrement du code et des données se trouvant dans l’enclave se produit à l’intérieur du processeur. Seul le processeur y a accès. Ce niveau d’isolation protège les données en cours d’utilisation et protège contre les attaques matérielles et logicielles. Pour plus d’informations, consultez le [site web Intel SGX](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html). 

Azure propose Intel SGX dans un environnement de virtualisation par le biais de différentes tailles de machine virtuelle de la série DC. Plusieurs tailles de machine virtuelle permettent d’avoir différentes tailles de mémoire EPC (Enclave Page Cache). La mémoire EPC correspond à la quantité maximale de la zone de mémoire dédiée à une enclave sur cette machine virtuelle. Actuellement, les machines virtuelles Intel SGX sont disponibles sur les machines virtuelles de [série DCsv2](../virtual-machines/dcv2-series.md) et les machines virtuelles de [série DCsv3/DCdsv3](../virtual-machines/dcv3-series.md).


### <a name="developing-applications"></a>Développement d’applications

Dans une application, il y a deux partitions générées avec des enclaves. 

L’**hôte** est le composant « non approuvé ». Votre application enclave s’exécute sur l’hôte. L’hôte est un environnement non approuvé. Lorsque vous déployez du code d’enclave sur l’hôte, ce dernier ne peut pas accéder à ce code.

L’**enclave** est un composant « approuvé ». Le code de l’application ainsi que ses données en cache et sa mémoire s’exécutent dans l’enclave. L’environnement de l’enclave protège vos secrets et vos données sensibles. Faites en sorte que vos calculs sécurisés se produisent dans une enclave.

![Diagramme d’une application, montrant les partitions de l’hôte et de l’enclave. À l’intérieur de l’enclave se trouvent les composants de données et de code d’application.](media/application-development/oe-sdk.png)

Pour utiliser la puissance des enclaves et des environnements isolés, choisissez des outils qui prennent en charge l’informatique confidentielle. Différents outils prennent en charge le développement d’applications enclaves. Par exemple, vous pouvez utiliser ces frameworks open source : 

- [Kit de développement logiciel (SDK) Open Enclave (SDK OE)](enclave-development-oss.md#oe-sdk)
- [SDK Intel SGX](enclave-development-oss.md#intel-sdk)
- [Kit de développement logiciel (SDK) EGo](enclave-development-oss.md#ego)
- [Confidential Consortium Framework (CCF)](enclave-development-oss.md#ccf)

Lorsque vous concevez une application, identifiez et déterminez quelle partie du code doit s’exécuter dans des enclaves. Le code situé dans le composant approuvé est isolé du reste de votre application. Une fois l’enclave initialisée et le code chargé dans la mémoire, les composants non approuvés ne peuvent pas lire ou modifier ce code.

## <a name="next-steps"></a>Étapes suivantes 

- [Déployer une machine virtuelle Intel SGX d’informatique confidentielle](quick-create-portal.md)
- [Commencer à développer des applications avec des logiciels open source](enclave-development-oss.md)
