---
title: Configurer des conditions de correspondance d’ensemble de règles Azure Front Door Standard/Premium
description: Cet article fournit la liste des différentes conditions de correspondance disponibles avec un ensemble de règles d’Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: 0765cfdc99024fbac0b117dfc4ae6bad6f04b292
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453551"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Conditions de correspondance d’un ensemble de règles Azure Front Door Standard/Premium

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Dans un [ensemble de règles](concept-rule-set.md) Azure Front Door Standard/Premium, une règle se compose de zéro, une ou plusieurs conditions de correspondance, et d’une action. Cet article fournit des descriptions détaillées des conditions de correspondance que vous pouvez utiliser dans un ensemble de règles Azure Front Door Standard/Premium.

La première partie d’une règle est une condition de correspondance ou un ensemble de conditions de correspondance. Une règle peut comporter jusqu’à 10 conditions de correspondance. Une condition de correspondance identifie des types spécifiques de requêtes pour lesquelles des actions définies sont effectuées. Si vous utilisez plusieurs conditions de correspondance, elles seront regroupées à l’aide de la logique AND. Pour toutes les conditions de correspondance qui prennent en charge plusieurs valeurs, un OR logique est utilisé.

Vous pouvez utiliser une condition de correspondance pour les opérations suivantes :

* Filtrer les requêtes en fonction d’une adresse IP, d’un pays ou d’une région spécifique.
* Filtrer les requêtes d’après les informations d’en-tête.
* Filtrer les requêtes provenant d’appareils mobiles ou de bureau.
* Filtrer les demandes à partir du nom de fichier de la demande et de l’extension de fichier.
* Filtrer les demandes provenant de l’URL de requête, du protocole, du chemin d’accès, de la chaîne de requête, des arguments de publication, etc.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="device-type"></a><a name="IsDevice"></a> Type d’appareil

Utilisez la condition de correspondance **device type** (type d’appareil) pour identifier les demandes effectuées à partir d’un appareil mobile ou d’un appareil de bureau.  

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-------|------------------|
| Opérateur | <ul><li>Dans le portail Azure : `Equal`, `Not Equal`</li><li>Dans les modèles ARM : `Equal` ; utilisez la propriété `negateCondition` pour spécifier _Not Equal_ (Non égal) |
| Valeur | `Mobile`, `Desktop` |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes détectées comme provenant d’un appareil mobile.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="Capture d’écran du portail montrant la condition de correspondance device type.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Arguments post

Utilisez la condition de correspondance **post args** pour identifier les demandes sur la base des arguments fournis dans le corps d’une demande POST. Une condition de correspondance unique correspond à un argument unique du corps de la demande POST. Vous pouvez spécifier plusieurs valeurs à mettre en correspondance, qui seront combinées à l’aide d’un OR logique.

> [!NOTE]
> La condition de correspondance **post args** fonctionne avec le type de contenu `application/x-www-form-urlencoded`.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Post args | Valeur de chaîne représentant le nom de l’argument POST. |
| Opérateur | Tout opérateur de la [liste d’opérateurs standard](#operator-list). |
| Valeur | Une ou plusieurs valeurs de chaîne ou d’entier représentant la valeur de l’argument POST à mettre en correspondance. Si plusieurs valeurs sont spécifiées, elles sont évaluées à l’aide d’un OR logique. |
| Transformation de cas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes POST où un argument `customerName` est fourni dans le corps de la demande, et où la valeur de `customerName` commence par la lettre `J` ou `K`. Nous utilisons une transformation de casse pour convertir les valeurs d’entrée en majuscules afin que les valeurs commençant par `J`, `j`, `K` et `k` soient toutes mises en correspondance.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="Capture d’écran du portail montrant la condition de correspondance post args.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Chaîne de requête

Utilisez la condition de correspondance **query string** pour identifier les demandes contenant une chaîne de requête spécifique. Vous pouvez spécifier plusieurs valeurs à mettre en correspondance, qui seront combinées à l’aide d’un OR logique.

> [!NOTE]
> La chaîne de requête entière est mise en correspondance en tant que chaîne, sans le signe `?` initial.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Opérateur | Tout opérateur de la [liste d’opérateurs standard](#operator-list). |
| Chaîne de requête | Une ou plusieurs valeurs de chaîne ou d’entier représentant la valeur de la chaîne de requête à mettre en correspondance. N’incluez pas le signe `?` au début de la chaîne de requête. Si plusieurs valeurs sont spécifiées, elles sont évaluées à l’aide d’un OR logique. |
| Transformation de cas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes où la chaîne de requête contient la chaîne `language=en-US`. Comme nous voulons que la condition de correspondance respecte la casse, nous ne transformons pas celle-ci.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="Capture d’écran du portail montrant la condition de correspondance query string.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Adresse distante

La condition de correspondance **remote address** identifie les demandes sur la base de l’adresse IP ou l’emplacement du demandeur. Vous pouvez spécifier plusieurs valeurs à mettre en correspondance, qui seront combinées à l’aide d’un OR logique.

* Utilisez la notation CIDR lorsque vous spécifiez des blocs d’adresses IP. Cela signifie que la syntaxe d’un bloc d’adresses IP est l’adresse IP de base suivie d’une barre oblique et de la taille de préfixe. Exemple :
    * **Exemple IPv4** : `5.5.5.64/26` correspond aux requêtes provenant des adresses 5.5.5.64 à 5.5.5.127.
    * **Exemple IPv6** : `1:2:3:/48` correspond aux requêtes qui proviennent des adresses 1:2:3:0:0:0:0:0 à 1:2:3: ffff:ffff:ffff:ffff:ffff.
* Quand vous spécifiez plusieurs adresses IP et blocs d’adresses IP, un « OR » logique est appliqué.
    * **Exemple IPv4** : si vous ajoutez deux adresses IP, `1.2.3.4` et `10.20.30.40`, la condition est remplie pour les demandes provenant de l’adresse 1.2.3.4 ou 10.20.30.40.
    * **Exemple IPv6** : si vous ajoutez deux adresses IP, `1:2:3:4:5:6:7:8` et `10:20:30:40:50:60:70:80`, la condition est remplie pour les demandes provenant de l’adresse 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
* L’adresse distante représente l’adresse IP d’origine du client, c’est-à-dire soit à partir de la connexion réseau, soit de l’en-tête de requête X-Forwarded-For si l’utilisateur se trouve derrière un proxy.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Opérateur | <ul><li>Dans le portail Azure : `Geo Match`, `Geo Not Match`, `IP Match` ou `IP Not Match`</li><li>Dans les modèles ARM : `GeoMatch`, `IPMatch` ; utilisez la propriété `negateCondition` pour spécifier _Geo Not Match_ (non-correspondance de zone géographique) ou _IP Not Match_ (non-correspondance d’adresse IP)</li></ul> |
| Valeur | <ul><li>Pour les opérateurs `IP Match` ou `IP Not Match` : spécifiez une ou plusieurs plages d’adresses IP. Si plusieurs plages d’adresses IP sont spécifiées, elles sont évaluées à l’aide d’un OR logique.</li><li>Pour les opérateurs `Geo Match` ou `Geo Not Match` : spécifiez un ou plusieurs emplacements à l’aide de leur indicatif de pays.</li></ul> |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes ne provenant pas des États-Unis.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="Capture d’écran du portail montrant la condition de correspondance remote address.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> Corps de la demande

La condition de correspondance **request body** identifie les demandes sur la base du texte apparaissant dans le corps de celles-ci. Vous pouvez spécifier plusieurs valeurs à mettre en correspondance, qui seront combinées à l’aide d’un OR logique.

> [!NOTE]
> Si le corps d’une demande dépasse 64 Ko, seuls les 64 premiers Ko sont pris en compte pour la condition de correspondance **request body**.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Opérateur | Tout opérateur de la [liste d’opérateurs standard](#operator-list). |
| Valeur | Une ou plusieurs valeurs de chaîne ou d’entier représentant la valeur du texte du corps de la demande à mettre en correspondance. Si plusieurs valeurs sont spécifiées, elles sont évaluées à l’aide d’un OR logique. |
| Transformation de cas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes dont le corps contient la chaîne `ERROR`. Nous transformons le corps de la demande en majuscules avant d’évaluer la correspondance, de sorte que `error` et d’autres variations de casse déclenchent également cette condition de correspondance.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="Capture d’écran du portail montrant la condition de correspondance request body.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> Nom de fichier de la demande

La condition de correspondance **request file name** identifie les demandes qui incluent le nom de fichier spécifié dans l’URL de la demande. Vous pouvez spécifier plusieurs valeurs à mettre en correspondance, qui seront combinées à l’aide d’un OR logique.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Opérateur | Tout opérateur de la [liste d’opérateurs standard](#operator-list). |
| Valeur | Une ou plusieurs valeurs de chaîne ou d’entier représentant la valeur du nom de fichier de demande à mettre en correspondance. Si plusieurs valeurs sont spécifiées, elles sont évaluées à l’aide d’un OR logique. |
| Transformation de cas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes où le nom de fichier de la demande est `media.mp4`. Nous transformons le nom de fichier en minuscules avant d’évaluer la correspondance, de sorte que `MEDIA.MP4` et d’autres variations de casse déclenchent également cette condition de correspondance.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="Capture d’écran du portail montrant la condition de correspondance request file name.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> Extension de fichier de demande

La condition de correspondance **request file extension** identifie les demandes qui incluent l’extension de fichier spécifiée dans le nom de fichier dans l’URL de la demande. Vous pouvez spécifier plusieurs valeurs à mettre en correspondance, qui seront combinées à l’aide d’un OR logique.

> [!NOTE]
> N’incluez pas de point initial. Par exemple, utilisez `html` au lieu de `.html`.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Opérateur | Tout opérateur de la [liste d’opérateurs standard](#operator-list). |
| Valeur | Une ou plusieurs valeurs de chaîne ou d’entier représentant la valeur de l’extension de fichier de la demande à mettre en correspondance. N’incluez pas de point initial. Si plusieurs valeurs sont spécifiées, elles sont évaluées à l’aide d’un OR logique. |
| Transformation de cas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes où l’extension de fichier de la demande est `pdf` ou `docx`. Nous transformons l’extension de fichier de la demande en minuscules avant d’évaluer la correspondance, de sorte que `PDF`, `DocX`, et d’autres variations de casse déclenchent également cette condition de correspondance.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="Capture d’écran du portail montrant la condition de correspondance request file extension.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> En-tête de la demande

La condition de correspondance **request header** identifie les demandes qui incluent un en-tête spécifique. Vous pouvez utiliser cette condition de correspondance pour vérifier si un en-tête existe, quelle que soit sa valeur, ou pour vérifier si l’en-tête correspond à une valeur spécifiée. Vous pouvez spécifier plusieurs valeurs à mettre en correspondance, qui seront combinées à l’aide d’un OR logique.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Nom de l’en-tête | Valeur de chaîne représentant le nom de l’argument POST. |
| Opérateur | Tout opérateur de la [liste d’opérateurs standard](#operator-list). |
| Valeur | Une ou plusieurs valeurs de chaîne ou d’entier représentant la valeur de l’en-tête de la demande à mettre en correspondance. Si plusieurs valeurs sont spécifiées, elles sont évaluées à l’aide d’un OR logique. |
| Transformation de cas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes contenant un en-tête nommé `MyCustomHeader`, quelle que soit sa valeur.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="Capture d’écran du portail montrant la condition de correspondance request header.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Méthode de demande

La condition de correspondance **request method** identifie les demandes qui utilisent la méthode de demande HTTP spécifiée. Vous pouvez spécifier plusieurs valeurs à mettre en correspondance, qui seront combinées à l’aide d’un OR logique.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Opérateur | <ul><li>Dans le portail Azure : `Equal`, `Not Equal`</li><li>Dans les modèles ARM : `Equal` ; utilisez la propriété `negateCondition` pour spécifier _Not Equal_ (Non égal) |
| Méthode de requête | Une ou plusieurs méthodes HTTP parmi les suivantes : `GET` , `POST` , `PUT` , `DELETE` , `HEAD` , `OPTIONS` , `TRACE`. Si plusieurs valeurs sont spécifiées, elles sont évaluées à l’aide d’un OR logique. |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes utilisant la méthode `DELETE`.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="Capture d’écran du portail montrant la condition de correspondance request method.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> Chemin de la demande

La condition de correspondance **request path** identifie les demandes qui incluent le chemin spécifié dans l’URL de la demande. Vous pouvez spécifier plusieurs valeurs à mettre en correspondance, qui seront combinées à l’aide d’un OR logique.

> [!NOTE]
> Le chemin est la partie de l’URL située derrière le nom d’hôte et une barre oblique. Par exemple, dans l’URL `https://www.contoso.com/files/secure/file1.pdf`, le chemin est `files/secure/file1.pdf`.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Opérateur | Tout opérateur de la [liste d’opérateurs standard](#operator-list). |
| Valeur | Une ou plusieurs valeurs de chaîne ou d’entier représentant la valeur du chemin de la demande à mettre en correspondance. N’incluez pas la barre oblique initiale. Si plusieurs valeurs sont spécifiées, elles sont évaluées à l’aide d’un OR logique. |
| Transformation de cas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes où le chemin du fichier de demande commence par `files/secure/`. Nous transformons l’extension de fichier de la demande en minuscules avant d’évaluer la correspondance, de sorte que les demandes adressées à `files/SECURE/`, et d’autres variations de casse, déclenchent également cette condition de correspondance.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="Capture d’écran du portail montrant la condition de correspondance request path.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> Protocole de la demande

La condition de correspondance **request protocol** identifie les demandes qui utilisent le protocole spécifié (HTTP ou HTTPS).

> [!NOTE]
> Le *protocole* est parfois également appelé *schéma*.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Opérateur | <ul><li>Dans le portail Azure : `Equal`, `Not Equal`</li><li>Dans les modèles ARM : `Equal` ; utilisez la propriété `negateCondition` pour spécifier _Not Equal_ (Non égal) |
| Méthode de requête | `HTTP`, `HTTPS` |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes utilisant le protocole `HTTP`.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="Capture d’écran du portail montrant la condition de correspondance request protocol.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> URL de demande

Identifie les requêtes qui correspondent à l’URL spécifiée. L’URL entière est évaluée, y compris le protocole et la chaîne de requête, mais pas le fragment. Vous pouvez spécifier plusieurs valeurs à mettre en correspondance, qui seront combinées à l’aide d’un OR logique.

> [!TIP]
> Lorsque vous utilisez cette condition de règle, veillez à inclure le protocole. Par exemple, utilisez `https://www.contoso.com` au lieu de `www.contoso.com`.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-|-|
| Opérateur | Tout opérateur de la [liste d’opérateurs standard](#operator-list). |
| Valeur | Une ou plusieurs valeurs de chaîne ou d’entier représentant la valeur de l’URL de la demande à mettre en correspondance. Si plusieurs valeurs sont spécifiées, elles sont évaluées à l’aide d’un OR logique. |
| Transformation de cas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemple

Dans cet exemple, nous mettons en correspondance toutes les demandes où l’URL de la demande commence par `https://api.contoso.com/customers/123`. Nous transformons l’extension de fichier de la demande en minuscules avant d’évaluer la correspondance, de sorte que les demandes adressées à `https://api.contoso.com/Customers/123`, et d’autres variations de casse, déclenchent également cette condition de correspondance.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="Capture d’écran du portail montrant la condition de correspondance request URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> Liste des opérateurs

Pour les règles qui acceptent les valeurs de la liste des opérateurs standard, les opérateurs suivants sont valides :

| Opérateur                   | Description                                                                                                                    | Prise en charge de modèle ARM                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Quelconque                        | Correspond quand il y a une valeur, quelle qu’elle soit.                                                                     | `operator`: `Any`                                               |
| Égal à                      | Correspond quand la valeur correspond exactement à la chaîne spécifiée.                                                                   | `operator`: `Equal`                                             |
| Contient                   | Correspond quand la valeur contient la chaîne spécifiée.                                                                          | `operator`: `Contains`                                          |
| Inférieur à                  | Correspond quand la longueur de la valeur est inférieure à l’entier spécifié.                                                       | `operator`: `LessThan`                                          |
| Supérieur à               | Correspond quand la longueur de la valeur est supérieure à l’entier spécifié.                                                    | `operator`: `GreaterThan`                                       |
| Inférieur ou égal à         | Correspond quand la longueur de la valeur est inférieure ou égale à l’entier spécifié.                                           | `operator`: `LessThanOrEqual`                                   |
| Supérieur ou égal à      | Correspond quand la longueur de la valeur est supérieure ou égale à l’entier spécifié.                                        | `operator`: `GreaterThanOrEqual`                                |
| Commence par                | Correspond quand la valeur commence par la chaîne spécifiée.                                                                       | `operator`: `BeginsWith`                                        |
| Se termine par                  | Correspond quand la valeur se termine par la chaîne spécifiée.                                                                         | `operator`: `EndsWith`                                          |
| Expression régulière                      | Correspond quand la valeur correspond à l’expression régulière spécifiée. [Voyez ci-dessous pour plus de détails.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Aucun                    | Correspond quand il n’y a aucune valeur.                                                                                                | `operator` : `Any` et `negateCondition` : `true`                |
| Not Equal                  | Correspond quand la valeur ne correspond pas à la chaîne spécifiée.                                                                    | `operator` : `Equal` et `negateCondition` : `true`              |
| Ne contient pas               | Correspond quand la valeur ne contient pas la chaîne spécifiée.                                                                  | `operator` : `Contains` et `negateCondition` : `true`           |
| Pas inférieur à              | Correspond quand la longueur de la valeur n’est pas inférieure à l’entier spécifié.                                                   | `operator` : `LessThan` et `negateCondition` : `true`           |
| Pas supérieur à           | Correspond quand la longueur de la valeur n’est pas supérieure à l’entier spécifié.                                                | `operator` : `GreaterThan` et `negateCondition` : `true`        |
| Pas inférieur ou égal à     | Correspond quand la longueur de la valeur n’est pas inférieure ou égale à l’entier spécifié.                                       | `operator` : `LessThanOrEqual` et `negateCondition` : `true`    |
| Pas supérieur ou égal à | Correspond quand la longueur de la valeur n’est pas supérieure ou égale à l’entier spécifié.                                    | `operator` : `GreaterThanOrEqual` et `negateCondition` : `true` |
| Ne commence pas par            | Correspond quand la valeur ne commence pas par la chaîne spécifiée.                                                               | `operator` : `BeginsWith` et `negateCondition` : `true`         |
| Ne se termine pas par              | Correspond quand la valeur ne se termine pas par la chaîne spécifiée.                                                                 | `operator` : `EndsWith` et `negateCondition` : `true`           |
| Pas expression régulière                  | Correspond quand la valeur ne correspond pas à l’expression régulière spécifiée. [Voyez ci-dessous pour plus de détails.](#regular-expressions) | `operator` : `RegEx` et `negateCondition` : `true`              |

> [!TIP]
> Pour les opérateurs numériques tels que *Inférieur à* et *Supérieur ou égal à*, la comparaison utilisée est basée sur la longueur. La valeur de la condition de correspondance doit être un entier spécifiant la longueur que vous voulez comparer.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> Expressions régulières

Les expressions régulières ne prennent pas en charge les opérations suivantes :

* Références arrière et capture de sous-expressions.
* Assertions arbitraires de largeur nulle.
* Références de sous-routines et modèles récursifs.
* Modèles conditionnels.
* Verbes de contrôle de retours sur trace.
* La directive `\C` mono-octet.
* La directive de correspondance de saut de ligne `\R`.
* La directive de début de correspondance de réinitialisation `\K`.
* Légendes et code incorporé.
* Regroupement atomique et quantificateurs de possession.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [ensembles de règles](concept-rule-set.md).
* Découvrez comment [configurer votre premier ensemble de règles](how-to-configure-rule-set.md).
* En savoir plus sur les [actions d’un ensemble de règles](concept-rule-set-actions.md).
