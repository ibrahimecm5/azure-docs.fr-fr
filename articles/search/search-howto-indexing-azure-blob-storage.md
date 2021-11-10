---
title: Indexer des données à partir du Stockage Blob Azure
titleSuffix: Azure Cognitive Search
description: Configurez un indexeur de blobs Azure pour automatiser l’indexation du contenu blob pour les opérations de recherche en texte intégral et l’exploration de connaissances dans la Recherche cognitive.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/14/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: f4f90b83a21eb85a9ccc3ae11de4582d50ab2363
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892207"
---
# <a name="index-data-from-azure-blob-storage"></a>Indexer des données à partir du Stockage Blob Azure

Cet article vous montre comment configurer l’indexeur Azure blob pour extraire le contenu et le rendre détectable dans la Recherche cognitive. Ce workflow crée un index de recherche sur la Recherche cognitive et le charge avec le contenu existant et les métadonnées extraites du Stockage Blob Azure.

Des indexeurs blob sont souvent utilisés dans un [enrichissement par IA](cognitive-search-concept-intro.md), où un [ensemble de compétences](cognitive-search-working-with-skillsets.md) attaché ajoute un traitement d’image et du langage naturel pour créer du contenu pouvant faire l’objet d’une recherche des types de contenu sans possibilité de recherche dans des conteneurs blobs.

Cet article explique comment configurer un indexeur blob Azure pour l’indexation centrée sur le texte. Vous pouvez configurer un indexeur de Stockage Blob Azure à l’aide de l’un des clients suivants :

* [Azure portal](https://ms.portal.azure.com)
* [API REST](/rest/api/searchservice/Indexer-operations) de Recherche cognitive Azure
* [Kit de développement logiciel (SDK) .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexer) de Recherche cognitive Azure

Cet article utilise les API REST. 

## <a name="supported-access-tiers"></a>Niveaux d’accès pris en charge

Les [niveaux d’accès](../storage/blobs/access-tiers-overview.md) au service Stockage Blob sont chaud, froid et archive. Les indexeurs ne peuvent accéder qu’aux niveaux chaud et froid. 

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Formats de document pris en charge

L’indexeur de blobs de Recherche cognitive Azure peut extraire du texte à partir des formats de document suivants :

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>Définitions de sources de données

La principale différence entre un indexeur de blobs et tout autre indexeur est la définition de la source de données attribuée à l’indexeur. La définition de la source de données spécifie le type de source de données (« type » : « azureblob »), ainsi que d’autres propriétés d’authentification et de connexion au contenu à indexer.

La définition d’une source de données blob ressemble à l’exemple ci-dessous :

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

La propriété `"credentials"` peut être une chaîne de connexion, comme indiqué dans l’exemple ci-dessus, ou l’une des autres approches décrites dans la section suivante. La propriété `"container"` fournit l’emplacement du contenu dans Stockage Azure, et `"query"` est utilisée pour spécifier un sous-dossier dans le conteneur. Pour plus d’informations sur les définitions de source de données, consultez [Créer une source de données (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Informations d'identification

Vous pouvez fournir les informations d’identification du conteneur d’objets blob de l’une des manières suivantes :

**Chaîne de connexion d’identité managée** : `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Cette chaîne de connexion ne nécessite pas de clé de compte, mais vous devez suivre les instructions fournies dans [Configurer une connexion à un compte Stockage Azure à l’aide d’une identité managée](search-howto-managed-identities-storage.md).

**Chaîne de connexion au compte de stockage avec accès complet** : `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Vous pouvez obtenir la chaîne de connexion sur le Portail Azure en sélectionnant vers le panneau du compte de stockage > Paramètres > Clés (pour les comptes de stockage Classic) ou Sécurité + mise en réseau > Clés d’accès (pour les comptes de stockage Azure Resource Manager).

Chaîne de connexion de la **signature d’accès partagé (SAP) au compte de stockage** : `{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

La SAP doit avoir les autorisations de liste et de lecture sur les conteneurs et les objets (blob en l’occurrence).

**Signature d’accès partagé du conteneur** : `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

La SAP doit avoir les autorisations de liste et lecture sur le conteneur. Pour plus d’informations sur les signatures d’accès partagé au stockage, consultez [Utilisation des signatures d’accès partagé](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Si vous utilisez des informations d’identification d’une SAP, vous devez mettre à jour les informations d’identification de la source de données régulièrement avec des signatures renouvelées afin d’éviter leur expiration. Si les informations d’identification de la SAP expirent, l’indexeur échoue avec un message d’erreur tel que « Les informations d’identification fournies dans la chaîne de connexion sont invalides ou ont expiré. »  

## <a name="index-definitions"></a>Définitions d’index

L’index spécifie les champs d’un document, les attributs et d’autres constructions qui façonnent l’expérience de recherche. Tous les indexeurs requièrent que vous spécifiiez une définition d’index de recherche comme destination. L’exemple suivant crée un index simple à l’aide de l’API [Créer un index (API REST)](/rest/api/searchservice/create-index). 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

Les définitions d’index requièrent qu’un champ de la collection `"fields"` fasse office de clé de document. Les définitions d’index doivent également inclure des champs pour le contenu et les métadonnées.

Un champ **`content`** est commun au contenu des blobs. Il contient le texte extrait des blobs. Votre définition de ce champ peut ressembler à celle ci-dessus. Vous n’êtes pas obligé d’utiliser ce nom, mais le faire vous permet de tirer parti des mappages de champs implicites. L’indexeur de blobs peut envoyer du contenu de blob à un champ Edm.String de contenu dans l’index, sans qu’aucun mappage de champ soit requis.

Vous pouvez également ajouter des champs pour toutes les métadonnées de blob que vous souhaitez dans l’index. L’indexeur peut lire les propriétés des métadonnées personnalisées, celles des [métadonnées standard](#indexing-blob-metadata) et celles des [métadonnées spécifiques](search-blob-metadata-properties.md) au contenu. Pour plus d’informations sur les index, consultez [Créer un index](search-what-is-an-index.md).

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Définition des clés de document et des mappages de champs

Dans un index de recherche, la clé de document identifie de façon unique chaque document. Le champ que vous choisissez doit être de type `Edm.String`. Pour le contenu des blobs, les meilleurs candidats pour une clé de document sont les propriétés des métadonnées sur le blob.

+ **`metadata_storage_name`**  : Cette propriété est candidate, mais uniquement si les noms sont uniques dans tous les conteneurs et dossiers que vous indexez. Quel que soit l’emplacement du blob, le résultat final est que la clé de document (nom) doit être unique dans l’index de recherche une fois que tout le contenu a été indexé. 

  Un autre problème potentiel concernant le nom de stockage est qu’il peut contenir des caractères non valides pour les clés de document, tels que les tirets. Vous pouvez traiter les caractères non valides en utilisant la [fonction de mappage des champs](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. Si vous procédez ainsi, n’oubliez pas de coder également les clés de document lors de leur transmission dans des appels d’API tels que [Recherche de document (REST)](/rest/api/searchservice/lookup-document). Dans .NET, vous pouvez utiliser la [méthode UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) pour encoder des caractères.

+ **`metadata_storage_path`**  : L’utilisation du chemin d’accès complet garantit l’unicité, mais le chemin d’accès contient invariablement des caractères `/` qui [ne sont pas valides dans une clé de document](/rest/api/searchservice/naming-rules). Comme ci-dessus, vous pouvez utiliser la [fonction](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode` pour encoder des caractères.

+ Une troisième option consiste à ajouter une propriété de métadonnées personnalisée aux blobs. Cette option contraint votre processus de chargement de blob à ajouter cette propriété de métadonnées à tous les blobs. Étant donné que la clé est une propriété obligatoire, les blobs auxquels il manque une valeur ne seront pas indexés.

> [!IMPORTANT]
> En l’absence de mappage explicite pour le champ de clé dans l’index, la Recherche cognitive Azure utilise automatiquement `metadata_storage_path` en guise de clé et encode les valeurs de clés en base 64 (la deuxième option ci-dessus).
>
> Si vous utilisez une propriété de métadonnées personnalisée comme clé, évitez d’apporter des modifications à cette propriété. Les indexeurs ajoutent des documents en double pour le même objet blob si la propriété de clé est modifiée.

#### <a name="example"></a>Exemple

L’exemple suivant montre `metadata_storage_name` comme clé de document. Supposons que l’index possède un champ de clé nommé `key` et un autre champ nommé `fileSize` pour stocker la taille du document. Les [mappages de champs](search-indexer-field-mappings.md) dans la définition de l’indexeur établissent des associations de champs, et `metadata_storage_name` a la [fonction de mappage des champs `base64Encode`](search-indexer-field-mappings.md#base64EncodeFunction) pour gérer les caractères non pris en charge.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>Comment faire en sorte qu’un champ encodé soit « recherchable »

Il peut arriver que vous deviez utiliser une version encodée d’un champ tel que `metadata_storage_path` en tant que clé, et que vous ayez également besoin de pouvoir rechercher ce champ (sans encodage) dans l’index de recherche. Pour prendre en charge les deux cas d’usage, vous pouvez mapper `metadata_storage_path` à deux champs : un pour la clé (encodé), et un second pour un champ de chemin d’accès dont on peut supposer qu’il est attribué comme « possibilité de recherche » dans le schéma d’index. L’exemple ci-dessous montre deux mappages de champs pour `metadata_storage_path`.

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>Indexer le contenu et les métadonnées

Les blobs contiennent du contenu et des métadonnées. Vous pouvez contrôler les parties des objets blob à indexer à l’aide du paramètre de configuration `dataToExtract`. Il peut avoir les valeurs suivantes :

+ `contentAndMetadata` : spécifie que toutes les métadonnées et tous les contenus textuels extraits de l’objet blob sont indexés. Il s’agit de la valeur par défaut.

+ `storageMetadata` : spécifie que seuls les propriétés standard [ et les métadonnées ](../storage/blobs/storage-blob-container-properties-metadata.md) spécifiées par l’utilisateur sont indexés.

+ `allMetadata` : spécifie que les propriétés de blob standard et toutes les [métadonnées pour les types de contenu trouvés](search-blob-metadata-properties.md) sont extraites du contenu du blob et indexées.

Par exemple, pour indexer uniquement les métadonnées de stockage, utilisez :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>Indexation du contenu des blobs

Par défaut, les blobs avec contenu structuré, tels que JSON ou CSV, sont indexés en tant que bloc de texte unique. Toutefois, si les documents JSON ou CSV ont une structure interne (délimiteurs), vous pouvez assigner des modes d’analyse pour générer des documents de recherche individuels pour chaque ligne ou élément. Pour plus d’informations, consultez [Indexation de blobs JSON](search-howto-index-json-blobs.md) et [Indexation de blobs CSV](search-howto-index-csv-blobs.md).

Un document composé ou incorporé (tel qu’une archive ZIP ou un document Word avec e-mail Outlook incorporé intégrant des pièces jointes, ou un fichier .MSG avec des pièces jointes) est également indexé en tant que document unique. Par exemple, toutes les images extraites des pièces jointes d’un fichier .MSG seront renvoyées dans le champ normalized_images.

Le contenu de texte du document est extrait dans un champ de chaîne nommé `content`.

  > [!NOTE]
  > Recherche cognitive Azure limite la quantité de texte extrait en fonction du niveau tarifaire. Les [limites de service](search-limits-quotas-capacity.md#indexer-limits) actuelles sont 32 000 caractères pour le niveau Gratuit, 64 000 pour le niveau De base, 4 millions pour le niveau Standard, 8 millions pour le niveau Standard S2 et 16 millions pour le niveau Standard S3. Un avertissement est inclus dans la réponse d’état de l’indexeur pour les documents tronqués.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>Indexation de métadonnées blob

Les indexeurs peuvent également indexer des métadonnées blob. Tout d’abord, toutes les propriétés de métadonnées spécifiées par l’utilisateur peuvent être extraites textuellement. Pour recevoir les valeurs, vous devez définir le champ dans l’index de recherche de type `Edm.String`, avec le même nom que la clé de métadonnées du blob. Par exemple, si un blob a une clé de métadonnées de `Sensitivity` avec la valeur `High`, vous devez définir un champ nommé `Sensitivity` dans votre index de recherche et il sera rempli avec la valeur `High`.

Ensuite, les propriétés standard de métadonnées blob peuvent être extraites dans les champs listés ci-dessous. L’indexeur de blobs crée automatiquement des mappages de champs internes pour ces propriétés de métadonnées blob. Vous devez toujours ajouter les champs que vous souhaitez utiliser dans la définition de l’index, mais vous pouvez omettre de créer des mappages de champs dans l’indexeur.

  + **metadata_storage_name** (`Edm.String`) : nom de fichier du blob. Par exemple, si vous disposez de l’objet blob /my-container/my-folder/subfolder/resume.pdf, ce champ présente la valeur `resume.pdf`.

  + **metadata_storage_path** (`Edm.String`) : URI complet du blob, incluant le compte de stockage. Par exemple : `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** (`Edm.String`) : type de contenu tel que spécifié par le code que vous avez utilisé pour charger le blob. Par exemple : `application/octet-stream`.

  + **metadata_storage_last_modified** (`Edm.DateTimeOffset`) : horodatage de la dernière modification du blob. La Recherche cognitive Azure utilise cet horodateur pour identifier les objets blob modifiés afin d’éviter une réindexation complète après l’indexation initiale.

  + **metadata_storage_size** (`Edm.Int64`) : taille du blob en octets.

  + **metadata_storage_content_md5** (`Edm.String`) : code de hachage MD5 du contenu du blob s’il est disponible.

  + **metadata_storage_sas_token** (`Edm.String`) : jeton SAP temporaire qui peut être utilisé par des [compétences personnalisées](cognitive-search-custom-skill-interface.md) pour accéder au blob. Ce jeton ne doit pas être stocké pour une utilisation ultérieure dans la mesure où il peut expirer.

Enfin, toutes les propriétés de métadonnées spécifiques au format de document des blobs que vous indexez peuvent également être représentées dans le schéma d’index. Pour plus d’informations sur les métadonnées spécifiques au contenu, consultez [Propriétés des métadonnées de contenu](search-blob-metadata-properties.md).

Il est important de souligner que vous n’avez pas besoin de définir les champs relatifs à chacune des propriétés ci-dessus dans votre index de recherche. Il vous suffit de capturer les propriétés dont vous avez besoin pour votre application.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Comment contrôler les blobs indexés

Vous pouvez contrôler quels blobs sont indexés, et lesquels sont ignorés, par le type de fichier du blob ou en définissant des propriétés sur le blob proprement dit, ce qui fait que l’indexeur les ignore.

### <a name="include-specific-file-extensions"></a>Inclure des extensions de fichier spécifiques

Utilisez `indexedFileNameExtensions` pour fournir une liste séparée par des virgules des extensions de fichier à indexer (avec un point de début). Par exemple, pour indexer uniquement les objets blob .PDF et .DOCX, procédez comme suit :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Exclure des extensions de fichier spécifiques

Utilisez `excludedFileNameExtensions` pour fournir une liste séparée par des virgules des extensions de fichier à ignorer (encore une fois, avec un point de début). Par exemple, pour indexer tous les objets blob, sauf ceux qui ont les extensions .PNG et .JPEG, procédez comme suit :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Si les paramètres `indexedFileNameExtensions` et `excludedFileNameExtensions` sont tous deux présents, l’indexeur examine d’abord `indexedFileNameExtensions`, puis `excludedFileNameExtensions`. Si la même extension de fichier figure dans les deux listes, elle est exclue de l’indexation.

### <a name="add-skip-metadata-the-blob"></a>Ajouter une fonctionnalité « ignorer » des métadonnées au blob

Les paramètres de configuration de l’indexeur s’appliquent à tous les blobs du conteneur ou du dossier. Parfois, vous souhaitez contrôler la manière dont *différents blobs* sont indexés. Pour cela, vous pouvez ajouter les propriétés et valeurs de métadonnées suivantes aux blobs dans Stockage Blob. Lorsque l’indexeur rencontre ces propriétés, il ignore le blob ou son contenu dans l’exécution de l’indexation.

| Nom de la propriété | Valeur de la propriété | Explication |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Indique à l’indexeur d’objets blob d’ignorer complètement l’objet blob. Ni l’extraction des métadonnées, ni l’extraction de contenu n’est tentée. Cette propriété est utile lorsqu’un objet blob spécifique échoue à plusieurs reprises et interrompt le processus d’indexation. |
| `AzureSearch_SkipContent` |`"true"` |Équivaut au réglage `"dataToExtract" : "allMetadata"` décrit [ci-dessus](#PartsOfBlobToIndex) au niveau d’un objet blob donné. |

## <a name="index-large-datasets"></a>Indexer des jeux de données volumineux

L’indexation d’objets blob peut être un processus long. Dans le cas où vous avez des millions de blobs à indexer, vous pouvez accélérer l’indexation en partitionnant les données et en utilisant plusieurs indexeurs pour [traiter les données en parallèle](search-howto-large-index.md#parallel-indexing). Par exemple, vous pouvez effectuer la configuration suivante :

+ Partitionnez les données dans plusieurs conteneurs d’objets blob ou des dossiers virtuels.

+ Configurez plusieurs sources de données, une par conteneur ou dossier. Pour pointer vers un dossier d’objets blob, utilisez le paramètre `query` :

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ Créez un indexeur correspondant pour chaque source de données. Tous les indexeurs doivent pointer vers le même index de recherche cible.  

+ Une unité de recherche dans votre service peut exécuter un indexeur à tout moment donné. La création de plusieurs indexeurs comme décrit ci-dessus est utile uniquement s’ils s’exécutent en parallèle.

  Pour exécuter plusieurs indexeurs en parallèle, effectuez un scale-out de votre service de recherche en créant un nombre approprié de partitions et réplicas. Par exemple, si votre service de recherche a 6 unités de recherche (2 partitions x 3 réplicas), 6 indexeurs peuvent s’exécuter simultanément, ce qui augmente le débit d’indexation par six. Pour en savoir plus sur la mise à l’échelle et la planification de capacité, consultez [Ajuster la capacité d’un service Recherche cognitive Azure](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Gestion des erreurs

Les erreurs qui se produisent généralement pendant l’indexation incluent les types de contenu non pris en charge, le contenu manquant ou les blobs surdimensionnés.

Par défaut, l’indexeur d’objets blob s’arrête dès qu’il rencontre un objet blob avec un type de contenu non pris en charge (par exemple, une image). Vous pouvez utiliser le paramètre `excludedFileNameExtensions` pour ignorer certains types de contenu. Toutefois, vous pourriez vouloir procéder à l’indexation même si des erreurs se produisent, puis déboguer les documents individuels plus tard. Pour plus d’informations sur les erreurs de l’indexeur, consultez [Guide de résolution des problèmes de l’indexeur](search-indexer-troubleshooting.md) et [Erreurs et avertissements de l’indexeur](cognitive-search-common-errors-warnings.md).

### <a name="respond-to-errors"></a>Répondre aux erreurs

Il existe quatre propriétés d’indexeur qui contrôlent la réponse de l’indexeur lorsque des erreurs se produisent. Les exemples suivants montrent comment définir ces propriétés dans la définition de l’indexeur. Si un indexeur existe déjà, vous pouvez ajouter ces propriétés en modifiant la définition dans le portail.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` et `"maxFailedItemsPerBatch"`

Poursuivez l’indexation si des erreurs se produisent à un moment quelconque du traitement, que ce soit durant l’analyse de blobs ou l’ajout de documents à un index. Définissez ces propriétés sur le nombre d’échecs acceptables. La valeur `-1` permet le traitement, quel que soit le nombre d’erreurs qui se produisent. Dans le cas contraire, la valeur est un entier positif.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` et `"failOnUnprocessableDocument"` 

Pour certains blobs, Recherche cognitive Azure ne parvient pas à déterminer le type de contenu ou à traiter un document avec un autre type de contenu pris en charge. Pour ignorer ces conditions d’échec, définissez les paramètres de configuration sur `false` :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>Assouplir les contraintes de l’indexeur

Vous pouvez également définir des [propriétés de configuration de blobs](/rest/api/searchservice/create-indexer#blob-configuration-parameters) qui déterminent efficacement si une condition d’erreur existe. La propriété suivante peut assouplir les contraintes, en supprimant les erreurs qui se produiraient autrement.

+ `"indexStorageMetadataOnlyForOversizedDocuments"` pour indexer les métadonnées de stockage d’un contenu de blob trop volumineux à traiter. Par défaut, les objets blob surdimensionnés sont traités comme des erreurs. Pour connaître les limites de taille des blobs, consultez [Limites du service](search-limits-quotas-capacity.md).

## <a name="see-also"></a>Voir également

+ [Indexeurs dans Recherche cognitive Azure](search-indexer-overview.md)
+ [Créer un indexeur](search-howto-create-indexers.md)
+ [Présentation de l’enrichissement par IA sur les blobs](search-blob-ai-integration.md)
+ [Présentation de la recherche sur les blobs](search-blob-storage-integration.md)