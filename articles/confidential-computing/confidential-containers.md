---
title: Conteneurs confidentiels sur Azure
description: Découvrez-en plus sur la prise en charge des conteneurs non modifiés avec des conteneurs confidentiels.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 11/01/2011
ms.author: amgowda
ms.service: container-service
ms.custom: ignite-fall-2021
ms.openlocfilehash: d7c94570a7b83fba70a8e95d17f5c6eb215740bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067498"
---
# <a name="confidential-containers-on-azure"></a>Conteneurs confidentiels sur Azure

L’informatique confidentielle Azure propose des conteneurs confidentiels. Il existe plusieurs [options que vous pouvez choisir pour les conteneurs confidentiels](choose-confidential-containers-offerings.md). Les environnements sécurisés et isolés avec attestation améliorent la sécurité globale de vos déploiements de conteneur. 

Un environnement d’exécution de confiance (TEE, Trusted Execution Environment) basé sur le matériel offre de solides garanties. Un environnement TEE fournit des mesures du matériel et des logiciels à partir de composants TCB (Trusted Computing Base). Les offres de conteneurs confidentiels sur Azure permettent la vérification de ces mesures et vérifient si les applications de conteneur s’exécutent dans un environnement d’exécution vérifiable.

Les conteneurs confidentiels prennent en charge les applications personnalisées développées avec n’importe quel langage de programmation. Vous pouvez également exécuter des applications de conteneur Docker du commerce.

![Diagramme de la limite de protection des conteneurs confidentiels dans Kubernetes.](./media/confidential-containers/sgx-confidential-container.jpg)

## <a name="enablers-with-intel-sgx-on-azure-kubernetes-serviceaks"></a>Activateurs avec Intel SGX sur Azure Kubernetes Service (AKS)

 Pour exécuter un conteneur Docker existant, les applications sur les nœuds d’informatique confidentielle nécessitent une couche d’abstraction ou un logiciel Intel SGX (Software Guard Extensions) pour utiliser le jeu d’instructions de processeur spécial. Configurez SGX pour protéger votre code d’application sensible. SGX crée une exécution directe sur le processeur pour supprimer le système d’exploitation invité, le système d’exploitation hôte ou l’hyperviseur de la limite d’approbation. Cette étape réduit les vulnérabilités et les zones d’attaque de la surface globale.

Azure Kubernetes Service (AKS) prend entièrement en charge les conteneurs confidentiels. Vous pouvez exécuter des conteneurs existants de manière confidentielle sur AKS.

![Diagramme de la conversion d’un conteneur confidentiel, avec de nouvelles étapes pour l’activation d’Intel SGX et d’AKS](./media/confidential-containers/confidential-containers-deploy-steps.jpg)

## <a name="partner-enablers"></a>Activateurs de partenaires

Vous pouvez activer des conteneurs confidentiels dans des projets de partenaires Azure et de logiciels Open Source (OSS, Open Source Software). Les développeurs peuvent choisir des fournisseurs de logiciels en fonction de leurs fonctionnalités, de l’intégration aux services Azure et de la prise en charge des outils. 

> [!IMPORTANT]
> Les partenaires Azure proposent les solutions suivantes. Ces solutions peuvent occasionner des frais de licence. Vérifier individuellement toutes les conditions d’utilisation des logiciels des partenaires. 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) dispose d’expériences du portail et de l’interface de ligne de commande (CLI) pour convertir leurs applications conteneurisées en conteneurs confidentiels compatibles SGX. Vous n’avez pas besoin de modifier ou de recompiler l’application. Fortanix offre la flexibilité nécessaire pour exécuter et gérer un large éventail d’applications. Vous pouvez utiliser des applications existantes, de nouvelles applications d’enclave natives et des applications prépackagées. Commencez avec l’interface utilisateur [Enclave Manager](https://em.fortanix.com/) de Fortanix ou les [API REST](https://www.fortanix.com/api/em/). Créer des conteneurs confidentiels à l’aide du [Guide de démarrage rapide pour AKS](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) de Fortanix .

![Diagramme du processus de déploiement de Fortanix, qui présente les étapes permettant de déplacer des applications vers des conteneurs confidentiels et de les déployer.](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>SCONE (Scontain)

Les stratégies de sécurité [SCONE](https://scontain.com/) (Scontain) génèrent des certificats, des clés et des secrets. Seuls les services avec attestation pour une application voient ces informations d’identification. Les services d’application se fournissent mutuellement une attestation via TLS. Vous n’avez pas besoin de modifier les applications ou TLS. Pour obtenir une explication plus détaillée, consultez la [démonstration d’application Flask](https://sconedocs.github.io/flask_demo/) de SCONE.

SCONE peut convertir la plupart des fichiers binaires existants en applications qui s’exécutent dans des enclaves. Vous n’avez pas besoin de changer ou de recompiler l’application. SCONE protège également les langages interprétés tels que Python, en chiffrant à la fois les fichiers de données et les fichiers de code Python. Vous pouvez utiliser des stratégies de sécurité SCONE pour protéger les fichiers chiffrés contre les accès non autorisés, les modifications et les restaurations. Pour plus d’informations, consultez la documentation de SCONE sur l’[utilisation de SCONE avec une application Python existante](https://sconedocs.github.io/sconify_image/).

![Diagramme du workflow SCONE, montrant comment SCONE traite les images binaires.](./media/confidential-containers/scone-workflow.png)

Vous pouvez déployer SCONE sur des nœuds d’informatique confidentielle Azure avec AKS. Ce processus est entièrement pris en charge et intégré. Pour plus d’informations, consultez l’[exemple d’application AKS SCONE](https://sconedocs.github.io/aks/).

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) fournit un logiciel de plateforme SGX permettant d’exécuter des conteneurs non modifiés sur AKS. Pour plus d’informations, consultez la [documentation sur les fonctionnalités et les exemples d’applications](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp) d’Anjuna.

Commencez avec un exemple de Cache Redis et d’application personnalisée Python [ici](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

![Diagramme du processus d’Anjuna, montrant comment les conteneurs sont exécutés sur l’informatique confidentielle Azure.](media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>Activateurs OSS

> [!NOTE]
> Les projets open source proposent les solutions suivantes. L’informatique confidentielle Azure et Microsoft ne sont pas directement affiliés à ces projets et solutions.  

### <a name="gramine"></a>Gramine

[Gramine](https://grapheneproject.io/) est un système d’exploitation invité léger, conçu pour exécuter une application Linux unique avec des exigences minimales en matière d’hébergement. Gramine peut exécuter des applications dans un environnement isolé. Il existe une prise en charge des outils pour la conversion d’applications de conteneur Docker existantes en conteneurs dotés d’une protection maximale Gramine (GSC, Gramine Shielded Containers).

Pour plus d’informations, consultez l’[exemple d’application et de déploiement sur AKS](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) de Gramine

### <a name="occlum"></a>Occlum

[Occlum](https://occlum.io/) est un système d’exploitation de bibliothèque (LibOS) multiprocessus, à mémoire sécurisée, pour Intel SGX. Ce système d’exploitation permet aux applications héritées de s’exécuter sur SGX avec peu, voire pas, de modification du code source. Occlum protège de manière transparente la confidentialité des charges de travail utilisateur, tout en permettant une migration « lift-and-shift » aisée vers des applications Docker existantes.

Occlum prend en charge les déploiements AKS. Pour plus d’informations, consultez les [instructions de déploiement et exemples d’applications](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md) d’Occlum.

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/) est un framework d’orchestration pour les conteneurs confidentiels. Vous pouvez exécuter et mettre à l’échelle des services confidentiels sur Kubernetes compatible SGX. Marblerun assure des tâches réutilisables telles que la vérification des services dans votre cluster, la gestion des secrets pour ces services et l’établissement de connexions mTLS d’enclave à enclave entre eux. Marblerun garantit également que votre cluster de conteneurs confidentiels respecte un manifeste défini en code JSON simple. Vous pouvez vérifier le manifeste avec des clients externes par le biais d’une attestation distante.

Ce framework étend la confidentialité, l’intégrité et les propriétés de vérifiabilité d’une enclave données à un cluster Kubernetes.

Marblerun prend en charge les conteneurs confidentiels créés avec Graphene, Occlum et EGo, avec des [exemples pour chaque SDK](https://docs.edgeless.systems/marblerun/#/examples?id=examples). Le framework s’exécute sur Kubernetes, parallèlement à vos outils natifs cloud existants. Il existe une interface CLI et des graphiques Helm. Marblerun prend également en charge les nœuds d’informatique confidentielle sur AKS. Suivez le [guide fourni pour déployer Marblerun sur AKS](https://docs.edgeless.systems/marblerun/#/deployment/cloud?id=cloud-deployment) de Marblerun.

## <a name="confidential-containers-demo"></a>Démonstration des conteneurs confidentiels

Pour obtenir un exemple d’application, consultez la [démonstration Santé avec des conteneurs confidentiels](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Prendre contact

Avez-vous des questions sur votre implémentation ? Voulez-vous devenir un activateur pour les conteneurs confidentiels ? Envoyez-nous un e-mail à l’adresse <acconaks@microsoft.com>.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un cluster AKS avec des nœuds de machine virtuelle confidentiel Intel SGX](./confidential-enclave-nodes-aks-get-started.md)
- [Microsoft Azure Attestation](../attestation/overview.md)
- [Machines virtuelles confidentielles Intel SGX](virtual-machine-solutions-sgx.md)
- [Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
