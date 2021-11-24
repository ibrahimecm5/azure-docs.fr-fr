---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 11/12/2021
ms.author: mahender
ms.openlocfilehash: 4a78d64e2a66f5d3025407b6d7d7907d085b1fe6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132530275"
---
## <a name="connections"></a>Connexions

Les propriétés `connectionStringSetting`/`connection` et `leaseConnectionStringSetting`/`leaseConnection` sont des références à la configuration de l’environnement qui spécifie la façon dont l’application doit se connecter à Azure Cosmos DB. Elles peuvent spécifier :

- Le nom d’un paramètre d’application contenant une [chaîne de connexion](#connection-string)
- Le nom d’un préfixe partagé pour plusieurs paramètres d’application, définissant ensemble une [connexion basée sur l’identité](#identity-based-connections). Cette option n’est disponible que pour les versions `connection` et `leaseConnection` de la [version 4.x ou ultérieure de l’extension].

Si la valeur configurée est à la fois une correspondance exacte pour un paramètre unique et une correspondance de préfixe pour d’autres paramètres, la correspondance exacte est utilisée.

### <a name="connection-string"></a>Chaîne de connexion

La chaîne de connexion de votre compte de base de données doit être stockée dans un paramètre d’application dont le nom correspond à la valeur spécifiée par la propriété de connexion de la configuration de la liaison.

### <a name="identity-based-connections"></a>Connexions basées sur l’identité

Si vous utilisez la [version 4.x ou ultérieure de l’extension], au lieu d’utiliser une chaîne de connexion avec un secret, vous pouvez faire en sorte que l’application utilise une [identité Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md). Pour ce faire, vous devez définir les paramètres sous un préfixe commun qui correspond à la propriété de connexion dans la configuration des déclencheurs et des liaisons.

Dans ce mode, l’extension nécessite les propriétés suivantes :

| Propriété                  | Modèle de variable d’environnement                       | Description                                | Valeur d'exemple                                        |
|---------------------------|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| Point de terminaison de compte | `<CONNECTION_NAME_PREFIX>__accountEndpoint` | URI du point de terminaison du compte Azure Cosmos DB. | https://<database_account_name>.documents.azure.com:443/ |

Des propriétés supplémentaires peuvent être définies pour personnaliser la connexion. Consultez [Propriétés communes pour les connexions basées sur l’identité](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-cosmos-permissions](./functions-cosmos-permissions.md)]

[version 4.x ou ultérieure de l’extension]: ../articles/azure-functions/functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher
