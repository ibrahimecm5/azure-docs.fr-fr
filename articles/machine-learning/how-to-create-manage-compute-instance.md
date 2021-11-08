---
title: Créer et gérer une instance de calcul
titleSuffix: Azure Machine Learning
description: Découvrez comment créer et gérer une instance de calcul Azure Machine Learning. Utilisez-la comme environnement de développement, ou comme cible de calcul à des fins de développement/test.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli, references_regions
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/21/2021
ms.openlocfilehash: 8515ad8c9a3db24c8504094cf7273cd149b7ab2d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559178"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Créer et gérer une instance de calcul Azure Machine Learning

Découvrez comment créer et gérer une [instance de calcul](concept-compute-instance.md) dans votre espace de travail Azure Machine Learning.

Utilisez une instance de calcul comme environnement de développement complètement configuré et managé dans le cloud. Pour le développement et le test, vous pouvez également utiliser l’instance en tant que [cible de calcul d’entraînement](concept-compute-target.md#train) ou pour une [cible d’inférence](concept-compute-target.md#deploy).   Une instance de calcul peut exécuter plusieurs travaux en parallèle et dispose d’une file d’attente de travaux. En tant qu’environnement de développement, une instance de calcul ne peut pas être partagée avec d’autres utilisateurs dans votre espace de travail.

Dans cet article, vous apprendrez comment :

* [Créer](#create) une instance de calcul
* [Gérer](#manage) (démarrer, arrêter, redémarrer, supprimer) une instance de calcul
* [Créer une planification](#schedule) pour démarrer et arrêter automatiquement l’instance de calcul (préversion)
* [Utiliser un script de configuration](#setup-script) pour personnaliser et configurer l’instance de calcul

Les instances de calcul peuvent exécuter des travaux en toute sécurité dans un [environnement de réseau virtuel](how-to-secure-training-vnet.md), sans qu’il soit nécessaire pour les entreprises d’ouvrir des ports SSH. Le travail s’exécute dans un environnement conteneurisé et empaquette les dépendances de votre modèle dans un conteneur Docker.

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* L’[extension Azure CLI pour Machine Learning service](reference-azure-machine-learning-cli.md), le [SDK Azure Machine Learning pour Python](/python/api/overview/azure/ml/intro) ou l’[extension Azure Machine Learning pour Visual Studio Code](how-to-setup-vs-code.md).

## <a name="create"></a>Créer

> [!IMPORTANT]
> Les éléments marqués (préversion) ci-dessous sont actuellement en préversion publique.
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Durée estimée** : 5 minutes environ.

La création d’une instance de calcul est un processus unique pour votre espace de travail. Vous pouvez réutiliser le calcul en tant que station de travail de développement ou en tant que cible de calcul pour l’entraînement. Plusieurs instances de calcul peuvent être attachées à votre espace de travail. 

Le quota de cœurs dédiés par région par famille de machine virtuelle et le quota régional total, qui s’appliquent à la création d’une instance de calcul, sont unifiés et partagés avec le quota de clusters de calcul d’entraînement Azure Machine Learning. L’arrêt de l’instance de calcul n’a pas pour effet de libérer le quota pour s’assurer que vous puissiez redémarrer l’instance de calcul. Il n’est pas possible de changer la taille de machine virtuelle de l’instance de calcul une fois celle-ci créée.

<a name="create-instance"></a> L’exemple suivant montre comment créer une instance de calcul :

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

Pour plus d’informations sur les classes, les méthodes et les paramètres utilisés dans cet exemple, consultez les documents de référence suivants :

* [Classe ComputeInstance](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Pour plus d’informations, consultez la documentation de référence sur [az ml computetarget create computeinstance](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_computeinstance).

# <a name="studio"></a>[Studio](#tab/azure-studio)

1. Accédez à [Azure Machine Learning Studio](https://ml.azure.com).
1. Sous __Gérer__, sélectionnez __Calcul__.
1. Sélectionnez **Instance de calcul** en haut.
1. Si vous n’avez aucune instance de calcul, sélectionnez **Créer** au milieu de la page.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Créer une cible de calcul":::

1. Si vous voyez une liste de ressources de calcul, sélectionnez **+ Nouveau** au-dessus de la liste.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Sélectionnez Nouveau":::
1. Remplissez le formulaire :

    |Champ  |Description  |
    |---------|---------|
    |Nom du calcul     |  <ul><li>Le nom est obligatoire et doit comprendre entre 3 et 24 caractères.</li><li>Les caractères valides sont les lettres majuscules et minuscules, les chiffres et le caractère **-** .</li><li>Le nom doit commencer par une lettre.</li><li>Le nom doit être unique parmi tous les calculs existants au sein d’une région Azure. Si le nom que vous choisissez n’est pas unique, une alerte s’affiche.</li><li>Si le caractère **-** est utilisé, il doit être suivi d’au moins une lettre plus loin dans le nom.</li></ul>     |
    |Type de machine virtuelle |  Choisissez UC ou GPU. Vous ne pouvez pas modifier ce type après la création.     |
    |Taille de la machine virtuelle     |  Il se peut que les tailles de machines virtuelles prises en charge soient restreintes dans votre région. Vérifier la [liste de disponibilité](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |

1. Sélectionnez **Créer** à moins que vous ne souhaitiez configurer des paramètres avancés pour l’instance de calcul.
1. <a name="advanced-settings"></a> Sélectionnez **Suivant : Paramètres avancés** si vous souhaitez :

    * Activer l’accès SSH.  Suivez les [instructions d’accès SSH détaillées](#enable-ssh) ci-dessous.
    * Activer le réseau virtuel. Pour créer l’instance de calcul à l’intérieur d’un réseau virtuel Azure (vnet), spécifiez le **Groupe de ressources**, le **Réseau virtuel** et le **Sous-réseau**. Vous pouvez également sélectionner __Aucune adresse IP publique__ (préversion) pour empêcher la création d’une adresse IP publique, ce qui nécessite un espace de travail de lien privé. Vous devez également satisfaire ces [exigences de réseau](./how-to-secure-training-vnet.md) pour la configuration du réseau virtuel. 
    * Affecter l’ordinateur à un autre utilisateur. Pour plus d’informations sur l’affectation à d’autres utilisateurs, consultez [Créer au nom de](#on-behalf).
    * Provisionner avec un script de configuration (préversion) : pour plus d’informations sur la création et l’utilisation d’un script de configuration, consultez [Personnalisation de l’instance de calcul avec un script](#setup-script).
    * Ajouter une planification (préversion). Planifiez le démarrage et/ou l’arrêt automatiques de l’instance de calcul. Consultez [Planifier les détails](#schedule) ci-dessous.


---

Vous pouvez également créer une instance de calcul avec un [modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance).

## <a name="enable-ssh-access"></a><a name="enable-ssh"></a> Activer l’accès SSH

L’accès SSH est désactivé par défaut.  Il n’est pas possible de changer l’accès SSH après sa création. Veillez à activer l’accès si vous envisagez de déboguer de manière interactive avec [VS Code Remote](how-to-set-up-vs-code-remote.md).  

[!INCLUDE [amlinclude-info](../../includes/machine-learning-enable-ssh.md)]

Une fois l’instance de calcul créée et en cours d’exécution, consultez [Se connecter avec un accès SSH](how-to-create-attach-compute-studio.md#ssh-access).

## <a name="create-on-behalf-of-preview"></a><a name="on-behalf"></a> Créer au nom de (préversion)

En tant qu’administrateur, vous pouvez créer une instance de calcul au nom d’un scientifique des données et lui affecter l’instance avec :

* Studio, en utilisant les [Paramètres avancés](?tabs=azure-studio#advanced-settings)

* [Modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)  Pour plus d’informations sur la façon de trouver les valeurs TenantID et ObjectID nécessaires dans ce modèle, consultez [Rechercher des ID d’objet d’identité pour la configuration de l’authentification](../healthcare-apis/azure-api-for-fhir/find-identity-object-ids.md).  Vous pouvez également trouver ces valeurs dans le portail Azure Active Directory.

* API REST

Le scientifique des données pour lequel vous créez l’instance de calcul doit disposer des [autorisations de contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md) :

* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*
* *Microsoft.MachineLearningServices/workspaces/computes/updateSchedules/action*

Le scientifique des données peut démarrer, arrêter et redémarrer l’instance de calcul. Il peut utiliser l’instance de calcul pour :
* Jupyter
* JupyterLab
* RStudio
* Notebooks intégrés

## <a name="schedule-automatic-start-and-stop-preview"></a><a name="schedule"></a> Planifier le démarrage et l’arrêt automatiques (préversion)

Définissez plusieurs planifications pour l’arrêt automatique et le démarrage automatique. Par exemple, créez une planification pour démarrer à 9h00 et arrêter à 18h00 du lundi au jeudi, et une deuxième planification pour démarrer à 9h00 et arrêter à 16h00 le vendredi.  Vous pouvez créer un total de quatre planifications par instance de calcul.

Vous pouvez également définir des planifications pour [créer des instances de calcul au nom d’autrui](#on-behalf). Vous pouvez créer une planification pour créer une instance de calcul dans un état arrêté. Cela s’avère particulièrement utile quand un utilisateur crée une instance de calcul au nom d’un autre utilisateur.

### <a name="create-a-schedule-in-studio"></a>Créer une planification dans le studio

1. [Remplissez le formulaire](?tabs=azure-studio#create-instance).
1. Dans la deuxième page du formulaire, ouvrez **Afficher les paramètres avancés**.
1. Sélectionnez **Ajouter une planification** pour ajouter une nouvelle planification.

    :::image type="content" source="media/how-to-create-attach-studio/create-schedule.png" alt-text="Capture d’écran : ajouter une planification dans les paramètres avancés.":::

1. Sélectionnez **Démarrer l’instance de calcul** ou **Arrêter l’instance de calcul**.
1. Sélectionnez le **Fuseau horaire**.
1. Sélectionnez l’**Heure de début** ou l’**Heure de l’arrêt**.
1. Sélectionnez les jours où cette planification est active.

    :::image type="content" source="media/how-to-create-attach-studio/stop-compute-schedule.png" alt-text="Capture d’écran : planifier l’arrêt d’une instance de calcul.":::

1. Resélectionnez **Ajouter une planification** si vous souhaitez créer une autre planification.

Une fois l’instance de calcul créée, vous pouvez l’afficher, la modifier ou ajouter de nouvelles planifications à partir de la section des détails de l’instance de calcul.
Notez que les étiquettes de fuseau horaire ne tiennent pas compte de l’heure d’été. Par exemple, (UTC+01:00) Amsterdam, Berlin, Berne, Rome, Stockholm, Vienne est en fait UTC+02:00 pendant les heures d’été.

### <a name="create-a-schedule-with-a-resource-manager-template"></a>Créer une planification avec un modèle Resource Manager

Vous pouvez planifier le démarrage et l’arrêt automatiques d’une instance de calcul avec un [modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance) Resource Manager.

Dans le modèle Resource Manager, ajoutez :

```
"schedules": "[parameters('schedules')]"
```

Utilisez ensuite les expressions cron ou LogicApps pour définir la planification qui démarre ou arrête l’instance dans votre fichier de paramètres :
 
```json
        "schedules": {
        "value": {
        "computeStartStop": [
          {
            "triggerType": "Cron",
            "cron": {              
              "timeZone": "UTC",
              "expression": "0 18 * * *"
            },
            "action": "Stop",
            "status": "Enabled"
          },
          {
            "triggerType": "Cron",
            "cron": {              
              "timeZone": "UTC",
              "expression": "0 8 * * *"
            },
            "action": "Start",
            "status": "Enabled"
          },
          { 
            "triggerType": "Recurrence", 
            "recurrence": { 
              "frequency": "Day", 
              "interval": 1, 
              "timeZone": "UTC", 
              "schedule": { 
                "hours": [17], 
                "minutes": [0]
              } 
            }, 
            "action": "Stop", 
            "status": "Enabled" 
          } 
        ]
      }
    }
```

* L’action peut avoir la valeur « Start » ou « Stop ».
* Pour le type de déclencheur `Recurrence`, utilisez la même syntaxe que l’application logique, avec ce [schéma de périodicité](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).
* Pour le type de déclencheur `cron`, utilisez la syntaxe cron standard :  

    ```cron
    // Crontab expression format: 
    // 
    // * * * * * 
    // - - - - - 
    // | | | | | 
    // | | | | +----- day of week (0 - 6) (Sunday=0) 
    // | | | +------- month (1 - 12) 
    // | | +--------- day of month (1 - 31) 
    // | +----------- hour (0 - 23) 
    // +------------- min (0 - 59) 
    // 
    // Star (*) in the value field above means all legal values as in 
    // braces for that column. The value column can have a * or a list 
    // of elements separated by commas. An element is either a number in 
    // the ranges shown above or two numbers in the range separated by a 
    // hyphen (meaning an inclusive range). 
    ```
### <a name="azure-policy-support-to-default-a-schedule"></a>Azure Policy la prise en charge d’une planification par défaut
Utilisez Azure Policy afin d’appliquer une planification d’arrêt qui existe pour chaque instance de calcul dans un abonnement ou d’utiliser une planification par défaut.
Voici un exemple de politique pour définir par défaut un horaire d'arrêt à 22 heures PST.
```json
{
    "mode": "All",
    "policyRule": {
     "if": {
      "allOf": [
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/computeType",
        "equals": "ComputeInstance"
       },
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/schedules",
        "exists": "false"
       }
      ]
     },
     "then": {
      "effect": "append",
      "details": [
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/schedules",
        "value": {
         "computeStartStop": [
          {
           "triggerType": "Cron",
           "cron": {
            "startTime": "2021-03-10T21:21:07",
            "timeZone": "Pacific Standard Time",
            "expression": "0 22 * * *"
           },
           "action": "Stop",
           "status": "Enabled"
          }
         ]
        }
       }
      ]
     }
    }
}    
```

## <a name="customize-the-compute-instance-with-a-script-preview"></a><a name="setup-script"></a> Personnalisation de l’instance de calcul avec un script (préversion)

Utilisez un script de configuration pour personnaliser et configurer automatiquement l’instance de calcul au moment du provisionnement. En tant qu’administrateur, vous pouvez écrire un script de personnalisation visant à provisionner toutes les instances de calcul dans l’espace de travail en fonction de vos besoins.

Voici quelques exemples de ce que vous pouvez faire dans un script de configuration :

* Installer des packages, des outils et des logiciels
* Monter des données
* Créer un environnement Conda personnalisé et des noyaux Jupyter
* Cloner des référentiels Git et définir la configuration Git
* Définir des proxys réseau
* Définir des variables d’environnement
* Installer des extensions JupyterLab

### <a name="create-the-setup-script"></a>Création du script de configuration

Le script de configuration est un script d’interpréteur de commandes, qui s’exécute en tant que *rootuser*.  Créez ou chargez le script dans vos fichiers **Notebooks** :

1. Connectez-vous au [studio](https://ml.azure.com) et sélectionnez votre espace de travail.
2. Sur la gauche, sélectionnez **Notebooks**.
3. Utilisez l’outil **Ajouter des fichiers** pour créer ou charger votre script shell de configuration.  Veillez à ce que le nom de fichier du script se termine par « .sh ».  Lorsque vous créez un fichier, remplacez également le **Type de fichier** par *bash (.sh)* .

:::image type="content" source="media/how-to-create-manage-compute-instance/create-or-upload-file.png" alt-text="Création ou chargement d’un script de configuration dans un fichier Notebooks dans le studio.":::

Lorsque le script s’exécute, le répertoire de travail actif du script est le répertoire dans lequel il a été chargé. Par exemple, si vous chargez le script dans **Utilisateurs>Administrateur**, l’emplacement du script sur l’instance de calcul et le répertoire de travail actuel lorsque le script s’exécute est */home/azureuser/cloudfiles/code/Users/admin*. Cela vous permettrait d’utiliser des chemins d’accès relatifs dans le script.

Les arguments de script peuvent être référencés dans le script sous la forme $1, $2, etc.

Si votre script effectue une opération propre à azureuser, par exemple l’installation de l’environnement Conda ou du noyau Jupyter, vous devez le placer dans le bloc *sudo -u azureuser* comme suit.

:::code language="bash" source="~/azureml-examples-main/setup-ci/install-pip-package.sh":::

La commande *sudo -u azureuser* change le répertoire de travail actuel en */home/azureuser*. Vous ne pouvez pas non plus accéder aux arguments du script dans ce bloc.

Pour d’autres exemples de script, consultez [azureml-examples](https://github.com/Azure/azureml-examples/tree/main/setup-ci).

Vous pouvez également utiliser les variables d’environnement suivantes dans votre script :

1. CI_RESOURCE_GROUP
2. CI_WORKSPACE
3. CI_NAME
4. CI_LOCAL_UBUNTU_USER. Cela pointe vers azureuser

Vous pouvez utiliser un script de configuration conjointement avec **Azure Policy afin d’appliquer ou d’utiliser par défaut un script de configuration pour chaque création d’instance de calcul**. La valeur par défaut du délai d’expiration du script de configuration est de 15 minutes. Celle-ci peut être modifiée via l’interface utilisateur de Studio ou des modèles ARM à l’aide du paramètre DURATION.
DURATION est un nombre à virgule flottante avec un suffixe facultatif : 's' pour les secondes (valeur par défaut), 'm' pour les minutes, 'h' pour les heures ou 'd' pour les jours.

### <a name="use-the-script-in-the-studio"></a>Utilisation du script dans le studio

Une fois que vous avez stocké le script, spécifiez-le lors de la création de votre instance de calcul :

1. Connectez-vous au [studio](https://ml.azure.com/) et sélectionnez votre espace de travail.
1. Sur la gauche, sélectionnez **Calcul**.
1. Sélectionnez **+ Créer** pour créer une instance de calcul.
1. [Remplissez le formulaire](?tabs=azure-studio#create-instance).
1. Dans la deuxième page du formulaire, ouvrez **Afficher les paramètres avancés**.
1. Activez **Provisionner avec un script de configuration**.
1. Accédez au script shell que vous avez enregistré,  ou téléchargez un script à partir de votre ordinateur.
1. Ajoutez les arguments de commande nécessaires.

:::image type="content" source="media/how-to-create-manage-compute-instance/setup-script.png" alt-text="Provisionnement d’une instance de calcul avec un script de configuration dans le studio.":::

Si le stockage d’espace de travail est attaché à un réseau virtuel, vous risquez de ne pas pouvoir accéder au fichier de script d’installation, sauf si vous accédez au studio depuis le réseau virtuel.

### <a name="use-script-in-a-resource-manager-template"></a>Utilisation du script dans un modèle Resource Manager

Dans un [modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance) Resource Manager, ajoutez `setupScripts` pour appeler le script de configuration lorsque l’instance de calcul est provisionnée. Par exemple :

```json
"setupScripts":{
    "scripts":{
        "creationScript":{
        "scriptSource":"workspaceStorage",
        "scriptData":"[parameters('creationScript.location')]",
        "scriptArguments":"[parameters('creationScript.cmdArguments')]"
        }
    }
}
```
La propriété *scriptData* ci-dessus spécifie l’emplacement du script de création dans le partage de fichiers Notebooks, comme *Users/admin/testscript.sh*. *scriptArguments* est facultatif ci-dessus et spécifie les arguments pour le script de création.

Il est également possible d’insérer le script inline pour un modèle Resource Manager.  La commande shell peut faire référence à toutes les dépendances chargées dans le partage de fichiers des notebooks.  Lorsque vous utilisez une chaîne inline, le répertoire de travail du script est */mnt/batch/tasks/shared/LS_root/mounts/clusters/**ciname**/code/Users*.

Par exemple, spécifiez une chaîne de commande encodée en Base64 pour `scriptData` :

```json
"setupScripts":{
    "scripts":{
        "creationScript":{
        "scriptSource":"inline",
        "scriptData":"[base64(parameters('inlineCommand'))]",
        "scriptArguments":"[parameters('creationScript.cmdArguments')]"
        }
    }
}
```

### <a name="setup-script-logs"></a>Journaux du script de configuration

Les journaux issus de l’exécution du script de configuration s’affichent dans le dossier Journaux de la page Détails de l’instance de calcul. Ils sont stockés dans le partage de fichiers de vos notebooks dans le dossier Logs\<compute instance name>. Les arguments de fichier de script et de commande d’une instance de calcul particulière apparaissent sur la page de détails.


## <a name="manage"></a>Gérer

Démarrez, arrêtez, redémarrez et supprimez une instance de calcul. Une instance de calcul ne fait pas automatiquement l’objet d’un scale-down. Veillez donc à arrêter la ressource pour éviter les frais récurrents. L’arrêt d’une instance de calcul a pour effet de la libérer. Ensuite, redémarrez-la quand vous en avez besoin. Si l’arrêt de l’instance de calcul met fin à la facturation des heures de calcul, le disque, l’IP publique et l’équilibreur de charge standard vous seront toujours facturés. 

Vous pouvez [créer une planification](#schedule) pour que l’instance de calcul démarre et s’arrête automatiquement en fonction d’une heure et d’un jour de la semaine.

> [!TIP]
> L’instance de calcul a un disque de système d’exploitation de 120 Go. Si vous ne disposez pas de suffisamment d’espace disque, [utilisez le terminal](how-to-access-terminal.md) pour effacer au moins 1 à 2 Go avant d’arrêter ou redémarrer l’instance de calcul. N’arrêtez pas l’instance de calcul en émettant la commande sudo shutdown depuis le terminal. La taille du disque temporaire sur l’instance de calcul dépend de la taille de machine virtuelle choisie et est montée sur/mnt.

# <a name="python"></a>[Python](#tab/python)

Dans les exemples ci-dessous, le nom de l’instance de calcul est **instance**.

* Obtenir l’état

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Arrêter

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Démarrer

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Redémarrer

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Supprimer

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Dans les exemples ci-dessous, le nom de l’instance de calcul est **instance**.

* Arrêter

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Pour plus d’informations, consultez [az ml computetarget stop computeinstance](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop).

* Démarrer

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Pour plus d’informations, consultez [az ml computetarget start computeinstance](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_start).

* Redémarrer

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Pour plus d’informations, consultez [az ml computetarget restart computeinstance](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart).

* Supprimer

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Pour plus d’informations, consultez [az ml computetarget delete computeinstance](/cli/azure/ml(v1)/computetarget#az_ml_computetarget_delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="schedule"></a>

Dans votre espace de travail dans Azure Machine Learning Studio, sélectionnez **Calcul**, puis sélectionnez **Instance de calcul** en haut.

![Gérer une instance de calcul](./media/concept-compute-instance/manage-compute-instance.png)

Vous pouvez effectuer les actions suivantes :

* Créer une instance de calcul
* Actualiser l’onglet des instances de calcul.
* Démarrer, arrêter et redémarrer une instance de calcul.  Vous payez l’instance chaque fois qu’elle s’exécute. Quand vous n’utilisez pas l’instance de calcul, arrêtez-la pour réduire les coûts. L’arrêt d’une instance de calcul a pour effet de la libérer. Ensuite, redémarrez-la quand vous en avez besoin. Vous pouvez également planifier l’heure de démarrage et d’arrêt de l’instance de calcul.
* Supprimer une instance de calcul.
* Filtrer la liste des instances de calcul pour afficher uniquement celles que vous avez créées.

Pour chaque instance de calcul d’un espace de travail que vous avez créée (ou qui a été créée pour vous), vous pouvez :

* Accéder à Jupyter, JupyterLab et RStudio sur l’instance de calcul.
* Ajouter un accès SSH à une instance de calcul. L’accès SSH est désactivé par défaut, mais il peut être activé au moment de la création de l’instance de calcul. L’accès SSH s’effectue par le biais d’un mécanisme de clé publique/privée. L’onglet vous donne des informations sur la connexion SSH, telles que l’adresse IP, le nom d’utilisateur et le numéro de port. Dans un déploiement de réseau virtuel, la désactivation de SSH empêche l’accès SSH à partir de l’Internet public ; vous pouvez toujours utiliser SSH à partir du réseau virtuel avec l’adresse IP privée du nœud d’instance de calcul et le port 22.
* Sélectionner le nom du calcul pour :
    * Afficher des informations sur une instance de calcul spécifique, telles que l’adresse IP et la région.
    * Créer ou modifier la planification du démarrage et de l’arrêt de l’instance de calcul (préversion).  Faites défiler vers le bas de la page pour modifier la planification.

---

Le [contrôle d'accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md) vous permet de contrôler les utilisateurs de l'espace de travail qui peuvent créer, supprimer, démarrer ou arrêter une instance de calcul. Tous les utilisateurs ayant les rôles Contributeur et Propriétaire dans l’espace de travail sont autorisés à créer, supprimer, démarrer, arrêter et redémarrer des instances de calcul dans tout l’espace de travail. Toutefois, seul le créateur d’une instance de calcul spécifique, ou l’utilisateur affecté si elle a été créée en son nom, est autorisé à accéder à Jupyter, JupyterLab et RStudio sur cette instance de calcul. Une instance de calcul est dédiée à un seul utilisateur disposant d’un accès racine et peut accéder au terminal par le biais de Jupyter/JupyterLab/RStudio. L'instance de calcul utilisera une connexion mono-utilisateur et toutes les actions utiliseront l'identité de cet utilisateur pour le contrôle d'accès en fonction du rôle Azure (Azure RBAC) et l'attribution des exécutions d'expériences. L’accès SSH est contrôlé par le biais d’un mécanisme de clé publique/privée.

Ces actions peuvent être contrôlées par Azure RBAC :
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/updateSchedules/action*

Pour créer une instance de calcul, vous avez besoin d’autorisations pour les actions suivantes :
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


## <a name="next-steps"></a>Étapes suivantes

* [Accéder au terminal d’une instance de calcul](how-to-access-terminal.md)
* [Créer et gérer des fichiers](how-to-manage-files.md)
* [Soumettre une exécution d’entraînement](how-to-set-up-training-targets.md)
