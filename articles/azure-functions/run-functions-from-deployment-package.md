---
title: Exécuter Azure Functions à partir d’un package
description: Exécutez vos fonctions avec le runtime Azure Functions, en montant un fichier de package de déploiement qui contient les fichiers projet de votre application de fonction.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: c2c0797f256cfd302f4b8a5a80300ef4469355e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256986"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Exécuter des fonctions Azure à partir d’un fichier de package

Dans Azure, vous pouvez exécuter vos fonctions directement dans un fichier de package de déploiement de votre application de fonction. L’autre option consiste à déployer vos fichiers dans le répertoire `d:\home\site\wwwroot` de votre application de fonction.

Cet article décrit les avantages que présente l’exécution de vos fonctions à partir d’un package. Il explique également comment activer cette fonctionnalité dans votre application de fonction.

## <a name="benefits-of-running-from-a-package-file"></a>Avantages de l’exécution à partir d’un fichier de package
  
L’exécution à partir d’un fichier de package présente plusieurs avantages :

+ Réduction des risques de verrouillage lors de la copie de fichiers
+ Déploiement possible sur une application de production (après redémarrage)
+ Connaissance des fichiers qui sont exécutés dans votre application
+ Amélioration des performances des [déploiements Azure Resource Manager](functions-infrastructure-as-code.md)
+ Peut réduire les temps de démarrage à froid, en particulier pour les fonctions JavaScript avec des arborescences de package npm de grande taille.

Pour plus d’informations, lisez [cette annonce](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Permettre aux fonctions de s’exécuter à partir d’un package

Pour permettre à votre application de fonction de s’exécuter à partir d’un package, il suffit d’ajouter un paramètre `WEBSITE_RUN_FROM_PACKAGE` à vos paramètres d’application de fonction. Le paramètre `WEBSITE_RUN_FROM_PACKAGE` doit avoir l’une des valeurs suivantes :

| Value  | Description  |
|---------|---------|
| **`1`**  | Recommandé pour les applications de fonction qui s’exécutent sur Windows. Lancez l’exécution à partir d’un fichier de package dans le dossier `d:\home\data\SitePackages` de votre application de fonction. Si vous ne [déployez pas le fichier avec la fonction de déploiement zip](#integration-with-zip-deployment), le dossier doit également contenir un fichier appelé `packagename.txt` pour que cette option puisse être utilisée. Ce fichier contient uniquement le nom du fichier de package du dossier, sans espace. |
|**`<URL>`**  | Emplacement d’un fichier de package que vous souhaitez exécuter. Lorsque vous spécifiez une URL, vous devez également [synchroniser les déclencheurs](functions-deployment-technologies.md#trigger-syncing) après avoir publié un package mis à jour. <br/>Lorsque vous utilisez le stockage Blob, vous ne devez généralement pas utiliser un Blob public. Au lieu de cela, utilisez un conteneur privé avec une [signature d’accès partagé (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) ou [utilisez une identité managée](#fetch-a-package-from-azure-blob-storage-using-a-managed-identity) pour permettre au runtime Functions d’accéder au package. Vous pouvez utiliser [l’Explorateur Stockage Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) pour charger des fichiers de package sur votre compte Stockage Blob. |

> [!CAUTION]
> Lorsque vous exécutez une application de fonction sur Windows, l’option d’URL externe génère les performances de démarrage à froid les moins élevées. Lorsque vous déployez votre application de fonction sur Windows, vous devez définir `WEBSITE_RUN_FROM_PACKAGE` sur `1` et effectuer la publication avec le déploiement zip.

L’exemple suivant montre une application de fonction configurée pour s’exécuter à partir d’un fichier zip hébergé dans le stockage Blob Azure :

![Paramètre d’application WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Actuellement, seuls les fichiers de package zip sont pris en charge.

### <a name="fetch-a-package-from-azure-blob-storage-using-a-managed-identity"></a>Extraire un package à partir de Stockage Blob Azure à l’aide d’une identité managée

[!INCLUDE [Run from package via Identity](../../includes/app-service-run-from-package-via-identity.md)]

## <a name="integration-with-zip-deployment"></a>Intégration au déploiement zip

[Déploiement zip][Zip deployment for Azure Functions] est une fonctionnalité Azure App Service qui vous permet de déployer votre projet d’application de fonction dans le répertoire `wwwroot`. Le projet est empaqueté dans un fichier de déploiement zip. Les mêmes API peuvent être utilisées pour déployer votre package dans le dossier `d:\home\data\SitePackages`. Quand le paramètre d’application `WEBSITE_RUN_FROM_PACKAGE` est défini sur `1`, les API de déploiement zip copient votre package dans le dossier `d:\home\data\SitePackages` au lieu d’extraire les fichiers dans `d:\home\site\wwwroot`. Le fichier `packagename.txt` est également créé. Après un redémarrage, le package est monté sur `wwwroot` en tant que système de fichiers en lecture seule. Pour plus d’informations sur le déploiement zip, consultez [Déploiement zip pour Azure Functions](deployment-zip-push.md).

> [!NOTE]
> Lorsqu’un déploiement se produit, un redémarrage de l’application de fonction est déclenché. Avant un redémarrage, toutes les exécutions de fonction existantes sont autorisées à se terminer ou à expirer. Pour plus d’informations, consultez [Comportements de déploiement](functions-deployment-technologies.md#deployment-behaviors).

## <a name="adding-the-website_run_from_package-setting"></a>Ajout du paramètre WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="example-workflow-for-manually-uploading-a-package-hosted-in-azure-storage"></a>Exemple de flux de travail pour le chargement manuel d’un package hébergé dans Stockage Azure

Pour déployer un package compressé lorsque vous utilisez l’option URL, vous devez créer un package de déploiement .zip compressé et le charger vers la destination. Cet exemple utilise un conteneur Stockage Blob. 

1. Créez un package .zip pour votre projet à l’aide de l’utilitaire de votre choix.

1. Dans le [portail Azure](https://portal.azure.com), recherchez le nom de votre compte de stockage, ou parcourez les comptes de stockage à sa recherche.
 
1. Dans le compte de stockage, sélectionnez **Conteneurs** sous **Stockage des données**.

1. Sélectionnez **+ Conteneur** pour créer un conteneur Stockage Blob dans votre compte.

1. Dans la page **Nouveau conteneur**, fournissez un **Nom** (par exemple « déploiements »), vérifiez que le **Niveau d’accès public** est **Privé**, puis sélectionnez **Créer**.

1. Sélectionnez le conteneur que vous avez créé, sélectionnez **Charger**, accédez à l’emplacement du fichier .zip que vous avez créé avec votre projet, puis sélectionnez **Charger**.

1. Une fois le chargement terminé, choisissez votre fichier blob chargé, puis copiez l’URL. Vous devrez peut-être générer une URL SAS si vous n’[utilisez pas d’identité](#fetch-a-package-from-azure-blob-storage-using-a-managed-identity).

1. Recherchez votre application de fonction ou parcourez la page **Function App** à sa recherche. 

1. Dans votre application de fonction, sélectionnez **Configurations** sous **Paramètres**.

1. Sous l’onglet **Paramètres d’application**, sélectionnez **Nouveau paramètre d’application**.

1. Entrez la valeur `WEBSITE_RUN_FROM_PACKAGE` comme **Nom**, puis collez l’URL de votre package dans Stockage Blob comme **Valeur**.

1. Sélectionnez **OK**. Ensuite, sélectionnez **Enregistrer** > **Continuer** pour enregistrer le paramètre et redémarrer l’application.

Vous pouvez maintenant exécuter votre fonction dans Azure pour vérifier que le déploiement a réussi à l’aide du fichier .zip du package de déploiement.

## <a name="troubleshooting"></a>Dépannage

- L’option d’exécution à partir d’un package rend `wwwroot` accessible en lecture seule. Donc, vous recevez une erreur lors de l’écriture de fichiers dans ce répertoire.
- Les formats TAR et GZIP ne sont pas pris en charge.
- Le fichier ZIP peut être au maximum de 1 Go.
- Cette fonctionnalité ne tient pas compte du cache local.
- Pour améliorer les performances de démarrage à froid, utilisez l’option zip locale (`WEBSITE_RUN_FROM_PACKAGE`= 1).
- L’option d’exécution à partir d’un package n’est pas compatible avec l’option de personnalisation du déploiement (`SCM_DO_BUILD_DURING_DEPLOYMENT=true`), l’étape de génération est donc ignorée pendant le déploiement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déploiement continu pour Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
