---
title: Informations de référence sur le langage de requête Azure Digital Twins – clause SELECT
titleSuffix: Azure Digital Twins
description: Documentation de référence sur la clause SELECT du langage de requête Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: c911a3ea8607cb83cb3d96a44bfac424042df30a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232865"
---
# <a name="azure-digital-twins-query-language-reference-select-clause"></a>Informations de référence sur le langage de requête Azure Digital Twins : clause SELECT

Ce document contient des informations de référence sur la **clause SELECT** du [langage de requête Azure Digital Twins](concepts-query-language.md).

La clause SELECT constitue la première partie d’une requête. Elle spécifie la liste des colonnes renvoyées par la requête.

Cette clause est requise pour toutes les requêtes.

## <a name="select-"></a>SELECT *

Utilisez le caractère `*` dans une instruction SELECT pour projeter le document de jumeau numérique tel que, sans l’attribuer à une propriété dans le jeu de résultats.

>[!NOTE]
> `SELECT *` est une syntaxe valide uniquement lorsque la requête n’utilise pas de `JOIN`. Pour plus d’informations sur les requêtes utilisant `JOIN`, consultez [Informations de référence sur le langage de requête Azure Digital Twins  : clause JOIN](reference-query-clause-join.md).

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectSyntax":::

### <a name="returns"></a>Retours

Jeu des propriétés qui sont retournées par la requête.

### <a name="example"></a>Exemple

La requête suivante renvoie tous les jumeaux numériques de l’instance. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectExample":::

## <a name="select-columns-with-projections"></a>Colonnes SELECT avec projections

Vous pouvez utiliser des projections dans la clause SELECT pour choisir les colonnes renvoyées par une requête. Vous pouvez spécifier des collections nommées de jumeaux et de relations, ou des propriétés de jumeaux et de relations.

La projection est désormais prise en charge pour les propriétés primitives et complexes.

### <a name="syntax"></a>Syntaxe

Pour projeter une collection :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectCollectionSyntax":::

Pour projeter une propriété :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertySyntax":::

### <a name="returns"></a>Retours

Collection de jumeaux, propriétés ou relations spécifiés dans la projection.

Si une propriété incluse dans la projection n’est pas présente pour une ligne de données spécifique, la propriété n’est pas présente dans le jeu de résultats. Pour obtenir un exemple de ce comportement, consultez [Exemple de projection de propriété : propriété non présente pour une ligne de données](#project-property-example-property-not-present-for-a-data-row).

### <a name="examples"></a>Exemples

#### <a name="example-scenario"></a>Exemple de scénario

Pour les exemples suivants, envisagez un graphique de jumeaux contenant les éléments de données suivants :
* Un jumeau de fabrique appelé FactoryA
    - Contient une propriété appelée `name` avec la valeur `FactoryA`
* Un jumeau de consommateur appelé Contoso
    - Contient une propriété appelée `name` avec la valeur `Contoso`
* Une relation consumerRelationship de FactoryA à Contoso, appelée `FactoryA-consumerRelationship-Contoso`
    - Contient une propriété appelée `managedBy` avec la valeur `Jeff`

Le diagramme suivant illustre ce scénario :

:::row:::
    :::column:::
        :::image type="content" source="media/reference-query-clause-select/projections-graph.png" alt-text="Diagramme montrant l’exemple de graphique décrit ci-dessus.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="project-collection-example"></a>Exemple de projection de collection

Vous trouverez ci-dessous un exemple de requête qui projette une collection à partir de ce graphique. La requête suivante renvoie tous les jumeaux numériques de l’instance, en nommant l’ensemble de la collection de jumeaux `T` et en projetant `T` en tant que collection à renvoyer. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectCollectionExample":::

Voici la charge utile JSON qui est retournée par cette requête :

```json
{
  "value": [
    {
      "result": [
        {
          "T": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          }
        },
        {
          "T": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-with-join-example"></a>Exemple de projection avec JOIN

La projection est couramment utilisée pour renvoyer une collection spécifiée dans un `JOIN`. La requête suivante utilise la projection pour retourner les données du consommateur, de la fabrique et de la relation. Pour plus d’informations sur la syntaxe `JOIN` utilisée dans l’exemple, consultez [Informations de référence sur le langage de requête Azure Digital Twins : clause JOIN](reference-query-clause-join.md).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectJoinExample":::

Voici la charge utile JSON qui est retournée par cette requête :

```json
{
  "value": [
    {
      "result": [
        {
          "Consumer": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          },
          "Factory": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          },
          "Relationship": {
            "$etag": "W/\"f01e07c1-19e4-4bbe-a12d-f5761e86d3e8\"",
            "$relationshipId": "FactoryA-consumerRelationship-Contoso",
            "$relationshipName": "consumerRelationship",
            "$sourceId": "FactoryA",
            "$targetId": "Contoso",
            "managedBy": "Jeff"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-property-example"></a>Exemple de projection de propriété

Voici un exemple projetant une propriété. La requête suivante utilise la projection pour renvoyer la propriété `name` du jumeau de consommateur et la propriété `managedBy` de la relation.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertyExample":::

Voici la charge utile JSON qui est retournée par cette requête :

```json
{
  "value": [
    {
      "result": [
        {
          "managedBy": "Jeff",
          "name": "Contoso"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-property-example-property-not-present-for-a-data-row"></a>Exemple de projection de propriété : propriété non présente pour une ligne de données

Si une propriété incluse dans la projection n’est pas présente pour une ligne de données spécifique, la propriété n’est pas présente dans le jeu de résultats.

Prenez l’exemple d’un ensemble de jumeaux représentant des personnes. Des âges sont associés à certains jumeaux, mais pas à d’autres.

Voici une requête qui projette les propriétés `name` et `age` :

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertyNotPresentExample":::

Le résultat peut ressembler à ceci, avec la propriété `age` absente de certains jumeaux dans le résultat où les jumeaux ne présentent pas cette propriété.

```json
{
  "value": [
    {
      "result": [
        {
          "name": "John",
          "age": 27
        },
        {
          "name": "Keanu"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

## <a name="select-count"></a>SELECT COUNT

Utilisez cette méthode pour compter le nombre d’éléments du jeu de résultats et renvoyer ce nombre.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountSyntax":::

### <a name="arguments"></a>Arguments

Aucun.

### <a name="returns"></a>Retours

Valeur `int`.

### <a name="example"></a>Exemple

La requête suivante renvoie le nombre de jumeaux numériques de l’instance.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountExample":::

La requête suivante renvoie le nombre de relations de l’instance.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountRelationshipsExample":::

## <a name="select-top"></a>SELECT TOP

Utilisez cette méthode pour retourner uniquement les principaux éléments qui satisfont aux exigences de requête.

### <a name="syntax"></a>Syntaxe

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectTopSyntax":::

### <a name="arguments"></a>Arguments

Valeur `int` spécifiant le nombre d’éléments principaux à sélectionner.

### <a name="returns"></a>Retours

Collection de jumeaux.

### <a name="example"></a>Exemple

La requête suivante renvoie uniquement les cinq premiers jumeaux numériques de l’instance.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectTopExample":::