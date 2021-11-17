---
title: 'Didacticiel : Noter les modèles de Machine Learning avec PREDICT dans les pools d’Apache Spark sans serveur'
description: Découvrez comment utiliser la fonctionnalité PREDICT dans les pools d’Apache Spark sans serveur pour prédire les scores via les modèles de Machine Learning.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 45338fa4aed963d2172af25fec680e2017c98e88
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549885"
---
# <a name="tutorial-score-machine-learning-models-with-predict-in-serverless-apache-spark-pools"></a>Didacticiel : Noter les modèles de Machine Learning avec PREDICT dans les pools d’Apache Spark sans serveur

Découvrez comment utiliser la fonctionnalité PREDICT dans les pools d’Apache Spark sans serveur dans Azure Synapse Analytics pour la prédiction de score. Vous pouvez utiliser un modèle formé inscrit dans Azure Machine Learning (AML) ou dans le Azure Data Lake Storage par défaut (ADLS) de votre espace de travail Synapse.

Prédire dans un notebook PySpark Synapse vous permet de noter les modèles Machine Learning à l’aide du langage SQL, des fonctions définies par l’utilisateur (UDF) ou des transformateurs. Avec PREDICT, vous pouvez amener vos modèles de Machine Learning existants formés à l’extérieur de Synapse et inscrits en Azure Data Lake Storage Gen2 ou Azure Machine Learning, pour noter les données d’historique dans les limites sécurisées d’Azure Synapse Analytics. La fonction PREDICT prend un modèle et des données comme entrées. Cette fonctionnalité élimine l’étape de déplacement de données précieuses hors de l’entrepôt de données pour effectuer le scoring. L’objectif est de permettre aux consommateurs de modéliser de déduire facilement les modèles de Machine Learning dans Synapse et de collaborer de façon transparente avec les producteurs de modèles qui travaillent avec le bon Framework pour leur tâche.


Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> - Prédiction des scores pour les données dans un pool d’Apache Spark sans serveur à l’aide de modèles Machine Learning qui sont formés en dehors de Synapse et inscrits dans Azure Machine Learning ou Azure Data Lake Storage Gen2.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- [Espace de travail Azure Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage Azure Data Lake Storage Gen2 configuré comme stockage par défaut. Vous devez être le *contributeur aux données Blob du stockage* du système de fichiers Data Lake Storage Gen2 que vous utilisez.
- Un pool Apache Spark sans serveur dans votre espace de travail Azure Synapse Analytics. Pour plus d’informations, consultez [Créer un pool Spark dans Azure Synapse](../get-started-analyze-spark.md).
- Un espace de travail Azure Machine Learning est nécessaire si vous souhaitez former ou inscrire un modèle dans Azure Machine Learning. Pour plus d’informations [Gérer les espaces de travail Azure Machine Learning dans le portail ou avec le SDK Python](../../machine-learning/how-to-manage-workspace.md).
- Si votre modèle est inscrit dans Azure Machine Learning, vous avez besoin d’un service lié. Dans Azure Synapse Analytics, un service lié définit vos informations de connexion au service. Dans ce didacticiel, vous allez ajouter un service lié Azure Synapse Analytics et Azure Machine Learning. Pour en savoir plus, voir [Créer un service lié Azure Machine Learning dans Synapse](quickstart-integrate-azure-machine-learning.md)
- La fonctionnalité de prédiction nécessite que vous disposiez déjà d’un modèle formé qui soit enregistré dans Azure Machine Learning ou chargé dans Azure Data Lake Storage Gen2.

> [!NOTE]
> - La fonctionnalité de prédiction est prise en charge sur le pool Apache Spark sans serveur **Spark3** dans Azure Synapse Analytics. La version de **Python 3.8** est recommandée pour la création et la formation de modèles. 
> - PREDICT prend en charge la plupart des packages de modèles Machine Learning au format **MLflow** : **TensorFlow, ONNX, PyTorch, SkLearn et pyfunc** sont pris en charge dans cette version préliminaire.
> - PREDICT prend en charge la source du modèle **AML et ADLS**. Ici, le compte ADLS fait référence au **compte ADLS de l’espace de travail Synapse par défaut**. 


## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).


## <a name="use-predict-for-mlflow-packaged-models"></a>Utiliser PREDICT pour les modèles empaquetés MLFLOW

Assurez-vous que tous les composants requis sont en place avant de suivre ces étapes pour utiliser PREDICT.

1. **Bibliothèques d’importation :** Importez les bibliothèques suivantes pour utiliser la prédiction dans une session Spark.

   ```python
      #Import libraries
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      from azureml.core import Workspace
      from azureml.core.authentication import ServicePrincipalAuthentication
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   ```

2. **Définir des paramètres à l’aide de variables :** Le chemin d’accès aux données de Synapse ADLS et l’URI de modèle doivent être définis à l’aide de variables d’entrée. Vous devez également définir le runtime qui est « mlflow » et le type de données de la sortie du modèle. Notez que tous les types de données pris en charge dans PySpark sont également pris en charge par prédiction.

   > [!NOTE]
   > Avant d’exécuter ce script, mettez-le à jour avec l’URI du fichier de données ADLS Gen2, ainsi que le type de données de retour de la sortie du modèle et l’URI ADLS/AML pour le fichier de modèle.

   ```python
      #Set input data path
      DATA_FILE = "abfss://<filesystemname>@<account name>.dfs.core.windows.net/<file path>"
   
      #Set model URI
          #Set AML URI, if trained model is registered in AML
             AML_MODEL_URI = "<aml model uri>" #In URI ":x" signifies model version in AML. You can   choose which model version you want to run. If ":x" is not provided then by default   latest version will be picked.
   
          #Set ADLS URI, if trained model is uploaded in ADLS
             ADLS_MODEL_URI = "abfss://<filesystemname>@<account name>.dfs.core.windows.net/<model   mlflow folder path>"
   
      #Define model return type
      RETURN_TYPES = "<data_type>" # for ex: int, float etc. PySpark data types are supported
   
      #Define model runtime. This supports only mlflow
      RUNTIME = "mlflow"
   ```

3. **Méthodes d’authentification AML espace de travail :** Si le modèle est stocké dans le compte ADLS par défaut de l’espace de travail Synapse, vous n’avez pas besoin d’une configuration d’authentification supplémentaire. Si le modèle est inscrit dans Azure Machine Learning, vous pouvez choisir l’une ou l’autre des deux méthodes d’authentification prises en charge suivantes.

   > [!NOTE]
   > Mettez à jour le locataire, le client, l’abonnement, le groupe de ressources, l’espace de travail AML et les détails du service lié dans ce script avant de l’exécuter.

   - **Via le principal du service :** vous pouvez utiliser l’ID client du principal du service et le secret directement pour l’authentification auprès de l’espace de travail AML. Le principal du Service doit disposer d’un accès « collaborateur » à l’espace de travail AML.

      ```python
         #AML workspace authentication using service principal
         AZURE_TENANT_ID = "<tenant_id>"
         AZURE_CLIENT_ID = "<client_id>"
         AZURE_CLIENT_SECRET = "<client_secret>"
  
         AML_SUBSCRIPTION_ID = "<subscription_id>"
         AML_RESOURCE_GROUP = "<resource_group_name>"
         AML_WORKSPACE_NAME = "<aml_workspace_name>"
  
         svc_pr = ServicePrincipalAuthentication( 
             tenant_id=AZURE_TENANT_ID,
             service_principal_id=AZURE_CLIENT_ID,
             service_principal_password=AZURE_CLIENT_SECRET
         )
  
         ws = Workspace(
             workspace_name = AML_WORKSPACE_NAME,
             subscription_id = AML_SUBSCRIPTION_ID,
             resource_group = AML_RESOURCE_GROUP,
             auth=svc_pr
         )
      ```

   - **Par le biais du service lié :** vous pouvez utiliser le service lié pour vous authentifier auprès de l’espace de travail AML. Le service lié peut utiliser le « principal du service » ou le «Managed Service Identity (MSI) de l’espace de travail Synapse pour l’authentification. Le « principal du Service » ou « Managed Service Identity (MSI) » doit avoir un accès « collaborateur » à l’espace de travail AML.

      ```python
         #AML workspace authentication using linked service
         from notebookutils.mssparkutils import azureML
         ws = azureML.getWorkspace("<linked_service_name>") #   "<linked_service_name>" is the linked service name, not AML workspace name. Also, linked   service supports MSI and service principal both
      ```

4. **Activer la prédiction dans une session Spark :** Définissez la configuration Spark `spark.synapse.ml.predict.enabled` sur `true` pour activer la bibliothèque.

   ```python
      #Enable SynapseML predict
      spark.conf.set("spark.synapse.ml.predict.enabled","true")
   ```

5. **Lier le modèle dans une session Spark :** Liez le modèle aux entrées requises pour que le modèle puisse être référencé dans la session Spark. Définissez également l’alias afin de pouvoir utiliser le même alias dans l’appel PREDICT.

   > [!NOTE]
   > Mettez à jour l’alias de modèle et l’URI de modèle dans ce script avant de l’exécuter.

   ```python
      #Bind model within Spark session
       model = pcontext.bind_model(
        return_types=RETURN_TYPES, 
        runtime=RUNTIME, 
        model_alias="<random_alias_name>", #This alias will be used in PREDICT call to refer  this   model
        model_uri=ADLS_MODEL_URI, #In case of AML, it will be AML_MODEL_URI
        aml_workspace=ws #This is only for AML. In case of ADLS, this parameter can be removed
        ).register()
   ```

6. **Lire les données de ADLS :** Lire des données à partir de ADLS. Créez un tableau de données Spark et une vue en plus de la trame de données.

   > [!NOTE]
   > Mettez à jour le nom de la vue dans ce script avant de l’exécuter.

   ```python
      #Read data from ADLS
      df = spark.read \
       .format("csv") \
       .option("header", "true") \
       .csv(DATA_FILE,
           inferSchema=True)
      df.createOrReplaceTempView('<view_name>')
   ```

7. **Générer le score à l’aide de PREDICT :** vous pouvez appeler trois façons, à l’aide de l’API Spark SQL, à l’aide de la fonction définie par l’utilisateur (UDF) et à l’aide de l’API Transformer. Voici quelques exemples :

   > [!NOTE]
   > Mettez à jour le nom de l’alias de modèle, le nom de la vue et le nom de colonne d’entrée de modèle séparé par des virgules dans ce script avant de l’exécuter. Les colonnes d’entrée de modèle séparées par des virgules sont les mêmes que celles utilisées lors de l’apprentissage du modèle.

   ```python
      #Call PREDICT using Spark SQL API
   
      predictions = spark.sql(
                     """
                         SELECT PREDICT('<random_alias_name>',
                                   <comma_separated_model_input_column_name>) AS predict 
                         FROM <view_name>
                     """
                 ).show()
   ```

   ```python
      #Call PREDICT using user defined function (UDF)
   
      df = df[<comma_separated_model_input_column_name>] # for ex. df["empid","empname"]
   
      df.withColumn("PREDICT",model.udf(lit("<random_alias_name>"),*df.columns)).show()
   ```

   ```python
      #Call PREDICT using Transformer API
      
      columns = [<comma_separated_model_input_column_name>] # for ex. df["empid","empname"]
      
      tranformer = model.create_transformer().setInputCols(columns).setOutputCol("PREDICT")
   
      tranformer.transform(df).show()
   ```

## <a name="sklearn-example-using-predict"></a>Exemple Sklearn utilisant PREDICT

1. Importez des bibliothèques et lisez le jeu de données d’apprentissage de ADLS.

   ```python
      # Import libraries and read training dataset from ADLS
      
      import fsspec
      import pandas
      from fsspec.core import split_protocol
      
      adls_account_name = 'xyz' #Provide exact ADLS account name
      adls_account_key = 'xyz' #Provide exact ADLS account key
      
      fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>',      account_name=adls_account_name, account_key=adls_account_key)
      
      with fsspec_handle.open() as f:
          train_df = pandas.read_csv(f)
   ```

1. Formez le modèle et générez des artefacts mlflow.

   ```python
      # Train model and generate mlflow artifacts
   
      import os
      import shutil
      import mlflow
      import json
      from mlflow.utils import model_utils
      import numpy as np
      import pandas as pd
      from sklearn.linear_model import LinearRegression
      
      
      class LinearRegressionModel():
        _ARGS_FILENAME = 'args.json'
        FEATURES_KEY = 'features'
        TARGETS_KEY = 'targets'
        TARGETS_PRED_KEY = 'targets_pred'
      
        def __init__(self, fit_intercept, nb_input_features=9, nb_output_features=1):
          self.fit_intercept = fit_intercept
          self.nb_input_features = nb_input_features
          self.nb_output_features = nb_output_features
      
        def get_args(self):
          args = {
              'nb_input_features': self.nb_input_features,
              'nb_output_features': self.nb_output_features,
              'fit_intercept': self.fit_intercept
          }
          return args
      
        def create_model(self):
          self.model = LinearRegression(fit_intercept=self.fit_intercept)
      
        def train(self, dataset):
      
          features = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.FEATURES_KEY])], axis=0)
      
          targets = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.TARGETS_KEY])], axis=0)
      
      
          self.model.fit(features, targets)
      
        def predict(self, dataset):
          features = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.FEATURES_KEY])], axis=0)
          targets_pred = self.model.predict(features)
          return targets_pred
      
        def save(self, path):
          if os.path.exists(path):
            shutil.rmtree(path)
      
          # save the sklearn model with mlflow
          mlflow.sklearn.save_model(self.model, path)
      
          # save args
          self._save_args(path)
      
        def _save_args(self, path):
          args_filename = os.path.join(path, LinearRegressionModel._ARGS_FILENAME)
          with open(args_filename, 'w') as f:
            args = self.get_args()
            json.dump(args, f)
      
      
      def train(train_df, output_model_path):
        print(f"Start to train LinearRegressionModel.")
      
        # Initialize input dataset
        dataset = train_df.to_numpy()
        datasets = {}
        datasets['targets'] = dataset[:, -1]
        datasets['features'] = dataset[:, :9]
      
        # Initialize model class obj
        model_class = LinearRegressionModel(fit_intercept=10)
        with mlflow.start_run(nested=True) as run:
          model_class.create_model()
          model_class.train(datasets)
          model_class.save(output_model_path)
          print(model_class.predict(datasets))
      
      
      train(train_df, './artifacts/output')
   ```

1. Stockez les artefacts du modèle MLFLOW dans ADLS ou inscrivez-vous dans AML.

   ```python
      # Store model MLFLOW artifacts in ADLS
      
      STORAGE_PATH = 'abfs[s]://<container>/<path-to-store-folder>'
      
      protocol, _ = split_protocol(STORAGE_PATH)
      print (protocol)
   
      storage_options = {
          'account_name': adls_account_name,
          'account_key': adls_account_key
      }
      fs = fsspec.filesystem(protocol, **storage_options)
      fs.put(
          './artifacts/output',
          STORAGE_PATH, 
          recursive=True, overwrite=True)
   ```

   ```python
      # Register model MLFLOW artifacts in AML
      
      from azureml.core import Workspace, Model
      from azureml.core.authentication import ServicePrincipalAuthentication
      
      AZURE_TENANT_ID = "xyz"
      AZURE_CLIENT_ID = "xyz"
      AZURE_CLIENT_SECRET = "xyz"
      
      AML_SUBSCRIPTION_ID = "xyz"
      AML_RESOURCE_GROUP = "xyz"
      AML_WORKSPACE_NAME = "xyz"
      
      svc_pr = ServicePrincipalAuthentication( 
          tenant_id=AZURE_TENANT_ID,
          service_principal_id=AZURE_CLIENT_ID,
          service_principal_password=AZURE_CLIENT_SECRET
      )
      
      ws = Workspace(
          workspace_name = AML_WORKSPACE_NAME,
          subscription_id = AML_SUBSCRIPTION_ID,
          resource_group = AML_RESOURCE_GROUP,
          auth=svc_pr
      )
      
      model = Model.register(
          model_path="./artifacts/output",
          model_name="xyz",
          workspace=ws,
      )
   ```

1. Définissez les paramètres requis à l’aide de variables.

   ```python
      # If using ADLS uploaded model
   
      import pandas as pd
      from pyspark.sql import SparkSession
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   
      DATA_FILE = "abfss://xyz@xyz.dfs.core.windows.net/xyz.csv"
      ADLS_MODEL_URI_SKLEARN = "abfss://xyz@xyz.dfs.core.windows.net/mlflow/sklearn/     e2e_linear_regression/"
      RETURN_TYPES = "INT"
      RUNTIME = "mlflow"
   ```

   ```python
      # If using AML registered model
   
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      from azureml.core import Workspace
      from azureml.core.authentication import ServicePrincipalAuthentication
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   
      DATA_FILE = "abfss://xyz@xyz.dfs.core.windows.net/xyz.csv"
      AML_MODEL_URI_SKLEARN = "aml://xyz"
      RETURN_TYPES = "INT"
      RUNTIME = "mlflow"
   ```

1. Activez la fonctionnalité de prédiction SynapseML dans une session Spark.

   ```python
      spark.conf.set("spark.synapse.ml.predict.enabled","true")
   ```

1. Liez le modèle dans la session Spark.

   ```python
      # If using ADLS uploaded model
   
      model = pcontext.bind_model(
       return_types=RETURN_TYPES, 
       runtime=RUNTIME, 
       model_alias="sklearn_linear_regression",
       model_uri=ADLS_MODEL_URI_SKLEARN,
       ).register()
   ```

   ```python
      # If using AML registered model
   
      model = pcontext.bind_model(
       return_types=RETURN_TYPES, 
       runtime=RUNTIME, 
       model_alias="sklearn_linear_regression",
       model_uri=AML_MODEL_URI_SKLEARN,
       aml_workspace=ws
       ).register()
   ```

1. Chargez les données de test à partir de ADLS.

   ```python
      # Load data from ADLS
   
      df = spark.read \
          .format("csv") \
          .option("header", "true") \
          .csv(DATA_FILE,
              inferSchema=True)
      df = df.select(df.columns[:9])
      df.createOrReplaceTempView('data')
      df.show(10)
   ```

1. Appelez PREDICT pour générer le score.

   ```python
      # Call PREDICT
      
      predictions = spark.sql(
                        """
                            SELECT PREDICT('sklearn_linear_regression', *) AS predict FROM data
                        """
                    ).show()
   ```


## <a name="next-steps"></a>Étapes suivantes

- [Fonctionnalités de Machine Learning dans Azure Synapse Analytics](what-is-machine-learning.md)
