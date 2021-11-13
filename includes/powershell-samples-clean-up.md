---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/19/2021
ms.author: cephalin
ms.openlocfilehash: a1128c7e2e8a89c8d60e629437a12e2c1378babc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455990"
---
## <a name="clean-up-resources"></a>Nettoyer les ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, supprimez le groupe de ressources en exécutant la commande PowerShell suivante :

```azurecli-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

L’exécution de cette commande peut prendre une minute.