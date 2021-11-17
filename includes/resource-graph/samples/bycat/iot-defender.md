---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 00d6f16fd755b7e6d87fa4201c804fba42f0e333
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060169"
---
### <a name="count-all-sensors-by-type"></a>Compter tous les capteurs par type

Cette requête récapitule tous les capteurs par type (OT, EIoT).

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/sensors'
| summarize count() by tostring(properties.sensorType)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/sensors' | summarize count() by tostring(properties.sensorType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/sensors' | summarize count() by tostring(properties.sensorType)"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.cn</a>

---

### <a name="count-how-many-iot-devices-there-are-in-your-network-by-operation-system"></a>Compter le nombre d’appareils IoT dans votre réseau, par système d’exploitation

Cette requête récapitule tous les appareils IoT par plateforme de système d’exploitation.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/locations/devicegroups/devices'
| summarize count() by tostring(properties.operatingSystem.platform)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/devices' | summarize count() by tostring(properties.operatingSystem.platform)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/devices' | summarize count() by tostring(properties.operatingSystem.platform)"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.cn</a>

---

### <a name="get-all-high-severity-recommendations"></a>Obtenir toutes les recommandations avec un niveau de gravité élevé

Cette requête fournit une liste de toutes les recommandations avec un niveau de gravité élevé pour l’utilisateur.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations'
| where properties.severity == 'High'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations' | where properties.severity == 'High'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations' | where properties.severity == 'High'"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.cn</a>

---

### <a name="list-sites-with-a-specific-tag-value"></a>Lister les sites avec une valeur d’étiquette spécifique

Cette requête fournit une liste de tous les sites avec une valeur d’étiquette spécifique.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/sites'
| where properties.tags['key'] =~ 'value1'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/sites' | where properties.tags['key'] =~ 'value1'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/sites' | where properties.tags['key'] =~ 'value1'"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.cn</a>

---

