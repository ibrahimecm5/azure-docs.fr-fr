---
title: Configurer la résolution de noms DNS pour les points de terminaison privés
description: Cet article donne un aperçu de la façon dont vous pouvez utiliser un point de terminaison privé pour votre compte Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/18/2021
ms.openlocfilehash: e97ed074cd9c0fee7a2fc3b3c7795d205292da28
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254151"
---
# <a name="configure-and-verify-dns-name-resolution-for-azure-purview-private-endpoints"></a>Configurer et vérifier la résolution de noms DNS pour les points de terminaison privés Azure Purview

> [!IMPORTANT]
> Si vous avez créé un point de terminaison privé de _portail_ pour votre compte Purview **avant le 27 septembre 2021 à 15h30 UTC**, vous devez effectuer les actions requises détaillées dans [Reconfigurer DNS pour les points de terminaison privés du portail](./catalog-private-link.md#reconfigure-dns-for-portal-private-endpoints). **Ces actions doivent être effectuées avant le 12 novembre 2021. Sinon, les points de terminaison privés existants du portail cesseront de fonctionner**.

## <a name="conceptual-overview"></a>Vue d'ensemble conceptuelle
Une résolution de noms précise est une condition essentielle lors de la configuration de points de terminaison privés pour vos comptes Azure Purview. 

Il se peut que vous deviez activer la résolution de noms interne dans vos paramètres DNS pour résoudre les adresses IP des points de terminaison privés en nom de domaine complet (FQDN) des sources de données et de votre machine de gestion vers le compte Azure Purview et le runtime d’intégration auto-hébergée, selon les scénarios que vous déployez.

L’exemple suivant montre la résolution de noms DNS Azure Purview à l’extérieur du réseau virtuel ou lorsqu’un point de terminaison privé Azure n’est pas configuré.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="Capture d’écran montrant la résolution de noms Azure Purview à l’extérieur de CorpNet.":::

L’exemple suivant illustre la résolution de noms DNS Azure Purview à partir du réseau virtuel.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="Capture d’écran montrant la résolution de noms Purview à partir de CorpNet.":::

## <a name="deployment-options"></a>Options de déploiement 

Utilisez l’une des options suivantes pour configurer la résolution de noms interne quand vous utilisez des points de terminaison privés pour votre compte Azure Purview :

- [Déployer de nouvelles zones DNS privé Azure](#option-1---deploy-new-azure-private-dns-zones) dans votre environnement Azure dans le cadre du déploiement de points de terminaison privés. (Option par défaut).
- [Utiliser les zones DNS privé Azure existantes](#option-2---use-existing-azure-private-dns-zones). Utilisez cette option si vous utilisez un point de terminaison privé dans un modèle hub-and-spoke à partir d’un autre abonnement ou même dans le même abonnement. 
- [Utiliser vos propres serveurs DNS](#option-3---use-your-own-dns-servers) si vous n’utilisez pas de redirecteurs DNS et que vous gérez les enregistrements A directement dans vos serveurs DNS locaux.

## <a name="option-1---deploy-new-azure-private-dns-zones"></a>Option 1 : Déployer de nouvelles zones DNS privé Azure  

### <a name="deploy-new-azure-private-dns-zones"></a>Déployer de nouvelles zones DNS privées Azure
Pour activer la résolution de noms interne, vous pouvez déployer les zones Azure DNS requises dans votre abonnement Azure où le compte Azure Purview est déployé. 

   :::image type="content" source="media/catalog-private-link/purview-pe-dns-zones.png" alt-text="Capture d’écran montrant des zones DNS.":::

Quand vous créez des points de terminaison privés d’ingestion, de portail et de compte, les enregistrements de ressources CNAME DNS pour Azure Purview sont automatiquement mis à jour en tant qu’alias dans quelques sous-domaines avec le préfixe `privatelink` :

- Par défaut, durant le déploiement du point de terminaison privé de _compte_ pour votre compte Purview, nous créons également une [zone DNS privée](../dns/private-dns-overview.md) qui correspond au sous-domaine `privatelink` d’Azure Purview sous la forme `privatelink.purview.azure.com` et qui comprend notamment des enregistrements de ressources A DNS pour les points de terminaison privés.

- Durant le déploiement du point de terminaison privé de _portail_ pour votre compte Purview, nous créons également une zone DNS privée qui correspond au sous-domaine `privatelink` d’Azure Purview sous la forme `privatelink.purviewstudio.azure.com` et qui comprend notamment des enregistrements de ressources A DNS pour le _web_.

- Si vous activez des points de terminaison privés d’ingestion, des zones DNS supplémentaires sont requises pour les ressources managées. 

Le tableau suivant présente un exemple de zones DNS privé Azure et d’enregistrements A DNS qui sont déployés dans le cadre de la configuration du point de terminaison privé pour un compte Azure Purview si vous activez l’_intégration DNS privé_ pendant le déploiement : 

Point de terminaison privé  |Point de terminaison privé associé à  |Zone DNS (nouveau)  |Enregistrement A (exemple) |
|---------|---------|---------|---------|
|Compte     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
|Portail     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |Web         |
|Ingestion     |Compte de stockage managé Purview – Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|Ingestion   |Compte de stockage managé Purview – File d’attente         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|Ingestion     |Compte de stockage managé Purview – Event Hub         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

### <a name="validate-virtual-network-links-on-azure-private-dns-zones"></a>Valider les liens de réseau virtuel dans les zones DNS privées Azure

Une fois le déploiement du point de terminaison privé effectué, vérifiez qu’il existe un [lien de réseau virtuel](../dns/private-dns-virtual-network-links.md) pour toutes les zones DNS privées Azure correspondantes vers le réseau virtuel Azure où le point de terminaison privé a été déployé.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-link.png" alt-text="Capture d’écran montrant les liens de réseau virtuel dans la zone DNS.":::

Pour plus d’informations, consultez [Configuration DNS des points de terminaison privés Azure](../private-link/private-endpoint-dns.md).

### <a name="verify-internal-name-resolution"></a>Vérifier la résolution de noms interne

Lorsque vous résolvez l’URL du point de terminaison Azure Purview à l’extérieur du réseau virtuel avec le point de terminaison privé, elle correspond au point de terminaison public d’Azure Purview. En cas de résolution à partir du réseau virtuel hébergeant le point de terminaison privé, l’URL du point de terminaison Azure Purview correspond à l’adresse IP du point de terminaison privé.

Par exemple, si le nom d’un compte Azure Purview est « Contoso-Purview », quand il est résolu à l’extérieur du réseau virtuel qui héberge le point de terminaison privé, il devient :

| Name | Type | Valeur |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | Un | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview Studio public endpoint\> |

Les enregistrements de ressources DNS de Contoso-Purview, une fois résolus dans le réseau virtuel qui héberge le point de terminaison privé, deviennent :

| Name | Type | Valeur |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | Un | \<Purview account private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview portal private endpoint IP address\> |

## <a name="option-2---use-existing-azure-private-dns-zones"></a>Option 2 : Utiliser les zones DNS privé Azure existantes

### <a name="use-existing-azure-private-dns-zones"></a>Utiliser des zones DNS privées Azure existantes

Durant le déploiement des points de terminaison privés Azure Purview, vous pouvez choisir _Intégration à DNS privé_ en utilisant des zones DNS privées Azure existantes. C’est généralement le cas pour les organisations où le point de terminaison privé est utilisé pour d’autres services dans Azure. Dans ce cas, lors du déploiement des points de terminaison privés, veillez à sélectionner les zones DNS existantes au lieu d’en créer de nouvelles. 

Ce scénario s’applique également si votre organisation utilise un abonnement central ou hub pour toutes les zones DNS privées Azure.

La liste suivante présente les zones Azure DNS et les enregistrements A requis pour les points de terminaison privés Purview :

> [!NOTE]
> Mettez à jour tous les noms avec `Contoso-Purview`, `scaneastusabcd1234` et `atlas-12345678-1234-1234-abcd-123456789abc` avec le nom des ressources Azure correspondantes dans votre environnement. Par exemple, au lieu de `scaneastusabcd1234`, utilisez le nom de votre compte de stockage managé Azure Purview.

Point de terminaison privé  |Point de terminaison privé associé à  |Zone DNS (existante)  |Enregistrement A (exemple) |
|---------|---------|---------|---------|
|Compte     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
|Portail     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |Web         |
|Ingestion     |Compte de stockage managé Purview – Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|Ingestion   |Compte de stockage managé Purview – File d’attente         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|Ingestion     |Compte de stockage managé Purview – Event Hub         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Diagramme illustrant la résolution de noms Azure Purview"lightbox="media/catalog-private-link/purview-name-resolution-diagram.png":::

Pour plus d’informations, consultez [Charges de travail de réseau virtuel sans serveur DNS personnalisé](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) et [Charges de travail locales à l’aide de scénarios de redirecteur DNS](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) dans [Configuration DNS de point de terminaison privé Azure](../private-link/private-endpoint-dns.md).

### <a name="verify-virtual-network-links-on-azure-private-dns-zones"></a>Vérifier les liens de réseau virtuel dans les zones DNS privées Azure

Une fois le déploiement du point de terminaison privé effectué, vérifiez qu’il existe un [lien de réseau virtuel](../dns/private-dns-virtual-network-links.md) pour toutes les zones DNS privées Azure correspondantes vers le réseau virtuel Azure où le point de terminaison privé a été déployé.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-link.png" alt-text="Capture d’écran montrant les liens de réseau virtuel dans la zone DNS.":::

Pour plus d’informations, consultez [Configuration DNS des points de terminaison privés Azure](../private-link/private-endpoint-dns.md).

### <a name="configure-dns-forwarders-if-custom-dns-is-used"></a>Configurer les redirecteurs DNS si un DNS personnalisé est utilisé

De plus, il est nécessaire de valider vos configurations DNS sur le réseau virtuel Azure où se trouve le PC de gestion ou la machine virtuelle du runtime d’intégration autohébergé. 

   :::image type="content" source="media/catalog-private-link/purview-pe-custom-dns.png" alt-text="Diagramme montrant le DNS personnalisé du réseau virtuel Azure":::

- Si la valeur configurée est _Par défaut_, aucune autre action n’est nécessaire à cette étape.

-  Si un serveur DNS personnalisé est utilisé, vous devez ajouter les redirecteurs DNS correspondants sur vos serveurs DNS pour les zones suivantes :
  
   -  Purview.azure.com
   -  Blob.core.windows.net
   -  Queue.core.windows.net
   -  Servicebus.windows.net

### <a name="verify-internal-name-resolution"></a>Vérifier la résolution de noms interne

Lorsque vous résolvez l’URL du point de terminaison Azure Purview à l’extérieur du réseau virtuel avec le point de terminaison privé, elle correspond au point de terminaison public d’Azure Purview. En cas de résolution à partir du réseau virtuel hébergeant le point de terminaison privé, l’URL du point de terminaison Azure Purview correspond à l’adresse IP du point de terminaison privé.

Par exemple, si le nom d’un compte Azure Purview est « Contoso-Purview », quand il est résolu à l’extérieur du réseau virtuel qui héberge le point de terminaison privé, il devient :

| Name | Type | Valeur |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | Un | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview Studio public endpoint\> |

Les enregistrements de ressources DNS de Contoso-Purview, une fois résolus dans le réseau virtuel qui héberge le point de terminaison privé, deviennent :

| Name | Type | Valeur |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | Un | \<Purview account private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview portal private endpoint IP address\> |

## <a name="option-3---use-your-own-dns-servers"></a>Option 3 : Utiliser vos propres serveurs DNS

Si vous n’utilisez pas de redirecteurs DNS et que vous gérez les enregistrements A directement dans vos serveurs DNS locaux pour résoudre les points de terminaison par le biais de leurs adresses IP privées, vous devrez peut-être créer les enregistrements A suivants dans vos serveurs DNS.

> [!NOTE]
> Mettez à jour tous les noms avec `Contoso-Purview`, `scaneastusabcd1234` et `atlas-12345678-1234-1234-abcd-123456789abc` avec le nom des ressources Azure correspondantes dans votre environnement. Par exemple, au lieu de `scaneastusabcd1234`, utilisez le nom de votre compte de stockage managé Azure Purview.

| Name | Type | Valeur |
| ---------- | -------- | --------------- |
| `web.purview.azure.com` | Un | \<portal private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.blob.core.windows.net` | A | \<blob-ingestion private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.queue.core.windows.net` | A | \<queue-ingestion private endpoint IP address of Azure Purview> |
| `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`| A | \<namespace-ingestion private endpoint IP address of Azure Purview> |
| `Contoso-Purview.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `Contoso-Purview.scan.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `Contoso-Purview.catalog.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.proxy.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.guardian.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `gateway.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `manifest.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `cdn.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `hub.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `catalog.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `cseo.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `datascan.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `datashare.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `datasource.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `policy.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |


## <a name="verify-and-dns-test-name-resolution-and-connectivity"></a>Vérification et test DNS de la résolution de noms et de la connectivité 

1. Si vous utilisez des zones DNS privé Azure, assurez-vous que les zones DNS suivantes et les enregistrements A correspondants sont créés dans votre abonnement Azure :

   |Point de terminaison privé  |Point de terminaison privé associé à  |Zone DNS  |Enregistrement A (exemple) |
   |---------|---------|---------|---------|
   |Compte     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
   |Portail     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |Web         |
   |Ingestion     |Compte de stockage managé Purview – Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
   |Ingestion   |Compte de stockage managé Purview – File d’attente         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
   |Ingestion     |Compte de stockage managé Purview – Event Hub         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

2. Créez des [liaisons de réseau virtuel](../dns/private-dns-virtual-network-links.md) dans vos zones DNS privé Azure pour vos réseaux virtuels Azure afin de permettre la résolution de noms interne.
   
3. À partir de votre PC de gestion et de la machine virtuelle du runtime d’intégration auto-hébergé, testez la résolution de noms et la connectivité réseau à votre compte Azure Purview à l’aide d’outils tels que Nslookup.exe et PowerShell.

Pour tester la résolution de noms, vous devez résoudre les noms FQDN suivants via leurs adresses IP privées : (À la place de Contoso-Purview, scaneastusabcd1234 ou atlas-12345678-1234-1234-abcd-123456789abc, utilisez le nom d’hôte associé au nom de votre compte Purview et aux noms des ressources managées.)

- `Contoso-Purview.purview.azure.com`
- `web.purview.azure.com`
- `scaneastusabcd1234.blob.core.windows.net`
- `scaneastusabcd1234.queue.core.windows.net`
- `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`

Pour tester la connectivité réseau, à partir de la machine virtuelle du runtime d’intégration auto-hébergé, vous pouvez lancer la console PowerShell et tester la connectivité à l’aide de `Test-NetConnection`. Vous devez résoudre chaque point de terminaison par son point de terminaison privé et obtenir la valeur True pour TcpTestSucceeded. (À la place de Contoso-Purview, scaneastusabcd1234 ou atlas-12345678-1234-1234-abcd-123456789abc, utilisez le nom d’hôte associé au nom de votre compte Purview et aux noms des ressources managées.)

- `Test-NetConnection -ComputerName Contoso-Purview.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName web.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.blob.core.windows.net -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.queue.core.windows.net -port 443`
- `Test-NetConnection -ComputerName atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net -port 443` 

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes de configuration des point de terminaison privés pour votre compte Azure Purview](catalog-private-link-troubleshoot.md)
- [Gérer des sources de données dans Azure Purview](./manage-data-sources.md)
