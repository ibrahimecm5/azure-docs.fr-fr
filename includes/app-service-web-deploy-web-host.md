---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 4108aca3e5f1d944005f02e56964687c75162b41
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571074"
---
### <a name="app-service-plan"></a>Plan App Service

Crée le plan de service pour l'hébergement de l'application web. Vous fournissez le nom du plan à l'aide du paramètre **hostingPlanName** . L’emplacement du plan est identique à celui utilisé pour le groupe de ressources. Le niveau tarifaire et la taille de worker sont spécifiés dans les paramètres **sku** et **workerSize**.

```config
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('hostingPlanName')]",
  "type": "Microsoft.Web/serverfarms",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[parameters('sku')]",
    "capacity": "[parameters('workerSize')]"
  },
  "properties": {
    "name": "[parameters('hostingPlanName')]"
  }
},
```
