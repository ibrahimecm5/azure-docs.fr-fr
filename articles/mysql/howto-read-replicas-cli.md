---
title: Gérer des réplicas en lecture - AZURE CLI, API REST - Azure Database pour MySQL
description: Découvrez comment configurer et gérer des réplicas en lecture dans Azure Database pour MySQL à partir d’Azure CLI ou de l’API REST.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c7f33156394b3dfde100014ace6d8b7f1cbc8caf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524456"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Comment créer et gérer des réplicas en lecture dans Azure Database pour MySQL à l’aide d’Azure CLI et de l’API REST

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Dans cet article, vous allez apprendre à créer et à gérer des réplicas en lecture dans le service Azure Database pour MySQL à l’aide d’Azure CLI et de l’API REST. Pour en savoir plus sur les réplicas en lecture, consultez [vue d’ensemble](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Vous pouvez créer et gérer des réplicas en lecture à l’aide d’Azure CLI.

### <a name="prerequisites"></a>Prérequis

- [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli)
- Un [serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) qui sera utilisé comme serveur source. 

> [!IMPORTANT]
> La fonctionnalité de réplica en lecture est disponible uniquement pour les serveurs Azure Database pour MySQL dans les niveaux tarifaires Usage général ou Mémoire optimisée. Vérifiez que le serveur source se trouve dans l’un de ces niveaux tarifaires.

### <a name="create-a-read-replica"></a>Créer un réplica en lecture

> [!IMPORTANT]
> Lorsque vous créez un réplica pour un serveur source qui n’en a pas, ce dernier commence par redémarrer afin de se préparer à la réplication. Tenez-en compte et effectuez ces opérations en période creuse.
>
>Si le GTID est activé sur un serveur primaire (`gtid_mode` = ON), il sera également activé sur les réplicas nouvellement créés, et ceux-ci utiliseront la réplication basée sur GTID. Pour en savoir plus, reportez-vous à [Identificateur de transaction global (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid)

Un serveur réplica en lecture peut être créé en utilisant la commande suivante :

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

La commande `az mysql server replica create` requiert les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources dans lequel le serveur réplica sera créé.  |
| name | mydemoreplicaserver | Nom du nouveau serveur réplica créé. |
| source-server | mydemoserver | Nom ou ID du serveur source à partir duquel la réplication doit être effectuée. |

Pour créer un réplica en lecture entre régions, utilisez le paramètre `--location`. L’exemple CLI ci-dessous crée le réplica dans la région USA Ouest.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Pour en savoir plus sur les régions dans lesquelles vous pouvez créer un réplica, consultez l’article [Concepts relatifs aux réplicas en lecture](concepts-read-replicas.md). 

> [!NOTE]
> * La commande `az mysql server replica create` possède un argument `--sku-name` qui vous permet de spécifier le niveau tarifaire (`{pricing_tier}_{compute generation}_{vCores}`) lorsque vous créez un réplica à l’aide d’Azure CLI. </br>
> * Le serveur primaire et le réplica en lecture doivent avoir le même niveau tarifaire (Usage général ou À mémoire optimisée). </br>
> * La configuration du serveur réplica peut également être modifiée après sa création. Il est recommandé de maintenir la configuration du serveur réplica à des valeurs égales ou supérieures à celles du serveur source pour garantir que le réplica sera à la hauteur du serveur maître.


### <a name="list-replicas-for-a-source-server"></a>Répertorier les réplicas d'un serveur source

Pour afficher tous les réplicas d'un serveur source donné, exécutez la commande suivante : 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

La commande `az mysql server replica list` requiert les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources dans lequel le serveur réplica sera créé.  |
| server-name | mydemoserver | Nom ou ID du serveur source. |

### <a name="stop-replication-to-a-replica-server"></a>Arrêter la réplication vers un serveur réplica

> [!IMPORTANT]
> L’arrêt de la réplication vers un serveur est irréversible. Une fois la réplication entre un serveur source et un serveur réplica arrêtée, il est impossible de revenir en arrière. Le serveur réplica devient un serveur autonome et prend désormais en charge la lecture et les écritures. Ce serveur ne peut pas être à nouveau transformé en réplica.

La réplication d'un serveur réplica en lecture peut être arrêtée en utilisant la commande suivante :

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

La commande `az mysql server replica stop` requiert les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources où se trouve le serveur réplica.  |
| name | mydemoreplicaserver | Nom du serveur réplica pour lequel arrêter la réplication. |

### <a name="delete-a-replica-server"></a>Supprimer un serveur réplica

La suppression d’un serveur réplica en lecture peut être effectuée en exécutant la commande **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Supprimer un serveur source

> [!IMPORTANT]
> La suppression d’un serveur source arrête la réplication vers tous les serveurs réplicas et supprime le serveur source proprement dit. Les serveurs réplicas deviennent des serveurs autonomes qui prennent désormais en charge la lecture et les écritures.

Pour supprimer un serveur source, vous pouvez exécuter la commande **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>API REST
Vous pouvez créer et gérer des réplicas en lecture à l’aide de l’[API REST Azure](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Créer un réplica en lecture
Vous pouvez créer un réplica en lecture à l’aide de l’[API de création](/rest/api/mysql/flexibleserver(preview)/servers/create) :

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Pour en savoir plus sur les régions dans lesquelles vous pouvez créer un réplica, consultez l’article [Concepts relatifs aux réplicas en lecture](concepts-read-replicas.md). 

Si vous n'avez pas affecté au paramètre `azure.replication_support` la valeur **REPLICA** sur un serveur source à usage général ou à mémoire optimisée, et que vous avez redémarré le serveur, vous recevez une erreur. Effectuez ces deux étapes avant de créer un réplica.

Le réplica doit être créé en utilisant les mêmes paramètres de calcul et de stockage que le serveur maître. Une fois le réplica créé, vous pouvez changer plusieurs paramètres indépendamment du serveur source : génération de calcul, vCores, stockage et durée de conservation des sauvegardes. Le niveau tarifaire peut également être changé indépendamment, sauf vers ou depuis le niveau De base.


> [!IMPORTANT]
> Avant de modifier un paramètre du serveur source, remplacez la valeur du paramètre du réplica par une valeur supérieure ou égale à celle du serveur maître. Vous garantissez ainsi l’alignement du réplica sur les changements apportés au serveur maître.

### <a name="list-replicas"></a>Lister les réplicas
Vous pouvez afficher la liste des réplicas d'un serveur source à l'aide de l'[API Lister les réplicas](/rest/api/mysql/flexibleserver(preview)/replicas/listbyserver) :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Arrêter la réplication vers un serveur réplica
Vous pouvez arrêter la réplication entre un serveur source et un réplica en lecture à l'aide de l'[API de mise à jour](/rest/api/mysql/flexibleserver(preview)/servers/update).

Une fois que vous avez arrêté la réplication entre un serveur source et un réplica en lecture, vous ne pouvez pas revenir en arrière. Le réplica en lecture devient un serveur autonome qui prend en charge les lectures et les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>Supprimer un serveur source ou réplica
Pour supprimer un serveur source ou réplica, vous devez utiliser l'[API de suppression](/rest/api/mysql/flexibleserver(preview)/servers/delete) :

Lorsque vous supprimez un serveur source, la réplication est arrêtée sur tous les réplicas en lecture. Les réplicas en lecture deviennent des serveurs autonomes qui prennent désormais en charge les lectures et les écritures.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```

### <a name="known-issue"></a>Problème connu

Il existe deux générations de stockage que les serveurs des niveaux Usage général et À mémoire optimisée utilisent : le stockage Usage général v1 (prend en charge jusqu’à 4 To) et le stockage Usage général v2 (prend en charge jusqu’à 16 To).
Le serveur source et le serveur réplica doivent avoir le même type de stockage. Le [stockage Usage général v2](./concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage) n’étant pas disponible dans toutes les régions, veillez à choisir la région de réplica appropriée lorsque vous utilisez l’emplacement avec l’interface CLI ou l’API REST pour la création de réplica en lecture. Pour savoir comment identifier le type de stockage de votre serveur source, consultez le lien [Comment puis-je déterminer le type de stockage sur lequel mon serveur s’exécute ?](./concepts-pricing-tiers.md#how-can-i-determine-which-storage-type-my-server-is-running-on) 

Si vous choisissez une région dans laquelle vous ne pouvez pas créer de réplica en lecture pour votre serveur source, vous rencontrerez le problème suivant : le déploiement continuera à s’exécuter comme indiqué dans la figure ci-dessous, puis expirera avec l’erreur *« L’opération d’approvisionnement en ressources ne s’est pas terminée dans le délai imparti »* .

[ :::image type="content" source="media/howto-read-replicas-cli/replcia-cli-known-issue.png" alt-text="Erreur CLI d’un réplica en lecture.":::](media/howto-read-replicas-cli/replcia-cli-known-issue.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Découvrir plus en détail les [réplicas en lecture](concepts-read-replicas.md)
