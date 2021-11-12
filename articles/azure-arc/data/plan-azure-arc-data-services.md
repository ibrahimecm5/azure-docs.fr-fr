---
title: Planifier un déploiement des services de données Azure Arc
description: Cet article explique les considérations relatives à la planification du déploiement des services de données Azure Arc.
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 15688b89b319b884707e9e6d8faa9a13de3233e0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563337"
---
# <a name="plan-an-azure-arc-enabled-data-services-deployment"></a>Planifier un déploiement des services de données Azure Arc

Cet article explique comment planifier le déploiement des services de données compatibles Azure Arc.

Lorsque vous commencez à planifier le déploiement des services de données Azure Arc, il est important de bien comprendre les charges de travail de votre base de données et les besoins de votre entreprise pour ces charges de travail. Par exemple, vous devez envisager des choses telles que la disponibilité, la continuité des activités et les besoins en capacité pour la mémoire, le processeur et le stockage de ces charges de travail. Et vous devez préparer soigneusement l’infrastructure pour prendre en charge les charges de travail de base de données, en fonction des besoins de votre entreprise.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que vous respectez certaines conditions préalables et que vous disposez du contexte et des informations nécessaires. Pour garantir la réussite du déploiement, votre environnement d’infrastructure doit être correctement configuré avec le bon niveau d’accès et les quantités de capacité de stockage, de processeur et de mémoire appropriées. 

Consultez les articles suivants :

- [Guide de dimensionnement](sizing-guidance.md)
- [Configuration du stockage](storage-configuration.md)
- [Modes et exigences de connectivité](connectivity.md)

Vérifiez que :

- L’[`arcdata`extension CLI](install-arcdata-extension.md) est installée.
- Les autres [outils clients](install-client-tools.md) sont installés.
- Vous avez accès au cluster Kubernetes.
- Votre fichier *kubeconfig* est configuré. Il doit pointer vers le cluster Kubernetes sur lequel vous souhaitez effectuer le déploiement. Pour vérifier le contexte actuel du cluster, exécutez la commande suivante :
   ```console
   kubectl cluster-info
   ```  
- Vous avez un abonnement Azure pour lequel seront projetées et facturées des ressources telles que le contrôleur de données Azure Arc, l’instance gérée SQL compatible Azure Arc ou un serveur hyperscale PostgreSQL Azure Arc.

Une fois l’infrastructure préparée, déployez les services de données Azure Arc de la façon suivante :
1. Créez un contrôleur de données compatible Azure Arc sur l’une des distributions validées d’un cluster Kubernetes.
1. Créez une instance gérée SQL compatible Azure Arc et/ou un groupe de serveur Hyperscale PostgreSQL Azure Arc.

> [!CAUTION]
> Certains des niveaux et modes des services de données sont [généralement disponibles (GA)](release-notes.md), tandis que d’autres sont en préversion. Nous vous recommandons de ne pas mélanger les services GA et en préversion sur le même contrôleur de données. Si vous mélangez les services GA et en préversion sur le même contrôleur de données, vous ne pouvez pas opérer de mise à niveau sur place. Dans ce scénario, lorsque vous souhaitez opérer une mise à niveau, vous devez supprimer et recréer le contrôleur de données et les services de données.

## <a name="overview-create-an-azure-arc-enabled-data-controller"></a>Présentation : Créer un contrôleur de données compatible Azure Arc

Vous pouvez créer des services de données Azure Arc sur différents types de clusters Kubernetes, et vous pouvez créer des services Kubernetes managés à l’aide de différentes approches.

Actuellement, la liste des services Kubernetes et distributions validée est la suivante :

- Amazon Elastic Kubernetes Service (Amazon EKS)
- Azure Kubernetes Service (AKS)
- Service Azure Kubernetes sur Azure Stack HCI
- Azure Red Hat OpenShift
- Google Kubernetes Engine (GKE)
- Kubernetes open source en amont (généralement déployé à l’aide de kubeadm)
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * La version minimale prise en charge de Kubernetes est la version v1.19. Pour plus d’informations, consultez la section « Problèmes connus » dans les [Notes de publication &nbsp; - Services de données Azure Arc](./release-notes.md#known-issues).
> * La version minimale prise en charge d’OCP est 4.7.
> * si vous utilisez Azure Container Service, la taille de machine virtuelle du nœud Worker de votre cluster doit être au moins Standard_D8s_v3 et utiliser des disques Premium. 
> * Le cluster ne doit pas s’étendre sur plusieurs zones de disponibilité. 
> * Pour plus d’informations, consultez la section « Problèmes connus » dans les [Notes de publication &nbsp; - Services de données Azure Arc](./release-notes.md#known-issues).

Lorsque vous créez des services de données Azure Arc, indépendamment du service ou de l’option de distribution que vous choisissez, vous devez fournir les informations suivantes :

- **Nom du contrôleur de données** : nom descriptif de votre contrôleur de données (par exemple, *production-dc* ou *seattle-dc*). Le nom doit respecter les [normes de dénomination de Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).

- **Nom d’utilisateur** : nom d’utilisateur de l’administrateur de Kibana/Grafana.
- **Mot de passe** : mot de passe de l’administrateur de Kibana/Grafana.
- **Nom de votre espace de noms Kubernetes** : nom de l'espace de noms Kubernetes dans lequel vous souhaitez créer le contrôleur de données.
- **Mode de connectivité** : détermine le degré de connectivité de votre environnement de services de données Azure Arc vers Azure. Le mode de connectivité *indirecte* est généralement disponible, et le mode de connectivité *directe* est en préversion. Votre choix du mode de connectivité détermine les options des méthodes de déploiement. Pour plus d’informations, consultez [Exigences et modes de connectivité réseau](./connectivity.md).
- **ID d'abonnement Azure** : GUID de l'abonnement Azure dans lequel vous souhaitez que soit créée la ressource de contrôleur de données dans Azure. Toutes les instances managées par Azure SQL et les groupes de serveurs hyperscale Azure Database pour PostgreSQL sont également créés dans et facturés pour cet abonnement.
- **Nom du groupe de ressources Azure** : nom du groupe de ressources dans lequel vous souhaitez que soit créée la ressource de contrôleur de données dans Azure. Toutes les instances managées par Azure SQL et les groupes de serveurs hyperscale Azure Database pour PostgreSQL sont également créés dans et facturés pour ce groupe de ressources.
- **Emplacement Azure** : emplacement Azure où les métadonnées de la ressource de contrôleur de données seront stockées. Pour obtenir la liste des régions disponibles, consultez la page [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) pour l’infrastructure globale Azure. Les métadonnées et les informations de facturation relatives aux ressources Azure gérées par le contrôleur de données que vous déployez sont stockées uniquement à l’emplacement dans Azure que vous spécifiez comme paramètre d’emplacement. Si vous effectuez le déploiement en mode de connectivité directe, le paramètre d’emplacement du contrôleur de données est le même que celui de votre ressource d’emplacement personnalisé ciblé.
- **Informations sur le principal du service** : 
   - Si vous effectuez un déploiement en mode de connectivité **indirecte**, vous aurez besoin d’informations de principal du service pour télécharger les données d’utilisation et de métriques. Pour plus d’informations, consultez la section « Attribuer des rôles au principal du service » de [Charger les données d’utilisation, les métriques et les journaux sur Azure](upload-metrics-and-logs-to-azure-monitor.md). 

- **Infrastructure** : à des fins de facturation, vous devez indiquer l’infrastructure sur laquelle vous exécutez les services de données avec Azure Arc. Les options sont :
- `alibaba`
- `aws`
- `azure`
- `gcp`
- `onpremises`
- `other`

## <a name="additional-concepts-for-direct-connectivity-mode"></a>Concepts supplémentaires pour le mode de connectivité directe

Comme expliqué dans [Modes et spécifications de la connectivité](./connectivity.md), vous pouvez déployer le contrôleur de données Azure Arc en mode de connectivité **directe** ou **indirecte**. Le déploiement des services de données Azure Arc en mode de connectivité directe nécessite des concepts et considérations supplémentaires :

* Tout d’abord, le cluster Kubernetes dans lequel les services de données avec Azure Arc seront déployés doit être un [cluster Kubernetes avec Azure Arc](../kubernetes/overview.md). En connectant votre cluster Kubernetes à Azure, vous pouvez déployer et gérer les services de données Azure Arc sur votre cluster directement à partir du Portail Azure, télécharger automatiquement votre utilisation, vos journaux et vos mesures dans Azure et bénéficier de plusieurs autres avantages Azure. Pour découvrir comment, consultez [Connecter votre cluster à Azure](../kubernetes/quickstart-connect-cluster.md).

* Une fois que le cluster Kubernetes est compatible avec Azure Arc, déployez les services de données Azure Arc en procédant comme suit :
   1. Créez l’extension des services de données Azure Arc. Pour en savoir plus, consultez [Extensions de cluster sur Kubernetes avec Azure Arc](../kubernetes/conceptual-extensions.md).
   1. Créer un emplacement personnalisé. Pour en savoir plus, consultez [Emplacements personnalisés sur Kubernetes avec Azure Arc](../kubernetes/conceptual-custom-locations.md).
   1. Créez le contrôleur de données Azure Arc.

   Vous pouvez effectuer ces trois étapes en une seule à l’aide de l’Assistant Création de contrôleur de données Azure Arc dans le portail Azure.

Une fois le contrôleur de données Azure Arc installé, vous pouvez créer et accéder à des services de données tels qu’une instance managée SQL ou une instance PostgreSQL Hyperscale compatibles avec Azure Arc.


## <a name="next-steps"></a>Étapes suivantes

Vous avez plusieurs options supplémentaires pour créer le contrôleur de données Azure Arc :

> **Vous voulez juste essayer ?**
> Commencez rapidement avec [Démarrage rapide avec Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) sur AKS, Amazon EKS ou GKE, ou dans une machine virtuelle Azure.
>
- [Créez un contrôleur de données en mode de connectivité directe avec le portail Azure](create-data-controller-direct-prerequisites.md)
- [Créez un contrôleur de données en mode de connectivité indirecte avec l’interface CLI](create-data-controller-indirect-cli.md)
- [Créez un contrôleur de données en mode de connectivité indirecte avec Azure Data Studio](create-data-controller-indirect-azure-data-studio.md)
- [Créez un contrôleur de données en mode de connectivité indirecte à partir du portail Azure via un notebook Jupyter dans Azure Data Studio](create-data-controller-indirect-azure-portal.md)
- [Créez un contrôleur de données en mode de connectivité indirecte avec des outils Kubernetes tels que kubectl ou oc](create-data-controller-using-kubernetes-native-tools.md)
