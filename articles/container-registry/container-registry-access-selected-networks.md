---
title: Configurer l’accès au registre public
description: Configurez des règles IP pour activer l’accès à un registre de conteneurs Azure à partir de certaines IP publiques ou plages d’adresses.
ms.topic: article
ms.date: 07/30/2021
ms.openlocfilehash: 3a4a4a28dfbcd859cf97be6799e24a8110add436
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627004"
---
# <a name="configure-public-ip-network-rules"></a>Configurer des règles de réseau IP public

Par défaut, un registre de conteneurs Azure accepte les connexions via Internet à partir d’hôtes sur n’importe quel réseau. Cet article explique comment configurer votre registre de conteneurs pour autoriser l’accès uniquement à partir d’IP publiques ou de plages d’adresses spécifiques. Les étapes équivalentes à l’aide de l’interface de ligne de commande Azure et du portail Azure sont fournies.

Les règles de réseau IP sont configurées sur le point de terminaison du registre public. Les règles de réseau IP ne s’appliquent pas aux points de terminaison privés configurés avec [Azure Private Link](container-registry-private-link.md).

La configuration des règles d’accès IP est disponible uniquement au niveau de service **Premium** de registre de conteneurs. Pour plus d’informations sur les niveaux de service et les limites de registre, consultez [Niveaux de service Azure Container Registry](container-registry-skus.md).

Chaque registre prend en charge un maximum de 100 règles d’accès IP.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>Accès à partir du réseau public sélectionné (CLI)

### <a name="change-default-network-access-to-registry"></a>Changer l’accès réseau par défaut au registre

Pour limiter l’accès à un réseau public sélectionné, commencez par changer l’action par défaut de manière à refuser l’accès. Substituez le nom de votre registre dans la commande [az acr update][az-acr-update] suivante :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Ajouter une règle de réseau au registre

Utilisez la commande [az acr network-rule add][az-acr-network-rule-add] pour ajouter à votre registre une règle de réseau qui autorise l’accès à partir d’une IP publique ou d’une plage. Par exemple, substituez le nom du registre de conteneurs et l’IP publique d’une machine virtuelle dans un réseau virtuel.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Après l’ajout d’une règle, il faut quelques minutes pour que la règle prenne effet.

## <a name="access-from-selected-public-network---portal"></a>Accès à partir du réseau public sélectionné (Portail)

1. Dans le portail, accédez à votre registre de conteneurs.
1. Sous **Paramètres**, sélectionnez **Mise en réseau**.
1. Sous l’onglet **Accès public**, sélectionnez l’option permettant l’accès public à partir des **réseaux sélectionnés**.
1. Sous **Pare-feu**, entrez une IP publique, telle que l’IP publique d’une machine virtuelle dans un réseau virtuel. Ou bien, entrez en notation CIDR une plage d’adresses qui contient l’adresse IP de la machine virtuelle.
1. Sélectionnez **Enregistrer**.

![Configurer la règle de pare-feu pour le registre de conteneurs][acr-access-selected-networks]

> [!NOTE]
> Après l’ajout d’une règle, il faut quelques minutes pour que la règle prenne effet.

> [!TIP]
> Activez éventuellement l’accès au registre à partir d’un ordinateur client local ou d’une plage d’adresses IP. Pour autoriser cet accès, vous avez besoin de l’adresse IPv4 publique de l’ordinateur. Vous pouvez trouver cette adresse en effectuant une recherche sur « quelle est mon adresse IP » dans un navigateur Internet. L’adresse IPv4 actuelle du client apparaît également de façon automatique lorsque vous configurez les paramètres de pare-feu sur la page **Mise en réseau** du portail.

## <a name="disable-public-network-access"></a>Désactiver l’accès au réseau public

Éventuellement, désactivez le point de terminaison public sur le registre. La désactivation du point de terminaison public écrase toutes les configurations de pare-feu. Par exemple, vous souhaiterez peut-être désactiver l’accès public à un registre sécurisé dans un réseau virtuel à l’aide d’[Azure Private Link](container-registry-private-link.md).

> [!NOTE]
> Si le registre est configuré dans un réseau virtuel avec un [point de terminaison de service](container-registry-vnet.md), la désactivation de l'accès au point de terminaison public du registre désactive également l'accès au registre au sein du réseau virtuel.

### <a name="disable-public-access---cli"></a>Désactiver l’accès public (CLI)

Pour désactiver l’accès public à l’aide d’Azure CLI, exécutez [az acr update][az-acr-update] et définissez `--public-network-enabled` avec `false`. L’argument `public-network-enabled` requiert Azure CLI 2.6.0 ou une version ultérieure. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Désactiver l’accès public (portail)

1. Dans le portail, accédez à votre registre de conteneurs et sélectionnez **Paramètres > Mise en réseau**.
1. Sous l’onglet **Accès public**, dans **Autoriser l’accès au réseau public**, sélectionnez **Désactivé**. Ensuite, sélectionnez **Enregistrer**.

![Désactiver l’accès public][acr-access-disabled]


## <a name="restore-public-network-access"></a>Restaurer l’accès au réseau public

Pour réactiver le point de terminaison public, mettez à jour les paramètres de mise en réseau pour autoriser l’accès public. L’activation du point de terminaison public écrase toutes les configurations de pare-feu. 

### <a name="restore-public-access---cli"></a>Restaurer l’accès public (CLI)

Exécutez [az acr update][az-acr-update] et définissez `--public-network-enabled` sur `true`. 

> [!NOTE]
> L’argument `public-network-enabled` requiert Azure CLI 2.6.0 ou une version ultérieure. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Restaurer l’accès public (Portail)

1. Dans le portail, accédez à votre registre de conteneurs et sélectionnez **Paramètres > Mise en réseau**.
1. Sous l’onglet **Accès public**, dans **Autoriser l’accès au réseau public**, sélectionnez **Tous les réseaux**. Ensuite, sélectionnez **Enregistrer**.

![Accès public à partir de tous les réseaux][acr-access-all-networks]

## <a name="troubleshoot"></a>Dépanner

### <a name="access-behind-https-proxy"></a>Accès derrière un proxy HTTPS

Si une règle de réseau public est définie ou si l’accès public au registre est refusé, les tentatives de connexion au registre à partir d’un réseau public non autorisé échouent. L’accès client depuis derrière un proxy HTTPS échoue également si aucune règle d’accès n’est définie pour le proxy. Un message d'erreur semblable à `Error response from daemon: login attempt failed with status: 403 Forbidden` ou `Looks like you don't have access to registry` s'affiche.

Ces erreurs peuvent également se produire si vous utilisez un proxy HTTPS qui est autorisé par une règle d’accès réseau, mais que le proxy n’est pas correctement configuré dans l’environnement client. Vérifiez que votre client Docker et le démon Docker sont configurés pour le comportement du proxy. Pour plus d’informations, consultez [proxy HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) dans la documentation Docker.

### <a name="access-from-azure-pipelines"></a>Accès à partir d’Azure Pipelines

Si vous utilisez Azure Pipelines avec un registre de conteneurs Azure qui limite l’accès à des adresses IP spécifiques, il se peut que le pipeline ne puisse pas accéder au registre, car l’adresse IP sortante du pipeline n’est pas fixe. Par défaut, le pipeline exécute des travaux à l’aide d’un [agent](/azure/devops/pipelines/agents/agents) hébergé par Microsoft sur un pool de machines virtuelles avec un ensemble d’adresses IP qui varie.

Une solution de contournement consiste à remplacer l’agent hébergé par Microsoft utilisé pour exécuter le pipeline par un agent autohébergé. Avec un agent auto-hébergé s’exécutant sur une machine [Windows](/azure/devops/pipelines/agents/v2-windows) ou [Linux](/azure/devops/pipelines/agents/v2-linux) que vous gérez, vous contrôlez l’adresse IP sortante du pipeline et vous pouvez ajouter cette adresse à une règle d’accès IP au registre.

### <a name="access-from-aks"></a>Accès à partir d’AKS

Si vous utilisez AKS (Azure Kubernetes Service) avec un registre de conteneurs Azure qui limite l’accès à des adresses IP spécifiques, vous ne pouvez pas configurer une adresse IP AKS fixe par défaut. L’adresse IP de sortie du cluster AKS est attribuée de façon aléatoire.

Pour permettre au cluster AKS d’accéder au registre, vous disposez des options suivantes :

* Si vous utilisez Azure Basic Load Balancer, configurez une [adresse IP statique](../aks/egress.md) pour le cluster AKS. 
* Si vous utilisez Azure Standard Load Balancer, consultez les instructions de [contrôle du trafic de sortie](../aks/limit-egress-traffic.md) à partir du cluster.

## <a name="next-steps"></a>Étapes suivantes

* Pour restreindre l’accès à un registre à l’aide d’un point de terminaison privé dans un réseau virtuel, consultez [Configurer Azure Private Link pour un registre de conteneurs Azure](container-registry-private-link.md).
* Si vous devez configurer des règles d’accès au registre derrière un pare-feu client, consultez [Configurer des règles pour accéder à un registre de conteneurs Azure derrière un pare-feu](container-registry-firewall-access-rules.md).
* Si vous avez besoin d’autres conseils pour la résolution des problèmes, consultez [Résoudre des problèmes de réseau avec un registre](container-registry-troubleshoot-access.md).

[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
