---
title: Configurer un réseau virtuel pour l’injection standard du runtime d’intégration Azure-SSIS
description: Apprenez comment configurer un réseau virtuel pour l’injection standard du runtime d’intégration Azure-SSIS.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ee88df19385041de660da311fe51c66099d15cc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096709"
---
# <a name="standard-virtual-network-injection-method"></a>Méthode d’injection de réseau virtuel standard

Lorsque vous utilisez SQL Server Integration Services (SSIS) dans Azure Data Factory (ADF), il existe deux méthodes pour joindre votre runtime d’intégration Azure-SSIS (IR) à un réseau virtuel : standard et express. Si vous utilisez la méthode standard, vous devez configurer votre réseau virtuel pour qu’il réponde aux exigences suivantes :

- Assurez-vous que *Microsoft. batch* est un fournisseur de ressources inscrit dans un abonnement Azure qui possède le réseau virtuel auquel votre Azure-SSIS IR se joint. Pour obtenir des instructions détaillées, consultez la section [Inscrire Azure Batch en tant que fournisseur de ressources](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

- Assurez-vous que l’utilisateur qui crée Azure-SSIS IR dispose des autorisations de contrôle d’accès en fonction du rôle (RBAC) nécessaires pour rejoindre le réseau virtuel/sous-réseau.  Pour plus d’informations, consultez la section [Sélectionner des autorisations de réseau virtuel](#perms) ci-dessous.

- Sélectionnez un sous-réseau approprié dans le réseau virtuel auquel votre Azure-SSIS IR se joint. Pour plus d'informations, consultez la section [Sélectionner un sous-réseau](#subnet) ci-dessous.

Selon votre scénario spécifique, vous pouvez éventuellement configurer les éléments suivants :

- Si vous souhaitez apporter vos propres adresses IP publiques statiques (BYOIP) pour le trafic sortant de votre Azure-SSIS IR, consultez la section [configurer des adresses IP publiques statiques](#ip) ci-dessous.

- Si vous souhaitez utiliser votre propre serveur DNS (Domain Name System) dans le réseau virtuel, consultez la section [configurer un serveur DNS personnalisé](#dns) ci-dessous.

- Si vous souhaitez utiliser un groupe de sécurité réseau (NSG) pour limiter le trafic entrant/sortant sur le sous-réseau, consultez la section [Configurer un NSG](#nsg) ci-dessous.

- Si vous souhaitez utiliser des itinéraires définis par l’utilisateur (UDR) pour auditer/inspecter le trafic sortant, consultez la section [Configurer les itinéraires définis par l’utilisateur](#udr) ci-dessous.

- Vérifiez que le groupe de ressources du réseau virtuel (ou le groupe de ressources des adresses IP publiques si vous apportez vos propres adresses IP publiques) peut créer et supprimer certaines ressources réseau Azure. Pour plus d’informations, consultez [Configurer le groupe de ressources approprié](#rg). 

- Si vous personnalisez votre runtime Azure-SSIS IR conformément à l’article [Personnalisation de la configuration d’Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md), votre processus interne de gestion de ses nœuds consommera des adresse IP privées à partir de la plage prédéfinie *172.16.0.0* à *172.31.255.255*. Vérifiez que les plages d’adresses IP privées de vos réseaux virtuels ou locaux n’entrent pas en conflit avec cette plage.

Ce diagramme montre les connexions requises pour votre runtime d’intégration Azure-SSIS :

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="Diagramme montrant les connexions requises pour votre runtime d’intégration Azure-SSIS.":::

## <a name="select-virtual-network-permissions"></a><a name="perms"></a>Sélectionner des autorisations de réseau virtuel

Pour activer l’injection de réseau virtuel standard, l’utilisateur qui crée Azure-SSIS IR doit disposer des autorisations RBAC nécessaires pour joindre le réseau virtuel/sous-réseau.

- Si vous associez votre Azure-SSIS IR à un réseau virtuel Azure Resource Manager, vous avez deux options :

  - Utilisez le rôle *Contributeur de réseaux* intégré. Ce rôle inclut l’autorisation _Microsoft.Network/\*_ , mais dispose d’une étendue plus large que nécessaire.

  - Créez un rôle personnalisé qui inclut uniquement l’autorisation _Microsoft.Network/virtualNetworks/\*/join/action_ nécessaire. Si vous voulez également apporter vos propres adresses IP publiques pour Azure-SSIS IR tout en le joignant à un réseau virtuel Azure Resource Manager, incluez également l’autorisation _Microsoft.Network/publicIPAddresses/\*/join/action_ dans le rôle.

  - Pour obtenir des instructions détaillées, consultez la section [Accorder des autorisations de réseau virtuel](azure-ssis-integration-runtime-virtual-network-configuration.md#grantperms).

- Si vous associez votre Azure-SSIS IR à un réseau virtuel classique, nous vous recommandons d’utiliser le rôle intégré *Contributeur de machines virtuelles classiques*. Sinon, vous devez créer un rôle personnalisé qui inclut l’autorisation de rejoindre le réseau virtuel. Vous devez également affecter *MicrosoftAzureBatch* à ce rôle intégré/personnalisé.

## <a name="select-a-subnet"></a><a name="subnet"></a>Sélectionner un sous-réseau

Pour activer l’injection de réseau virtuel standard, vous devez sélectionner un sous-réseau approprié à joindre à votre Azure-SSIS IR :

- Ne sélectionnez pas le GatewaySubnet, car il est dédié aux passerelles réseau virtuel.

- Assurez-vous que le sous-réseau sélectionné a des adresses IP disponibles pendant au moins deux fois votre numéro de nœud Azure-SSIS IR. Celles-ci sont nécessaires pour éviter toute interruption lors du déploiement de correctifs/mises à niveau pour votre Azure-SSIS IR. Azure réserve aussi des adresses IP qui ne peuvent pas être utilisées dans chaque sous-réseau. La première et la dernière adresses IP sont réservées à la conformité des protocoles, tandis que trois adresses supplémentaires sont réservées pour les services Azure. Pour plus d’informations, consultez la section [Restrictions d’adresses IP de sous-réseau](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

- N'utilisez pas un sous-réseau qui est exclusivement occupé par d'autres services Azure (par exemple, Azure SQL Managed Instance, App Service, etc.). 

## <a name="configure-static-public-ip-addresses"></a><a name="ip"></a>Configurer les adresses IP publiques statiques

Si vous souhaitez apporter vos propres adresses IP publiques statiques au trafic sortant d’Azure-SSIS IR tout en les joignant à un réseau virtuel, vous pouvez les autoriser sur vos pare-feux et vous assurer qu’ils répondent aux exigences suivantes :

- Vous devez en fournir exactement deux non utilisées qui ne soient pas déjà associées à d’autres ressources Azure. L’adresse supplémentaire est utilisée lors de la mise à niveau régulière de votre Azure-SSIS IR. Notez qu’une adresse IP publique ne peut pas être partagée parmi vos instances Azure-SSIS IR actives.

- Il doit s’agir de deux adresses statiques de type standard. Pour plus d’informations, reportez-vous à [SKU d’adresse IP publique](../virtual-network/ip-services/public-ip-addresses.md#sku).

- Toutes deux doivent avoir un nom DNS. Si vous n’avez pas fourni de nom DNS lors de leur création, vous pouvez le faire sur le portail Azure.

  :::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Runtime d’intégration Azure SSIS":::

- Les adresses IP et le réseau virtuel doivent se trouver dans le même abonnement et dans la même région.

## <a name="configure-a-custom-dns-server"></a><a name="dns"></a>Configurer un serveur DNS personnalisé 

Si vous souhaitez utiliser votre propre serveur DNS dans le réseau virtuel pour résoudre vos noms d’hôte privés, assurez-vous qu’il peut également résoudre les noms d’hôte Azure globaux (par exemple, votre objet Stockage Blob Azure nommé `<your storage account>.blob.core.windows`.).

Nous vous recommandons de configurer votre propre serveur DNS pour transférer les requêtes DNS non résolues à l’adresse IP des programmes de résolution récursifs Azure (*168.63.129.16*).

Pour plus d'informations, voir la section [Résolution du nom de serveur DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!NOTE]
> Utilisez un nom de domaine complet (FQDN) pour votre nom d’hôte privé (par exemple, utilisez `<your_private_server>.contoso.com` au lieu de `<your_private_server>`). Vous pouvez également utiliser une installation personnalisée standard dans Azure-SSIS IR pour ajouter automatiquement votre propre suffixe DNS (par exemple `contoso.com`) à un nom de domaine à étiquette unique non qualifié, et le convertir en nom de domaine complet avant de l’utiliser dans des requêtes DNS. Pour plus d’informations, consultez la section [Exemples d’installation personnalisée standard](how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

## <a name="configure-an-nsg"></a><a name="nsg"></a>Configurer un NSG

Si vous souhaitez utiliser un NSG sur le sous-réseau joint par votre Azure-SSIS IR, autorisez le trafic entrant et sortant suivant :

| Sens | Protocole de transfert | Source | Ports source | Destination | Ports de destination | Commentaires | 
|-----------|--------------------|--------|--------------|-------------|-------------------|----------| 
| Trafic entrant | TCP | *BatchNodeManagement* | * | *VirtualNetwork* | *29876, 29877* (si vous joignez votre runtime d’intégration SSIS à un réseau virtuel Azure Resource Manager)<br/><br/>*10100, 20100, 30100* (si vous joignez votre runtime d’intégration SSIS à un réseau virtuel classique)| Le service Data Factory utilise ces ports pour communiquer avec vos nœuds Azure-SSIS IR sur le réseau virtuel.<br/><br/>Que vous créez ou non un NSG sur le sous-réseau, Data Factory configure toujours un NSG sur la carte d’interface réseau (NIC) attachée aux machines virtuelles qui hébergent votre Azure-SSIS IR.<br/><br/>Cet NSG au niveau de la carte réseau n’autorise que le trafic entrant provenant d’adresses IP Data Factory sur les ports spécifiés.<br/><br/>Même si vous ouvrez ces ports pour le trafic Internet au niveau du sous-réseau, le trafic provenant d’adresses IP qui ne sont pas des adresses IP Data Factory est toujours bloqué au niveau de la carte réseau. | 
| Trafic entrant | TCP | *CorpNetSaw* | * | *VirtualNetwork* | *3389* | (Facultatif) Uniquement obligatoire lorsqu’un ingénieur de support technique Microsoft vous demande d’ouvrir le port *3389* pour le dépannage avancé qui peut être fermé juste après le dépannage.<br/><br/>La balise de service *CorpNetSaw* autorise uniquement les machines de station de travail à accès sécurisé (SAW) du réseau d’entreprise Microsoft à accéder à votre Azure-SSIS IR via le protocole RDP (Remote Desktop Protocol).<br/><br/>Cette balise de service ne peut pas être sélectionnée à partir du portail Azure et n’est disponible que via Azure PowerShell/CLI.<br/><br/>Dans le NSG au niveau de la carte réseau, le port *3389* est ouvert par défaut, mais vous pouvez le contrôler à l’aide d’un NSG au niveau du sous-réseau, tandis que le trafic sortant sur celui-ci n’est pas autorisé par défaut sur vos nœuds Azure-SSIS IR à l’aide de la règle de pare-feu Windows. | 

| Sens | Protocole de transfert | Source | Ports source | Destination | Ports de destination | Commentaires |
|-----------|--------------------|--------|--------------|-------------|-------------------|----------|
| Règle de trafic sortant | TCP | *VirtualNetwork* | * | *AzureCloud* | *443* | Nécessaire à votre Azure-SSIS IR pour accéder aux services azure, tels que le Stockage Azure et les Azure Event Hubs. | 
| Règle de trafic sortant | TCP | *VirtualNetwork* | * | *Internet* | *80* | (Facultatif) Votre Azure-SSIS IR utilise ce port pour télécharger une liste de révocation de certificats à partir d’Internet.<br/><br/>Si vous bloquez ce trafic, vous risquez de subir une dégradation des performances lors du démarrage de votre Azure-SSIS IR et de perdre la possibilité de vérifier les listes de révocation de certificats lors de l’utilisation de certificats, ce qui n’est pas recommandé du point de vue de la sécurité.<br/><br/>Si vous souhaitez limiter les destinations à certains noms de domaine complets, consultez la section **Configurer les itinéraires définis par l’utilisateur** ci-dessous. | 
| Règle de trafic sortant | TCP | *VirtualNetwork* | * | *Sql/VirtualNetwork* | *1433, 11000-11999* | (Facultatif) Obligatoire uniquement si vous utilisez un serveur Azure SQL Database/Managed Instance pour héberger le catalogue SSIS (SSISDB).<br/><br/>Si votre serveur Azure SQL Database/Managed Instance est configuré avec un point de terminaison public/de service de réseau virtuel, utilisez la balise de service *SQL* comme destination.<br/><br/>Si votre serveur Azure SQL Database/Managed Instance est configuré avec un point de terminaison public, utilisez la balise de service *VirtualNetwork* comme destination.<br/><br/>Si votre stratégie de connexion de serveur est définie sur *Proxy* au lieu de *Rediriger*, seul le port *1433* est nécessaire. | 
| Règle de trafic sortant | TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *443* | (Facultatif) Obligatoire uniquement si vous utilisez un conteneur d’objet Blob de stockage Azure pour stocker vos fichiers/scripts d’installation personnalisée standard.<br/><br/>Si votre stockage Azure est configuré avec un point de terminaison public/de service de réseau virtuel, utilisez la balise de service *Stockage* comme destination.<br/><br/>Si votre stockage Azure est configuré avec un point de terminaison public, utilisez la balise de service *VirtualNetwork* comme destination. | 
| Règle de trafic sortant | TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *445* | (Facultatif) Obligatoire uniquement si vous devez accéder à Azure Files.<br/><br/>Si votre stockage Azure est configuré avec un point de terminaison public/de service de réseau virtuel, utilisez la balise de service *Stockage* comme destination.<br/><br/>Si votre stockage Azure est configuré avec un point de terminaison public, utilisez la balise de service *VirtualNetwork* comme destination. | 

## <a name="configure-udrs"></a><a name="udr"></a>Configurer les itinéraires définis par l’utilisateur

Si vous voulez auditer/inspecter le trafic sortant à partir de votre Azure-SSIS IR, vous pouvez utiliser des [itinéraires définis par l’utilisateur (UDR)](../virtual-network/virtual-networks-udr-overview.md) pour le rediriger vers une appliance de pare-feu locale via le tunneling forcé d'[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) qui publie un itinéraire BGP (Border Gateway Protocol) *0.0.0.0/0* vers le réseau virtuel, vers une appliance virtuelle réseau (appliance virtuelle réseau) configurée en tant que pare-feu ou service de [pare-feu Azure](../firewall/overview.md).

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="Scénario d’appliance virtuelle réseau pour Azure-SSIS IR":::

Pour le faire fonctionner, vous devez vérifier les éléments suivants :

- Le trafic entre le service de gestion Azure Batch et votre Azure-SSIS IR ne doit pas être acheminé vers un service ou une appliance de pare-feu.

- L’appliance/le service de pare-feu autorise le trafic sortant requis par Azure-SSIS IR.

Si le trafic entre le service de gestion Azure Batch et votre Azure-SSIS IR est acheminé vers un service ou une appliance de pare-feu, il est interrompu en raison d’un routage asymétrique. Les itinéraires définis par l’utilisateur doivent être définis pour ce trafic, de telle sorte qu’il puisse traverser les mêmes itinéraires par lesquels il est entré. Vous pouvez configurer des itinéraires définis par l’utilisateur pour acheminer le trafic entre le service de gestion Azure Batch et votre Azure-SSIS IR avec un type de tronçon suivant tel qu’*Internet*.

Par exemple, si votre Azure-SSIS IR se trouve dans la région *Royaume-Uni Sud* et que vous souhaitez inspecter le trafic sortant utilisant le Pare-feu Azure, vous devez commencer par obtenir les plages d’adresses IP pour l’étiquette de service *BatchNodeManagement.UKSouth* à partir du [lien de téléchargement de plage d’adresses IP des balises de service](https://www.microsoft.com/download/details.aspx?id=56519) ou l’[API de découverte d’étiquette de service](../virtual-network/service-tags-overview.md#service-tags-on-premises). Vous pouvez ensuite configurer les itinéraires définis par l’utilisateur suivants des itinéraires d’adresses IP pertinents avec le type de tronçon suivant *Internet* et l’itinéraire *0.0.0.0/0* avec le type de tronçon suivant *Appliance virtuelle*.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Paramètres des UDR Azure Batch":::

> [!NOTE]
> Cette approche implique des coûts de maintenance supplémentaires, puisque vous devez vérifier régulièrement les plages d’adresses IP pertinentes et ajouter les itinéraires définis par l’utilisateur pour les nouvelles, afin d’éviter de rompre votre Azure-SSIS IR. Nous vous recommandons de les vérifier tous les mois, car lorsqu’une nouvelle plage d’adresses IP s’affiche pour l’étiquette de service pertinente, un mois supplémentaire est nécessaire pour être pris en compte. 

Vous pouvez exécuter le script PowerShell suivant pour ajouter les itinéraires définis par l’utilisateur pour le service de gestion Azure Batch :

```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your route table]"
$RouteTableResourceName = "[resource name of your route table]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start adding UDRs to your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch Azure service tag, please confirm that your location is valid."
}
```

À la suite de nos conseils dans la section [Configurer un groupe de sécurité réseau](#nsg) ci-dessus, vous devez implémenter des règles similaires sur le service/l’appliance de pare-feu pour autoriser le trafic sortant à partir de votre Azure-SSIS IR :

- Si vous utilisez le pare-feu Azure :
  - Vous devez ouvrir le port *443* pour le trafic TCP sortant avec l’étiquette de service *AzureCloud* en tant que destination.

  - Si vous utilisez un serveur Azure SQL Database/Managed Instance pour héberger SSISDB, vous devez ouvrir les ports *1433, 11000-11999* pour le trafic TCP sortant avec l’étiquette de service *Sql/VirtualNetwork* comme destination.

  - Si vous utilisez un conteneur d’objet blob de stockage Azure pour stocker votre script/vos fichiers d’installation personnalisée standard, vous devez ouvrir le port *443* pour le trafic TCP sortant avec l’étiquette de service *Storage/VirtualNetwork* en tant que destination.

  - Si vous avez besoin d’accéder à Azure Files, vous devez ouvrir le port *445* pour le trafic TCP sortant avec l’étiquette de service *Storage/VirtualNetwork* en tant que destination.

- Si vous utilisez un autre service/une appliance de pare-feu :
  - Vous devez ouvrir le port *443* pour le trafic TCP sortant avec *0.0.0.0/0* ou les noms de domaine complets (FQDN) spécifiques à l’environnement Azure en tant que destination.

    | Environnement Azure | FQDN |
    |-------------------|-------|
    | <b>Azure public</b> | <ul><li><b>Azure Data Factory (gestion)</b><ul><li>_\*.frontend.clouddatahub.net_</li></ul></li><li><b>Stockage Azure (gestion)</b><ul><li>_\*.blob.core.windows.net_</li><li>_\*.table.core.windows.net_</li></ul></li><li><b>Azure Container Registry (installation personnalisée)</b><ul><li>_\*.azurecr.io_</li></ul></li><li><b>Event Hubs (journalisation)</b><ul><li>_\*.servicebus.windows.net_</li></ul></li><li><b>Service de journalisation Microsoft (utilisation interne)</b><ul><li>_gcs.prod.monitoring.core.windows.net_</li><li>_prod.warmpath.msftcloudes.com_</li><li>_azurewatsonanalysis-prod.core.windows.net_</li></ul></li></ul> |
    | <b>Azure Government</b> | <ul><li><b>Azure Data Factory (gestion)</b><ul><li>_\*.frontend.datamovement.azure.us_</li></ul></li><li><b>Stockage Azure (gestion)</b><ul><li>_\*.blob.core.usgovcloudapi.net_</li><li>_\*.table.core.usgovcloudapi.net_</li></ul></li><li><b>Azure Container Registry (installation personnalisée)</b><ul><li>_\*.azurecr.us_</li></ul></li><li><b>Event Hubs (journalisation)</b><ul><li>_\*.servicebus.usgovcloudapi.net_</li></ul></li><li><b>Service de journalisation Microsoft (utilisation interne)</b><ul><li>_fairfax.warmpath.usgovcloudapi.net_</li><li>_azurewatsonanalysis.usgovcloudapp.net_</li></ul></li></ul> |
    | <b>Azure China 21Vianet</b> | <ul><li><b>Azure Data Factory (gestion)</b><ul><li>_\*.frontend.datamovement.azure.cn_</li></ul></li><li><b>Stockage Azure (gestion)</b><ul><li>_\*.blob.core.chinacloudapi.cn_</li><li>_\*.table.core.chinacloudapi.cn_</li></ul></li><li><b>Azure Container Registry (installation personnalisée)</b><ul><li>_\*.azurecr.cn_</li></ul></li><li><b>Event Hubs (journalisation)</b><ul><li>_\*.servicebus.chinacloudapi.cn_</li></ul></li><li><b>Service de journalisation Microsoft (utilisation interne)</b><ul><li>_mooncake.warmpath.chinacloudapi.cn_</li><li>_azurewatsonanalysis.chinacloudapp.cn_</li></ul></li></ul> |

  - Si vous utilisez un serveur Azure SQL Database/Managed Instance pour héberger SSISDB, vous devez ouvrir les ports *1433, 11000-11999* pour le trafic TCP sortant avec *0.0.0.0/0* ou votre nom de domaine complet de serveur Azure SQL Database/Managed Instance comme destination.

  - Si vous utilisez un conteneur d’objet blob de stockage Azure pour stocker votre script/vos fichiers d’installation personnalisée standard, vous devez ouvrir le port *443* pour le trafic TCP sortant avec *0.0.0.0/0* ou votre nom de domaine complet de stockage Blob Azure en tant que destination.

  - Si vous avez besoin d’accéder à Azure Files, vous devez ouvrir le port *445* pour le trafic TCP sortant avec *0.0.0.0/0* ou votre nom de domaine complet Azure Files en tant que destination.

- Si vous configurez un point de terminaison de service de réseau virtuel pour le stockage Azure/le registre de conteneurs/Event Hubs/SQL en activant les ressources *Microsoft.Storage*/*Microsoft.ContainerRegistry*/*Microsoft.EventHub*/*Microsoft.Sql*, respectivement, dans votre sous-réseau, tout le trafic entre votre Azure-SSIS IR et ces services dans les mêmes régions/régions jumelées est acheminé vers le réseau principal Azure au lieu de votre service/appliance de pare-feu.

- Vous devez ouvrir le port *80* pour le trafic TCP sortant avec les sites de téléchargement de la liste de révocation de certificats (CRL) suivants comme destination :

  - *crl.microsoft.com:80*
  - *mscrl.microsoft.com:80*
  - *crl3.digicert.com:80*
  - *crl4.digicert.com:80*
  - *ocsp.digicert.com:80*
  - *cacerts.digicert.com:80*
  
  Si vous utilisez des certificats avec différentes listes de révocation de certificats, vous devez également ajouter leurs sites de téléchargement en tant que destination. Pour plus d’informations, consultez l’article [Liste de révocation de certificats](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

  Si vous bloquez ce trafic, vous risquez de subir une dégradation des performances lors du démarrage de votre Azure-SSIS IR et de perdre la possibilité de vérifier les listes de révocation de certificats lors de l’utilisation de certificats, ce qui n’est pas recommandé du point de vue de la sécurité.

Si vous n’avez pas besoin d’auditer/inspecter le trafic sortant à partir de votre Azure-SSIS IR, vous pouvez utiliser les itinéraires définis par l’utilisateur pour forcer tout le trafic avec le type de tronçon suivant comme *Internet* :

- Lorsque vous utilisez Azure ExpressRoute, vous pouvez configurer un itinéraire défini par l’utilisateur pour l’itinéraire *0.0.0.0/0* dans votre sous-réseau avec le type de tronçon suivant *Internet*. 

- Lorsque vous utilisez un appliance virtuelle réseau, vous pouvez modifier l’itinéraire défini par l’utilisateur existant pour l’itinéraire *0.0.0.0/0* dans votre sous-réseau afin de faire passer le type de tronçon suivant de *Appliance virtuelle* à *Internet*.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="Ajouter un itinéraire":::

> [!NOTE]
> La configuration des itinéraires définis par l’utilisateur avec le type de tronçon suivant *Internet* ne signifie pas que tout le trafic passe par Internet. Tant que l’adresse de destination appartient à l’un des services Azure, Azure achemine tout le trafic vers cette adresse sur le réseau principal Azure au lieu d’Internet.

## <a name="configure-the-relevant-resource-group"></a><a name="rg"></a>Configurer le groupe de ressources approprié

Pour activer l’injection de réseau virtuel standard, votre Azure-SSIS IR doit créer certaines ressources réseau dans le même groupe de ressources que le réseau virtuel. Ces ressources incluent :

- Un équilibreur de charge Azure, avec le nom _\<Guid\>-azurebatch-cloudserviceloadbalancer_.
- Une adresse IP publique Azure, avec le nom _\<Guid\>-azurebatch-cloudservicepublicip_.
- Un groupe de sécurité réseau, dont le nom est _\<Guid\>-azurebatch-cloudservicenetworksecuritygroup_. 

> [!NOTE]
> Vous pouvez maintenant apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR. Dans ce scénario, nous allons créer l’équilibreur de charge Azure et le groupe de sécurité réseau sous le même groupe de ressources que vos adresses IP publiques statiques au lieu du réseau virtuel.

Ces ressources sont créées au démarrage de votre Azure-SSIS IR. Elles sont supprimées lorsqu’il est arrêté. Si vous apportez vos propres adresses IP publiques statiques pour Azure-SSIS IR, elles ne seront pas supprimées lors de l’arrêt de votre Azure-SSIS IR. Pour éviter de bloquer l’arrêt de votre Azure-SSIS IR, ne réutilisez pas ces ressources pour d’autres objectifs.

Vérifiez que vous n’avez pas de verrou sur le groupe de ressources ou l’abonnement auquel appartiennent le réseau virtuel ou vos adresses IP publiques statiques. Si vous configurez un verrou en lecture seule ou de suppression, le démarrage et l’arrêt du runtime d’intégration Azure-SSIS IR échoueront ou celui-ci ne répondra plus.

Vérifiez qu’aucune affectation Azure Policy n’empêche la création des ressources suivantes dans le groupe de ressources ou l’abonnement auquel appartiennent le réseau virtuel ou vos adresses IP publiques statiques : 

- *Microsoft.Network/LoadBalancers* 
- *Microsoft.Network/NetworkSecurityGroups* 
- *Microsoft.Network/PublicIPAddresses* 

Assurez-vous que le quota de ressources pour votre abonnement est suffisant pour ces ressources. Plus précisément, pour chaque Azure-SSIS IR créé dans un réseau virtuel, vous devez réserver deux fois le nombre de ces ressources, car les ressources supplémentaires seront utilisées lors de la mise à niveau périodique de vos Azure-SSIS IR.

## <a name="faq"></a><a name="faq"></a>Forum Aux Questions

- Comment protéger l’adresse IP publique exposée sur mon runtime d’intégration Azure-SSIS IR pour la connexion entrante ? Est-il possible de supprimer une adresse IP publique ?
 
  À l’heure actuelle, une adresse IP publique est créée automatiquement lorsque votre runtime d’intégration Azure-SSIS IR rejoint un réseau virtuel. Nous disposons d’un groupe de sécurité réseau au niveau de la carte réseau pour autoriser uniquement le service de gestion Azure Batch à établir une connexion entrante à votre Azure-SSIS IR. Vous pouvez également spécifier le groupe de sécurité réseau au niveau du sous-réseau pour la protection entrante.

  Si vous ne souhaitez pas que les adresses IP publiques soient exposées, vous pouvez envisager de [configurer un runtime d’intégration auto-hébergé en tant que proxy pour votre Azure-SSIS IR](self-hosted-integration-runtime-proxy-ssis.md) plutôt que de joindre celui-ci à un réseau virtuel.
 
- Puis-je ajouter l’IP publique de mon runtime d’intégration Azure-SSIS à la liste d’autorisation du pare-feu pour mes sources de données ?

  Vous pouvez maintenant apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR. Dans ce cas, vous pouvez ajouter vos adresses IP à la liste d’autorisation du pare-feu pour vos sources de données. Sinon, vous pouvez également envisager les options suivantes pour sécuriser l’accès aux données à partir de votre Azure-SSIS IR en fonction de votre scénario :

  - Si votre source de données est locale, une fois que vous avez connecté un réseau virtuel à votre réseau local et joint votre runtime d’intégration Azure-SSIS à ce sous-réseau de réseau virtuel, vous pouvez ajouter la plage d’adresses IP privées de ce sous-réseau à la liste d’autorisation du pare-feu pour votre source de données.

  - Si votre source de données est un service Azure qui prend en charge les points de terminaison de service de réseau virtuel, vous pouvez configurer un point de terminaison de service de réseau virtuel dans votre sous-réseau de réseau virtuel et joindre votre Azure-SSIS IR à ce sous-réseau. Vous pouvez ensuite ajouter une règle de réseau virtuel avec ce sous-réseau au pare-feu pour votre source de données.

  - Si votre source de données est un service cloud autre qu’Azure, vous pouvez utiliser un itinéraire défini par l’utilisateur pour acheminer le trafic sortant de votre Azure-SSIS IR vers une adresse IP publique statique via une appliance virtuelle réseau/un pare-feu Azure. Vous pouvez ensuite ajouter l’IP publique statique de votre appliance virtuelle réseau/Pare-feu Azure à la liste d’autorisation du pare-feu pour votre source de données.

  - Si aucune de ces options ne correspond à vos besoins, vous pouvez envisager de [configurer un IR auto-hébergé en tant que proxy pour votre Azure-SSIS IR](self-hosted-integration-runtime-proxy-ssis.md). Vous pouvez ensuite ajouter l’IP publique statique de l’ordinateur qui héberge votre IR auto-hébergé à la liste d’autorisation du pare-feu pour votre source de données.

- Pourquoi dois-je fournir deux adresses publiques statiques si je veux apporter mes propres adresses IP publiques pour Azure-SSIS IR ?

  Azure-SSIS IR est automatiquement mis à jour de façon régulière. De nouveaux nœuds sont créés lors de la mise à niveau et les anciens nœuds sont supprimés. Toutefois, pour éviter un temps d’arrêt, les anciens nœuds ne sont pas supprimés tant que les nouveaux ne sont pas prêts. Ainsi, votre première adresse IP publique statique utilisée par les anciens nœuds ne peut pas être libérée immédiatement et nous avons donc besoin de votre deuxième adresse IP publique statique pour créer les nouveaux nœuds.

- J’ai apporté mes propres adresses IP publiques statiques pour Azure-SSIS IR. Pourquoi le runtime d’intégration ne peut-il toujours pas accéder à mes sources de données ?

  Veuillez vérifier que les deux IP publiques statiques sont ajoutées à la liste d’autorisation du pare-feu pour vos sources de données. À chaque mise à niveau de votre Azure-SSIS IR, son adresse IP publique statique alterne entre les deux adresses que vous avez fournies. Si vous n’en ajoutez qu’une seule à la liste d’autorisation, l’accès aux données pour votre runtime d’intégration Azure-SSIS sera interrompu après sa mise à niveau.

  Si votre source de données est un service Azure, vérifiez que vous l’avez configurée avec des points de terminaison de service de réseau virtuel. Le cas échéant, le trafic d’Azure-SSIS IR vers votre source de données permutera pour utiliser les adresses IP privées gérées par les services Azure, et l’ajout de vos propres IP publiques statiques à la liste d’autorisation du pare-feu pour votre source de données ne prendra pas effet.

## <a name="next-steps"></a>Étapes suivantes

- [Joindre Azure-SSIS IR à un réseau virtuel via une interface utilisateur ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Joindre le runtime d’intégration Azure-SSIS IR à un réseau virtuel via Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 

- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise un serveur Microsoft Azure SQL Database pour héberger SSISDB. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il fournit des instructions sur l’utilisation d’un serveur Microsoft Azure SQL Database configuré avec un point de terminaison de service de réseau virtuel, une règle de pare-feu IP ou un point de terminaison privé, ou Azure SQL Managed Instance qui joint un réseau virtuel pour héberger SSISDB. Il explique comment joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
