---
title: Orchestration flexible pour des groupes de machines virtuelles identiques dans Azure
description: Découvrez comment utiliser un mode d’orchestration flexible pour des groupes de machines virtuelles identiques dans Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/13/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 3403cebab79b49f3a22353bec0a03b68b5a24f0f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432830"
---
# <a name="flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>Orchestration flexible pour des groupes de machines virtuelles identiques dans Azure

**S’applique à :** :heavy_check_mark: groupes identiques flexibles

Les groupes de machines virtuelles identiques avec orchestration Flexible vous permettent de combiner la scalabilité des [groupes de machines virtuelles identiques utilisant le mode d’orchestration Uniform](../virtual-machine-scale-sets/overview.md) avec les garanties de disponibilité régionale des [groupes à haute disponibilité](availability-set-overview.md).

Les groupes identiques de machines virtuelles Azure vous permettent de créer et de gérer un groupe de machines virtuelles et disposant d’une charge équilibrée. Le nombre d’instances de machine virtuelle peut augmenter ou diminuer automatiquement en fonction d’une demande ou d’un calendrier défini. Les groupes identiques vous offrent les avantages suivants :
- Création et gestion faciles de plusieurs machines virtuelles
- Offre une haute disponibilité et une résilience d’application en répartissant les machines virtuelles entre les zones de disponibilité et les domaines d’erreur
- Permet une mise à l’échelle automatique de votre application en fonction des variations du besoin en ressources
- Fonctionne à grande échelle

Avec l’orchestration Flexible, Azure offre une expérience unifiée sur tout l’écosystème de machines virtuelles Azure. L’orchestration Flexible garantit une haute disponibilité (jusqu’à 1 000 machines virtuelles) en répartissant les machines virtuelles entre différents domaines d’erreur dans une région ou dans une zone de disponibilité. Vous pouvez ainsi effectuer un scale-out de votre application tout en conservant l’isolation du domaine d’erreur, essentielle pour exécuter des charges de travail avec état ou basées sur un quorum, y compris :
- Charges de travail basées sur un quorum
- Bases de données open source
- Applications avec état
- Services nécessitant une haute disponibilité et une grande échelle
- Services cherchant à combiner différents types de machines virtuelles ou à tirer parti à la fois de machines virtuelles spot et à la demande
- Applications d’un groupe à haute disponibilité existant

> [!IMPORTANT]
> Cet article concerne les groupes de machines virtuelles identiques en mode d’orchestration Flexible, que nous vous recommandons d’utiliser pour tous les nouveaux déploiements de groupes identiques. Pour consulter des informations sur les groupes identiques Uniform, accédez à la documentation relative aux [groupes de machines virtuelles identiques en mode d’orchestration Uniform](../virtual-machine-scale-sets/overview.md).

Apprenez-en davantage sur les différences entre les groupes identiques uniformes et les groupes identiques flexibles dans [Modes d’orchestration](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md).

> [!CAUTION]
> Le mode d’orchestration est défini lorsque vous créez le groupe identique et ne peut pas être modifié ou mis à jour ultérieurement.


## <a name="why-use-virtual-machine-scale-sets"></a>Pourquoi utiliser les groupes identiques de machines virtuelles ?
Pour offrir une redondance et de meilleures performances, les applications sont généralement réparties sur plusieurs instances. Les clients peuvent accéder à votre application via un équilibreur de charge qui distribue les requêtes à l’une des instances de l’application. Si vous avez besoin effectuer une maintenance ou de mettre à jour une instance d’application, vos clients doivent être répartis sur une autre instance disponible de l’application. Pour suivre la demande de clients supplémentaires, vous devrez peut-être augmenter le nombre d’instances d’application qui exécutent votre application.

Les groupes identiques de machines virtuelles Azure fournissent les fonctionnalités de gestion pour des applications exécutées sur nombreuses machines virtuelles, la mise à l’échelle automatique des ressourceset un équilibrage de charge du trafic. Les groupes identiques vous offrent les avantages suivants :

- **Création et gestion faciles de plusieurs machines virtuelles**
    - Lorsque vous disposez de nombreuses machines virtuelles qui exécutent votre application, il est important de conserver une configuration cohérente dans votre environnement. Pour des performances fiables de votre application, la taille de la machine virtuelle, la configuration du disque et les installations de l’application doivent correspondre entre toutes les machines virtuelles.
    - Avec les groupes identiques, toutes les instances de machine virtuelle sont créées à partir de la même image de système d’exploitation de base et de la même configuration. Cette approche vous permet de gérer facilement des centaines de machines virtuelles sans tâches de configuration supplémentaires ou gestion de réseau.
    - Les groupes identiques prennent en charge l’utilisation de l’[équilibreur de charge Azure](../load-balancer/load-balancer-overview.md) pour la distribution du trafic de type Couche 4 de base, et [Azure Application Gateway](../application-gateway/overview.md) pour une distribution du trafic de type Couche 7 plus avancée et un arrêt TLS.

- **Offre une haute disponibilité et une résilience des applications**
    - Les groupes identiques sont utilisés pour exécuter plusieurs instances de votre application. Si l’une de ces instances de machine virtuelle a un problème, les clients continuent d’accéder à votre application via l’une des autres instances de machine virtuelle avec une interruption minimale.
    - Pour plus de disponibilité, vous pouvez utiliser des [Zones de disponibilité](../availability-zones/az-overview.md) pour distribuer automatiquement des instances de machine virtuelle dans un groupe identique au sein d’un centre de données unique ou de plusieurs centres de données.

- **Permet une mise à l’échelle automatique de votre application en fonction des variations du besoin en ressources**
    - La demande du client pour votre application peut changer pendant la journée ou la semaine. Pour suivre la demande du client, les groupes identiques peuvent augmenter automatiquement le nombre d’instances de machine virtuelle lorsque la demande de l’application augmente, et le réduire lorsque la demande diminue.
    - La mise à l’échelle automatique réduit également le nombre d’instances de machine virtuelle inutiles exécutant votre application lorsque la demande est faible, tandis que les clients continuent de recevoir un niveau de performance acceptable lorsque la demande se développe et des instances de machines virtuelles supplémentaires sont ajoutées automatiquement. Cette capacité permet de réduire les coûts et de créer efficacement des ressources Azure en fonction des besoins.

- **Fonctionne à grande échelle**
    - Les groupes identiques prennent en charge jusqu’à 1 000 instances de machines virtuelles pour les images du marketplace standard et les images personnalisées via Azure Compute Gallery (anciennement Shared Image Gallery). Si vous créez un groupe identique à l’aide d’une image managée, la limite est de 600 instances de machines virtuelles.
    - Pour des performances optimales avec des charges de travail de production, utilisez [Azure Disques managés](../virtual-machines/managed-disks-overview.md).



## <a name="get-started-with-flexible-orchestration-mode"></a>Démarrer avec le mode d’orchestration Flexible

Commencez à utiliser le mode d’orchestration flexible pour vos groupes identiques via le [portail Azure](flexible-virtual-machine-scale-sets-portal.md), [Azure CLI](flexible-virtual-machine-scale-sets-cli.md), [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md) ou un [modèle ARM](flexible-virtual-machine-scale-sets-rest-api.md). 

> [!IMPORTANT]
> Vérifiez que vous disposez d’une connectivité réseau sortante explicite. Consultez [Réseaux virtuels et machines virtuelles dans Azure](../virtual-network/network-overview.md) pour en savoir plus à ce sujet, et veillez à suivre les [bonnes pratiques](../virtual-network/concepts-and-best-practices.md) Azure concernant les réseaux.


## <a name="add-instances-with-autoscaling-or-manually"></a>Ajouter des instances avec mise à l’échelle automatique ou manuellement
Les groupes de machines virtuelles identiques avec une orchestration flexible fonctionnent comme une fine couche d’orchestration pour gérer plusieurs machines virtuelles. Il existe plusieurs façons d’ajouter des machines virtuelles à gérer par le groupe identique :

- **Définir le nombre d’instances**

    Lorsque vous créez le groupe identique avec une orchestration flexible, définissez un profil de machine virtuelle ou un modèle à utiliser pour effectuer un scale-out. Vous pouvez ensuite définir le paramètre de capacité afin d’augmenter ou de réduire le nombre d’instances de machine virtuelle gérées par le groupe identique. 

- **Mise à l’échelle automatique à l’aide de métriques ou d’une planification** 

    Vous pouvez également configurer des règles de mise à l’échelle automatique afin d’augmenter ou de réduire la capacité en fonction de métriques ou d’une planification. Consultez [Groupes de machines virtuelles identiques avec mise à l’échelle automatique](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md). 

- **Spécifier un groupe identique lors de la création d’une machine virtuelle**

    Lorsque vous créez une machine virtuelle, vous pouvez spécifier qu’elle est ajoutée à un groupe de machines virtuelles identiques. Une machine virtuelle ne peut être ajoutée à un groupe identique qu’au moment de la création de la machine virtuelle. La machine virtuelle nouvellement créée doit se trouver dans le même groupe de ressources que le groupe identique Flexible, quelle que soit la méthode de déploiement.

Le mode d’orchestration flexible peut être utilisé avec les références SKU de machine virtuelle qui prennent en charge les [mises à jour de préservation de la mémoire ou la migration dynamique](../virtual-machines/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), qui comprend 90 % de toutes les machines virtuelles IaaS déployées dans Azure. Globalement, cela comprend des familles de tailles à usage général, comme les machines virtuelles des séries B, D, E et F. Actuellement, le mode flexible ne peut pas orchestrer des références SKU ou familles de machines virtuelles qui ne prennent pas en charge les mises à jour de préservation de la mémoire, notamment des machines virtuelles des séries G, H, L, M et N. Vous pouvez utiliser l’[API des références SKU de ressources de calcul](/rest/api/compute/resource-skus/list) pour déterminer si une référence SKU de machine virtuelle spécifique est prise en charge.

```azurecli-interactive
az vm list-skus -l eastus --size standard_d2s_v3 --query "[].capabilities[].[name, value]" -o table
```

> [!IMPORTANT]
> Le comportement de la mise en réseau varie selon la façon dont vous choisissez de créer des machines virtuelles au sein de votre groupe identique. Pour plus d’informations, consultez la [connectivité réseau évolutive](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity).


## <a name="features"></a>Fonctionnalités
Le tableau suivant liste les fonctionnalités du mode d’orchestration Flexible et fournit des liens vers la documentation associée.

### <a name="basic-setup"></a>Configuration de base

| Fonctionnalité | Prise en charge par l’orchestration Flexible pour les groupes identiques |
|---|---|
| Type de machine virtuelle  | Machine virtuelle Azure IaaS standard (Microsoft.compute/virtualmachines)  |
| Nombre maximal d’instances (avec garanties de domaine d’erreur)  | 1 000  |
| Références SKU prises en charge  | Série D, série E, série F, série A, série B, Intel, AMD ; les références SKU de spécialité (G, H, L, M, N) ne sont pas prises en charge |
| Contrôle total sur la machine virtuelle, les cartes réseau et les disques  | Oui  |
| Autorisations RBAC nécessaires  | Écriture sur des VMSS de calcul, écriture sur des machines virtuelles de calcul, réseau |
| Mise en réseau accélérée  | Oui  |
| Instances spot et tarifs   | Oui, vous pouvez avoir des instances spot et des instances à priorité normale  |
| Différents systèmes d’exploitation  | Oui, Linux et Windows peuvent résider dans le même groupe identique Flexible  |
| Types de disques  | Disques managés uniquement, tous les types de stockage  |
| Accélérateur d’écriture   | Non  |
| Groupes de placement de proximité   | Oui, lisez la [Documentation sur les groupes de placement de proximité](../virtual-machine-scale-sets/proximity-placement-groups.md) |
| Hôtes dédiés Azure   | Non  |
| Identité managée  | Identité affectée par l’utilisateur uniquement  |
| Ajout d’une machine virtuelle existante à un groupe/suppression du groupe  | Non  |
| Service Fabric  | Non  |
| Azure Kubernetes Service (AKS) / AKE  | Non  |
| UserData  | Partielle. UserData peut être spécifié pour chaque machine virtuelle |


### <a name="autoscaling-and-instance-orchestration"></a>Mise à l’échelle automatique et orchestration des instances

| Fonctionnalité | Prise en charge par l’orchestration Flexible pour les groupes identiques |
|---|---|
| Liste des machines virtuelles d’un groupe | Oui |
| Mise à l’échelle automatique (manuelle, basée sur des métriques, basée sur une planification) | Oui |
| Supprimer automatiquement les cartes réseau et les disques lors de la suppression d’instances de machine virtuelle | Oui |
| Stratégie de mise à niveau (groupes de machines virtuelles identiques) | Non, la stratégie de mise à niveau doit être null ou [] lors de la création |
| Mises à jour automatiques de système d’exploitation (groupes de machines virtuelles identiques) | Non |
| Mise à jour corrective de sécurité intégrée (« in-guest ») | Oui |
| Notifications d’arrêt (groupes de machines virtuelles identiques) | Oui, lisez la [Documentation sur les notifications d’arrêt](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md) |
| Supervision de l’intégrité de l’application | Extension Intégrité de l’application |
| Réparation d’instance (groupes de machines virtuelles identiques) | Oui, lisez la [documentation sur la réparation d’instance](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md) |
| Protection des instances | Non, utilisez des [verrous de ressource Azure](../azure-resource-manager/management/lock-resources.md) |
| Stratégie de scale-in | Non |
| VMSS - Obtenir une vue d’instance | Non |
| Opérations de traitement par lots de machines virtuelles (Tout démarrer, Tout arrêter, supprimer un sous-ensemble, etc.) | Non (peut déclencher des opérations sur chaque instance à l’aide de l’API de machine virtuelle) |

### <a name="high-availability"></a>Haute disponibilité 

| Fonctionnalité | Prise en charge par l’orchestration Flexible pour les groupes identiques |
|---|---|
| Contrat SLA de disponibilité | 99,95 % pour les instances réparties sur plusieurs domaines d’erreur ; 99,99 % pour les instances réparties sur plusieurs zones |
| Zones de disponibilité | Spécifiez des instances appartenant à 1, 2 ou 3 zones de disponibilité |
| Affecter une machine virtuelle à une zone de disponibilité spécifique | Oui |
| Domaine d’erreur – Diffusion maximale (Azure va diffuser les instances au maximum) | Oui |
| Domaine d’erreur – Diffusion fixe | 2 ou 3 domaines d’erreur (en fonction du nombre maximal de domaines d’erreur par région), 1 pour les déploiements zonaux |
| Attribuer une machine virtuelle à un domaine d’erreur spécifique | Oui |
| Mettre à jour les domaines | Déprécié (maintenance de plateforme effectuée domaine d’erreur par domaine d’erreur) |
| Effectuer la maintenance | Déclencher la maintenance sur chaque instance à l’aide de l’API de machine virtuelle |

### <a name="networking"></a>Mise en réseau 

| Fonctionnalité | Prise en charge par l’orchestration Flexible pour les groupes identiques |
|---|---|
| Connectivité sortante par défaut | Non, doit avoir une [connectivité sortante explicite](../virtual-network/ip-services/default-outbound-access.md) |
| Azure Load Balancer - SKU Standard | Oui |
| Application Gateway | Oui |
| Réseau InfiniBand | Non |
| Équilibreur de charge logiciel De base | Non |
| Transfert de port réseau | Oui (règles NAT pour chaque instance) |

### <a name="backup-and-recovery"></a>Sauvegarde et récupération 

| Fonctionnalité | Prise en charge par l’orchestration Flexible pour les groupes identiques |
|---|---|
| Sauvegarde Azure  | Oui |
| Azure Site Recovery | Oui (via PowerShell) |
| Alertes Azure  | Oui |
| Insights de machine virtuelle  | Peut être installé sur chaque machine virtuelle |

### <a name="unsupported-parameters"></a>Paramètres non pris en charge

Les paramètres de groupe de machines virtuelles identiques suivants ne sont pas pris en charge en mode d’orchestration Flexible :
- Groupe de placement unique : vous devez choisir `singlePlacementGroup=False`
- Déploiement à l’aide de références (SKU) de spécialité : familles de machines virtuelles des séries G, H, L, M, N
- Configuration de disque Ultra : `diskIOPSReadWrite`, `diskMBpsReadWrite`
- Surapprovisionnement de VMSS
- Mises à niveau automatiques du système d’exploitation basées sur une image
- Intégrité de l’application via une sonde d’intégrité SLB : utiliser l’extension d’intégrité d’application sur les instances
- Stratégie de mise à niveau de groupe de machines virtuelles identiques : doit être null ou vide
- Déploiement sur Azure Dedicated Host
- Disques non managés
- Stratégie de scale-in de groupe de machines virtuelles identiques
- Protection d’instance de groupe de machines virtuelles identiques
- Basic Load Balancer
- Réacheminement de port via un pool NAT Standard Load Balancer : vous pouvez configurer des règles NAT sur des instances spécifiques


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Résoudre les problèmes de groupes identiques avec orchestration Flexible
Trouvez la solution adaptée à votre scénario de dépannage.

<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-3-must-fall-in-the-range-1-to-2"></a>InvalidParameter. Le nombre de domaines d’erreur spécifié (3) doit s’inscrire dans la plage allant de 1 à 2.

```
InvalidParameter. The specified fault domain count 3 must fall in the range 1 to 2.
```

**Cause :** Le paramètre `platformFaultDomainCount` n’est pas valide pour la région ou la zone sélectionnée.

**Solution :** Sélectionnez une valeur `platformFaultDomainCount` valide. Pour les déploiements zonaux, la valeur `platformFaultDomainCount` maximale est 1. Pour les déploiements régionaux où aucune zone n’est spécifiée, la valeur `platformFaultDomainCount` maximale varie selon la région. Consultez les scripts de l’article [Gestion de la disponibilité des machines virtuelles](../virtual-machines/availability.md) pour déterminer le nombre maximal de domaines d’erreur par région.


<!-- error -->
### <a name="operationnotallowed-deletion-of-virtual-machine-scale-set-is-not-allowed-as-it-contains-one-or-more-vms-please-delete-or-detach-the-vms-before-deleting-the-virtual-machine-scale-set"></a>OperationNotAllowed. La suppression du groupe de machines virtuelles identiques n’est pas autorisée, car il contient une ou plusieurs machines virtuelles. Supprimez ou détachez la ou les machines virtuelles avant de supprimer le groupe de machines virtuelles identiques.

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Cause :** Tentative de suppression d’un groupe identique en mode d’orchestration Flexible associé à une ou plusieurs machines virtuelles.

**Solution :** Supprimez toutes les machines virtuelles associées au groupe identique en mode d’orchestration Flexible. Vous pourrez alors supprimer le groupe identique.


<!-- error -->
### <a name="invalidparameter-the-value-true-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-false"></a>InvalidParameter. La valeur « True » n’est pas autorisée pour le paramètre « singlePlacementGroup ». Seule la valeur False est autorisée.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Cause** : par défaut, le paramètre `singlePlacementGroup` est défini sur *True*.

**Solution :** Le paramètre `singlePlacementGroup` doit être défini sur *False*.


<!-- error -->
### <a name="outboundconnectivitynotenabledonvm-no-outbound-connectivity-configured-for-virtual-machine"></a>OutboundConnectivityNotEnabledOnVM. Aucune connectivité sortante n’est configurée pour la machine virtuelle.

```
OutboundConnectivityNotEnabledOnVM. No outbound connectivity configured for virtual machine.
```
**Cause** : tentative de création d’un groupe de machines virtuelles identiques en mode d’orchestration Flexible sans connectivité Internet sortante.

**Solution** : activez l’accès sortant sécurisé pour votre groupe de machines virtuelles identiques de la manière la mieux adaptée à votre application. L’accès sortant peut être activé avec NAT Gateway sur votre sous-réseau, en ajoutant des instances à un pool de back-ends Load Balancer ou en ajoutant une adresse IP publique explicite par instance. Pour les applications hautement sécurisées, vous pouvez spécifier des routes personnalisées définies par l’utilisateur via votre pare-feu ou vos applications réseau virtuelles. Pour plus d’informations, consultez [Accès sortant par défaut](../virtual-network/ip-services/default-outbound-access.md).

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Mode d’orchestration flexible pour vos groupes identiques avec le portail Azure.](flexible-virtual-machine-scale-sets-portal.md)