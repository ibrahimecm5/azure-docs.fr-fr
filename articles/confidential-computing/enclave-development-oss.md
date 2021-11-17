---
title: Développer des enclaves d’application avec des solutions open source dans l’informatique confidentielle Azure
description: Découvrez comment utiliser des outils pour développer des applications Intel SGX pour l’informatique confidentielle Azure.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71be688b3f69860468ce6c638e484caae0ba72f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290207"
---
# <a name="open-source-solutions-to-build-enclave-applications"></a>Solutions open source pour générer des applications enclaves

Cet article passe en revue les solutions open source pour la génération d’applications qui utilisent des enclaves d’application. Avant de le lire, veillez à lire la page conceptuelle sur les [applications enclaves](application-development.md). 

## <a name="intel-sgx-compatible-tools"></a>Outils compatibles Intel SGX
Azure offre des enclaves d’application via des [machines virtuelles confidentielles avec Intel SGX (Software Guard Extensions) activé](virtual-machine-solutions-sgx.md). Après avoir déployé une machine virtuelle Intel SGX, vous devez disposer d’outils spécialisés pour faire en sorte que votre application reconnaisse les enclaves. De cette façon, vous pouvez générer des applications qui ont aussi bien des portions de code approuvées que non approuvées. 

Par exemple, vous pouvez utiliser ces frameworks open source : 

- [SDK Open Enclave (OE)](#oe-sdk)
- [SDK EGo](#ego)
- [SDK Intel SGX](#intel-sdk)
- [Confidential Consortium Framework (CCF)](#ccf)

Si vous n’envisagez pas d’écrire un nouveau code d’application, vous pouvez wrapper une application conteneurisée à l’aide d’[activateurs de conteneurs confidentiels](confidential-containers.md)

### <a name="open-enclave-software-development-kit-oe-sdk"></a>SDK Open Enclave (SDK OE) <a id="oe-sdk"></a>

Utilisez une bibliothèque ou un framework pris en charge par votre fournisseur si vous souhaitez écrire du code qui s’exécute dans une enclave. Le [SDK Open Enclave](https://github.com/openenclave/openenclave) (SDK OE) est un SDK open source qui permet de créer une couche d’abstraction sur différents matériels prenant en charge l’informatique confidentielle. 

Le SDK OE est conçu pour servir de couche d’abstraction unique sur n’importe quel matériel sur n’importe quel fournisseur de solutions cloud. Le SDK OE peut être utilisé sur des machines virtuelles d’informatique confidentielle Azure pour créer et exécuter des applications sur des enclaves. La maintenance du référentiel Open Enclave est assurée par Microsoft.

### <a name="ego-software-development-kit"></a>Kit de développement logiciel (SDK) EGo<a id="ego"></a>

[EGo](https://ego.dev/) est un kit SDK open source qui vous permet d’exécuter des applications écrites dans le langage de programmation Go à l’intérieur d’enclaves. EGo s’appuie sur le kit SDK OE, et est fourni avec une bibliothèque Go intégrée à l’enclave pour l’attestation et le scellement. De nombreuses applications Go existantes s’exécutent sur EGo sans modification.  

### <a name="intel-sgx-software-development-kit"></a>Kit de développement logiciel (SDK) Intel SGX<a id="intel-sdk"></a>
Le [SDK Intel SGX](https://01.org/intel-softwareguard-extensions) est développé et tenu à jour par l’équipe SGX d’Intel. Le SDK est un ensemble d’outils permettant aux développeurs de logiciels de créer et de déboguer des applications compatibles Intel SGX en C/C++.

### <a name="confidential-consortium-framework-ccf"></a>CCF (Confidential Consortium Framework) <a id="ccf"></a>

Le [CCF](https://www.microsoft.com/research/project/confidential-consortium-framework/) ([Confidential Consortium Framework](https://www.microsoft.com/research/project/confidential-consortium-framework/)) est un exemple de framework blockchain distribué. Le CCF repose sur l’informatique confidentielle Azure. Lancé par Microsoft Research, ce framework utilise la puissance des environnements d’exécution de confiance (TEE, Trusted Execution Environment) pour créer un réseau d’enclaves distantes à des fins d’attestation. Les nœuds peuvent s’exécuter sur des machines virtuelles Azure Intel SGX et tirer pleinement parti de l’infrastructure d’enclaves. Avec les protocoles d’attestation, les utilisateurs du blockchain peuvent vérifier l’intégrité d’un nœud CCF et contrôler efficacement l’ensemble du réseau.

Dans le framework CCF, le registre décentralisé stocke les modifications enregistrées dans un magasin clé-valeur qui est répliqué sur tous les nœuds du réseau. Chacun de ces nœuds exécute un moteur de transaction qui peut être déclenché par les utilisateurs du blockchain sur TLS. Quand vous déclenchez un point de terminaison, vous mutez le magasin clé-valeur. Avant que le changement chiffré soit enregistré dans le registre décentralisé, il doit être accepté par plusieurs nœuds pour parvenir à un accord.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer une machine virtuelle Intel SGX d’informatique confidentielle](quick-create-portal.md)
