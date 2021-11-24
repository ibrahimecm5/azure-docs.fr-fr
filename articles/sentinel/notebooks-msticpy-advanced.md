---
title: Configurations avancées pour les notebooks Jupyter et MSTICPy dans Microsoft Sentinel | Microsoft Docs
description: En savoir plus sur les configurations avancées disponibles pour les notebooks Jupyter avec MSTICPy lorsque vous travaillez dans Microsoft Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 513f7c3c7e80f526f76e899a509de2b743fc9e7c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516966"
---
# <a name="advanced-configurations-for-jupyter-notebooks-and-msticpy-in-microsoft-sentinel"></a>Configurations avancées pour les notebooks Jupyter et MSTICPy dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article décrit les configurations avancées pour l’utilisation des notebooks Jupyter et MSTICPy dans Microsoft Sentinel.

Pour plus d’informations, consultez [Utiliser des notebooks Jupyter pour repérer des menaces de sécurité](notebooks.md) et [Tutoriel : Prendre en main les notebooks Jupyter et MSTICPy dans Microsoft Sentinel](notebook-get-started.md).

## <a name="prerequisites"></a>Prérequis

Cet article est une suite du [tutoriel : Prendre en main les notebooks Jupyter et MSTICPy dans Microsoft Sentinel](notebook-get-started.md). Nous vous recommandons de suivre le didacticiel avant de passer aux procédures avancées décrites ci-dessous.

## <a name="specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis"></a>Spécifier les paramètres d’authentification pour les API Azure et Microsoft Sentinel

Cette procédure décrit comment configurer les paramètres d’authentification pour Microsoft Sentinel et d’autres ressources de l’API Azure dans votre fichier **msticpyconfig.yaml**.

**Pour ajouter l’authentification Azure et les paramètres de l’API Microsoft Sentinel dans l’éditeur de paramètres MSTICPy** :

1. Passez à la cellule suivante, avec le code suivant et exécutez-la :

    ```python
    mpedit.set_tab("Data Providers")
    mpedit
    ```

1. Dans l’onglet **Fournisseurs de données**, sélectionnez **AzureCLI** > **Ajouter**.

1. Sélectionnez les méthodes d’authentification à utiliser :

    - Bien que vous puissiez utiliser un autre ensemble de méthodes à partir des [paramètres par défaut d’Azure](notebook-get-started.md#specify-the-azure-cloud-and-azure-authentication-methods), cette utilisation n’est pas une configuration classique.
    - À moins que vous ne souhaitiez utiliser l’authentification **env** (variable d’environnement), laissez les champs **ClientID**, **tenantiId** et **clientSecret** vides.
    - Bien que cela ne soit pas recommandé, MSTICPy prend également en charge l’utilisation des ID d’application cliente et des secrets pour votre authentification. Dans ce cas, définissez vos champs **ClientID**, **tenantId** et **ClientSecret** directement dans l’onglet **Fournisseurs de données** .

1. Cliquez sur **Enregistrer** pour enregistrer vos modifications :

## <a name="define-autoloading-query-providers"></a>Définir le chargement automatique des fournisseurs de requêtes

Définissez les fournisseurs de requêtes que vous souhaitez MSTICPy charger automatiquement lorsque vous exécutez la fonction `nbinit.init_notebook`.

Lorsque vous créez fréquemment de nouveaux blocs-notes, le chargement automatique des fournisseurs de requêtes peut vous faire gagner du temps en vous assurant que les fournisseurs requis sont chargés avant d’autres composants, tels que les fonctions pivot et notebooklets.

**Pour ajouter le chargement automatique des fournisseurs de requêtes**:

1. Passez à la cellule suivante, avec le code suivant et exécutez-la :

    ```python
    mpedit.set_tab("Autoload QueryProvs")
    mpedit
    ```

1. Sous l’onglet **Autoload QueryProv** :

   - Pour les fournisseurs Microsoft Sentinel, spécifiez le nom du fournisseur et le nom de l’espace de travail auquel vous souhaitez vous connecter.
   - Pour les autres fournisseurs de requêtes, spécifiez uniquement le nom du fournisseur.

   Chaque fournisseur a également les valeurs facultatives suivantes :

   - **Connexion automatique :** Cette option est définie sur **True** par défaut, et MSTICPy essaie de s’authentifier auprès du fournisseur immédiatement après le chargement. MSTICPy suppose que vous avez configuré les informations d’identification du fournisseur dans vos paramètres.

   - **Alias :** Quand MSTICPy charge un fournisseur, il attribue le fournisseur à un nom de variable Python. Par défaut, le nom de la variable est **qryworkspace_name** pour les fournisseurs Microsoft Sentinel et **qryprovider_name** pour les autres fournisseurs.

        Par exemple, si vous chargez un fournisseur de requêtes pour l’espace de travail *ContosoSOC*, ce fournisseur de requêtes sera créé dans votre environnement de bloc-notes avec le nom `qry_ContosoSOC`. Ajoutez un alias si vous souhaitez utiliser un nom plus petit ou plus facile à taper et à mémoriser. Le nom de la variable du fournisseur sera `qry_<alias>` , où `<alias>` est remplacé par le nom d’alias que vous avez fourni.

        Les fournisseurs que vous chargez par ce mécanisme sont également ajoutés à l' attribut `current_providers` MSTICPy, qui est utilisé, par exemple, dans le code suivant :

        ```python
        import msticpy
        msticpy.current_providers
        ```

1. Cliquez sur **Enregistrer les paramètres** pour enregistrer vos modifications :

## <a name="define-autoloaded-msticpy-components"></a>Définir les composants MSTICPy chargés

Cette procédure décrit comment définir d’autres composants qui sont chargés automatiquement par MSTICPy lors de l’exécution de la fonction `nbinit.init_notebook`.

Les composants pris en charge incluent, dans l’ordre suivant :

1. **TILookup :** [Bibliothèque du fournisseur TI](notebook-get-started.md#add-threat-intelligence-provider-settings)
1. **GeoIP :** [Fournisseur GeoIP](notebook-get-started.md#add-geoip-provider-settings) que vous souhaitez utiliser
1. **AzureData :** Module que vous utilisez pour interroger des détails sur les [ressources Azure](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis)
1. **AzureSentinelAPI :** Module que vous utilisez pour interroger l’[API Microsoft Sentinel](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis)
1. **Notebooklets :** Notebooklets à partir du [package msticnb](https://msticnb.readthedocs.io/en/latest/)
1. **Pivot:** Fonctions pivot

> [!NOTE]
> Les composants sont chargés dans cet ordre, car le composant pivot a besoin d’une requête et d’autres fournisseurs chargés pour trouver les fonctions pivot qu’il attache aux entités. Pour plus d’informations, consultez la [documentation MSTICPy](https://msticpy.readthedocs.io/en/latest/data_analysis/PivotFunctions.html).
>

**Pour définir les composants MSTICPy auto-chargés** :

1. Passez à la cellule suivante, avec le code suivant et exécutez-la :

   ```python
   mpedit.set_tab("Autoload Components")
   mpedit
   ```

1. Dans l’onglet **Composants de chargement automatique**, définissez les valeurs de paramètre en fonction des besoins. Par exemple :

   - **GeoIpLookup**.  Entrez le nom du fournisseur GeoIP que vous souhaitez utiliser, soit *GeoLiteLookup* , soit *IPStack*.  Pour plus d'informations, consultez [Ajoutez les paramètres de fournisseur GeoIP](notebook-get-started.md#add-geoip-provider-settings).

   - **Composants AzureData et AzureSentinelAPI**.  Renseignez les valeurs suivantes :

      - **auth_methods :** Remplacez les paramètres par défaut de AzureCLI et connectez-vous à l’aide des méthodes sélectionnées.
      - **Connexion automatique :** Affectez la valeur false à Load sans vous connecter.

      Pour plus d’informations, consultez[Spécifier les paramètres d’authentification pour les API Azure et Microsoft Sentinel](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis).

   - **Notebooklets**. Le composant **Notebooklets** a un seul bloc de paramètres : **AzureSentinel**.

      Spécifiez votre espace de travail Microsoft Sentinel à l’aide de la syntaxe suivante : `workspace:\<workspace name>`. Le nom de l’espace de travail doit être l’un des espaces de travail définis dans l’onglet **Microsoft Sentinel**.

      Si vous souhaitez ajouter d’autres paramètres à envoyer à la fonction `notebooklets init`, spécifiez-les en tant que paires clé-valeur, séparées par des sauts de lignes. Par exemple :

      ```python
      workspace:<workspace name>
      providers=["LocalData","geolitelookup"]
      ```

      Pour plus d’informations, consultez la [documentation MSTICNB (MSTIC Notebooklets)](https://msticnb.readthedocs.io/en/latest/msticnb.html#msticnb.data_providers.init).

    Certains composants, tels que **TILookup** et **pivot,** ne requièrent aucun paramètre.

1. Cliquez sur **Enregistrer les paramètres** pour enregistrer vos modifications.

## <a name="switch-between-python-36-and-38-kernels"></a>Basculer entre les noyaux Python 3.6 et 3.8

Si vous basculez entre les noyaux Python 3.65 et 3.8, vous constaterez peut-être que MSTICPy et d’autres packages ne sont pas installés comme prévu.

Cela peut se produire lorsque la commande `!pip install pkg` s’installe correctement dans le premier environnement, mais ne s’installe pas correctement dans le second. Cela crée une situation dans laquelle le deuxième environnement ne peut pas importer ou utiliser le package.

Nous vous recommandons de ne pas utiliser `!pip install...` pour installer des packages dans les blocs-notes Azure ML. Vous pouvez utiliser l’une des options suivantes :

- **Utilisez la magie de la ligne PIP dans un Notebook**. Exécutez :

  ```python

  %pip install --upgrade msticpy
  ```

- **Installer à partir d’un terminal**:

  1. Ouvrez un terminal dans les blocs-notes Azure ML et exécutez les commandes suivantes :

     ``` bash
     conda activate azureml_py38
     pip install --upgrade msticpy
     ```

  1. Fermez le terminal et redémarrez le noyau.

## <a name="set-an-environment-variable-for-your-msticpyconfigyaml-file"></a>Définir une variable d’environnement pour votre fichier msticpyconfig.yaml

Si vous exécutez dans Azure ML et que vous avez votre fichier **msticpyconfig. yaml** à la racine de votre dossier utilisateur, MSTICPy trouvera automatiquement ces paramètres. Toutefois, si vous exécutez les blocs-notes dans un autre environnement, suivez les instructions de cette section pour définir une variable d’environnement qui pointe vers l’emplacement de votre fichier de configuration.

La définition du chemin d’accès à votre fichier **msticpyconfig.yaml** dans une variable d’environnement vous permet de stocker votre fichier à un emplacement connu et de vous assurer que vous chargez toujours les mêmes paramètres.

Utilisez plusieurs fichiers de configuration, avec plusieurs variables d’environnement, si vous souhaitez utiliser des paramètres différents pour différents blocs-notes.

1. Choisissez un emplacement pour votre fichier **msticpyconfig.yaml** , par exemple **~/.msticpyconfig.yaml** ou **% UserProfile%/msticpyconfig.yaml**

    **Utilisateurs d’azure ML**: si vous stockez votre fichier de configuration dans votre dossier utilisateur azure ML, la `init_notebook` fonction MSTICPy (exécutée dans la cellule d’initialisation) trouvera et utilise automatiquement le fichier, et vous n’avez pas besoin de définir une variable d’environnement **MSTICPYCONFIG** .

    Toutefois, si vous avez également des secrets stockés dans le fichier, nous vous recommandons de stocker le fichier de configuration sur le lecteur local de calcul. Le stockage interne de calcul est accessible uniquement à la personne qui a créé le calcul, tandis que le stockage partagé est accessible à toute personne ayant accès à votre espace de travail Azure ML.

    Pour plus d’informations, consultez [Qu’est-ce qu’une instance de calcul Azure Machine Learning ?](../machine-learning/concept-compute-instance.md)

1. Si nécessaire, copiez votre fichier **msticpyconfig.yaml** à l’emplacement sélectionné.

1. Définissez la variable d’environnement **MSTICPYCONFIG** pour qu’elle pointe vers cet emplacement.

Utilisez l’une des procédures suivantes pour définir la variable d’environnement **MSTICPYCONFIG** .

# <a name="windows"></a>[Windows](#tab/windows)

Par exemple, pour définir la variable d’environnement **MSTICPYCONFIG** sur les systèmes Windows :

1. Déplacez le fichier **msticpyconfig.yaml** vers l’instance de calcul en fonction des besoins.
1. Dans la boîte de dialogue **Propriétés système**, cliquez sur l'onglet **Avancé** .
1. Sélectionnez **Variables d’environnement...** pour ouvrir la boîte de dialogue **Variables d’environnement** .
1. Dans la zone **Variables système**, sélectionnez **Nouveau...** , puis définissez les valeurs comme suit :

    - **Nom de la variable** : définir comme `MSTICPYCONFIG`
    - **Valeur de la variable** : entrez le chemin d’accès à votre fichier **msticpyconfig.yaml**

# <a name="linux"></a>[Linux](#tab/linux)

Cette procédure décrit comment mettre à jour le fichier **.bashrc** pour définir la variable d’environnement **MSTICPYCONFIG** sur les systèmes Linux.

1. Déplacez le fichier **msticpyconfig.yaml** vers l’instance de calcul en fonction des besoins.

1. Ouvrez un terminal de ML Azure, par exemple à partir de la page **Notebooks** de Microsoft Sentinel.

1. Vérifiez que vous pouvez accéder à votre fichier **msticpyconfig.yaml** .

   Dans votre terminal Azure ML, votre répertoire actuel doit être le répertoire de base de votre banque de fichiers Azure ML, monté dans le système de calcul Linux. L’invite est semblable à l'exemple suivant :

   ```python
   azureuser@alicecontoso-azml7:~/cloudfiles/code/Users/alicecontoso$
   ```

   Répertoriez tous les fichiers dans le répertoire de départ, y compris le fichier **msticpyconfig.yaml** , en entrant `ls` .

1. Pour déplacer le fichier **msticpyconfig.yaml** vers le magasin Fichier de calcul, entrez :

   ```python
   mv msticpyconfig.yaml ~
   ```

1. Utilisez l’un des processus suivants pour modifier le fichier **.bashrc** pour votre variable d’environnement :

    |Commande  |Étapes  |
    |---------|---------|
    |**vim**     |     1. Exécutez :`vim ~/.bashrc` <br>2. Accédez à la fin du fichier en appuyant sur **SHIFT + G** > **fin**. 3. Créez une nouvelle ligne en entrant **un** et en appuyant sur **ENTRÉE**. <br>4. Ajoutez votre variable d’environnement, puis appuyez sur **Échap** pour revenir au mode commande. <br>5. Enregistrez le fichier en entrant **: wq**.    |
    |**nano**     |           1. Exécutez :`nano ~/.bashrc`<br>        1. Accédez à la fin du fichier en appuyant sur **ALT+/** ou **OPTION+/** .<br>        1. Ajoutez votre variable d’environnement, puis enregistrez votre fichier. Appuyez sur **CTRL + X**, puis sur **Y**.      |
    |     |         |

    Ajoutez les variables d’environnement suivantes :

    - Si vous avez déplacé le fichier **msticpyconfig.yaml**, exécutez `export MSTICPYCONFIG=~/msticpyconfig.yaml` .
    - Si vous avez déplacé le fichier **msticpyconfig.yaml**, exécutez `export MSTICPYCONFIG=~/cloudfiles/code/Users/<YOURNAME>/msticpyconfig.yaml` .

# <a name="azure-ml-options"></a>[Options Azure ML](#tab/azure-ml)

Si vous devez stocker votre fichier **msticpyconfig.yaml** à un emplacement autre que votre dossier utilisateur Azure ML, utilisez l’une des options suivantes :

- **Un fichier *nbuser_settings. py* à la racine de votre dossier utilisateur**.  Bien que ce processus soit plus simple et moins intrusif que la modification du fichier **kernel.json** , il est pris en charge uniquement lorsque vous exécutez la fonction `init_notebook` au début du code de votre bloc-notes. Bien qu’il s’agit du comportement par défaut, si vous exécutez le code du bloc-notes sans exécuter `init_notebook` au préalable , MSTICPy peut ne pas être en mesure de trouver le fichier de configuration.

    1. Dans le terminal Azure ML, créez le fichier **nbuser_settings. py** à la racine de votre dossier utilisateur, qui est le dossier avec votre nom d’utilisateur.
    1. Dans votre fichier **nbuser_settings.py**, ajoutez les lignes suivantes :

        ```python
          import os
          os.environ["MSTICPYCONFIG"] = "~/msticpyconfig.yaml"
        ```

    Ce fichier est automatiquement importé par la fonction `init_notebook` et définit la variable d’environnement `MSTICPYCONFIG` pour le bloc-notes actuel.

- **Fichier *kernel.json* pour votre noyau Python**. Utilisez cette procédure si vous prévoyez d’exécuter le bloc-notes manuellement et éventuellement sans appeler la fonction `init_notebook` au démarrage.

  Il existe des noyaux pour Python 3.6 et Python 3.8. Si vous utilisez les deux noyaux, modifiez les deux fichiers.

  - **Emplacement Python 3.8**: */usr/local/share/jupyter/kernels/python38-azureml/kernel.json*
  - **Emplacement Python 3.6**: */usr/local/share/jupyter/kernels/python3-azureml/kernel.json*

  Pour définir la variable d’environnement dans le fichier **kernel.json** :

  1. Effectuez une copie du fichier **kernel.json** et ouvrez le fichier d’origine dans un éditeur. Vous devrez peut-être utiliser `sudo` pour remplacer le fichier **kernel.json** , et le contenu du fichier doit ressembler à l’exemple suivant :

      ```python
      {
         "argv": [
         "/anaconda/envs/azureml_py38/bin/python",
         "-m",
         "ipykernel_launcher",
         "-f",
         "{connection_file}"
         ],
         "display_name": "Python 3.8 - AzureML",
         "language": "python"
      }
      ```

  1. Après l’élément `"language"`, ajoutez la ligne suivante :`"env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }`

      Veillez à ajouter la virgule à la fin de la ligne `"language": "python"`. Par exemple :

      ```python
      {
          "argv": [
          "/anaconda/envs/azureml_py38/bin/python",
          "-m",
          "ipykernel_launcher",
          "-f",
          "{connection_file}"
          ],
          "display_name": "Python 3.8 - AzureML",
          "language": "python",
          "env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }
      }
      ```

---

> [!NOTE]
> Pour les options Linux et Windows, vous devez redémarrer votre serveur Jupyter pour qu’il récupère la variable d’environnement que vous avez définie.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

|Objet  |Plus de références  |
|---------|---------|
|**MSTICPy**     |      - [Configuration de package MSTICPy](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [Éditeur de Paramètres MSTICPy](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [Configuration de votre environnement de bloc-notes](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb).<br>    - [Bloc-notes MPSettingsEditor](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb). <br><br>**Remarque**: le référentiel Azure-Sentinel-Notebooks GitHub contient également un fichier modèle *msticpyconfig.yaml* avec des sections commentées, ce qui peut vous aider à comprendre les paramètres.      |
|**Microsoft Sentinel et notebooks Jupyter**     |      - [Jupyter Notebook : présentation](https://realpython.com/jupyter-notebook-introduction/)<br>    - [Documentation MSTICPy](https://msticpy.readthedocs.io/)<br>    - [Documentation sur les notebooks Microsoft Sentinel](notebooks.md)<br>    - [Le livre Jupyter Infosec](https://infosecjupyterbook.com/introduction.html)<br>    - [Procédure pas-à-pas du bloc-notes Linux Host Explorer](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [Pourquoi utiliser Jupyter pour les investigations de sécurité](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [Investigations de sécurité avec Microsoft Sentinel et les notebooks](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Documentation Pandas](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Documentation Bokeh](https://docs.bokeh.org/en/latest/)       |
|     |         |
