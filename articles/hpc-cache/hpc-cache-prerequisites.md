---
title: Prérequis pour Azure HPC Cache
description: Prérequis à l’utilisation d’Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/03/2021
ms.author: rohogue
ms.openlocfilehash: 1929677370ddf6f505f2425f61bfdaf4466223bb
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137745"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Prérequis pour Azure HPC Cache

Avant d’utiliser le portail Azure pour créer une nouvelle instance d’Azure HPC Cache, vérifiez que votre environnement répond à ces exigences.

## <a name="video-overviews"></a>Vues d’ensemble en vidéo

Regardez ces vidéos pour obtenir une vue d’ensemble rapide des composants du système et de ce dont ils ont besoin pour fonctionner ensemble.

(Cliquez sur l’image vidéo ou sur le lien pour regarder son contenu.)

* [Fonctionnement](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) - Explique comment Azure HPC Cache interagit avec le stockage et les clients

  [![image miniature de la vidéo : Azure HPC Cache : Fonctionnement (cliquez pour accéder à la page vidéo)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Prérequis](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) - Décrit les conditions requises pour le stockage NAS, Stockage Blob Azure, l’accès réseau et l’accès client

  [![image miniature de la vidéo : Azure HPC Cache : Prérequis (cliquez pour accéder à la page vidéo)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Pour obtenir des suggestions spécifiques, lisez le reste de cet article.

## <a name="azure-subscription"></a>Abonnement Azure

Un abonnement payant est recommandé.

## <a name="network-infrastructure"></a>Infrastructure réseau

Avant de pouvoir utiliser votre cache, vous devez configurer deux prérequis liés au réseau :

* Un sous-réseau dédié pour l’instance Azure HPC Cache
* La prise en charge du DNS afin que le cache puisse accéder au stockage et aux autres ressources

### <a name="cache-subnet"></a>Sous-réseau du cache

Azure HPC Cache a besoin d’un sous-réseau dédié avec les qualités suivantes :

* Le sous-réseau doit disposer d’au moins 64 adresses IP.
* Le sous-réseau ne peut pas héberger d’autres machines virtuelles, même pour les services associés tels que les ordinateurs clients.
* Si vous utilisez plusieurs instances Azure HPC Cache, chacune d’elles doit avoir son propre sous-réseau.

La méthode recommandée consiste à créer un nouveau sous-réseau pour chaque cache. Vous pouvez créer un réseau virtuel et un sous-réseau dans le cadre de la création du cache.

### <a name="dns-access"></a>Accès DNS

Le cache a besoin d’un accès DNS pour accéder aux ressources situées en dehors de son réseau virtuel. Selon les ressources que vous utilisez, vous devrez peut-être configurer un serveur DNS personnalisé et configurer un transfert entre ce serveur et les serveurs Azure DNS :

* Pour accéder aux points de terminaison du stockage Blob Azure et à d’autres ressources internes, vous avez besoin d’un serveur DNS basé sur Azure.
* Pour accéder au stockage local, vous devez configurer un serveur DNS personnalisé capable de résoudre les noms d’hôte de votre stockage. Vous devez effectuer cette opération avant de créer le cache.

Si vous n’utilisez que le Stockage Blob, vous pouvez vous servir du serveur DNS par défaut qui est fourni par Azure pour votre cache. Toutefois, si vous avez besoin d’accéder au stockage ou à d’autres ressources en dehors d’Azure, nous vous recommandons de créer un serveur DNS personnalisé et de le configurer dans le but de transférer au serveur Azure DNS toutes les demandes de résolution propres à Azure.

Pour utiliser un serveur DNS personnalisé, vous devez effectuer ces étapes de configuration avant de créer votre cache :

* Créez le réseau virtuel qui hébergera Azure HPC Cache.
* Créez le serveur DNS.
* Ajoutez le serveur DNS au réseau virtuel du cache.

  Procédez comme suit pour ajouter le serveur DNS au réseau virtuel dans le portail Azure :

  1. Ouvrez le réseau virtuel dans le portail Azure.
  1. Choisissez Serveurs DNS dans le menu Paramètres de la barre latérale.
  1. Sélectionnez Personnalisée.
  1. Entrez l’adresse IP du serveur DNS dans le champ.

Un simple serveur DNS peut également être utilisé pour équilibrer la charge des connexions clientes entre tous les points de montage du cache disponibles.

Pour plus d’informations sur les réseaux virtuels Azure et les configurations de serveur DNS, consultez [Résolution de noms des ressources dans les réseaux virtuels Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="ntp-access"></a>Accès NTP

HPC Cache a besoin d’accéder à un serveur NTP pour un fonctionnement normal. Si vous limitez le trafic sortant à partir de vos réseaux virtuels, assurez-vous d’autoriser le trafic vers au moins un serveur NTP. Le serveur par défaut est time.windows.com et le cache contacte ce serveur sur le port UDP 123.

Créez une règle dans le [groupe de sécurité réseau](../virtual-network/network-security-groups-overview.md) du réseau de votre cache qui autorise le trafic sortant vers votre serveur NTP. La règle peut simplement autoriser tout le trafic sortant sur le port UDP 123, ou avoir des restrictions supplémentaires.

Cet exemple ouvre explicitement le trafic sortant vers l’adresse IP 168.61.215.74, qui est l’adresse utilisée par time.windows.com.

| Priority | Nom | Port | Protocol | Source | Destination   | Action |
|----------|------|------|----------|--------|---------------|--------|
| 200      | NTP  | Quelconque  | UDP      | Quelconque    | 168.61.215.74 | Allow  |

Assurez-vous que la règle NTP a une priorité plus élevée que toutes les règles qui refusent largement l’accès sortant.

Conseils supplémentaires pour l’accès NTP :

* Si vous avez des pare-feu entre HPC Cache et le serveur NTP, assurez-vous que ces pare-feu autorisent également l’accès NTP.

* Vous pouvez configurer le serveur NTP utilisé par HPC Cache sur la page **Mise en réseau**. Pour plus d’informations, consultez [Configurer des paramètres supplémentaires](configuration.md#customize-ntp).

## <a name="permissions"></a>Autorisations

Consultez les prérequis liés aux autorisations avant de créer votre cache.

* L’instance de cache doit pouvoir créer des interfaces réseau virtuelles (NIC). L’utilisateur qui crée le cache doit disposer de privilèges suffisants dans l’abonnement pour créer des cartes réseau.

* Si vous utilisez le stockage Blob, Azure HPC Cache a besoin d’une autorisation pour accéder à votre compte de stockage. Utilisez le contrôle d’accès basé sur le rôle Azure (Azure RBAC) pour permettre au cache d’accéder à votre stockage Blob. Deux rôles sont nécessaires : Contributeur de comptes de stockage et Contributeur aux données Blob du stockage.

  Suivez les instructions fournies dans [Ajouter des cibles de stockage](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account).

## <a name="storage-infrastructure"></a>Infrastructure du stockage
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

Le cache prend en charge les conteneurs d’objets blob Azure, les exportations de stockage matériel NFS et les conteneurs d’objets blob ADLS montés sur NFS. Ajoutez des cibles de stockage après avoir créé le cache.

La taille de votre cache détermine le nombre de cibles de stockage qu’il peut prendre en charge, jusqu’à 10 cibles de stockage pour la plupart des caches, voire 20 pour les plus grandes tailles. Pour plus d’informations, consultez [Dimensionner correctement votre cache pour prendre en charge vos cibles de stockage](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets).

Chaque type de stockage possède des conditions préalables spécifiques.

### <a name="blob-storage-requirements"></a>Exigences relatives au stockage Blob

Si vous souhaitez utiliser le stockage Blob Azure avec votre cache, vous aurez besoin d’un compte de stockage compatible, et soit d’un conteneur d’objets blob vide, soit d’un conteneur comprenant des données au format Azure HPC Cache. Pour plus d’informations, consultez [Déplacer des données vers le stockage Blob Azure](hpc-cache-ingest.md).

> [!NOTE]
> Des exigences différentes s’appliquent au stockage d’objets blob monté NFS. Pour plus d’informations, consultez [Exigences du stockage ADLS-NFS](#nfs-mounted-blob-adls-nfs-storage-requirements).

Créez le compte avant de tenter d’ajouter une cible de stockage. Vous pouvez créer un conteneur lorsque vous ajoutez la cible.

Pour créer un compte de stockage compatible, utilisez une des combinaisons suivantes :

| Performances | Type | Réplication | Niveau d’accès |
|--|--|--|--|
| standard | StorageV2 (v2 universel)| Stockage localement redondant (LRS) ou stockage redondant interzone (ZRS) | À chaud |
| Premium | Objets blob de blocs | Stockage localement redondant (LRS) | À chaud |

Le compte de stockage doit être accessible à partir du sous-réseau privé de votre cache. Si votre compte utilise un point de terminaison privé ou public qui est limité à des réseaux virtuels spécifiques, veillez à activer l’accès à partir du sous-réseau du cache. (Il n’est pas recommandé d’utiliser un point de terminaison public ouvert.)

Il est recommandé d’utiliser un compte de stockage se trouvant dans la même région Azure que votre cache.

Vous devez également autoriser l’application de cache à accéder à votre compte de stockage Azure, tel que mentionné dans [Autorisations](#permissions), ci-dessus. Suivez la procédure indiquée dans [Ajouter des cibles de stockage](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) pour accorder au cache les rôles d’accès requis. Si vous n’êtes pas le propriétaire du compte de stockage, demandez au propriétaire d’effectuer cette étape.

### <a name="nfs-storage-requirements"></a>Conditions requises pour le stockage NFS
<!-- linked from configuration.md and add storage -->

Si vous utilisez un système de stockage NFS (par exemple, un système NAS matériel local), assurez-vous qu’il répond à ces exigences. Vous devrez peut-être travailler avec les administrateurs réseau ou les managers de pare-feu de votre système de stockage (ou centre de données) pour vérifier ces paramètres.

> [!NOTE]
> La création de la cible de stockage échoue si le cache ne dispose pas d’un accès suffisant au système de stockage NFS.

Pour plus d’informations, consultez [Résoudre les problèmes de configuration NAS et de cible de stockage NFS](troubleshoot-nas.md).

* Connectivité réseau : Azure HPC Cache a besoin d’un accès réseau à bande passante élevée entre le sous-réseau du cache et le centre de données du système NFS. Il est recommandé de disposer d’un accès [ExpressRoute](../expressroute/index.yml) ou similaire. Si vous utilisez un VPN, vous devrez peut-être le configurer pour fixer la MSS TCP à 1350 afin de vous assurer que les paquets volumineux ne sont pas bloqués. Pour obtenir de l’aide relative à la résolution des problèmes de paramètres VPN, consultez [Restrictions de taille des paquets VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions).

* Accès au port : Le cache a besoin d’accéder à des ports TCP/UDP spécifiques sur votre système de stockage. Les différents types de stockage ont des exigences de port différentes.

  Pour vérifier les paramètres de votre système de stockage, procédez comme suit.

  * Adressez une commande `rpcinfo` à votre système de stockage pour vérifier les ports nécessaires. La commande ci-dessous répertorie les ports et met en forme les résultats pertinents dans une table. (Utilisez l’adresse IP de votre système à la place du terme *<storage_IP>* .)

    Vous pouvez lancer cette commande à partir de tout client Linux doté d’une infrastructure NFS. Si vous utilisez un client à l’intérieur du sous-réseau du cluster, il peut également vous aider à vérifier la connectivité entre le sous-réseau et le système de stockage.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Vérifiez que tous les ports retournés par la requête ``rpcinfo`` autorisent sans restrictions le trafic provenant du sous-réseau d’Azure HPC Cache.

  * Si vous ne pouvez pas utiliser la commande `rpcinfo`, assurez-vous que ces ports couramment utilisés autorisent le trafic entrant et sortant :

    | Protocol | Port  | Service  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

    Certains systèmes utilisent des numéros de port différents pour ces services : consultez la documentation de votre système de stockage pour vous en assurer.

  * Vérifiez les paramètres du pare-feu pour vous assurer qu’ils autorisent le trafic sur tous ces ports requis. Veillez à vérifier les pare-feux utilisés dans Azure ainsi que ceux de votre centre de données.

* Accès racine (lecture/écriture) : Le cache se connecte au système principal en tant qu’identifiant utilisateur 0. Vérifiez ces paramètres sur votre système de stockage :
  
  * Activez `no_root_squash`. Cette option permet de s’assurer que l’utilisateur racine distant peut accéder aux fichiers appartenant à la racine.

  * Vérifiez les stratégies d’exportation pour vous assurer qu’elles n’incluent pas de restrictions sur l’accès à la racine depuis le sous-réseau du cache.

  * Si votre stockage comporte des exportations représentant des sous-répertoires d'une autre exportation, assurez-vous que le cache dispose d’un accès à la racine du segment le plus bas du chemin. Pour plus de détails, consultez l'article sur le dépannage de la cible de stockage NFS [Accès racine aux chemins d’accès aux répertoires](troubleshoot-nas.md#allow-root-access-on-directory-paths).

* Le stockage back-end NFS doit être une plateforme matérielle ou logicielle compatible. Pour plus d’informations, contactez l’équipe Azure HPC Cache.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements"></a>Exigences du stockage d’objets blob montés sur NFS (ADLS-NFS)

Azure HPC Cache peut également utiliser un conteneur d’objets blob monté avec le protocole NFS comme cible de stockage.

Pour plus d’informations sur cette fonctionnalité, consultez [Prise en charge du protocole NFS 3.0 dans le Stockage Blob Azure](../storage/blobs/network-file-system-protocol-support.md).

Les exigences en matière de compte de stockage sont différentes pour une cible de stockage d’objets blob ADLS-NFS et pour une cible de stockage d’objets blob standard. Suivez avec soin les instructions de [Montage du stockage blob avec le protocole NFS (Network File System) 3.0](../storage/blobs/network-file-system-protocol-support-how-to.md) pour créer et configurer le compte de stockage compatible NFS.

Il s’agit d’une vue d’ensemble générale de la procédure. Les étapes sont susceptibles de changer. Pour connaître les informations à jour, consultez toujours les [instructions ADLS-NFS](../storage/blobs/network-file-system-protocol-support-how-to.md).

1. Vérifiez que les fonctionnalités dont vous avez besoin sont disponibles dans les régions où vous envisagez de travailler.

1. Activez la fonctionnalité de protocole NFS pour votre abonnement, et ce, *avant* de créer le compte de stockage.

1. Créez un réseau virtuel sécurisé (VNet) pour le compte de stockage. Utilisez le même réseau virtuel pour votre compte de stockage compatible NFS que pour votre instance Azure HPC Cache. (Ne choisissez pas le même sous-réseau que pour le cache.)

1. Créez le compte de stockage.

   * Au lieu d’utiliser les paramètres d’un compte de stockage d’objets blob standard, suivez les instructions contenues dans le [guide pratique](../storage/blobs/network-file-system-protocol-support-how-to.md). Le type de compte de stockage pris en charge peut varier selon la région Azure.

   * Dans la section Mise en réseau, choisissez un point de terminaison privé dans le réseau virtuel sécurisé que vous avez créé (recommandé), ou bien un point de terminaison public avec accès restreint à partir du réseau virtuel sécurisé.

   * N’oubliez pas d’effectuer la section Avancé, dans laquelle vous activez l’accès NFS.

   * Autorisez l’application de cache à accéder à votre compte de stockage Azure (cf. [Autorisations](#permissions) plus haut). Vous pouvez effectuer cette opération la première fois que vous créez une cible de stockage. Suivez la procédure indiquée dans [Ajouter des cibles de stockage](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) pour accorder au cache les rôles d’accès requis.

     Si vous n’êtes pas le propriétaire du compte de stockage, demandez au propriétaire d’effectuer cette étape.

Pour en savoir plus sur l’utilisation de cibles de stockage ADLS-NFS avec Azure HPC cache, voir [Utilisation du stockage BLOB monté sur NFS avec Azure HPC Cache](nfs-blob-considerations.md).

## <a name="set-up-azure-cli-access-optional"></a>Configuration de l’accès Azure CLI (facultatif)

Si vous souhaitez créer ou gérer Azure HPC Cache à partir de l’interface de ligne de commande Azure (Azure CLI), vous devez installer le logiciel CLI et l’extension hpc-cache. Suivez les instructions dans [Configurer Azure CLI pour Azure HPC Cache](az-cli-prerequisites.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créez une instance d’Azure HPC Cache](hpc-cache-create.md) à partir du portail Azure.
