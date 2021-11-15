---
title: Comprendre les définitions de rôles Azure - RBAC Azure
description: Apprenez-en davantage sur les définitions de rôles Azure dans le cadre du contrôle d’accès en fonction du rôle (RBAC) Azure pour une gestion affinée des accès aux ressources Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 09/28/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: e60c9f2e7cf4c2f2ed19612ff8e01260a5f8fcb7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055101"
---
# <a name="understand-azure-role-definitions"></a>Comprendre les définitions de rôles Azure

Si vous essayez de comprendre comment un rôle Azure fonctionne, ou si vous créez votre propre [rôle personnalisé Azure](custom-roles.md), il est utile de comprendre la façon dont les rôles sont définis. Cet article décrit en détail les définitions de rôles et fournit quelques exemples.

## <a name="role-definition"></a>Définition de rôle

Une *définition de rôle* est une collection d’autorisations. On l’appelle parfois simplement *rôle*. Une définition de rôle liste les actions qui peuvent être effectuées, comme lire, écrire et supprimer. Elle permet également de répertorier les actions qui sont exclues des actions autorisées ou des actions liées aux données sous-jacentes.

L’exemple suivant illustre les propriétés d’une définition de rôle lorsqu’elle est affichée à l’aide d’Azure PowerShell :

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

L’exemple suivant illustre les propriétés d’une définition de rôle lorsqu’elle est affichée à l’aide du Portail Azure, d’Azure CLI ou de l’API REST :

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

Le tableau suivant décrit ce que signifient les propriétés de rôle.

| Propriété | Description |
| --- | --- |
| `Name`</br>`roleName` | Nom d’affichage du rôle. |
| `Id`</br>`name` | ID unique du rôle. Les rôles intégrés ont le même ID de rôle dans tous les clouds. |
| `IsCustom`</br>`roleType` | Indique s’il s’agit d’un rôle personnalisé. À définir sur `true` ou `CustomRole` pour les rôles personnalisés. À définir sur `false` ou `BuiltInRole` pour les rôles intégrés. |
| `Description`</br>`description` | Description du rôle. |
| `Actions`</br>`actions` | Tableau de chaînes qui spécifie les actions du plan de contrôle que le rôle autorise. |
| `NotActions`</br>`notActions` | Tableau de chaînes qui spécifie les actions du plan de contrôle exclues des `Actions` autorisées. |
| `DataActions`</br>`dataActions` | Tableau de chaînes qui spécifie les actions du plan de données que le rôle autorise sur vos données au sein de cet objet. |
| `NotDataActions`</br>`notDataActions` | Tableau de chaînes qui spécifie les actions du plan de données exclues des `DataActions` autorisées. |
| `AssignableScopes`</br>`assignableScopes` | Tableau de chaînes qui spécifie les étendues pour lesquelles le rôle est disponible à des fins d’attribution. |

### <a name="actions-format"></a>Format des actions

Les actions sont spécifiées à l’aide de chaînes dont le format est le suivant :

- `{Company}.{ProviderName}/{resourceType}/{action}`

La portion `{action}` d’une chaîne d’action spécifie le type d’actions que vous pouvez effectuer sur un type de ressource. Par exemple, vous verrez les sous-chaînes suivantes dans `{action}` :

| Sous-chaîne d’action    | Description         |
| ------------------- | ------------------- |
| `*` | Le caractère générique donne accès à toutes les actions qui correspondent à la chaîne. |
| `read` | Permet les actions de lecture (GET). |
| `write` | Permet les actions d’écriture (PUT ou PATCH). |
| `action` | Permet des actions personnalisées, telles que le redémarrage de machines virtuelles (POST). |
| `delete` | Permet les actions de suppression (DELETE). |

### <a name="role-definition-example"></a>Exemple de définition de rôle

Voici la définition de rôle [Contributeur](built-in-roles.md#contributor) comme indiqué dans Azure PowerShell et Azure CLI. Les actions avec caractère générique (`*`) sous `Actions` indiquent que le principal affecté à ce rôle peut effectuer toutes les actions. En d’autres termes, il peut tout gérer. Cela inclut les actions qui seront définies dans le futur, à mesure qu’Azure ajoutera de nouveaux types de ressources. Les actions sous `NotActions` sont soustraites de `Actions`. Dans le cas du rôle [Contributeur](built-in-roles.md#contributor), `NotActions` supprime la possibilité pour ce rôle de gérer l’accès aux ressources et gère également les affectations Azure Blueprints.

Rôle contributeur tel qu’il s’affiche dans Azure PowerShell :

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rôle contributeur tel qu’il s’affiche dans Azure CLI :

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="control-and-data-actions"></a>Actions de contrôle et actions sur les données

Le contrôle d’accès en fonction du rôle pour les actions de plan de contrôle est spécifié dans les propriétés `Actions` et `NotActions` d’une définition de rôle. Voici quelques exemples d’actions de plan de contrôle dans Azure :

- Gérer l’accès à un compte de stockage
- Créer, mettre à jour ou supprimer un conteneur d’objets blob
- Supprimer un groupe de ressources et toutes ses ressources

L’accès au plan de contrôle n’est pas transmis à votre plan de données à condition que la méthode d’authentification du conteneur soit définie sur « Compte d’utilisateur Azure AD » et non sur « Clé d’accès ». Cette séparation empêche des rôles avec caractères génériques (`*`) d’avoir un accès illimité à vos données. Par exemple, si un utilisateur a un rôle [Lecteur](built-in-roles.md#reader) sur un abonnement, il peut afficher le compte de stockage, mais pas les données sous-jacentes, par défaut.

Auparavant, le contrôle d’accès en fonction du rôle n’était pas utilisé pour les actions sur les données. L’autorisation pour les actions sur les données variait selon les fournisseurs de ressources. Le même modèle d’autorisation du contrôle d’accès en fonction du rôle utilisé pour les actions de plan de contrôle a été étendu aux actions de plan de données.

Pour prendre en charge les actions de plan de données, de nouvelles propriétés de données ont été ajoutées à la définition de rôle. Les actions de plan de données sont spécifiées dans les propriétés `DataActions` et `NotDataActions`. En ajoutant ces propriétés de données, la séparation entre le plan de contrôle et le plan de données est conservée. Cela empêche les attributions de rôle contenant des caractères génériques (`*`) d’accéder soudainement aux données. Voici quelques actions de plan de données qui peuvent être spécifiées dans `DataActions` et `NotDataActions` :

- Lire une liste d’objets blob dans un conteneur
- Écrire un objet blob de stockage dans un conteneur
- Supprimer un message dans une file d’attente

Voici la définition de rôle [Lecteur des données blob du stockage](built-in-roles.md#storage-blob-data-reader), qui inclut des actions à la fois dans les propriétés `Actions` et `DataActions`. Ce rôle vous permet de lire le conteneur d’objets blob ainsi que les données d’objets blob sous-jacentes.

Rôle Lecteur des données blob du stockage tel qu’il est affiché dans Azure PowerShell :

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rôle Lecteur des données blob de stockage tel qu’il est affiché dans Azure CLI :

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Seules des actions de plan de données peuvent être ajoutées aux propriétés `DataActions` et `NotDataActions`. Les fournisseurs de ressources identifient quelles actions sont des actions sur les données en définissant la propriété `isDataAction` sur `true`. Pour afficher la liste des actions où `isDataAction` est `true`, consultez [Opérations de fournisseur de ressources](resource-provider-operations.md). Les rôles qui n’ont pas d’actions sur les données ne sont pas obligés d’avoir les propriétés `DataActions` et `NotDataActions` dans la définition de rôle.

L’autorisation pour tous les appels d’API de plan de contrôle est gérée par Azure Resource Manager. L’autorisation pour les appels d’API de plan de données est gérée par un fournisseur de ressources ou Azure Resource Manager.

### <a name="data-actions-example"></a>Exemple d’actions sur les données

Pour mieux comprendre comment fonctionnent les actions de plan de contrôle et de plan de données, prenons un exemple spécifique. Alice a reçu le rôle [Propriétaire](built-in-roles.md#owner) au niveau de l’étendue de l’abonnement. Bob a reçu le rôle [Contributeur aux données blob du stockage](built-in-roles.md#storage-blob-data-contributor) dans une étendue de compte de stockage. Le diagramme qui suit présente cet exemple.

![Le contrôle d’accès en fonction du rôle a été étendu pour prendre en charge les actions de plan de contrôle et de plan de données.](./media/role-definitions/rbac-data-plane.png)

Le rôle [Propriétaire](built-in-roles.md#owner) pour Alice et le rôle [Contributeur aux données blob du stockage](built-in-roles.md#storage-blob-data-contributor) pour Bob effectuent les actions suivantes :

Propriétaire

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Contributeur aux données Blob du stockage

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Comme Alice dispose d’une action avec caractère générique (`*`) au niveau de l’étendue de l’abonnement, elle hérite d’autorisations lui permettant d’effectuer toutes les actions de plan de contrôle. Alice peut lire, écrire et supprimer des conteneurs. En revanche, elle ne peut pas effectuer d’actions de plan de données sans passer par des étapes supplémentaires. Par exemple, par défaut, Alice ne peut pas lire les objets blob à l’intérieur d’un conteneur. Pour cela, elle doit récupérer les clés d’accès de stockage et les utiliser pour accéder aux objets blob.

Les autorisations de Bob se limitent aux actions `Actions` et `DataActions` spécifiées dans le rôle [Contributeur aux données blob du stockage](built-in-roles.md#storage-blob-data-contributor). En fonction du rôle, Bob peut effectuer à la fois des actions de plan de contrôle et de plan de données. Par exemple, Bob peut lire, écrire et supprimer des conteneurs du compte de stockage spécifié, mais aussi lire, écrire et supprimer les objets blob.

Pour plus d’informations sur la sécurité des plans de contrôle et de données pour le stockage, consultez le [guide de sécurité Stockage Microsoft Azure](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-actions"></a>Quels outils permettent d’utiliser les rôles Azure pour les actions sur les données ?

Pour afficher et utiliser des actions sur les données, vous devez disposer des versions appropriées des outils ou des Kits de développement logiciel (SDK) :

| Outil  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 ou ultérieure |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 ou version ultérieure |
| [Azure pour .NET](/dotnet/azure/) | 2.8.0-preview ou version ultérieure |
| [Kit de développement logiciel (SDK) Azure pour Go](/azure/go/azure-sdk-go-install) | 15.0.0 ou version ultérieure |
| [Azure pour Java](/java/azure/) | 1.9.0 ou version ultérieure |
| [Azure pour Python](/azure/python/) | 0.40.0 ou version ultérieure |
| [Kit de développement logiciel (SDK) Azure pour Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 ou version ultérieure |

Pour afficher et utiliser les actions sur les données dans l’API REST, vous devez définir le paramètre **api-version** sur la version suivante ou ultérieure :

- 01-07-2018

## <a name="actions"></a>Actions

L’autorisation `Actions` spécifie les actions de plan de contrôle que le rôle autorise. Il s’agit d’un ensemble de chaînes qui identifient les actions sécurisables des fournisseurs de ressources Azure. Voici quelques exemples d’actions de plan de contrôle qui peuvent être utilisées dans `Actions`.

> [!div class="mx-tableFixed"]
> | Chaîne d’action    | Description         |
> | ------------------- | ------------------- |
> | `*/read` | Accorde l’accès aux actions de lecture pour tous les types de ressources de l’ensemble des fournisseurs de ressources Azure.|
> | `Microsoft.Compute/*` | Accorde l’accès à l’ensemble des actions pour tous les types de ressources dans le fournisseur de ressources Microsoft.Compute.|
> | `Microsoft.Network/*/read` | Accorde l’accès aux actions de lecture pour tous les types de ressources dans le fournisseur de ressources Microsoft.Network.|
> | `Microsoft.Compute/virtualMachines/*` | Accorde l’accès à toutes les actions des machines virtuelles et à leurs types de ressources enfants.|
> | `microsoft.web/sites/restart/Action` | Accorde l’accès au redémarrage d’une application web.|

## <a name="notactions"></a>NotActions

L’autorisation `NotActions` spécifie les actions de plan de contrôle qui sont soustraites ou exclues des `Actions` autorisées ayant un caractère générique (`*`). Utilisez l’autorisation `NotActions` si l’ensemble des actions que vous souhaitez autoriser est plus facile à définir en soustrayant des `Actions` ayant un caractère générique (`*`). L’accès accordé par un rôle (autorisations effectives) est calculé en soustrayant les actions `NotActions` des actions `Actions`.

`Actions - NotActions = Effective control plane permissions`

Le tableau suivant présente deux exemples d’autorisations effectives de plan de contrôle pour une action avec caractère générique [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement) :

> [!div class="mx-tableFixed"]
> | Actions | NotActions | Autorisations effectives de plan de contrôle |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *Aucune* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> Si un utilisateur se voit attribuer un rôle qui exclut une action dans `NotActions`, et un second rôle qui accorde l’accès à cette même action, il est autorisé à effectuer celle-ci. `NotActions` n’est pas une règle de refus : il s’agit simplement d’un moyen pratique de créer un ensemble d’actions autorisées lorsque des actions spécifiques doivent être exclues.
>

### <a name="differences-between-notactions-and-deny-assignments"></a>Différences entre les NotActions et les affectations de refus

Les `NotActions` et les affectations de refus ne sont pas les mêmes et servent des objectifs différents. Les `NotActions` sont un moyen pratique de soustraire des actions spécifiques d’une action avec caractère générique (`*`).

Les affectations de refus empêchent les utilisateurs d’effectuer des actions particulières, même si une attribution de rôle leur accorde l’accès. Pour plus d’informations, consultez [Comprendre les affectations de refus Azure](deny-assignments.md).

## <a name="dataactions"></a>DataActions

L’autorisation `DataActions` spécifie les actions de plan de données que le rôle autorise sur vos données au sein de cet objet. Par exemple, si un utilisateur dispose d’un accès en lecture aux données blob d’un compte de stockage, il peut lire les objets blob de ce compte de stockage. Voici quelques exemples d’actions sur les données qui peuvent être utilisées dans `DataActions`.

> [!div class="mx-tableFixed"]
> | Chaîne d’action sur les données    | Description         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Retourne un objet blob ou une liste d'objets blob. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Retourne le résultat de l'écriture d'un objet blob. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Retourne un message. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Retourne un message ou le résultat de l’écriture ou de la suppression d’un message. |

## <a name="notdataactions"></a>NotDataActions

L’autorisation `NotDataActions` spécifie les actions de plan de données qui sont soustraites ou exclues des `DataActions` autorisées ayant un caractère générique (`*`). Utilisez l’autorisation `NotDataActions` si l’ensemble des actions que vous souhaitez autoriser est plus facile à définir en soustrayant des `DataActions` ayant un caractère générique (`*`). L’accès accordé par un rôle (autorisations effectives) est calculé en soustrayant les actions `NotDataActions` des actions `DataActions`. Chaque fournisseur de ressources fournit son propre ensemble d’API pour répondre à des actions sur les données.

`DataActions - NotDataActions = Effective data plane permissions`

Le tableau suivant présente deux exemples d’autorisations effectives de plan de données pour une action avec caractère générique [Microsoft.Storage](resource-provider-operations.md#microsoftstorage) :

> [!div class="mx-tableFixed"]
> | DataActions | NotDataActions | Autorisations effectives de plan de données |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *Aucune* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> Si un utilisateur se voit attribuer un rôle qui exclut une action sur les données dans `NotDataActions`, et un second rôle qui accorde l’accès à cette même action, il est autorisé à effectuer celle-ci. `NotDataActions` n’est pas une règle de refus : il s’agit simplement d’un moyen pratique de créer un ensemble d’actions sur les données autorisées lorsque des actions sur les données spécifiques doivent être exclues.
>

## <a name="assignablescopes"></a>AssignableScopes

La propriété `AssignableScopes` spécifie les étendues (groupes d’administration, abonnements ou groupes de ressources) auxquelles cette définition de rôle peut être attribuée. Vous pouvez mettre le rôle à disposition pour pouvoir l’attribuer uniquement dans les groupes d’administration, les abonnements ou les groupes de ressources qui en ont besoin. Vous devez utiliser au moins un groupe de gestion, un abonnement ou un groupe de ressources.

La chaîne `AssignableScopes` est définie sur l’étendue racine (`"/"`) pour les rôles intégrés. L’étendue racine indique que le rôle est disponible pour attribution dans toutes les étendues. Voici des exemples d’étendues assignables valides :

> [!div class="mx-tableFixed"]
> | Le rôle est disponible à l’attribution | Exemple |
> |----------|---------|
> | Abonnement unique | `"/subscriptions/{subscriptionId1}"` |
> | Deux abonnements | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Groupe de ressources réseau | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Un seul groupe d’administration | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Groupe d’administration et abonnement | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Toutes les étendues (applicable uniquement aux rôles intégrés) | `"/"` |

Pour plus d’informations sur `AssignableScopes` pour des rôles personnalisés, consultez [Rôles personnalisés Azure](custom-roles.md).

## <a name="next-steps"></a>Étapes suivantes

* [Rôles intégrés Azure](built-in-roles.md)
* [Rôle personnalisés Azure](custom-roles.md)
* [Opérations de fournisseur de ressources Azure](resource-provider-operations.md)
