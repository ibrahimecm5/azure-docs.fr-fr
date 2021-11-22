---
title: Configurer Azure Key Vault Managed HSM avec des points de terminaison privés
description: Découvrir comment intégrer Azure Key Vault Managed HSM au service Azure Private Link
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/21/2021
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e7ccb16efe21503087dcac035e2ca8f832ef7b7
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370344"
---
# <a name="integrate-managed-hsm-with-azure-private-link"></a>Intégrer Managed HSM à Azure Private Link

Le service Azure Private Link vous permet d’accéder aux services Azure (par exemple, Managed HSM, Stockage Azure, Azure Cosmos DB, etc.), ainsi qu’aux services de partenaires ou de clients hébergés par Azure via un point de terminaison privé de votre réseau virtuel.

Un point de terminaison privé Azure est une interface réseau qui vous connecte de façon privée et sécurisée à un service basé sur la technologie Azure Private Link. Le point de terminaison privé utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel. Sachant que l’ensemble du trafic à destination du service peut être routé via le point de terminaison privé, il n’y a aucun besoin de passerelles, d’appareils NAT, de connexions ExpressRoute ou VPN ou d’adresses IP publiques. Le trafic entre votre réseau virtuel et le service transite par le réseau principal de Microsoft, éliminant ainsi toute exposition à l’Internet public. Vous pouvez vous connecter à une instance d’une ressource Azure, ce qui vous donne le plus haut niveau de granularité en matière de contrôle d’accès.

Pour plus d’informations, consultez [Qu’est-ce qu’Azure Private Link ?](../../private-link/private-link-overview.md)

> [!NOTE]
> Le module de sécurité matériel (HSM) managé ne prend actuellement pas en charge les règles IP ou les [Points de terminaison de service de réseau virtuel](../../virtual-network/virtual-network-service-endpoints-overview.md). 
> 
## <a name="prerequisites"></a>Prérequis

Pour intégrer un module de sécurité matériel (HSM) managé à Azure Private Link, vous avez besoin des éléments suivants :

- Un module de sécurité matériel (HSM) managé. Voir [Provisionner et activer un HSM managé à l’aide d’Azure CLI](quick-create-cli.md) pour plus de détails
- Un réseau virtuel Azure.
- Un sous-réseau dans le réseau virtuel.
- Des autorisations de propriétaire ou de contributeur à la fois pour le HSM managé et le réseau virtuel.
- Azure CLI version 2.25.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

Votre point de terminaison privé et votre réseau virtuel doivent se trouver dans la même région. Au moment de sélectionner la région du point de terminaison privé sur le portail, les réseaux virtuels qui se trouvent dans cette région sont filtrés automatiquement. Votre HSM peut se trouver dans une autre région.

Votre point de terminaison privé utilise une adresse IP privée de votre réseau virtuel.


## <a name="establish-a-private-link-connection-to-managed-hsm-using-cli-initial-setup"></a>Établir une connexion de liaison privée à Managed HSM à l’aide de l’interface CLI (installation initiale)

```azurecli
az login                                                                   # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                            # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                            # Create a new Resource Group
az provider register -n Microsoft.KeyVault                                 # Register KeyVault as a provider
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny # Turn on firewall

az network vnet create -g {RG} -n {vNet NAME} --location {REGION}           # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.managedhsm.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.managedhsm.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="allow-trusted-services-to-access-managed-hsm"></a>Autoriser les services approuvés à accéder à Managed HSM

Lorsque le pare-feu est activé, tout accès au HSM à partir de n’importe quel emplacement qui n’utilise pas de connexion de points de terminaison privés est refusé, y compris Internet public et les services Azure. Utilisez l'option `--bypass AzureServices` si vous souhaitez autoriser les services Microsoft à accéder à vos clés dans votre HSM géré. Les entités individuelles (par exemple, un compteAzure Storage ou une instance Azure SQL Server) doivent toujours avoir des attributions de rôle spécifiques en place pour pouvoir accéder à une clé. 

> [!NOTE]
> Seuls les scénarios d’utilisation de services approuvés spécifiques sont pris en charge. Pour plus d’informations, reportez-vous à la [liste des scénarios d’utilisation des services de confiance](../general/overview-vnet-service-endpoints.md#trusted-services).

```azurecli
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny --bypass AzureServices
```

### <a name="create-a-private-endpoint-automatically-approve"></a>Créer un point de terminaison privé (approuver automatiquement) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

> [!NOTE]
> Si vous supprimez ce module HSM, le point de terminaison privé cessera de fonctionner. Si vous récupérez (restaurez) ce module HSM ultérieurement, vous devez recréer un nouveau point de terminaison privé.

### <a name="create-a-private-endpoint-manually-request-approval"></a>Créer un point de terminaison privé (demander manuellement une approbation) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Gérer des connexions à liaison privée

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --hsm-name {HSM NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>Ajouter des enregistrements DNS privés
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.managedhsm.azure.net" -n {HSM NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.managedhsm.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {HSM NAME}.managedhsm.azure.net
nslookup {HSM NAME}.privatelink.managedhsm.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>Vérifier le fonctionnement de la connexion à liaison privée

Vous devez vérifier que les ressources contenues dans le sous-réseau de la ressource de point de terminaison privé se connectent à votre HSM via une adresse IP privée, et qu’elles sont intégrées à la zone DNS privée appropriée.

Commencez par créer une machine virtuelle en suivant les étapes décrites dans [Créer une machine virtuelle Windows sur le portail Azure](../../virtual-machines/windows/quick-create-portal.md).

Sous l’onglet « Mise en réseau » :

1. Spécifiez un réseau virtuel et un sous-réseau. Vous pouvez créer un nouveau réseau virtuel ou en utiliser un existant. Si vous en sélectionnez un existant, veillez à ce que la région corresponde.
1. Spécifiez une ressource IP publique.
1. Dans « Groupe de sécurité réseau de la carte réseau », sélectionnez « Aucun ».
1. Dans « Équilibrage de charge », sélectionnez « Non ».

Ouvrez la ligne de commande et exécutez la commande suivante :

```console
nslookup <your-HSM-name>.managedhsm.azure.net
```

Si vous exécutez la commande ns lookup pour résoudre l’adresse IP d’un HSM géré via un point de terminaison public, vous obtenez un résultat semblable à ceci :

```console
c:\ >nslookup <your-hsm-name>.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-hsm-name>.managedhsm.azure.net
```

Si vous exécutez la commande ns lookup pour résoudre l’adresse IP d’un HSM géré via un point de terminaison privé, vous obtenez un résultat semblable à ceci :

```console
c:\ >nslookup your_hsm_name.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-hsm-name>.managed.azure.net
          <your-hsm-name>.privatelink.managedhsm.azure.net
```

## <a name="troubleshooting-guide"></a>Guide de résolution des problèmes

* Vérifiez que le point de terminaison privé est dans l’état Approuvé. 
    1. Utilisez la sous-commande ```az keyvault private-endpoint-connections show``` pour afficher l’état d’une connexion de point de terminaison privé.
    2. Vérifiez que l’état de la connexion est Approuvé et que l’état de provisionnement est Réussite. 
    3. Assurez-vous que le réseau virtuel correspond à celui que vous utilisez.

* Vérifiez que vous disposez d’une ressource de zone DNS privée. 
    1. Vous devez avoir une ressource de zone DNS privée avec ce nom exact : privatelink.managedhsm.azure.net 
    2. Pour savoir comment configurer cela, consultez le lien suivant. [Zones DNS privées](../../dns/private-dns-privatednszone.md)
    
* Vérifiez que la zone DNS privée est liée au réseau virtuel. Ceci peut être le problème si vous continuez de recevoir l’adresse IP publique en retour. 
    1. Si la zone DNS privée n’est pas liée au réseau virtuel, la requête DNS provenant du réseau virtuel va retourner l’adresse IP publique du HSM. 
    2. Accédez à la ressource de zone DNS privée dans le portail Azure et cliquez sur l’option des liens du réseau virtuel. 
    4. Le réseau virtuel qui va effectuer les appels au HSM doit être répertorié. 
    5. Si ce n’est pas le cas, ajoutez-le. 
    6. Pour obtenir des étapes détaillées, consultez le document suivant : [Lier un réseau virtuel à une zone DNS privée](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* Vérifiez que la zone DNS privée contient bien un enregistrement A pour le HSM. 
    1. Accédez à la page Zone DNS privée. 
    2. Cliquez sur Vue d’ensemble et vérifiez qu’il existe un enregistrement A avec le nom simple de votre HSM. Ne spécifiez aucun suffixe.
    3. Vérifiez bien l’orthographe, puis créez ou corrigez l’enregistrement A. Vous pouvez utiliser une durée de vie de 3600 (1 heure). 
    4. Veillez à spécifier l’adresse IP privée correcte. 
    
* Vérifiez que l’enregistrement a l’adresse IP correcte. 
    1. Vous pouvez confirmer l’adresse IP en ouvrant la ressource de point de terminaison privé dans le portail Azure.
    2. Accédez à la ressource Microsoft.Network/privateEndpoints dans le portail Azure.
    3. Dans la page Vue d’ensemble, recherchez interface réseau, puis cliquez sur ce lien. 
    4. Le lien montre la vue d’ensemble de la ressource de carte réseau, qui contient la propriété Adresse IP privée. 
    5. Vérifiez qu’il s’agit de l’adresse IP correcte spécifiée dans l’enregistrement A.

## <a name="limitations-and-design-considerations"></a>Limitations et remarques sur la conception

> [!NOTE]
> Le nombre de HSM avec des points de terminaison privés activés par abonnement est une limite modifiable. La limite indiquée ci-dessous est la limite par défaut. Si vous souhaitez demander une augmentation de la limite pour votre abonnement, veuillez créer un ticket de support Azure. Nous approuverons ces demandes au cas par cas.

**Prix** : Pour plus d’informations sur les prix, consultez [Prix d’Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Nombre maximal de points de terminaison privés par HSM géré** : 64

**Nombre par défaut de HSM gérés avec points de terminaison privés par abonnement** : 400

Pour plus d’informations, consultez [Service Azure Private Link : Limitations](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Private Link](../../private-link/private-link-service-overview.md)
- En savoir plus sur les [HSM gérés](overview.md).
