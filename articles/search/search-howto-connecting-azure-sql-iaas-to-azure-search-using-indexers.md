---
title: Connexion de l’indexeur à SQL Server sur les machines virtuelles Azure
titleSuffix: Azure Cognitive Search
description: Activer les connexions chiffrées et configurer le pare-feu pour autoriser les connexions à SQL Server sur une machine virtuelle Azure à partir d’un indexeur sur la Recherche cognitive Azure.
author: nitinme
ms.author: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: d405b28acfcac8270b0e2ec4b3493f151108e4cf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076108"
---
# <a name="indexer-connections-to-sql-server-on-an-azure-virtual-machine"></a>Connexions de l’indexeur à SQL Server sur une machine virtuelle Azure

Lorsque vous configurez un [indexeur Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq) pour extraire du contenu d’une base de données sur une machine virtuelle Azure, des étapes supplémentaires sont requises pour des connexions de sécurité. 

Une connexion entre la Recherche cognitive Azure et SQL Server sur une machine virtuelle constitue une connexion Internet publique. Pour que les connexions sécurisées réussissent, procédez comme suit :

+ Obtenez un certificat auprès d'un [fournisseur d'autorité de certification](https://en.wikipedia.org/wiki/Certificate_authority#Providers) pour le nom de domaine complet de l'instance SQL de la machine virtuelle

+ Installez le certificat sur la machine virtuelle, puis activez et configurez des connexions chiffrées sur la machine virtuelle en suivant les instructions de cet article.

## <a name="enable-encrypted-connections"></a>Activer des connexions chiffrées

Le service Recherche cognitive Azure requiert un canal chiffré pour toutes les demandes d’indexeur via une connexion internet publique. Cette section répertorie les étapes pour y parvenir.

1. Vérifiez les propriétés du certificat pour vous assurer que le nom du sujet est le nom de domaine complet (FQDN) de la machine virtuelle Azure. Vous pouvez utiliser un outil tel que CertUtils ou le composant logiciel enfichable Certificats pour afficher les propriétés. Vous pouvez obtenir le nom de domaine complet à partir de la section Essentials du panneau du service de la machine virtuelle dans le champ **Adresse IP publique/Étiquette du nom DNS** dans le [portail Azure](https://portal.azure.com/).
  
   + Pour les machines virtuelles créées à l'aide du modèle **Resource Manager** le plus récent, le nom de domaine complet est au format `<your-VM-name>.<region>.cloudapp.azure.com`.

   + Pour les machines virtuelles plus anciennes créées comme machines virtuelles **Classic**, le nom de domaine complet est au format `<your-cloud-service-name.cloudapp.net>`.

1. Configurez SQL Server de manière à utiliser le certificat à l’aide de l’Éditeur du Registre (regedit). 

   Bien que le Gestionnaire de Configuration SQL Server soit souvent utilisé pour cette tâche, vous ne pouvez pas l’utiliser pour ce scénario. Il ne trouve pas le certificat importé, car le nom de domaine complet de la machine virtuelle sur Azure ne correspond pas au nom de domaine complet tel que déterminé par la machine virtuelle (il identifie le domaine en tant qu’ordinateur local ou domaine du réseau auquel il est joint). Lorsque les noms ne correspondent pas, utilisez regedit pour spécifier le certificat.

   + Dans regedit, accédez à la clé de Registre suivante : `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.

     La partie `[MSSQL13.MSSQLSERVER]` varie en fonction du nom de version et de l’instance. 

   + Définissez la valeur de la clé de **Certificat** sur l’**empreinte numérique** du certificat TLS/SSL que vous avez importé sur la machine virtuelle.

     Il existe plusieurs manières d’obtenir l’empreinte numérique, certaines étant mieux que d’autres. Si vous la copiez à partir du composant logiciel enfichable **certificats** dans MMC, vous allez probablement choisir un caractère de début invisible [comme décrit dans cet article du support technique](https://support.microsoft.com/kb/2023869/), ce qui entraîne une erreur lors de la tentative de connexion. Il existe plusieurs solutions de contournement pour résoudre ce problème. La plus simple consiste à reculer et retaper le premier caractère de l’empreinte numérique pour supprimer le caractère de début dans le champ de la valeur de clé dans regedit. Vous pouvez également utiliser un autre outil pour copier l’empreinte numérique.

1. Accordez des autorisations pour le compte de service. 

    Assurez-vous que le compte de service SQL Server a l’autorisation appropriée sur la clé privée du certificat TLS/SSL. Si vous ignorez cette étape, SQL Server ne démarre pas. Vous pouvez utiliser le composant logiciel enfichable **Certificats** ou **CertUtils** pour cette tâche.

1. Redémarrez le service SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurer la connectivité SQL Server dans la machine virtuelle

Après avoir configuré la connexion chiffrée requise par la Recherche cognitive Azure, vous devez réaliser des étapes de configuration supplémentaires intrinsèques à SQL Server sur les machines virtuelles Azure. Si vous ne l’avez pas déjà fait, l’étape suivante consiste à terminer la configuration en utilisant l’un de ces articles :

+ Pour une machine virtuelle **Resource Manager** , consultez l’article [Connect to a SQL Server Virtual Machine on Azure using Resource Manager](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)(Se connecter à une machine virtuelle SQL Server sur Azure à l’aide de Resource Manager). 

+ Pour une machine virtuelle **classique** , consultez [Connexion à une machine virtuelle sur Azure Classic](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect).

En particulier, examinez la section de chaque article pour la « connexion via internet ».

## <a name="configure-the-network-security-group-nsg"></a>Configurer le groupe de sécurité réseau

Il n’est pas inhabituel de configurer le groupe de sécurité réseau et le point de terminaison correspondant ou la liste de contrôle d’accès Azure correspondant(e) pour rendre votre machine virtuelle Azure accessible à d’autres parties. Vous avez sans doute déjà effectué cela pour permettre à votre propre logique d’application de se connecter à votre machine virtuelle SQL Azure. C’est la même chose pour une connexion de la Recherche cognitive Azure à votre machine virtuelle SQL Azure. 

Les liens ci-dessous fournissent des instructions sur la configuration du groupe de sécurité réseau pour les déploiements de machines virtuelles. Utilisez ces instructions pour faire figurer un point de terminaison Recherche cognitive Azure dans la liste de contrôle d’accès en fonction de son adresse IP.

> [!NOTE]
> Pour obtenir des informations générales, consultez [Présentation du groupe de sécurité réseau](../virtual-network/network-security-groups-overview.md)

+ Pour une machine virtuelle **Resource Manager** , consultez [How to create NSGs for ARM deployments](../virtual-network/tutorial-filter-network-traffic.md)(Procédure de création des groupes de sécurité réseau pour les déploiements ARM).

+ Pour une machine virtuelle **classique** , consultez [How to create NSGs for Classic deployments](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps)(Procédure de création des groupes de sécurité réseau pour les déploiements classiques).

L’adressage IP peut poser quelques problèmes qui sont facilement surmontés si vous êtes conscient du problème et des solutions de contournement possibles. Les sections restantes fournissent des recommandations pour la gestion des problèmes liés aux adresses IP dans la liste de contrôle d’accès.

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Restreindre l’accès à Recherche cognitive Azure

Nous vous recommandons fortement de restreindre l’accès à l’adresse IP de votre service de recherche et la plage d’adresses IP de l’ [étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags)`AzureCognitiveSearch` dans la liste de contrôle d’accès au lieu de rendre vos machines virtuelles SQL Azure disponibles pour toutes les demandes de connexion.

Vous pouvez trouver l’adresse IP en effectuer un test ping sur le nom de domaine complet (par exemple, `<your-search-service-name>.search.windows.net`) de votre service de recherche. Même s’il est possible que l’adresse IP du service de recherche change, il est peu probable que cela arrive. L’adresse IP a tendance à être statique pour la durée de vie du service.

Vous pouvez trouver la plage d’adresses IP de `AzureCognitiveSearch` [l’étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) en utilisant des [fichiers JSON téléchargeables](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) ou via [l’API de détection d’étiquettes de service](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api). La plage d’adresses IP est mise à jour chaque semaine.

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Inclure les adresses IP du portail de la Recherche cognitive Azure

Si vous utilisez le Portail Azure pour créer un indexeur, vous devez octroyer l’accès entrant au portail à votre machine virtuelle SQL Azure. Pour une règle de trafic entrant dans le pare-feu, vous devez fournir l’adresse IP du portail.

Pour obtenir l’adresse IP du portail, exécutez la commande ping `stamp2.ext.search.windows.net`, qui est le domaine du gestionnaire de trafic. La requête expire, mais l’adresse IP est visible dans le message d’état. Par exemple, dans le message « Pinging azsyrie.northcentralus.cloudapp.azure.com [52.252.175.48] », l’adresse IP est « 52.252.175.48 ».

> [!NOTE]
> Les clusters dans différentes régions se connectent à différents gestionnaires de trafic. Quel que soit le nom de domaine, l’adresse IP renvoyée par la commande ping est la bonne à utiliser lors de la définition d’une règle de pare-feu entrante pour le Portail Azure dans votre région.

## <a name="next-steps"></a>Étapes suivantes

Une fois la configuration résolue, vous pouvez maintenant spécifier un serveur SQL Server sur une machine virtuelle Azure comme source de données pour un indexeur de la Recherche cognitive Azure. Pour plus d’informations, consultez [Connexion d’Azure SQL Database à Recherche cognitive Azure à l’aide d’indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).
