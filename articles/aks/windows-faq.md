---
title: FAQ sur les pools de nœuds Windows Server
titleSuffix: Azure Kubernetes Service
description: Consultez la foire aux questions lorsque vous exécutez des pools de nœuds Windows Server et des charges de travail d’application dans Azure Kubernetes service (AKS).
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 9839b9075c3747c6c803e95c5622416da85d34d4
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138488"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>Foire aux questions relative aux pools de nœuds Windows Server dans AKS

Dans Azure Kubernetes Service (AKS), vous pouvez créer un pool de nœuds qui exécute Windows Server en tant que système d’exploitation invité sur les nœuds. Ces nœuds peuvent exécuter des applications de conteneur Windows natives, comme celles reposant sur .NET Framework. Il existe des différences dans la manière dont les systèmes d’exploitation Linux et Windows prennent en charge les conteneurs. Certaines fonctionnalités courantes relatives à Linux Kubernetes et aux pods ne sont pas actuellement disponibles pour les pools de nœuds Windows.

Cet article présente certaines des questions fréquemment posées et quelques concepts de système d’exploitation pour les nœuds Windows Server dans AKS.

## <a name="which-windows-operating-systems-are-supported"></a>Quels sont les systèmes d’exploitation Windows pris en charge ?

AKS utilise Windows Server 2019 comme version de système d’exploitation hôte et prend en charge uniquement l’isolation des processus. Les images conteneurs générées à l’aide d’autres versions de Windows Server ne sont pas prises en charge. Pour plus d’informations, consultez [Compatibilité des versions de conteneurs Windows][windows-container-compat].

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Kubernetes est-il différent sur Windows et Linux ?

La prise en charge des pools de nœuds Windows Server comprend certaines limitations qui font partie du projet Windows Server dans Kubernetes en amont. Ces limitations ne sont pas spécifiques à AKS. Pour plus d’informations sur la prise en charge en amont de Windows Server dans Kubernetes, consultez la section [Fonctionnalités prises en charge et limitations][upstream-limitations] du document [Introduction à la prise en charge de Windows dans Kubernetes][intro-windows], dans le projet Kubernetes.

Historiquement, Kubernetes est axé sur Linux. De nombreux exemples utilisés sur le site web [Kubernetes.io][kubernetes] en amont sont destinés à être utilisés sur des nœuds Linux. Lorsque vous créez des déploiements qui utilisent des conteneurs Windows Server, les considérations suivantes au niveau du système d’exploitation s’appliquent :

- **Identité** : Linux identifie un utilisateur par un identificateur d’utilisateur entier (UID). Un utilisateur dispose également d’un nom d’utilisateur alphanumérique pour se connecter, que Linux convertit en UID de l’utilisateur. De même, Linux identifie un groupe d’utilisateurs par un identificateur de groupe entier (GID) et convertit un nom de groupe en son GID correspondant.
    Windows Server utilise un identificateur de sécurité binaire (SID) plus long qui est stocké dans la base de données Windows Security Access Manager (SAM). Cette base de données n’est pas partagée entre l’hôte et les conteneurs, ou entre conteneurs.
- **Autorisations de fichiers** : Windows Server utilise une liste de contrôle d’accès basée sur les SID, plutôt qu’un masque de bits d’autorisations et d’UID et GID.
- **Chemins de fichiers** : La convention sur Windows Server consiste à utiliser \ au lieu de /. 
    Dans les spécifications de pod qui montent des volumes, spécifiez le chemin d’accès correctement pour les conteneurs Windows Server. Par exemple, au lieu d’un point de montage de */mnt/volume* dans un conteneur Linux, spécifiez une lettre de lecteur et un emplacement comme */K/Volume* à monter en tant que lecteur *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Quels sont les types de disques pris en charge pour Windows ?

Azure Disks et Azure Files sont les types de volume pris en charge. Ils sont accessibles en tant que volumes NTFS dans le conteneur Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Puis-je exécuter des clusters exclusivement Windows dans AKS ?

Les nœuds maîtres (le plan de contrôle) d’un cluster AKS sont hébergés par le service AKS. Vous ne serez pas exposé au système d’exploitation des nœuds hébergeant les composants maîtres. Tous les clusters AKS sont créés avec un premier pool de nœuds par défaut qui est basé sur Linux. Ce pool de nœuds contient les services système nécessaires au fonctionnement du cluster. Nous vous recommandons d’exécuter au moins deux nœuds dans le premier pool de nœuds afin de garantir la fiabilité de votre cluster et la possibilité d’effectuer des opérations de cluster. Il est impossible de supprimer le premier pool de nœuds Linux, sauf en cas de suppression du cluster AKS.

## <a name="how-do-i-patch-my-windows-nodes"></a>Comment corriger mes nœuds Windows ?

Afin d’obtenir les derniers correctifs pour les nœuds Windows, vous pouvez soit [mettre à niveau le pool de nœuds][nodepool-upgrade], soit [mettre à niveau l’image de nœud][upgrade-node-image]. Les mises à jour Windows ne sont pas activées sur les nœuds dans AKS. AKS met en production de nouvelles images de pool de nœuds dès que des correctifs sont disponibles. Il incombe à l’utilisateur de mettre à niveau les pools de nœuds pour rester à jour en matière de correctifs. Cela est également vrai pour la version de Kubernetes utilisée. Les [notes de publication d’AKS][aks-release-notes] indiquent quand de nouvelles versions sont disponibles. Pour plus d’informations sur la mise à niveau de l’ensemble du pool de nœuds Windows Server, consultez [Mettre à niveau un pool de nœuds][nodepool-upgrade]. Si vous souhaitez uniquement mettre à jour l’image de nœud, consultez [Mises à niveau d’une image de nœud AKS][upgrade-node-image].

> [!NOTE]
> L’image Windows Server mise à jour n’est utilisée que si une mise à niveau de cluster (mise à niveau de plan de contrôle) a été effectuée avant la mise à niveau du pool de nœuds.
>

## <a name="what-network-plug-ins-are-supported"></a>Quels sont les plug-ins réseau pris en charge ?

Les clusters AKS comportant des pools de nœuds Windows doivent utiliser le modèle de mise en réseau Azure Container Networking Interface (Azure CNI) [avancé]. La mise en réseau Kubenet (de base) n’est pas prise en charge. Pour plus d’informations sur les différences dans les modèles de réseau, consultez [Concepts réseau pour les applications dans AKS][azure-network-models]. Le modèle de réseau Azure CNI nécessite une planification et une prise en compte supplémentaires pour la gestion des adresses IP. Pour plus d’informations sur la façon de planifier et implémenter Azure CNI, consultez [Configurer le réseau Azure CNI dans AKS][configure-azure-cni].

Les nœuds Windows sur les clusters AKS ont également l’option [Retour direct du serveur (DSR)][dsr] activée par défaut lorsque Calico est activé.

## <a name="is-preserving-the-client-source-ip-supported"></a>La conservation de l’adresse IP source du client est-elle prise en charge ?

À l’heure actuelle, la [préservation de l’adresse IP source du client][client-source-ip] n’est pas prise en charge avec les nœuds Windows.

## <a name="can-i-change-the-maximum-number-of-pods-per-node"></a>Puis-je modifier le nombre maximal de pods par nœud ?

Oui. Pour connaître les implications d’une telle modification et les options disponibles, consultez [Nombre maximal de pods][maximum-number-of-pods].

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Pourquoi un message d’erreur s’affiche-t-il lorsque j’essaie de créer un pool d’agents Windows ?

Si vous avez créé votre cluster avant février 2020 et n’avez jamais effectué d’opérations de mise à niveau du cluster, le cluster utilise toujours une ancienne image Windows. Vous avez peut-être rencontré une erreur ressemblant à ceci :

« La liste suivante d’images référencées à partir du modèle de déploiement est introuvable : Éditeur : MicrosoftWindowsServer, Offre : WindowsServer, référence (SKU) : 2019-datacenter-core-smalldisk-2004, Version : dernière. Consultez [Rechercher et utiliser des images de machine virtuelle de Place de marché Azure avec Azure PowerShell](../virtual-machines/windows/cli-ps-findimage.md) pour obtenir des instructions sur la recherche d’images disponibles. »

Pour résoudre ce problème :

1. Mettez à niveau le [plan de contrôle du cluster][upgrade-cluster-cp] pour mettre à jour l’offre et l’éditeur de l’image.
1. Créez des pools d’agents Windows.
1. Déplacez les pod Windows des pools d’agents Windows existants vers les nouveaux pools d’agents Windows.
1. Supprimez les anciens pools d’agents Windows.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Comment faire pivoter le principal du service pour mon pool de nœuds Windows ?

Les pools de nœuds Windows ne prennent pas en charge la rotation du principal de service. Pour mettre à jour le principal du service, créez un pool de nœuds Windows, puis migrez vos pods de l’ancien pool vers le nouveau. Une fois que vos pods ont migré vers le nouveau pool, supprimez l’ancien pool de nœuds.

Au lieu des principaux de service, utilisez des identités managées, qui sont essentiellement des wrappers autour des principaux de service. Pour plus d’informations, voir [Utiliser les identités managées dans Azure Kubernetes Service][managed-identity].

## <a name="how-do-i-change-the-administrator-password-for-windows-server-nodes-on-my-cluster"></a>Comment modifier le mot de passe d’administrateur pour les nœuds Windows Server sur mon cluster ?

Lorsque vous créez votre cluster AKS, vous spécifiez les paramètres `--windows-admin-password` et `--windows-admin-username` afin de définir les informations d’identification d’administrateur pour tous les nœuds Windows Server du cluster. Si vous n’avez pas spécifié d’informations d’identification d’administrateur lors de la création d’un cluster à l’aide du portail Azure ou lors de la définition de `--vm-set-type VirtualMachineScaleSets` et `--network-plugin azure` à l’aide d’Azure CLI, le nom d’utilisateur est défini par défaut sur *azureuser* et un mot de passe aléatoire.

Pour modifier le mot de passe d’administrateur, utilisez la commande `az aks update` suivante :

```azurecli
az aks update \
    --resource-group $RESOURCE_GROUP \
    --name $CLUSTER_NAME \
    --windows-admin-password $NEW_PW
```

> [!IMPORTANT]
> Effectuer l’opération `az aks update` met à niveau uniquement les pools de nœuds Windows Server. Les pools de nœuds Linux ne sont pas affectés.
> 
> Lorsque vous modifiez `--windows-admin-password`, le nouveau mot de passe doit comporter au moins 14 caractères et répondre aux [exigences de mot de passe de Windows Server][windows-server-password].

## <a name="how-many-node-pools-can-i-create"></a>Combien de pools de nœuds puis-je créer ?

Le cluster AKS peut avoir un maximum de 100 pools de nœuds. Vous pouvez avoir au maximum 1 000 nœuds dans ces pools de nœuds. Pour plus d’informations, consultez [Limitations de pool de nœuds][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Comment puis-je nommer mes pools de nœuds Windows ?

Les noms ne doivent pas comporter plus de six caractères. Il s’agit de la limite actuelle d’AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Toutes les fonctionnalités sont-elles prises en charge avec les nœuds Windows ?

Kubenet n’est actuellement pas pris en charge sur les nœuds Windows.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Puis-je exécuter des contrôleurs d’entrée sur des nœuds Windows ?

Oui, un contrôleur d’entrée prenant en charge les conteneurs Windows Server peut s’exécuter sur des nœuds Windows dans AKS.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Mes conteneurs Windows Server peuvent-ils utiliser gMSA ?

Les comptes de service gérés de groupe (gMSA) ne sont actuellement pas pris en charge dans AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Puis-je utiliser Azure Monitor pour des conteneurs avec des nœuds et conteneurs Windows ?

Oui, vous pouvez. Toutefois, Azure Monitor est en préversion publique pour la collecte des journaux (stdout, stderr) et des métriques des conteneurs Windows. Vous pouvez aussi établir un attachement au flux en direct des journaux stdout à partir d’un conteneur Windows.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Existe-t-il des limites au nombre de services sur un cluster avec des nœuds Windows ?

Un cluster avec des nœuds Windows peut avoir environ 500 services avant d’être confronté à l’épuisement des ports.

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Puis-je utiliser Azure Hybrid Benefit avec des nœuds Windows ?

Oui. Azure Hybrid Benefit pour Windows Server réduit les coûts d’exploitation en vous permettant de placer votre licence Windows Server locale sur des nœuds Windows AKS.

Azure Hybrid Benefit peut être utilisé sur l’ensemble de votre cluster AKS ou sur des nœuds individuels. Pour les nœuds individuels, vous devez accéder au [groupe de ressources des nœuds][resource-groups] et appliquer Azure Hybrid Benefit aux nœuds directement. Pour plus d’informations relatives à l’application d’Azure Hybrid Benefit sur des nœuds individuels, consultez [Azure Hybrid Benefit pour Windows Server][hybrid-vms]. 

Pour utiliser Azure Hybrid Benefit sur un nouveau cluster AKS, utilisez l’argument `--enable-ahub`.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

Pour utiliser Azure Hybrid Benefit sur un cluster AKS existant, mettez à jour le cluster à l’aide de l’argument `--enable-ahub`.

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

Pour vérifier si Azure Hybrid Benefit est défini sur le cluster, utilisez la commande suivante :

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

Si Azure Hybrid Benefit est activé sur le cluster, la sortie de `az vmss show` sera similaire à ce qui suit :

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Puis-je utiliser le tableau de bord web Kubernetes avec des conteneurs Windows ?

Oui, vous pouvez utiliser le [tableau de bord web de Kubernetes][kubernetes-dashboard] pour accéder aux informations sur les conteneurs Windows. Cependant, pour l’instant, vous ne pouvez pas exécuter *kubectl exec* dans un conteneur Windows en cours d’exécution directement à partir du tableau de bord web de Kubernetes. Pour plus d’informations sur la connexion à un conteneur Windows en cours d’exécution, consultez [Se connecter avec RDP à des nœuds Windows Server de cluster AKS (Azure Kubernetes Service) à des fins de maintenance ou de résolution des problèmes][windows-rdp].

## <a name="how-do-i-change-the-time-zone-of-a-running-container"></a>Comment modifier le fuseau horaire d’un conteneur en cours d’exécution ?

Pour modifier le fuseau horaire d’un conteneur Windows Server en cours d’exécution, connectez-vous au conteneur en cours d’exécution avec une session PowerShell. Par exemple :
    
```azurecli-interactive
kubectl exec -it CONTAINER-NAME -- powershell
```

Dans le conteneur en cours d’exécution, utilisez [Set-TimeZone](/powershell/module/microsoft.powershell.management/set-timezone) pour définir le fuseau horaire du conteneur en cours d’exécution. Par exemple :

```powershell
Set-TimeZone -Id "Russian Standard Time"
```

Pour afficher le fuseau horaire actuel du conteneur en cours d’exécution ou une liste de fuseaux horaires disponible, utilisez [Get-TimeZone](/powershell/module/microsoft.powershell.management/get-timezone).

## <a name="can-i-maintain-session-affinity-from-client-connections-to-pods-with-windows-containers"></a>Puis-je maintenir l’affinité de session des connexions des clients aux pods avec des conteneurs Windows ?

Bien que le maintien de l’affinité de session entre les connexions des clients et les pods avec des conteneurs Windows sera pris en charge dans la version du système d’exploitation Windows Server 2022, vous obtenez actuellement l’affinité de session par l’adresse IP du client en limitant votre pod souhaité à l’exécution d’une seule instance par nœud et en configurant votre service Kubernetes pour diriger le trafic vers le pod sur le nœud local. 

Utilisez la configuration suivante : 

1. Utilisez un cluster AKS avec une version minimale de 1.20.
1. Limitez votre pod à une seule instance par nœud Windows. Vous pouvez y parvenir en utilisant l’anti-affinité dans votre configuration de déploiement.
1. Dans la configuration de votre service Kubernetes, définissez **externalTrafficPolicy=Local**. Cela garantit que le service Kubernetes dirige le trafic uniquement vers les pods du nœud local.
1. Dans la configuration de votre service Kubernetes, définissez **sessionAffinity: ClientIP**. Cela garantit que l’équilibreur de charge Azure est configuré avec l’affinité de session.

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>Que se passe-t-il si j’ai besoin d’une fonctionnalité qui n’est pas prise en charge ?

Si vous rencontrez des lacunes dans les fonctionnalités, le projet open source [aks-engine][aks-engine] en amont offre un moyen simple et entièrement personnalisable d’exécuter Kubernetes dans Azure et inclut la prise en charge de Windows. Pour plus d’informations, consultez [Feuille de route AKS][aks-roadmap].

## <a name="next-steps"></a>Étapes suivantes

Pour vous lancer avec des conteneurs Windows Server dans AKS, consultez [Créer un pool de nœuds qui exécute Windows Server dans AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
