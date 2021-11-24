---
title: Autoriser l’accès à des plages d’adresses IP de l’indexeur
titleSuffix: Azure Cognitive Search
description: Configurez les règles de pare-feu IP pour autoriser l’accès aux données par un indexeur Recherche cognitive Azure.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 50ee624a3795015d35637e0e984a48025c802698
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492717"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-in-azure-cognitive-search"></a>Configurer des règles de pare-feu IP pour autoriser les connexions d’indexeur dans Recherche cognitive Azure

Pour le compte d’un indexeur, un service de recherche émet des appels sortants vers une ressource Azure externe pour extraire les données pendant l’indexation. Si votre ressource Azure utilise des règles de pare-feu IP pour filtrer les appels entrants, vous devez créer une règle de trafic entrant dans votre pare-feu qui admet les requêtes d’indexeur.

Cet article explique comment rechercher l’adresse IP0 de votre service de recherche, puis utiliser le Portail Azure pour configurer une règle d’adresse IP entrante sur un compte Stockage Azure. Bien que propre à Stockage Azure, cette approche fonctionne également pour les autres ressources Azure qui utilisent des règles de pare-feu IP pour l’accès aux données, telles que Cosmos DB et Azure SQL.

> [!NOTE]
> Les règles de pare-feu IP pour un compte de stockage ne sont effectives que si le compte de stockage et le service de recherche se trouvent dans des régions différentes. Si votre installation ne le permet pas, nous vous recommandons d’utiliser l’[option d’exception de service approuvé](search-indexer-howto-access-trusted-service-exception.md) comme alternative.

## <a name="get-a-search-service-ip-address"></a>Obtenir une adresse IP de service de recherche

1. Déterminez le nom de domaine complet (FQDN) de votre service de recherche. Il ressemble à ceci : `<search-service-name>.search.windows.net`. Vous pouvez déterminer le FQDN en recherchant votre service de recherche sur le portail Azure.

   ![Obtenir le FQDN du service](media\search-indexer-howto-secure-access\search-service-portal.png "Obtenir le FQDN du service")

1. Recherchez l’adresse IP du service de recherche en exécutant `nslookup` (ou `ping`) du nom de domaine complet (FQDN) sur une invite de commandes.

1. Copiez l’adresse IP afin de pouvoir la spécifier sur une règle de trafic entrant à l’étape suivante. Dans l’exemple ci-dessous, l’adresse IP que vous devez copier est « 150.0.0.1 ».

   ```azurepowershell
   nslookup contoso.search.windows.net
   Server:  server.example.org
   Address:  10.50.10.50
    
   Non-authoritative answer:
   Name:    <name>
   Address:  150.0.0.1
   aliases:  contoso.search.windows.net
   ```

## <a name="get-ip-addresses-for-azurecognitivesearch-service-tag"></a>Obtenir les adresses IP pour la balise de service « AzureCognitiveSearch »

Selon la configuration de votre service de recherche, vous devrez peut-être également créer une règle de trafic entrant qui admet les requêtes issues d’une plage d’adresses IP. En particulier, des adresses IP supplémentaires sont utilisées pour les requêtes qui proviennent de l’[environnement d’exécution mutualisé](search-indexer-securing-resources.md#indexer-execution-environment) de l’indexeur. 

Vous pouvez récupérer cette plage d’adresses IP à partir de l’étiquette de service `AzureCognitiveSearch`.

1. Récupérez les plages d’adresses IP de l’étiquette de service `AzureCognitiveSearch` avec l’[API de détection](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) ou le [fichier JSON téléchargeable](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

1. Si le service de recherche est le cloud public Azure, le [fichier JSON public Azure](https://www.microsoft.com/download/details.aspx?id=56519) doit être téléchargé.

   ![Télécharger le fichier JSON](media\search-indexer-howto-secure-access\service-tag.png "Télécharger un fichier JSON")

1. Dans le fichier JSON, en supposant que le service de recherche se trouve dans la région USA Centre-Ouest, les adresses IP de l’environnement d’exécution de l’indexeur mutualisé suivantes sont répertoriées.

```json
{
"name": "AzureCognitiveSearch.WestCentralUS",
"id": "AzureCognitiveSearch.WestCentralUS",
"properties": {
   "changeNumber": 1,
   "region": "westcentralus",
   "platform": "Azure",
   "systemService": "AzureCognitiveSearch",
   "addressPrefixes": [
      "52.150.139.0/26",
      "52.253.133.74/32"
   ]
}
}
```

Pour les adresses IP `/32`, supprimez le suffixe « /32 » (52.253.133.74/32 devient 52.253.133.74 dans la définition de la règle). Toutes les autres adresses IP peuvent être utilisées textuellement.

## <a name="add-ip-addresses-to-ip-firewall-rules"></a>Ajouter des adresses IP aux règles de pare-feu IP

Une fois que vous disposez des adresses IP, vous êtes prêt à configurer la règle. Le moyen le plus simple d’ajouter des plages d’adresses IP à la règle de pare-feu d’un compte de stockage consiste à utiliser le portail Azure. 

1. Sur le portail, recherchez le compte de stockage sur et accédez à l’onglet **Pare-feux et réseaux virtuels**.

   ![Pare-feu et réseaux virtuels](media\search-indexer-howto-secure-access\storage-firewall.png "Pare-feu et réseaux virtuels")

1. Ajoutez les trois adresses IP obtenues précédemment (une pour l’adresse IP du service de recherche, deux pour l’étiquette de service `AzureCognitiveSearch`) dans la plage d’adresses, puis cliquez sur **Enregistrer**.

   ![Règles IP de pare-feu](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Règles IP de pare-feu")

La mise à jour des règles de pare-feu prend 5 à 10 minutes après lesquelles les indexeurs accèdent aux données du compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer des pare-feu pour le service Stockage Azure](../storage/common/storage-network-security.md)
- [Configurer un pare-feu IP pour Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Configurer un pare-feu IP pour Azure SQL Server](../azure-sql/database/firewall-configure.md)
