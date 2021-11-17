---
title: Paramètres DNS du Pare-feu Azure
description: Vous pouvez configurer Pare-feu Azure avec les paramètres de serveur DNS et de proxy DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/08/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 44a9da990b1f44f151ab6492cbca65d1b445ae65
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059542"
---
# <a name="azure-firewall-dns-settings"></a>Paramètres DNS du Pare-feu Azure

Vous pouvez configurer un serveur DNS personnalisé et activer le proxy DNS pour le pare-feu Azure. Configurez ces paramètres lorsque vous déployez le pare-feu ou configurez-les plus tard à partir de la page **Paramètres DNS**. Par défaut, le pare-feu Azure utilise Azure DNS et le Proxy DNS est désactivé.

## <a name="dns-servers"></a>Serveurs DNS

Un serveur DNS gère et résout les noms de domaine en adresses IP. Par défaut, le Pare-feu Azure utilise Azure DNS pour la résolution de noms. Le paramètre **Serveur DNS** vous permet de configurer vos propres serveurs DNS pour la résolution de noms du Pare-feu Azure. Vous pouvez configurer un ou plusieurs serveurs. Si vous configurez plusieurs serveurs DNS, le serveur utilisé est choisi de façon aléatoire.

> [!NOTE]
> Pour les instances Pare-feu Azure gérés à l’aide d’Azure Firewall Manager, les paramètres DNS sont configurés dans la stratégie Pare-feu Azure associée.

### <a name="configure-custom-dns-servers---azure-portal"></a>Configurer des serveurs DNS personnalisés – Portail Azure

1. Sous **Paramètres** du pare-feu Azure, sélectionnez **Paramètres DNS**.
2. Sous **Serveurs DNS**, vous pouvez saisir ou ajouter des serveurs DNS existants qui ont été précédemment spécifiés dans votre réseau virtuel.
3. Sélectionnez **Appliquer**.

Le pare-feu dirige désormais le trafic DNS vers les serveurs DNS spécifiés pour la résolution de noms.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Capture d’écran montrant les paramètres des serveurs DNS.":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Configurer des serveurs DNS personnalisés – Azure CLI

L’exemple suivant met à jour Pare-feu Azure avec des serveurs DNS personnalisés à l’aide de l’interface de ligne de commande Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> La commande `az network firewall` nécessite l’installation de l’extension Azure CLI `azure-firewall` . Vous pouvez l’installer en utilisant la commande `az extension add --name azure-firewall`. 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Configurer des serveurs DNS personnalisés – Azure PowerShell

L’exemple suivant met à jour Pare-feu Azure avec des serveurs DNS personnalisés à l’aide d’Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>Proxy DNS

Vous pouvez configurer le Pare-feu Azure pour qu’il agisse comme proxy DNS. Un proxy DNS est un intermédiaire pour les requêtes DNS entre des machines virtuelles clientes et un serveur DNS.

Si vous souhaitez activer le filtrage des nom de domaine complets (FQDN) dans les règles de réseau, activez le proxy DNS et mettez à jour la configuration de la machine virtuelle pour utiliser le pare-feu comme proxy DNS.

:::image type="content" source="media/dns-settings/dns-proxy-2.png" alt-text="Configuration du proxy DNS à l’aide d’un serveur DNS personnalisé.":::

Si vous activez le filtrage des noms de domaine complets dans les règles de réseau et que vous ne configurez pas les machines virtuelles clientes pour utiliser le pare-feu comme proxy DNS, les requêtes DNS de ces clients risquent d’être transmises à un serveur DNS à un moment différent ou de renvoyer une réponse différente de celle du pare-feu. Le proxy DNS place le Pare-feu Azure sur le trajet des demandes du client pour éviter toute incohérence.


Lorsque Pare-feu Azure est un proxy DNS, deux types de fonctions de mise en cache sont possibles :

- **Cache positif** : la résolution DNS est réussie. Le pare-feu met en cache ces réponses en fonction de la durée de vie (TTL) dans la réponse pendant jusqu’à une heure. 

- **Cache négatif** : la résolution DNS ne produit aucune réponse ni aucune résolution. Le pare-feu met en cache ces réponses en fonction de la durée de vie (TTL) dans la réponse pendant jusqu’à 30 minutes.

Le proxy DNS stocke toutes les adresses IP résolues à partir de noms de domaine complets dans des règles de réseau. Nous vous recommandons d’utiliser des noms de domaine complets qui se résolvent en une seule adresse IP.

En cas d’échec de connexion, le proxy DNS n’effectue pas de nouvelles tentatives ou bascule vers d’autres serveurs DNS, y compris Azure DNS.

### <a name="policy-inheritance"></a>Héritage de stratégie

 Les paramètres DNS de stratégie appliqués à un pare-feu autonome remplacent les paramètres DNS du pare-feu autonome. Une stratégie enfant hérite de tous les paramètres DNS de la stratégie parente, mais elle peut remplacer la stratégie parente.

Par exemple, pour utiliser des noms de domaine complets dans la règle réseau, le proxy DNS doit être activé. Toutefois, si une stratégie parente n’a **pas** de proxy DNS activé, la stratégie enfant ne prend pas en charge les noms de domaine complets dans les règles de réseau, sauf si vous remplacez localement ce paramètre.

### <a name="dns-proxy-configuration"></a>Configuration du proxy DNS

La configuration du proxy DNS nécessite trois étapes :
1. Activez le proxy DNS dans les paramètres DNS de Pare-feu Azure.
2. Si vous le souhaitez, configurez votre serveur DNS personnalisé ou utilisez la valeur par défaut fournie.
3. Configurez l’adresse IP privée de Pare-feu Azure en tant qu’adresse DNS personnalisée dans les paramètres du serveur DNS de votre réseau virtuel. Ce réglage garantit que le trafic DNS est dirigé vers Pare-feu Azure.

#### <a name="configure-dns-proxy---azure-portal"></a>Configurer le proxy DNS – Portail Azure

Pour configurer le proxy DNS, vous devez configurer le paramètre des serveurs DNS de votre réseau virtuel pour utiliser l’adresse IP privée du pare-feu. Activez ensuite le proxy DNS dans les **paramètres DNS** de Pare-feu Azure.

##### <a name="configure-virtual-network-dns-servers"></a>Configurer les serveurs DNS du réseau virtuel 

1. Sélectionnez le réseau virtuel dans lequel le trafic DNS sera routé via l’instance Pare-feu Azure.
2. Sous **Paramètres**, sélectionnez **Serveurs DNS**.
3. Sous **Serveurs DNS**, sélectionnez **Personnalisé**.
4. Saisissez l’adresse IP privée du pare-feu.
5. Sélectionnez **Enregistrer**.
6. Redémarrez les machines virtuelles connectées au réseau virtuel, afin que les nouveaux paramètres de serveur DNS leur soient attribués. Les machines virtuelles continuent d’utiliser leurs paramètres DNS actuels jusqu’à ce qu’elles soient redémarrées.

##### <a name="enable-dns-proxy"></a>Activer le proxy DNS

1. Sélectionnez votre instance Pare-feu Azure.
2. Sous **Paramètres**, sélectionnez **Paramètres DNS**.
3. Le **proxy DNS** est désactivé par défaut. Lorsque ce paramètre est activé, le pare-feu écoute le port 53 et transfère les requêtes DNS aux serveurs DNS configurés.
4. Vérifiez la configuration des **serveurs DNS** pour vous assurer que les paramètres sont adaptés à votre environnement.
5. Sélectionnez **Enregistrer**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Capture d’écran montrant les paramètres du proxy DNS.":::

#### <a name="configure-dns-proxy---azure-cli"></a>Configurer le proxy DNS – Azure CLI

Vous pouvez utiliser l’interface de ligne de commande Azure pour configurer les paramètres du proxy DNS dans Pare-feu Azure. Vous pouvez également l’utiliser pour mettre à jour des réseaux virtuels afin d’utiliser Pare-feu Azure comme serveur DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Configurer les serveurs DNS du réseau virtuel

L’exemple suivant configure le réseau virtuel pour qu’il utilise Pare-feu Azure comme serveur DNS.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>Activer le proxy DNS

L’exemple suivant active la fonctionnalité de proxy DNS dans Pare-feu Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>Configurer le proxy DNS – Azure PowerShell

Vous pouvez utiliser Azure PowerShell pour configurer les paramètres du proxy DNS dans Pare-feu Azure. Vous pouvez également l’utiliser pour mettre à jour des réseaux virtuels afin d’utiliser Pare-feu Azure comme serveur DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Configurer les serveurs DNS du réseau virtuel

L’exemple suivant configure le réseau virtuel pour qu’il utilise Pare-feu Azure comme serveur DNS.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>Activer le proxy DNS

L’exemple suivant active la fonctionnalité de proxy DNS dans Pare-feu Azure.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Étapes suivantes

- [Détails du proxy DNS du Pare-feu Azure](dns-details.md)
- [Filtrage de nom de domaine complet dans les règles de réseau](fqdn-filtering-network-rules.md)
