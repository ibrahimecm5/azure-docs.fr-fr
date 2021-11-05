---
title: Création de solutions confidentielles Azure
description: Découvrez comment créer des solutions sur l’informatique confidentielle Azure
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: f86c56fbc779af0bc1a2a002d79d41842c1ac0a7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097600"
---
# <a name="building-confidential-computing-solutions"></a>Création de solutions d’informatique confidentielle

L’informatique confidentielle Azure offre différentes options pour créer des solutions confidentielles. Le spectre de plages d’options allant de l’activation des scénarios « lift-and-shift » d’applications existantes à un contrôle total de diverses fonctionnalités de sécurité. Ces fonctionnalités incluent le contrôle du niveau de l’accès. Vous définissez les niveaux d’accès de fournisseur d’hôtes ou d’opérateur aux données et au code. Vous pouvez également contrôler d’autres accès de rootkits ou de logiciels malveillants susceptibles de compromettre l’intégrité des charges de travail s’exécutant dans le cloud.

## <a name="solutions"></a>Solutions

Des technologies telles que des enclaves sécurisées ou des machines virtuelles confidentielles permettent aux clients de choisir l’approche à suivre pour créer des solutions confidentielles.

- Des applications existantes dépourvues d’accès au code source peuvent tirer parti de machines virtuelles confidentielles basées sur la technologie AMD SEV-SNP pour faciliter l’intégration à la plateforme d’informatique confidentielle Azure.
- Les charges de travail sophistiquées qui incluent du code propriétaire à protéger à partir de n’importe quel vecteur d’approbation peuvent bénéficier de la technologie d’enclave d’application sécurisée. Azure offre des enclaves d’application dans des machines virtuelles basées sur Intel SGX. Intel SGX assure la protection des données et du code s’exécutant dans une zone mémoire chiffrée par du matériel. Ces applications nécessitent généralement une communication avec une enclave sécurisée attestée, obtenue à l’aide d’infrastructures open source.
- Des solutions conteneurisées s’exécutant sur des [conteneurs confidentiels](confidential-containers.md) activés dans Azure Kubernetes Service peuvent convenir pour des clients en quête d’une approche équilibrée de la confidentialité. Dans ces scénarios, des applications existantes peuvent être empaquetées et déployées dans des conteneurs moyennant des modifications limitées, tout en offrant une isolation complète de la sécurité assurée par le fournisseur de services cloud et les administrateurs.

![Capture d’écran du spectre de l’informatique confidentielle, montrant des options allant des plus faciles à utiliser à celles offrant un maximum de contrôle de la sécurité.](media/confidential-computing-solutions/spectrum.png)

## <a name="learn-more"></a>En savoir plus

Pour exploiter la puissance des enclaves et des environnements isolés, vous devez utiliser des outils qui prennent en charge l’informatique confidentielle. Différents outils prennent en charge le développement d’applications enclaves. Pour plus d’informations, documentez-vous sur le [développement d’applications d’enclave](application-development.md). 

Découvrez les [outils open source](enclave-development-oss.md) permettant de créer des solutions pour des applications d’enclave Intel SGX pour machines virtuelles.

Utilisez [des partenaires et des outils open source pour les conteneurs confidentiels](confidential-containers.md). Vous pouvez également utiliser certains de ces outils pour des charges de travail Azure Kubernetes.

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez le développement d’enclave d’application](application-development.md)
