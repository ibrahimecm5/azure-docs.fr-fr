---
title: Gérer des packages Python 2 dans Azure Automation
description: Cet article explique comment gérer des packages Python 2 dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 10/29/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 302ed99a2032200d770f371d24388c92a28a97e6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470793"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gérer des packages Python 2 dans Azure Automation

Cet article décrit comment importer, gérer et utiliser des packages Python 2 dans Azure Automation s’exécutant sur l’environnement de bac à sable Azure et des Runbooks Worker hybrides. Vous pouvez simplifier les runbooks en utilisant des packages Python pour importer les modules dont vous avez besoin.

Pour plus d’informations sur la gestion des packages Python 3, consultez [Gérer les packages Python 3](./python-3-packages.md).

## <a name="import-packages"></a>Importer des packages

1. Dans votre compte Automation, sélectionnez **Packages Python** sous **Ressources partagées**. Cliquez sur **+ Ajouter un package Python**.

    :::image type="content" source="media/python-packages/add-python-package.png" alt-text="Capture d’écran de la page des packages Python montrant les packages Python dans le menu de gauche et Ajouter un package Python sélectionné.":::

2. Dans la page **Ajouter un package Python**, sélectionnez un package local à charger. Le package peut être un fichier **.whl** ou **.tar.gz**. 
3. Entrez un nom et sélectionnez 2.x.x comme **Version du runtime**
4. Sélectionnez **Importer**.

   :::image type="content" source="media/python-packages/upload-package.png" alt-text="Capture d’écran montrant la page Ajouter un package Python avec un fichier tar.gz chargé sélectionné.":::

Dès qu’un package est importé, il est listé dans la page des **packages Python** de votre compte Automation. Pour supprimer un package, sélectionnez-le et cliquez sur **Supprimer**.

:::image type="content" source="media/python-packages/package-list.png" alt-text="Capture d’écran montrant la page des packages Python 2.7.x après l’importation d’un package.":::

## <a name="import-packages-with-dependencies"></a>Importer des packages avec des dépendances

Azure Automation ne résout pas les dépendances de packages Python pendant le processus d’importation. Il existe deux méthodes pour importer un package avec toutes ses dépendances. Seule l’une des étapes suivantes doit être utilisée pour importer les packages dans votre compte Automation.

### <a name="manually-download"></a>Téléchargement manuel

Sur un ordinateur Windows 64 bits où sont installés [Python 2.7](https://www.python.org/downloads/release/latest/python2) et [pip](https://pip.pypa.io/en/stable/), exécutez la commande suivante pour télécharger un package et toutes ses dépendances :

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Une fois les packages téléchargés, vous pouvez les importer dans votre compte Automation.

### <a name="runbook"></a>Runbook

 Pour obtenir un runbook, [importer des packages Python 2 à partir de Pypi dans Azure Automation](https://github.com/azureautomation/import-python-2-packages-from-pypi-into-azure-automation-account) de compte à partir de l’organisation Azure Automation GitHub dans votre compte Automation. Vérifiez que les paramètres d’exécution sont définis sur **Azure**, puis démarrez le runbook avec les paramètres. Pour que le compte Automation fonctionne, le runbook a besoin d’un compte d’identification. Veillez à démarrer chaque paramètre avec le commutateur, comme indiqué dans la liste et la capture d’écran suivantes :

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="Capture d’écran qui montre la page de vue d’ensemble pour import_py2package_from_pypi avec le volet Démarrer le Runbook sur le côté droit.":::

Le runbook vous permet de spécifier le package à télécharger. Par exemple, l’utilisation du paramètre `Azure` permet de télécharger tous les modules Azure et toutes les dépendances (environ 105).

Une fois le runbook terminé, vous pouvez consulter les **packages Python** sous **Ressources partagées** dans votre compte Automation pour vérifier que le package a bien été importé.

## <a name="use-a-package-in-a-runbook"></a>Utiliser un package dans un runbook

Vous pouvez utiliser un package importé dans un runbook. L’exemple suivant utilise le [package de l’utilitaire Azure Automation](https://github.com/azureautomation/azure_automation_utility). Ce package facilite l’utilisation de Python avec Azure Automation. Pour utiliser le package, suivez les instructions du référentiel GitHub et ajoutez-le au runbook. Par exemple, vous pouvez utiliser `from azure_automation_utility import get_automation_runas_credential` pour importer la fonction de récupération du compte d’identification.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

> [!NOTE]
> Le package `automationassets` Python n’étant pas disponible sur pypi.org, il n’est pas disponible pour importation sur une machine Windows.

## <a name="develop-and-test-runbooks-offline"></a>Développer et tester les runbooks en mode hors connexion

Pour développer et tester vos runbooks Python 2 en mode hors connexion, utilisez par exemple le module [Azure Automation python emulated assets](https://github.com/azureautomation/python_emulated_assets) (Ressources émulées Python dans Azure Automation) sur GitHub. Ce module vous permet de référencer vos ressources partagées telles que les informations d’identification, variables, connexions et certificats.

## <a name="next-steps"></a>Étapes suivantes

Pour préparer un runbook Python, consultez [Créer un runbook Python](./learn/automation-tutorial-runbook-textual-python-3.md).