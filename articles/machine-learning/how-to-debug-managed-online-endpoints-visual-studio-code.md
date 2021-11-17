---
title: Déboguer les points de terminaison en ligne localement dans VS Code (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser Visual Studio Code pour tester et déboguer les points de terminaison en ligne localement avant de les déployer sur Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: luisquintanilla
ms.author: luquinta
ms.date: 11/03/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 33b46c490912d0c651c5629da17795efe074b6d9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566721"
---
# <a name="debug-online-endpoints-locally-in-visual-studio-code-preview"></a>Déboguer les points de terminaison en ligne localement dans Visual Studio Code (préversion)

Découvrez comment utiliser le débogueur Visual Studio Code (VS Code) pour tester et déboguer les points de terminaison en ligne localement avant de les déployer sur Azure.

Les points de terminaison locaux Azure Machine Learning vous aident à tester et à déboguer votre script de scoring, la configuration de l’environnement, la configuration du code et le modèle Machine Learning localement.

## <a name="online-endpoint-local-debugging"></a>Débogage local du point de terminaison en ligne

Le débogage des points de terminaison localement avant le déploiement dans le cloud peut vous aider à détecter plus tôt les erreurs dans votre code et votre configuration. Vous disposez de différentes options pour déboguer les points de terminaison localement avec VS Code.

- [Serveur HTTP d’inférence Azure Machine Learning (préversion)](how-to-inference-server-http.md)
- Point de terminaison local

Ce guide se concentre sur les points de terminaison locaux.

Le tableau suivant fournit une vue d’ensemble des scénarios pour vous aider à choisir la solution la mieux adaptée.

| Scénario | Serveur HTTP d’inférence | Point de terminaison local |
|--|--|--|
| Mettre à jour l’environnement Python local, **sans** régénération d’image Docker | Oui | Non |
| Mettre à jour le script de scoring | Oui | Oui |
| Mettre à jour les configurations de déploiement (déploiement, environnement, code, modèle) | Non | Oui |
| Intégration du débogueur VS Code | Oui | Oui |

## <a name="prerequisites"></a>Prérequis

Ce guide suppose que les éléments suivants sont installés localement sur votre PC.

- [Docker](https://docs.docker.com/engine/install/)
- [Code Visual Studio](https://code.visualstudio.com/#alt-downloads)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Extension Azure CLI`ml` ](how-to-configure-cli.md)

Pour plus d’informations, consultez le guide expliquant [comment préparer votre système pour déployer des points de terminaison en ligne gérés](how-to-deploy-managed-online-endpoints.md#prepare-your-system).

Les exemples de cet article sont basés sur des exemples de code contenus dans le référentiel [azureml-examples](https://github.com/azure/azureml-examples). Pour exécuter les commandes localement sans avoir à copier/coller le fichier YAML et d’autres fichiers, clonez le référentiel, puis remplacer les répertoires par le répertoire `cli` dans le référentiel :

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples
cd cli
```

Si vous n’avez pas encore défini les paramètres par défaut pour l’interface CLI Azure, enregistrez vos paramètres par défaut. Pour éviter de transmettre plusieurs fois les valeurs de votre abonnement, de votre espace de travail et de votre groupe de ressources, utilisez les commandes suivantes. Remplacez les paramètres suivants par des valeurs pour votre configuration spécifique :

* Remplacez `<subscription>` par l’identifiant de votre abonnement Azure.
* Remplacez `<workspace>` par le nom de votre espace de travail Azure Machine Learning.
* Remplacez `<resource-group>` par le groupe de ressources Azure qui contient votre espace de travail.
* Remplacez `<location>` par la région Azure qui contient votre espace de travail.

> [!TIP]
> Vous pouvez afficher les valeurs par défaut actuelles à l’aide de la commande `az configure -l`.

```azurecli
az account set --subscription <subscription>
az configure --defaults workspace=<workspace> group=<resource-group> location=<location>
```

## <a name="launch-development-container"></a>Lancer le conteneur de développement

Les points de terminaison locaux Azure Machine Learning utilisent les conteneurs de développement Docker et VS Code (conteneur de développement) pour générer et configurer un environnement de débogage local. Avec les conteneurs de développement, vous pouvez tirer parti des fonctionnalités de VS Code au sein d’un conteneur Docker. Pour plus d’informations sur les conteneurs de développement, consultez [Créer un conteneur de développement](https://code.visualstudio.com/docs/remote/create-dev-container).

Pour déboguer les points de terminaison en ligne localement dans VS Code, utilisez l’indicateur `--vscode-debug` lors de la création ou de la mise à jour du déploiement en ligne Azure Machine Learning. La commande suivante utilise un exemple de déploiement issu du référentiel des exemples :

```azurecli
az ml online-deployment create --file endpoints/online/managed/sample/blue-deployment.yml --local --vscode-debug
```

> [!IMPORTANT]
> Sur un sous-système Windows pour Linux (WSL), vous devez mettre à jour votre variable d’environnement PATH pour inclure le chemin d’accès à l’exécutable VS Code ou utiliser l’interop WSL. Pour plus d’informations, consultez [Interopérabilité Windows avec Linux](/windows/wsl/interop).

Une image Docker est générée localement. Toute erreur de configuration d’environnement ou de fichier de modèle est exposée à cette étape du processus.

> [!NOTE]
> La première fois que vous lancez un conteneur de développement nouveau ou mis à jour, l’opération peut prendre plusieurs minutes.

Une fois que l’image est correctement générée, votre conteneur de développement s’ouvre dans une fenêtre VS Code.

Vous allez utiliser quelques extensions VS Code pour déboguer vos déploiements dans le conteneur de développement. Azure Machine Learning installe automatiquement ces extensions dans votre conteneur de développement.

- Débogage d’inférence
- [Pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance)
- [Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter)
- [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

> [!IMPORTANT]
> Avant de démarrer votre session de débogage, assurez-vous que les extensions VS Code ont terminé de s’installer dans votre conteneur de développement.  

## <a name="start-debug-session"></a>Démarrer la session de débogage

Une fois que votre environnement est configuré, utilisez le débogueur VS Code pour tester et déboguer votre déploiement localement.

1. Ouvrez votre script de scoring dans Visual Studio Code.

    > [!TIP]
    > Le script score.py utilisé par le point de terminaison déployé précédemment se trouve à l’emplacement `azureml-samples/cli/endpoints/online/managed/sample/score.py` dans le référentiel que vous avez cloné. Toutefois, les étapes de ce guide fonctionnent pour tout script de scoring.

1. Définissez un point d’arrêt n’importe où dans votre script de scoring.

    - Pour déboguer le comportement de démarrage, placez votre ou vos points d’arrêt à l’intérieur de la fonction `init`.
    - Pour déboguer le comportement de scoring, placez votre ou vos points d’arrêt à l’intérieur de la fonction `run`.

1. Sélectionnez la vue Exécuter VS Code.
1. Dans la liste déroulante Exécuter et déboguer, sélectionnez **Azure ML : déboguer le point de terminaison local** pour démarrer le débogage de votre point de terminaison localement.

    Dans la section **Points d’arrêt** de la vue Exécuter, vérifiez les éléments suivants :

    - Les **Exceptions levées** sont **désactivées**
    - Les **Exceptions interceptées** sont **activées**

    :::image type="content" source="media/how-to-debug-managed-online-endpoints-visual-studio-code/configure-debug-profile.png" alt-text="Configurer le profil de débogage d’environnement local Azure ML":::

1. Sélectionnez l’icône de lecture à côté de la liste déroulante Exécuter et déboguer pour démarrer votre session de débogage.

    À ce stade, tous les points d’arrêt de votre fonction `init` sont interceptés. Utilisez les actions de débogage pour parcourir votre code. Pour plus d’informations sur les actions de débogage, consultez le [Guide des actions de débogage](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Pour plus d’informations sur le débogueur VS Code, consultez [Débogage dans VS Code](https://code.visualstudio.com/Docs/editor/debugging).

## <a name="debug-your-endpoint"></a>Déboguer votre point de terminaison

Maintenant que votre application s’exécute dans le débogueur, essayez d’effectuer une prédiction pour déboguer votre script de scoring.

Utilisez la commande `invoke` d’extension `ml` pour effectuer une requête à votre point de terminaison local.

```azurecli
az ml online-endpoint invoke --name <ENDPOINT-NAME> --request-file <REQUEST-FILE> --local
```

Dans ce cas, `<REQUEST-FILE>` est un fichier JSON qui contient des exemples de données d’entrée pour que le modèle effectue des prédictions similaires au code JSON suivant :

```json
{"data": [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]}
```

> [!TIP]
> L’URI de scoring correspond à l’adresse où votre point de terminaison écoute les demandes. Utilisez l’extension `ml` pour récupérer l’URI de scoring.
>
>    ```azurecli
>    az ml online-endpoint show --name <ENDPOINT-NAME> --local
>    ```
>
> Le résultat doit être semblable à ce qui suit :
>
> ```json
> {
>  "auth_mode": "aml_token",
>  "location": "local",
>  "name": "my-new-endpoint",
>  "properties": {},
>  "provisioning_state": "Succeeded",
>  "scoring_uri": "http://localhost:5001/score",
>  "tags": {},
>  "traffic": {},
>  "type": "online"
>}
>```
>
>L’URI de scoring se trouve dans la propriété `scoring_uri`.

À ce stade, tous les points d’arrêt de votre fonction `run` sont interceptés. Utilisez les actions de débogage pour parcourir votre code. Pour plus d’informations sur les actions de débogage, consultez le [Guide des actions de débogage](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

## <a name="edit-your-endpoint"></a>Modifier votre point de terminaison

Quand vous déboguez et dépannez votre application, il existe des scénarios dans lesquels vous devez mettre à jour votre script de scoring et vos configurations.

Pour appliquer les modifications à votre code :

1. Mettre à jour votre code
1. Redémarrez votre session de débogage à l’aide de la commande `Developer: Reload Window` dans la palette de commandes. Pour plus d’informations, consultez la [documentation sur la palette de commandes](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette).

> [!NOTE]
> Étant donné que le répertoire contenant votre code et vos ressources de point de terminaison est monté sur le conteneur de développement, toutes les modifications que vous effectuez dans le conteneur de développement sont synchronisées avec votre système de fichiers local.

Pour effectuer des modifications plus approfondies concernant les mises à jour de la configuration de votre environnement et de vos points de terminaison, utilisez la commande `update` d’extension `ml`. Cette action déclenche une reconstruction d’image complète avec vos modifications.

```azurecli
az ml online-deployment update --file <DEPLOYMENT-YAML-SPECIFICATION-FILE> --local --vscode-debug
```

Une fois que l’image mise à jour est générée et que votre conteneur de développement est lancé, utilisez le débogueur VS Code pour tester et résoudre les problèmes liés à votre point de terminaison mis à jour.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer et scorer un modèle Machine Learning en utilisant un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md)
- [Résolution des problèmes de déploiement et de scoring de points de terminaison en ligne managés (préversion)](how-to-troubleshoot-managed-online-endpoints.md)
