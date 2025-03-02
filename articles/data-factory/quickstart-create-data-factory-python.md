---
title: 'Démarrage rapide : Créer une fabrique de données Azure à l’aide de Python'
description: Utilisez une fabrique de données pour copier les données d’un emplacement dans le stockage Blob Azure vers un autre emplacement.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.devlang: python
ms.topic: quickstart
ms.date: 05/27/2021
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 0344ac6e358b35f2f5d420932b188c229bd94fe8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749837"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>Démarrage rapide : Créer une fabrique de données et un pipeline à l’aide de Python

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version actuelle](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dans ce guide de démarrage rapide, vous créez une fabrique de données en utilisant Python. Le pipeline dans cette fabrique de données copie les données d’un dossier vers un autre dossier dans le stockage Blob Azure.

Azure Data Factory est un service d’intégration de données basé sur le cloud qui vous permet de créer des workflows orientés données pour orchestrer et automatiser le déplacement et la transformation des données. Il permet de créer et de planifier des workflows basés sur les données (nommés pipelines).

Les pipelines peuvent ingérer des données provenant de différents magasins de données. Les pipelines traitent ou transforment des données à l’aide de services de calcul tels que Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics et Azure Machine Learning. Les pipelines publient des données de sortie dans des magasins de données comme Azure Synapse Analytics pour des applications décisionnelles (BI).

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.6 ou version ultérieure](https://www.python.org/downloads/).

* [Un compte Stockage Azure](../storage/common/storage-account-create.md).

* [Explorateur Stockage Azure](https://storageexplorer.com/) (facultatif).

* [Une application dans Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Pour créer l’application, suivez les étapes de ce lien, avec l’option d’authentification 2 (secret d’application), puis affectez l’application au rôle **Contributeur** en suivant les instructions fournies dans le même article. Notez les valeurs suivantes, comme indiqué dans l’article, pour une utilisation ultérieure : **ID de l’application (client), valeur du secret client et ID du locataire.**

## <a name="create-and-upload-an-input-file"></a>Créer et charger un fichier d’entrée

1. Lancez le Bloc-notes. Copiez le texte suivant et enregistrez-le comme fichier **input.txt** sur votre disque.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  Utilisez des outils tels que l’[Explorateur Stockage Azure](https://storageexplorer.com/) pour créer le conteneur **adfv2tutorial** et **charger** le fichier sur ce dernier. Ensuite, chargez le fichier **input.txt** dans le dossier **input**.

## <a name="install-the-python-package"></a>Installer le package Python

1. Ouvrez un terminal ou une invite de commandes avec des privilèges d’administrateur. 
2. D’abord, installez le package Python pour les ressources de gestion Azure :

    ```python
    pip install azure-mgmt-resource
    ```
3. Pour installer le package Python pour Data Factory, exécutez la commande suivante :

    ```python
    pip install azure-mgmt-datafactory
    ```

    Le [kit SDK Python pour Data Factory](https://github.com/Azure/azure-sdk-for-python) prend en charge Python 2.7 et 3.6 ou versions ultérieures.

4. Pour installer le package Python pour l’authentification d’identité Azure, exécutez la commande suivante :

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > Le package « azure-identity » peut être en conflit avec « azure-cli » sur certaines dépendances communes. Si vous rencontrez un problème d’authentification, supprimez « azure-cli » et ses dépendances, ou utilisez un nouvel ordinateur sans installer le package « azure-cli » pour le faire fonctionner.
    > Pour les clouds souverains, vous devez utiliser les constantes spécifiques au cloud appropriées.  Consultez [Se connecter à toutes les régions à l'aide des bibliothèques Azure pour Python Multi-cloud | Microsoft Docs](/azure/developer/python/azure-sdk-sovereign-domain) pour obtenir des instructions sur la connexion à Python dans les clouds souverains.
    
    
## <a name="create-a-data-factory-client"></a>Créer un client de fabrique de données

  
1. Créez un fichier nommé **datafactory.py**. Insérez les instructions suivantes pour ajouter des références aux espaces de noms.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Ajoutez les fonctions suivantes qui affichent les informations.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. Ajoutez le code suivant à la méthode **Main** qui crée une instance de la classe DataFactoryManagementClient. Cet objet vous permet de créer la fabrique de données, un service lié, des jeux de données ainsi qu’un pipeline. Cet objet vous permet également de surveiller les détails de l’exécution du pipeline. Définissez la variable **subscription_id** pour l’ID de votre abonnement Azure. Pour obtenir la liste des régions Azure dans lesquelles Data Factory est actuellement disponible, sélectionnez les régions qui vous intéressent dans la page suivante, puis développez **Analytique** pour localiser **Data Factory** : [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/). Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent être proposés dans d’autres régions.

        
    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = '<resource group>'

        # The data factory name. It must be globally unique.
        df_name = '<factory name>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ClientSecretCredential(client_id='<Application (client) ID>', client_secret='<client secret value>', tenant_id='<tenant ID>') 
        
        # Specify following for Soverign Clouds, import right cloud constant and then use it to connect.
        # from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD as CLOUD
        # credentials = DefaultAzureCredential(authority=CLOUD.endpoints.active_directory, tenant_id=tenant_id)
        
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'westus'}
        df_params = {'location':'westus'}
    ```

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Ajoutez le code suivant à la méthode **Main** qui crée une **fabrique de données**. Si votre groupe de ressources existe déjà, commentez la première instruction `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Créer un service lié

Ajoutez le code suivant à la méthode **Main** qui crée un **service lié Stockage Azure**.

Vous allez créer des services liés dans une fabrique de données pour lier vos magasins de données et vos services de calcul à la fabrique de données. Dans ce guide de démarrage rapide, vous devez uniquement créer un service lié Azure Storage pour la source de copie et le magasin récepteur, nommé « AzureStorageLinkedService » dans l’exemple. Remplacez `<storageaccountname>` et `<storageaccountkey>` par le nom et la clé de votre compte de stockage Azure.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Créez les jeux de données

Dans cette section, vous créez deux jeux de données : un pour la source et l’autre pour le récepteur.

### <a name="create-a-dataset-for-source-azure-blob"></a>Créer un jeu de données pour l’objet blob Azure source

Ajoutez le code suivant à la méthode Main qui crée un jeu de données d’objet blob Azure. Pour plus d’informations sur les propriétés du jeu de données d’objet blob Azure, consultez l’article [Connecteur d’objets blob Azure](connector-azure-blob-storage.md#dataset-properties).

Vous définissez un jeu de données qui représente les données sources dans l’objet blob Azure. Ce jeu de données d’objet blob fait référence au service lié Azure Storage que vous avez créé à l’étape précédente.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)) 
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Créer un jeu de données pour l’objet blob Azure récepteur

Ajoutez le code suivant à la méthode Main qui crée un jeu de données d’objet blob Azure. Pour plus d’informations sur les propriétés du jeu de données d’objet blob Azure, consultez l’article [Connecteur d’objets blob Azure](connector-azure-blob-storage.md#dataset-properties).

Vous définissez un jeu de données qui représente les données sources dans l’objet blob Azure. Ce jeu de données d’objet blob fait référence au service lié Azure Storage que vous avez créé à l’étape précédente.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```


## <a name="create-a-pipeline"></a>Créer un pipeline

Ajoutez le code suivant à la méthode **Main** qui crée un **pipeline avec une activité de copie**.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    
    #Note1: To pass parameters to the pipeline, add them to the json string params_for_pipeline shown below in the format { “ParameterName1” : “ParameterValue1” } for each of the parameters needed in the pipeline.
    #Note2: To pass parameters to a dataflow, create a pipeline parameter to hold the parameter name/value, and then consume the pipeline parameter in the dataflow parameter in the format @pipeline().parameters.parametername.
    
    p_name = 'copyPipeline'
    params_for_pipeline = {}

    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>Créer une exécution du pipeline

Ajoutez le code suivant à la méthode **Main** qui **déclenche une exécution du pipeline**.

```python
    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>Surveiller une exécution du pipeline

Pour surveiller l’exécution du pipeline, ajoutez le code suivant à la méthode **Main** :

```python
    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

Maintenant, ajoutez l’instruction suivante pour appeler la méthode **main** lorsque le programme est exécuté :

```python
# Start the main method
main()
```

## <a name="full-script"></a>Script complet

Voici le code Python complet :

```python
from azure.identity import ClientSecretCredential 
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time

def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)

def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n\n")

def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The data factory name. It must be globally unique.
    df_name = '<factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location':'westus'}
    df_params = {'location':'westus'}
 
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename))
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>Exécuter le code

Créez et démarrez l’application, puis vérifiez l’exécution du pipeline.

La console affiche la progression de la création de la fabrique de données, du service lié, des jeux de données, du pipeline et de l’exécution du pipeline. Patientez jusqu’à l’affichage des détails de l’exécution de l’activité de copie avec la taille des données lues/écrites. Utilisez ensuite des outils comme l’[explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour vérifier que les objets blob sont copiés dans « outputBlobPath » depuis « inputBlobPath » comme vous l’avez spécifié dans les variables.

Voici l'exemple de sortie :

```console
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer la fabrique de données, ajoutez le code suivant au programme :

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Consultez les [didacticiels](tutorial-copy-data-dot-net.md) pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios.
