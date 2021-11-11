---
title: Changer le port PostgreSQL
description: Changez le port sur lequel le groupe de serveurs PostgreSQL Hyperscaleavec Azure Arc est en écoute.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 516c296bc18a3a38b0a6bbe303313119fe2bce1d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561799"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Changer le port sur lequel le groupe de serveurs écoute 

Pour changer le port, modifiez le groupe de serveurs. Par exemple, examinez la commande suivante :

```azurecli
 az postgres arc-server edit -n <server group name> --port <desired port number> --k8s-namespace <namespace> --use-k8s
```

Si le nom de votre groupe de serveurs est _postgres01_ et que vous souhaitez qu’il écoute sur le port _866_. Exécutez la commande suivante :

```azurecli
 az postgres arc-server edit -n postgres01 --port 866 --k8s-namespace arc --use-k8s
```

## <a name="verify-that-the-port-was-changed"></a>Vérifiez que le port a changé

Pour vérifier que le port a changé, exécutez la commande suivante pour afficher la configuration de votre groupe de serveurs :

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

Dans la sortie de cette commande, examinez le numéro de port affiché pour l’élément « port » dans la section « service » des spécifications de votre groupe de serveurs.

Vous pouvez également vérifier dans l’élément `externalEndpoint` de la section État des spécifications de votre groupe de serveurs que l’adresse IP est suivie par le numéro de port que vous avez configuré.

À titre d’illustration, pour continuer l’exemple ci-dessus, exécutez la commande suivante :

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s
```

La commande retourne le port 866 :

```output
"services": {
      "primary": {
        "port": 866,
        "type": "LoadBalancer"
      }
    }
```

Notez également la valeur de `primaryEndpoint`.

```output
"primaryEndpoint": "12.345.67.890:866",
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur la [procédure de connexion à votre groupe de serveurs](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- En savoir plus sur la procédure de configuration d’autres aspects de votre groupe de serveurs dans la section Procédures\Gérer\Configurer et mettre à l'échelle de la documentation.
