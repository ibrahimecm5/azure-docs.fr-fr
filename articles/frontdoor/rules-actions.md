---
title: Configurer les actions des règles Azure Front Door
description: Cet article fournit la liste des différentes actions que vous pouvez effectuer avec des règles Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: yuajia
ms.openlocfilehash: c072cf341049e5e2fc8631fdb628f04bdf719b97
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350540"
---
# <a name="azure-front-door-rules-actions"></a>Actions des règles Azure Front Door

Le [moteur de règles](front-door-rules-engine.md) Azure Front Door et l’[ensemble de règles](standard-premium/concept-rule-set.md) Azure Front Door Standard/Premium sont constitués de règles avec une combinaison de conditions de correspondance et d’actions. Cet article fournit une description détaillée des types d’actions que vous pouvez utiliser dans le moteur de règles et l’ensemble de règles Azure Front Door. L’action définit le comportement appliqué à un type de requête identifié par une ou plusieurs conditions de correspondance. Une règle peut contenir jusqu’à cinq actions.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les actions suivantes sont prêtes à être utilisées dans un ensemble de règles Azure Front Door.  

## <a name="cache-expiration"></a>Expiration du cache

Utilisez l’action **expiration du cache** pour remplacer la valeur de durée de vie (TTL) du point de terminaison pour les requêtes spécifiées par les conditions de correspondance des règles.

> [!NOTE]
> Les origines peuvent spécifier de ne pas mettre en cache des réponses spécifiques à l’aide de l'en-tête `Cache-Control` avec la valeur `no-cache`, `private` ou `no-store`. Dans ces circonstances, Front Door ne met jamais en cache le contenu et cette action n’a aucun effet.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-------|------------------|
| Comportement du cache | <ul><li>**Ignorer le cache :** Le contenu ne doit pas être mis en cache. Dans les modèles ARM, définissez la propriété `cacheBehavior` sur `BypassCache`.</li><li>**Remplacer :** La valeur TTL retournée par votre origine est remplacée par la valeur spécifiée dans l’action. Ce comportement est appliqué uniquement si la réponse est mise en cache. Dans les modèles ARM, définissez la propriété `cacheBehavior` sur `Override`.</li><li>**Définir si manquante :** Si aucune valeur TTL n’est retournée à partir de votre origine, la règle la définit sur la valeur spécifiée dans l’action. Ce comportement est appliqué uniquement si la réponse est mise en cache. Dans les modèles ARM, définissez la propriété `cacheBehavior` sur `SetIfMissing`.</li></ul> |
| Durée du cache | Lorsque le _Comportement du cache_ est défini sur `Override` ou `Set if missing`, ces champs doivent spécifier la durée du cache à utiliser. La durée maximale est de 366 jours.<ul><li>Dans le Portail Azure : spécifiez les jours, les heures, les minutes et les secondes.</li><li>Dans les modèles ARM : spécifiez la durée au format `d.hh:mm:ss`. |

### <a name="example"></a>Exemple

Dans cet exemple, nous remplaçons l’expiration du cache à 6 heures, pour les requêtes correspondantes qui ne spécifient pas déjà une durée de cache.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="./media/rules-actions/cache-expiration.png" alt-text="Capture d’écran du portail montrant l’action d’expiration du cache.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a>Chaîne de requête de clé de cache

Utilisez l’action **chaîne de requête de clé de cache** pour modifier la clé de cache en fonction des chaînes de requête. La clé de cache est la manière dont Front Door identifie les demandes uniques à mettre en cache.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-------|------------------|
| Comportement | <ul><li>**Inclure :** Les chaînes de requête spécifiées dans les paramètres sont incluses quand la clé de cache est générée. Dans les modèles ARM, définissez la propriété `queryStringBehavior` sur `Include`.</li><li>**Mettre en cache chaque URL unique :** Chaque URL unique possède sa propre clé de cache. Dans les modèles ARM, utilisez `queryStringBehavior` de `IncludeAll`.</li><li>**Exclure :** Les chaînes de requête spécifiées dans les paramètres sont exclues quand la clé de cache est générée. Dans les modèles ARM, définissez la propriété `queryStringBehavior` sur `Exclude`.</li><li>**Ignorer les chaînes de requête :** Les chaînes de requête ne sont pas prises en compte quand la clé de cache est générée. Dans les modèles ARM, définissez la propriété `queryStringBehavior` sur `ExcludeAll`.</li></ul>  |
| Paramètres | Liste des noms de paramètre de chaîne de requête, séparés par des virgules. |

### <a name="example"></a>Exemple

Dans cet exemple, nous modifions la clé de cache pour inclure un paramètre de chaîne de requête nommé `customerId`.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="./media/rules-actions/cache-key-query-string.png" alt-text="Capture d’écran du portail montrant l’action de la chaîne de requête de clé de cache.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a>Modifier l’en-tête de requête

Utilisez l’action **modifier l’en-tête de requête** pour modifier les en-têtes de la requête lorsqu’elle est envoyée à votre origine.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-------|------------------|
| Opérateur | <ul><li>**Ajouter :** L’en-tête spécifié dans est ajouté à la requête avec la valeur spécifiée. Si l’en-tête est déjà présent, la valeur est ajoutée à la valeur d’en-tête existante à l’aide de la concaténation de chaînes. Aucun délimiteur n’est ajouté. Dans les modèles ARM, utilisez `headerAction` de `Append`.</li><li>**Remplacer :** L’en-tête spécifié est ajouté à la requête avec la valeur spécifiée. Si l’en-tête est déjà présent, la valeur spécifiée remplace la valeur existante. Dans les modèles ARM, utilisez `headerAction` de `Overwrite`.</li><li>**Supprimer :** Si l’en-tête spécifié dans la règle est présent, l’en-tête est supprimé de la requête. Dans les modèles ARM, utilisez `headerAction` de `Delete`.</li></ul> |
| Nom de l’en-tête | Nom de l'en-tête à modifier. |
| Valeur de l’en-tête | Valeur à ajouter ou à remplacer. |

### <a name="example"></a>Exemple

Dans cet exemple, nous ajoutons la valeur `AdditionalValue` à l'en-tête de requête `MyRequestHeader`. Si l’origine affecte à l’en-tête de réponse la valeur `ValueSetByClient`, une fois cette action appliquée, l’en-tête de requête a la valeur `ValueSetByClientAdditionalValue`.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="./media/rules-actions/modify-request-header.png" alt-text="Capture d’écran du portail montrant l’action modifier l’en-tête de requête.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a>Modifier l’en-tête de réponse

Utilisez l’action **modifier l’en-tête de réponse** pour modifier les en-têtes présents dans les réponses avant qu’ils ne soient retournés à vos clients.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|-------|------------------|
| Opérateur | <ul><li>**Ajouter :** L’en-tête spécifié est ajouté à la réponse avec la valeur spécifiée. Si l’en-tête est déjà présent, la valeur est ajoutée à la valeur d’en-tête existante à l’aide de la concaténation de chaînes. Aucun délimiteur n’est ajouté. Dans les modèles ARM, utilisez `headerAction` de `Append`.</li><li>**Remplacer :** L’en-tête spécifié est ajouté à la réponse avec la valeur spécifiée. Si l’en-tête est déjà présent, la valeur spécifiée remplace la valeur existante. Dans les modèles ARM, utilisez `headerAction` de `Overwrite`.</li><li>**Supprimer :** Si l’en-tête spécifié dans la règle est présent, l’en-tête est supprimé de la réponse.  Dans les modèles ARM, utilisez `headerAction` de `Delete`.</li></ul> |
| Nom de l’en-tête | Nom de l'en-tête à modifier. |
| Valeur de l’en-tête | Valeur à ajouter ou à remplacer. |

### <a name="example"></a>Exemple

Dans cet exemple, nous supprimons l’en-tête avec le nom `X-Powered-By` des réponses avant qu’elles ne soient retournées au client.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="./media/rules-actions/modify-response-header.png" alt-text="Capture d’écran du portail montrant l’action modifier l’en-tête de réponse.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a>Redirection d’URL

Utilisez l’action de **redirection d’URL** pour rediriger les clients vers une nouvelle URL. Les clients reçoivent une réponse de redirection de Front Door.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|----------|------------------|
| Type de redirection | Type de réponse à renvoyer au demandeur. <ul><li>Dans le Portail Azure : Trouvé (302), Déplacé (301), Redirection temporaire (307), Redirection permanente (308).</li><li>Dans les modèles ARM : `Found`, `Moved`, `TemporaryRedirect`, `PermanentRedirect`</li></ul> |
| Protocole de redirection | <ul><li>Dans le Portail Azure : `Match Request`, `HTTP`, `HTTPS`</li><li>Dans les modèles ARM : `MatchRequest`, `Http`, `Https`</li></ul> |
| Hôte de destination | Nom d’hôte vers lequel vous souhaitez rediriger la requête. Laissez vide pour conserver l’hôte entrant. |
| Chemin de destination | Chemin d’accès à utiliser dans la redirection. Incluez l’interligne`/`. Laissez vide pour conserver le chemin d’accès entrant. |
| Chaîne de requête | Chaîne de requête utilisée dans la redirection. N’incluez pas l’interligne `?`. Laissez vide pour conserver la chaîne de requête entrante. |
| Fragment de destination | Fragment à utiliser dans la redirection. Laissez vide pour conserver le fragment entrant. |

### <a name="example"></a>Exemple

Dans cet exemple, nous redirigeons la requête vers `https://contoso.com/exampleredirection?clientIp={client_ip}`, tout en conservant le fragment. Une redirection HTTP temporaire (307) est utilisée. L’adresse IP du client est utilisée à la place du jeton `{client_ip}` dans l’URL à l’aide de la [variable serveur](#server-variables) `client_ip`.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="./media/rules-actions/url-redirect.png" alt-text="Capture d’écran du portail montrant l’action de redirection d’URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a>Réécrire URL

Utilisez l’action **réécriture d’URL** pour réécrire le chemin d’accès d’une requête en cours d’acheminement vers votre origine.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|----------|------------------|
| Modèle source | Définissez le modèle source dans le chemin d’accès de l’URL à remplacer. Actuellement, le modèle source utilise une correspondance basée sur un préfixe. Pour correspondre à tous les chemins d’URL, utilisez une barre oblique (`/`) comme valeur de modèle source. |
| Destination | Définissez le chemin d’accès de destination à utiliser lors de la réécriture. Le chemin d’accès de destination remplace le modèle source. |
| Conserver le chemin d’accès sans correspondance | S’il est défini sur _Oui_, le chemin d’accès restant après le modèle source est ajouté au nouveau chemin d’accès de destination. |

### <a name="example"></a>Exemple

Dans cet exemple, nous réécrivons toutes les demandes dans le chemin d’accès `/redirection` et ne conservons pas le reste du chemin d’accès.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="./media/rules-actions/url-rewrite.png" alt-text="Capture d’écran du portail montrant l’action de réécriture d’URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="origin-group-override"></a> Remplacement du groupe d’origines

Utilisez l’action **Remplacement du groupe d’origines** pour modifier le groupe d’origines vers lequel la demande doit être acheminée.

### <a name="properties"></a>Propriétés

| Propriété | Valeurs prises en charge |
|----------|------------------|
| Groupe d’origines | Groupe d’origines vers lequel la demande doit être acheminée. Cela remplace la configuration spécifiée dans l’itinéraire du point de terminaison Front Door. |

### <a name="example"></a>Exemple

Dans cet exemple, nous acheminons toutes les demandes mises en correspondance vers un groupe d’origines nommé `SecondOriginGroup`, quelle que soit la configuration de l’itinéraire du point de terminaison Front Door.

# <a name="portal"></a>[Portail](#tab/portal)

:::image type="content" source="./media/rules-actions/origin-group-override.png" alt-text="Capture d’écran du portail illustrant l’action de remplacement du groupe d’origines.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "OriginGroupOverride",
  "parameters": {
    "originGroup": {
      "id": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Cdn/profiles/<profile-name>/originGroups/SecondOriginGroup"
    },
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleOriginGroupOverrideActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'OriginGroupOverride'
  parameters: {
    originGroup: {
      id: '/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Cdn/profiles/<profile-name>/originGroups/SecondOriginGroup'
    }
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleOriginGroupOverrideActionParameters'
  }
}
```

---

## <a name="server-variables"></a>Variables de serveur

Les variables de serveur de l’ensemble de règles permettent d’accéder aux informations structurées sur la requête. Vous pouvez utiliser des variables de serveur pour modifier dynamiquement les en-têtes de requête/réponse ou les chemins de réécriture d’URL/chaînes de requête, par exemple, quand une nouvelle page est chargée ou qu’un formulaire est publié.

### <a name="supported-variables"></a>Variables prises en charge

| Nom de la variable    | Description                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | Adresse IP de la connexion directe à la périphérie Azure Front Door. Si le client a utilisé un proxy HTTP ou un équilibreur de charge pour envoyer la requête, la valeur de `socket_ip` est l’adresse IP du proxy ou de l’équilibreur de charge.                                                                      |
| `client_ip`      | Adresse IP du client à l’origine de la requête. S’il existait un en-tête `X-Forwarded-For` dans la requête, l’adresse IP du client est sélectionnée dans l’en-tête.                                                                                                               |
| `client_port`    | Adresse IP du port du client qui a effectué la requête.                                                                                                                                                                                                                                          |
| `hostname`       | Nom d’hôte dans la requête du client.                                                                                                                                                                                                                                             |
| `geo_country`    | Indique le pays/la région d’origine du demandeur au travers de son préfixe international.                                                                                                                                                                                                       |
| `http_method`    | Méthode utilisée pour effectuer la requête d’URL, comme `GET` ou `POST`.                                                                                                                                                                                                                         |
| `http_version`   | Protocole de requête. Généralement `HTTP/1.0`, `HTTP/1.1` ou `HTTP/2.0`.                                                                                                                                                                                                                      |
| `query_string`   | Liste de paires variable/valeur qui suivent le « ? » dans l’URL demandée.<br />Par exemple, dans la requête `http://contoso.com:8080/article.aspx?id=123&title=fabrikam`, la valeur `query_string` sera `id=123&title=fabrikam`.                                                      |
| `request_scheme` | Schéma de la requête : `http` ou `https`.                                                                                                                                                                                                                                                    |
| `request_uri`    | URI complet de la requête d’origine (avec les arguments).<br />Par exemple, dans la requête `http://contoso.com:8080/article.aspx?id=123&title=fabrikam`, la valeur `request_uri` sera `/article.aspx?id=123&title=fabrikam`.                                                                     |
| `ssl_protocol`   | Protocole d’une connexion TLS établie.                                                                                                                                                                                                                                            |
| `server_port`    | Port du serveur qui a accepté une requête.                                                                                                                                                                                                                                           |
| `url_path`       | Identifie la ressource spécifique dans l’hôte à laquelle le client web souhaite accéder. Il s’agit de la partie de l’URI de demande sans les arguments.<br />Par exemple, dans la requête `http://contoso.com:8080/article.aspx?id=123&title=fabrikam`, la valeur `uri_path` sera `/article.aspx`. |

### <a name="server-variable-format"></a>Format de la variable serveur    

Les variables serveur peuvent être spécifiées à l’aide des formats suivants :

* `{variable}` : Inclut l’intégralité de la variable serveur. Par exemple, si l’adresse IP du client est `111.222.333.444`, le jeton `{client_ip}` prend la valeur `111.222.333.444`.
* `{variable:offset}`: Inclut la variable serveur après un décalage spécifique, jusqu’à la fin de la variable. Le décalage est basé sur zéro. Par exemple, si l’adresse IP du client est `111.222.333.444`, le jeton `{client_ip:3}` prend la valeur `.222.333.444`.
* `{variable:offset:length}`: Inclut la variable serveur après un décalage spécifique, jusqu’à la longueur spécifiée. Le décalage est basé sur zéro. Par exemple, si l’adresse IP du client est `111.222.333.444`, le jeton `{client_ip:4:3}` prend la valeur `222`.

### <a name="supported-actions"></a>Actions prises en charge

Les variables serveur sont prises en charge sur les actions suivantes :

* Chaîne de requête de clé de cache
* Modifier l’en-tête de requête
* Modifier l’en-tête de réponse
* Redirection d’URL
* Réécrire URL

## <a name="next-steps"></a>Étapes suivantes

* Découvrez-en plus sur l’[ensemble de règles Azure Front Door Standard/Premium](standard-premium/concept-rule-set.md).
* En savoir plus sur le [Moteur de règles Azure Front Door](front-door-rules-engine.md).
* Découvrez-en plus sur les [Conditions de correspondance d’un ensemble de règles](rules-match-conditions.md).
