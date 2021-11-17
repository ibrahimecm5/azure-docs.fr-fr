---
title: Commandes et vue d'ensemble de l'interface CLI Bicep
description: Décrit les commandes que vous pouvez utiliser dans l'interface CLI Bicep. Ces commandes incluent la création de modèles Azure Resource Manager à partir de Bicep.
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: bbb9bbb0f960cac27cba794da410842b80356fb6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551935"
---
# <a name="bicep-cli-commands"></a>Commandes de l'interface CLI Bicep

Cet article décrit les commandes que vous pouvez utiliser dans l'interface CLI Bicep. L'[interface CLI Bicep](./install.md) doit être installée pour pouvoir exécuter les commandes.

Cet article explique comment exécuter les commandes dans Azure CLI. Si vous n'utilisez pas Azure CLI, exécutez les commandes sans `az` au début de celle-ci. Par exemple, `az bicep version` devient ``bicep version``.

## <a name="build"></a>build

La commande `build` convertit un fichier Bicep en modèle Azure Resource Manager (modèle ARM). En général, vous n'avez pas besoin d'exécuter cette commande car elle s'exécute automatiquement lorsque vous déployez un fichier Bicep. Exécutez-la manuellement lorsque vous souhaitez voir le modèle ARM JSON créé à partir de votre fichier Bicep.

L'exemple suivant convertit un fichier Bicep nommé _main.bicep_ en modèle ARM nommé _main.json_. Le nouveau fichier est créé dans le même répertoire que le fichier Bicep.

```azurecli
az bicep build --file main.bicep
```

L'exemple suivant enregistre le fichier _main.json_ dans un autre répertoire.

```azurecli
az bicep build --file main.bicep --outdir c:\jsontemplates
```

L'exemple suivant spécifie le nom et l'emplacement du fichier à créer.

```azurecli
az bicep build --file main.bicep --outfile c:\jsontemplates\azuredeploy.json
```

Pour imprimer le fichier sur `stdout`, utilisez :

```azurecli
az bicep build --file main.bicep --stdout
```

Si votre fichier Bicep comprend un module qui fait référence à un registre externe, la commande de génération appelle automatiquement [restore](#restore). La commande restore récupère le fichier à partir du registre et le stocke dans le cache local.

Pour ne pas appeler la commande restore automatiquement, utilisez le commutateur `--no-restore` :

```azurecli
az bicep build --no-restore <bicep-file>
```

Le processus de génération avec le commutateur `--no-restore` échoue si l’un des modules externes n’est pas déjà mis en cache :

```error
The module with reference "br:exampleregistry.azurecr.io/bicep/modules/storage:v1" has not been restored.
```

Quand vous recevez cette erreur, vous devez exécuter la commande `build` sans le commutateur `--no-restore` ou exécuter `bicep restore` en premier.

Pour utiliser le commutateur `--no-restore`, vous devez disposer de Bicep CLI version **0.4.1008 ou ultérieure**.

## <a name="decompile"></a>decompile

La commande `decompile` convertit le modèle ARM JSON en fichier Bicep.

```azurecli
az bicep decompile --file main.json
```

Pour plus d’informations sur l'utilisation de cette commande, consultez [Décompiler le modèle ARM JSON en Bicep](decompile.md).

## <a name="install"></a>installer

La commande `install` ajoute l'interface CLI Bicep à votre environnement local. Pour plus d'informations, consultez [Installer les outils Bicep](install.md).

Pour installer la version la plus récente, utilisez :

```azurecli
az bicep install
```

Pour installer une version spécifique :

```azurecli
az bicep install --version v0.3.255
```

## <a name="list-versions"></a>list-versions

La commande `list-versions` renvoie toutes les versions disponibles de l'interface CLI Bicep. Utilisez cette commande pour savoir si vous devez [mettre à niveau](#upgrade) ou [installer](#install) une nouvelle version.

```azurecli
az bicep list-versions
```

La commande renvoie le tableau des versions disponibles.

```azurecli
[
  "v0.4.1",
  "v0.3.539",
  "v0.3.255",
  "v0.3.126",
  "v0.3.1",
  "v0.2.328",
  "v0.2.317",
  "v0.2.212",
  "v0.2.59",
  "v0.2.14",
  "v0.2.3",
  "v0.1.226-alpha",
  "v0.1.223-alpha",
  "v0.1.37-alpha",
  "v0.1.1-alpha"
]
```

## <a name="publish"></a>Publier

La commande `publish` ajoute un module à un registre. Le registre de conteneurs Azure doit exister et le compte qui publie dans le registre doit disposer des autorisations appropriées. Pour plus d’informations sur la configuration d’un registre de modules, consultez [Utiliser un registre privé pour les modules Bicep](private-module-registry.md).

Après avoir publié le fichier dans le registre, vous pouvez le [référencer dans un module](modules.md#file-in-registry).

Pour utiliser la commande publish, vous devez disposer de Bicep CLI version **0.4.1008 ou ultérieure**.

Pour publier un module dans un registre, utilisez :

```azurecli
az bicep publish <bicep-file> --target br:<registry-name>.azurecr.io/<module-path>:<tag>
```

Par exemple :

```azurecli
az bicep publish storage.bicep --target br:exampleregistry.azurecr.io/bicep/modules/storage:v1
```

La commande `publish` ne reconnaît pas les alias que vous avez définis dans un fichier [bicepconfig.json](bicep-config-modules.md). Indiquez le chemin d’accès complet au module.

> [!WARNING]
> La publication sur la même cible remplace l’ancien module. Nous vous recommandons d’incrémenter la version lors de la mise à jour.

## <a name="restore"></a>restauration

Lorsque votre fichier Bicep utilise des modules qui sont publiés dans un registre, la commande `restore` obtient des copies de tous les modules requis à partir du registre. Elle stocke ces copies dans un cache local. Un fichier Bicep ne peut être généré que lorsque les fichiers externes sont disponibles dans le cache local. En règle générale, vous n’avez pas besoin d’exécuter `restore`, car cette commande est appelée automatiquement par `build`.

Pour utiliser la commande restore, vous devez disposer de Bicep CLI version **0.4.1008 ou ultérieure**.

Pour restaurer manuellement les modules externes d’un fichier, utilisez :

```azurecli
az bicep restore <bicep-file>
```

Le fichier Bicep que vous fournissez est le fichier que vous souhaitez déployer. Il doit contenir un module qui établit un lien vers un registre. Par exemple, vous pouvez restaurer le fichier suivant :

```bicep
module stgModule 'br:exampleregistry.azurecr.io/bicep/modules/storage:v1' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: 'examplestg1'
  }
}
```

Le cache local se trouve sous :

```path
%USERPROFILE%\.bicep\br\<registry-name>.azurecr.io\<module-path\<tag>
```

## <a name="upgrade"></a>upgrade

La commande `upgrade` procède à la mise à jour de la version installée vers la dernière version.

```azurecli
az bicep upgrade
```

## <a name="version"></a>version

La commande `version` renvoie la version installée.

```azurecli
az bicep version
```

La commande indique le numéro de version.

```azurecli
Bicep CLI version 0.4.1 (e2387595c9)
```

Si vous n'avez pas installé l'interface CLI Bicep, un message d'erreur apparaît pour indiquer que celle-ci est introuvable.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement d'un fichier Bicep, consultez :

* [Azure CLI](deploy-cli.md)
* [Cloud Shell](deploy-cloud-shell.md)
* [PowerShell](deploy-powershell.md)
