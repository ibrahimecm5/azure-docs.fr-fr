---
title: Résoudre les problèmes liés aux images d’environnement
titleSuffix: Azure Machine Learning
description: Découvrez comment résoudre les problèmes liés à la génération d’images d’environnement et à l’installation de packages.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: saachigopal
ms.author: sagopal
ms.date: 10/21/2021
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: 75f79ca277a017cce8b506e423faaf2e49485686
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137555"
---
# <a name="troubleshoot-environment-image-builds"></a>Résoudre les problèmes liés à la génération d’images d’environnement

Découvrez comment résoudre les problèmes liés à la génération d’images d’environnement et à l’installation de packages Docker.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).
* Le [Kit de développement logiciel (SDK) Azure Machine Learning](/python/api/overview/azure/ml/install).
* [Interface de ligne de commande Azure](/cli/azure/install-azure-cli).
* [Extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Pour déboguer localement, vous devez avoir une installation opérationnelle de Docker sur votre système local.

## <a name="docker-image-build-failures"></a>Échecs de génération d’images Docker
 
Pour la plupart des échecs de génération d’images, vous trouverez la cause racine dans le journal de génération d’images.
Le journal de génération d’images se trouve sur le portail Azure Machine Learning (20\_image\_build\_log.txt) ou dans vos journaux d’exécution de tâches Azure Container Registry.
 
Il est généralement plus facile de reproduire les erreurs localement. Vérifiez le type d’erreur et essayez l’un des `setuptools` suivants :

- Installez une dépendance conda localement : `conda install suspicious-dependency==X.Y.Z`.
- Installez une dépendance pip localement : `pip install suspicious-dependency==X.Y.Z`.
- Essayez de matérialiser l’environnement entier :`conda create -f conda-specification.yml`.

> [!IMPORTANT]
> Assurez-vous que la plateforme et l’interpréteur sur votre cluster de calcul correspondent à ceux sur le cluster de calcul distant. 

### <a name="timeout"></a>Délai d'expiration 
 
Les problèmes de réseau suivants peuvent occasionner des erreurs de délai d’attente :

- Bande passante Internet faible
- Problèmes de serveur
- Dépendances de grande taille qui ne peuvent pas être téléchargée avec les paramètres donnés de délai d’expiration conda et pip
 
Des messages similaires aux exemples suivants indiquent le problème :
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Si vous recevez un message d’erreur, essayez l’une des solutions possibles suivantes :
 
- Essayez une autre source, telle que des miroirs, le Stockage Blob Azure ou d’autres flux Python, pour la dépendance.
- Mettez à jour conda ou pip. Si vous utilisez un fichier Docker personnalisé, mettez à jour les paramètres de délai d’attente.
- Certaines versions de pip présentent des problèmes connus. Envisagez d’ajouter une version spécifique de pip aux dépendances d’environnement.

### <a name="package-not-found"></a>Package introuvable

Les erreurs suivantes sont les plus courantes pour les échecs de génération d’images :

- Le package conda est introuvable :

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- La version ou le package pip spécifiés sont introuvables :

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Dépendance pip imbriquée incorrecte :

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Vérifiez que le package existe sur les sources spécifiées. Utilisez [pip search](https://pip.pypa.io/en/stable/reference/pip_search/) pour vérifier les dépendances pip :

- `pip search azureml-core`

Pour les dépendances conda, utilisez [conda search](https://docs.conda.io/projects/conda/en/latest/commands/search.html) :

- `conda search conda-forge::numpy`

Pour voir plus d'options, essayez :
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Notes du programme d’installation

Assurez-vous que la distribution requise existe pour la plateforme spécifiée et la version de l’interpréteur Python.

Pour les dépendances pip, accédez à `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` pour voir si la version requise est disponible. Accédez à la page https://pypi.org/project/azureml-core/1.11.0/#files pour voir un exemple.

Pour les dépendances conda, vérifiez le package sur le référentiel du canal.
Pour les canaux gérés par Anaconda, Inc., vérifiez la [page sur les packages Anaconda](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Mise à jour du package pip

Pendant l’installation ou la mise à jour d’un package pip, le programme de résolution peut avoir besoin de mettre à jour un package déjà installé pour répondre aux nouvelles exigences.
La désinstallation peut échouer pour diverses raisons liées à la version de pip ou à la façon dont la dépendance a été installée.
Le scénario le plus courant est qu’une dépendance installée par conda n’a pas pu être désinstallée par pip.
Pour ce scénario, envisagez de désinstaller la dépendance à l’aide de `conda remove mypackage`.

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problèmes liés au programme d’installation

Certaines versions du programme d’installation présentent des problèmes dans les programmes de résolution de package, qui peuvent entraîner l’échec de la génération.

Si vous utilisez une image de base personnalisée ou un Dockerfile, nous vous recommandons d’utiliser conda version 4.5.4 ou supérieure.

Un package pip est requis pour installer les dépendances pip. Si aucune version n’est spécifiée dans l’environnement, la version la plus récente est utilisée.
Nous vous recommandons d’utiliser une version connue de pip pour éviter des problèmes temporaires ou des changements cassants que la dernière version de l’outil pourrait apporter.

Envisagez d’épingler la version de pip dans votre environnement si vous voyez le message suivant :

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Erreur du sous-processus pip :
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

L’installation de pip peut être bloquée dans une boucle infinie en cas de conflits insolubles dans les dépendances. Si vous travaillez localement, rétrogradez la version de pip vers une version antérieure à 20.3. Dans un environnement conda créé à partir d’un fichier YAML, vous rencontrerez ce problème uniquement si conda-forge est le canal avec la priorité la plus élevée. Pour atténuer le problème, spécifiez explicitement pip < 20.3 (épingle !=20.3 ou =20.2.4 vers une autre version) en tant que dépendance conda dans le fichier de spécification conda.

### <a name="modulenotfounderror-no-module-named-distutilsdir_util"></a>ModuleNotFoundError : Aucun module nommé « distutils.dir_util »

Quand vous configurez votre environnement, vous pouvez parfois rencontrer le problème **ModuleNotFoundError : Aucun module nommé « distutils.dir_util »** . Pour le résoudre, exécutez la commande suivante :

```bash
apt-get install -y --no-install-recommends python3 python3-distutils && \
ln -sf /usr/bin/python3 /usr/bin/python
```

Quand vous utilisez un fichier Dockerfile, exécutez-le dans le cadre d’une commande RUN.

```dockerfile
RUN apt-get update && \
  apt-get install -y --no-install-recommends python3 python3-distutils && \
  ln -sf /usr/bin/python3 /usr/bin/python
```

L’exécution de cette commande installe les dépendances de module appropriées pour configurer votre environnement. 

### <a name="build-failure-when-using-spark-packages"></a>Échec de génération lors de l’utilisation de packages Spark

Configurez l’environnement de façon à ne pas précacher les packages. 

```python
env.spark.precache_packages = False
```

## <a name="service-side-failures"></a>Échecs côté service

Consultez les scénarios suivants pour résoudre d’éventuels échecs côté service.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>Vous ne parvenez pas à extraire une image d’un registre de conteneurs, ou l’adresse n’a pas pu être résolue pour un registre de conteneurs

Problèmes possibles :
- Le nom du chemin d'accès au registre de conteneurs n’est peut-être pas résolu correctement. Vérifiez que les noms d’images utilisent des doubles barres obliques et que la direction des barres obliques sur les hôtes Linux et Windows est correcte.
- Si un registre de conteneurs situé derrière un réseau virtuel utilise un point de terminaison privé dans [une région non prise en charge](../private-link/private-link-overview.md#availability), configurez le registre de conteneurs à l’aide du point de terminaison de service (accès public) du portail et réessayez.
- Une fois que vous avez placé le registre de conteneurs derrière un réseau virtuel, exécutez le [modèle Azure Resource Manager](./how-to-network-security-overview.md) pour que l’espace de travail puisse communiquer avec l’instance de registre de conteneurs.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Vous recevez une erreur 401 d’un registre de conteneurs d’espace de travail

Resynchronisez les clés de stockage à l’aide de la commande [ws.sync_keys()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--).

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>L’environnement continue à générer l’erreur « En attente de la fin d’autres opérations Conda… »

Quand une génération d’image est en cours, conda est verrouillé par le client du kit SDK. Si le processus s’est arrêté ou a été annulé de manière incorrecte par l’utilisateur, conda reste à l’état verrouillé. Pour résoudre ce problème, supprimez manuellement le fichier de verrouillage. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>Votre image Docker personnalisée ne figure pas dans le registre

Vérifiez si l’[étiquette correcte](./how-to-use-environments.md#create-an-environment) est utilisée et que `user_managed_dependencies = True`. `Environment.python.user_managed_dependencies = True` désactive conda et utilise les packages installés de l’utilisateur.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>Vous recevez l’un des problèmes de réseau virtuel courants suivants

- Vérifiez que le compte de stockage, le cluster de calcul et le registre de conteneurs se trouvent tous dans le même sous-réseau du réseau virtuel.
- Lorsque votre registre de conteneurs se trouve derrière un réseau virtuel, il ne peut pas être utilisé directement pour générer des images. Vous devez utiliser le cluster de calcul pour générer des images.
- Il peut être nécessaire de placer le stockage derrière un réseau virtuel dans les cas suivants :
    - Vous utilisez une inférence ou une roue privée.
    - Vous rencontrez des erreurs de service 403 (non autorisé).
    - Vous ne pouvez pas accéder aux détails de l’image à partir d’Azure Container Registry.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>La génération d’image échoue quand vous tentez d’accéder au stockage protégé du réseau

- Les tâches de Container Registry ne fonctionnent pas derrière un réseau virtuel. Si l’utilisateur a son registre de conteneurs derrière un réseau virtuel, il doit utiliser le cluster de calcul pour générer une image.
- Le stockage doit se trouver derrière un réseau virtuel pour pouvoir en extraire les dépendances.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>Vous ne pouvez pas exécuter des expériences lorsque la sécurité réseau est activée pour le stockage

Si vous utilisez les images Docker par défaut et activez les dépendances gérées par l’utilisateur, utilisez les [étiquettes de service](./how-to-network-security-overview.md) MicrosoftContainerRegistry et AzureFrontDoor.FirstParty pour mettre en liste verte Azure Container Registry et ses dépendances.

 Pour plus d'informations, consultez [Activation de réseaux virtuels](./how-to-network-security-overview.md).


## <a name="next-steps"></a>Étapes suivantes

- [Entraîner un modèle Machine Learning pour catégoriser les fleurs](how-to-train-scikit-learn.md)
- [Effectuer l'apprentissage d’un modèle Machine Learning à l’aide d’une image Docker personnalisée](how-to-train-with-custom-image.md)
