---
title: Choisir des offres de conteneur pour l’informatique confidentielle
description: Comment choisir les offres de conteneur confidentiel appropriées pour répondre à vos besoins en matière de sécurité, d’isolation et de développement.
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: 158e3e5a8191bc8e193ff9dab803feed7da2cc8a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096273"
---
# <a name="choosing-confidential-container-offerings"></a>Choix des offres de conteneur confidentiel

L’informatique confidentielle Azure propose plusieurs types de conteneurs confidentiels. Vous pouvez utiliser ces conteneurs pour prendre en charge l’intégrité et la confidentialité des données, ainsi que l’intégrité du code.

Les conteneurs confidentiels facilitent également la protection du code par le biais du chiffrement. Vous pouvez créer des assurances basées sur le matériel et une racine de confiance matérielle. Vous pouvez également réduire votre surface d’exposition aux attaques avec des conteneurs confidentiels.

## <a name="enclaves-confidential-containers"></a>Conteneurs confidentiels d’enclaves

Vous pouvez déployer des conteneurs confidentiels avec des enclaves. Cette méthode de déploiement de conteneur offre la sécurité et l’isolation de calcul les plus fortes, avec une base de calcul fiable (TCB) de niveau inférieur. Les conteneurs confidentiels basés sur les extensions Intel Software Guard (SGX) qui s’exécutent dans l’environnement d’exécution de confiance (TEE) basé sur le matériel sont disponibles. Ces conteneurs prennent en charge le levage et le décalage de vos applications de conteneur existantes. Une autre option consiste à permettre la création d’applications personnalisées avec la sensibilisation aux enclaves.

Il existe deux modèles de programmation et de déploiement sur Azure Kubernetes Service (AKS). 

Les **conteneurs non modifiés** prennent en charge des langages de programmation plus élevés sur Intel SGX via l’écosystème de partenaires Azure des projets OSS. Pour plus d’informations, consultez les [exemples et le déroulement du déploiement de conteneurs non modifiés](./confidential-containers.md).

Les **conteneurs reconnaissant les enclaves** utilisent un modèle de programmation Intel SGX personnalisé. Pour plus d’informations, consultez le [workflow et les exemples relatifs au déploiement de conteneurs prenant en charge les enclaves](./enclave-aware-containers.md). 

![Diagramme de conteneurs d’enclaves confidentiels avec Intel SGX, montrant les limites d’isolement et de sécurité.](./media/confidential-containers/confidential-container-intel-sgx.png)

## <a name="learn-more"></a>En savoir plus

- [Machines virtuelles confidentielles Intel SGX sur Azure](./virtual-machine-solutions-sgx.md)
- [Conteneurs confidentiels sur Azure](./confidential-containers.md)
