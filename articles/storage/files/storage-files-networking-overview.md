---
title: Considérations relatives à la mise en réseau Azure Files | Microsoft Docs
description: Vue d’ensemble des options de réseau pour Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d3f3bb466156b08ae90561d5e02e5b5ddf469755
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518505"
---
# <a name="azure-files-networking-considerations"></a>Considérations relatives à la mise en réseau Azure Files 
Vous pouvez vous connecter à un partage de fichiers Azure de deux manières :

- En accédant au partage directement par le biais des protocoles SMB (Server Message Block), NFS (Network File System) ou FileREST. Ce modèle d’accès est principalement utilisé pour éliminer autant de serveurs locaux que possible.
- En créant un cache du partage de fichiers Azure sur un serveur local (ou sur une machine virtuelle Azure) avec Azure File Sync, puis en accédant aux données du partage de fichiers à partir du serveur local avec le protocole de votre choix (SMB, NFS, FTPS, etc.) en fonction de votre cas d’usage. Ce modèle d’accès est pratique car il combine le meilleur des performances locales et de l’échelle du cloud, ainsi que des services pouvant être attachés sans serveur (serverless), tels que Sauvegarde Azure.

Cet article explique comment configurer la mise en réseau lorsque votre cas d’utilisation demande à accéder directement au partage de fichiers Azure au lieu d’utiliser Azure File Sync. Pour plus d’informations sur les considérations relatives à la mise en réseau pour un déploiement Azure File Sync, consultez [Considérations relatives à la mise en réseau Azure File Sync](../file-sync/file-sync-networking-overview.md).

La configuration réseau des partages de fichiers Azure s’effectue sur le compte de stockage Azure. Un compte de stockage est une construction de gestion qui représente un pool de stockage partagé dans lequel vous pouvez déployer plusieurs partages de fichiers, ainsi que d’autres ressources de stockage, telles que des conteneurs d’objets blob ou des files d’attente. Les comptes de stockage présentent plusieurs paramètres qui permettent sécuriser l’accès réseau à vos partages de fichiers : points de terminaison réseau, paramètres de pare-feu de compte de stockage et chiffrement en transit. 

> [!Important]  
> Azure Files prend en charge plusieurs options de routage réseau. L’option par défaut, routage Microsoft, fonctionne avec toutes les configurations d’Azure Files. L’option de routage Internet ne prend pas en charge les scénarios de jonction de domaine AD ou Azure File Sync.

Avant d’entamer la lecture de ce guide conceptuel, nous vous recommandons de lire [Planification d’un déploiement Azure Files](storage-files-planning.md).

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Cette vidéo montre comment exposer directement et de façon sécurisée les partages de fichiers Azure aux travailleurs de l’information et aux applications, en cinq étapes simples. Les sections ci-dessous fournissent des liens et un contexte supplémentaire à la documentation référencée dans la vidéo.
   :::column-end:::
:::row-end:::

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |

## <a name="accessing-your-azure-file-shares"></a>Accès à vos partages de fichiers Azure
Les partages de fichiers SMB Azure sont immédiatement accessibles via le point de terminaison public du compte de stockage avec SMB 3.1.1 et SMB 3.0. Ainsi, les demandes authentifiées, comme celles qui sont autorisées par l’identité d’ouverture de session d’un utilisateur, peuvent provenir de manière sécurisée, de l’intérieur comme de l’extérieur d’Azure. Les partages de fichiers NFS Azure sont accessibles uniquement par le biais du point de terminaison public du compte de stockage si ce point de terminaison public est limité aux réseaux virtuels Azure.

Pour de nombreux environnements, vous souhaiterez peut-être appliquer une configuration réseau supplémentaire à leurs partages de fichiers Azure :

- En ce qui concerne les partage de fichiers SMB, de nombreuses organisations et autres fournisseurs de services Internet bloquent le port dont se sert SMB pour communiquer, à savoir le port 445. Cette pratique résulte des recommandations de sécurité héritées concernant les versions déconseillées et non sécurisées pour Internet du protocole SMB. Bien que SMB 3.x soit un protocole sécurisé pour Internet, les stratégies organisationnelles ou ISP peuvent ne pas être modifiables. 

- En ce qui concerne les partages de fichiers NFS, l’accès au point de terminaison public restreint limite uniquement les montages au sein d’Azure.

- Certaines organisations requièrent du trafic vers Azure pour suivre un chemin déterministe.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunneling du trafic sur un réseau privé virtuel ou ExpressRoute
Quand vous établissez un tunnel réseau entre votre réseau local et Azure, vous effectuez le peering de votre réseau local avec un ou plusieurs réseaux virtuels dans Azure. Un [réseau virtuel](../../virtual-network/virtual-networks-overview.md), ou VNet, est similaire à un réseau classique que vous exploiteriez localement. À l’instar d’un compte de stockage Azure ou d’une machine virtuelle Azure, un réseau virtuel est une ressource Azure déployée dans un groupe de ressources. 

Azure Files prend en charge les mécanismes suivants pour tunneliser le trafic entre vos stations de travail et serveurs locaux et les partages de fichiers SMB/NFS Azure :

- une passerelle VPN est un type spécifique de passerelle de réseau virtuel utilisé pour envoyer du trafic chiffré d’un réseau virtuel Azure vers un autre emplacement (par exemple local) via Internet. Une passerelle VPN Azure est une ressource Azure qui peut être déployée dans un groupe de ressources aux côtés d’un compte de stockage ou d’autres ressources Azure. Les passerelles VPN exposent deux types de connexion :
    - Des connexions de passerelle [VPN point à site (P2S)](../../vpn-gateway/point-to-site-about.md) , qui sont des connexions VPN entre Azure et un client individuel. Cette solution est principalement utile pour des appareils ne faisant pas partie du réseau local de votre organisation. C’est le cas, par exemple, quand des télétravailleurs souhaitent pouvoir monter leur partage de fichiers Azure à partir de chez eux, d’un café ou d’un hôtel quand ils sont en déplacement. Pour utiliser une connexion VPN P2S avec Azure Files, une connexion VPN P2S doit être configurée pour chaque client qui souhaite se connecter. Pour simplifier le déploiement d’une connexion VPN P2S, consultez [Configurer un VPN point à site (P2S) sur Windows pour une utilisation avec Azure Files](storage-files-configure-p2s-vpn-windows.md) et [Configurer un VPN point à site (P2S) sur Linux pour une utilisation avec Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - Des connexions [VPN site à site (S2S) ](../../vpn-gateway/design.md#s2smulti) entre Azure et le réseau de votre organisation. Une connexion VPN S2S vous permet de configurer une connexion VPN une seule fois pour un serveur VPN ou un appareil hébergé sur le réseau de votre entreprise, au lieu de le faire que pour chaque appareil client devant accéder à votre partage de fichiers Azure. Pour simplifier le déploiement d’une connexion VPN S2S, consultez [Configurer un VPN site à site (S2S) pour une utilisation avec Azure Files](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md), qui vous permet de créer un itinéraire défini entre Azure et votre réseau local qui ne traverse pas Internet. ExpressRoute peut être utile quand les performances du réseau doivent être prises en compte, car il fournit un chemin dédié entre votre centre de données local et Azure. ExpressRoute est également une bonne option quand une stratégie ou des exigences réglementaires de votre organisation exigent un chemin d’accès déterministe à vos ressources dans le cloud.

Quelle que soit la méthode de tunneling que vous utilisez pour accéder à vos partages de fichiers Azure, vous avez besoin d’un mécanisme qui fasse en sorte que le trafic à destination de votre compte de stockage emprunte le tunnel et non votre connexion Internet normale. Il est techniquement possible de router le trafic vers le point de terminaison public du compte de stockage, mais cela nécessite de coder en dur toutes les adresses IP pour les clusters de stockage Azure d’une région, car les comptes de stockage peuvent à tout moment être déplacés entre les clusters de stockage. Cela nécessite aussi une mise à jour permanente des mappages d’adresses IP, car de nouveaux clusters sont ajoutés en permanence.

Plutôt que de coder en dur les adresses IP de vos comptes de stockage dans vos règles de routage VPN, nous vous suggérons d’utiliser des points de terminaison privés. Vos comptes de stockage recevront ainsi une adresse IP issue de l’espace d’adressage d’un réseau virtuel Azure. La création d’un tunnel vers Azure a pour effet d’établir un peering entre votre réseau local et un ou plusieurs réseaux virtuels, et donc de permettre un routage correct dans la durée.

### <a name="private-endpoints"></a>Instances Private Endpoint
En plus du point de terminaison public par défaut du compte de stockage, Azure Files permet de disposer d’un ou plusieurs points de terminaison privés. Un point de terminaison privé est un point de terminaison qui n’est accessible qu’à l’intérieur d’un réseau virtuel Azure. Quand vous créez un point de terminaison privé pour votre compte de stockage, celui-ci obtient une adresse IP privée en provenance de l’espace d’adressage de votre réseau virtuel, de la même manière qu’un serveur de fichiers local ou un périphérique NAS reçoit une adresse IP de l’espace d’adressage dédié de votre réseau local. 

Un point de terminaison privé individuel est associé à un sous-réseau de réseau virtuel Azure spécifique. Un compte de stockage peut avoir des points de terminaison privés dans plusieurs réseaux virtuels.

L’utilisation de points de terminaison privés avec Azure Files vous permet de :
- Établir une connexion sécurisée avec vos partages de fichiers Azure à partir de réseaux locaux en utilisant une connexion VPN ou ExpressRoute avec un peering privé.
- Sécuriser vos partages de fichiers Azure en configurant le pare-feu du compte de stockage de façon à bloquer toutes les connexions sur le point de terminaison public. Par défaut, la création d’un point de terminaison privé n’a pas pour effet de bloquer les connexions au point de terminaison public.
- Renforcer la sécurité du réseau virtuel en bloquant l’exfiltration des données du réseau virtuel (et des limites du peering).

Pour créer un point de terminaison privé, consultez [Configuration des points de terminaison privés pour Azure Files](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Points de terminaison privés et DNS
Quand vous créez un point de terminaison privé, par défaut, nous créons aussi une zone DNS privée (ou en mettons une existante à jour) correspondant au sous-domaine `privatelink`. Vous n’êtes pas à proprement parler tenu de créer une zone DNS privée pour utiliser un point de terminaison privé avec votre compte de stockage. Cependant, cela est en général fortement recommandé et explicitement exigé quand il s’agit de monter un partage de fichiers Azure avec un principal d’utilisateur Active Directory ou d’y accéder à partir de l’API FileREST.

> [!Note]  
> Cet article utilise le suffixe DNS de compte de stockage pour les régions publiques Azure, `core.windows.net`. Ce commentaire vaut aussi pour les clouds souverains Azure que sont notamment le cloud Azure US Government et le cloud Azure Chine (il vous suffit de remplacer les suffixes appropriés pour votre environnement). 

Dans votre zone DNS privée, nous créons un enregistrement A pour `storageaccount.privatelink.file.core.windows.net` et un enregistrement CNAME pour le nom régulier du compte de stockage, qui suit le modèle `storageaccount.file.core.windows.net`. Sachant que votre zone DNS privée Azure est connectée au réseau virtuel contenant le point de terminaison privé, vous pouvez observer la configuration DNS en appelant l’applet de commande `Resolve-DnsName` à partir de PowerShell dans une machine virtuelle Azure (ou `nslookup` dans Windows et Linux) :

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

Pour cet exemple, le compte de stockage `storageaccount.file.core.windows.net` est résolu en adresse IP privée du point de terminaison privé, à savoir `192.168.0.4`.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Si vous exécutez la même commande en local, vous constaterez que le même nom de compte de stockage est résolu en adresse IP publique du compte de stockage. `storageaccount.file.core.windows.net` est un enregistrement CNAME pour `storageaccount.privatelink.file.core.windows.net` qui, de son côté, est un enregistrement CNAME pour le cluster de stockage Azure hébergeant le compte de stockage :

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Cela traduit le fait que le compte de stockage peut exposer à la fois le point de terminaison public et un ou plusieurs points de terminaison privés. Pour être certain que le nom du compte de stockage est résolu en adresse IP privée du point de terminaison privé, vous devez modifier la configuration sur vos serveurs DNS locaux. Il existe plusieurs façons de procéder :

- Modifiez le fichier hosts sur vos clients pour faire en sorte que `storageaccount.file.core.windows.net` soit résolu en adresse IP privée du point de terminaison privé souhaité. Cela est fortement déconseillé pour les environnements de production, car vous devrez apporter ces modifications à chaque client désireux de monter vos partages de fichiers Azure, et les modifications apportées au compte de stockage ou au point de terminaison privé ne seront pas gérées automatiquement.
- Créez un enregistrement A pour `storageaccount.file.core.windows.net` sur vos serveurs DNS locaux. L’avantage de cette méthode est que les clients de votre environnement local pourront résoudre automatiquement le compte de stockage sans avoir à configurer chaque client. Cependant, cette solution s’avère tout aussi fragile pour ce qui est de la modification du fichier hosts, car les modifications n’apparaîtront pas. Bien que fragile, cette solution peut s’avérer être le meilleur choix pour certains environnements.
- Transférez la zone de `core.windows.net` de vos serveurs DNS locaux vers votre zone DNS privée Azure. Il est possible d’atteindre l’hôte DNS privé Azure via une adresse IP spéciale (`168.63.129.16`) qui est uniquement accessible à l’intérieur des réseaux virtuels qui sont liés à la zone DNS privée Azure. Pour contourner cette limitation, vous pouvez exécuter des serveurs DNS supplémentaires à l’intérieur de votre réseau virtuel, qui transféreront `core.windows.net` vers la zone DNS privée Azure. Pour simplifier cette configuration, nous avons prévu des applets de commande PowerShell qui assurent le déploiement automatique des serveurs DNS dans votre réseau virtuel Azure et les configurent comme vous le souhaitez. Pour savoir comment configurer le transfert DNS, consultez [Configuration du DNS avec Azure Files](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Paramètres de pare-feu de compte de stockage
Un pare-feu est une stratégie réseau qui détermine les demandes qui sont autorisées à accéder au point de terminaison public d’un compte de stockage. En utilisant le pare-feu de compte de stockage, vous pouvez limiter l’accès au point de terminaison public du compte de stockage à certaines adresses IP, à des plages ou à un réseau virtuel. En général, la plupart des stratégies de pare-feu d’un compte de stockage limitent l’accès réseau à un ou plusieurs réseaux virtuels. 

Il existe deux approches pour limiter l’accès d’un compte de stockage à un réseau virtuel :
- Créer un ou plusieurs points de terminaison privés pour le compte de stockage et limiter tous les accès au point de terminaison public. Seul le trafic en provenance des réseaux virtuels souhaités accède alors aux partages de fichiers Azure du compte de stockage.
- Limiter le point de terminaison public à un ou plusieurs réseaux virtuels. Cette approche s’appuie sur une fonctionnalité du réseau virtuel appelée *points de terminaison de service*. Quand vous limitez le trafic à un compte de stockage via un point de terminaison de service, vous continuez d’accéder au compte de stockage via l’adresse IP publique.

> [!NOTE]
> Les partages de fichiers NFS peuvent uniquement accéder au point de terminaison public du compte de stockage via les réseaux virtuels. Les partages NFS peuvent accéder librement aux points de terminaison privés du compte de stockage.

Pour en savoir plus sur la façon de configurer le pare-feu de compte de stockage, consultez [Configurer des pare-feux et des réseaux virtuels dans Stockage Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Voir aussi
- [Vue d’ensemble d’Azure Files](storage-files-introduction.md)
- [Planification d’un déploiement Azure Files](storage-files-planning.md)
