---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 710c90895a9cf9ca1437c4882920977fdbf3983a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060176"
---
### <a name="find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group"></a>Rechercher les comptes de stockage avec une étiquette spécifique qui ne respecte pas la casse sur le groupe de ressources

Semblable à la requête « Rechercher les comptes de stockage avec une étiquette spécifique qui respecte la casse sur le groupe de ressources », mais lorsqu’il est nécessaire de rechercher un nom et une valeur d’étiquette qui ne respectent pas la casse, utilisez `mv-expand` avec le paramètre **bagexpansion**. Dans la mesure où cette requête utilise plus de quota que la requête originale, utilisez `mv-expand` uniquement en cas de nécessité.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | mv-expand bagexpansion=array tags
    | where isnotempty(tags)
    | where tags[0] =~ 'key1' and tags[1] =~ 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20mv-expand%20bagexpansion%3darray%20tags%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20where%20tags%5b0%5d%20%3d%7e%20%27key1%27%20and%20tags%5b1%5d%20%3d%7e%20%27value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20mv-expand%20bagexpansion%3darray%20tags%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20where%20tags%5b0%5d%20%3d%7e%20%27key1%27%20and%20tags%5b1%5d%20%3d%7e%20%27value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20mv-expand%20bagexpansion%3darray%20tags%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20where%20tags%5b0%5d%20%3d%7e%20%27key1%27%20and%20tags%5b1%5d%20%3d%7e%20%27value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.cn</a>

---

### <a name="find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group"></a>Rechercher les comptes de stockage avec une étiquette spécifique qui respecte la casse sur le groupe de ressources

La requête suivante utilise **inner** `join` pour connecter des comptes de stockage à des groupes de ressources ayant un nom et une valeur d’étiquette spécifiques qui respectent la casse.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['Key1'] =~ 'Value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20where%20tags%5b%27Key1%27%5d%20%3d%7e%20%27Value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20where%20tags%5b%27Key1%27%5d%20%3d%7e%20%27Value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%0a%7c%20join%20kind%3dinner%20(%0a%09ResourceContainers%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resources%2fsubscriptions%2fresourcegroups%27%0a%09%7c%20where%20tags%5b%27Key1%27%5d%20%3d%7e%20%27Value1%27%0a%09%7c%20project%20subscriptionId%2c%20resourceGroup)%0aon%20subscriptionId%2c%20resourceGroup%0a%7c%20project-away%20subscriptionId1%2c%20resourceGroup1" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-tag-names"></a>Lister tous les noms d’étiquette

Cette requête commence par l’étiquette et génère un objet JSON listant tous les noms d’étiquette uniques et leurs types correspondants.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20tags%0a%7c%20summarize%20buildschema(tags)" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20tags%0a%7c%20summarize%20buildschema(tags)" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20tags%0a%7c%20summarize%20buildschema(tags)" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-tags-and-their-values"></a>Lister toutes les étiquettes et leurs valeurs

Cette requête liste les étiquettes sur les groupes d’administration, les abonnements et les ressources ainsi que leurs valeurs. La requête limite d’abord les résultats aux ressources où les étiquettes sont non vides (`isnotempty()`), puis limite les champs inclus en incluant uniquement _tags_ dans `project`, `mvexpand` et `extend` pour obtenir les données associées à partir du jeu de propriétés. Elle utilise ensuite `union` pour combiner les résultats de _ResourceContainers_ aux mêmes résultats de _Resources_, couvrant largement les étiquettes effectivement extraites. Enfin, elle limite les résultats aux données associées `distinct` et exclut les étiquettes cachées par le système.

```kusto
ResourceContainers
| where isnotempty(tags)
| project tags
| mvexpand tags
| extend tagKey = tostring(bag_keys(tags)[0])
| extend tagValue = tostring(tags[tagKey])
| union (
    resources
    | where isnotempty(tags)
    | project tags
    | mvexpand tags
    | extend tagKey = tostring(bag_keys(tags)[0])
    | extend tagValue = tostring(tags[tagKey])
)
| distinct tagKey, tagValue
| where tagKey !startswith "hidden-"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) | union ( resources | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) ) | distinct tagKey, tagValue | where tagKey !startswith "hidden-""
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) | union ( resources | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) ) | distinct tagKey, tagValue | where tagKey !startswith "hidden-""
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20isnotempty(tags)%0a%7c%20project%20tags%0a%7c%20mvexpand%20tags%0a%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a%7c%20union%20(%0a%09resources%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20project%20tags%0a%09%7c%20mvexpand%20tags%0a%09%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%09%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a)%0a%7c%20distinct%20tagKey%2c%20tagValue%0a%7c%20where%20tagKey%20!startswith%20%22hidden-%22" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20isnotempty(tags)%0a%7c%20project%20tags%0a%7c%20mvexpand%20tags%0a%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a%7c%20union%20(%0a%09resources%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20project%20tags%0a%09%7c%20mvexpand%20tags%0a%09%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%09%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a)%0a%7c%20distinct%20tagKey%2c%20tagValue%0a%7c%20where%20tagKey%20!startswith%20%22hidden-%22" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0a%7c%20where%20isnotempty(tags)%0a%7c%20project%20tags%0a%7c%20mvexpand%20tags%0a%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a%7c%20union%20(%0a%09resources%0a%09%7c%20where%20isnotempty(tags)%0a%09%7c%20project%20tags%0a%09%7c%20mvexpand%20tags%0a%09%7c%20extend%20tagKey%20%3d%20tostring(bag_keys(tags)%5b0%5d)%0a%09%7c%20extend%20tagValue%20%3d%20tostring(tags%5btagKey%5d)%0a)%0a%7c%20distinct%20tagKey%2c%20tagValue%0a%7c%20where%20tagKey%20!startswith%20%22hidden-%22" target="_blank">portal.azure.cn</a>

---

### <a name="list-resources-with-a-specific-tag-value"></a>Lister les ressources avec une valeur d’étiquette spécifique

Nous pouvons limiter les résultats en fonction d’autres propriétés que le type de ressource Azure, notamment une étiquette. Dans cet exemple, nous filtrons les ressources Azure ayant une étiquette nommée **environment** avec pour valeur **internal**. Pour savoir également les étiquettes dont dispose la ressource et leurs valeurs, ajoutez la propriété **étiquettes** au mot-clé `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20tags.environment%3d%7e%27internal%27%0a%7c%20project%20name%2c%20tags" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20tags.environment%3d%7e%27internal%27%0a%7c%20project%20name%2c%20tags" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20tags.environment%3d%7e%27internal%27%0a%7c%20project%20name%2c%20tags" target="_blank">portal.azure.cn</a>

---

