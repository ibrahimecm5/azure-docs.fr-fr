---
title: Configurer les paramètres réseau pour les clusters Service Fabric managés
description: Découvrez comment configurer votre cluster géré par Service Fabric pour les règles de groupe de sécurité réseau, l’accès aux port RDP, les règles d’équilibrage de charge, etc.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 3482f414029c79ceea9c0ee8bcc258ed2fc495e1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558874"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters"></a>Configurer les paramètres réseau pour les clusters Service Fabric managés

Les clusters Service Fabric managés sont créés avec une configuration réseau par défaut. Cette configuration se compose d’un [Azure Load Balancer](../load-balancer/load-balancer-overview.md) avec une adresse IP publique, un réseau virtuel avec un sous-réseau alloué, et un groupe de sécurité réseau configuré pour les fonctionnalités essentielles des clusters. Il existe également des règles de groupe de sécurité réseau facultatives, telles que l’autorisation de tout le trafic sortant par défaut, destinées à faciliter la configuration du client. Ce document explique comment modifier les options de configuration réseau suivantes, et bien plus encore :

- [Gérer les règles de groupe de sécurité réseau](#nsgrules)
- [Gérer l’accès RDP](#rdp)
- [Gérer la configuration de l’équilibreur de charge](#lbconfig)
- [Activer IPv6](#ipv6)
- [Apporter votre propre réseau virtuel](#byovnet)
- [Apporter votre propre équilibreur de charge](#byolb)

<a id="nsgrules"></a>
## <a name="manage-nsg-rules"></a>Gérer les règles de groupe de sécurité réseau

### <a name="nsg-rules-guidance"></a>Instructions relatives aux règles de groupe de sécurité réseau

Tenez compte de ces considérations lors de la création de règles de groupe de sécurité réseau (NSG, Network Security Group) pour votre cluster géré.

* Les clusters Service Fabric gérés réservent la plage de priorités de 0 à 999 aux fonctionnalités essentielles des règles NSG. Il n’est pas possible de créer de règles NSG personnalisées avec une valeur de priorité inférieure à 1 000.
* Les clusters Service Fabric gérés réservent la plage de priorités de 3 001 à 4 000 à la création de règles NSG facultatives. Ces règles sont ajoutées automatiquement pour faciliter et accélérer la configuration. Vous pouvez les remplacer en ajoutant des règles NSG personnalisées dans la plage de priorités comprise entre 1 000 et 3 000.
* Les règles NSG personnalisées doivent avoir une priorité comprise entre 1 000 et 3 000.

### <a name="apply-nsg-rules"></a>Application de règles NSG
Les clusters Service Fabric gérés vous permettent d’affecter des règles NSG directement dans la ressource de cluster de votre modèle de déploiement.

Utilisez la propriété [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) de votre ressource *Microsoft.ServiceFabric/managedclusters* (version `2021-05-01` ou ultérieure) pour affecter des règles NSG. Par exemple :

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "properties": {
    "networkSecurityRules": [
      {
        "name": "AllowCustomers",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33000-33499",
        "access": "Allow",
        "priority": 2001,
        "direction": "Inbound"
      },
      {
        "name": "AllowARM",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "AzureResourceManager",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33500-33699",
        "access": "Allow",
        "priority": 2002,
        "direction": "Inbound"
      },
      {
        "name": "DenyCustomers",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33700-33799",
        "access": "Deny",
        "priority": 2003,
        "direction": "Outbound"
      },
      {
        "name": "DenyRDP",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "destinationPortRange": "3389",
        "access": "Deny",
        "priority": 2004,
        "direction": "Inbound",
        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
      }
    ],
    "fabricSettings": [
      "..."
    ]
  }
}
```

## <a name="clientconnection-and-httpgatewayconnection-default-and-optional-rules"></a>ClientConnection et HttpGatewayConnection par défaut, et règles facultatives
### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>Règle NSG : SFMC_AllowServiceFabricGatewayToSFRP

Une règle NSG par défaut est ajoutée pour permettre au fournisseur de ressources Service Fabric d’accéder aux ports clientConnectionPort et httpGatewayConnectionPort du cluster. Elle autorise l’accès aux ports par l’intermédiaire de l’étiquette de service « ServiceFabric ».

>[!NOTE]
>Cette règle est toujours ajoutée et ne peut pas être remplacée.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayToSFRP",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.",
        "protocol": "TCP",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "ServiceFabric",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 500,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>Règle NSG : SFMC_AllowServiceFabricGatewayPorts

Cette règle facultative permet aux clients d’accéder à SFX, de se connecter au cluster à l’aide de PowerShell et d’utiliser les points de terminaison de l’API du cluster Service Fabric à partir d’Internet en ouvrant les ports d’équilibrage de charge pour clientConnectionPort et httpGatewayPort.

>[!NOTE]
>Cette règle n’est pas ajoutée s’il existe une règle personnalisée avec les mêmes valeurs d’accès, de direction et de protocole pour le même port. Vous pouvez remplacer cette règle par des règles NSG personnalisées.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3001,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

<a id="rdp"></a>
## <a name="enable-access-to-rdp-ports-from-internet"></a>Activer l’accès aux ports RDP à partir d’Internet

Par défaut, les clusters gérés par Service Fabric ne permettent pas l’accès entrant aux ports RDP à partir d’Internet. Vous pouvez ouvrir l’accès entrant aux ports RDP à partir d’Internet en définissant la propriété suivante sur une ressource de cluster géré par Service Fabric.

```json
"allowRDPAccess": true
```

Lorsque la propriété allowRDPAccess est définie sur true, la règle NSG suivante est ajoutée au déploiement de cluster.

```json
{
    "name": "SFMC_AllowRdpPort",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

Les clusters Service Fabric gérés créent automatiquement des règles NAT de trafic entrant pour chaque instance dans un type de nœud. Pour rechercher les mappages de port permettant d’atteindre des instances spécifiques (nœuds de cluster), procédez comme suit :

À l’aide du portail Azure, recherchez les règles NAT de trafic entrant créées par le cluster géré pour le protocole RDP (Remote Desktop Protocol).

1. Accédez au groupe de ressources du cluster géré dans votre abonnement, nommé au format suivant : SFC_{cluster-id}.

2. Sélectionnez l’équilibreur de charge pour le cluster avec le format suivant : LB-{cluster-name}.

3. Sur la page de votre équilibreur de charge, sélectionnez Règles NAT de trafic entrant. Examinez les règles NAT de trafic entrant pour confirmer le mappage du port frontend du trafic entrant au port cible pour un nœud. 

   La capture d’écran suivante montre les règles NAT de trafic entrant pour trois différents types de nœuds :

   ![Règles NAT entrantes][Inbound-NAT-Rules]

   Par défaut, pour les clusters Windows, l’allocation du Port frontal démarre à 50000 et le port cible est le port 3389, lequel est mappé au service RDP sur le nœud cible.
   >[!NOTE]
   > Si vous utilisez la fonctionnalité BYOLB et que vous souhaitez utiliser le protocole RDP, vous devez configurer un pool NAT séparément. Cela ne crée pas automatiquement de règles NAT pour ces types de nœuds.

4. Connectez-vous à distance au nœud (instance de groupe identique) spécifique. Vous pouvez utiliser le nom d’utilisateur et le mot de passe que vous avez définis lors de la création du cluster ou de toutes autres informations d’identification que vous avez configurées.

La capture d’écran suivante illustre l’utilisation de Remote Desktop Connection pour se connecter au nœud d’applications (Instance 0) dans un cluster Windows :

![Connexion Bureau à distance][sfmc-rdp-connect]

<a id="lbconfig"></a>
## <a name="modify-default-load-balancer-configuration"></a>Modifier la configuration par défaut de l’équilibreur de charge

### <a name="load-balancer-ports"></a>Ports d’équilibreur de charge

Les clusters Service Fabric gérés créent une règle NSG dans la plage de priorités par défaut pour tous les ports d’équilibrage de charge configurés dans la section « loadBalancingRules » sous les propriétés *ManagedCluster*. Cette règle ouvre les ports d’équilibrage de charge pour le trafic entrant à partir d’Internet.  

>[!NOTE]
>Cette règle est ajoutée dans la plage de priorités facultative et peut être remplacée en ajoutant des règles NSG personnalisées.

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>Sondes d’équilibreur de charge

Les clusters gérés par Service Fabric créent automatiquement des sondes d’équilibreur de charge pour les ports de passerelle de la structure, ainsi que tous les ports configurés dans la section `loadBalancingRules` des propriétés du cluster géré.

```json
{
  "value": [
    {
        "name": "FabricTcpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19000,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "FabricHttpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "probe1_tcp_8080",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 8080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
            {
                "id": "<>"
            }
        ]
      },
      "type": "Microsoft.Network/loadBalancers/probes"
    }
  ]
}
```

<a id="ipv6"></a>
## <a name="enable-ipv6-preview"></a>Activer le protocole IPv6 (préversion)
Les clusters gérés n’activent pas le protocole IPv6 par défaut. Cette fonctionnalité active la capacité IPv4/IPv6 double pile complète de routage du serveur frontal de l’équilibreur de charge aux ressources principales. Toutes les modifications que vous apportez à la configuration de l’équilibreur de charge du cluster géré ou aux règles de groupe de sécurité réseau affectent le routage IPv4 et IPv6.

> [!NOTE]
> Ce paramètre n’est pas disponible dans le portail et ne peut pas être modifié une fois le cluster créé

1. Définissez la propriété suivante sur une ressource de cluster géré par Service Fabric.
   ```json
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                "enableIpv6": true
                },
            }
   ```

2. Déployez votre cluster géré avec IPv6. Personnalisez l’[exemple de modèle](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-IPv6) en fonction des besoins ou créez votre propre modèle.
   Dans l’exemple suivant, nous allons créer un groupe de ressources nommé `MyResourceGroup` dans `westus`, et déployer un cluster avec cette fonctionnalité activée.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```
   Après déploiement, le réseau virtuel et les ressources de votre cluster sont à double pile. Par conséquent, l’équilibreur de charge frontal du cluster a une adresse DNS unique créée, par exemple `mycluster-ipv6.southcentralus.cloudapp.azure.com`, qui est associée à une adresse IPv6 publique sur Azure Load Balancer, et à des adresses IPv6 privées sur les machines virtuelles. 


<a id="byovnet"></a>
## <a name="bring-your-own-virtual-network-preview"></a>Apporter votre propre réseau virtuel (préversion)
Cette fonctionnalité permet aux clients d’utiliser un réseau virtuel existant en spécifiant un sous-réseau dédié dans lequel le cluster géré déploiera ses ressources. Cela peut être utile si vous disposez déjà d’un réseau virtuel et d’un sous-réseau configurés avec des stratégies de sécurité et un routage du trafic associés que vous souhaitez utiliser. Après avoir opéré le déploiement sur un réseau virtuel existant, il est facile d’utiliser ou d’intégrer d’autres fonctionnalités de mise en réseau telles qu’Azure ExpressRoute, une passerelle VPN Azure, un groupe de sécurité réseau et un appairage de réseaux virtuels. En outre, vous pouvez [apporter votre propre Azure Load Balancer](#byolb), si nécessaire.

> [!NOTE]
> Lorsque vous utilisez BYOVNET, les ressources de cluster gérées sont déployées dans un sous-réseau. 

> [!NOTE]
> Vous ne pouvez pas modifier ce paramètre après la création du cluster et le cluster géré attribue un groupe de sécurité réseau au sous-réseau fourni. Ne modifiez pas l’attribution du groupe de sécurité réseau, car cela pourrait interrompre le trafic.


**Pour apporter votre propre réseau virtuel :**

1. Obtenez l’`Id` de service de votre abonnement pour votre application fournisseur de ressources Service Fabric.
   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > Vérifiez que vous êtes bien dans le bon abonnement. L’ID du principal ne sera pas le même si l’abonnement se trouve dans un autre locataire.

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   Notez l’**ID** de la sortie précédente comme **principalId**. Vous allez l’utiliser dans une étape ultérieure.

   |Nom de la définition de rôle|ID de définition de rôle|
   |----|-------------------------------------|
   |Contributeur de réseau|4d97b98b-1d4f-4787-a291-c67834d212e7|

   Notez les valeurs de propriété `Role definition name` et `Role definition ID`. Vous allez les utiliser dans une étape ultérieure.

2. Ajoutez une attribution de rôle à l’application fournisseur de ressources Service Fabric. L’ajout d’une attribution de rôle est une action ponctuelle. Pour ajouter le rôle, exécutez les commandes PowerShell suivantes ou configurez un modèle Azure Resource Manager (ARM) comme indiqué ci-dessous. 

   Dans les étapes suivantes, nous commençons avec un réseau virtuel existant nommé ExistingRG-vnet dans le groupe de ressources ExistingRG. Le sous-réseau est nommé default.

   Obtenez les informations requises du réseau virtuel existant.

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzVirtualNetwork -Name ExistingRG-vnet -ResourceGroupName ExistingRG
   ```
   Notez le nom de sous-réseau suivant et la valeur de propriété `Id` retournée dans la section `Subnets` de la réponse. Vous allez les utiliser dans une étape ultérieure.

   ```JSON
   Subnets:[
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/virtualNetworks/ExistingRG-vnet/subnets/default"
   }]
   ```

   Exécutez la commande PowerShell suivante en utilisant l’ID de principal, le nom de définition de rôle de l’étape 2 et l’étendue d’affectation `Id` obtenue ci-dessus :
   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
   ```

   Vous pouvez également ajouter l’attribution de rôle à l’aide d’un modèle Azure Resource Manager (ARM) configuré avec les valeurs appropriées pour `principalId`, `roleDefinitionId`, `vnetName` et `subnetName` :

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('VNetRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > VNetRoleAssignmentID doit être un [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16). Si vous redéployez un modèle incluant cette attribution de rôle, vérifiez que le GUID est celui utilisé à l’origine. Nous vous suggérons d’exécuter ceci de façon isolée ou de supprimer cette ressource du modèle de cluster après le déploiement, car elle ne doit être créée qu’une seule fois.

   Voici un exemple complet de [modèle Azure Resource Manager (ARM) qui crée un sous-réseau de réseau virtuel et effectue une attribution de rôle](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOVNET/createVNet-assign-role.json), que vous pouvez utiliser pour cette étape.

3. Configurez la propriété `subnetId` pour le déploiement de cluster une fois le rôle configuré comme ci-dessous :

   ```JSON
    "resources": [
        {
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            },
            "properties": {
                "subnetId": "subnetId",
            ...
            }
   ```
   Consultez l’[exemple de modèle Apporter votre propre cluster de réseau virtuel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOVNET) ou personnalisez votre propre cluster.

4. Déployez le modèle Azure Resource Manager (ARM) de cluster géré configuré.

   Dans l’exemple suivant, nous allons créer un groupe de ressources nommé `MyResourceGroup` dans `westus`, et déployer un cluster avec cette fonctionnalité activée.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   Lorsque vous apportez votre propre sous-réseau de réseau virtuel, le point de terminaison public est toujours créé et géré par le fournisseur de ressources, mais dans le sous-réseau configuré. La fonctionnalité ne vous permet pas de spécifier l’adresse IP publique ou de réutiliser une adresse IP statique sur l’Azure Load Balancer. Vous pouvez [apporter votre propre Azure Load Balancer](#byolb) de concert avec cette fonctionnalité ou seul si vous avez besoin de ces scénarios ou d’autres scénarios d’équilibreur de charge qui ne sont pas pris en charge en mode natif.

<a id="byolb"></a>
## <a name="bring-your-own-azure-load-balancer-preview"></a>Apporter votre propre Azure Load Balancer (préversion)
Les clusters gérés créent un Azure Load Balancer et un nom de domaine complet avec une adresse IP publique statique pour les types de nœuds principal et secondaires. Cette fonctionnalité vous permet de créer ou de réutiliser un Azure Load Balancer pour les types de nœuds secondaires pour le trafic tant entrant que sortant. Lorsque vous apportez votre propre Azure Load Balancer, vous pouvez :

* Utiliser une adresse IP statique d’équilibreur de charge préconfigurée pour le trafic privé ou public
* Mapper un équilibreur de charge à un type de nœud spécifique
* Configurer des règles de groupe de sécurité réseau par type de nœud, car chaque type de nœud est déployé dans son propre sous-réseau avec un NSG unique 
* Maintenir les stratégies et contrôles existants que vous avez peut-être mis en place

> [!NOTE]
> Lorsque vous utilisez BYOVNET, les ressources de cluster gérées sont déployées dans un sous-réseau avec un NSG, indépendamment des équilibreurs de charge configurés supplémentaires.

> [!NOTE]
> Après le déploiement d’un cluster pour un type de nœud, vous ne pouvez pas passer de par défaut à personnalisé, mais vous pouvez modifier la configuration de l’équilibreur de charge personnalisé.

**Exigences concernant les fonctionnalités**
 * Les types d’Azure Load Balancer de référence (SKU) De base et Standard sont pris en charge
 * Des pools principal et NAT doivent être configurés sur l’Azure Load Balancer existant. Pour un exemple, consultez l’[exemple complet de création et attribution de rôle ici](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOLB/createlb-and-assign-role.json). 

Voici quelques exemples de scénarios pour lesquels les clients peuvent utiliser ceci :

Dans cet exemple, un client souhaite acheminer le trafic via un Azure Load Balancer existant configuré avec une adresse IP statique existante vers deux types de nœuds.
![Apporter votre propre équilibreur de charge, exemple 1][sfmc-byolb-example-1]

Dans cet exemple, un client souhaite acheminer le trafic via des équilibreurs de charge Azure existants afin de faciliter la gestion du flux de trafic de manière indépendante vers ses applications résidant sur des types de nœuds distincts. En cas de configuration comme dans cet exemple, chaque type de nœud se trouve derrière son groupe de sécurité réseau que vous pouvez gérer.
![Apporter votre propre équilibreur de charge, exemple 2][sfmc-byolb-example-2]

Pour configurer l’apport de votre propre équilibreur de charge :

1. Obtenez le service `Id` de votre abonnement pour l’application fournisseur de ressources Service Fabric :

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > Vérifiez que vous êtes bien dans le bon abonnement. L’ID du principal ne sera pas le même si l’abonnement se trouve dans un autre locataire.

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   Notez l’**ID** de la sortie précédente comme **principalId**. Vous allez l’utiliser dans une étape ultérieure.

   |Nom de la définition de rôle|ID de définition de rôle|
   |----|-------------------------------------|
   |Contributeur de réseau|4d97b98b-1d4f-4787-a291-c67834d212e7|

   Notez les valeurs de propriété `Role definition name` et `Role definition ID`. Vous allez les utiliser dans une étape ultérieure.

2. Ajoutez une attribution de rôle à l’application fournisseur de ressources Service Fabric. L’ajout d’une attribution de rôle est une action ponctuelle. Pour ajouter le rôle, exécutez les commandes PowerShell suivantes ou configurez un modèle Azure Resource Manager (ARM) comme indiqué ci-dessous.

   Dans les étapes suivantes, nous allons commencer avec un équilibreur de charge existant nommé Existing-LoadBalancer1, dans le groupe de ressources Existing-RG.

   Obtenez les informations de propriété `Id` requises à partir de l’Azure Load Balancer existant. Nous allons 

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzLoadBalancer -Name "Existing-LoadBalancer1" -ResourceGroupName "Existing-RG"
   ```
   Notez l’`Id` suivant que vous allez utiliser à l’étape suivante :
   ```JSON
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/loadBalancers/Existing-LoadBalancer1"
   }
   ```
   Exécutez la commande PowerShell suivante en utilisant l’ID de principal, le nom de définition de rôle de l’étape 2 et l’étendue d’affectation `Id` que vous venez d’obtenir :

   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/loadBalancers/<LoadBalancerName>"
   ```

   Vous pouvez également ajouter l’attribution de rôle à l’aide d’un modèle Azure Resource Manager (ARM) configuré avec les valeurs appropriées pour `principalId` obtenues à l’étape 1, `loadBalancerRoleAssignmentID` et `roleDefinitionId` :

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('loadBalancerRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > loadBalancerRoleAssignmentID doit être un [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16). Si vous redéployez un modèle incluant cette attribution de rôle, vérifiez que le GUID est celui utilisé à l’origine. Nous vous suggérons d’exécuter ceci de façon isolée ou de supprimer cette ressource du modèle de cluster après le déploiement, car elle ne doit être créée qu’une seule fois.

3. Configurez la connectivité sortante requise. Tous les nœuds doivent être en mesure d’acheminer le trafic sortant sur le port 443 vers le fournisseur de ressources Service Fabric. Vous pouvez utiliser la balise de service `ServiceFabric` dans votre groupe de sécurité réseau pour limiter la destination du trafic au point de terminaison Azure.

4. Configurez éventuellement un port d’application entrant et une sonde associée sur votre Azure Load Balancer existant.

5. Configurez éventuellement les règles de groupe de sécurité réseau de cluster géré appliquées au type de nœud pour autoriser tout trafic requis que vous avez configuré sur l’Azure Load Balancer. Autrement, le trafic sera bloqué.

   Pour un exemple d’ouverture de règles de trafic entrant, consultez le [modèle Azure Resource Manager (ARM) Apporter votre propre équilibreur de charge](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOLB).

6. Déployer le modèle ARM de cluster géré configuré

   Dans l’exemple suivant, nous allons créer un groupe de ressources nommé `MyResourceGroup` dans `westus`, et déployer un cluster avec cette fonctionnalité activée.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   Après le déploiement, votre type de nœud secondaire est configuré pour utiliser l’équilibreur de charge spécifié pour le trafic entrant et sortant. La connexion client Service Fabric et les points de terminaison de passerelle pointent toujours vers le DNS public de l’adresse IP statique du type de nœud principal de cluster géré.


## <a name="next-steps"></a>Étapes suivantes
[Options de configuration de cluster géré Service Fabric](how-to-managed-cluster-configuration.md)
[Vue d’ensemble des clusters gérés Service Fabric](overview-managed-cluster.md)

<!--Image references-->
[Inbound-NAT-Rules]: ./media/how-to-managed-cluster-networking/inbound-nat-rules.png
[sfmc-rdp-connect]: ./media/how-to-managed-cluster-networking/sfmc-rdp-connect.png
[sfmc-byolb-example-1]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-1.png
[sfmc-byolb-example-2]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-2.png

