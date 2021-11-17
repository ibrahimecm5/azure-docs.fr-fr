---
title: Choisir entre les modèles de déploiement
description: Choisir entre les modèles de déploiement
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/04/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8ed7f4b88e38581bea6eaf1a19ce945858031f1d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347686"
---
# <a name="confidential-computing-deployment-models"></a>Modèles de déploiement de calcul confidentiel

Le calcul confidentiel Azure prend en charge plusieurs modèles de déploiement. Ces différents modèles prennent en charge la plupart des exigences de sécurité des clients pour les cloud computing modernes.

## <a name="infrastructure-as-a-service-iaas"></a>IaaS (Infrastructure as a service)

Pour l’infrastructure en tant que service (IaaS), vous pouvez utiliser des machines virtuelles confidentielles dans le calcul confidentiel. Vous pouvez utiliser des machines virtuelles basées sur les [enclaves d’application Intel Software Guard Extensions (SGX)](confidential-computing-enclaves.md) ou la technologie AMD SEV-SNP.

## <a name="platform-as-a-service-paas"></a>PaaS (Platform as a Service)

Pour la plateforme en tant que service (PaaS), vous pouvez utiliser des [conteneurs confidentiels](confidential-containers.md) dans le calcul confidentiel. Cette offre comprend des conteneurs compatibles avec les enclaves dans Azure Kubernetes Service (AKS).

Le choix du modèle de déploiement adapté dépend de nombreux facteurs. Vous devrez peut-être tenir compte de l’existence d’applications héritées, des fonctionnalités de système d’exploitation et de la migration à partir de réseaux locaux.

Bien qu’il existe encore de nombreuses raisons d’utiliser des machines virtuelles, les conteneurs offrent une plus grande flexibilité pour les nombreux environnements logiciels de l’informatique moderne. 

Les conteneurs peuvent prendre en charge des applications qui :

- S’exécutent sur plusieurs Clouds.
- Se connectent aux microservices.
- Utilisent différents langages de programmation et infrastructures.
- Utilisent l’automatisation et Azure Pipelines, y compris l’intégration continue et l’implémentation de déploiement continu (CI/CD).

Les conteneurs augmentent également la portabilité des applications et améliorent l’utilisation des ressources en appliquant l’élasticité du Cloud Azure.

Normalement, vous pouvez déployer votre solution sur des machines virtuelles confidentielles si :

- Vous disposez d’applications héritées qui ne peuvent pas être modifiées ou en conteneur. Toutefois, vous devez toujours introduire une protection des données en mémoire, tandis que les données sont en cours de traitement.
- Vous exécutez plusieurs applications nécessitant différents systèmes d’exploitation sur une seule et même infrastructure.
- Vous voulez émuler un environnement de calcul entier, y compris toutes les ressources de système d’exploitation.
- Vous migrez vos machines virtuelles existantes de l’environnement local vers Azure.

Vous pouvez opter pour une approche basé sur conteneur confidentiel lorsque :

- Vous vous intéressez à l’allocation des ressources et des coûts. Toutefois, vous avez besoin d’une plateforme plus agile pour le déploiement de vos applications et jeux de données propriétaires.
- Vous créez une solution cloud native moderne. Vous disposez également d’un contrôle total sur le code source et le processus de déploiement.
- Vous avez besoin de la prise en charge de plusieurs Clouds.

Les deux options offrent le niveau de sécurité le plus élevé pour les services Azure. Il existe quelques différences dans les postures de sécurité des [machines virtuelles confidentielles](#confidential-vms-on-amd-sev-snp) et des [conteneurs confidentiels](#secure-enclaves-on-intel-sgx), comme suit.

### <a name="confidential-vms-on-amd-sev-snp"></a>Machines virtuelles confidentielles sur AMD SEV-SNP

Les **machines virtuelles confidentielles sur AMD SEV-SNP** offrent une protection chiffrée par le matériel de l’ensemble de la machine virtuelle contre tout accès non autorisé par l’administrateur de l’hôte. Ce niveau comprend généralement l’hyperviseur géré par le fournisseur de services Cloud (CSP). Vous pouvez utiliser ce type de machine virtuelle confidentielle pour empêcher le CSP d’accéder aux données et au code exécuté au sein de la machine virtuelle.

Les administrateurs de machines virtuelles ou les autres applications ou services s’exécutant à l’intérieur de la machine virtuelle fonctionnent au-delà des limites protégées. Ces utilisateurs et services peuvent accéder aux données et au code au sein de la machine virtuelle.

La technologie AMD SEV-SNP fournit l’isolation des machines virtuelles à partir de l’hyperviseur. La protection de l’intégrité de la mémoire basée sur le matériel permet d’éviter les attaques basées sur un hyperviseur malveillant. Le modèle SEV-SNP approuve le processeur sécurisé AMD et la machine virtuelle. Le modèle ne fait pas confiance à d’autres composants matériels et logiciels. Les composants non fiables incluent le BIOS et l’hyperviseur sur le système hôte.

:::image type="content" source="media/confidential-computing-deployment-models/amd-sev-snp-vm.png" alt-text="Diagramme de l’architecture de machine virtuelle AMD SEV-SNP, définition des composants approuvés et non approuvés.":::

### <a name="secure-enclaves-on-intel-sgx"></a>Les enclaves sécurisées sur Intel SGX

Les **enclaves sécurisées sur Intel SGX** protègent les espaces mémoire à l’intérieur d’une machine virtuelle avec un chiffrement basé sur le matériel. Les limites de sécurité des enclaves d’application sont plus limitées que les machines virtuelles confidentielles sur AMD SEV-SNP. Pour Intel SGX, la limite de sécurité s’applique aux portions de mémoire d’une machine virtuelle. Les utilisateurs, les applications et les services qui s’exécutent à l’intérieur de la machine virtuelle équipée d’Intel SGX ne peuvent pas accéder aux données et au code en cours d’exécution à l’intérieur de l’enclave.

Intel SGX vous aide à protéger les données utilisées par l’isolation des applications. En protégeant le code et les données sélectionnés à partir de la modification, les développeurs peuvent partitionner leur application en enclaves renforcées ou en modules d’exécution approuvés pour améliorer la sécurité de l’application. Les entités extérieures à l’enclave ne peuvent pas lire ou écrire la mémoire de l’enclave, quel que soit leur niveau d’autorisation. L’hyperviseur ou le système d’exploitation ne peut pas obtenir cet accès par le biais d’appels au niveau du système d’exploitation normaux. Pour appeler une fonction enclave, vous devez utiliser un nouvel ensemble d’instructions dans les processeurs Intel SGX. Ce processus comprend plusieurs contrôles de protection.

:::image type="content" source="media/confidential-computing-deployment-models/intel-sgx-enclave.png" alt-text="Diagramme de l’architecture Intel SGX des enclaves, montrant des informations sécurisées dans l’enclave des applications.":::

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les conteneurs confidentiels](confidential-containers.md)
- [En savoir plus sur les enclaves de calcul confidentiel](confidential-computing-enclaves.md)
