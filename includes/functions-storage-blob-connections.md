---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 5fc17f7a4a5220ebcfb05b179c3ee75ba077e5a4
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132529845"
---
## <a name="connections"></a>Connexions

La propriété `connection` est une référence à la configuration de l’environnement qui spécifie la façon dont l’application doit se connecter à Azure Blobs. Elle peut spécifier :

- Le nom d’un paramètre d’application contenant une [chaîne de connexion](#connection-string)
- Le nom d’un préfixe partagé pour plusieurs paramètres d’application, définissant ensemble une [connexion basée sur l’identité](#identity-based-connections).

Si la valeur configurée est à la fois une correspondance exacte pour un paramètre unique et une correspondance de préfixe pour d’autres paramètres, la correspondance exacte est utilisée.

### <a name="connection-string"></a>Chaîne de connexion

Pour obtenir une chaîne de connexion, suivez les étapes indiquées à la section [Gérer les clés d’accès au compte de stockage](../articles/storage/common/storage-account-keys-manage.md). La chaîne de connexion doit être destinée à un compte de stockage à usage général, et non pas à un [compte Stockage Blob](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

Cette chaîne de connexion doit être stockée dans un paramètre d’application dont le nom correspond à la valeur spécifiée par la propriété `connection` de la configuration de liaison.

Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.

### <a name="identity-based-connections"></a>Connexions basées sur l’identité

Si vous utilisez [la version 5.x ou une version ultérieure de l’extension](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher), au lieu d’utiliser une chaîne de connexion avec un secret, vous pouvez faire en sorte que l’application utilise une [identité Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md). Pour ce faire, vous devez définir les paramètres sous un préfixe commun qui correspond à la propriété `connection` dans le déclencheur et la configuration de liaison.

Dans ce mode, l’extension nécessite les propriétés suivantes :

| Propriété                  | Modèle de variable d’environnement                       | Description                                | Valeur d'exemple |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| URI du service blob | `<CONNECTION_NAME_PREFIX>__blobServiceUri`<sup>1</sup>  | URI du plan de données du service blob auquel vous vous connectez, à l’aide du même schéma HTTPS. | https://<storage_account_name>.blob.core.windows.net |

<sup>1</sup> `<CONNECTION_NAME_PREFIX>__serviceUri` peut être utilisé en tant qu’alias. Si les deux alias sont fournis, le formulaire `blobServiceUri` est utilisé. Le formulaire `serviceUri` ne peut pas être utilisé lorsque la configuration globale de la connexion doit être utilisée sur des objets blob, des files d’attente et/ou des tables.

> [!NOTE]
> Par défaut, le déclencheur d’objet blob utilise les files d’attente Azure en interne. `<CONNECTION_NAME_PREFIX>__queueServiceUri` peut également être spécifié, mais le comportement par défaut n’est pas d’utiliser la connexion définie par « AzureWebJobsStorage ». Le déclencheur a besoin de [Contributeur aux données en file d’attente du stockage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor) sur la connexion à utiliser pour ces files d’attente.

Des propriétés supplémentaires peuvent être définies pour personnaliser la connexion. Consultez [Propriétés communes pour les connexions basées sur l’identité](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-blob-permissions](./functions-blob-permissions.md)]