---
title: Mappages de champs dans les indexeurs
titleSuffix: Azure Cognitive Search
description: Configurez des mappages de champs d’un indexeur vers un compte pour détecter des différences de noms de champs et de représentations des données.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 9fdff284d3b183ef9a0589d9dee4c568249b854b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238001"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mappages de champs et transformations à l’aide d’indexeurs Recherche cognitive Azure

![Étapes de l'indexeur](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "étapes de l'indexeur")

Lorsque vous utilisez des indexeurs de Recherche cognitive Azure, les indexeurs mappent automatiquement les champs d’une source de données aux champs d’un index cible, en supposant que les noms et les types de champs sont compatibles. Dans certains cas, les données d’entrée ne correspondent pas exactement au schéma de votre index cible. Une solution consiste à utiliser des *mappages de champs* pour définir spécifiquement le chemin de données pendant le processus d’indexation.

Des mappages de champs peuvent être utilisés pour les scénarios suivants :

+ Noms de champs non concordants. Supposons que votre source de données comprend un champ nommé `_id`. Étant donné que Recherche cognitive Azure n’autorise pas les noms de champs commençant par un trait de soulignement, un mappage de champ vous permet de renommer efficacement un champ.

+ De un champ à plusieurs champs. Vous pouvez remplir plusieurs champs de l’index à partir des données de la même source. Par exemple, vous pouvez appliquer différents analyseurs à chaque champ.

+ De plusieurs champs à un champ. Vous voulez remplir un champ d’index avec des données provenant de plusieurs sources de données, lesquelles utilisent des noms de champs différents.

+ Encodage ou décodage de données Base64. Les mappages de champs prennent en charge plusieurs [**fonctions de mappage**](#mappingFunctions), y compris les fonctions d’encodage et de décodage en Base64.

+ Fractionnement de chaînes ou refonte d’un tableau JSON dans une collection de chaînes. Les fonctions de mappage de champs offrent cette capacité.

Les mappages de champs dans les indexeurs sont un moyen simple de mapper des champs de données à des champs d’index, avec une certaine possibilité de conversion de données simples. Les données plus complexes devront peut-être être prétraitées pour être converties dans un format propice à l’indexation. L’une des options que vous pouvez envisager est [Azure Data Factory](../data-factory/index.yml).

> [!NOTE]
> Les mappages de champs s’appliquent aux index de recherche uniquement. Pour les indexeurs qui créent également des [bases de connaissances](knowledge-store-concept-intro.md), les formes et les projections de données déterminent les associations de champs, et tous les mappages de champs et mappages de champs de sortie dans l’indexeur sont ignorés.

## <a name="set-up-field-mappings"></a>Configurer des mappages de champs

Un mappage de champs se compose de trois parties :

+ « sourceFieldName », qui représente un champ de votre source de données. Cette propriété est requise.
+ « TargetFieldName », facultative, qui représente un champ dans votre index de recherche. En cas d’omission, la valeur de « sourceFieldName » est utilisée pour la cible.
+ « MappingFunction », facultative, qui peut transformer vos données à l’aide de l’une des [fonctions prédéfinies](#mappingFunctions). Celle-ci peut être appliquée sur les mappages de champs d’entrée et de sortie.

Les mappages de champs sont ajoutés au tableau « fieldMappings » de la définition de l’indexeur.

> [!NOTE]
> Si aucun mappage de champs n’est présent, les indexeurs supposent que les champs de source de données doivent être mappés à des champs d’index du même nom. L’ajout d’un mappage de champs remplace ces mappages de champs par défaut pour les champs source et cible. Certains indexeurs, comme l’[indexeur de stockage d’objets blob](search-howto-indexing-azure-blob-storage.md), ajoutent des mappages de champs par défaut pour le champ de clé d’index.

## <a name="map-fields-using-rest"></a>Mapper des champs à l’aide de REST

Vous pouvez ajouter des mappages de champs lors de la création d’un indexeur avec la requête d’API [Créer un indexeur](/rest/api/searchservice/create-Indexer). Vous pouvez gérer les mappages de champs d’un indexeur existant avec la requête d’API [Mise à jour d’un indexeur](/rest/api/searchservice/update-indexer).

Par exemple, voici comment mapper un champ source à un champ cible avec un nom différent :

```JSON
PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Un champ source peut être référencé dans plusieurs mappages de champs. L’exemple suivant montre comment dupliquer (fork) un champ, en copiant le même champ source dans deux champs d’index différents :

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search utilise une comparaison qui ne respecte pas la casse pour résoudre les noms de champ et de fonction dans les mappages de champs. C'est pratique (vous n'avez besoin de faire attention à la casse), mais cela signifie que votre source de données et votre index ne peuvent pas comporter des champs qui diffèrent uniquement par la casse.  
>

## <a name="map-fields-using-net"></a>Mapper des champs à l’aide de .NET

Vous pouvez définir des mappages de champs dans le Kit de développement logiciel (SDK) .NET à l’aide de la classe [FieldMapping](/dotnet/api/azure.search.documents.indexes.models.fieldmapping) qui possède les propriétés « SourceFieldName » et « TargetFieldName », ainsi qu’une référence facultative « MappingFunction ».

Vous pouvez spécifier des mappages de champs lors de la construction de l’indexeur, ou ultérieurement en définissant directement [SearchIndexer.FieldMappings](/dotnet/api/azure.search.documents.indexes.models.searchindexer.fieldmappings).

L’exemple C# suivant définit les mappages de champs lors de la construction d’un indexeur.

```csharp
var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "SQL data indexer",
    Schedule = schedule,
    Parameters = parameters,
    FieldMappings =
    {
        new FieldMapping("_id") {TargetFieldName = "HotelId", FieldMappingFunction.Base64Encode()},
        new FieldMapping("Amenities") {TargetFieldName = "Tags"}
    }
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Fonctions de mappage de champs

Une fonction de mappage de champ transforme le contenu d’un champ avant son stockage dans l’index. Les fonctions de mappage actuellement prises en charge sont les suivantes :

+ [base64Encode](#base64EncodeFunction)
+ [base64Decode](#base64DecodeFunction)
+ [extractTokenAtPosition](#extractTokenAtPositionFunction)
+ [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
+ [urlEncode](#urlEncodeFunction)
+ [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>Fonction base64Encode

Exécute l’encodage Base64 *sécurisé pour les URL* de la chaîne d'entrée. Suppose que l'entrée est encodée en UTF-8.

#### <a name="example---document-key-lookup"></a>Exemple de recherche d’une clé de document

Seuls les caractères sécurisés pour les URL peuvent apparaître dans une clé de document Recherche cognitive Azure, de façon à ce que vous puissiez traiter le document à l’aide de l’[API de recherche](/rest/api/searchservice/lookup-document). Si le champ source de votre clé contient des caractères non sécurisés pour les URL, vous pouvez utiliser la fonction `base64Encode` pour les convertir au moment de l’indexation. Cependant, une clé de document (avant et après la conversion) ne doit pas excéder 1 024 caractères.

Une fois que vous avez récupéré la clé encodée au moment de la recherche, utilisez la fonction `base64Decode` pour obtenir la valeur de clé d’origine, et l’utiliser pour récupérer le document source.

```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

#### <a name="example---preserve-original-values"></a>Exemple : conserver les valeurs d’origine

L’[indexeur de stockage d’objets blob](search-howto-indexing-azure-blob-storage.md) ajoute automatiquement un mappage de champs à partir de `metadata_storage_path`, l’URI de l’objet blob, au champ de clé d’index si aucun mappage de champs n’est spécifié. Cette valeur est encodée en Base64 afin d’être utilisée en toute sécurité comme clé de document Recherche cognitive Azure. L’exemple suivant montre comment mapper simultanément une *version* de `metadata_storage_path` en codage Base64 sécurisée pour les URL à un champ `index_key` et conserver la valeur d’origine dans un champ `metadata_storage_path` :

```JSON
"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

Si vous n’incluez aucune propriété de paramètre pour votre fonction de mappage, la valeur par défaut est `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Recherche cognitive Azure prend en charge deux encodages en Base64. Vous devez utiliser les mêmes paramètres lors de l’encodage et du décodage du même champ. Pour décider quels paramètres utiliser, consultez les [options relatives à l’encodage base64](#base64details).

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>Fonction base64Decode

Effectue le décodage en Base64 de la chaîne d'entrée. L’entrée est considérée comme une chaîne encodée en Base64 et *sécurisée pour les URL*.

#### <a name="example---decode-blob-metadata-or-urls"></a>Exemple : décodage de métadonnées de blob ou d’URL

Votre source de données peut contenir des chaînes encodées en Base64, telles que des chaînes de métadonnées de blob ou des URL web, que vous voulez rendre disponibles pour des recherches sous la forme de texte brut. Vous pouvez utiliser la fonction `base64Decode` pour transformer les données encodées en chaînes normales lors du remplissage de votre index de recherche.

```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Si vous n’incluez aucune propriété de paramètre, la valeur par défaut est `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Recherche cognitive Azure prend en charge deux encodages en Base64. Vous devez utiliser les mêmes paramètres lors de l’encodage et du décodage du même champ. Pour décider quels paramètres utiliser, reportez-vous aux [options relatives à l’encodage base64](#base64details).

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Options d’encodage en Base64

La Recherche cognitive Azure prend en charge l’encodage en base64 normal et sécurisé pour les URL. Une chaîne encodée en base64 lors de l’indexation devra être décodée ultérieurement avec les mêmes options d’encodage. Dans le cas contraire, le résultat ne correspondra pas à la version d’origine.

Si les paramètres `useHttpServerUtilityUrlTokenEncode` ou `useHttpServerUtilityUrlTokenDecode` d’encodage et de décodage, respectivement, sont définis sur `true`, `base64Encode` se comporte comme [HttpServerUtility.UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) et `base64Decode` se comporte comme [HttpServerUtility.UrlTokenDecode](/dotnet/api/system.web.httpserverutility.urltokendecode).

> [!WARNING]
> Si `base64Encode` est utilisé pour générer des valeurs de clé, `useHttpServerUtilityUrlTokenEncode` doit être défini sur true. Seul l’encodage en base64 sécurisé pour les URL peut être utilisé pour les valeurs de clés. Pour obtenir l’ensemble des restrictions appliquées aux caractères des valeurs de clé, consultez [Règles de nommage](/rest/api/searchservice/naming-rules).

Les bibliothèques .NET dans la Recherche cognitive Azure utilisent l’intégralité de .NET Framework qui fournit un encodage intégré. Les options `useHttpServerUtilityUrlTokenEncode` et `useHttpServerUtilityUrlTokenDecode` tirent parti de cette fonctionnalité intégrée. Si vous utilisez .NET Core ou une autre infrastructure, nous vous recommandons de définir ces options sur `false` et d’appeler directement les fonctions d’encodage et de décodage de votre infrastructure.

Le tableau suivant compare les différents encodages base64 de la chaîne `00>00?00`. Pour déterminer quel traitement supplémentaire s’avère nécessaire (le cas échéant) pour vos fonctions base64, appliquez votre fonction d’encodage de bibliothèque à la chaîne `00>00?00` et comparez le résultat obtenu au résultat attendu `MDA-MDA_MDA`.

| Encodage | Résultat d’encodage base64 | Traitement supplémentaire après l’encodage de bibliothèque | Traitement supplémentaire avant l’encodage de bibliothèque |
| --- | --- | --- | --- |
| Base64 avec remplissage | `MDA+MDA/MDA=` | Utiliser des caractères sécurisés pour les URL et supprimer le remplissage | Utiliser des caractères base64 standard et ajouter le remplissage |
| Base64 sans remplissage | `MDA+MDA/MDA` | Utiliser des caractères sécurisés pour les URL | Utiliser des caractères base64 standard |
| Base64 sécurisé pour les URL avec remplissage | `MDA-MDA_MDA=` | Supprimer le remplissage | Ajouter le remplissage |
| Base64 sécurisé pour les URL sans remplissage | `MDA-MDA_MDA` | None | None |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>Fonction extractTokenAtPosition

Divise un champ de chaîne en utilisant le séparateur spécifié et récupère le jeton à la position spécifiée dans le fractionnement résultant.

Cette fonction utilise les paramètres suivants :

+ `delimiter`: une chaîne à utiliser comme séparateur lors du fractionnement de la chaîne d'entrée.
+ `position`: une position entière à base zéro du jeton à choisir une fois la chaîne d'entrée fractionnée.

Par exemple, si l’entrée est `Jane Doe`, que le `delimiter` est `" "` (espace) et que la `position` est 0, le résultat est `Jane` ; si la `position` est 1, le résultat est `Doe`. Si la position fait référence à un jeton qui n’existe pas, une erreur est retournée.

#### <a name="example---extract-a-name"></a>Exemple : extraction d’un nom

Votre source de données contient un champ `PersonName` et vous souhaitez l’indexer en tant que deux champs `FirstName` et `LastName` distincts. Vous pouvez utiliser cette fonction pour fractionner l'entrée en utilisant l'espace comme séparateur.

```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>Fonction jsonArrayToStringCollection

Transforme une chaîne formatée en tant que tableau de chaînes JSON en un tableau de chaînes utilisable pour remplir un champ `Collection(Edm.String)` dans l'index.

Par exemple, si la chaîne d’entrée est `["red", "white", "blue"]`, le champ cible de type `Collection(Edm.String)` est rempli avec les valeurs `red`, `white` et `blue`. Pour les valeurs d’entrée qui ne peuvent pas être analysées en tant que tableaux de chaînes JSON, une erreur est retournée.

#### <a name="example---populate-collection-from-relational-data"></a>Exemple : remplissage d’une collection avec des données relationnelles

Microsoft Azure SQL Database n’inclut aucun type de données intégré qui se mappe naturellement aux champs `Collection(Edm.String)` dans Recherche cognitive Azure. Pour remplir les champs de la collection de chaînes, vous pouvez prétraiter votre source de données en tant que tableau de chaînes JSON, puis utiliser la fonction de mappage `jsonArrayToStringCollection`.

```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>fonction urlEncode

Cette fonction peut être utilisée pour encoder une chaîne de sorte qu’elle soit « URL safe ». Quand elle est utilisée avec une chaîne qui contient des caractères qui ne sont pas autorisés dans une URL, cette fonction convertit ces caractères « non sécurisés » en équivalents d’entité de caractère. Cette fonction utilise le format d’encodage UTF-8.

#### <a name="example---document-key-lookup"></a>Exemple de recherche d’une clé de document

La fonction `urlEncode` peut être utilisée comme alternative à la fonction `base64Encode`, si seuls les caractères non sécurisés d’URL doivent être convertis, tout en conservant les autres caractères tels quels.

Par exemple, si la chaîne d’entrée est `<hello>`, le champ cible de type`(Edm.String)` est rempli avec la valeur `%3chello%3e`

Une fois que vous avez récupéré la clé encodée au moment de la recherche, vous pouvez utiliser la fonction `urlDecode` pour obtenir la valeur de clé d’origine, et l’utiliser pour récupérer le document source.

```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

### <a name="urldecode-function"></a>fonction urlDecode

 Cette fonction convertit une chaîne encodée en URL en chaîne décodée à l’aide du format d’encodage UTF-8.

### <a name="example---decode-blob-metadata"></a>Exemple : décodage de métadonnées de blob

 Certains clients de stockage Azure encodent automatiquement les métadonnées Blob si elles contiennent des caractères non-ASCII. Toutefois, si vous souhaitez effectuer des recherches dans ces métadonnées (en texte brut), vous pouvez utiliser la fonction `urlDecode` pour réactiver les données encodées dans des chaînes normales lors du remplissage de votre index de recherche.

 ```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
### <a name="fixedlengthencode-function"></a>Fonction fixedLengthEncode
 
 Cette fonction convertit une chaîne de n’importe quelle longueur en chaîne à longueur fixe.
 
### <a name="example---map-document-keys-that-are-too-long"></a>Exemple - mapper des clés de document trop longues
 
Lorsque vous rencontrez une erreur indiquant que la clé du document excède 1 024 caractères, cette fonction peut être appliquée pour réduire la longueur de la clé.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```