---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: a34e4d77d77ad34a8a0487a9efcdeb48ed510dda
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131663711"
---
### <a name="publish-messages-and-manage-the-clients"></a>Publier des messages et gérer les clients

Azure CLI fournit également les commandes [az webpubsub service](/cli/azure/webpubsub/service) pour gérer les connexions client.

Ouvrez **une autre** commande CLI pour pouvoir diffuser des messages aux clients :

- Nom de l’hub : **myHub1**.
- Groupe de ressources nommé : **myResourceGroup**.

```azurecli-interactive
az webpubsub service broadcast --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name "myHub1" --payload "Hello World"
```

Revenez à la commande CLI précédente pour pouvoir vérifier que le client a reçu le message :
```JSON
{"type":"message","from":"server","dataType":"text","data":"Hello World"}
```

Vous pouvez également répertorier toutes les commandes disponibles à l’aide de l'option `--help` et jouer avec les commandes répertoriées.

```azurecli-interactive
az webpubsub service --help
```
