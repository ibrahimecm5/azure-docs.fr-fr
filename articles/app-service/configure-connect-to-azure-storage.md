---
title: Monter le Stockage Azure en tant que partage local (conteneur)
description: Découvrez comment attacher un partage réseau personnalisé dans une application conteneurisée dans Azure App Service. Partagez des fichiers entre plusieurs applications, gérez le contenu statique à distance, accédez localement, etc.
author: msangapu-msft
ms.topic: article
ms.date: 09/02/2021
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 12f2cc667cc568132eb4e91a1f739f81b075fd80
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400088"
---
# <a name="mount-azure-storage-as-a-local-share-in-a-container-app-in-app-service"></a>Monter le Stockage Azure en tant que partage local dans une application de conteneur dans App Service

::: zone pivot="container-windows"

> [!NOTE]
>Le Stockage Azure dans un conteneur Windows dans App Service est **en préversion** et **n’est pas pris en charge** pour les **scénarios de production**.

Ce guide montre comment monter des fichiers Stockage Azure en tant que partage réseau dans un conteneur Windows dans App Service. Seuls les [partages de fichiers Azure](../storage/files/storage-how-to-use-files-portal.md) et [partages de fichiers Premium](../storage/files/storage-how-to-create-file-share.md) sont pris en charge. Voici les avantages du stockage monté personnalisé :

::: zone-end

::: zone pivot="container-linux"

Ce guide montre comment monter le Stockage Azure en tant que partage réseau dans un conteneur Linux intégré ou un conteneur Linux personnalisé dans App Service. Regardez la vidéo [Comment monter le Stockage Azure en tant que partage local](https://www.youtube.com/watch?v=OJkvpWYr57Y). Voici les avantages du stockage monté personnalisé :

::: zone-end

- Configurez un stockage persistant pour votre application App Service et gérez le stockage séparément.
- Rendez le contenu statique comme les vidéos et les images immédiatement disponible pour votre application App Service. 
- Écrire les fichiers journaux des applications ou archiver l’ancien journal des applications dans les partages de fichiers Azure.  
- Partager du contenu entre plusieurs applications ou avec d’autres services Azure.

::: zone pivot="container-windows"

Les fonctionnalités suivantes sont prises en charge pour les conteneurs Windows :

- Accès sécurisé aux comptes de stockage avec des [liaisons privées](../storage/common/storage-private-endpoints.md) (quand l’[intégration au réseau virtuel](./overview-vnet-integration.md) est utilisée). Aucun support pour le [point de terminaison de service](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) n’est pas actuellement disponible.
- Azure Files (lecture/écriture).
- Jusqu’à cinq points de montage par application.
- Affectations de lettres de lecteur (`C:` à `Z:`).

::: zone-end

::: zone pivot="container-linux"

Les fonctionnalités suivantes sont prises en charge pour les conteneurs Linux :

- Accès sécurisé aux comptes de stockage avec des [points de terminaison de service](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) et des [liaisons privées](../storage/common/storage-private-endpoints.md) (quand [l’intégration au réseau virtuel](./overview-vnet-integration.md) est utilisée).
- Azure Files (lecture/écriture).
- Objets blob Azure (en lecture seule).
- Jusqu’à cinq points de montage par application.

::: zone-end

## <a name="prerequisites"></a>Prérequis

::: zone pivot="container-windows"

- [Une application de conteneur Windows existante dans Azure App Service](quickstart-custom-container.md)
- [Création d’un partage de fichiers Azure](../storage/files/storage-how-to-use-files-portal.md)
- [Charger des fichiers dans un partage de fichiers Azure](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Une [application Azure App Service sur Linux](index.yml).
- [Compte Stockage Azure](../storage/common/storage-account-create.md?tabs=azure-cli)
- Un [partage et répertoire de fichiers Azure](../storage/files/storage-how-to-use-files-portal.md).

::: zone-end

> [!NOTE]
> Le Stockage Azure n’est pas le stockage par défaut pour App Service. Il n’est pas inclus et est facturé séparément.
>

## <a name="limitations"></a>Limites

::: zone pivot="container-windows"

- Les montages de Stockage ne sont pas pris en charge pour les applications natives Windows (non conteneurisées).
- Les objets blobs Azure ne sont pas pris en charge.
- [Le pare-feu de stockage](../storage/common/storage-network-security.md) est pris en charge uniquement via des [points de terminaison privés](../storage/common/storage-private-endpoints.md) (quand [l’intégration au réseau virtuel](./overview-vnet-integration.md) est utilisée). La prise en charge du système DNS personnalisé n’est pas disponible actuellement quand le compte Stockage Azure monté utilise un point de terminaison privé.
- Accès FTP/FTPS au stockage monté non pris en charge (utilisez [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/)).
- Les mappages `[C-Z]:\`, `[C-Z]:\home`, `/` et `/home` vers un stockage monté personnalisé ne sont pas pris en charge.
- Les montages de Stockage ne peuvent pas être utilisés avec l’option de clonage des paramètres lors de la création de [l’emplacement de déploiement](deploy-staging-slots.md).
- Les montages de Stockage ne sont pas sauvegardés quand vous [sauvegardez votre application](manage-backup.md). Veillez à suivre les meilleures pratiques pour sauvegarder les comptes Stockage Azure. 

::: zone-end

::: zone pivot="container-linux"

- [Le pare-feu de stockage](../storage/common/storage-network-security.md) est pris en charge uniquement par le biais des [points de terminaison de service](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) et des [points de terminaison privés](../storage/common/storage-private-endpoints.md) (quand [l’intégration au réseau virtuel](./overview-vnet-integration.md) est utilisée). La prise en charge du système DNS personnalisé n’est pas disponible actuellement quand le compte Stockage Azure monté utilise un point de terminaison privé.
- Accès FTP/FTPS au stockage monté personnalisé non pris en charge (utilisez [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/)).
- La prise en charge d’Azure CLI, d’Azure PowerShell et du kit Azure SDK est en préversion.
- Le mappage `/` ou `/home` vers le stockage monté personnalisé n’est pas pris en charge.
- Ne mappez pas le montage de stockage personnalisé sur `/tmp` ou ses sous-répertoires, car cela peut entraîner une expiration de délai lors du démarrage de l’application.
- Les montages de Stockage ne peuvent pas être utilisés avec l’option de clonage des paramètres lors de la création de [l’emplacement de déploiement](deploy-staging-slots.md).
- Les montages de Stockage ne sont pas sauvegardés quand vous [sauvegardez votre application](manage-backup.md). Veillez à suivre les meilleures pratiques pour sauvegarder les comptes Stockage Azure. 

::: zone-end

::: zone pivot="container-windows"
## <a name="mount-storage-to-windows-container"></a>Monter le stockage sur un conteneur Windows
::: zone-end
::: zone pivot="container-linux"
## <a name="mount-storage-to-linux-container"></a>Monter le stockage sur un conteneur Linux
::: zone-end

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Dans le [portail Azure](https://portal.azure.com), accéder à l’application.
1. Dans la barre de navigation gauche, cliquez sur **Configuration** > **Mappages de chemin d’accès** > **Nouveau montage Stockage Azure**. 
1. Configurez le montage de stockage conformément au tableau suivant. Lorsque vous avez terminé, cliquez sur **OK**.

    ::: zone pivot="container-windows"
    | Paramètre | Description |
    |-|-|
    | **Nom** | Nom de la configuration de montage. Les espaces ne sont pas autorisés. |
    | **Options de configuration** | Sélectionnez **De base** si le compte de stockage n’utilise pas de [points de terminaison privés](../storage/common/storage-private-endpoints.md). Sinon, sélectionnez **Avancé**. |
    | **Comptes de stockage** | Compte Stockage Azure. Il doit contenir un partage Azure Files. |
    | **Nom de partage** | Partage de fichiers à monter. |
    | **Clé d’accès** (Avancé uniquement) | [Clé d’accès](../storage/common/storage-account-keys-manage.md) de votre compte de stockage. |
    | **Chemin de montage** | Répertoire dans le conteneur Windows à monter sur Stockage Azure. N’utilisez pas un répertoire racine (`[C-Z]:\` ou `/`) ou le répertoire `home` (`[C-Z]:\home` ou `/home`).|
    ::: zone-end
    ::: zone pivot="container-linux"
    | Paramètre | Description |
    |-|-|
    | **Nom** | Nom de la configuration de montage. Les espaces ne sont pas autorisés. |
    | **Options de configuration** | Sélectionnez **De base** si le compte de stockage n’utilise pas de [points de terminaison de service](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) ni de [points de terminaison privés](../storage/common/storage-private-endpoints.md). Sinon, sélectionnez **Avancé**. |
    | **Comptes de stockage** | Compte Stockage Azure. |
    | **Type de stockage** | Sélectionnez le type en fonction du stockage que vous souhaitez monter. Les objets blob Azure prennent en charge l’accès en lecture seule uniquement. |
    | **Conteneur de stockage** ou **Nom de partage** | Partage de fichiers ou conteneur d’objets blob à monter. |
    | **Clé d’accès** (Avancé uniquement) | [Clé d’accès](../storage/common/storage-account-keys-manage.md) de votre compte de stockage. |
    | **Chemin de montage** | Répertoire dans le conteneur Linux à monter sur Stockage Azure. N’utilisez pas `/` ou `/home`.|
    ::: zone-end

    > [!CAUTION]
    > Le répertoire spécifié dans **Chemin de montage** dans le conteneur doit être vide. Tout contenu stocké dans ce répertoire est supprimé quand le Stockage Azure est monté (si vous spécifiez un répertoire sous `/home` par exemple). Si vous migrez des fichiers pour une application existante, faites une sauvegarde de l’application et de son contenu avant de commencer.
    >
    
# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Utilisez la commande [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add). Par exemple :

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

::: zone pivot="container-windows"
- `--storage-type` doit être `AzureFiles` pour les conteneurs Windows. 
- `mount-path-directory` doit avoir la forme `/path/to/dir` ou `[C-Z]:\path\to\dir` sans lettre de lecteur. N’utilisez pas un répertoire racine (`[C-Z]:\` ou `/`) ou le répertoire `home` (`[C-Z]:\home` ou `/home`).
::: zone-end
::: zone pivot="container-linux"
- `--storage-type` peut avoir la valeur `AzureBlob` ou `AzureFiles`. `AzureBlob` est en lecture seule.
- `--mount-path` est le répertoire dans le conteneur Linux à monter sur Stockage Azure. Ne pas utiliser `/` (répertoire racine).
::: zone-end

Vérifiez que votre stockage est monté en exécutant la commande suivante :

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

> [!CAUTION]
> Le répertoire spécifié dans `--mount-path` dans le conteneur doit être vide. Tout contenu stocké dans ce répertoire est supprimé quand le Stockage Azure est monté (si vous spécifiez un répertoire sous `/home` par exemple). Si vous migrez des fichiers pour une application existante, faites une sauvegarde de l’application et de son contenu avant de commencer.
>

Vérifiez votre configuration en exécutant la commande suivante :

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

---

> [!NOTE]
> L’ajout, la modification ou la suppression d’un montage de stockage entraîne le redémarrage de l’application. 

::: zone pivot="container-linux"

## <a name="test-the-mounted-storage"></a>Tester le stockage monté

Pour valider que le Stockage Azure est correctement monté pour l’application :

1. [Ouvrez une session SSH](configure-linux-open-ssh-session.md) dans le conteneur.
1. Dans le terminal SSH, exécutez ensuite la commande suivante :

    ```bash
    df –h 
    ```
1. Vérifiez si le partage de stockage est monté. S’il n’est pas présent, il y a un problème au niveau du montage du partage de stockage.
1. Vérifiez la latence ou l’accessibilité générale du montage de stockage avec la commande suivante :

    ```bash
    tcpping Storageaccount.file.core.windows.net 
    ```

::: zone-end

## <a name="best-practices"></a>Meilleures pratiques

- Pour éviter tout problème potentiel lié à la latence, placez l’application et le compte Stockage Azure dans la même région Azure. Notez toutefois que si l’application et le compte Stockage Azure se trouvent dans la même région Azure, et que vous permettez l’accès à partir d’adresses IP App Service dans la [configuration du pare-feu Stockage Azure](../storage/common/storage-network-security.md), ces restrictions d’adresse IP ne sont pas respectées.
::: zone pivot="container-windows"
- Le répertoire de montage dans l’application de conteneur doit être vide. Tout contenu stocké dans ce chemin est supprimé quand le Stockage Azure est monté. Si vous migrez des fichiers pour une application existante, faites une sauvegarde de l’application et de son contenu avant de commencer.
::: zone-end
::: zone pivot="container-linux"
- Le répertoire de montage dans l’application de conteneur doit être vide. Tout contenu stocké dans ce chemin est supprimé quand le Stockage Azure est monté (si vous spécifiez un répertoire sous `/home` par exemple). Si vous migrez des fichiers pour une application existante, faites une sauvegarde de l’application et de son contenu avant de commencer.

- Il n’est pas recommandé de monter le stockage sur `/home`, car cela peut entraîner des goulots d’étranglement des performances pour l’application. 
::: zone-end
- Dans le compte Stockage Azure, évitez de [régénérer la clé d’accès](../storage/common/storage-account-keys-manage.md) utilisée pour monter le stockage dans l’application. Le compte de stockage contient deux clés différentes. Utilisez une approche progressive pour vous assurer que le montage de stockage reste disponible pour l’application lors de la régénération de la clé. Imaginons par exemple que vous avez utilisé **key1** pour configurer le montage de stockage dans votre application :

    1. Régénérer **key2**. 
    1. Dans la configuration du montage de stockage, mettez à jour l’accès à la clé pour utiliser la clé régénérée **key2**.
    1. Régénérer **key1**.

- Si vous supprimez un compte Stockage Azure, un conteneur ou un partage, supprimez la configuration de montage de stockage correspondante dans l’application pour éviter d’éventuels scénarios d’erreur. 

- Le compte Stockage Azure monté peut être un niveau de performance Standard ou Premium. En fonction des exigences en termes de capacité et de débit de l’application, choisissez le niveau de performance approprié pour le compte de stockage. Consultez les objectifs de scalabilité et de performances qui correspondent au type de stockage :

    - [Pour les fichiers](../storage/files/storage-files-scale-targets.md) (conteneurs Windows et Linux)
    - [Pour les blobs](../storage/blobs/scalability-targets.md) (conteneurs Linux uniquement)

- Si votre application [effectue une mise à l’échelle pour plusieurs instances](../azure-monitor/autoscale/autoscale-get-started.md), toutes les instances se connectent au même compte Stockage Azure monté. Pour éviter les problèmes de goulots d’étranglement des performances et de débit, choisissez le niveau de performance approprié pour le compte de stockage.  

- Il n’est pas recommandé d’utiliser des montages de stockage pour les bases de données locales (comme SQLite) ou pour d’autres applications et composants qui reposent sur des verrous et handles de fichiers. 

- Quand vous utilisez les [points de terminaison privés](../storage/common/storage-private-endpoints.md) Stockage Azure avec l’application, vous devez définir les deux paramètres d’application suivants :

    - `WEBSITE_DNS_SERVER` = `168.63.129.16`
    - `WEBSITE_VNET_ROUTE_ALL` = `1`

- Si vous [initiez un basculement de stockage](../storage/common/storage-initiate-account-failover.md) et que le compte de stockage est monté sur l’application, le montage ne parvient pas à se connecter tant que vous n’avez pas redémarré l’application ni supprimé puis ajouté le montage Stockage Azure. 

## <a name="next-steps"></a>Étapes suivantes

::: zone pivot="container-windows"

- [Migrer des logiciels personnalisés vers Azure App Service à l’aide d’un conteneur personnalisé](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Configurer un conteneur personnalisé](configure-custom-container.md?pivots=platform-linux).
- [Vidéo : Monter le Stockage Azure en tant que partage local](https://www.youtube.com/watch?v=OJkvpWYr57Y).

::: zone-end
