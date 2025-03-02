---
title: 'Démarrage rapide : Outil de requête de l’Explorateur de recherche'
titleSuffix: Azure Cognitive Search
description: L’Explorateur de recherche est un outil de requête dans le portail Azure qui envoie des demandes de requête à un index de recherche dans Recherche cognitive Azure. Utilisez-le pour apprendre la syntaxe, tester les expressions de requête ou inspecter un document de recherche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 08/24/2021
ms.openlocfilehash: d246c9aad024b1086a531c31a2a9559dfa798642
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772827"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Démarrage rapide : Utiliser l’Explorateur de recherche pour exécuter des requêtes dans le portail

**L’Explorateur de recherche** est un outil de requête intégré au portail Azure utilisé pour exécuter des requêtes sur un index de recherche dans Recherche cognitive Azure. Cet outil facilite l’apprentissage de la syntaxe des requêtes, le test d’une requête ou d’une expression de filtre, ou la confirmation de l’actualisation des données en vérifiant si du nouveau contenu existe dans l’index.

Ce guide de démarrage rapide utilise un index existant pour illustrer l’explorateur de recherche. 

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez disposer des prérequis suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/).

+ Service Recherche cognitive Azure. [Créez un service](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide. 

+ *realestate-us-sample-index* est utilisé pour ce guide de démarrage rapide. Utilisez le [Démarrage rapide : Créez un index](search-import-data-portal.md) pour créer l’index à l’aide des valeurs par défaut. Un exemple de source de données intégré et hébergé par Microsoft (**realestate-us-sample**) fournit les données.

## <a name="start-search-explorer"></a>Démarrer l’Explorateur de recherche

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la page de vue d’ensemble de la recherche à partir du tableau de bord ou [recherchez votre service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Ouvrez l’Explorateur de recherche dans la barre de commandes :

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Commande d’accès à l’explorateur de recherche dans le portail" border="true":::

    Ou utilisez l’onglet **Explorateur de recherche** intégré sur un index ouvert :

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Onglet Explorateur de recherche" border="true":::

## <a name="unspecified-query"></a>Requête non spécifiée

Dans l’Explorateur de recherche, les requêtes sont formulées à l’aide de [l’API REST Search](/rest/api/searchservice/search-documents), et les réponses sont retournées sous forme de documents JSON détaillés.

Pour un premier aperçu du contenu, exécutez une recherche vide en cliquant sur **Rechercher** sans indiquer de termes. Une recherche vide est utile en tant que première requête, car elle renvoie des documents entiers pour vous permettre d'examiner leur composition. Une recherche vide ne présente aucun classement et les documents sont renvoyés dans une ordre arbitraire (`"@search.score": 1` pour tous les documents). Par défaut, 50 documents sont renvoyés dans une requête de recherche.

La syntaxe équivalente d'une recherche vide est `*` ou `search=*`.
   
   ```http
   search=*
   ```

   **Résultats**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Exemple de requête vide non qualifiée ou vide" border="true":::

## <a name="free-text-search"></a>Recherche en texte libre

Les requêtes de forme libre, avec ou sans opérateurs, permettent de simuler les requêtes définies par l’utilisateur envoyées à partir d’une application personnalisée vers la Recherche cognitive Azure. Seuls les champs attribués en tant que **Recherche possible** dans la définition de l’index sont analysés pour rechercher les correspondances. 

Notez que lorsque vous indiquez des critères de recherche, comme des termes ou expressions de requête, le classement de la recherche intervient. L'exemple suivant illustre une recherche de texte libre.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Résultats**

   Vous pouvez utiliser Ctrl+F pour rechercher des termes spécifiques dans les résultats.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Exemple de requête de texte libre" border="true":::

## <a name="count-of-matching-documents"></a>Nombre de documents correspondants 

Ajoutez **$count=true** pour obtenir le nombre de correspondances trouvées dans un index. Dans une recherche vide, ce nombre correspond au nombre total de documents dans l’index. Dans une recherche qualifiée, il correspond au nombre de documents correspondant à l’entrée de requête. N’oubliez pas que le service retourne les 50 premières correspondances par défaut. Il est donc possible qu’il y ait plus de correspondances dans l’index que ce qui est inclus dans les résultats.

   ```http
   $count=true
   ```

   **Résultats**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Nombre de documents correspondants dans l’index" border="true":::

## <a name="limit-fields-in-search-results"></a>Limiter les champs dans les résultats de la recherche

Ajoutez [ **$select**](search-query-odata-select.md) pour limiter les résultats aux champs explicitement nommés et disposer d’une sortie plus lisible dans **l’Explorateur de recherche**. Pour conserver la chaîne de recherche et **$count = true**, faites précéder les arguments de **&** . 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Résultats**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Restreindre les champs dans les résultats de la recherche" border="true":::

## <a name="return-next-batch-of-results"></a>Retourner le jeu de résultats suivant

La Recherche cognitive Azure retourne les 50 premières correspondances selon le classement de la recherche. Pour obtenir le jeu de documents correspondants suivant, ajoutez **$top = 100 & $skip = 50** afin d'augmenter le jeu de résultats à 100 documents (50 par défaut, 1 000 maximum), en ignorant les 50 premiers documents. Vous pouvez vérifier la clé de document (listingID) pour identifier un document. 

N'oubliez pas qu'il vous faut fournir des critères de recherche, terme ou expression de requête, pour obtenir des résultats classés. Notez que les scores de recherche diminuent au fil des résultats de la recherche.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Résultats**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Retourner le lot de résultats de recherche suivant" border="true":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filtrer les expressions (supérieur à, inférieur à, égal à)

Utilisez le paramètre [ **$filter**](search-query-odata-filter.md) lorsque vous souhaitez spécifier des critères précis plutôt qu’une recherche de texte libre. Le champ doit être attribué comme **Filtrable** dans l’index. Cet exemple recherche plus de 3 chambres :

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Résultats**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Filtrer par critère" border="true":::

## <a name="order-by-expressions"></a>Expressions OrderBy

Ajoutez [ **$orderby**](search-query-odata-orderby.md) pour trier les résultats selon un autre champ, à côté du score de recherche. Le champ doit être attribué comme **Triable** dans l’index. Pour le tester, vous pouvez utiliser l'exemple d'expression suivant :

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Résultats**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Changer l’ordre de tri" border="true":::

Les expressions **$filter** et **$orderby** sont des constructions OData. Pour plus d’informations, consultez l’article [Filter OData syntax](/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Syntaxe d’expression de filtre OData).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Éléments importants à retenir

Dans ce guide de démarrage rapide, vous avez utilisé **l’Explorateur de recherche** pour interroger un index à l’aide de l’API REST.

+ Les résultats sont renvoyés sous forme de documents JSON détaillés afin de vous permettre de les consulter dans leur intégralité. Le paramètre **$select** dans une expression de requête peut limiter les champs qui sont retournés.

+ Les documents sont composés de tous les champs marqués comme **récupérables** dans l’index. Pour visualiser les attributs d’index dans le portail, cliquez sur *realestate-us-sample* dans la liste **Index** de la page de vue d'ensemble de la recherche.

+ Les requêtes de forme libre, semblables à celles que vous entrez dans un navigateur web commercial, permettent de tester une expérience d'utilisateur final. Par exemple, dans le cas de l'exemple d'index realestate intégré, vous pourriez entrer « Seattle apartments lake washington », puis utiliser Ctrl+F pour rechercher des termes dans les résultats de la recherche. 

+ Les expressions de requête et de filtre sont articulées dans une syntaxe implémentée par la Recherche cognitive Azure. Par défaut, cela correspond à une [syntaxe simple](/rest/api/searchservice/simple-query-syntax-in-azure-search), mais vous pouvez également utiliser [Lucene complète](/rest/api/searchservice/lucene-query-syntax-in-azure-search) pour de plus puissantes requêtes. Les [expressions de filtre](/rest/api/searchservice/odata-expression-syntax-for-azure-search) présentent une syntaxe OData.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous travaillez dans votre propre abonnement, il est recommandé, à la fin de chaque projet, de déterminer si vous avez toujours besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la syntaxe et les structures des requêtes, utilisez Postman ou un outil équivalent pour créer des expressions de requête qui utilisent d’autres parties de l’API. [L’API REST Recherche](/rest/api/searchservice/search-documents) est particulièrement utile pour l’apprentissage et l’exploration.

> [!div class="nextstepaction"]
> [Créer une requête de base dans Postman](search-get-started-rest.md)