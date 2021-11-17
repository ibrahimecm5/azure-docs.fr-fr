---
title: Configurer un point de terminaison privé avec liaison privée
description: Configurez un point de terminaison privé sur un registre de conteneurs et activez l’accès sur une liaison privée dans un réseau virtuel local. L’accès à la liaison privée est une fonctionnalité du niveau de service Premium.
ms.topic: article
ms.date: 10/26/2021
ms.openlocfilehash: ef3f6cd30852f6c2bac5f5e9dce383d8f4501023
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523407"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Connexion privée à un registre de conteneurs Azure à l’aide d’Azure Private Link

Limitez l’accès à un registre en attribuant des adresses IP privées de réseau virtuel aux points de terminaison du registre et en utilisant [Azure Private Link](../private-link/private-link-overview.md). Le trafic entre les clients du réseau virtuel et les points de terminaison privés du registre traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine toute exposition sur l’Internet public. Azure Private Link permet également un accès privé au registre à partir d’un emplacement local par le biais d’un Peering privé [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) ou d’une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

Vous pouvez [configurer des paramètres DNS](../private-link/private-endpoint-overview.md#dns-configuration) pour les points de terminaison privés du registre afin que les paramètres résolvent l’adresse IP privée allouée au registre. Avec la configuration DNS, les clients et les services du réseau peuvent continuer à accéder au registre avec le nom de domaine complet du registre, tel que *myregistry.azurecr.io*. 

Cet article explique comment configurer un point de terminaison privé pour votre registre à l’aide du Portail Azure (recommandé) ou du d’Azure CLI. Cette fonctionnalité est disponible uniquement au niveau de service **Premium** de registre de conteneurs. Pour plus d’informations sur les niveaux de service et les limites de registre, consultez [Niveaux de service Azure Container Registry](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

> [!NOTE]
> À compter d’octobre 2021, les nouveaux registres de conteneurs autorisent un maximum de 200 points de terminaison privés. Les registres créés précédemment autorisent un maximum de 10 points de terminaison privés. Utilisez la commande [az acr show-usage](/cli/azure/acr#az_acr_show_usage) pour voir la limite de votre registre.

## <a name="prerequisites"></a>Prérequis

* Réseau virtuel et sous-réseau dans lequel configurer le point de terminaison privé. Vous pouvez également [créer un nouveau réseau virtuel et un sous-réseau](../virtual-network/quick-create-portal.md).
* Pour le test, il est recommandé de configurer une machine virtuelle dans le réseau virtuel. Pour connaître les étapes de création d’une machine virtuelle de test pour accéder à votre registre, consultez [Créer une machine virtuelle compatible à Docker](container-registry-vnet.md#create-a-docker-enabled-virtual-machine). 
* Pour utiliser les étapes Azure CLI décrites dans cet article, il est recommandé d’utiliser Azure CLI version 2.6.0 ou ultérieure. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli]. Ou exécutez cela dans [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Si vous ne disposez pas d’un registre de conteneurs, créez-en un (niveau Premium requis) et [importez](container-registry-import-images.md) un exemple d’image publique comme `mcr.microsoft.com/hello-world` à partir de Microsoft Container Registry. Par exemple, utilisez le [portail Azure][quickstart-portal] ou [Azure CLI][quickstart-cli] pour créer un registre.

### <a name="register-container-registry-resource-provider"></a>Inscrire le fournisseur de ressources du registre de conteneurs

Pour configurer l’accès au registre à l’aide d’une liaison privée dans un autre abonnement ou abonné Azure, vous devez [inscrire le fournisseur de ressources](../azure-resource-manager/management/resource-providers-and-types.md) pour Azure Container Registry dans cet abonnement. Utilisez le portail Azure, Azure CLI ou d’autres outils.

Exemple :

```azurecli
az account set --subscription <Name or ID of subscription of private link>

az provider register --namespace Microsoft.ContainerRegistry
``` 

## <a name="set-up-private-endpoint---portal-recommended"></a>Configurer un point de terminaison privé - portail (recommandé)

Configurez un point de terminaison privé quand vous créez un registre ou ajoutez un point de terminaison privé à un registre existant. 

### <a name="create-a-private-endpoint---new-registry"></a>Créer un point de terminaison privé (nouveau registre)

1. Quand vous créez un registre dans le portail, sous l’onglet **De base**, dans **SKU**, sélectionnez **Premium**.
1. Sélectionnez l’onglet **Réseau**.
1. Dans **Connectivité réseau**, sélectionnez **Point de terminaison privé** >  **+ Ajouter**.
1. Entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Entrez le nom d’un groupe existant ou créez-en un nouveau.|
    | Nom | Entrez un nom unique. |
    | Sous-ressource du Registre |Sélectionnez le **registre**.|
    | **Mise en réseau** | |
    | Réseau virtuel| Sélectionnez le réseau virtuel pour le point de terminaison privé. Exemple : *myDockerVMVNET*. |
    | Subnet | Sélectionnez le sous-réseau pour le point de terminaison privé. Exemple : *myDockerVMSubnet*. |
    |**Intégration à un DNS privé**||
    |Intégrer à une zone DNS privée |Sélectionnez **Oui**. |
    |Zone DNS privée |Sélectionnez *(Nouveau) privatelink.azurecr.io* |
    |||
1. Configurez les paramètres de registre restants, puis sélectionnez **Évaluer + créer**.
  
:::image type="content" source="media/container-registry-private-link/private-link-create-portal.png" alt-text="Créer un registre avec un point de terminaison privé":::



Votre liaison privée est désormais configurée et prête à être utilisée.

### <a name="create-a-private-endpoint---existing-registry"></a>Créer un point de terminaison privé (registre existant)

1. Dans le portail, accédez à votre registre de conteneurs.
1. Sous **Paramètres**, sélectionnez **Mise en réseau**.
1. Sous l’onglet **Points de terminaison privés**, sélectionnez **+ Point de terminaison privé**.
    :::image type="content" source="media/container-registry-private-link/private-endpoint-existing-registry.png" alt-text="Ajouter un point de terminaison privé au registre":::

1. Sous l’onglet **Informations de base**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Entrez le nom d’un groupe existant ou créez-en un nouveau.|
    | **Détails de l’instance** |  |
    | Nom | Entrez un nom. |
    |Région|Sélectionnez une région.|
    |||
1. Sélectionnez **Suivant : Ressource**.
1. Entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |Méthode de connexion  | Pour cet exemple, sélectionnez **Se connecter à une ressource Azure dans mon répertoire**.|
    | Abonnement| Sélectionnez votre abonnement. |
    | Type de ressource | Sélectionnez **Microsoft.ContainerRegistry/registries**. |
    | Ressource |Sélectionnez le nom de votre registre.|
    |Sous-ressource cible |Sélectionnez le **registre**.|
    |||
1. Sélectionnez **Suivant : Configuration**.
1. Entrez ou sélectionnez les informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    |**Mise en réseau**| |
    | Réseau virtuel| Sélectionnez le réseau virtuel pour le point de terminaison privé |
    | Subnet | Sélectionnez le sous-réseau pour le point de terminaison privé |
    |**Intégration à un DNS privé**||
    |Intégrer à une zone DNS privée |Sélectionnez **Oui**. |
    |Zone DNS privée |Sélectionnez *(Nouveau) privatelink.azurecr.io* |
    |||

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration. 
1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

### <a name="confirm-endpoint-configuration"></a>Confirmer la configuration du point de terminaison

Une fois le point de terminaison privé créé, les paramètres DNS dans la zone privée apparaissent avec les paramètres **Points de terminaison privés** dans le portail :

1. Dans le portail, accédez à votre registre de conteneurs et sélectionnez **Paramètres > Mise en réseau**.
1. Sous l’onglet **Points de terminaison privés**, sélectionnez le point de terminaison privé que vous avez créé. 
1. Sélectionner **Configuration DNS**.
1. Consultez les paramètres de la liaison et les paramètres DNS personnalisés.

:::image type="content" source="media/container-registry-private-link/private-endpoint-overview.png" alt-text="Paramètres DNS du point de terminaison dans le portail":::
## <a name="set-up-private-endpoint---cli"></a>Configurer le point de terminaison privé - CLI

Les exemples Azure CLI fournis dans cet article utilisent les variables d’environnement suivantes. Vous devez disposer des noms d’un registre de conteneurs, d’un réseau virtuel et d’un sous-réseau pour configurer un point de terminaison privé. Remplacez les valeurs par celles appropriées pour votre environnement. Tous les exemples sont mis en forme pour l’interpréteur de commandes Bash :

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name> # Resource group for your existing virtual network and subnet
NETWORK_NAME=<virtual-network-name>
SUBNET_NAME=<subnet-name>
```
### <a name="disable-network-policies-in-subnet"></a>Désactiver les stratégies réseau dans le sous-réseau

[Désactivez les stratégies réseau](../private-link/disable-private-endpoint-network-policy.md) telles que les groupes de sécurité réseau dans le sous-réseau pour le point de terminaison privé. Mettez à jour votre configuration de sous-réseau avec [az network vnet subnet update][az-network-vnet-subnet-update] :

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configurer la zone DNS privée

Créez une [zone Azure DNS privée](../dns/private-dns-privatednszone.md) pour le domaine du registre de conteneurs Azure privé. Dans les étapes ultérieures, vous allez créer des enregistrements DNS pour votre domaine de registre dans cette zone DNS. Pour plus d’informations, consultez [Options de configuration DNS](#dns-configuration-options) plus loin dans cet article.

Pour utiliser une zone privée afin de remplacer la résolution DNS par défaut pour votre registre de conteneurs Azure, la zone doit être nommée **privatelink.azurecr.io**. Exécutez la commande [az network private-dns zone create][az-network-private-dns-zone-create] suivante pour créer la zone privée :

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Créer un lien d’association

Exécutez [az network private-dns link vnet create][az-network-private-dns-link-vnet-create] pour associer votre zone privée au réseau virtuel. Cet exemple crée un lien appelé *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Créer un point de terminaison de registre privé

Dans cette section, créez le point de terminaison privé du registre dans le réseau virtuel. Tout d’abord, obtenez l’ID de ressource de votre registre :

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Exécutez la commande [az network private-endpoint create][az-network-private-endpoint-create] pour créer le point de terminaison privé du registre.

L’exemple suivant crée le point de terminaison *myPrivateEndpoint* et la connexion de service *myConnection*. Pour spécifier une ressource de registre de conteneurs pour le point de terminaison, transmettez `--group-ids registry` :

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-endpoint-ip-configuration"></a>Obtenir la configuration IP du point de terminaison

Pour configurer les enregistrements DNS, récupérez la configuration IP du point de terminaison privé. Deux adresses IP privées sont associées à l’interface réseau du point de terminaison privé dans cet exemple pour le registre de conteneurs : une pour le registre proprement dit et l’autre pour le point de terminaison de données du registre. Si votre registre est géo-répliqué, une adresse IP supplémentaire est associée à chaque réplica.

Tout d’abord, exécutez [az network private-endpoint show][az-network-private-endpoint-show] pour interroger le point de terminaison privé afin d’obtenir l’ID d’interface réseau :

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Les commandes [az network nic show][az-network-nic-show] suivantes obtiennent les adresses IP privées et les noms de domaine complet pour le registre de conteneurs et le point de terminaison de données du registre :

```azurecli
REGISTRY_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateIpAddress" \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateIpAddress" \
  --output tsv)

# An FQDN is associated with each IP address in the IP configurations

REGISTRY_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateLinkConnectionProperties.fqdns" \
  --output tsv)

DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```

#### <a name="additional-endpoints-for-geo-replicas"></a>Points de terminaison supplémentaires pour les géo-réplicas

Si votre registre est [géorépliqué](container-registry-geo-replication.md), interrogez le point de terminaison de données supplémentaire pour chaque réplica du registre. Par exemple, dans la région *eastus* : 

```azurecli
REPLICA_LOCATION=eastus
GEO_REPLICA_DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REPLICA_LOCATION'].privateIpAddress" \
  --output tsv) 

GEO_REPLICA_DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REPLICA_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```
### <a name="create-dns-records-in-the-private-zone"></a>Créer des enregistrements DNS dans la zone privée

Les commandes suivantes créent des enregistrements DNS dans la zone privée pour le point de terminaison du registre et son point de terminaison de données. Par exemple, si vous avez un registre nommé *myregistry* dans la région *westeurope*, les noms des points de terminaison sont `myregistry.azurecr.io` et `myregistry.westeurope.data.azurecr.io`. 

Tout d’abord, exécutez [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] pour créer des jeux d’enregistrements A vides pour le point de terminaison du registre et le point de terminaison de données :

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Exécutez la commande [az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] pour créer les enregistrements A pour le point de terminaison du registre et le point de terminaison de données :

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $REGISTRY_PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

#### <a name="additional-records-for-geo-replicas"></a>Enregistrements supplémentaires pour les géo-réplicas

Si votre registre est géo-répliqué, créez des paramètres DNS supplémentaires pour chaque réplica. Poursuivre l’exemple dans la région *eastus* :

```azurecli
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REPLICA_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REPLICA_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $GEO_REPLICA_DATA_ENDPOINT_PRIVATE_IP
```

La liaison privée est désormais configurée et prête à être utilisée.

## <a name="disable-public-access"></a>Désactiver l’accès public

Pour de nombreux scénarios, désactivez l’accès au registre à partir de réseaux publics. Cette configuration empêche les clients situés en dehors du réseau virtuel d’atteindre les points de terminaison du registre. 

### <a name="disable-public-access---portal"></a>Désactiver l’accès public (portail)

1. Dans le portail, accédez à votre registre de conteneurs et sélectionnez **Paramètres > Mise en réseau**.
1. Sous l’onglet **Accès public**, dans **Autoriser l’accès au réseau public**, sélectionnez **Désactivé**. Ensuite, sélectionnez **Enregistrer**.

### <a name="disable-public-access---cli"></a>Désactiver l’accès public (CLI)

Pour désactiver l’accès public à l’aide d’Azure CLI, exécutez [az acr update][az-acr-update] et définissez `--public-network-enabled` avec `false`. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```

## <a name="validate-private-link-connection"></a>Valider la connexion de liaison privée

Vous devez vérifier que les ressources contenues dans le sous-réseau du point de terminaison privé se connectent à votre registre via une adresse IP privée, et qu’elles sont intégrées à la zone DNS privée appropriée.

Pour valider la connexion de liaison privée, connectez-vous à la machine virtuelle que vous avez configurée dans le réseau virtuel.

Exécutez un utilitaire tel que `nslookup` ou `dig` pour rechercher l’adresse IP de votre registre via la liaison privée. Par exemple :

```bash
dig $REGISTRY_NAME.azurecr.io
```

L’exemple de sortie montre l’adresse IP du registre dans l’espace d’adressage du sous-réseau :

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Comparez ce résultat avec l’adresse IP publique dans la sortie `dig` pour le même registre via un point de terminaison public :

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Opérations de registre via la liaison privée

Vérifiez également que vous pouvez effectuer des opérations de registre à partir de la machine virtuelle dans le sous-réseau. Établissez une connexion SSH à votre machine virtuelle et exécutez [az acr login][az-acr-login] pour vous connecter à votre registre. Selon la configuration de votre machine virtuelle, vous pouvez être amené à préfixer les commandes suivantes avec `sudo`.

```bash
az acr login --name $REGISTRY_NAME
```

Effectuez des opérations de registre telles que `docker pull` pour extraire (pull) un exemple d’image du registre. Remplacez `hello-world:v1` par une image et une balise appropriées pour votre registre, précédées du nom du serveur de connexion du registre (entièrement en minuscules) :

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker tire (pull) correctement l’image sur la machine virtuelle.

## <a name="manage-private-endpoint-connections"></a>Gérer les connexions de point de terminaison privé

Gérez les connexions de point de terminaison privé d’un registre à l’aide du portail Azure ou en utilisant des commandes dans le groupe de commandes [az acr private-endpoint-connection][az-acr-private-endpoint-connection]. Ces opérations incluent l’approbation, la suppression, le fait de lister, le rejet et l’affichage des détails des connexions de point de terminaison privé d’un registre.

Par exemple, pour lister les connexions de point de terminaison privé d’un registre, exécutez la commande [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list]. Par exemple :

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Quand vous configurez une connexion de point de terminaison privé via la procédure décrite dans cet article, le registre accepte automatiquement les connexions à partir des clients et des services qui ont des autorisations Azure RBAC sur le registre. Vous pouvez configurer le point de terminaison pour exiger l’approbation manuelle des connexions. Pour obtenir des informations sur l’approbation et le rejet des connexions de point de terminaison privé, consultez [Gérer une connexion de point de terminaison privé](../private-link/manage-private-endpoint.md).

> [!IMPORTANT]
> Actuellement, si vous supprimez un point de terminaison privé d’un registre, vous devrez peut-être également supprimer la liaison entre le réseau virtuel et la zone privée. Si la liaison n’est pas supprimée, une erreur similaire à `unresolvable host` est susceptible de s’afficher.

## <a name="dns-configuration-options"></a>Options de configuration DNS

Le point de terminaison privé de cet exemple s’intègre à une zone DNS privée associée à un réseau virtuel de base. Cette configuration utilise directement le service DNS fourni par Azure pour résoudre le nom de domaine complet public du registre en ses adresses IP privées dans le réseau virtuel. 

La liaison privée prend en charge des scénarios de configuration DNS supplémentaires qui utilisent la zone privée, y compris des solutions DNS personnalisées. Par exemple, vous pouvez avoir une solution DNS personnalisée déployée dans le réseau virtuel ou localement dans un réseau que vous connectez au réseau virtuel à l’aide d’une passerelle VPN ou d’Azure ExpressRoute. 

Pour résoudre le nom de domaine complet public du registre en adresse IP privée dans ces scénarios, vous devez configurer un redirecteur au niveau du serveur vers le service DNS fourni par Azure (168.63.129.16). Les options et les étapes exactes de configuration dépendent de vos réseaux et DNS existants. Pour obtenir des exemples, consultez [Configuration DNS des points de terminaison privés Azure](../private-link/private-endpoint-dns.md).

> [!IMPORTANT]
> Si, pour la haute disponibilité, vous avez créé des points de terminaison privés dans plusieurs régions, nous vous recommandons d’utiliser un groupe de ressources distinct dans chaque région et d’y placer le réseau virtuel et la zone DNS privée associés. Cette configuration permet également d’éviter une résolution DNS imprévisible causée par le partage de la même zone DNS privée.

### <a name="manually-configure-dns-records"></a>Configurer manuellement les enregistrements DNS

Pour certains scénarios, vous devrez peut-être configurer manuellement les enregistrements DNS dans une zone privée au lieu d’utiliser la zone privée fournie par Azure. Veillez à créer des enregistrements pour chacun des points de terminaison suivants : le point de terminaison du registre, le point de terminaison de données du registre, et le point de terminaison de données pour tout réplica régional supplémentaire. Si tous les enregistrements ne sont pas configurés, le registre risque d’être inaccessible.

> [!IMPORTANT]
> Si vous ajoutez par la suite un nouveau réplica, vous devez ajouter manuellement un nouvel enregistrement DNS pour le point de terminaison de données dans cette région. Par exemple, si vous créez un réplica *myregistry* dans l’emplacement northeurope, ajoutez un enregistrement pour `myregistry.northeurope.data.azurecr.io`.

Les noms de domaine complets et les adresses IP privées dont vous avez besoin pour créer des enregistrements DNS sont associés à l’interface réseau du point de terminaison privé. Vous pouvez obtenir ces informations à l’aide du Portail Azure ou Azure CLI.

* Dans le portail, accédez à votre point de terminaison privé, puis sélectionnez **Configuration DNS**. 
* À l’aide d’Azure CLI, exécutez la commande [az network nic show][az-network-nic-show]. Pour obtenir des exemples de commandes, consultez [Obtenir la configuration IP du point de terminaison](#get-endpoint-ip-configuration) plus haut dans cet article.

Après avoir créé les enregistrements DNS, vérifiez que les noms de domaine complets du registre sont correctement résolus en leurs adresses IP privées respectives.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour nettoyer vos ressources dans le portail, accédez à votre groupe de ressources. Une fois le groupe de ressources chargé, cliquez sur **Supprimer le groupe de ressources** pour supprimer le groupe de ressources et les ressources à cet endroit.

Si vous avez créé toutes les ressources Azure dans le même groupe de ressources et que vous n’en avez plus besoin, vous pouvez éventuellement supprimer les ressources à l’aide d’une seule commande [az group delete](/cli/azure/group) :

```azurecli
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur Liaison privée (Private Link), consultez la documentation [Azure Private Link](../private-link/private-link-overview.md).

* Pour vérifier les paramètres DNS dans le réseau virtuel qui achemine vers un point de terminaison privé, exécutez la commande [az acr check-health](/cli/azure/acr#az_acr_check_health) avec le paramètre `--vnet`. Pour plus d’informations, consultez [Vérifier l’intégrité d’un registre de conteneurs Azure](container-registry-check-health.md) 

* Si vous devez configurer des règles d’accès au registre derrière un pare-feu client, consultez [Configurer des règles pour accéder à un registre de conteneurs Azure derrière un pare-feu](container-registry-firewall-access-rules.md).

* [Résoudre les problèmes de connectivité d’Azure Private Endpoint](../private-link/troubleshoot-private-endpoint-connectivity.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az_acr_private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az_acr_private_endpoint_connection_approve
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az_network_private_endpoint_create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az_network_private_endpoint_show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az_network_private_dns_zone_create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_add_record
[az-network-nic-show]: /cli/azure/network/nic#az_network_nic_show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
