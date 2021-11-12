---
title: Indexer les données d’Azure Files (préversion)
titleSuffix: Azure Cognitive Search
description: Configurez un indexeur Azure Files pour automatiser l’indexation des partages de fichiers dans Recherche cognitive Azure.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 94f1a1760c02401bbca04ac01af5dd9b10093311
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511399"
---
# <a name="index-data-from-azure-files"></a>Indexer les données d’Azure Files

> [!IMPORTANT] 
> Azure Files est actuellement en préversion publique dans le cadre de [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Utilisez une [API REST en préversion (2021-04-30-Preview ou version ultérieure)](search-api-preview.md) pour indexer votre contenu. La prise en charge du portail est actuellement limitée, et le Kit de développement logiciel (SDK) .NET n’est pas pris en charge.

Cet article examine le flux de travail de base pour l’extraction de contenu et de métadonnées de partages de fichiers Azure et leur envoi à un index de recherche dans le service Recherche cognitive Azure. L’index obtenu peut être interrogé à l’aide d’une recherche en texte intégral.

> [!NOTE]
> Vous êtes déjà familiarisé avec le flux de travail et la composition ? Votre prochaine étape est [Comment configurer un indexeur de fichiers](#configure).

## <a name="functionality"></a>Fonctionnalité

Dans le service Recherche cognitive Azure, un indexeur est un robot qui extrait des données et métadonnées pouvant faire l’objet d’une recherche à partir d’une source de données. L’indexeur Azure Files se connectera à vos fichiers d’index et de partage de fichiers Azure. L’indexeur offre les fonctionnalités suivantes :

+ Indexez le contenu à partir d’un partage de fichiers Azure.
+ L’indexeur prend en charge l’indexation incrémentielle : il identifie le contenu modifié du partage de fichiers Azure et indexe uniquement le contenu mis à jour au cours des prochaines exécutions d’indexation. Prenons l’exemple de 5 fichiers PDF initialement indexés par l’indexeur. Si l’un d’eux est mis à jour et que l’indexeur s’exécute à nouveau, ce dernier indexe uniquement le fichier PDF qui a été mis à jour.
+ Par défaut, les images texte et normalisées sont extraites des fichiers qui sont indexés. Vous pouvez éventuellement ajouter un ensemble de compétences au pipeline pour améliorer l’enrichissement du contenu. Pour plus d’informations sur les ensembles de compétences, consultez l’article [Concepts des ensembles de compétences dans Recherche cognitive Azure](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Formats de document pris en charge

L’indexeur Azure Files de Recherche cognitive Azure peut extraire du texte à partir des formats de document suivants :

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="required-resources"></a>Ressources nécessaires

Vous devez disposer des services Recherche cognitive Azure et [Azure Files](https://azure.microsoft.com/services/storage/files/). Dans Azure Files, vous avez besoin d’un partage de fichiers qui fournit le contenu source.

<a name="configure"></a>

## <a name="configuring-a-file-indexer"></a>Configuration d’un indexeur de fichiers

Les indexeurs de fichiers Azure partagent de nombreuses options de configuration communes avec les [indexeurs d’objets Blob Azure](search-howto-indexing-azure-blob-storage.md). Par exemple, les indexeurs de fichiers Azure prennent en charge la [production de plusieurs documents de recherche à partir d’un fichier unique](search-howto-index-one-to-many-blobs.md), de [fichiers texte bruts](search-howto-index-plaintext-blobs.md), de [fichiers JSON](search-howto-index-json-blobs.md) et de [fichiers chiffrés](search-howto-index-encrypted-blobs.md). Une grande partie des mêmes [options de configuration](search-howto-indexing-azure-blob-storage.md) s’appliquent également. Les différences importantes sont mises en évidence ci-dessous.

## <a name="data-source-definitions"></a>Définitions de sources de données

La principale différence entre un indexeur de fichiers et tout autre indexeur est la définition de la source de données attribuée à l’indexeur. La définition de la source de données spécifie le type de source de données (« type » : « azurefile »), et d’autres propriétés d’authentification et de connexion au contenu à indexer.

La définition d’une source de données de fichier ressemble à l’exemple ci-dessous :

```http
{
    "name" : "my-file-datasource",
    "type" : "azurefile",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-file", "query" : "<optional-directory-name>" }
}
```

La propriété `"credentials"` peut être une chaîne de connexion, comme indiqué dans l’exemple ci-dessus, ou l’une des autres approches décrites dans la section suivante. La propriété `"container"` fournit le partage de fichiers dans Stockage Azure, et `"query"` est utilisée pour spécifier un sous-dossier dans le partage. Pour plus d’informations sur les définitions de source de données, consultez [Créer une source de données (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Informations d'identification

Vous pouvez fournir les informations d’identification du partage de fichiers de l’une des manières suivantes :

**Chaîne de connexion d’identité managée** : `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Cette chaîne de connexion ne nécessite pas de clé de compte, mais vous devez suivre les instructions fournies dans [Configurer une connexion à un compte Stockage Azure à l’aide d’une identité managée](search-howto-managed-identities-storage.md).

**Chaîne de connexion au compte de stockage avec accès complet** : `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Vous pouvez obtenir la chaîne de connexion sur le Portail Azure en sélectionnant vers le panneau du compte de stockage > Paramètres > Clés (pour les comptes de stockage Classic) ou Sécurité + mise en réseau > Clés d’accès (pour les comptes de stockage Azure Resource Manager).

Chaîne de connexion de la **signature d’accès partagé (SAP) au compte de stockage** : `{ "connectionString" : "BlobEndpoint=https://<your account>.file.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&sp=rl&sr=s;" }`

La SAP doit avoir les autorisations de liste et lecture sur les partages de fichiers.

**Signature d’accès partagé du conteneur** : `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.file.core.windows.net/<share name>?sv=2016-05-31&sr=s&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

La SAP doit avoir les autorisations de liste et lecture sur le partage de fichiers. Pour plus d’informations sur les signatures d’accès partagé au stockage, consultez [Utilisation des signatures d’accès partagé](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Si vous utilisez des informations d’identification d’une SAP, vous devez mettre à jour les informations d’identification de la source de données régulièrement avec des signatures renouvelées afin d’éviter leur expiration. Si les informations d’identification de la SAP expirent, l’indexeur échoue avec un message d’erreur tel que « Les informations d’identification fournies dans la chaîne de connexion sont invalides ou ont expiré. »

## <a name="indexing-file-metadata"></a>Indexation des métadonnées de fichier

Pour faciliter le tri dans les fichiers constitués de tout type de contenu, un scénario courant consiste à indexer les métadonnées personnalisées et les propriétés système pour chaque fichier. De cette façon, les informations de tous les fichiers sont indexées indépendamment du type de document et stockées dans un index de votre service de recherche. Le nouvel index vous permet alors d’effectuer un tri, un filtrage et une facette dans l’ensemble du contenu du stockage de fichiers.

Les propriétés standard de métadonnées de fichier peuvent être extraites dans les champs listés ci-dessous. L’indexeur de fichiers crée automatiquement des mappages de champs internes pour ces propriétés de métadonnées de fichier. Vous devez toujours ajouter les champs que vous souhaitez utiliser dans la définition de l’index, mais vous pouvez omettre de créer des mappages de champs dans l’indexeur.

+ **metadata_storage_name** (`Edm.String`) : le nom du fichier. Par exemple, si vous disposez du fichier /my-share/my-folder/subfolder/resume.pdf, ce champ présente la valeur `resume.pdf`.
+ **metadata_storage_path** (`Edm.String`) : URI complet du fichier, incluant le compte de stockage. Par exemple : `https://myaccount.file.core.windows.net/my-share/my-folder/subfolder/resume.pdf`
+ **metadata_storage_content_type** (`Edm.String`) : type de contenu tel que spécifié par le code que vous avez utilisé pour charger le fichier. Par exemple : `application/octet-stream`.
+ **metadata_storage_last_modified** (`Edm.DateTimeOffset`) : horodatage de la dernière modification du fichier. La Recherche cognitive Azure utilise cet horodateur pour identifier les fichiers modifiés afin d’éviter une réindexation complète après l’indexation initiale.
+ **metadata_storage_size** (`Edm.Int64`) : taille du fichier en octets.
+ **metadata_storage_content_md5** (`Edm.String`) : code de hachage MD5 du contenu du fichier s’il est disponible.
+ **metadata_storage_sas_token** (`Edm.String`) : jeton SAP temporaire qui peut être utilisé par des [compétences personnalisées](cognitive-search-custom-skill-interface.md) pour accéder au fichier. Ce jeton ne doit pas être stocké pour une utilisation ultérieure dans la mesure où il peut expirer.

## <a name="index-by-file-type"></a>Indexer par type de fichier

Vous pouvez contrôler les documents qui sont indexés et ignorés.

### <a name="include-documents-having-specific-file-extensions"></a>Inclure les documents ayant des extensions de fichier spécifiques

Vous pouvez indexer uniquement les documents avec les extensions de nom de fichier que vous spécifiez à l’aide du paramètre de configuration d’indexeur `indexedFileNameExtensions`. La valeur est une chaîne contenant une liste d'extensions de fichier séparées par des virgules (précédées d'un point). Par exemple, pour indexer uniquement les documents .PDF et .DOCX, procédez comme suit :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Exclure les documents ayant des extensions de fichier spécifiques

Vous pouvez exclure de l’indexation les documents avec des extensions de nom de fichier spécifiques à l’aide du paramètre de configuration `excludedFileNameExtensions`. La valeur est une chaîne contenant une liste d'extensions de fichier séparées par des virgules (précédées d'un point). Par exemple, pour indexer tous les contenus sauf ceux qui ont les extensions .PNG et .JPEG, procédez comme suit :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Si les paramètres `indexedFileNameExtensions` et `excludedFileNameExtensions` sont tous deux présents, la Recherche cognitive Azure regarde d’abord `indexedFileNameExtensions`, puis `excludedFileNameExtensions`. Cela signifie que, si la même extension de fichier est présente dans les deux listes, elle sera exclue de l'indexation.

<a name="deleted-files"></a>

## <a name="detecting-deleted-files"></a>Détection des fichiers supprimés

Après avoir créé un index de recherche initial, vous souhaiterez peut-être que les travaux de l’indexeur récupèrent uniquement les documents nouveaux et changés. Pour le contenu de recherche en provenance du Stockage Fichier Azure, la détection des modifications se produit automatiquement lorsque vous utilisez une planification pour déclencher l’indexation. Par défaut, le service réindexe uniquement les fichiers modifiés, déterminés par leur horodateur `LastModified`. Contrairement à d’autres sources de données prises en charge par les indexeurs de recherche, les fichiers ont toujours un horodateur, ce qui évite d’avoir à configurer manuellement une stratégie de détection des modifications.

Bien que la détection des modifications soit acquise, la détection des suppressions ne l’est pas. Si vous souhaitez détecter des fichiers supprimés, veillez à adopter une approche de « suppression réversible ». Si vous supprimez complètement les fichiers, les documents correspondants ne seront pas supprimés de l’index de recherche.

## <a name="soft-delete-using-custom-metadata"></a>Suppression réversible à l’aide de métadonnées personnalisées

Cette méthode utilise les métadonnées d’un fichier pour déterminer si un document de recherche doit être supprimé de l’index. Cette méthode requiert deux actions distinctes, la suppression du document de recherche de l’index, puis la suppression du fichier dans le service Stockage Azure.

Vous devez suivre des étapes dans le Stockage Fichier et dans Recherche cognitive, mais il n’y a pas d’autres dépendances de fonctionnalités. Cette fonctionnalité est prise en charge dans les API mises à la disposition générale.

1. Ajoutez une paire clé-valeur de métadonnées personnalisées au fichier pour indiquer à Recherche cognitive Azure qu’il est logiquement supprimé.

1. Configurez une stratégie de détection des colonnes de suppression réversible sur la source de données. Par exemple, la stratégie suivante considère qu’un fichier est supprimé s’il présente une propriété de métadonnées `IsDeleted` avec la valeur `true` :

    ```http
    PUT https://[service name].search.windows.net/datasources/file-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "file-datasource",
        "type" : "azurefile",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-share", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Une fois que l’indexeur a traité le blob et supprimé le document de l’index, vous pouvez supprimer le blob dans le service Stockage Blob Azure.

### <a name="reindexing-undeleted-files-using-custom-metadata"></a>Réindexation de fichiers dont la suppression a été annulée (à l’aide de métadonnées personnalisées)

Une fois qu’un indexeur a traité un fichier supprimé, et supprimé de l’index le document de recherche correspondant, il ne réexaminera pas ce blob en cas de restauration ultérieure si l’horodateur `LastModified` du fichier est antérieur à la dernière exécution de l’indexeur.

Si vous souhaitez réindexer ce document, modifiez la valeur `"softDeleteMarkerValue" : "false"` pour ce fichier, puis réexécutez l’indexeur.

## <a name="see-also"></a>Voir aussi

+ [Indexeurs dans Recherche cognitive Azure](search-indexer-overview.md)
+ [Qu’est-ce qu’Azure Files ?](../storage/files/storage-files-introduction.md)
