---
title: Résoudre les problèmes liés à des exceptions interdites Azure Cosmos DB
description: Découvrez comment diagnostiquer et résoudre des exceptions interdites.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 9a5f1590e73388429f613a1c6b078f5c315397d6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238941"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnostiquer et résoudre les problèmes liés à des exceptions interdites Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Le code d’état HTTP 403 indique que la demande n’est pas autorisée à se terminer.

## <a name="firewall-blocking-requests"></a>Requêtes de blocage de pare-feu

Les requêtes de plan de données peuvent être envoyées à Cosmos DB via les trois chemins suivants.

- Internet public (IPv4)
- Point de terminaison de service
- Point de terminaison privé

Quand une requête de plan de données est bloquée avec 403 Interdit, le message d’erreur spécifie par lequel des trois chemins ci-dessus la requête est parvenue à Cosmos DB.

- `Request originated from client IP {...} through public internet.`
- `Request originated from client VNET through service endpoint.`
- `Request originated from client VNET through private endpoint.`

### <a name="solution"></a>Solution

Déterminez à partir de quel chemin la requête est **censée** parvenir à Cosmos DB.
   - Si le message d’erreur indique que la requête n’est pas parvenue à Cosmos DB via le chemin attendu, le problème est probablement lié à la configuration côté client. Vérifiez votre configuration côté client conformément à la documentation.
      - Internet public : [Configurer un pare-feu IP dans Azure Cosmos DB](../how-to-configure-firewall.md).
      - Point de terminaison de service : [Configurer l’accès à Azure Cosmos DB à partir de réseaux virtuels (VNet)](../how-to-configure-vnet-service-endpoint.md). Par exemple, si vous prévoyez d’utiliser un point de terminaison de service mais que la requête a été envoyée à Cosmos DB via l’Internet public, il est possible que le sous-réseau sur lequel le client s’exécutait n’ait pas activé le point de terminaison de service vers Cosmos DB.
      - Point de terminaison privé : [Configurer Azure Private Link pour un compte Azure Cosmos](../how-to-configure-private-endpoints.md). Par exemple, si vous prévoyez d’utiliser un point de terminaison privé mais que la requête a été envoyée à Cosmos DB via l’Internet public, le système DNS de la machine virtuelle n’a peut-être pas été configuré pour résoudre le point de terminaison de compte vers l’adresse IP privée, et il est donc passé à la place par l’adresse IP publique du compte.
   - Si la requête est parvenue à Cosmos DB via le chemin attendu, la requête a été bloquée car l’identité du réseau source n’a pas été configurée pour être autorisée pour le compte. Vérifiez les paramètres du compte en fonction du chemin par lequel la requête est parvenue à Cosmos DB.
      - Internet public : vérifiez les configurations d’[accès réseau public](../how-to-configure-private-endpoints.md#blocking-public-network-access-during-account-creation) et de filtre de plage d’adresses IP du compte.
      - Point de terminaison de service : vérifiez les configurations d’[accès réseau public](../how-to-configure-private-endpoints.md#blocking-public-network-access-during-account-creation) et de filtre de réseau virtuel du compte.
      - Point de terminaison privé : vérifiez la configuration du point de terminaison privé du compte et la configuration DNS privée du client. Cela peut être dû à l’accès à un compte à partir d’un point de terminaison privé configuré pour un autre compte.

Si vous avez récemment mis à jour les configurations de pare-feu du compte, gardez à l’esprit que l’**application des modifications peut nécessiter jusqu’à 15 minutes**.

## <a name="partition-key-exceeding-storage"></a>Clé de partition dépassant le stockage
Dans ce scénario, il est courant de voir des erreurs similaires à celles ci-dessous :

```
Response status code does not indicate success: Forbidden (403); Substatus: 1014
```

```
Partition key reached maximum size of {...} GB
```

### <a name="solution"></a>Solution
Cette erreur signifie que vos [conception de partitionnement](../partitioning-overview.md#logical-partitions) et charge de travail actuelles essaient de stocker une quantité de données supérieure à celle autorisée pour une valeur de clé de partition donnée. Le nombre de partitions logiques dans votre conteneur n’est pas limitée, contrairement à la taille des données que chaque partition logique peut stocker. Vous pouvez contacter le support technique pour obtenir des clarifications.

## <a name="non-data-operations-are-not-allowed"></a>Les opérations non liées à des données ne sont pas autorisées
Ce scénario se produit lorsque des [opérations non liées à des données sont interdites dans le compte](../how-to-restrict-user-data.md#disallow-the-execution-of-non-data-operations). Dans ce scénario, il est courant de voir des erreurs similaires à celles ci-dessous :

```
Operation 'POST' on resource 'calls' is not allowed through Azure Cosmos DB endpoint
```

### <a name="solution"></a>Solution
Effectuez l’opération via Azure Resource Manager, le portail Azure, Azure CLI ou Azure PowerShell. Vous pouvez également ré-autoriser l’exécution d’opérations non liées à des données.

## <a name="next-steps"></a>Étapes suivantes
* Configurez le [pare-feu IP](../how-to-configure-firewall.md).
* Configurez l’accès à partir de [réseaux virtuels](../how-to-configure-vnet-service-endpoint.md).
* Configurez l’accès depuis des [points de terminaison privés](../how-to-configure-private-endpoints.md).
