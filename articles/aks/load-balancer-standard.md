---
title: Utiliser un équilibreur de charge public
titleSuffix: Azure Kubernetes Service
description: Découvrez comment utiliser un équilibreur de charge public avec une référence SKU Standard pour exposer vos services avec Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 1da7c5f189b3ffee7f74a4af94bda7fe2d755c74
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025776"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Utiliser un équilibreur de charge Standard public dans Azure Kubernetes Service (AKS)

L’Azure Load Balancer est sur la couche 4 (L4) du modèle OSI (Open Systems Interconnection) qui prend en charge les scénarios entrants et sortants. Il distribue les flux entrants arrivant sur le serveur front-end de l’équilibreur de charge sur des instances du pool de back-ends.

Intégré à AKS, un équilibreur de charge **public** remplit deux fonctions :

1. Fournir des connexions sortantes aux nœuds de cluster à l’intérieur du réseau virtuel AKS. Il atteint cet objectif en traduisant l’adresse IP privée des nœuds en adresse IP publique incluse dans son *pool sortant*.
2. Fournir l’accès aux applications via les services Kubernetes de type `LoadBalancer`. Cela vous permet de mettre facilement à l’échelle vos applications et de créer des services hautement disponibles.

Un équilibreur de charge **interne (ou privé)** est utilisé lorsque des adresses IP privées sont autorisées uniquement au niveau du serveur front-end. Les équilibreurs de charge internes sont utilisés pour équilibrer la charge du trafic au sein d’un réseau virtuel. Vous pouvez également accéder au front-end d’un équilibreur de charge à partir d’un réseau local dans le cadre d’un scénario hybride.

Ce document aborde l’intégration de l’équilibreur de charge public. Pour l’intégration de l’équilibreur de charge interne, consultez la [documentation relative à l’équilibreur de charge interne AKS](internal-lb.md).

## <a name="before-you-begin"></a>Avant de commencer

Azure Load Balancer se décline en deux références SKU : *De base* et *Standard*. Par défaut, la référence SKU *Standard* est utilisée lorsque vous créez un cluster AKS. Utilisez la référence SKU *Standard* pour accéder à d’autres fonctionnalités, par exemple un pool principal plus grand, [**des pools multinœuds**](use-multiple-node-pools.md) et des [**zones de disponibilité**](availability-zones.md). Il s’agit de la référence SKU d’équilibreur de charge recommandée pour AKS.

Pour plus d’informations sur les références SKU *De base* et *Standard*, consultez [Comparaison des références SKU des équilibreurs de charge Azure][azure-lb-comparison].

Cet article suppose que vous disposez d’un cluster AKS avec l’équilibreur de charge de référence SKU *Standard*, et il vous explique comment utiliser et configurer certaines fonctionnalités de l’équilibreur de charge. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Si vous ne souhaitez pas tirer parti de l’équilibreur de charge Azure pour fournir une connexion sortante et préférez utiliser à la place votre propre passerelle, pare-feu ou proxy à cet effet, vous pouvez ignorer la création du pool sortant de l’équilibreur de charge et de l’adresse IP frontale respective en utilisant un [**type de sortie UserDefinedRouting (UDR)**](egress-outboundtype.md). Le type de sortie définit la méthode de sortie d’un cluster et correspond par défaut au type : équilibreur de charge.

## <a name="use-the-public-standard-load-balancer"></a>Utiliser l’équilibreur de charge standard public

Après la création d’un cluster AKS avec le type de sortie : Équilibreur de charge (par défaut), le cluster est prêt à utiliser l’équilibreur de charge pour exposer également les services.

Pour cela, vous pouvez créer un service public de type `LoadBalancer`, comme indiqué dans l’exemple suivant. Commencez par créer un manifeste de service appelé `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

Déployez le manifeste de service public avec la commande [kubectl apply][kubectl-apply] et spécifiez le nom de votre manifeste YAML :

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

L’équilibreur de charge Azure sera configuré avec une nouvelle adresse IP publique placée au premier plan de ce nouveau service. Étant donné que l’équilibreur de charge Azure peut avoir plusieurs adresses IP frontales, chaque nouveau service déployé obtient une nouvelle adresse IP frontale dédiée qui sera accessible de manière unique.

Vous pouvez confirmer que votre service est créé et que l’équilibreur de charge est configuré en exécutant, par exemple :

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Quand vous affichez les détails du service, l’adresse IP publique créée pour ce service sur l’équilibreur de charge apparaît dans la colonne *EXTERNAL-IP*. Le passage de l’adresse IP de l’état *\<pending\>* à une adresse IP publique réelle peut prendre une ou deux minutes, comme indiqué dans l’exemple ci-dessus.

## <a name="configure-the-public-standard-load-balancer"></a>Configurer l’équilibreur de charge standard public

Lors de l’utilisation de l’équilibreur de charge public SKU standard, un ensemble d’options peuvent être personnalisées au moment de la création ou en mettant à jour le cluster. Ces options vous permettent de personnaliser l’équilibreur de charge pour répondre à vos besoins en matière de charges de travail, et doivent être examinées en conséquence. Avec l’équilibreur de charge standard, vous pouvez :

* Définir ou mettre à l’échelle le nombre d’adresses IP sortantes gérées
* Fournir vos propres [adresses IP sortantes ou préfixes d’adresses IP sortantes](#provide-your-own-outbound-public-ips-or-prefixes)
* Personnaliser le nombre de ports de sortie alloués à chaque nœud du cluster
* Configurer le paramètre de délai d’expiration pour les connexions inactives

> [!IMPORTANT]
> Une seule option d’adresse IP sortante (adresse IP gérée, apport d’une adresse IP propre ou préfixe IP) peut être utilisée à un moment donné.

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Mettre à l’échelle le nombre d’adresses IP sortantes gérées

Azure Load Balancer fournit la connectivité sortante à partir d’un réseau virtuel en plus de la connectivité entrante. Les règles de trafic sortant facilitent la configuration de la traduction des adresses réseau sortantes publiques dans Standard Load Balancer.

Comme toutes les règles dans Load Balancer, les règles de trafic sortant suivent la syntaxe habituelle des règles d’équilibrage de charge et des règles de NAT de trafic entrant :

***Adresses IP frontales + paramètres + pool backend***

Une règle de trafic sortant configure la NAT de trafic sortant pour que toutes les machines virtuelles identifiées par le pool backend soient traduites sur le frontend. Les paramètres permettent de contrôler avec plus de précision l’algorithme de la NAT de trafic sortant.

Une règle de trafic sortant peut être utilisée avec une seule adresse IP publique, mais les règles de trafic sortant facilitent la configuration de la mise à l’échelle de la NAT de trafic sortant. Vous pouvez utiliser plusieurs adresses IP pour vos scénarios à grande échelle et utiliser des règles de trafic sortant afin de limiter les risques d’épuisement de ports SNAT. Chaque adresse IP supplémentaire fournie par un serveur frontal met à disposition 64 000 ports éphémères que Load Balancer peut utiliser en tant que ports SNAT. 

Lorsque vous utilisez un équilibreur de charge de référence (SKU) *Standard* avec des adresses IP sortantes publiques gérées créées par défaut, vous pouvez mettre à l’échelle le nombre de ces adresses à l’aide du paramètre **`load-balancer-managed-ip-count`** .

Pour mettre à jour un cluster existant, exécutez la commande suivante. Ce paramètre peut également être défini au moment de la création du cluster pour avoir plusieurs adresses IP publiques sortantes gérées.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

L’exemple ci-dessus définit le nombre d’adresses IP sortantes publiques gérées sur *2* pour le cluster *myAKSCluster* dans *myResourceGroup*. 

Vous pouvez également utiliser le paramètre **`load-balancer-managed-ip-count`** pour définir le nombre initial d’adresses IP publiques sortantes gérées lors de la création de votre cluster en ajoutant le paramètre **`--load-balancer-managed-outbound-ip-count`** et en lui attribuant la valeur de votre choix. Le nombre d’adresses IP sortantes publiques gérées par défaut est 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Fournir vos propres adresses IP sortantes ou préfixes

Lorsque vous utilisez un équilibreur de charge SKU *Standard*, le cluster AKS crée automatiquement par défaut une adresse IP publique dans le groupe de ressources de l’infrastructure gérée par AKS et l’affecte au pool sortant de l’équilibreur de charge.

Une adresse IP publique créée par AKS est considérée comme une ressource managée AKS. Cela signifie que le cycle de vie de cette adresse IP publique est destiné à être géré par AKS et ne nécessite aucune action de l’utilisateur directement sur la ressource IP publique. Vous pouvez également affecter votre propre adresse IP publique ou préfixe d’adresse IP publique au moment de la création du cluster. Vos adresses IP personnalisées peuvent également être mises à jour sur les propriétés de l’équilibreur de charge d’un cluster existant.

Configuration requise pour l’utilisation de votre propre adresse IP publique ou préfixe :

- Les adresses IP publiques personnalisées doivent être créées et détenues par l’utilisateur. Les adresses IP publiques gérées créées par AKS ne peuvent pas être réutilisées comme adresses IP personnalisées car cela peut entraîner des conflits de gestion.
- Vous devez vérifier que l’identité de cluster AKS (principal du service ou identité managée) dispose des autorisations nécessaires pour accéder à l’adresse IP sortante. Conformément à la [liste des autorisations d’adresses IP publiques requises](kubernetes-service-principal.md#networking).
- Assurez-vous que vous respectez les [conditions préalables et contraintes](../virtual-network/ip-services/public-ip-address-prefix.md#limitations) requises pour configurer des adresses IP sortantes ou des préfixes d’adresses IP sortantes.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Mettre à jour le cluster avec votre propre adresse IP publique sortante

Utilisez la commande [az network public-ip show][az-network-public-ip-show] pour répertorier les ID de vos adresses IP publiques.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

La commande ci-dessus affiche l’ID de l’adresse IP publique *myPublicIP* dans le groupe de ressources *myResourceGroup*.

Utilisez la commande `az aks update` avec le paramètre **`load-balancer-outbound-ips`** pour mettre à jour votre cluster avec vos adresses IP publiques.

L’exemple suivant utilise le paramètre `load-balancer-outbound-ips` avec les ID obtenus par la commande précédente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Mettre à jour le cluster avec votre propre préfixe d’adresse IP publique sortante

Vous pouvez également utiliser des préfixes IP publics pour la sortie avec votre équilibreur de charge de référence (SKU) *Standard*. L’exemple suivant utilise la commande [az network public-ip prefix show][az-network-public-ip-prefix-show] pour répertorier les ID de vos préfixes IP publics :

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

La commande ci-dessus affiche l’ID du préfixe IP public *myPublicIPPrefix* dans le groupe de ressources *myResourceGroup*.

L’exemple suivant utilise le paramètre *load-balancer-outbound-ip-prefixes* avec les ID obtenus par la commande précédente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Créer le cluster avec vos propres adresses IP publiques ou préfixes

Vous souhaiterez peut-être apporter vos propres adresses IP ou préfixes d’adresses IP pour la sortie au moment de la création du cluster, afin de prendre en charge des scénarios tels que l’ajout de points de terminaison de sortie à une liste verte. Ajoutez les mêmes paramètres que ceux indiqués ci-dessus à l’étape de création de votre cluster pour définir vos propres adresses IP publiques et préfixes d’adresses IP au début du cycle de vie d’un cluster.

Utilisez la commande *az aks create* avec le paramètre *load-balancer-outbound-ips* pour créer un cluster commençant par vos adresses IP publiques.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Utilisez la commande *az aks create* avec le paramètre *load-balancer-outbound-ip-prefixes* pour créer un cluster commençant par vos préfixes d’adresses IP publiques.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Configurer les ports de sortie alloués

> [!IMPORTANT]
> Si votre cluster comprend des applications pouvant établir un grand nombre de connexions à un petit ensemble de destinations, par exemple plusieurs instances d’une application front-end se connectant à une base de données, vous risquez de vous retrouver dans une situation d’insuffisance de ports SNAT. L’insuffisance de ports SNAT se produit lorsqu’une application n’a plus assez de ports de sortie pour établir une connexion à une autre application ou à un autre hôte. Si vous êtes dans une situation susceptible de faire l’objet d’une insuffisance de ports SNAT, il est fortement recommandé d’augmenter le nombre de ports de sortie et d’IP front-end sortantes sur l’équilibreur de charge pour y remédier. Vous trouverez ci-dessous des informations sur la façon de calculer correctement les valeurs des ports de sortie et des IP front-end sortantes.

Par défaut, AKS définit *AllocatedOutboundPorts* sur son équilibreur de charge avec la valeur `0`, ce qui permet l’[affectation automatique des ports de sortie en fonction de la taille du pool de back-ends][azure-lb-outbound-preallocatedports] lors de la création d’un cluster. Par exemple, si un cluster a 50 nœuds ou moins, 1 024 ports sont alloués à chaque nœud. À mesure que le nombre de nœuds dans le cluster augmente, le nombre de ports disponibles par nœud diminue. Pour afficher la valeur d’*AllocatedOutboundPorts* affectée à l’équilibreur de charge du cluster AKS, utilisez `az network lb outbound-rule list`. Par exemple :

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

L’exemple de sortie suivant montre que l’affectation automatique des ports de sortie en fonction de la taille du pool de back-ends est activée pour le cluster :

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Pour configurer une valeur spécifique pour *AllocatedOutboundPorts* et les adresses IP sortantes lors de la création ou de la mise à jour d’un cluster, utilisez `load-balancer-outbound-ports` et `load-balancer-managed-outbound-ip-count`, `load-balancer-outbound-ips` ou `load-balancer-outbound-ip-prefixes`. Avant de définir une valeur spécifique ou d’incrémenter une valeur existante pour les ports de sortie et les adresses IP sortantes, vous devez calculer le nombre approprié de ports de sortie et d’adresses IP sortantes. Utilisez l’équation suivante pour arrondir ce calcul à l’entier le plus proche : `64,000 ports per IP / <outbound ports per node> * <number of outbound IPs> = <maximum number of nodes in the cluster>`.

Quand vous calculez le nombre de ports de sortie et d’adresses IP sortantes et définissez les valeurs correspondantes, tenez compte des points suivants :
* Le nombre de ports de sortie est fixé par nœud en fonction de la valeur que vous définissez.
* La valeur des ports de sortie doit être un multiple de 8.
* L’ajout d’IP supplémentaires n’ajoute pas d’autres ports à un nœud. Cela permet d’avoir plus de nœuds dans le cluster.
* Vous devez prendre en compte les nœuds qui peuvent être ajoutés dans le cadre de mises à niveau, notamment le nombre de nœuds spécifié par les [valeurs maxSurge][maxsurge].

Les exemples suivants montrent l’impact des valeurs que vous définissez sur le nombre de ports de sortie et d’adresses IP sortantes :
- Si les valeurs par défaut sont utilisées et que le cluster a 48 nœuds, chaque nœud dispose alors de 1 024 ports.
- Si les valeurs par défaut sont utilisées et que le cluster passe de 48 à 52 nœuds, le nombre de ports disponibles pour chaque nœud passe de 1 024 à 512.
- Si le nombre de ports de sortie est défini sur 1 000 et le nombre d’IP sortantes sur 2, le cluster peut prendre en charge un maximum de 128 nœuds : `64,000 ports per IP / 1,000 ports per node * 2 IPs = 128 nodes`.
- Si le nombre de ports de sortie est défini sur 1 000 et le nombre d’IP sortantes sur 7, le cluster peut prendre en charge un maximum de 448 nœuds : `64,000 ports per IP / 1,000 ports per node * 7 IPs = 448 nodes`.
- Si le nombre de ports de sortie est défini sur 4 000 et le nombre d’IP sortantes sur 2, le cluster peut prendre en charge un maximum de 32 nœuds : `64,000 ports per IP / 4,000 ports per node * 2 IPs = 32 nodes`.
- Si le nombre de ports de sortie est défini sur 4 000 et le nombre d’IP sortantes sur 7, le cluster peut prendre en charge un maximum de 112 nœuds : `64,000 ports per IP / 4,000 ports per node * 7 IPs = 112 nodes`.

> [!IMPORTANT]
> Après avoir calculé le nombre de ports de sortie et d’IP sortantes, vérifiez que vous disposez d’une capacité supplémentaire de ports de sortie pour faire face à la surutilisation des nœuds pendant les mises à niveau. Il est essentiel d’allouer suffisamment de ports d’appoint compte tenu des nœuds supplémentaires nécessaires à la mise à niveau et à d’autres opérations. Par défaut, AKS a un nœud tampon pour les opérations de mise à niveau. Si vous utilisez des [valeurs maxSurge][maxsurge], multipliez le nombre de ports de sortie par nœud par votre valeur maxSurge pour déterminer le nombre de ports nécessaires. Par exemple, si vous avez calculé qu’il vous faut 4 000 ports par nœud avec 7 adresses IP sur un cluster avec au maximum 100 nœuds et 2 nœuds d’appoint :
> * 2 nœuds d’appoint * 4 000 ports par nœud = 8 000 ports nécessaires pour faire face à la surutilisation des nœuds pendant les mises à niveau.
> * 100 nœuds * 4 000 ports par nœud = 400 000 ports nécessaires pour votre cluster.
> * 7 IP * 64 000 ports par IP = 448 000 ports disponibles pour votre cluster.
>
> L’exemple ci-dessus montre que le cluster a une capacité excédentaire de 48 000 ports, ce qui est suffisant pour gérer les 8 000 ports nécessaires pour faire face à la surutilisation des nœuds pendant les mises à niveau.

Une fois les valeurs calculées et vérifiées, vous pouvez les appliquer en utilisant `load-balancer-outbound-ports` et `load-balancer-managed-outbound-ip-count`, `load-balancer-outbound-ips` ou `load-balancer-outbound-ip-prefixes` lors de la création ou de la mise à jour d’un cluster. Par exemple :

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Configuration du délai d’expiration de l’équilibreur de charge

En cas d’épuisement des ressources de port SNAT, les flux sortants échouent tant que les flux existants ne libèrent pas des ports SNAT. L’équilibreur de charge récupère les ports SNAT lorsque le flux se ferme, et l’équilibreur de charge configuré par AKS utilise un délai d’expiration de 30 minutes pour récupérer les ports SNAT des flux inactifs.
Vous pouvez également utiliser un transport (par exemple, **`TCP keepalives`** ) ou **`application-layer keepalives`** pour actualiser un flux inactif et réinitialiser ce délai d’expiration, si nécessaire. Vous pouvez configurer ce délai d’inactivité en suivant l’exemple ci-dessous : 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Si vous anticipez de nombreuses connexions à durée de vie limitée, aucune connexion à longue durée et quelques périodes d’inactivité prolongées, par exemple en tirant parti de `kubectl proxy` ou de `kubectl port-forward`, vous pouvez utiliser une valeur de délai d’expiration faible, par exemple 4 minutes. De même, lorsque vous utilisez des conservations de connexion active TCP, il suffit de les activer sur un côté de la connexion. Par exemple, il suffit de les activer sur le côté serveur uniquement pour réinitialiser la minuterie d’inactivité ; il est inutile que les 2 côtés lancent des conservations de connexion active. Des concepts similaires existent pour la couche d’application, notamment les configurations client-serveur de base de données. Examinez côté serveur les options de persistance de connexion spécifiques aux applications.

> [!IMPORTANT]
> AKS active par défaut la réinitialisation TCP en cas d’inactivité et vous recommande de conserver cette configuration et de l’appliquer pour bénéficier d’un comportement d’application plus prévisible dans vos scénarios.
> TCP RST est envoyé uniquement au cours d’une connexion TCP dont l’état est ESTABLISHED. Pour en savoir plus, cliquez [ici](../load-balancer/load-balancer-tcp-reset.md).

Lorsque vous définissez *IdleTimeoutInMinutes* sur une valeur différente de la valeur par défaut égale à 30 minutes, réfléchissez à la durée pendant laquelle vos charges de travail auront besoin d’une connexion sortante. Tenez également compte de la valeur du délai d’expiration par défaut d’un équilibreur de charge de la référence SKU *Standard* utilisé en dehors d’AKS qui s’élève à 4 minutes. Une valeur *IdleTimeoutInMinutes* qui reflète plus précisément votre charge de travail AKS spécifique peut contribuer à réduire l’épuisement SNAT provoqué par la liaison des connexions qui ne sont plus utilisées.

> [!WARNING]
> La modification des valeurs *AllocatedOutboundPorts* et *IdleTimeoutInMinutes* peut changer de manière significative le comportement de la règle sortante pour votre équilibreur de charge et elle ne doit pas être effectuée à la légère, sans comprendre les compromis et les modèles de connexion de votre application. Consultez la [section Résolution des problèmes SNAT ci-dessous][troubleshoot-snat] ainsi que les rubriques [Règles de trafic sortant dans Load Balancer][azure-lb-outbound-rules-overview] et les [Connexions sortantes dans Azure][azure-lb-outbound-connections] avant de mettre à jour ces valeurs pour comprendre pleinement l’impact de vos modifications.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Limiter le trafic entrant à des plages d’adresses IP spécifiques

Le manifeste suivant utilise *loadBalancerSourceRanges* afin de spécifier une nouvelle plage d’adresses IP pour le trafic externe entrant :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

Cet exemple met à jour la règle pour autoriser le trafic externe entrant uniquement à partir de la plage `MY_EXTERNAL_IP_RANGE`. Si vous remplacez `MY_EXTERNAL_IP_RANGE` par l’adresse IP du sous-réseau interne, le trafic est limité à des adresses IP internes du cluster. Si le trafic est limité à des adresses IP internes du cluster, les clients en dehors de votre cluster Kubernetes ne peuvent pas accéder à l’équilibreur de charge.

> [!NOTE]
> Le trafic entrant et externe passe de l’équilibreur de charge au réseau virtuel pour votre cluster AKS. Le réseau virtuel dispose d’un groupe de sécurité réseau (NSG) qui autorise tout le trafic entrant provenant de l’équilibreur de charge. Ce groupe de sécurité réseau utilise une [balise de service][service-tags] de type *LoadBalancer* pour autoriser le trafic provenant de l’équilibreur de charge.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Conserver l’adresse IP du client sur les connexions entrantes

Par défaut, un service de type `LoadBalancer` [dans Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) et dans AKS ne conserve pas l’adresse IP du client sur la connexion au pod. L’adresse IP privée du nœud correspondra à l’adresse IP source du paquet livré au pod. Pour conserver l’adresse IP du client, vous devez définir `service.spec.externalTrafficPolicy` sur `local` dans la définition du service. Le manifeste suivant montre un exemple :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Personnalisations supplémentaires via des annotations Kubernetes

Voici une liste des annotations prises en charge pour les services Kubernetes avec le type `LoadBalancer` ; ces annotations s’appliquent uniquement aux flux entrants **INBOUND** :

| Annotation | Valeur | Description
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` ou `false`                     | Spécifiez si l’équilibreur de charge doit être interne. Si elle n’est pas définie, la valeur par défaut est public.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Nom du sous-réseau                    | Spécifiez le sous-réseau auquel l’équilibreur de charge interne doit être lié. Il s’agit par défaut du sous-réseau configuré dans le fichier de configuration Cloud, s’il n’est pas défini.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Nom de l’étiquette DNS sur les adresses IP publiques   | Spécifiez le nom d’étiquette DNS pour le service **public**. Si elle est définie sur une chaîne vide, l’entrée DNS de l’adresse IP publique ne sera pas utilisée.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` ou `false`                     | Spécifiez que le service doit être exposé à l’aide d’une règle de sécurité Azure qui peut être partagée avec un autre service, la spécificité des règles d’une augmentation du nombre de services qui peuvent être exposés. Cette annotation s’appuie sur la fonctionnalité [Règles de sécurité augmentée](../virtual-network/network-security-groups-overview.md#augmented-security-rules) Azure des groupes de sécurité réseau. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Nom du groupe de ressources            | Spécifiez le groupe de ressources des adresses IP publiques de l’équilibreur de charge ne figurant pas dans le même groupe de ressources que l’infrastructure de cluster (groupe de ressources de nœud).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Liste des balises de service autorisées          | Spécifiez une liste de [balises de service][service-tags] autorisées et séparées par une virgule.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Le délai d'expiration TCP en minutes          | Spécifiez la valeur en minutes, des délais d’expiration de la connexion TCP sur l’équilibreur de charge. La valeur par défaut et minimale est 4. La valeur maximale est 30. Doit être un entier.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Désactivez `enableTcpReset` pour SLB. Déconseillé dans Kubernetes 1.18 et supprimé dans 1.20. 


## <a name="troubleshooting-snat"></a>Résolution des problèmes SNAT

Si vous savez que vous lancez de nombreuses connexions TCP ou UDP sortantes vers les mêmes adresse IP et port de destination et constatez que des connexions sortantes échouent, ou si l’équipe de support vous signale que les ports SNAT (ports éphémères préaffectés) utilisés par la PAT arrivent à épuisement, plusieurs options d’atténuation générales s’offrent à vous. Passez en revue ces options et choisissez l’option disponible la plus appropriée pour votre scénario. Plusieurs options peuvent être adaptées à votre scénario. Pour plus d’informations, consultez le [Guide de résolution des problèmes de connexions sortantes](../load-balancer/troubleshoot-outbound-connection.md).

Souvent, la cause racine de l’épuisement des ressources SNAT est un anti-modèle du mode d’établissement et de gestion de la connectivité sortante, ou des minuteurs configurables dont la valeur par défaut a été changée. Lisez attentivement cette section.

### <a name="steps"></a>Étapes
1. Vérifiez si vos connexions restent inactives pendant une longue période et s’appuient sur le délai d’expiration par défaut pour libérer ce port. Dans ce cas, le délai d’expiration par défaut de 30 minutes devra éventuellement être réduit pour votre scénario.
2. Examinez comment votre application crée une connectivité sortante (par exemple, la révision de code ou la capture de paquets).
3. Déterminez si cette activité est un comportement attendu ou si l’application ne fonctionne pas correctement. Utilisez des [métriques](../load-balancer/load-balancer-standard-diagnostics.md) et des [journaux d’activité](../load-balancer/monitor-load-balancer.md) dans Azure Monitor pour justifier vos découvertes. Utilisez la catégorie « Échec » pour la métrique Connexions SNAT, par exemple.
4. Évaluez si les [modèles](#design-patterns) appropriés sont suivis.
5. Évaluez si l’épuisement des ports SNAT doit être atténué avec des [adresses IP sortantes supplémentaires + des ports sortants alloués supplémentaires](#configure-the-allocated-outbound-ports).

### <a name="design-patterns"></a>Modèles de conception
Tirez toujours parti de la réutilisation des connexions et du regroupement de connexions chaque fois que c’est possible. Ces modèles évitent les problèmes d’épuisement des ressources et entraînent un comportement prévisible. Des primitives pour ces modèles sont disponibles dans beaucoup de frameworks et de bibliothèques de développement.

- Les demandes atomiques (une demande par connexion) ne sont généralement pas un bon choix de conception. Ce type d’anti-modèle limite la mise à l’échelle, réduit les performances et diminue la fiabilité. À la place, réutilisez les connexions HTTP/S pour réduire le nombre de connexions et les ports SNAT associés. L’échelle de l’application et les performances augmentent en raison de la réduction des connexions (handshakes), de la surcharge et du coût des opérations de chiffrement pendant l’utilisation de TLS.
- Si vous utilisez un cluster ou un DNS personnalisé, ou des serveurs en amont personnalisés sur coreDNS, gardez à l’esprit que DNS peut introduire un grand nombre de flux individuels quand le client ne met pas en cache le résultat des programmes de résolution DNS. Veillez à personnaliser d’abord coreDNS au lieu d’utiliser des serveurs DNS personnalisés, puis définissez une valeur de mise en cache adaptée.
- Les flux UDP (par exemple, les recherches DNS) allouent des ports SNAT pour la durée du délai d’inactivité. Plus le délai d’inactivité est long, plus la sollicitation des ports SNAT est forte. Utilisez un délai d’inactivité court (par exemple, 4 minutes).
Utilisez des pools de connexions pour déterminer votre volume de connexions.
- N’abandonnez jamais un flux TCP en mode silencieux et ne vous fiez pas aux minuteries TCP pour nettoyer un flux. Si vous ne laissez pas le protocole TCP fermer explicitement la connexion, l’état reste alloué aux systèmes intermédiaires et aux points de terminaison, et les ports SNAT ne sont alors pas disponibles pour les autres connexions. Ce modèle peut déclencher des échecs d’application et l’épuisement des ports SNAT.
- Ne changez pas les valeurs de minuteur lié à la fermeture TCP au niveau du système d’exploitation sans en connaître précisément l’impact. Même si la pile TCP est récupérée, les performances de votre application risquent d’être affectées si les points de terminaison d’une connexion ont des attentes incompatibles. Quand vous voulez changer les minuteurs, c’est généralement qu’il y a un problème de conception sous-jacent. Tenez compte des recommandations suivantes.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passage d’un équilibreur de charge avec une référence SKU de base à une référence SKU standard

Si vous avez un cluster existant avec le Load Balancer à référence SKU De base, il existe des différences de comportement importantes à noter lorsque vous migrez pour utiliser un cluster avec le Load Balancer à référence SKU Standard.

Par exemple, le fait d’effectuer des déploiements bleus/verts pour migrer des clusters est une pratique courante étant donné que le type de `load-balancer-sku` d’un cluster ne peut être défini qu’au moment de la création du cluster. Toutefois, les Load Balancers à *référence SKU De base* utilisent des adresses IP à *référence SKU De base* qui ne sont pas compatibles avec les Load Balancers à *référence SKU Standard*, car ceux-ci requièrent des adresses IP à *référence SKU Standard*. Lors de la migration de clusters pour mettre à niveau les références SKU du Load Balancer, une nouvelle adresse IP avec une référence SKU d’adresse IP compatible est nécessaire.

Pour plus d’informations sur la migration de clusters, consultez [notre documentation sur les considérations relatives à la migration](aks-migration.md) pour afficher une liste des rubriques importantes à prendre en compte lors de la migration. Les limitations ci-dessous constituent également des différences de comportement importantes à prendre en compte lors de l’utilisation de Load Balancers à référence SKU Standard dans AKS.

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous créez et gérez des clusters AKS prenant un charge un équilibreur de charge avec la référence SKU *Standard* :

* Au moins une adresse IP publique ou un préfixe IP sont requis pour autoriser le trafic sortant du cluster AKS. L’adresse IP publique et le préfixe IP sont également nécessaires pour maintenir la connectivité entre le plan de contrôle et les nœuds d’agent, ainsi que pour assurer la compatibilité avec les versions précédentes d’AKS. Pour spécifier des adresses IP publiques ou des préfixes IP avec un équilibreur de charge de référence (SKU) *Standard*, vous disposez des options suivantes :
    * Fournir vos propres adresses IP publiques.
    * Fournir vos propres préfixes IP publics.
    * Spécifier un nombre de 1 à 100 pour autoriser le cluster AKS à créer ce nombre d’adresses IP publiques de référence (SKU) *Standard* dans le groupe de ressources créé en tant que cluster AKS, dont le nom commence généralement par *MC_* . AKS attribue l’adresse IP publique pour l’équilibreur de charge de référence SKU *Standard*. Par défaut, une adresse IP publique est automatiquement créée dans le même groupe de ressources que le cluster AKS si aucune adresse IP publique, aucun préfixe IP public ou aucun nombre d’adresses IP ne sont spécifiés. Vous devez également autoriser des adresses publiques et éviter de créer une Azure Policy interdisant la création d’adresses IP.
* Une adresse IP publique créée par AKS ne peut pas être réutilisée comme votre propre adresse IP publique. Toutes les adresses IP personnalisées doivent être créées et gérées par l’utilisateur.
* Vous ne pouvez définir la référence (SKU) d’équilibreur de charge que lorsque vous créez un cluster AKS. Vous ne pouvez pas modifier la référence SKU de l’équilibreur de charge après la création d’un cluster AKS.
* Vous pouvez utiliser un seul type de référence SKU d’équilibreur de charge (De base ou Standard) dans un même cluster.
* Les Load Balancers à référence SKU *Standard* prennent uniquement en charge les adresses IP à référence SKU *Standard*.


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les services Kubernetes dans la [documentation des services Kubernetes][kubernetes-services].

Pour en savoir plus sur l’utilisation de l’équilibreur de charge interne pour le trafic entrant, consultez la [documentation sur l’équilibreur de charge interne AKS](internal-lb.md).

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
[maxsurge]: upgrade-cluster.md#customize-node-surge-upgrade