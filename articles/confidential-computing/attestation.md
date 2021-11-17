---
title: Attestation pour les enclaves SGX
description: Vérifiez que votre enclave SGX d’informatique confidentielle est sécurisée avec l’attestation.
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2020
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 03a389facebbfcb242cf63afd0a8fcca6ad7b670
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347667"
---
# <a name="attestation-for-sgx-enclaves"></a>Attestation pour les enclaves SGX

L’informatique confidentielle sur Azure propose des machines virtuelles basées sur Intel SGX qui peuvent isoler une partie de votre code ou de vos données. Lorsque vous utilisez ces [enclaves](confidential-computing-enclaves.md), vous devez vérifier et valider que votre environnement approuvé est sécurisé. Cette vérification est le processus d’attestation. 

## <a name="overview"></a>Vue d’ensemble 

L’attestation permet à une partie de confiance d’avoir une confiance accrue en ce qui concerne le logiciel :

1. En cours d’exécution dans une enclave
1. À jour
1. Sécuriser

Par exemple, une enclave peut demander au matériel sous-jacent de générer des informations d’identification. Ces informations d’identification incluent la preuve que l’enclave existe sur la plateforme. Une autre enclave peut recevoir et vérifier que la même plateforme a généré le rapport.

![Diagramme du processus d’attestation, montrant l’échange sécurisé du client avec l’enclave qui contient les données et le code de l’application.](media/attestation/attestation.png)

L’attestation doit être implémentée à l’aide d’un service d’attestation sécurisé qui est compatible avec le logiciel système et le silicium. Par exemple :

- [Microsoft Azure Attestation](../attestation/overview.md) 
- [Services d'attestation et d'approvisionnement d'Intel](https://software.intel.com/sgx/attestation-services)


Les deux services sont compatibles avec les machines virtuelles Intel SGX de série DCsv2 d’informatique confidentielle Azure. Les machines virtuelles de série DCsv3 et DCdsv3 ne sont pas compatibles avec le service d’attestation Intel. 

## <a name="next-step"></a>Étape suivante

> [!div class="nextstepaction"]
> [Exemples Microsoft Azure Attestation pour les applications prenant en charge les enclaves](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)
