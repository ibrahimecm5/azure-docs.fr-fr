---
title: Instructions pour la résolution des problèmes de l’indexeur
titleSuffix: Azure Cognitive Search
description: Cet article fournit des instructions pour la résolution des problèmes de l’indexeur dans les cas où aucun message d’erreur n’est retourné par la recherche de service.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: d54aec5bae8fb86b9c0ed0356cd6af713500527f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492054"
---
# <a name="indexer-troubleshooting-guidance-for-azure-cognitive-search"></a>Instructions pour la résolution des problèmes de l’indexeur pour la Recherche cognitive Azure

Les indexeurs rencontrent parfois des problèmes et il n’y a pas d’erreur pour aider au diagnostic. Cet article traite des problèmes et des résolutions potentielles lorsque les résultats de l’indexeur sont inattendus et que les informations pour les résoudre sont limitées. Si vous avez une erreur sur laquelle travailler, consultez plutôt [Résolution des erreurs et des avertissements courants de l’indexeur](cognitive-search-common-errors-warnings.md).

## <a name="connection-errors"></a>Erreurs de connexion

> [!NOTE]
> Les indexeurs ont une prise en charge limitée pour accéder aux sources de données et à d’autres ressources sécurisées par les mécanismes de sécurité réseau Azure. Actuellement, les indexeurs peuvent accéder uniquement aux sources de données par le biais de mécanismes de restriction de plage d’adresses IP ou de règles NSG correspondantes, le cas échéant. Vous trouverez ci-dessous des détails sur l’accès à chaque source de données prise en charge.
>
> Vous trouverez l’adresse IP de votre service Search en effectuant un test ping de son nom de domaine complet (p. ex., `<your-search-service-name>.search.windows.net`).
>
> Vous pouvez trouver la plage d’adresses IP de `AzureCognitiveSearch` [l’étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) en utilisant des [fichiers JSON téléchargeables](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) ou via [l’API de détection d’étiquettes de service](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api). La plage d’adresses IP est mise à jour chaque semaine.

### <a name="firewall-rules"></a>Règles de pare-feu

Stockage Azure, Cosmos DB et Azure SQL fournissent un pare-feu configurable. Il n’y a pas de message d’erreur spécifique lorsqu’il est activé. En règle générale, les erreurs de pare-feu sont génériques et se présentent comme `The remote server returned an error: (403) Forbidden` ou `Credentials provided in the connection string are invalid or have expired`.

Vous pouvez utiliser deux options pour autoriser les indexeurs à accéder à ces ressources dans un tel cas :

* Désactiver le pare-feu en choisissant d’autoriser l’accès de **Tous les réseaux** (si possible).

* Vous pouvez également autoriser l’accès à l’adresse IP de votre service Search et à la plage d’adresses IP de `AzureCognitiveSearch` [l’étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) dans les règles de pare-feu de votre ressource (restriction de plage d’adresses IP).

Pour plus d’informations sur la configuration des restrictions de plage d’adresses IP pour chaque type de source de données, consultez les liens suivants :

* [Stockage Azure](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Limitation** : Comme indiqué dans la documentation ci-dessus pour le stockage Azure, les restrictions de plage d’adresses IP ne fonctionnent que si votre service Search et votre compte de stockage se trouvent dans des régions différentes.

Azure Functions (qui peut être utilisé comme une [compétence API web personnalisée](cognitive-search-custom-skill-web-api.md)) prend également en charge les [restrictions d’adresse IP](../azure-functions/ip-addresses.md#ip-address-restrictions). La liste d’adresses IP à configurer correspond à l’adresse IP de votre service Search et à la plage d’adresses IP de `AzureCognitiveSearch` l’étiquette de service.

Pour plus d’informations sur la connexion à une machine virtuelle, consultez [Configurer une connexion à SQL Server sur une machine virtuelle Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Configurer les règles du groupe de sécurité réseau (NSG)

Lors de l’accès aux données d’une instance gérée SQL, ou lorsqu’une machine virtuelle Azure est utilisée comme URI de service we pour une [compétence API web personnalisée](cognitive-search-custom-skill-web-api.md), les clients n’ont pas à se préoccuper des adresses IP spécifiques.

Dans ce cas, la machine virtuelle Azure ou l’instance gérée SQL peut être configurée pour résider dans un réseau virtuel. Ensuite, le groupe de sécurité réseau peut être configuré pour filtrer le type de trafic réseau pouvant circuler vers et depuis les interfaces réseau et les sous-réseaux de réseau virtuel.

`AzureCognitiveSearch` L’étiquette de service peut être utilisée directement dans les [règles de groupe de sécurité réseau](../virtual-network/manage-network-security-group.md#work-with-security-rules) entrantes sans avoir besoin de rechercher sa plage d’adresses IP.

Pour plus d’informations sur l’accès aux données dans une instance managée SQL, cliquez [ici](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="azure-sql-database-serverless-indexing-error-code-40613"></a>Indexation serverless Azure SQL Database (code d’erreur 40613)

Si votre base de données SQL est un sur un [niveau de calcul serverless](../azure-sql/database/serverless-tier-overview.md), assurez-vous que la base de données est en cours d’exécution (et non en pause) lorsque l’indexeur se connecte à celle-ci.

Si la base de données est suspendue, la première connexion de votre service de recherche réactivera automatiquement la base de données, mais renverra également une erreur indiquant que la base de données n’est pas disponible avec le code d’erreur 40613. Une fois la base de données en cours d’exécution, relancez la connexion pour établir la connectivité.

## <a name="sharepoint-online-conditional-access-policies"></a>Stratégies d’accès conditionnel SharePoint Online

Lors de la création d’un indexeur SharePoint Online, vous allez effectuer une étape qui vous oblige à vous connecter à votre application Azure AD après avoir fourni un code d’appareil. Si vous recevez un message indiquant `"Your sign-in was successful but your admin requires the device requesting access to be managed"`, il est probable que l’indexeur ne peut pas accéder à la bibliothèque de documents SharePoint Online en raison d’une stratégie d’[accès conditionnel](../active-directory/conditional-access/overview.md).

Pour mettre à jour la stratégie afin d’autoriser l’indexeur à accéder à la bibliothèque de documents, suivez les étapes ci-dessous :

1. Ouvrez le Portail Azure et recherchez **Accès conditionnel Azure AD**, puis sélectionnez **Stratégies** dans le menu de gauche. Si vous n’avez pas accès à cette page, vous devez trouver une personne disposant d’un accès ou demander un accès.

1. Déterminez quelle stratégie empêche l’indexeur SharePoint Online d’accéder à la bibliothèque de documents. La stratégie susceptible de bloquer l’indexeur inclura le compte d’utilisateur que vous avez utilisé pour l’authentification lors de l’étape de création de l’indexeur dans la section **Utilisateurs et groupes**. La stratégie peut également avoir des **conditions** qui :
    * Limitent les plateformes **Windows**.
    * Limitent les **Applications mobiles et clients de bureau**.
    * Ont l’**État de l’appareil** configuré sur **Oui**.

1. Une fois que vous avez confirmé qu’une stratégie bloque l’indexeur, vous devez ensuite créer une exemption pour l’indexeur. Récupérez l’adresse IP du service de recherche.

    1. Obtenez le nom de domaine complet (FQDN) de votre service de recherche. Il ressemble à ceci : `<search-service-name>.search.windows.net`. Vous pouvez déterminer le FQDN en recherchant votre service de recherche sur le portail Azure.

   ![Obtenir le FQDN du service](media\search-indexer-howto-secure-access\search-service-portal.png "Obtenir le FQDN du service")

    Vous pouvez obtenir l’adresse IP du service de recherche en exécutant une commande `nslookup` (ou `ping`) du FQDN. Dans l’exemple ci-dessous, vous devez ajouter « 150.0.0.1 » à une règle de trafic entrant sur le Pare-feu de Stockage Azure. L’accès au compte de stockage Azure peut prendre jusqu’à 15 minutes après la mise à jour des paramètres du Pare-feu.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Récupérez les plages d’adresses IP pour l’environnement d’exécution de l’indexeur pour votre région.

    Des adresses IP supplémentaires sont utilisées pour les requêtes qui proviennent de l’[environnement d’exécution mutualisé](search-indexer-securing-resources.md#indexer-execution-environment) de l’indexeur. Vous pouvez récupérer cette plage d’adresses IP à partir de l’étiquette de service.

    Vous pouvez obtenir les plages d’adresses IP de l’étiquette de service `AzureCognitiveSearch` avec l’[API de détection](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) ou le [fichier JSON téléchargeable](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    Pour cette procédure pas à pas, en supposant que le service de recherche est le cloud public Azure, le [fichier JSON public Azure](https://www.microsoft.com/download/details.aspx?id=56519) doit être téléchargé.

   ![Télécharger le fichier JSON](media\search-indexer-troubleshooting\service-tag.png "Télécharger un fichier JSON")

    Dans le fichier JSON, en supposant que le service de recherche se trouve dans la région USA Centre-Ouest, les adresses IP de l’environnement d’exécution de l’indexeur mutualisé suivantes sont répertoriées.

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

1. Retournez sur la page Accès conditionnel dans le portail Azure, sélectionnez **Emplacements nommés** dans le menu de gauche, puis choisissez **+ Emplacement des plages d’adresses IP**. Donnez un nom à votre nouvel emplacement nommé et ajoutez les plages d’adresses IP pour votre service de recherche et les environnements d’exécution de l’indexeur que vous avez collectés au cours des deux dernières étapes.
    * Pour l’adresse IP de votre service de recherche, vous devrez peut-être ajouter « /32 » à la fin de l’adresse IP, car le système n’accepte que des plages d’adresses IP valides.
    * N’oubliez pas que pour les plages d’adresses IP de l’environnement d’exécution de l’indexeur, vous devez uniquement ajouter les plages d’adresses IP pour la région dans laquelle se trouve votre service de recherche.

1. Excluez le nouvel emplacement nommé de la stratégie. 
    1. Sélectionnez **Stratégies** dans le menu de gauche. 
    1. Sélectionnez la stratégie qui bloque l’indexeur.
    1. Sélectionnez **Conditions**.
    1. Sélectionner **Emplacements**.
    1. Sélectionnez **Exclure**, puis ajoutez le nouvel emplacement nommé.
    1. **Enregistrez** les modifications.

1. Attendez quelques minutes pour que la stratégie se mette à jour et applique les nouvelles règles de stratégie.

1. Tentative de recréation de l’indexeur
    1. Envoyez une requête de mise à jour pour l’objet source de données que vous avez créé.
    1. Renvoyez la requête de création de l’indexeur. Utilisez le nouveau code pour vous connecter, puis envoyez une autre requête de création d’indexeur.

## <a name="indexing-unsupported-document-types"></a>Indexation de types de documents non pris en charge

Si vous indexez du contenu de Stockage Blob Azure et que le conteneur comprend des objets blob d’un [type de contenu non pris en charge](search-howto-indexing-azure-blob-storage.md#SupportedFormats), l’indexeur ignore ce document. Dans d’autres cas, des problèmes avec des documents individuels peuvent se produire. 

Vous pouvez [définir des options de configuration](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) pour permettre au traitement de l’indexeur de continuer en cas de problèmes avec des documents individuels.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

## <a name="missing-documents"></a>Documents manquants

Les indexeurs extraient des documents ou des lignes d’une [source de données](/rest/api/searchservice/create-data-source) externe et créent des *documents de recherche* qui sont alors indexés par le service de recherche. Parfois, un document existant dans la source de données n’apparaît pas dans un index de recherche. Ce résultat inattendu peut être dû aux raisons suivantes :

* Le document a été mis à jour après l’exécution de l’indexeur. Si votre indexeur suit une [planification](/rest/api/searchservice/create-indexer#indexer-schedule), il s’exécutera à nouveau et trouvera le document.
* Le délai de l’indexeur a expiré avant l’ingestion du document. Des [limites de temps de traitement maximum](search-limits-quotas-capacity.md#indexer-limits) après lesquelles aucun document ne sera traité existent. Vous pouvez surveiller l’état de l’indexeur dans le portail ou en appelant [Obtenir l’état de l’indexeur (API REST)](/rest/api/searchservice/get-indexer-status).
* Des [mappages de champs](/rest/api/searchservice/create-indexer#fieldmappings) ou un [enrichissement par IA](./cognitive-search-concept-intro.md) ont modifié le document et son articulation dans l’index de recherche est différente que ce à quoi vous vous attendiez.
* Les valeurs de [suivi des modifications](/rest/api/searchservice/create-data-source#data-change-detection-policies) sont erronées ou des prérequis sont manquants. Si la valeur de la limite supérieure est une date future, tous les documents dont la date est antérieure à celle-ci sont ignorés par l’indexeur. Vous pouvez comprendre l’état de suivi des modifications de votre indexeur à l’aide des champs « initialTrackingState » et « finalTrackingState » dans l’[état de l’indexeur](/rest/api/searchservice/get-indexer-status#indexer-execution-result). Les indexeurs pour Azure SQL et MySQL doivent avoir un index dans la colonne de limite supérieure de la table source, sinon les requêtes utilisées par l’indexeur risquent d’expirer. 

> [!TIP]
> Si des documents sont manquants, vérifiez la [requête](/rest/api/searchservice/search-documents) que vous utilisez pour vous assurer qu’elle n’exclut pas le document concerné. Pour rechercher un document spécifique, utilisez l'[API REST de document de recherche](/rest/api/searchservice/lookup-document).

## <a name="missing-content-from-blob-storage"></a>Contenu manquant de Stockage Blob

L’indexeur d’objets blob [recherche et extrait du texte dans les objets blob d’un conteneur](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). L’extraction de texte peut poser les problèmes suivants :

* Le document ne contient que des images numérisées. Les objets blob PDF comportant du contenu non textuel, comme des images numérisées (JPG), ne produisent pas de résultats dans un pipeline d’indexation blob standard. Si vous avez du contenu d’image comportant des éléments textuels, vous pouvez utiliser la [recherche cognitive](cognitive-search-concept-image-scenarios.md) pour rechercher et extraire le texte.

* L’indexeur d’objets blob est configuré pour indexer uniquement les métadonnées. Pour extraire le contenu, il doit être configuré de façon à [extraire à la fois le contenu et les métadonnées](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex) :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="missing-content-from-cosmos-db"></a>Contenu manquant de Cosmos DB

La Recherche cognitive Azure comporte une dépendance implicite vis-à-vis de l’indexation Cosmos DB. Si l’indexation automatique est désactivée dans Cosmos DB, la Recherche cognitive Azure renvoie un état réussi, mais ne parvient pas à indexer le contenu du conteneur. Pour savoir comment vérifier les paramètres et activer l’indexation, voir [Gérer l’indexation dans Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

## <a name="documents-processed-multiple-times"></a>Documents traités plusieurs fois

Les indexeurs s’appuient sur une stratégie de mise en mémoire tampon conservatrice afin de garantir que chaque document nouveau ou modifié dans la source de données est pris en compte lors de l’indexation. Dans certaines situations, ces mémoires tampon peuvent se chevaucher, ce qui fait qu’un indexeur indexe un document deux fois ou plus, et que le nombre de documents traités est supérieur au nombre réel de documents dans la source de données. Ce comportement n’affecte **pas** les données stockées dans l’index, comme la duplication des documents, mais il peut allonger le délai nécessaire pour atteindre la cohérence finale. Cette situation peut être particulièrement prévalente si l’une des conditions suivantes est vraie :

- Les demandes d’indexation à la demande sont émises et se suivent rapidement
- La topologie de la source de données inclut plusieurs réplicas et partitions (un exemple de ce type est présenté [ici](https://docs.microsoft.com/azure/cosmos-db/consistency-levels))

Les indexeurs ne sont pas destinés à être invoqués plusieurs fois de suite et rapidement. Si vous avez besoin de mises à jour rapides, l’approche recommandée consiste à envoyer les mises à jour vers l’index tout en mettant simultanément à jour la source de données. Pour un traitement à la demande, nous vous recommandons d’échelonner vos demandes par intervalles de cinq minutes ou plus, puis d’exécuter l’indexeur de manière planifiée.

### <a name="example-of-duplicate-document-processing-with-30-second-buffer"></a>Exemple de traitement de documents en double avec une mémoire tampon de 30 secondes
Les conditions dans lesquelles un document est traité deux fois sont expliquées ci-dessous dans une chronologie qui consigne chaque action et contre-action. La chronologie suivante illustre le problème :

| Chronologie (hh:mm:ss) | Événement | Limite supérieure de l’indexeur | Commentaire |
|---------------------|-------|-------------------------|---------|
| 00:01:00 | Écrire `doc1` dans la source de données avec une cohérence finale | `null` | L’horodatage du document est 00:01:00. |
| 00:01:05 | Écrire `doc2` dans la source de données avec une cohérence finale | `null` | L’horodatage du document est 00:01:05. |
| 00:01:10 | Lancement de l’indexeur | `null` | |
| 00:01:11 | Requêtes de l’indexeur pour toutes les modifications avant 00:01:10 ; le réplica que l’indexeur interroge est le seul à connaître `doc2` ; récupère `doc2` uniquement | `null` | L’indexeur demande toutes les modifications avant de commencer l’horodatage mais reçoit en fait un sous-ensemble. Ce comportement nécessite la mémoire tampon des périodes passées. |
| 00:01:12 | L’indexeur traite `doc2` pour la première fois | `null` | |
| 00:01:13 | Fin de l’indexeur | 00:01:10 | La limite supérieure est mise à jour avec l’horodatage de l’exécution de l'indexeur actuel. |
| 00:01:20 | Lancement de l’indexeur | 00:01:10 | |
| 00:01:21 | Requêtes de l’indexeur pour toutes les modifications entre 00:00:40 et 00:01:20 ; le réplica que l’indexeur interroge est le seul à connaître `doc1` et `doc2` ; récupère `doc1` et `doc2` uniquement | 00:01:10 | Demandes de l’indexeur pour toutes les modifications entre la limite supérieure actuelle moins la mémoire tampon de 30 secondes et l’horodatage de début d’exécution de l’indexeur actuel. |
| 00:01:22 | L’indexeur traite `doc1` pour la première fois | 00:01:10 | |
| 00:01:23 | L’indexeur traite `doc2` pour la deuxième fois | 00:01:10 | |
| 00:01:24 | Fin de l’indexeur | 00:01:20 | La limite supérieure est mise à jour avec l’horodatage de l’exécution de l'indexeur actuel. |
| 00:01:32 | Lancement de l’indexeur | 00:01:20 | |
| 00:01:33 | Requêtes de l’indexeur pour toutes les modifications entre 00:00:50 et 00:01:32 ; récupère `doc1` et `doc2` | 00:01:20 | Demandes de l’indexeur pour toutes les modifications entre la limite supérieure actuelle moins la mémoire tampon de 30 secondes et l’horodatage de début d’exécution de l’indexeur actuel. |
| 00:01:34 | L’indexeur traite `doc1` pour la deuxième fois | 00:01:20 | |
| 00:01:35 | L’indexeur traite `doc2` pour la troisième fois | 00:01:20 | |
| 00:01:36 | Fin de l’indexeur | 00:01:32 | La limite supérieure est mise à jour avec l’horodatage de l’exécution de l'indexeur actuel. |
| 00:01:40 | Lancement de l’indexeur | 00:01:32 | |
| 00:01:41 | Requêtes de l’indexeur pour toutes les modifications entre 00:01:02 et 00:01:40 ; récupère `doc2` | 00:01:32 | Demandes de l’indexeur pour toutes les modifications entre la limite supérieure actuelle moins la mémoire tampon de 30 secondes et l’horodatage de début d’exécution de l’indexeur actuel. |
| 00:01:42 | L’indexeur traite `doc2` pour la quatrième fois | 00:01:32 | |
| 00:01:43 | Fin de l’indexeur | 00:01:40 | Notez que l’exécution de cet indexeur a commencé plus de 30 secondes après la dernière écriture dans la source de données et a également traité `doc2`. Il s’agit du comportement attendu, car si toutes les exécutions de l'indexeur avant 00:01:35 sont éliminées, celle-ci deviendra la première et seule exécution à traiter `doc1` et `doc2`. |

En pratique, ce scénario ne se produit que lorsque des indexeurs à la demande sont invoqués manuellement à quelques minutes d'intervalle, pour certaines sources de données. Il peut en résulter des chiffres erronés (par exemple, l'indexeur a traité 345 documents au total selon ses statistiques d’exécution, mais il y a 340 documents dans la source de données et l’index), ou une facturation potentiellement accrue si vous exécutez plusieurs fois les mêmes compétences pour le même document. L’exécution planifiée d’un indexeur est la meilleure approche.

## <a name="see-also"></a>Voir aussi

* [Résolution des erreurs et des avertissements courants de l’indexeur](cognitive-search-common-errors-warnings.md)
* [Superviser l’indexation basée sur un indexeur](search-howto-monitor-indexers.md)
