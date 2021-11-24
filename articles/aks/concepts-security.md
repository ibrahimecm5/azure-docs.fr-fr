---
title: 'Concepts : sécurité dans AKS (Azure Kubernetes Service)'
description: Découvrez la sécurité dans AKS (Azure Kubernetes Service), notamment la communication entre les maîtres et les nœuds, les stratégies réseau et les secrets Kubernetes.
services: container-service
author: miwithro
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: miwithro
ms.openlocfilehash: b29d7f245ce809745665bbeb4ea5cb858014e23b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490759"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Concepts de sécurité pour les applications et les clusters dans AKS (Azure Kubernetes Service)

La sécurité des conteneurs protège l’ensemble du pipeline de bout en bout du build aux charges de travail d’application s’exécutant dans le service Azure Kubernetes (AKS).

La chaîne d’approvisionnement sécurisée comprend l’environnement et le registre de génération.

Kubernetes inclut des composants de sécurité, tels que les *normes de sécurité des pods* et les *secrets*. En outre, Azure comprend des composants tels que Active Directory, Microsoft Defender pour le Cloud, Azure Policy, Azure Key Vault, les groupes de sécurité réseau et les mises à niveau de clusters orchestrées. AKS associe ces composants de sécurité à :
* Fournir un scénario complet d’authentification et d’autorisation.
* Tirer parti d’Azure Policy intégré à AKS pour sécuriser vos applications.
* Des informations de bout en bout du build dans votre application avec Microsoft Defender for Containers.
* Conserver votre cluster AKS exécutant les dernières mises à jour de sécurité du système d’exploitation et les dernières versions de Kubernetes.
* Fournir un trafic de pod sécurisé et un accès aux informations d’identification sensibles.

Cet article présente les concepts fondamentaux qui sécurisent vos applications dans AKS :

- [Concepts de sécurité pour les applications et les clusters dans AKS (Azure Kubernetes Service)](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Intégrer la sécurité](#build-security)
  - [Sécurité des registres](#registry-security)
  - [Sécurité des clusters](#cluster-security)
  - [Sécurité des nœuds](#node-security)
    - [Isolation du calcul](#compute-isolation)
  - [Mise à niveau des clusters](#cluster-upgrades)
    - [Isolation et drainage](#cordon-and-drain)
  - [Sécurité du réseau](#network-security)
    - [Groupes de sécurité réseau Azure](#azure-network-security-groups)
  - [Sécurité des applications](#application-security)
  - [Secrets Kubernetes](#kubernetes-secrets)
  - [Étapes suivantes](#next-steps)

## <a name="build-security"></a>Sécurité de build

En tant que point d’entrée pour la chaîne d’approvisionnement, il est important d’effectuer une analyse statique des builds d’image avant qu’elles ne soient promues dans le pipeline. Cela comprend l’évaluation de la conformité et de la vulnérabilité.  Il ne s’agit pas d’un échec de la génération d’une build à cause d’une forte vulnérabilité, car cela rompt le développement. Il s’agit de l’examen du « statut du fournisseur » en fonction des vulnérabilités qui peuvent être exploitées par les équipes de développement.  Tirez également parti des « périodes de grâce » pour permettre aux développeurs de corriger les problèmes identifiés. 

## <a name="registry-security"></a>Sécurité des registres

L’évaluation de l’état des vulnérabilités de l’image dans le registre détecte la dérive et intercepte également les images qui n’ont pas été issues de votre environnement de génération. Utilisez [Notary V2](https://github.com/notaryproject/notaryproject) pour joindre des signatures à vos images pour vous assurer que les déploiements proviennent d’un emplacement approuvé.

## <a name="cluster-security"></a>Sécurité des clusters

Dans AKS, les composants maîtres Kubernetes font partie du service managé fourni et tenu à jour par Microsoft. Chaque cluster AKS a son propre maître Kubernetes dédié monolocataire pour fournir le serveur d’API, le planificateur, etc.

Par défaut, le serveur d’API Kubernetes utilise une adresse IP publique avec un nom de domaine complet (FQDN). Vous pouvez limiter l’accès au point de terminaison du serveur d’API à l’aide de [plages d’adresses IP autorisées][authorized-ip-ranges]. Vous pouvez également créer un [cluster entièrement privé][private-clusters] pour limiter l’accès du serveur d’API à votre réseau virtuel.

Vous pouvez contrôler l’accès au serveur d’API avec des contrôles d’accès en fonction du rôle Kubernetes (RBAC Kubernetes) et le RBAC Azure. Pour plus d’informations, consultez [Intégration d’Azure AD à AKS][aks-aad].

## <a name="node-security"></a>Sécurité des nœuds

Les nœuds AKS sont des machines virtuelles Azure dont vous assurez la gestion et la maintenance. 
* Les nœuds Linux exécutent une distribution Ubuntu optimisée à l’aide de `containerd` ou du runtime de conteneur Docker. 
* Les nœuds Windows Server exécutent une version Windows Server 2019 optimisée utilisant le `containerd` ou le runtime de conteneur Docker.

Quand un cluster AKS est créé ou fait l’objet d’un scale-up, les nœuds sont déployés automatiquement avec les dernières configurations et mises à jour de sécurité du système d’exploitation.

> [!NOTE]
> Les clusters AKS utilisant :
> * Kubernetes version 1.19 et ultérieure pour les pools de nœuds Linux utilisent `containerd` comme runtime de conteneur. L’utilisation de `containerd` avec les pools de nœuds Windows Server 2019 est actuellement en préversion. Pour plus d’informations, consultez [Ajouter un pool de nœuds Windows Server avec `containerd`][aks-add-np-containerd].
> * Une version de Kubernetes antérieure à la v1.19 pour les pools de nœuds Linux utilisent Docker comme runtime de conteneur. Pour les pools de nœuds Windows Server 2019, Docker est le runtime de conteneur par défaut.

### <a name="node-security-patches"></a>Correctifs de sécurité du nœud

#### <a name="linux-nodes"></a>Nœuds Linux
Chaque soir, les nœuds Linux dans AKS obtiennent les correctifs de sécurité par le biais de leur canal de mise à jour de distribution. Ce comportement est configuré automatiquement à mesure que les nœuds sont déployés dans un cluster AKS. Pour minimiser les perturbations et l’impact potentiel sur les charges de travail en cours d’exécution, les nœuds ne sont pas automatiquement redémarrés si un correctif de sécurité ou mise à jour du noyau l’exige. Pour plus d’informations sur le traitement des redémarrages de nœud, consultez la section[Appliquer des mises à jour de sécurité et du noyau à des nœuds dans AKS][aks-kured].

Les mises à jour nocturnes appliquent les mises à jour de sécurité au système d’exploitation sur le nœud, mais l’image de nœud utilisée pour créer les nœuds de votre cluster reste inchangée. Si un nouveau nœud Linux est ajouté à votre cluster, l’image d’origine est utilisée pour créer le nœud. Ce nouveau nœud recevra toutes les mises à jour de sécurité et de noyau disponibles au cours de la vérification automatique chaque nuit, mais restera non corrigé jusqu’à ce que toutes les vérifications et tous les redémarrages soient terminés. Vous pouvez utiliser la mise à niveau d’image de nœud pour vérifier et mettre à jour les images de nœud utilisées par votre cluster. Pour plus d’informations sur la mise à niveau d’une image de nœud, consultez [mise à niveau d’une image de nœud dans le Service Azure Kubernetes (AKS)][node-image-upgrade].

#### <a name="windows-server-nodes"></a>Nœuds Windows Server

Pour les nœuds Windows Server, Windows Update n’exécute pas et n’applique pas automatiquement les dernières mises à jour. Planifiez les mises à niveau des pools de nœuds Windows Server dans votre cluster AKS autour du cycle de publication de Windows Update standard et de votre propre processus de validation. Ce processus de mise à niveau crée des nœuds qui exécutent la dernière image et les derniers correctifs de Windows Server, puis supprime les anciens nœuds. Pour plus d’informations sur ce processus, consultez [Mettre à niveau un pool de nœuds dans AKS][nodepool-upgrade].

### <a name="node-deployment"></a>Déploiement de nœuds
Les nœuds sont déployés sur un sous-réseau de réseau virtuel privé, sans aucune adresse IP publique affectée. À des fins de dépannage et de gestion, SSH est activé par défaut et est uniquement accessible à partir de l’adresse IP interne.

### <a name="node-storage"></a>Stockage du nœud
Pour fournir le stockage, les nœuds utilisent Azure Disques managés. Pour la plupart des tailles de nœud de machine virtuelle, les disques managés Azure sont des disques Premium assortis de disques SSD hautes performances. Les données stockées sur les disques managés sont automatiquement chiffrées au repos au sein de la plateforme Azure. Pour améliorer la redondance, les disques managés Azure sont répliqués de manière sécurisée au sein du centre de données Azure.

### <a name="hostile-multi-tenant-workloads"></a>Charges de travail multi-locataires hostiles

Actuellement, les environnements Kubernetes ne sont pas sûrs pour une utilisation multilocataire hostile. Des fonctionnalités de sécurité supplémentaires, telles que les *stratégies de sécurité de pod* ou le RBAC Kubernetes pour les nœuds, bloquent efficacement les attaques. Pour une véritable sécurité lors de l’exécution de charges de travail multilocataires hostiles, ne faites confiance qu’à un hyperviseur. Le domaine de sécurité de Kubernetes devient le cluster, et non un nœud individuel. 

Pour ces types de charges de travail multi-locataires hostiles, vous devez utiliser des clusters physiquement isolés. Pour plus d’informations sur les méthodes d’isolation des charges de travail, consultez [Meilleures pratiques relatives à l’isolation de clusters dans AKS][cluster-isolation].

### <a name="compute-isolation"></a>Isolation du calcul

Pour des raisons de conformité ou d’exigences réglementaires, certaines charges de travail peuvent nécessiter un niveau d’isolation élevé par rapport aux autres charges de travail client. Pour ces charges de travail, Azure fournit des [machines virtuelles isolées](../virtual-machines/isolation.md) à utiliser en tant que nœuds d’agent dans un cluster AKS. Ces machines virtuelles sont isolées dans un type de matériel spécifique et dédiées à un client unique. 

Sélectionnez [l’une des tailles de machine virtuelle isolée](../virtual-machines/isolation.md) comme **taille de noyau** lorsque vous créez un cluster AKS ou que vous ajoutez un pool de nœuds.

## <a name="cluster-upgrades"></a>Mise à niveau des clusters

Azure fournit des outils d’orchestration de mise à niveau pour mettre à niveau un cluster AKS et des composants, maintenir la sécurité et la conformité, et accéder aux fonctionnalités les plus récentes. Cette orchestration de la mise à niveau inclut les composants maîtres et agents Kubernetes. 

Pour démarrer le processus de mise à niveau, vous spécifiez [l’une des versions de Kubernetes disponibles](supported-kubernetes-versions.md). Ensuite, Azure isole et draine de manière sécurisée chaque nœud AKS et effectue les mises à niveau.

### <a name="cordon-and-drain"></a>Isolation et drainage

Pendant le processus de mise à niveau, les nœuds AKS sont chacun isolés du cluster afin que de nouveaux pods ne soient pas planifiés sur ces nœuds. Les nœuds sont ensuite drainés et mis à niveau comme suit :

1.  Un nouveau nœud est déployé dans le pool de nœuds. 
    * Ce nœud exécute la dernière image et les derniers correctifs du système d’exploitation.
1. Un des nœuds existants est identifié pour être mis à niveau. 
1. Les pods sur le nœud identifié sont arrêtés normalement et planifiés sur les autres nœuds du pool de nœuds.
1. Ce nœud vidé est supprimé du cluster AKS.
1. Les étapes 1-4 sont répétées jusqu’à ce que tous les nœuds soient correctement remplacés dans le cadre du processus de mise à niveau.

Pour plus d’informations, consultez [Mettre à niveau un cluster AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Sécurité du réseau

Pour la connectivité et la sécurité avec les réseaux locaux, vous pouvez déployer votre cluster AKS sur des sous-réseaux de réseau virtuel Azure existants. Ces réseaux virtuels se reconnectent à votre réseau local à l’aide d’un VPN de site à site ou d’Express Route. Définissez des contrôleurs d’entrée Kubernetes avec des adresses IP internes privées pour limiter l’accès aux services à la connexion réseau interne.

### <a name="azure-network-security-groups"></a>Groupes de sécurité réseau Azure

Pour filtrer le flux du trafic dans les réseaux virtuels, Azure utilise des règles de groupe de sécurité réseau. Ces règles définissent les plages d’adresses IP source et de destination, les ports et les protocoles qui se voient autoriser ou refuser l’accès aux ressources. Des règles par défaut sont créées pour autoriser le trafic TLS vers le serveur d’API Kubernetes. Vous créez des services avec des équilibreurs de charge, des mappages de port ou des itinéraires entrants. AKS modifie automatiquement le groupe de sécurité réseau pour le flux de trafic.

Si vous fournissez votre propre sous-réseau pour votre cluster AKS (que vous utilisiez l’interface Azure CNI ou Kubernet), **ne modifiez pas** le groupe de sécurité réseau au niveau de la carte réseau gérée par AKS. Au lieu de cela, créez d’autres groupes de sécurité réseau au niveau du sous-réseau pour modifier le flux du trafic. Assurez-vous qu’ils n’interfèrent pas avec le trafic nécessaire qui gère le cluster, par exemple l’accès à l’équilibreur de charge, la communication avec le plan de contrôle et la [sortie][aks-limit-egress-traffic].

### <a name="kubernetes-network-policy"></a>Stratégie de réseau Kubernetes

Pour limiter le trafic réseau entre les pods de votre cluster, AKS propose la prise en charge de [stratégies de réseau Kubernetes][network-policy]. Les stratégies de réseau vous permettent d’autoriser ou de refuser des chemins d’accès réseau spécifiques au sein du cluster en fonction des espaces de noms et des sélecteurs d’étiquettes.

## <a name="application-security"></a>Sécurité des applications

Pour protéger les pod en cours d’exécution sur AKS, utilisez[Microsoft Defender pour Kubernetes][azure-defender-for-kubernetes] afin de détecter et de restreindre les attaques contre les cybercriminels contre vos applications qui s’exécutent dans vos pods.  Effectuez une analyse continue pour détecter les dérives dans l'état de vulnérabilité de votre application et mettez en place un processus bleu/vert/canari pour corriger et remplacer les images vulnérables. 


## <a name="kubernetes-secrets"></a>Secrets Kubernetes

Avec un *secret* Kubernetes, vous injectez des données sensibles dans des pods, telles que les clés ou les informations d’identification d’accès. 
1. Créez un secret en vous servant de l’API Kubernetes. 
1. Définissez votre pod ou déploiement et demandez un secret spécifique. 
    * Les secrets sont fournis uniquement aux nœuds avec un pod planifié qui en a besoin.
    * Le secret est stocké dans *tmpfs*, qui n’est pas écrit sur le disque. 
1. Lorsque vous supprimez le dernier pod sur un nœud nécessitant un secret, ce dernier est supprimé du tmpfs du nœud. 
   * Les secrets sont stockés dans un espace de noms donné et ne sont accessibles qu’aux pods se trouvant dans cet espace de noms.

L’utilisation de secrets réduit la quantité d’informations sensibles définies dans le manifeste YAML des pods ou services. Au lieu de cela, vous demandez le secret stocké sur le serveur d’API Kubernetes dans le cadre de votre manifeste YAML. Ainsi, l’accès au secret n’est accordé qu’au pod concerné. 

> [!NOTE]
> Les fichiers manifeste secrets bruts contiennent les données secrètes au format base64 (consultez la [documentation officielle][secret-risks] pour plus d’informations). Traitez ces fichiers comme des informations sensibles et ne les validez jamais dans le contrôle de code source.

Les secrets Kubernetes sont stockés dans etcd, un magasin de clés-valeurs distribué. Le magasin etcd est entièrement managé par AKS et [les données sont chiffrées au repos sur la plateforme Azure][encryption-atrest]. 

## <a name="next-steps"></a>Étapes suivantes

Pour vous familiariser avec la sécurisation de vos clusters AKS, consultez [Mettre à niveau un cluster AKS][aks-upgrade-cluster].

Pour connaître les meilleures pratiques associées, voir [Meilleures pratiques relatives aux mises à jour et à la sécurité du cluster dans AKS][operator-best-practices-cluster-security] et [Bonnes pratiques relatives à la sécurité de pod dans AKS][developer-best-practices-pod-security].

Pour plus d’informations sur les concepts fondamentaux de Kubernetes et d’AKS, consultez :

- [Clusters et charges de travail Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identité Kubernetes/AKS][aks-concepts-identity]
- [Réseaux virtuels Kubernetes/AKS][aks-concepts-network]
- [Stockage Kubernetes/AKS][aks-concepts-storage]
- [Mise à l’échelle Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[azure-defender-for-kubernetes]: ../defender-for-cloud/container-security.md
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-add-np-containerd]: windows-container-cli.md#add-a-windows-server-node-pool-with-containerd-preview
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-kured]: node-updates-kured.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
[node-image-upgrade]: node-image-upgrade.md
