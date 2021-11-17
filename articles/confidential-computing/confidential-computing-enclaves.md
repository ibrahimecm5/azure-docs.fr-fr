---
title: Générez avec des enclaves SGX - Machines virtuelles Azure
description: Apprenez-en davantage sur le matériel Intel SGX pour activer vos charges de travail d’informatique confidentielle.
author: stempesta
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ac8c9ddced2fd37c829277c925b8953e7c71151
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310012"
---
# <a name="build-with-sgx-enclaves"></a>Générez avec des enclaves SGX 

Azure Confidential Computing offre des machines virtuelles de [série DCsv2](../virtual-machines/dcv2-series.md) et [DCsv3/DCdsv3](../virtual-machines/dcv3-series.md)*. Ces machines virtuelles disposent de [Intel® Software Guard Extensions (SGX)](https://intel.com/sgx). 

La technologie Intel SGX permet aux clients de créer des enclaves qui protègent les données et de conserver le chiffrement des données pendant que le processeur traite les données. Le système d’exploitation et l’hyperviseur ne peuvent pas accéder aux données. Les administrateurs de centres de données disposant d’un accès physique ne peuvent pas non plus accéder aux données.

## <a name="enclaves-concept"></a>Concept d’enclaves

Les enclaves sont les parties sécurisées du processeur et de la mémoire d’un matériel. Il n’existe aucun moyen de consulter les données ou le code à l’intérieur de l’enclave, même avec un débogueur. Si du code non fiable tente de modifier le contenu de la mémoire de l’enclave, SGX désactive l’environnement et refuse les opérations. Ces fonctionnalités uniques vous aident à protéger vos secrets contre les accès en clair.  

![Diagramme du modèle de machine virtuelle, montrant les données sécurisées dans les enclaves.](media/overview/hardware-backed-enclave.png)

Imaginez une enclave comme un référentiel sécurisé. Vous placez le code et les données chiffrés dans le référentiel sécurisé. De l’extérieur, vous ne voyez rien. Vous donnez à l’enclave une clé pour déchiffrer les données. L’enclave traite et chiffre à nouveau les données, avant de renvoyer les données.

Chaque enclave possède un cache de page chiffré (EPC) avec une taille définie. L’EPC détermine la quantité de mémoire qu’une enclave peut conserver. [Série DCsv2](../virtual-machines/dcv2-series.md) Les machines virtuelles contiennent jusqu’à 168 Mio. Les machines virtuelles de [série DCsv3/DCdsv3](../virtual-machines/dcv3-series.md)* contiennent jusqu’à 256 Go pour davantage de charges de travail gourmandes en mémoire.

> [!NOTE]
> DCsv3 et DCdsv3 sont en **version préliminaire publique** le 1er novembre 2021.

Pour plus d’informations, consultez [comment déployer des machines virtuelles Intel SGX avec des enclaves approuvées basées sur le matériel](virtual-machine-solutions-sgx.md).

## <a name="developing-for-enclaves"></a>Développement pour les enclaves

Vous pouvez utiliser divers [outils logiciels pour développer des applications qui s’exécutent dans les enclaves](application-development.md). Ces outils vous aident à protéger certaines parties de votre code et de vos données à l’intérieur de l’enclave. Assurez-vous que personne en dehors de votre environnement de confiance ne peut afficher ou modifier vos données avec ces outils.

## <a name="next-steps"></a>Étapes suivantes
- [Déployer une machine virtuelle DCsv2 ou DCsv3/DCdsv3](quick-create-portal.md)
- [Développer une application reconnaissant les enclaves](application-development.md) à l’aide du Kit de développement logiciel (SDK) OE
