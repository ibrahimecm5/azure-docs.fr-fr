---
title: Guide pratique pour utiliser les résultats de la recherche
titleSuffix: Azure Cognitive Search
description: Structurez et triez les résultats de recherche, récupérez le nombre de documents et ajoutez la navigation du contenu aux résultats de recherche dans Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: e810a5d3a94ad4e356d0b44a422b757b5710a781
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525772"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Guide pratique pour utiliser les résultats de recherche dans Recherche cognitive Azure

Cet article explique comment formuler une réponse à une requête dans Recherche cognitive Azure. La structure d’une réponse est déterminée par les paramètres de la requête : [Search Document](/rest/api/searchservice/Search-Documents) dans l’API REST, ou [SearchResults Class](/dotnet/api/azure.search.documents.models.searchresults-1) dans le Kit de développement logiciel (SDK) .NET. Vous pouvez utiliser les paramètres de la requête pour structurer le jeu de résultats des manières suivantes :

+ Limiter ou traiter par lot le nombre de documents dans les résultats (50 par défaut)
+ Sélection des champs à inclure dans les résultats
+ Classer les résultats
+ Mettre en surbrillance un terme entier ou partiel correspondant dans le corps des résultats de la recherche

## <a name="result-composition"></a>Composition des résultats

Bien qu’un document de recherche puisse comporter un grand nombre de champs, en général, seuls quelques-uns sont nécessaires pour représenter chaque document dans le jeu de résultats. Dans une demande de requête, ajoutez `$select=<field list>` pour spécifier les champs qui s’affichent dans la réponse. Un champ doit être attribué en tant que **Récupérable** dans l’index pour être inclus dans un résultat. 

Les champs qui fonctionnent le mieux incluent ceux qui distinguent et différencient les documents, en fournissant suffisamment d’informations pour inviter l’utilisateur à cliquer sur le document. Sur un site d’e-commerce, il peut s’agir d’un nom de produit, d’une description, d’une couleur, d’une taille, d’un prix et d’une évaluation. Pour l’exemple intégré hotels-sample-index, il peut s’agir de champs dans l’exemple suivant :

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Si vous souhaitez inclure des fichiers image dans un résultat, tel qu’une photo de produit ou un logo, stockez-les en dehors de Recherche cognitive Azure, mais incluez un champ dans votre index pour référencer l’URL d’image dans le document de recherche. Les exemples d’index qui prennent en charge les images dans les résultats incluent la démonstration **realestate-sample-us**, présentée dans ce [guide de démarrage rapide](search-create-app-portal.md), et l’[application de démonstration New York City Jobs](https://aka.ms/azjobsdemo).

### <a name="tips-for-unexpected-results"></a>Conseils en cas de résultats inattendus

Parfois, la substance et non la structure de résultats est inattendue. Quand les résultats de requête sont inattendus, vous pouvez essayer ces modifications de requête pour voir si les résultats s’améliorent :

+ Remplacez **`searchMode=any`** (valeur par défaut) par **`searchMode=all`** pour exiger des correspondances sur tous les critères plutôt que sur un seul d’entre eux. Cela s’applique particulièrement quand des opérateurs booléens sont inclus dans la requête.

+ Expérimentez différents analyseurs lexicaux ou analyseurs personnalisés pour déterminer s’ils modifient le résultat de la requête. L’analyseur par défaut fractionne les mots avec tirets et réduit les mots aux formes racines, ce qui améliore généralement la robustesse d’une réponse de requête. Toutefois, si vous devez conserver les tirets ou si des chaînes incluent des caractères spéciaux, vous devrez peut-être configurer des analyseurs personnalisés pour vous assurer que l’index contient des jetons au format approprié. Pour plus d’informations, consultez [Recherche de termes partiels et modèles avec des caractères spéciaux (traits d’union, caractères génériques, expressions régulières, modèles)](search-query-partial-matching.md).

## <a name="paging-results"></a>Résultats de pagination

Par défaut, le moteur de recherche retourne jusqu’aux 50 premières correspondances, comme déterminé par le score de recherche si la requête est une recherche en texte intégral, ou dans un ordre arbitraire pour les requêtes de correspondance exacte.

Pour retourner un nombre différent de documents correspondants, ajoutez les paramètres `$top` et `$skip` à la demande de requête. La liste suivante explique la logique.

+ Ajoutez `$count=true` pour obtenir le nombre total de documents correspondants dans un index.

+ Retournez le premier jeu de 15 documents correspondants plus un nombre total de correspondances : `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Retournez le deuxième jeu, en ignorant les 15 premiers pour obtenir les 15 suivants : `$top=15&$skip=15`. Procédez de la même façon pour le troisième jeu de 15 : `$top=15&$skip=30`

Il n’est pas garanti que les résultats des requêtes paginées soient stables si l’index sous-jacent est modifié. La pagination modifie la valeur de `$skip` pour chaque page, mais chaque requête est indépendante et opère sur l’affichage actuel des données telles qu’elles existent dans l’index au moment de la requête. En d’autres termes, il n’y a aucune mise en cache ni capture instantanée des résultats, comme c’est le cas dans une base de données à usage général.
 
Voici un exemple de la façon dont vous pouvez obtenir des doublons. Imaginons un index avec quatre documents :

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Supposons à présent que vous souhaitiez que les résultats soient retournés par deux, classés par évaluation. Vous exécuterez cette requête pour obtenir la première page des résultats, `$top=2&$skip=0&$orderby=rating desc`, et vous obtenez les résultats suivants :

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
Sur le service, supposez qu’un cinquième document est ajouté à l’index entre les appels de requête : `{ "id": "5", "rating": 4 }`.  Peu de temps après, vous exécutez une requête pour extraire la deuxième page, `$top=2&$skip=2&$orderby=rating desc`, et vous obtenez ces résultats :

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
Notez que le document 2 est extrait 2 fois. Cela est dû au fait que le nouveau document 5 a une plus grande valeur d’évaluation. Il est donc trié avant le document 2 et atterrit sur la première page. Bien que ce comportement puisse être inattendu, c’est généralement ainsi qu’un moteur de recherche se comporte.

## <a name="ordering-results"></a>Classement des résultats

Pour les requêtes de recherche en texte intégral, les résultats sont automatiquement classés en fonction d’un score de recherche, calculé sur la base de la fréquence et de la proximité des termes dans un document (valeurs dérivées de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)), les scores les plus élevés étant attribués aux documents présentant des correspondances plus nombreuses ou plus fortes sur un terme de recherche. 

Les scores de recherche donnent une impression générale de pertinence, reflétant la force de la correspondance par rapport à d’autres documents du même jeu de résultats. Mais les scores ne sont pas toujours cohérents d’une requête à l’autre. Par conséquent, lorsque vous utilisez des requêtes, vous pouvez remarquer des différences mineures dans l’ordre des documents recherchés. Il existe plusieurs explications à cette situation.

| Cause | Description |
|-----------|-------------|
| Volatilité des données | Le contenu de l’index varie au fur et à mesure que vous ajoutez, modifiez ou supprimez des documents. La fréquence des termes changera à mesure que les mises à jour de l’index seront traitées, ce qui aura un impact sur les scores de recherche des documents correspondants. |
| Réplicas multiples | Pour les services qui utilisent plusieurs réplicas, les requêtes sont émises en parallèle pour chaque réplicas. Les statistiques d’index utilisées pour calculer un score de recherche sont calculées par réplica, les résultats étant fusionnés et classés dans la réponse de la requête. Les réplicas sont principalement des miroirs les uns des autres, mais les statistiques peuvent varier en raison de petites différences d’état. Par exemple, un réplica peut avoir supprimé des documents contribuant à leurs statistiques, qui ont été fusionnées à partir d’autres réplicas. En règle générale, les différences dans les statistiques par réplica sont plus perceptibles dans les index plus petits. |
| Scores identiques | Si plusieurs documents ont le même score, chacun d’entre eux peut apparaître en premier.  |

### <a name="how-to-get-consistent-ordering"></a>Guide pratique pour obtenir un classement cohérent

Si une application nécessite un classement cohérent, vous pouvez définir explicitement une [expression **`$orderby`**](query-odata-filter-orderby-syntax.md) sur un champ. Seuls les champs qui sont indexés en tant que **`sortable`** peuvent être utilisés pour trier les résultats. Les champs couramment utilisés dans **`$orderby`** incluent les champs d’évaluation, de date et d’emplacement si vous spécifiez la valeur du paramètre **`orderby`** pour inclure les noms de champs et les appels à la [**fonction `geo.distance()`**](query-odata-filter-orderby-syntax.md) pour les valeurs géospatiales.

Une autre approche qui favorise la cohérence consiste à utiliser un [profil de score personnalisé](index-add-scoring-profiles.md). Les profils de scoring vous permettent de mieux contrôler le classement des éléments dans les résultats de recherche, avec la possibilité d’augmenter le nombre de correspondances trouvées dans des champs spécifiques. La logique de scoring supplémentaire peut aider à surmonter les différences mineures entre les réplicas, car les scores de recherche pour chaque document sont plus éloignés les uns des autres. Nous vous recommandons d’utiliser l’[algorithme de classement](index-ranking-similarity.md) pour cette approche.

## <a name="hit-highlighting"></a>Mise en surbrillance des correspondances

La mise en surbrillance des correspondances fait référence à la mise en forme de texte (par exemple, caractères gras ou surlignage jaune) appliquée au terme correspondant dans un résultat, ce qui facilite le repérage de l’occurrence. Des instructions pour la mise en surbrillance des correspondances sont fournies dans la [demande de requête](/rest/api/searchservice/search-documents). 

Pour activer la mise en surbrillance des correspondances, ajoutez `highlight=[comma-delimited list of string fields]` pour spécifier les champs qui utiliseront la mise en surbrillance. La mise en surbrillance est utile pour des champs de contenu longs, tels qu’un champ de description, où la correspondance n’est pas immédiatement évidente. Seules les définitions de champs attribuées comme **pouvant faire l’objet d’une recherche** sont éligibles pour la mise en surbrillance des correspondances.

Par défaut, la Recherche cognitive Azure renvoie jusqu’à cinq éléments en surbrillance par champ. Vous pouvez ajuster ce nombre en ajoutant au champ un tiret suivi d’un entier. Par exemple, `highlight=Description-10` retourne jusqu’à 10 mises en surbrillance de contenu correspondant dans le champ Description.

La mise en forme est appliquée aux requêtes de termes entières. Le type de mise en forme est déterminé par des balises, `highlightPreTag` et `highlightPostTag`, et votre code gère la réponse (par exemple, en appliquant une police en gras ou un arrière-plan jaune).

Dans l’exemple suivant, les termes « sablonneux », « sable », « plages » et « plage » trouvés dans le champ Description sont balisés pour la mise en surbrillance. Les requêtes qui déclenchent une extension de requête dans le moteur, telles que les recherches floues ou par caractères génériques, offrent une prise en charge limitée de la mise en surbrillance des correspondances.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Nouveau comportement (à partir du 15 juillet)

Les services créés après le 15 juillet 2020 offriront une expérience de mise en surbrillance différente. Le comportement de mise en surbrillance ne varie pas pour les services créés avant cette date. 

Avec le nouveau comportement :

+ Seules les expressions qui correspondent à la requête d’expression complète sont renvoyées. L’expression de requête « super bowl » retourne les résultats mis en surbrillance comme suit :

  ```json
  "@search.highlights": {
      "sentence": [
          "The <em>super</em> <em>bowl</em> is super awesome with a bowl of chips"
     ]
  ```

  Notez que les autres instances de *super* et *bowl* ne sont pas mises en surbrillance, car ces instances ne correspondent pas à l’expression complète.

Lorsque vous écrivez du code client qui implémente la mise en surbrillance des correspondances, tenez compte de cette modification. Notez que cela n’aura aucun impact sauf si vous créez un service de recherche entièrement nouveau.

## <a name="next-steps"></a>Étapes suivantes

Pour générer rapidement une page de recherche pour votre client, envisagez les options suivantes :

+ Dans le portail, le [générateur d’applications](search-create-app-portal.md) crée une page HTML avec une barre de recherche, une navigation par facettes et une zone de résultats qui contient des images.
+ Le tutoriel [Créer votre première application en C#](tutorial-csharp-create-first-app.md) permet de créer un client fonctionnel. L’exemple de code illustre les requêtes paginées, la mise en surbrillance des correspondances et le tri.

Plusieurs exemples de code incluent une interface web front-end que vous pouvez trouver ici : [application de démonstration New York City Jobs](https://aka.ms/azjobsdemo) ou [exemple de code JavaScript avec un site de démonstration actif](https://github.com/liamca/azure-search-javascript-samples).
