---
title: Versions de Azure Service Fabric
description: Notes de publication pour Azure Service Fabric. Inclut des informations sur les dernières fonctionnalités et améliorations incluses dans Service Fabric.
ms.date: 04/13/2021
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 0555bf71402230cf8264a271ce3ac6cf59357c12
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134041"
---
# <a name="service-fabric-releases"></a>Versions de Azure Service Fabric

Cet article fournit des informations sur les version et mises à jour les plus récentes du runtime Service Fabric et des Kits de développement logiciel (SDK).

Les ressources suivantes sont également disponibles :
- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guides de résolution des problèmes</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Suivi des problèmes</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Options de support</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Versions prises en charge</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Exemples de code</a>


## <a name="service-fabric-82"></a>Service Fabric 8.2

Nous avons le plaisir d’annoncer que la version 8.2 du runtime Service Fabric a commencé à être déployée dans les différentes régions Azure, avec des mises à jour des outils et des kits de développement logiciel (SDK). Les mises à jour des Kits de développement logiciel (SDK) .NET et Java et du runtime Service Fabric sont disponibles via Web Platform Installer, les packages NuGet et les référentiels Maven.

### <a name="key-announcements"></a>Principales annonces
- Exposer une API dans le gestionnaire de cluster pour voir si la mise à niveau a un impact

### <a name="service-fabric-82-releases"></a>Versions de Service Fabric 8.2
| Date de publication | Libérer | En savoir plus |
|---|---|---|
| 29 octobre 2021 | [Azure Service Fabric 8.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-2-release/ba-p/2895108)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_82.md)|


## <a name="service-fabric-81"></a>Service Fabric 8.1

Nous avons le plaisir d’annoncer que la version 8.1 du runtime Service Fabric a commencé à être déployée dans les différentes régions Azure, avec des mises à jour des outils et des kits SDK. Les mises à jour des Kits de développement logiciel (SDK) .NET et Java et du runtime Service Fabric sont disponibles via Web Platform Installer, les packages NuGet et les référentiels Maven.

### <a name="key-announcements"></a>Principales annonces
- Ajout de la prise en charge du réplica auxiliaire
- **Préversion** Ajout de la prise en charge pour les applications Service Fabric .NET 6.0
- Ajout de la prise en charge d’API pour la mise à jour des descriptions d’application
- Ajout d’une commande ping périodique entre Reconfiguration Agent (RA) et Reconfiguration Agent Proxy (RAP) pour détecter les défaillances IPC et traiter les blocages
- Ajout de la prise en charge des sondes liveness et readiness pour les applications non conteneurisées
- Mise à niveau des clusters pour les mises à jour de capacité de nœud désormais sans impact

### <a name="service-fabric-81-releases"></a>Versions de Service Fabric 8.1
| Date de publication | Libérer | En savoir plus |
|---|---|---|
| 28 juillet 2021 | [Azure Service Fabric 8.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-1-release/ba-p/2594194)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_81.md)|
| 13 août 2021 | [Première mise en production actualisée d’Azure Service Fabric 8.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-1-first-refresh-release/ba-p/2638798) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_81CU1.md) |
| 09 septembre 2021 | [Deuxième mise en production actualisée d’Azure Service Fabric 8.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-1-second-refresh-release/ba-p/2734904) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_81CU2.md) |
| 6 octobre 2021 | [Troisième mise en production actualisée d’Azure Service Fabric 8.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-1-third-refresh-release/ba-p/2816117) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_81CU3.md) |


## <a name="service-fabric-80"></a>Service Fabric 8.0

Nous avons le plaisir d’annoncer que la version 8.0 du runtime Service Fabric a commencé à être déployée dans les différentes régions Azure, avec des mises à jour des outils et des kits SDK. Les mises à jour des Kits de développement logiciel (SDK) .NET et Java et du runtime Service Fabric sont disponibles via Web Platform Installer, les packages NuGet et les référentiels Maven.

### <a name="key-announcements"></a>Principales annonces

- **Disponibilité générale** de la prise en charge de .NET 5 pour Windows
- **Disponibilité générale** des [NodeTypes sans état](./service-fabric-stateless-node-types.md)
- Possibilité de déplacer des instances de service sans état
- Possibilité d’ajouter des DefaultLoad paramétrisés dans le manifeste de l’application
- Pour les mises à niveau des réplicas singleton : possibilité de définir certains paramètres de niveau cluster au niveau de l’application
- Possibilité d’un positionnement intelligent basé sur les étiquettes de nœud
- Possibilité de définir le seuil en pourcentage des nœuds non sains qui influencent l’intégrité du cluster
- Possibilité d’interroger les principaux services chargés
- Possibilité d’ajouter un nouvel intervalle pour les nouveaux codes d’erreur
- Possibilité de marquer l’instance de service comme terminée
- Prise en charge du modèle de déploiement par vague pour les mises à niveau automatiques
- Ajout d’une sonde de préparation pour les applications conteneurisées
- Affectation par défaut de la valeur true à UseSeparateSecondaryMoveCost
- Correction de StateManager de façon à publier la référence dès que la publication est sans risque
- Bloquer la suppression du service de secrets central lors du stockage des secrets d’utilisateur

### <a name="service-fabric-80-releases"></a>Mises en production de Service Fabric 8.0
| Date de publication | Libérer | En savoir plus |
|---|---|---|
| 08 avril 2021 | [Azure Service Fabric 8.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-release/ba-p/2260016)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80.md)|
| 17 mai 2021 | [Azure Service Fabric 8.0 First Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-first-refresh-release/ba-p/2362556) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80CU1.md) |
| 17 juin 2021 | [Azure Service Fabric 8.0 Second Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-second-refresh-release/ba-p/2462979) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80CU2.md) |
| 28 juillet 2021 | [Azure Service Fabric 8.0 Third Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-third-refresh-release/ba-p/2594180) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80CU3.md) |


## <a name="previous-versions"></a>Versions précédentes

### <a name="service-fabric-72"></a>Service Fabric 7.2

#### <a name="key-announcements"></a>Principales annonces

- **Préversion** : [**Les clusters managés Service Fabric**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) sont désormais disponibles en préversion publique. Les clusters managés Service Fabric visent à simplifier le déploiement et la gestion des clusters en encapsulant les ressources sous-jacentes qui composent un cluster Service Fabric dans une ressource ARM unique. Pour plus d’informations, consultez [Vue d’ensemble des clusters managés Service Fabric](./overview-managed-cluster.md).
- **Préversion** : [**La prise en charge des services sans état avec un nombre d’instances supérieur au nombre de nœuds**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) est désormais disponible en préversion publique. Une stratégie de positionnement permet la création de plusieurs instances sans état d’une partition sur un nœud.
- [**FabricObserver (FO) 3.0**](https://aka.ms/sf/fabricobserver) est maintenant disponible.
    - Vous pouvez désormais exécuter FabricObserver dans des clusters Linux et Windows.
    - Vous pouvez désormais créer des plug-ins d’observation personnalisés. Pour plus d’informations et de code, consultez le [fichier Lisez-moi relatif aux plug-ins](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) et l’[exemple de projet de plug-in](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin).
    - Vous pouvez maintenant modifier n’importe quel paramètre d’observation via la mise à niveau des paramètres d’application. Cela signifie que vous n’avez plus besoin de redéployer FO pour modifier des paramètres d’observation spécifiques. Consultez cet [exemple](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Prise en charge des images conteneur Ubuntu 18.04 OneBox**](https://hub.docker.com/_/microsoft-service-fabric-onebox).
- **Préversion** : [**La référence au coffre de clés pour les applications Service Fabric prend en charge **UNIQUEMENT les secrets avec version**. Les secrets sans versions ne sont pas pris en charge.**](./service-fabric-keyvault-references.md)
- Le kit de développement logiciel (SDK) SF requiert la mise à jour la plus récente de VS 2019, 16.7.6 ou 16.8 Preview 4, pour pouvoir créer de nouveaux projets .NET Framework sans état/avec état/d’acteurs. Si vous ne disposez pas de la dernière mise à jour de VS, après avoir créé le projet de service, utilisez le gestionnaire de package pour installer Microsoft.ServiceFabric.Services (version 4.2.x) pour les projets avec état/sans état et Microsoft.ServiceFabric.Actors (version 4.2.x) pour les projets d’acteur de nuget.org.
- **RunToCompletion** : Service Fabric prend en charge le concept d’exécution jusqu’à l’achèvement pour les exécutables invités. Avec cette mise à jour, une fois l’exécution du réplica terminée, les ressources de cluster allouées à ce réplica seront libérées.
- [**La prise en charge de la gouvernance des ressources a été améliorée**](./service-fabric-resource-governance.md) : elle autorise les requêtes et limite les spécifications pour les ressources de processeur et de mémoire.

#### <a name="service-fabric-72-releases"></a>Mises en production Service Fabric 7.2
| Date de publication | Libérer | En savoir plus |
|---|---|---|
| 21 octobre 2020 | [Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 9 novembre 2020 | [Azure Service Fabric 7.2 Second Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 10 novembre 2020  | Azure Service Fabric 7.2 Third Refresh Release | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2 décembre 2020 | [Azure Service Fabric 7.2 Fourth Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 25 janvier 2021 | [Cinquième mise en production actualisée d’Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 17 février 2021 | [Sixième mise en production actualisée d’Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 10 mars 2021 | [Septième mise en production actualisée d’Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)


### <a name="service-fabric-71"></a>Service Fabric 7.1

En raison de la crise actuelle liée au COVID-19, et en prenant en compte les défis auxquels nos clients font face, nous rendons la version 7.1 disponible, mais nous ne mettrons pas automatiquement à niveau les clusters définis pour recevoir des mises à niveau automatiques. Nous suspendons les mises à niveau automatiques jusqu’à nouvel ordre, pour nous assurer que les clients peuvent appliquer les mises à niveau lorsque cela leur convient, afin d’éviter des interruptions inattendues.

Vous pouvez effectuer la mise à jour vers la version 7.1 via le [Portail Azure](./service-fabric-cluster-upgrade-version-azure.md#manual-upgrades-with-azure-portal) ou via un [déploiement Azure Resource Manager](./service-fabric-cluster-upgrade-version-azure.md#resource-manager-template).

Les clusters Service Fabric avec des mises à niveau automatiques activées commenceront à recevoir automatiquement la mise à jour 7.1 une fois la procédure de déploiement standard relancée. Nous ferons une autre annonce avant le lancement du déploiement standard sur le [site Service Fabric Tech Community](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
Nous avons également publié des mises à jour jusqu’à la date de fin de support des versions principales allant de la version 6.5 à la version 7.1, [ici](./service-fabric-versions.md). 

#### <a name="key-announcements"></a>Principales annonces

- **Disponibilité générale** des [**identités managées de Service Fabric pour les applications Service Fabric**](./concepts-managed-identity.md)
- [**Prise en charge d’Ubuntu 18.04.**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Préversion : Prise en charge de disques Ephemeral OS de groupe de machines virtuelles identiques**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)** : Les disques de système d’exploitation éphémères sont des dispositifs de stockage créés sur la machine virtuelle locale, qui ne sont pas enregistrés sur le Stockage Azure à distance. Ils sont recommandés pour tous les types de nœuds Service Fabric (principaux et secondaires) car, par rapport aux disques de système d’exploitation permanents traditionnels, les disques de système d’exploitation éphémères :
      -  réduisent la latence de lecture/écriture sur le disque du système d’exploitation ;
      -  permettent des opérations plus rapides de gestion de réinitialisation/réimagerie de nœuds ;
      -  réduisent les coûts globaux (les disques sont gratuits et n’impliquent aucun coût de stockage supplémentaire).
- La prise en charge de la déclaration de [**certificats de point de terminaison d’applications Service Fabric par nom commun d’objet**](./service-fabric-service-manifest-resources.md).
- [**Prise en charge des sondes d’intégrité pour les services en conteneur**](./probes-codepackage.md) : Prise en charge du mécanisme Probe Liveness pour les applications en conteneur. Le diagnostic Probe Liveness vous aide à annoncer l’activité de l’application en conteneur et lorsqu’il ne répond pas dans les délais, cela entraîne un redémarrage. 
- [**Prise en charge des packages de code d’initialiseur**](./initializer-codepackages.md) pour les [conteneurs](./service-fabric-containers-overview.md) et les applications [d’exécutables invités](./service-fabric-guest-executables-introduction.md). Cela permet d’exécuter des packages de code (par exemple, des conteneurs), dans un ordre spécifié, afin d’effectuer l’initialisation du package de services.
- **FabricObserver et ClusterObserver** sont des applications sans état qui capturent les données de télémétrie Service Fabric liées à différents aspects d’un cluster SF. Ces deux applications sont prêtes à être déployées sur des clusters de production Windows pour capturer une télémétrie riche avec prise en charge implémentée d’ApplicationInsights, d’EventSource et de LogAnalytics.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer) : s’exécute sur tous les nœuds, génère des événements d’intégrité, émet la télémétrie quand les seuils d’utilisation des ressources configurés par l’utilisateur sont atteints. Cette version contient plusieurs améliorations au niveau de la surveillance, de la gestion des données, des détails des événements d’intégrité et de la télémétrie structurée.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) : s’exécute sur un nœud, capture la télémétrie d’intégrité au niveau du cluster. Dans cette version, ClusterObserver surveille également l’état des nœuds et émet des données de télémétrie quand le nœud est en panne/en cours de désactivation/désactivé pendant une période plus longue que celle spécifiée par l’utilisateur.

#### <a name="improve-application-life-cycle-experience"></a>Améliorer l’expérience de cycle de vie des applications

- **[Préversion : demande de vidage](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** : Pendant la maintenance planifiée du service, comme les mises à niveau du service ou la désactivation du nœud, vous souhaitez autoriser les services à purger correctement les connexions. Cette fonctionnalité ajoute une durée de délai de fermeture de l’instance dans la configuration du service. Pendant les opérations planifiées, SF supprime l’adresse du service de la découverte, puis attend pendant cette durée avant d’arrêter le service.
- **[Détection et équilibrage automatiques du sous-cluster](./cluster-resource-manager-subclustering.md)** : Le sous-clustering se produit quand des services avec des contraintes de placement différentes ont une [métrique de charge](./service-fabric-cluster-resource-manager-metrics.md) commune. Si la charge sur les différents jeux de nœuds diffère considérablement, le Gestionnaire des ressources clusters Service Fabric estime que le cluster est déséquilibré, même s’il a le meilleur équilibre possible en raison des contraintes de placement. Par conséquent, il tente de rééquilibrer le cluster, ce qui peut entraîner des mouvements de service inutiles (puisque le « déséquilibre » ne peut pas être réellement amélioré). À partir de cette version, le Gestionnaire des ressources clusters tentera à présent de détecter automatiquement ces types de configurations et de comprendre quand le déséquilibre peut être résolu par le biais du mouvement et quand, au contraire, il ne doit toucher à rien, car aucune amélioration substantielle ne peut être apportée.  
- [**Coût de déplacement différent pour les réplicas secondaires**](./service-fabric-cluster-resource-manager-movement-cost.md) : Nous avons introduit une nouvelle valeur de coût (VeryHigh) de déplacement qui offre une plus grande flexibilité dans certains scénarios pour définir si un coût de déplacement distinct doit être utilisé pour les réplicas secondaires.
- Mécanisme [**Probe Liveness**](./probes-codepackage.md) activé pour les applications en conteneur. Le diagnostic Probe Liveness vous aide à annoncer l’activité de l’application en conteneur et lorsqu’il ne répond pas dans les délais, cela entraîne un redémarrage.
- [**Exécuter jusqu’à achèvement/une fois pour les services**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Améliorations du Magasin d’images
 - Service Fabric 7.1 utilise **un transport personnalisé pour sécuriser le transfert de fichiers entre les nœuds par défaut**. La dépendance sur le partage de fichiers SMB est supprimée de la version 7.1. Les partages de fichiers SMB sécurisés existent toujours sur les nœuds qui contiennent un réplica du service de magasin d’images pour donner le choix au client de refuser la configuration par défaut et pour la mise à niveau et le passage à une version antérieure.
       
 #### <a name="reliable-collections-improvements"></a>Améliorations de collections fiables

- [**Prise en charge du stockage en mémoire uniquement pour les services avec état utilisant des collections fiables**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections) : La fonctionnalité de collections fiables volatiles permet de stocker les données de façon persistante sur disque pour les protéger contre les pannes à grande échelle, peut être utilisé pour les charges de travail comme le cache répliqué, par exemple, où une perte occasionnelle de données peut être tolérée. Sur la base des [limites et restrictions de Volatile Reliable Collections](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections), nous le recommandons pour les charges de travail qui n’ont pas besoin de persistance, pour les services qui traitent les rares cas de perte de quorum.
- [**Préversion : Explorateur de sauvegarde Service Fabric**](https://github.com/microsoft/service-fabric-backup-explorer) : Pour faciliter la gestion des sauvegardes de collections fiables pour les applications avec état Service Fabric, l’Explorateur de sauvegarde Service Fabric permet aux utilisateurs de faire ce qui suit :
    - Auditer et examiner le contenu des collections fiables
    - Mettre à jour l’état actuel vers une vue cohérente
    - Créer une sauvegarde de l’instantané actuel des collections fiables
    - Corriger la corruption de
                 
#### <a name="service-fabric-71-releases"></a>Mises en production Service Fabric 7.1
| Date de publication | Libérer | En savoir plus |
|---|---|---|
| 20 avril 2020 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Notes de publication](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16 juin 2020 | [Microsoft Azure Service Fabric 7.1 First Refresh](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20 juillet 2020 | [Microsoft Azure Service Fabric 7.1 Second Refresh](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12 août 2020 | [Microsoft Azure Service Fabric 7.1 Third Refresh](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10 septembre 2020 | [Microsoft Azure Service Fabric 7.1 Fourth Refresh](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 7 octobre 2020 | Microsoft Azure Service Fabric 7.1 Sixth Refresh | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 23 novembre 2020 | Microsoft Azure Service Fabric 7.1 Eighth Refresh | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7.0

Azure Service Fabric 7.0 est désormais disponible ! Vous pouvez effectuer la mise à jour vers 7.0 via le Portail Azure ou via un déploiement d’Azure Resource Manager. En raison des commentaires des clients sur les versions pendant les vacances, nous ne commencerons pas à mettre à jour automatiquement les clusters définis pour recevoir des mises à niveau automatiques jusqu’au mois de janvier.
En janvier, nous reprendrons la procédure de déploiement standard et les clusters avec des mises à niveau automatiques activées commenceront à recevoir automatiquement la mise à jour 7.0. Nous ferons une autre annonce avant le début du déploiement.
Nous mettrons également à jour les dates de publication planifiées pour indiquer que cette stratégie est prise en compte. Recherchez des mises à jour sur nos futures [planifications de version](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).

#### <a name="key-announcements"></a>Principales annonces
 - [**Prise en charge de KeyVaultReference pour les secrets de l’application (préversion)**](./service-fabric-keyvault-references.md) : Les applications Service Fabric qui ont activé [Identités managées](./concepts-managed-identity.md) peuvent désormais référencer directement une URL de secret Key Vault en tant que variable d’environnement, paramètre d’application ou informations d’identification de référentiel de conteneur. Service Fabric résoudra automatiquement le secret à l’aide de l’identité managée de l’application. 
     
- **Amélioration de la sécurité de la mise à niveau pour les services sans état** : Pour garantir la disponibilité lors de la mise à niveau d’une application, nous avons introduit de nouvelles configurations pour définir le [nombre minimal d’instances pour les services sans état](/dotnet/api/system.fabric.description.statelessservicedescription) à considérer comme disponibles. Auparavant, cette valeur était 1 pour tous les services et n’était pas modifiable. Grâce à cette nouvelle vérification de la sécurité par service, vous pouvez vous assurer que vos services conservent un nombre minimal d’instances lors des mises à niveau de l’application, des mises à niveau de cluster et d’autres opérations de maintenance qui s’appuient sur les contrôles d’intégrité et de sécurité de Service Fabric.
  
- [**Limites des ressources des services utilisateur**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services) : Les utilisateurs peuvent configurer des limites de ressources pour les services utilisateur sur un nœud afin d’éviter des scénarios tels que l’épuisement des ressources des services système Service Fabric. 
  
- [**Coût de déplacement de service très élevé**](./service-fabric-cluster-resource-manager-movement-cost.md) pour un type de réplica. Les réplicas avec un coût de déplacement « très élevé » sont déplacés uniquement si une violation de contrainte existant dans le cluster ne peut pas être résolue d’une autre façon. Reportez-vous au document lié pour plus d’informations sur l’utilisation raisonnable du coût de déplacement « très élevé » et sur les considérations supplémentaires.
  
-  **Vérifications supplémentaires de la sécurité des clusters** : Dans cette version, nous avons introduit une vérification de la sécurité du quorum de nœuds initiaux configurables. Cela vous permet de personnaliser le nombre de nœuds initiaux qui doivent être disponibles pendant les scénarios de gestion et de cycle de vie du cluster. Les opérations qui prennent le cluster en dessous de la valeur configurée sont bloquées. Aujourd’hui la valeur par défaut est toujours un quorum de nœuds initiaux. Par exemple, si vous avez 7 nœuds initiaux, une opération qui nécessiterait en dessous de 5 nœuds initiaux serait bloquée par défaut. Avec cette modification, vous pourriez définir la valeur de sécurité minimale sur 6, ce qui permettrait d’autoriser seulement un nœud initial d’être en panne à la fois.
   
- Support ajouté pour [**gérer le service de sauvegarde et de restauration dans Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). Cela rend les activités suivantes possibles directement depuis SFX : découverte du service de sauvegarde et de restauration, création d’une politique de sauvegarde, activation des sauvegardes automatiques, prise de sauvegardes ad hoc, déclenchement d’opération de restauration et recherche de sauvegarde existantes.

- Annonce de la disponibilité de [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool) : Cet outil permet de valider que les types utilisés dans les collections fiables ont une compatibilité ascendante et descendante pendant le déploiement d’une mise à niveau d’application. Cela permet d’éviter les échecs de mise à niveau ou la perte et l’altération de données en raison de types manquants ou incompatibles.

- [**Activer les lectures stables sur les réplicas secondaires**](./service-fabric-reliable-services-configuration.md#configuration-names-1) : le fait d’activer les lectures stables aura pour effet de restreindre les réplicas secondaires aux valeurs retournées demandées par le quorum.

De plus, cette version contient d’autres nouvelles fonctionnalités, corrections de bogues, et améliorations en termes de prise en charge, de fiabilité et de performances. Pour la liste complète des modifications, reportez-vous aux [notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

#### <a name="service-fabric-70-releases"></a>Mises en production Service Fabric 7.0

| Date de publication | Libérer | En savoir plus |
|---|---|---|
| 18 novembre 2019 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 janvier 2020 | [Mise en production actualisée d’Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 février 2020 | [Mise en production actualisée d’Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 mars 2020 | [Mise en production actualisée d’Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 6 mai 2020 | [Azure Service Fabric 7.0 Sixth Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 9 octobre 2020 | Azure Service Fabric 7.0 Ninth Refresh Release | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

Cette version inclut la prise en charge, la fiabilité, les améliorations de performances, les nouvelles fonctionnalités, les correctifs de bogues et les améliorations destinées à faciliter la gestion de cycle de vie du cluster et des applications.

> [!IMPORTANT]
> Service Fabric 6.5 est la version finale avec prise en charge des outils Service Fabric dans Visual Studio 2015. Les clients sont invités à passer à [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) à l’avenir.

Nouveautés de Service Fabric 6.5 :

- Service Fabric Explorer inclut [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) pour examiner les applications téléchargées dans le magasin d’images.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) version [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) comprend de nombreuses améliorations d’autodiagnostic. Il est recommandé aux clients de POA de passer à cette version.

- [EventStore Service est activé par défaut](service-fabric-visualizing-your-cluster.md#event-store) pour les clusters Service Fabric 6.5, sauf si vous l’avez refusé.

- Ajout d’[événements de cycle de vie des réplicas](service-fabric-diagnostics-event-generation-operational.md#replica-events) pour les services avec état.

- [Meilleure visibilité de l’état du nœud initial](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), y compris des avertissements au niveau du cluster si un nœud initial est incorrect (*Inactif*, *Supprimé* ou *Inconnu*).

- L’[outil de récupération d’urgence de l’application Service Fabric](https://github.com/Microsoft/Service-Fabric-AppDRTool) permet une récupération rapide en cas d’incident sur le cluster principal. Les données du cluster principal sont synchronisées en continu sur l’application secondaire de secours à l’aide de la restauration et de sauvegarde périodiques.

- Prise en charge de Visual Studio pour la [publication d’applications .NET Core sur les clusters Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](./service-fabric-cli.md) sera automatiquement installé pour Service Fabric 6.5 (et versions ultérieures) lors de la mise à niveau ou de la création d’un nouveau cluster Linux sur Azure.

- [SFCTL](./service-fabric-cli.md) est installé par défaut sur les clusters OneBox Linux/Mac OS.

Pour plus d’informations, consultez les [Notes de publication relatives à Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

#### <a name="service-fabric-65-releases"></a>Versions Release de Service Fabric 6.5

| Date de publication | Libérer | En savoir plus |
|---|---|---|
| 11 juin 2019 | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juillet 2019 | [Version Refresh Release d’Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juillet 2019 | [Version Refresh Release d’Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 août 2019 | [Version Refresh Release d’Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 octobre 2019 | [Version Refresh Release d’Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Notes de publication](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |

### <a name="service-fabric-64-releases"></a>Versions de Service Fabric 6.4

| Date de publication | Libérer |
|---|---|
| 30 novembre 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/) |
| 12 décembre 2018 | [Azure Service Fabric 6.4 Refresh Release for Windows clusters](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 4 février 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 4 mars 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 8 avril 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2 mai 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 28 mai 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
