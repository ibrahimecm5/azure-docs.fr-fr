---
title: Considérations relatives au stockage pour Azure Functions
description: En savoir plus sur les exigences de stockage d’Azure Functions et sur le chiffrement des données stockées.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 6dc2bad744118e57b9e958658814f5c194f633ad
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216615"
---
# <a name="storage-considerations-for-azure-functions"></a>Considérations relatives au stockage pour Azure Functions

Azure Functions nécessite un compte Stockage Azure lorsque vous créez une instance d’application de fonction. Les services de stockage suivants peuvent être utilisés par votre application de fonction :


|Service de stockage  | Utilisation de Functions  |
|---------|---------|
| [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md)     | Conserve l’état des liaisons et les touches de fonction.  <br/>Également utilisé par [des hubs de tâches dans Durable Functions](durable/durable-functions-task-hubs.md). |
| [Azure Files](../storage/files/storage-files-introduction.md)  | Partage de fichiers utilisé pour stocker et exécuter le code de votre application de fonction dans un [plan de consommation](consumption-plan.md) et un [plan Premium](functions-premium-plan.md). <br/>Azure Files est configuré par défaut, mais sous certaines conditions, vous pouvez [créer une application sans Azure Files](#create-an-app-without-azure-files). |
| [Stockage File d’attente Azure](../storage/queues/storage-queues-introduction.md)     | Utilisé par [des hubs de tâches dans Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Stockage de tables Azure](../storage/tables/table-storage-overview.md)  |  Utilisé par [des hubs de tâches dans Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Lorsque vous utilisez le plan d’hébergement de consommation/Premium, les fichiers de code de fonction et de configuration de liaison sont stockés dans Azure Files dans le compte de stockage principal. Lorsque vous supprimez le compte de stockage principal, ce contenu est supprimé et ne peut pas être récupéré.

## <a name="storage-account-requirements"></a>Conditions requises pour le compte de stockage

Quand vous créez une application de fonction, vous devez créer un compte de stockage Azure à usage général qui prend en charge le stockage Blob, File d’attente et Table, ou établir un lien vers un compte de ce type. Cela est dû au fait que Functions s’appuie sur Stockage Azure pour les opérations telles que la gestion des déclencheurs et la journalisation des exécutions de fonctions. Certains comptes de stockage ne prennent pas en charge les files d’attente ni les tables. Ces comptes incluent les comptes de stockage blob uniquement et Stockage Premium Azure.

Pour en savoir plus sur les types de compte de stockage, consultez [Présentation des services Stockage Azure](../storage/common/storage-introduction.md#core-storage-services). 

Bien que vous puissiez utiliser un compte de stockage existant avec votre application de fonction, vous devez vous assurer qu’il répond à ces exigences. Les comptes de stockage créés dans le cadre du flux de création de l’application de fonction dans le portail Azure sont assurés de répondre à ces exigences en matière de comptes de stockage. Dans le portail, les comptes non pris en charge sont filtrés lorsque vous choisissez un compte de stockage existant pendant la création d’une application de fonction. Dans ce flux, vous êtes uniquement autorisé à choisir des comptes de stockage existants dans la même région que l’application de fonction que vous créez. Pour plus d’informations, consultez [Emplacement du compte de stockage](#storage-account-location).

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>Guide du compte de stockage

Chaque application de fonction nécessite un compte de stockage afin de fonctionner. Si ce compte est supprimé, votre application de fonction ne s’exécutera pas. Pour détecter un problème lié au stockage, consultez [Comment résoudre les problèmes liés au stockage](functions-recover-storage-account.md). Les autres considérations suivantes s’appliquent au compte de stockage utilisé par les applications de fonction.

### <a name="storage-account-location"></a>Emplacement du compte de stockage

Pour des performances optimales, votre application de fonction doit utiliser un compte de stockage dans la même région, ce qui réduit la latence. Le portail Azure applique cette meilleure pratique. Si, pour une raison quelconque, vous devez utiliser un compte de stockage dans une région différente de votre application de fonction, vous devez créer votre application de fonction en dehors du portail. 

### <a name="storage-account-connection-setting"></a>Paramètre de connexion au compte de stockage

La connexion au compte de stockage est conservée dans le [paramètre d’application AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

La chaîne de connexion du compte de stockage doit être mise à jour lorsque vous régénérez des clés de stockage. [En savoir plus sur la gestion des clés de stockage ici](../storage/common/storage-account-create.md).

### <a name="shared-storage-accounts"></a>Comptes de stockage partagés

Plusieurs applications de fonction peuvent partager le même compte de stockage sans aucun problème. Par exemple, dans Visual Studio, vous pouvez développer plusieurs applications à l’aide de l’émulateur Stockage Azure. Dans ce cas, l’émulateur agit comme un compte de stockage unique. Le même compte de stockage que celui utilisé par votre application de fonction peut également être utilisé pour stocker vos données d’application. Toutefois, cette approche n’est pas toujours recommandée dans un environnement de production.

### <a name="optimize-storage-performance"></a>Optimiser les performances de stockage

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Chiffrement des données de stockage

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>Résidence des données dans la région

Lorsque toutes les données client doivent rester dans une seule région, le compte de stockage associé à l’application de fonction doit être un compte avec une [redondance dans la région](../storage/common/storage-redundancy.md). Un compte de stockage redondant dans la région doit également être utilisé avec [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection).

Les autres données client gérées par la plateforme ne sont stockées au sein de la région que si elles sont hébergées dans un environnement App Service Environment (ASE) avec équilibrage de charge interne. Pour plus d’informations, consultez [Redondance de zone ASE](../app-service/environment/zone-redundancy.md#in-region-data-residency).

## <a name="create-an-app-without-azure-files"></a>Créer une application sans Azure Files

Azure Files est configuré par défaut pour les plans de consommation Premium et non basés sur Linux afin de faire office de système de fichiers partagé dans les scénarios à grande échelle. Le système de fichiers est utilisé par la plateforme pour certaines fonctionnalités telles que le streaming de journaux, mais il assure essentiellement la cohérence de la charge utile de la fonction déployée. Lorsqu’une application est [déployée à l’aide d’une URL de package externe](./run-functions-from-deployment-package.md), le contenu de l’application est pris en charge à partir d’un système de fichiers en lecture seule distinct et dès lors, Azure Files peut être omis. Dans ce cas, un système de fichiers accessible en écriture est mis à disposition, mais il n’est pas garanti qu’il soit partagé avec toutes les instances d’application de fonction.

Lorsque Azure Files n’est pas utilisé, vous devez tenir compte de ce qui suit :

* Vous devez effectuer le déploiement à partir d’une URL de package externe
* Votre application ne peut pas reposer sur un système de fichiers accessible en écriture partagé
* L’application ne peut pas utiliser le runtime Functions v1
* Les expériences de streaming de journaux dans les clients tels que le portail Azure s’apparentent aux journaux du système de fichiers. Privilégiez les journaux Application Insights.

Si les éléments ci-dessus sont correctement pris en compte, vous pouvez créer l’application sans Azure Files. Créez l’application de fonction sans spécifier les paramètres `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` et `WEBSITE_CONTENTSHARE`. Pour ce faire, vous pouvez générer un modèle ARM pour un déploiement standard, supprimer ces deux paramètres, puis déployer le modèle. 

Étant donné que les fonctions utilisent Azure Files durant certaines parties du processus de scale-out dynamique, la mise à l’échelle peut être limitée lors de l’exécution sans Azure Files sur les plans Consommation et Premium.

## <a name="mount-file-shares"></a>Monter des partages de fichiers

_Cette fonctionnalité est actuellement disponible uniquement sous Linux._ 

Vous pouvez monter des partages Azure Files existants dans vos applications de fonction Linux. Monter un partage vers votre application de fonction Linux vous permet d’accéder à des modèles Machine Learning existants ou à d’autres données dans vos fonctions. Vous pouvez utiliser la commande [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) pour monter un partage existant dans votre application de fonction Linux. 

Dans cette commande, `share-name` est le nom du partage Azure Files existant, et `custom-id` peut représenter n’importe quelle chaîne qui définit de façon unique le partage lorsqu’il est monté sur l’application de fonction. En outre, `mount-path` est le chemin d’accès à partir duquel le partage est accessible dans votre application de fonction. `mount-path` doit être au format `/dir-name`, et ne peut pas commencer par `/home`.

Pour obtenir un exemple complet, consultez les scripts dans [Créer une application de fonction Python et monter un partage Azure Files](scripts/functions-cli-mount-files-storage-linux.md). 

Actuellement, seul un stockage `storage-type` de type `AzureFiles` est pris en charge. Vous pouvez uniquement monter cinq partages sur une application de fonction donnée. Le montage d’un partage de fichiers peut augmenter le temps de démarrage à froid d’au moins 200 à 300 ms voire plus si le compte de stockage se trouve dans une autre région.

Le partage monté est disponible pour votre code de fonction à l’emplacement `mount-path` spécifié. Par exemple, lorsque `mount-path` est `/path/to/mount`, vous pouvez accéder au répertoire cible à l’aide des API du système de fichiers, comme dans l’exemple Python suivant :

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les options d’hébergement d’Azure Functions.

> [!div class="nextstepaction"]
> [Échelle et hébergement dans Azure Functions](functions-scale.md)
