---
title: Ajouter des profils de scoring pour surévaluer les scores de recherche
titleSuffix: Azure Cognitive Search
description: Surévaluez les scores de pertinence des résultats de Recherche cognitive Azure en ajoutant des profils de scoring à un index de recherche.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 757290560f921ee87cf9454037a4f8f199283f29
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449485"
---
# <a name="add-scoring-profiles-to-a-search-index"></a>Ajouter des profils de scoring à un index de recherche

Pour les requêtes de recherche en texte intégral, le moteur de recherche calcule un score de recherche pour chaque document correspondant, ce qui permet de classer les résultats de haut en bas. Recherche cognitive Azure utilise un algorithme de scoring par défaut pour calculer un score initial, mais vous pouvez personnaliser le calcul grâce à un *profil de scoring*.

Les profils de scoring sont incorporés dans les définitions d’index et comprennent des propriétés permettant de surévaluer le score des correspondances, où des critères supplémentaires trouvés dans le profil fournissent la logique de surévaluation. Par exemple, vous pouvez surévaluer les correspondances en fonction de leur potentiel de chiffre d’affaires, promouvoir les nouveaux articles ou surévaluer le score des articles qui sont en stock depuis trop longtemps.  

Les concepts de pertinence ne vous sont pas familiers ? La vidéo suivante explique rapidement le fonctionnement des profils de scoring dans Recherche cognitive Azure, mais elle couvre également les concepts de base. Vous pouvez également consulter [Classement de similarité et scoring](index-similarity-and-scoring.md) pour en savoir plus.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="what-is-a-scoring-profile"></a>Qu’est-ce qu’un profil de scoring ?

Un profil de scoring fait partie de la définition d’index et se compose de champs, de fonctions et de paramètres pondérés. L’objectif d’un profil de scoring est de surévaluer ou d’amplifier les documents correspondants en fonction des critères que vous fournissez. 

La définition suivante montre un profil simple nommé « géo ». Celui-ci surévalue les résultats dont le champ hotelName contient le terme recherché. Il utilise également la fonction `distance` pour favoriser les résultats qui se trouvent dans un rayon de dix kilomètres de l’emplacement actuel. Si quelqu'un effectue une recherche sur le terme « inn » dans un rayon de 10 km par rapport à la position actuelle, les documents d'hôtels dont le nom contient cette chaîne de caractères apparaissent en tête des résultats de la recherche.  

```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  

Pour utiliser ce profil de scoring, votre requête est formulée de façon à spécifier le paramètre scoringProfile dans la requête.

```http
POST /indexes/hotels/docs&api-version=2020-06-30
{
    "search": "inn",
    "scoringProfile": "geo",
    "scoringParameter": currentLocation--122.123,44.77233
}
```  

Cette requête recherche le terme « inn » et transmet l’emplacement actuel. Notez que cette requête inclut d’autres paramètres, tels que scoringParameter. Les paramètres de requête sont décrits dans [Recherche dans des documents (API REST)](/rest/api/searchservice/Search-Documents).  

Pour voir un exemple plus détaillé de profil de scoring, consultez [Exemple étendu](#bkmk_ex).  

<a name=what-is-default-scoring></a>

## <a name="how-scores-are-computed"></a>Mode de calcul des scores

Les scores sont calculés pour les requêtes de recherche en texte intégral afin de classer les correspondances les plus pertinentes et de les renvoyer en tête de la réponse. Le score global pour chaque document est une agrégation des scores individuels pour chaque champ, où le score individuel de chaque champ est calculé sur la base de la fréquence des termes et de la fréquence des documents des termes recherchés dans ce champ (connu sous le nom de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) ou « fréquence de terme-fréquence inverse de document »). 

> [!Tip]
> Vous pouvez utiliser le paramètre [featuresMode](index-similarity-and-scoring.md#featuresmode-parameter-preview) pour demander des détails de scoring supplémentaires avec les résultats de la recherche (y compris les scores au niveau des champs).

## <a name="when-to-add-scoring-logic"></a>Quand ajouter une logique de notation

Quand le classement par défaut produit des résultats trop éloignés de vos objectifs, vous devez créer un ou plusieurs profils de calcul de score. Par exemple, vous pouvez décider que la pertinence de la recherche doit privilégier les éléments récemment ajoutés. De même, il se peut qu'un champ affiche une marge bénéficiaire, ou un autre un revenu potentiel. La surévaluation des résultats qui sont plus significatifs pour vos utilisateurs ou pour l’entreprise est souvent le facteur décisif dans l’adoption des profils de scoring.

Le classement par pertinence dans une page de recherche est mis en œuvre par l’intermédiaire de profils de scoring. Songez aux pages de résultats de recherche que vous avez utilisées par le passé, qui vous permettaient de trier par prix, date, évaluation ou pertinence. Dans Recherche cognitive Azure, les profils de scoring peuvent être utilisés pour gérer l’option « pertinence ». Vous contrôlez la définition de la pertinence en fonction de vos objectifs et du type d'expérience de recherche que vous souhaitez.  

<a name="bkmk_ex"></a>

## <a name="extended-example"></a>Exemple étendu

L’exemple suivant montre le schéma d’un index comprenant deux profils de scoring (`boostGenre`, `newAndHighlyRated`). Toute requête sur cet index qui comprend un profil comme paramètre de requête utilise le profil pour évaluer le jeu de résultats. 

Le profil `boostGenre` utilise des champs de texte pondérés, surévaluant les correspondances trouvées dans les champs albumTitle, genre, et artistName. Ces champs sont respectivement multipliés par 1,5, 5 et 2. Pourquoi la pondération du champ genre est-elle beaucoup plus élevée que celle des autres champs ? Si la recherche est effectuée sur des données relativement homogènes (comme c’est le cas de « genre » dans musicstoreindex), il se peut que vous ayez besoin d’une variance plus importante dans les pondérations relatives. Par exemple, dans musicstoreindex, « rock » apparaît à la fois comme genre et dans des descriptions de genre formulées de façon identique. Si vous souhaitez que le genre ait une pondération plus élevée que la description du genre, la pondération relative du champ Genre doit être sensiblement plus importante.

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="steps-for-adding-a-scoring-profile"></a>Étapes d’ajout d’un profil de scoring

Pour mettre en œuvre un calcul de score personnalisé, ajoutez un profil de calcul de score au schéma qui définit l'index. Vous pouvez avoir jusqu’à 100 profils de calcul de score au sein d’un même index (consultez [Limites de service](search-limits-quotas-capacity.md)), mais vous ne pouvez spécifier qu’un seul profil à la fois dans une requête donnée.

1. Commencez par une définition d’index. Vous pouvez ajouter et mettre à jour des profils de scoring sur un index existant sans avoir à le régénérer. Utilisez une requête [Update Index](/rest/api/searchservice/update-index) pour afficher votre révision.

1. Collez le [modèle](#bkmk_template) fourni dans cette rubrique.  

1. Donnez-lui un nom. Les profils de calcul de score sont facultatifs mais, si vous en ajoutez un, le nom est obligatoire. Veillez à respecter les [conventions d’affectation de noms](/rest/api/searchservice/naming-rules) de Recherche cognitive pour les champs (commencer par une lettre, éviter les caractères spéciaux et les mots réservés).  

1. Spécifiez les critères de surévaluation. Un profil unique peut contenir des [champs pondérés](#weighted-fields), des [fonctions](#functions), ou les deux. 

Vous devez travailler de manière itérative, en utilisant un jeu de données qui vous aidera à prouver ou à réfuter l’efficacité d’un profil donné.

Les profils de scoring peuvent être définis dans le portail Azure, comme le montre la capture d’écran suivante, ou par programmation via les [API REST](/rest/api/searchservice/update-index) ou les Kits de développement logiciel (SDK) Azure, comme la classe [ScoringProfile](/dotnet/api/azure.search.documents.indexes.models.scoringprofile) du SDK Azure pour .NET.

   :::image type="content" source="media/scoring-profiles/portal-add-scoring-profile-small.png" alt-text="Page Ajouter des profils de scoring" lightbox="media/scoring-profiles/portal-add-scoring-profile.png" border="true":::

<a name="weighted-fields"></a>

### <a name="using-weighted-fields"></a>Utilisation de champs pondérés

Utilisez des champs pondérés lorsque le contexte du champ est important et que les requêtes sont une recherche en texte intégral (également appelées recherche de texte libre). Par exemple, si une requête comprend le terme « aéroport », vous souhaiterez peut-être que « aéroport » ait plus de poids dans le champ Description que dans le champ HotelName. 

Les champs pondérés se composent d’un champ avec recherche possible et d’un nombre positif utilisé comme multiplicateur. Si le score original du champ HotelName est de 3, le score surévalué pour ce champ est de 6, ce qui contribue à un score global plus élevé pour le document parent lui-même.

```json
"scoringProfiles": [  
{  
  "name": "boostKeywords",  
  "text": {  
    "weights": {  
      "HotelName": 2,  
      "Description": 5 
    }  
  }  
}
```

<a name="functions"></a>

### <a name="using-functions"></a>Utilisation des fonctions

Utilisez des fonctions lorsque les pondérations relatives simples sont insuffisantes ou ne s’appliquent pas, comme dans le cas de « distance » et de « freshness », qui sont des calculs sur des données numériques. Vous pouvez spécifier plusieurs fonctions par profil de scoring.

| Fonction | Description |
|-|-|
| « freshness » | Augmente par valeurs d’un champ DateHeure (Edm.DataTimeOffset). Cette fonction a un attribut `boostingDuration` qui vous permet de spécifier une valeur représentant un intervalle de temps pendant lequel la surévaluation se produit. | 
| « magnitude » | Surévalue selon le niveau élevé ou bas d’une valeur numérique. Parmi les scénarios qui appellent cette fonction figurent la valorisation de la marge bénéficiaire, du prix le plus élevé, du prix le plus bas ou du nombre de téléchargements. Cette fonction ne peut être utilisée qu’avec les champs Edm.Double et Edm.Int. Pour la fonction magnitude, vous pouvez inverser la plage (de la valeur la plus élevée à la valeur la plus basse) si vous souhaitez obtenir le modèle inverse (par exemple, pour surévaluer les articles les moins chers davantage que les articles les plus chers). Pour une gamme de prix allant de 100 USD à 1 USD, vous pouvez définir « boostingRangeStart » à 100 et « boostingRangeEnd » à 1 pour surévaluer les articles les moins chers. | 
| « distance »  | Surévalue en fonction de la proximité ou de l’emplacement géographique. Cette fonction ne peut être utilisée qu’avec les champs Edm.GeographyPoint. | 
| « tag »  | Surévalue selon les étiquettes communes aux documents de recherche et aux chaînes de requête. Les balises sont fournies dans un `tagsParameter`. Cette fonction ne peut être utilisée qu’avec les champs Edm.String et Collection(Edm.String). | 

### <a name="rules-for-using-functions"></a>Règles d'utilisation des fonctions

+ Les fonctions ne peuvent être appliquées qu’aux champs filtrables.
+ Le type de fonction (« freshness », « magnitude », « distance », « tag ») doit être en lettres minuscules.
+ Les fonctions ne peuvent pas contenir de valeurs null ou vides.

<a name="bkmk_template"></a>

## <a name="template"></a>Modèle

 Cette section présente la syntaxe et le modèle de profils de calcul de score. Pour obtenir des descriptions des attributs des profils de scoring, consultez [Informations de référence sur les propriétés](#bkmk_indexref) dans la section suivante.  

```json
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "(...)",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...", 
```  

<a name="bkmk_indexref"></a> 

## <a name="property-reference"></a>Informations de référence sur les propriétés

|Attribut|Description|  
|---------------|-----------------|  
| name | Obligatoire. Nom du profil de calcul de score. Il suit les conventions d'affectation de noms applicables aux champs. Il doit commencer par une lettre, ne peut pas contenir les signes point, deux-points ou @, et ne peut pas commencer par l’expression azureSearch (respecte la casse).|  
| texte | Contient la propriété weights.|  
| weights | Optionnel. Des paires nom-valeur qui spécifient un champ avec recherche possible et un entier positif ou un nombre à virgule flottante pour surévaluer le score du champ. L’entier ou le nombre positif devient un multiplicateur du score original du champ généré par l’algorithme de classement. Par exemple, si le score d’un champ est de 2 et que la valeur de pondération est de 3, le score surévalué du champ est de 6. Les scores des champs individuels sont ensuite agrégés pour créer un score de champ de document, qui est ensuite utilisé pour classer le document parmi les résultats. |  
| functions | facultatif. Vous pouvez appliquer une fonction de calcul de score uniquement à des champs filtrables.|  
| functions > type | Obligatoire pour les fonctions de calcul de score. Indique le type de fonction à utiliser. Les valeurs autorisées sont magnitude, freshness, distance et tag. Vous pouvez inclure plusieurs fonctions dans chaque profil de calcul de score. Le nom de fonction doit être en lettres minuscules.|  
| functions > boost | Obligatoire pour les fonctions de calcul de score. Nombre positif utilisé comme multiplicateur pour le score brut. Il ne peut pas être égal à 1.|  
| functions > fieldname | Obligatoire pour les fonctions de calcul de score. Vous pouvez appliquer une fonction de calcul de score uniquement à des champs faisant partie de la collection de champs de l'index, et qui sont filtrables. De plus, chaque type de fonction fait l'objet de restrictions supplémentaires (la valeur freshness est utilisée avec des champs datetime, la valeur amplitude avec des champs integer ou double, et la valeur distance avec des champs location). Vous pouvez spécifier un seul champ par définition de fonction. Par exemple, pour utiliser une valeur magnitude deux fois dans le même profil, vous devez inclure deux définitions de magnitude, une pour chaque champ.|  
| functions > interpolation | Obligatoire pour les fonctions de calcul de score. Définit la pente pour laquelle la valorisation du score augmente, du début à la fin de la plage. Les valeurs autorisées sont Linear (par défaut), Constant, Quadratic et Logarithmic. Consultez la rubrique [Définition d’interpolations](#bkmk_interpolation) pour plus d’informations.|  
| functions > magnitude | La fonction de calcul de score magnitude est utilisée pour modifier des classements en fonction de la plage de valeurs pour un champ numérique. Voici quelques exemples d’utilisation courants : </br></br>« Évaluations » : modifiez le scoring en fonction de la valeur figurant dans le champ « Star Rating ». Lorsque deux éléments sont pertinents, l’élément associé à l’évaluation la plus élevée s’affiche en premier. </br>« Marge » : quand deux documents sont pertinents, un détaillant peut souhaiter surévaluer le document présentant les marges les plus élevées. </br>« Nombres de clics » : pour les applications qui suivent les actions de clic sur des produits ou des pages, vous pouvez utiliser la fonction magnitude pour surévaluer les éléments qui drainent le plus de trafic. </br>« Nombres de téléchargements » : pour les applications qui suivent les téléchargements, la fonction magnitude permet de surévaluer les éléments les plus téléchargés.|  
| functions > magnitude > boostingRangeStart | Définit la valeur de début de la plage sur la base de laquelle les scores de magnitude sont calculés. La valeur doit être un entier ou un nombre à virgule flottante. Pour des évaluations de 1 à 4, il s'agit de 1. Pour des marges de plus de 50 %, il s'agit de 50.|  
| functions > magnitude > boostingRangeEnd | Définit la valeur de fin de la plage sur laquelle les scores de magnitude sont calculés. La valeur doit être un entier ou un nombre à virgule flottante. Pour les évaluations de 1 à 4, il s'agit de 4.|  
| functions > magnitude > constantBoostBeyondRange | Les valeurs autorisées sont true ou false (par défaut). Quand la valeur est true, la valorisation complète continue de s'appliquer aux documents dont la valeur pour le champ cible est supérieure à la limite supérieure de la plage. Quand la valeur est false, la valorisation de cette fonction ne s'applique pas aux documents dont la valeur pour le champ cible se situe en dehors de la plage.|  
| functions > freshness | La fonction de calcul de score freshness permet de modifier les scores de classement d'éléments en fonction des valeurs des champs DateTimeOffset. Par exemple, un élément dont la date est plus récente peut être classé plus haut que des éléments plus anciens. </br></br>Il est également possible de classer les éléments tels que les événements de calendrier comportant des dates ultérieures afin que les éléments plus proches de la date du jour soient classés plus haut que les éléments plus éloignés dans le temps. </br></br>Dans la version de service actuelle, une extrémité de la plage doit être définie sur l'heure réelle. L’autre extrémité est une heure dans le passé selon l’attribut boostingDuration. Pour surévaluer une plage d’heures à venir, utilisez un attribut boostingDuration négatif. </br></br>La vitesse à laquelle la valorisation passe d'une plage maximale à une plage minimale est déterminée par l'interpolation appliquée au profil de calcul de score (voir la figure ci-dessous). Pour inverser le facteur de valorisation appliqué, choisissez un facteur inférieur à 1.|  
| functions > freshness > boostingDuration | Définit une période d'expiration après laquelle la valorisation s'arrête pour un document spécifique. Pour plus d’informations sur la syntaxe et des exemples, consultez [Set boostingDuration](#bkmk_boostdur) dans la section suivante.|  
| functions > distance | La fonction de calcul de score à distance est utilisée pour affecter le score de documents sur la base de leur proximité ou de l'éloignement par rapport à un emplacement géographique de référence. L’emplacement de référence est indiqué comme partie intégrante de la requête dans un paramètre (à l’aide du paramètre de requête scoringParameter) en tant qu’argument lon,lat.|  
|functions > distance > referencePointParameter | Paramètre à transmettre dans les requêtes, à utiliser comme emplacement de référence (à l’aide du paramètre de requête scoringParameter). Pour obtenir une description des paramètres de requête, consultez [Rechercher des documents (API REST)](/rest/api/searchservice/Search-Documents).|  
| functions > distance > boostingDistance | Nombre indiquant la distance en kilomètres par rapport à l'emplacement de référence où la valorisation se termine.|  
| functions > tag | La fonction de calcul de score de balises est utilisée pour affecter le score de documents sur la base de balises dans des documents et des requêtes de recherche. Les documents contenant des balises communes avec la requête de recherche seront privilégiés. Les étiquettes pour la requête de recherche sont fournies en tant que paramètre de scoring dans chaque requête de recherche (à l’aide du paramètre de requête scoringParameter). |  
| functions > tag > tagsParameter | Paramètre à transmettre dans les requêtes pour spécifier les étiquettes d’une requête particulière (à l’aide du paramètre de requête scoringParameter). Pour obtenir une description des paramètres de requête, consultez [Rechercher des documents (API REST)](/rest/api/searchservice/Search-Documents).|  
| functions > functionAggregation | facultatif. S'applique uniquement quand des fonctions sont spécifiées. Les valeurs autorisées sont les suivantes : sum (par défaut), average, minimum, maximum et firstMatching. Un score de recherche est une valeur unique calculée à partir de plusieurs variables, notamment plusieurs fonctions. Cet attribut indique comment les valorisations de toutes les fonctions sont combinées en une valorisation agrégée qui est ensuite appliquée au score du document de base. Le score de base dépend de la valeur [tf-idf](http://www.tfidf.com/) calculée à partir du document et de la requête de recherche.|  
| defaultScoringProfile | Lors de l'exécution d'une requête de recherche, le calcul de score par défaut est utilisé ([tf-idf](http://www.tfidf.com/) uniquement) si aucun profil de calcul de score n'est spécifié. </br></br>Vous pouvez remplacer la valeur par défaut intégrée, en substituant un profil personnalisé comme celui à utiliser lorsqu’aucun profil spécifique n’est indiqué dans la requête de recherche.|  

<a name="bkmk_interpolation"></a> 

## <a name="set-interpolations"></a>Définition d’interpolations

Les interpolations vous permettent de définir la forme de la pente utilisée pour calculer les scores. Le score allant d'élevé à faible, la pente est toujours décroissante, mais l’interpolation détermine la courbe de la pente descendante. Les interpolations utilisables sont les suivantes :  

| Interpolation | Description |  
|-|-|  
|`linear`|pour les éléments qui s’inscrivent dans la plage de max à min, la valorisation appliquée décroît de manière constante. L'interpolation de type Linear est l'interpolation par défaut pour un profil de calcul de score.|  
|`constant`|Pour les éléments qui s'inscrivent dans la plage du début à la fin, une valorisation constante est appliquée aux résultats du classement.|  
|`quadratic`|Par rapport à une interpolation de type Linear dont la valorisation décroît de façon constante, une interpolation de type Quadratic décroît initialement plus lentement, puis, lorsqu’elle approche de la plage de fin, beaucoup plus rapidement. Cette option d'interpolation n'est pas autorisée dans les fonctions de calcul de score de balises.|  
|`logarithmic`|Par rapport à une interpolation de type Linear dont la valorisation décroît de façon constante, une interpolation de type Logarithmic décroît initialement plus rapidement, puis, lorsqu’elle approche de la plage de fin, beaucoup plus lentement. Cette option d'interpolation n'est pas autorisée dans les fonctions de calcul de score de balises.|  

 ![Lignes constantes, linéaires, quadratiques, log10 sur le graphique](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

<a name="bkmk_boostdur"></a> 

## <a name="set-boostingduration"></a>Définition de boostingDuration

`boostingDuration` est un attribut de la fonction `freshness`. Il permet de définir une période d'expiration après laquelle la valorisation s'arrête pour un document spécifique. Par exemple, pour valoriser une ligne de produits ou une marque pendant une période promotionnelle de 10 jours, vous spécifiez la période de 10 jours en tant que « P10D » pour les documents correspondants.  

`boostingDuration` doit être au format « dayTimeDuration » XSD (sous-ensemble limité d'une valeur de durée ISO 8601). Le modèle est le suivant : « P[nD][T[nH][nM][nS]] ».  

Le tableau suivant fournit plusieurs exemples.  

|Duration|boostingDuration|  
|--------------|----------------------|  
|1 jour|« P1D »|  
|2 jours et 12 heures|« P2DT12H »|  
|15 minutes|« PT15M »|  
|30 jours, 5 heures 10 minutes, et 6 334 secondes|« P30DT5H10M6.334S »|  

Pour plus d’exemples, consultez [Schéma XML : types de données (site web W3.org)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Voir aussi

+ [Classement de similarité et scoring dans Recherche cognitive Azure](index-similarity-and-scoring.md)
+ [Référence d’API REST](/rest/api/searchservice/)
+ [Créer une API d’index](/rest/api/searchservice/create-index)
+ [Kit de développement logiciel (SDK) de Recherche cognitive Azure .NET](/dotnet/api/overview/azure/search?)
+ [Que sont les profils de scoring ?](https://social.technet.microsoft.com/wiki/contents/articles/26706.azure-search-what-are-scoring-profiles.aspx)
