---
title: Vue d’ensemble des index
titleSuffix: Azure Cognitive Search
description: Explique ce qu’est un index de recherche dans Recherche cognitive Azure et décrit le contenu, la construction, l’expression physique et le schéma d’index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 51b075dbce189370cf502bce6d46471da6c58348
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490532"
---
# <a name="indexes-in-azure-cognitive-search"></a>Index dans Recherche cognitive Azure

Dans Azure Recherche cognitive, un *index de recherche* est un contenu pouvant faire l’objet d’une recherche, accessible au moteur de recherche pour l’indexation, la recherche en texte intégral et les requêtes filtrées. Un index est défini par un schéma et enregistré dans le service de recherche, l’importation des données se faisant dans un deuxième temps. Ce contenu existe dans votre service de recherche, en dehors de vos magasins de données principaux, ce qui est nécessaire pour les temps de réponse de millisecondes attendus dans les applications modernes. À l’exception des scénarios d’indexation spécifiques, le service de recherche ne se connectera jamais à vos données locales et les interrogera.

Si vous créez et gérez un index de recherche, cet article vous aidera à comprendre ce qui suit :

+ Contenu (documents et schéma)
+ Représentation physique
+ Opérations de base

Vous préférez être pratique immédiatement ? Consultez [Créer un index de recherche](search-how-to-create-search-index.md) à la place.

## <a name="content-of-a-search-index"></a>Contenu d’un index de recherche

Dans Recherche cognitive, les index contiennent des *documents de recherche*. Conceptuellement, un document correspond à une unité de données pouvant faire l’objet d’une recherche dans un index. Par exemple, un détaillant peut avoir un document pour chaque produit, une organisation de presse peut avoir un document pour chaque article, un site de voyage peut avoir un document pour chaque hôtel et destination, et ainsi de suite. Pour comparer avec des éléments de base de données plus familiers, un *index de recherche* correspond à une *table*, et les *documents* équivalent plus ou moins aux *lignes* d’une table.

La structure d’un document est déterminée par le schéma d’index, comme illustré ci-dessous. La collection « fields » correspond généralement à la majeure partie de l’index, dans laquelle chaque champ est nommé, se voit attribuer un [type de données](/rest/api/searchservice/Supported-data-types) et est pourvu de comportements autorisés qui déterminent son utilisation.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

D’autres éléments sont réduits par souci de concision, mais les liens suivants peuvent fournir les détails : 

+ Les [générateurs de suggestions](index-add-suggesters.md) prennent en charge les requêtes de type anticipé comme la saisie semi-automatique
+ Les [profils de score](index-add-scoring-profiles.md) sont utilisés pour le paramétrage de la pertinence
+ Les [analyseurs](search-analyzers.md) sont utilisés pour traiter les chaînes en jetons en fonction des règles linguistiques ou d’autres caractéristiques prises en charge par l’analyseur
+ Les [scripts à distance Cross-Origin (CORS)](search-how-to-create-search-index.md#corsoptions) sont utilisés pour les applications qui délivrent des demandes de différents domaines
+ La [clé de chiffrement](search-security-manage-encryption-keys.md) est utilisée pour le double-chiffrement du contenu sensible dans l’index.

### <a name="field-definitions"></a>Définitions de champs

Un document de recherche est défini par la collection « fields ». Vous aurez besoin de champs pour l’identification du document (clés), le stockage de texte pouvant faire l’objet d’une recherche et la prise en charge des filtres, facettes et tris. Il se peut également que vous ayez besoin de champs pour les données qu’un utilisateur ne voit jamais. Par exemple, il peut être utile de disposer de champs pour des marges bénéficiaires ou des promotions commerciales que vous pouvez utiliser pour modifier le rang de recherche.

Si les données entrantes sont hiérarchiques par nature, vous pouvez les représenter dans un index en tant que [type complexe](search-howto-complex-data-types.md), utilisé pour représenter des structures imbriquées. L’exemple de jeu de données intégré, Hotels, illustre des types complexes utilisant une adresse (contenant plusieurs sous-champs) qui entretient une relation un-à-un avec chaque hôtel, et une collection complexe Rooms, où plusieurs chambres sont associées à chaque hôtel. 

<a name="index-attributes"></a>

### <a name="field-attributes"></a>Attributs de champ

Les attributs d’un champ déterminent son utilisation, par exemple s’il est utilisé dans la recherche en texte intégral, la navigation par facettes, les opérations de tri et ainsi de suite. 

Les champs de type chaîne sont souvent marqués avec les attributs « Possibilité de recherche » et « Récupérable ». Vous pouvez utiliser les champs « Triable », « Filtrable » et « À choix multiple » pour affiner les résultats de la recherche.

|Attribut|Description|  
|---------------|-----------------|  
|« Possibilité de recherche » |Recherche en texte intégral, avec analyse lexicale (césure de mots) lors de l’indexation. Si vous définissez un champ avec possibilité de recherche sur une valeur comme « journée ensoleillée », cette valeur est fractionnée au niveau interne en jetons individuels « journée » et « ensoleillée ». Pour en savoir plus, consultez la rubrique [Fonctionnement de la recherche en texte intégral](search-lucene-query-architecture.md).|  
|« Filtrable » |Référencé dans les requêtes $filter. Les champs filtrables de type `Edm.String` ou `Collection(Edm.String)` ne font pas l’objet d’une analyse lexicale, les comparaisons ne concernent donc que les correspondances exactes. Par exemple, si vous définissez un champ avec la valeur « journée ensoleillée », la requête `$filter=f eq 'sunny'` ne renverra aucune correspondance, contrairement à `$filter=f eq 'sunny day'`. |  
|« Triable » |Le système trie les résultats par score par défaut, mais vous pouvez configurer le tri en fonction des champs des documents. Les champs de type `Collection(Edm.String)` ne sont pas « triables ». |  
|« À choix multiple » |Généralement utilisé dans une présentation des résultats de recherche qui inclut un nombre de correspondances par catégorie (par exemple, les hôtels dans une ville spécifique). Cette option ne peut pas être utilisée avec des champs de type `Edm.GeographyPoint`. Les champs de type `Edm.String` qui sont « filtrables », « triables » ou « à choix multiple » ne peuvent pas dépasser 32 Ko de longueur. Pour plus d’informations, consultez l’article [Créer un index (API REST)](/rest/api/searchservice/create-index).|  
|« Clé » |Identificateur unique des documents dans l’index. Un seul champ doit être choisi comme champ clé et il doit être de type `Edm.String`.|  
|« Récupérable » |Définit si le champ peut être retourné dans un résultat de recherche. Cet attribut est utile quand vous voulez utiliser un champ (comme *profit margin*) comme mécanisme de filtre, de tri ou de score, mais que vous ne voulez pas qu’il soit visible par l’utilisateur final. Il doit être `true` for `key` .|  

Même si vous pouvez ajouter de nouveaux champs à tout moment, les définitions de champ existantes sont verrouillées pour toute la durée de vie de l’index. C’est pourquoi les développeurs utilisent généralement le portail pour créer des index simples, tester des idées ou rechercher une définition de paramètre. Il est plus efficace d’effectuer des itérations fréquentes sur la conception d’un index si vous suivez une approche basée sur du code pour reconstruire l’index facilement.

> [!NOTE]
> Les API que vous utilisez pour créer un index ont différents comportements par défaut. Pour les [API REST](/rest/api/searchservice/Create-Index), la plupart des attributs sont activés par défaut (par exemple, « Possibilité de recherche » et « Récupérable » sont actifs pour les champs de type chaîne) et vous n’avez généralement besoin de les définir que si vous voulez les désactiver. Pour le Kit de développement logiciel (SDK) .NET, l’inverse est vrai. Pour les propriétés que vous ne définissez pas explicitement, l’option par défaut désactive le comportement de recherche correspondante, sauf si vous l’activez de façon spécifique.

<a name="index-size"></a>

## <a name="physical-representation"></a>Représentation physique

Dans Azure Recherche cognitive, la structure physique d’un index est en grande partie une implémentation interne. Vous pouvez accéder à son schéma, interroger son contenu, surveiller sa taille et gérer la capacité, mais les clusters eux-mêmes (index, partitions et autres fichiers et dossiers) sont délimités et gérés en interne par Microsoft en votre nom.

La taille d’un index est déterminée par les éléments suivants :

+ Quantité et composition de vos documents
+ Configuration de l’index (en particulier si vous incluez des générateurs de suggestions)
+ Attributs sur des champs individuels

Vous pouvez surveiller la taille de l’index dans l’onglet Index de la Portail Azure ou en émettant une [requête GET INDEX](/rest/api/searchservice/get-index) sur votre service de recherche.

### <a name="factors-influencing-index-size"></a>Facteurs influençant la taille de l’index

La composition et la quantité des documents seront déterminées par ce que vous choisissez d’importer. N’oubliez pas qu’un index de recherche ne doit contenir que du contenu pouvant faire l’objet d’une recherche. Si les documents sources incluent des champs binaires, vous devez généralement omettre ces champs du schéma d’index (à moins que vous n’utilisiez l’enrichissement par IA pour craquer et analyser le contenu pour créer des informations de recherche de texte.)

La configuration d’index peut inclure d’autres composants en dehors des documents, tels que des générateurs de suggestions, des analyseurs clients, des profils de score, des paramètres CORS et des informations de clé de chiffrement. Dans la liste ci-dessus, le seul composant qui est susceptible d’avoir un impact sur la taille de l’index est des générateurs de suggestions. Les [**générateurs de suggestions**](index-add-suggesters.md) sont des constructions qui prennent en charge les requêtes de type anticipé ou de saisie semi-automatique. Ainsi, lorsque vous incluez un générateur de suggestions, le processus d’indexation crée les structures de données nécessaires pour les correspondances de caractères textuels. Les générateurs de suggestions sont implémentés au niveau du champ. Par conséquent, choisissez uniquement les champs qui sont raisonnables pour le type.

Les attributs de champ sont la troisième considération de la taille de l’index. Les attributs déterminent les comportements. Pour prendre en charge ces comportements, le processus d’indexation crée les structures de données de prise en charge. Par exemple, « recherchée » appelle la [recherche en texte intégral](search-lucene-query-architecture.md), qui analyse les index inversés pour le terme à jetons. En revanche, un attribut « filtrable » ou « triable » prend en charge l’itération sur des chaînes non modifiées.

### <a name="example-demonstrating-the-storage-implications-of-attributes-and-suggesters"></a>Exemple illustrant les implications de stockage des attributs et des générateurs de suggestions

La capture d’écran suivante illustre les caractéristiques du stockage d’index résultant des différentes combinaisons d’attributs. L’index est basé sur l’**exemple d’index de biens immobiliers**, que vous pouvez créer facilement à l’aide de l’Assistant Importer des données et les exemples de données intégrés. Bien que les schémas de l’index ne soient pas montrés, vous pouvez en déduire les attributs d’après le nom de l’index. Par exemple, pour l’index *realestate-searchable*, seul l’attribut « Possibilité de recherche » est sélectionné ; pour l’index *realestate-retrievable*, seul l’index « Récupérable » est sélectionné, et ainsi de suite.

![Taille de l’index en fonction de la sélection de l’attribut](./media/search-what-is-an-index/realestate-index-size.png "Taille de l’index en fonction de la sélection de l’attribut")

Bien que ces variantes d’index soient plutôt artificielles, nous pouvons nous y reporter pour nous faire une idée de la façon dont les attributs affectent le stockage. Le paramètre « Récupérable » fait-il croître l’index ? Non. L’ajout de champs à un **suggesteur** fait-il croître l’index ? Oui. 

Le fait de rendre un champ filtrable ou triable augmente également la consommation de stockage, car les champs filtrés et triés ne sont pas sous forme de jeton, de sorte que les séquences de caractères peuvent être mises en correspondance textuellement.

L’impact des [analyseurs](search-analyzers.md) n’est pas non plus pris en compte dans le tableau ci-dessus. Si vous utilisez le générateur de jetons edgeNgram pour stocker des séquences de caractères verbatim (a, ab, abc, abcd), la taille de l’index sera supérieure à si vous avez utilisé un analyseur standard.

## <a name="basic-operations"></a>Opérations de base

Maintenant que vous avez une meilleure idée de ce qu’est un index, cette section présente les opérations d’exécution d’index, notamment la connexion à et la sécurisation d’un index unique.

### <a name="index-isolation"></a>Isolation d’index
  
Dans Recherche cognitive, vous travaillerez avec un index à la fois, où toutes les opérations liées à l’index ciblent un index unique. Il n’existe aucun concept d’index associé ou de jointure d’index indépendants pour l’indexation et l’interrogation. 

Lors de la gestion d’un index, sachez qu’il n’existe pas de prise en charge de portail ou d’API pour le déplacement ou la copie d’un index. Au lieu de cela, les clients pointent généralement leur solution de déploiement d’applications sur un service de recherche différent (si vous utilisez le même nom d’index), ou modifient le nom pour créer une copie sur le service de recherche actuel, puis le générer.

### <a name="continuously-available"></a>Disponible en continu

Un index est disponible en continu, sans possibilité de suspension ou de mise hors connexion. Étant donné qu’il est conçu pour une opération continue, les mises à jour de son contenu, ou les ajouts à l’index lui-même, se produisent en temps réel. Par conséquent, les requêtes peuvent retourner temporairement des résultats incomplets si une demande coïncide avec une mise à jour de document.

Notez que la continuité des requêtes existe pour les opérations de document (actualisation ou suppression) et pour les modifications qui n’ont pas d’impact sur la structure et l’intégrité existantes de l’index actuel (comme l’ajout de nouveaux champs). Si vous devez effectuer des mises à jour structurelles (en modifiant des champs existants), elles sont généralement gérées à l’aide d’un flux de travail de suppression et de recréation dans un environnement de développement, ou en créant une nouvelle version de l’index sur le service de production.

Pour éviter la reconstruction, certains clients qui effectuent de petites modifications choisissent de « créer une version » d’un champ en en créant un nouveau qui coexiste avec une version précédente. Au fil du temps, cela se traduit par un contenu orphelin sous la forme de champs obsolètes ou de définitions d’analyseur personnalisé obsolètes, en particulier dans un index de production qui est coûteux à répliquer. Vous pouvez résoudre ces problèmes sur les mises à jour planifiées de l’index dans le cadre de la gestion du cycle de vie des index.

### <a name="endpoint-connection-and-security"></a>Connexion de point de terminaison et sécurité

Toutes les demandes d’indexation et de requête ciblent un index. Les points de terminaison sont généralement l’un des suivants :

| Point de terminaison | Connexion et contrôle d’accès |
|----------|-------------------------------|
| `<your-service>.search.windows.net/indexes` | Cible la collection d’index. Utilisé lors de la création, de la liste ou de la suppression d’un index. Des droits d’administrateur sont nécessaires pour ces opérations, disponibles par le biais de clés d’API d’administration ou d’un rôle contributeur de recherche. |
| `<your-service>.search.windows.net/indexes/<your-index>/docs` | Cible la collection documents d’un index unique. Utilisé lors de l’interrogation d’un index. Les droits de lecture sont suffisants et disponibles par le biais de clés API de requête ou d’un rôle de lecteur de données. |

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez acquérir de l’expérience en créant un index en utilisant presque n’importe quel exemple ou procédure pas à pas pour Recherche cognitive. Pour commencer, vous pouvez choisir l’un des démarrages rapides à partir de la table des matières.

Mais vous souhaiterez également vous familiariser avec les méthodologies de chargement d’un index avec des données. Les stratégies de définition d’index et d’importation de données sont définies en tandem. Les articles suivants fournissent plus d’informations sur la création et le chargement d’un index.

+ [Créer un index de recherche](search-how-to-create-search-index.md)

+ [Vue d’ensemble de l’importation des données](search-what-is-data-import.md)

+ [Ajout, mise à jour ou suppression de documents (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 