---
title: Règles de réseau sortant requises pour Azure Managed Instance pour Apache Cassandra
description: En savoir plus sur les règles de réseau sortant requises et les noms de domaine complets pour Azure Managed Instance pour Apache Cassandra.
author: christopheranderson
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: chrande
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6d52f1c72765b3e7d3b7dd171c53c84e85138a20
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005230"
---
# <a name="required-outbound-network-rules"></a>Règles de trafic réseau sortant requises

Le service Azure Managed Instance pour Apache Cassandra requiert certaines règles de réseau pour gérer correctement le service. En vous assurant que les règles appropriées sont exposées, vous pouvez sécuriser votre service et prévenir les problèmes opérationnels.

## <a name="virtual-network-service-tags"></a>Balises de service du réseau virtuel

Si vous utilisez le pare-feu Azure pour limiter l’accès sortant, nous vous recommandons vivement d’utiliser des [étiquettes de service de réseau virtuel](../virtual-network/service-tags-overview.md). Voici les étiquettes requises pour qu’Azure Managed Instance pour Apache Cassandra fonctionne correctement.

| Étiquette du service de destination                                                             | Protocol | Port    | Utilisation  |
|----------------------------------------------------------------------------------|----------|---------|------|
| Stockage | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Stockage Azure pour la communication et la configuration du plan de contrôle|
| AzureKeyVault | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Azure Key Vault. Les certificats et les clés sont utilisés pour sécuriser la communication à l’intérieur du cluster|
| Event Hub | HTTPS | 443 | Requis pour transférer des journaux vers Azure |
| AzureMonitor | HTTPS | 443 | Requis pour transférer des métriques vers Azure |
| AzureActiveDirectory| HTTPS | 443 | Obligatoire pour l’authentification Azure Active Directory.|
| AzureResourceManager| HTTPS | 443 | Requis pour collecter des informations sur les nœuds Cassandra et les gérer (par exemple, redémarrer)|
| AzureFrontDoor.FirstParty| HTTPS | 443 | Requis pour les opérations de journalisation.|
| GuestAndHybridManagement | HTTPS | 443 |  Requis pour collecter des informations sur les nœuds Cassandra et les gérer (par exemple, redémarrer) |
| ApiManagement  | HTTPS | 443 | Requis pour collecter des informations sur les nœuds Cassandra et les gérer (par exemple, redémarrer) |

> [!NOTE]
> Outre ce qui précède, vous devez ajouter les préfixes d’adresse suivants, car il n’existe pas d’étiquette de service pour le service concerné : 104.40.0.0/13 13.104.0.0/14 40.64.0.0/10

## <a name="user-defined-routes"></a>Itinéraires définis par l’utilisateur

Si vous utilisez un pare-feu tiers pour limiter l’accès sortant, nous vous recommandons vivement de configurer des [itinéraires définis par l’utilisateur (UDR)](../virtual-network/virtual-networks-udr-overview.md#user-defined) pour les préfixes d’adresses Microsoft, au lieu de tenter d’autoriser la connectivité via votre propre pare-feu. Pour voir comment ajouter les préfixes d’adresse requis dans les itinéraires définis par l’utilisateur, consultez exemple de [script bash](https://github.com/Azure-Samples/cassandra-managed-instance-tools/blob/main/configureUDR.sh).

## <a name="azure-global-required-network-rules"></a>Règles de réseau requises pour Azure Global

Les règles de réseau et les dépendances d’adresse IP requises sont les suivantes :

| Point de terminaison de destination                                                             | Protocol | Port    | Utilisation  |
|----------------------------------------------------------------------------------|----------|---------|------|
|snovap\<region\>.blob.core.windows.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure Storage | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Stockage Azure pour la communication et la configuration du plan de contrôle|
|\*.store.core.windows.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure Storage | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Stockage Azure pour la communication et la configuration du plan de contrôle|
|\*.blob.core.windows.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure Storage | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Stockage Azure pour stocker les sauvegardes. *La fonctionnalité de sauvegarde est en cours de révision et le nom du stockage suivra un modèle par disponibilité générale*|
|vmc-p-\<region\>.vault.azure.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) - Azure KeyVault | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Azure Key Vault. Les certificats et les clés sont utilisés pour sécuriser la communication à l’intérieur du cluster|
|management.azure.com:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure Virtual Machine Scale Sets/API de gestion Azure | HTTPS | 443 | Requis pour collecter des informations sur les nœuds Cassandra et les gérer (par exemple, redémarrer)|
|\*.servicebus.windows.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) - Azure EventHub | HTTPS | 443 | Requis pour transférer des journaux vers Azure|
|jarvis-west.dc.ad.msft.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure Monitor | HTTPS | 443 | Requis pour transférer des métriques Azure |
|login.microsoftonline.com:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure AD | HTTPS | 443 | Obligatoire pour l’authentification Azure Active Directory.|
| packages.microsoft.com | HTTPS | 443 | Requis pour les mises à jour des définitions et des signatures d’analyseur de la sécurité Azure |
| azure.microsoft.com | HTTPS | 443 | Requis pour obtenir des informations sur les groupes de machines virtuelles identiques |
| \<region\>-dsms.dsms.core.windows.net | HTTPS | 443 | Certificat pour la journalisation |
| gcs.prod.monitoring.core.windows.net | HTTPS | 443 | Point de terminaison de journalisation nécessaire pour la journalisation |
| global.prod.microsoftmetrics.com | HTTPS | 443 | Nécessaire pour les métriques |
| shavsalinuxscanpkg.blob.core.windows.net | HTTPS | 443 | Nécessaire pour télécharger/mettre à jour le scanner de sécurité |
| crl.microsoft.com | HTTPS | 443 | Nécessaire pour accéder aux certificats Microsoft publics |
| global-dsms.dsms.core.windows.net | HTTPS | 443 | Nécessaire pour accéder aux certificats Microsoft publics |

### <a name="dns-access"></a>Accès DNS

Le système utilise des noms DNS pour atteindre les services Azure décrits dans cet article afin de pouvoir utiliser des équilibreurs de charge. Par conséquent, le réseau virtuel doit exécuter un serveur DNS capable de résoudre ces adresses. Les machines virtuelles du réseau virtuel honorent le serveur de noms communiqué via le protocole DHCP. Dans la plupart des cas, Azure configure automatiquement un serveur DNS pour le réseau virtuel. Si cela ne se produit pas dans votre scénario, les noms DNS décrits dans cet article constituent un bon guide pour commencer.

## <a name="internal-port-usage"></a>Utilisation du port interne

Les ports suivants sont accessibles uniquement au sein du réseau virtuel (ou réseaux virtuels associés/connexions ExpressRoute). Les instances Managed Instance pour Apache Cassandra n’ont pas d’IP publique et ne doivent pas être rendues accessibles sur Internet.

| Port | Utilisation |
| ---- | --- |
| 8443 | Interne |
| 9443 | Interne |
| 7001 | Gossip. Utilisé par les nœuds Cassandra pour communiquer entre eux |
| 9042 | Cassandra. Utilisé par les clients pour se connecter à Cassandra |
| 7199 | Interne |

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les règles de réseau pour gérer correctement le service. Explorez plus en détail Azure Managed Instance pour Apache Cassandra avec les articles suivants :

* [Vue d’ensemble d’Azure Managed Instance pour Apache Cassandra](introduction.md)
* [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)
