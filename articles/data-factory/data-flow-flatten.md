---
title: Transformation d’aplatissement dans le flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: Dénormalisez des données hiérarchiques à l’aide de la transformation d’aplatissement dans les pipelines Azure Data Factory et Synapse Analytics.
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 4fb8d5ea1bfaa9534f7db27296d3cb7f61d0c4fb
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274952"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Transformation d’aplatissement dans le flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Utilisez la transformation d’aplatissement pour prendre des valeurs de tableau à l’intérieur de structures hiérarchiques, par exemple au format JSON, et les dérouler dans des lignes individuelles. Ce processus est appelé « dénormalisation ».

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWLX9j]

## <a name="configuration"></a>Configuration

La transformation d’aplatissement contient les paramètres de configuration suivants :

:::image type="content" source="media/data-flow/flatten1.png" alt-text="Paramètres d’aplatissement":::

### <a name="unroll-by"></a>Dérouler par

Sélectionnez un tableau à dérouler. Les données de sortie comporteront une ligne par élément dans chaque tableau. Si le tableau « Dérouler par » sur la ligne d’entrée est null ou vide, il y aura une ligne de sortie avec des valeurs non déroulées en tant que null.

### <a name="unroll-root"></a>Racine du déroulement

Par défaut, la transformation d’aplatissement déroule un tableau au sommet de la hiérarchie dans laquelle il existe. Vous pouvez éventuellement sélectionner un tableau comme racine de déroulement. La racine de déroulement doit être un tableau d’objets complexes qui est ou contient le tableau « Dérouler par ». Si une racine de déroulement est sélectionnée, les données de sortie contiennent au moins une ligne par élément dans la racine de déroulement. Si la ligne d’entrée n’a aucun élément dans la racine de déroulement, elle est supprimée des données de sortie. Le choix d’une racine de déroulement génère toujours un nombre de lignes inférieur ou égal au comportement par défaut.

### <a name="flatten-mapping"></a>Mappage de l’aplatissement

Comme pour la transformation de sélection, choisissez la projection de la nouvelle structure à partir des champs entrants et du tableau dénormalisé. Si un tableau dénormalisé est mappé, la colonne de sortie a le même type de données que le tableau. Si le tableau « Dérouler par » est un tableau d’objets complexes qui contient des sous-tableaux, le mappage d’un élément de ce sous-tableau génère un tableau.

Pour vérifier votre sortie de mappage, reportez-vous à l’onglet Inspection et à la vue d’ensemble des données.

## <a name="rule-based-mapping"></a>Mappage basé sur des règles

La transformation d’aplatissement prend en charge le mappage basé sur des règles, ce qui vous permet de créer des transformations dynamiques et flexibles qui aplatiront les tableaux en fonction des règles, et aplatiront les structures en fonction des niveaux de la hiérarchie.

:::image type="content" source="media/data-flow/flatten-pattern.png" alt-text="Modèle d’aplatissement":::

### <a name="matching-condition"></a>Condition de correspondance

Entrez une condition de correspondance de modèle pour la ou les colonnes que vous souhaitez aplatir en fonction de correspondances exactes ou de modèles. Exemple : ```like(name,'cust%')```

### <a name="deep-column-traversal"></a>Parcours de colonne en profondeur

Paramètre facultatif qui indique au service de gérer toutes les sous-colonnes d’un objet complexe individuellement au lieu de gérer l’objet complexe comme une colonne entière.

### <a name="hierarchy-level"></a>Niveau de hiérarchie

Choisissez le niveau de la hiérarchie que vous souhaitez développer.

### <a name="name-matches-regex"></a>Correspondances de nom (regex)

Si vous le souhaitez, choisissez d’exprimer votre correspondance de nom sous forme d’expression régulière dans cette zone, au lieu d’utiliser la condition de correspondance ci-dessus.

## <a name="examples"></a>Exemples

Reportez-vous à l’objet JSON suivant pour les exemples ci-dessous de transformation d’aplatissement.

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Aucune racine de déroulement avec tableau de chaînes

| Dérouler par | Racine du déroulement | Projection |
| --------- | ----------- | ---------- |
| goods.customers | None | name <br> customer = goods.customer |

#### <a name="output"></a>Sortie

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Aucune racine de déroulement avec tableau complexe

| Dérouler par | Racine du déroulement | Projection |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItems | None | name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> location = location |

#### <a name="output"></a>Sortie

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Même racine que le tableau de déroulement

| Dérouler par | Racine du déroulement | Projection |
| --------- | ----------- | ---------- |
| goods.orders | goods.orders | name <br> goods.orders.shipped.orderItems.itemName <br> goods.customers <br> location |

#### <a name="output"></a>Sortie

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Racine de déroulement avec tableau complexe

| Dérouler par | Racine du déroulement | Projection |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | goods.orders |name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> location = location |

#### <a name="output"></a>Sortie

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Exemple

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Étapes suivantes

* Utilisez la [transformation Tableau croisé dynamique](data-flow-pivot.md) pour convertir des lignes en colonnes.
* Utilisez la [transformation de suppression de tableau croisé dynamique](data-flow-unpivot.md) pour convertir des colonnes en lignes.
