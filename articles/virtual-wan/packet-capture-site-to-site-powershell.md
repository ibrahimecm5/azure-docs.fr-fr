---
title: Configurer une capture de paquets pour les connexions de site à site Virtual WAN
description: Découvrez comment effectuer une capture de paquets sur la passerelle VPN site à site Virtual WAN au moyen de PowerShell.
services: virtual-wan
titleSuffix: Azure Virtual WAN
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/09/2021
ms.author: cherylmc
ms.openlocfilehash: a5b4427627b164c4f87d2c65203a69c1d2cc2699
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138905"
---
# <a name="configure-a-packet-capture-for-virtual-wan-site-to-site-vpn-powershell"></a>Configurer une capture de paquets pour VPN de site à site Virtual WAN : PowerShell

Cet article vous aide à créer une capture de paquets pour une passerelle VPN de site à site Azure Virtual WAN à l’aide d’Azure PowerShell. La capture de paquets vous aide à restreindre l’étendue d’un problème à certaines parties du réseau. Elle peut vous aider à déterminer si le problème se situe sur le réseau côté local, côté Azure ou entre les deux. En circonscrivant le problème, vous pouvez déboguer plus efficacement et prendre des mesures correctives.

Bien qu'il existe des outils de capture de paquets couramment disponibles, il peut être difficile d'obtenir des captures de paquets pertinentes avec ces outils, en particulier dans les scénarios de trafic à fort volume. Les capacités de filtrage fournies par la capture de paquets Virtual WAN sont particulièrement utiles. La capture de paquets Virtual WAN peut être utilisée avec les outils de capture de paquets couramment disponibles.

## <a name="prerequisites"></a>Prérequis

Vérifiez que la configuration suivante figure déjà dans votre environnement :

* Un réseau étendu virtuel et un hub virtuel.
* Une passerelle VPN de site à site déployée dans le hub virtuel.
* Vous pouvez également avoir des connexions entre des sites VPN et votre passerelle VPN site à site.

### <a name="working-with-azure-powershell"></a>Utilisation d’Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="set-up-the-environment"></a>Configurer l’environnement

Utilisez la commande suivante pour vérifier que vous utilisez l’abonnement approprié et que vous êtes connecté en tant qu’utilisateur disposant des autorisations nécessaires pour effectuer la capture de paquets sur la passerelle VPN de site à site

```azurepowershell-interactive
$subid = “<insert Virtual WAN subscription ID here>”
Set-AzContext -SubscriptionId $subid
```

## <a name="create-a-storage-account-and-container"></a><a name="storage"></a> Créer un compte de stockage et un conteneur

Un compte de stockage est utilisé pour stocker les résultats des captures de paquets.

1. Créez un compte de stockage. Pour obtenir des instructions, consultez [Créer un compte de stockage](../storage/common/storage-account-create.md?tabs=azure-portal).
1. Créez un objet conteneur dans votre compte de stockage. Pour obtenir des instructions, consultez [Créer un compte de stockage](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

## <a name="generate-the-sas-url"></a><a name="URL"></a> Générer l’URL SAS

Lorsque vous arrêtez une capture de paquets, vous devez fournir l'**URL SAS** du conteneur de stockage que vous avez créé. Les résultats de votre capture de paquets sont stockés via cette URL. 

Exécutez les commandes suivantes pour générer une URL de signature d’accès partagé (SAP) :

```azurepowershell-interactive
$rg = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rg -Name $storeName
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
```

## <a name="start-a-packet-capture"></a><a name="start"></a> Démarrer une capture de paquets

Cette section vous aide à démarrer une capture de paquets pour votre passerelle VPN de site à site (toutes les connexions).

1. Pour exécuter une capture de paquets, vous avez besoin de la valeur **-Name** de la passerelle VPN de site à site. Pour rechercher la valeur **-Name**, dans le Portail Azure, accédez à votre hub virtuel, sous **Connectivité**, cliquez sur **VPN (de site à site)** .

   :::image type="content" source="./media/packet-capture-site-to-site-powershell/vpn-gateway-name.png" alt-text="Image du nom de la passerelle Virtual WAN." lightbox="./media//packet-capture-site-to-site-powershell/vpn-gateway-name.png":::

1. Pour démarrer une capture de paquets, exécutez la commande suivante :

   ```azurepowershell-interactive
   Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>"
   ```

## <a name="optional-specify-filters"></a><a name="filters"></a> Facultatif : spécifier des filtres

Pour simplifier vos captures de paquets, vous pouvez spécifier des filtres pour cibler des comportements spécifiques.

>[!NOTE]
> Pour TracingFlags et TCPFlags, vous pouvez spécifier plusieurs protocoles en additionnant les valeurs numériques des protocoles à capturer (identique à l’opérateur logique OR). Par exemple, si vous souhaitez capturer uniquement des paquets ESP et OPVN, spécifiez une valeur TracingFlag de 8 + 1 = 9.  

| Paramètre | Description | Valeurs par défaut | Valeurs disponibles|
|--- |--- | --- | ---|
| TracingFlags | Entier qui détermine les types de paquets à capturer | 11 (ESP, IKE, OVPN) | ESP = 1 IKE = 2 OPVN = 8 |
| TCPFlags | Entier qui détermine les types de paquets TCP à capturer | 0 (aucun) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16,URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|Taille maximale, en octets, d’un paquet capturé. Les paquets sont tronqués s’ils dépassent la valeur fournie. |120|Quelconque|
| MaxFileSize |Taille maximale du fichier de capture en Mo. Les captures sont stockées dans une mémoire tampon circulaire de sorte que le dépassement de capacité est géré d’une manière de type FIFO (les anciens paquets sont supprimés en premier)|100|Quelconque|
|SourceSubnets|Les paquets des plages CIDR spécifiées sont capturés. Spécifié sous forme de tableau.|[] (toutes les adresses IPv4)|Tableau de sous-réseaux IPV4 séparés par des virgules|
| DestinationSubnets |Les paquets destinés aux plages CIDR spécifiées sont capturés. Spécifié sous forme de tableau. |[] (toutes les adresses IPv4)|Tableau de sous-réseaux IPV4 séparés par des virgules|
|SourcePort |Les paquets dont la source est dans les plages spécifiées sont capturés. Spécifié sous forme de tableau.|[] (tous les ports)|Tableau de ports séparés par des virgules|
|DestinationPort |Les paquets dont la destination est dans les plages spécifiées sont capturés. Spécifié sous forme de tableau.|[] (tous les ports)|Tableau de ports séparés par des virgules|
| CaptureSingleDirectionTrafficOnly |Si la valeur est true, une seule direction d’un fluide bidirectionnel s’affiche dans la capture de paquets. Cela permet de capturer toutes les combinaisons possibles d’IP et de ports.|Vrai|True, False|
|Protocole|Tableau d’entiers qui correspondent aux protocoles IANA. |[] (tous les protocoles)| Tous les protocoles trouvés [ici](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) |

L’exemple suivant montre une capture de paquets à l’aide d’une chaîne de filtrage. Vous pouvez changer les paramètres en fonction de vos besoins.

```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -SasUrl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -FilterData $filter
```

## <a name="stop-a-packet-capture"></a><a name="stop"></a> Arrêter une capture de paquets

Nous vous recommandons de laisser la capture de paquets s’exécuter pendant au moins 600 secondes avant de s’arrêter. Lorsque vous arrêtez une capture de paquets, les paramètres sont similaires aux paramètres de la section [Démarrer une capture de paquets](#start). Dans la commande, l’URL SAS a été générée dans la section [Créer un compte de stockage](#storage). Si le paramètre `SasUrl` n’est pas configuré correctement, la capture peut échouer avec des erreurs de stockage.

Lorsque vous êtes prêt à arrêter la capture de paquets, exécutez la commande suivante :

```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sasurl
```

## <a name="view-a-packet-capture"></a><a name="view"></a> Examiner une capture de paquets

Cette section vous aide à télécharger le fichier PCAP de capture de paquets à afficher.

1. Dans le Portail Azure, accédez au compte de stockage que vous avez créé.
1. Cliquez sur **Conteneurs** pour afficher les conteneur du compte de stockage.
1. Cliquez sur le conteneur que vous avez créé.
1. Parcourez la structure de dossiers pour localiser votre fichier PCAP. Le nom et la structure du dossier sont basés sur la date et l’heure UTC. Lorsque vous avez localisé le fichier PCAP, cliquez sur **Télécharger**.

   :::image type="content" source="./media/packet-capture-site-to-site-powershell/download.png" alt-text="Graphique illustrant comment télécharger un fichier." lightbox="./media/packet-capture-site-to-site-powershell/download-expand.png":::
1. Les fichiers de données de capture de paquets sont générés au format PCAP. Vous pouvez utiliser Wireshark ou une autre application couramment disponible pour ouvrir les fichiers PCAP.

## <a name="next-steps"></a>Étapes suivantes

Ensuite, pour plus d’informations sur Virtual WAN, consultez la page [FAQ sur Virtual WAN](virtual-wan-faq.md).
