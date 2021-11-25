---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 965a2abb8a56894efc700cea75097526daf4e544
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132529923"
---
## <a name="connections"></a>Connexions

La propriété `connection` est une référence à la configuration de l’environnement qui spécifie la façon dont l’application doit se connecter aux files d’attente Azure. Elle peut spécifier :

- Le nom d’un paramètre d’application contenant une [chaîne de connexion](#connection-string)
- Le nom d’un préfixe partagé pour plusieurs paramètres d’application, définissant ensemble une [connexion basée sur l’identité](#identity-based-connections).

Si la valeur configurée est à la fois une correspondance exacte pour un paramètre unique et une correspondance de préfixe pour d’autres paramètres, la correspondance exacte est utilisée.

### <a name="connection-string"></a>Chaîne de connexion

Pour obtenir une chaîne de connexion, suivez les étapes indiquées dans [Gérer les clés d’accès au compte de stockage](../articles/storage/common/storage-account-keys-manage.md).

Cette chaîne de connexion doit être stockée dans un paramètre d’application dont le nom correspond à la valeur spécifiée par la propriété `connection` de la configuration de liaison.

Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.

### <a name="identity-based-connections"></a>Connexions basées sur l’identité

Si vous utilisez [la version 5.x ou une version ultérieure de l’extension](../articles/azure-functions/functions-bindings-storage-queue.md#storage-extension-5x-and-higher), au lieu d’utiliser une chaîne de connexion avec un secret, vous pouvez faire en sorte que l’application utilise une [identité Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md). Pour ce faire, vous devez définir les paramètres sous un préfixe commun qui correspond à la propriété `connection` dans le déclencheur et la configuration de liaison.

Dans ce mode, l’extension nécessite les propriétés suivantes :

| Propriété                  | Modèle de variable d’environnement                       |Description                                | Valeur d'exemple |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| URI du service de file d’attente | `<CONNECTION_NAME_PREFIX>__queueServiceUri`<sup>1</sup>  | URI du plan de données du service de file d’attente auquel vous vous connectez, à l’aide du même schéma HTTPS. | https://<storage_account_name>.queue.core.windows.net |

<sup>1</sup> `<CONNECTION_NAME_PREFIX>__serviceUri` peut être utilisé comme alias. Si les deux formes sont fournies, la forme `queueServiceUri` est utilisée. La forme `serviceUri` ne peut pas être utilisée lorsque la configuration globale de la connexion doit être utilisée sur des objets blob, des files d’attente et/ou des tables.

Des propriétés supplémentaires peuvent être définies pour personnaliser la connexion. Consultez [Propriétés communes pour les connexions basées sur l’identité](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-queue-permissions](./functions-queue-permissions.md)]