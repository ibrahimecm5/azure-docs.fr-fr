---
author: craigshoemaker
ms.service: app-service
ms.topic: include
ms.date: 10/25/2021
ms.author: cshoe
ms.openlocfilehash: 67821760435fdac37c51400190c1afe44996d014
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096383"
---
### <a name="get-fully-qualified-domain-name"></a>Obtenir un nom de domaine complet

La commande `az containerapp show` retourne le nom de domaine complet de l’application de conteneur.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --query configuration.ingress.fqdn
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp show `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --query configuration.ingress.fqdn
```

---

Dans cet exemple, remplacez les espaces réservés entre `<>` par vos valeurs.

La valeur retournée par cette commande ressemble à un nom de domaine comme dans l’exemple suivant :

```console
myapp.happyhill-70162bb9.canadacentral.azurecontainerapps.io
```
