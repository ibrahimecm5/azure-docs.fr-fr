---
title: Gérer des packages Python 3 dans Azure Automation
description: Cet article explique comment gérer des packages Python 3 (préversion) dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 11/01/2021
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: a33f4f9b6403d1f4aff88cadb57ae0f78f02038b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443733"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Gérer des packages Python 3 (préversion) dans Azure Automation

Cet article décrit comment importer, gérer et utiliser des packages Python 3 (préversion) dans Azure Automation s’exécutant sur l’environnement de bac à sable Azure et des Runbooks Worker hybrides. Vous pouvez simplifier les runbooks en utilisant des packages Python pour importer les modules dont vous avez besoin. 

Pour prendre en charge les runbooks Python 3 dans le service Automation, le package Azure 4.0.0 est installé par défaut dans le compte Automation. La version par défaut peut être remplacée en important des packages Python dans votre compte Automation. La préférence est donnée à la version importée dans le compte Automation. Pour importer un package unique, consultez [Importer un package](#import-a-package). Pour importer un package avec plusieurs packages, consultez [Importer un package avec des dépendances](#import-a-package-with-dependencies). 

Pour plus d’informations sur la gestion des packages Python 2, consultez [Gérer les packages Python 2](./python-packages.md).

## <a name="packages-as-source-files"></a>Packages en tant que fichiers sources

Azure Automation prend en charge uniquement un package Python qui contient uniquement du code Python et qui n’inclut pas des extensions dans d’autres langages ou du code dans d’autres langages. Toutefois, il est possible que l’environnement de bac à sable Azure ne soit pas doté des compilateurs requis pour les fichiers binaires C/C++. Nous vous recommandons donc d’utiliser à la place des [fichiers wheel](https://pythonwheels.com/). [Python Package Index](https://pypi.org/) (PyPI) est un référentiel de logiciels pour le langage de programmation Python. Lorsque vous sélectionnez un package Python 3 à importer dans votre compte Automation à partir de PyPI, notez les parties suivantes du nom de fichier :

| Partie du nom de fichier | Description |
|---|---|
|cp38|Automation prend en charge **Python 3.8.x** pour les tâches cloud.|
|amd64|Les processus du bac à sable Azure correspondent à l’architecture **Windows 64 bits**.|

Par exemple, si vous souhaitez importer Pandas, vous pouvez sélectionner un fichier wheel portant un nom similaire à `pandas-1.2.3-cp38-win_amd64.whl`.

Certains packages Python disponibles sur PyPI ne fournissent pas de fichier wheel. Dans ce cas, téléchargez le fichier source (.zip ou .tar.gz), puis générez le fichier wheel en utilisant `pip`. Par exemple, suivez cette procédure en utilisant un ordinateur 64 bits avec Python 3.8. x et le package wheel installé :

1. Téléchargez le fichier source `pandas-1.2.4.tar.gz`.
1. Exécutez pip pour récupérer le fichier wheel avec la commande suivante : `pip wheel --no-deps pandas-1.2.4.tar.gz`.

## <a name="import-a-package"></a>Importer un package

1. Dans votre compte Automation, sélectionnez **Packages Python** sous **Ressources partagées**. Puis sélectionnez **+ Add a Python package** (Ajouter un package Python).

   :::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Capture d’écran de la page des packages Python montrant les packages Python dans le menu de gauche et Ajouter un package Python sélectionné.":::

1. Dans la page **Ajouter un package Python**, sélectionnez un package local à charger. Le package peut être un fichier **.whl** ou **.tar.gz**. 
1. Entrez un nom et sélectionnez Python 3.8.x (préversion) comme **Version du runtime**
1. Sélectionnez **Importer**

   :::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Capture d’écran montrant la page Ajouter un package Python 3.8.x avec un fichier tar.gz chargé sélectionné.":::

Dès qu’un package est importé, il est listé dans la page des packages Python de votre compte Automation. Pour supprimer un package, sélectionnez-le et cliquez sur **Supprimer**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Capture d’écran montrant la page des packages Python 3.8.x après l’importation d’un package.":::

### <a name="import-a-package-with-dependencies"></a>Importer un package avec des dépendances

Vous pouvez importer un package Python 3 et ses dépendances en important le script Python suivant dans un runbook Python 3, puis en l’exécutant.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Importation du script dans un runbook
Pour plus d’informations sur l’importation des runbooks, consultez [Importer un runbook à partir du Portail Azure](manage-runbooks.md#import-a-runbook-from-the-azure-portal). Copiez le fichier à partir de GitHub vers le stockage auquel le portail peut accéder avant d’exécuter l’importation.

La page **Importer un runbook** donne par défaut un nom au runbook pour qu’il corresponde à celui du script. Si vous avez accès au champ, vous pouvez modifier le nom. Le **type de Runbook** peut par défaut être **Python 2**. Si c’est le cas, veillez à le remplacer par **Python 3**.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="Capture d’écran montre la page d’importation du runbook Python 3.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Exécution du runbook pour importer le package et les dépendances

Après avoir créé et publié le runbook, exécutez-le pour importer le package. Pour plus d’informations sur l’exécution du runbook, consultez [Démarrer un runbook dans Azure Automation](start-runbooks.md).

Le script (`import_py3package_from_pypi.py`) requiert les paramètres suivants.

| Paramètre | Description |
|---------------|-----------------|
|subscription_id | ID d’abonnement du compte Automation |
| resource_group | Nom du groupe de ressources dans lequel est défini le compte Automation |
| automation_account | Nom du compte Automation |
| module_name | Nom du module à importer de `pypi.org` |

Pour plus d’informations sur l’utilisation des paramètres avec runbooks, consultez [Utiliser les paramètres runbook](start-runbooks.md#work-with-runbook-parameters).

## <a name="use-a-package-in-a-runbook"></a>Utiliser un package dans un runbook

Une fois le package importé, vous pouvez l’utiliser dans un runbook. Ajoutez le code suivant pour lister tous les groupes de ressources dans un abonnement Azure.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

> [!NOTE]
> Le package `automationassets` Python n’étant pas disponible sur pypi.org, il n’est pas disponible pour importation sur une machine Windows.

## <a name="identify-available-packages-in-sandbox"></a>Identifier les packages disponibles dans le bac à sable

Utilisez le code suivant pour répertorier les modules installés par défaut :

```python
#!/usr/bin/env python3

import pkg_resources
installed_packages = pkg_resources.working_set
installed_packages_list = sorted(["%s==%s" % (i.key, i.version)
   for i in installed_packages])

for package in installed_packages_list:
    print(package)
```

## <a name="next-steps"></a>Étapes suivantes

Pour préparer un runbook Python, consultez [Créer un runbook Python](learn/automation-tutorial-runbook-textual-python-3.md).
