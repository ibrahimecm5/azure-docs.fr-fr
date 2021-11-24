---
title: Prendre en main les notebooks Jupyter et MSTICPy dans Microsoft Sentinel
description: Parcourez le guide de prise en main pour notebooks ML Microsoft Sentinel pour découvrir les principes de base des notebooks Microsoft Sentinel avec MSTICPy et des requêtes.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 3bcafc82c3fced8a5c3ff53feddb8be5d4338178
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517459"
---
# <a name="tutorial-get-started-with-jupyter-notebooks-and-msticpy-in-microsoft-sentinel"></a>Tutoriel : Prendre en main les notebooks Jupyter et MSTICPy dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ce tutoriel explique comment exécuter le notebook **Guide de prise en main pour notebooks ML Microsoft Sentinel**, qui définit les configurations de base pour l’exécution de notebooks Jupyter dans Microsoft Sentinel et l’exécution de requêtes de données simples.

Le notebook **Guide de prise en main pour notebooks ML Microsoft Sentinel** utilise MSTICPy, une bibliothèque Python d’outils de cybersécurité conçue par Microsoft, qui fournit des fonctionnalités de chasse et d’investigation des menaces.

MSTICPy réduit la quantité de code que les clients doivent écrire pour Microsoft Sentinel et fournit les éléments suivants :

- Capacités d’interrogation des données sur les tables Microsoft Sentinel, Microsoft Defender pour point de terminaison, Splunk et d’autres sources de données.
- Recherches de renseignement sur les menaces avec des fournisseurs TI, tels que VirusTotal et AlienVault OTX.
- Fonctions d’enrichissement telles que la géolocalisation des adresses IP, l’extraction de l’indicateur de compromission (IoC) et les recherches WhoIs.
- Outils de visualisation utilisant les chronologies d’événements, les arborescences de processus et le mappage géographique.
- Analyses avancées, telles que la décomposition de série chronologique, la détection des anomalies et le clustering.

Les étapes de ce tutoriel décrivent comment exécuter le notebook **Guide de prise en main pour notebooks ML Microsoft Sentinel** dans votre espace de travail Azure ML via Microsoft Sentinel. Vous pouvez également utiliser ce didacticiel comme une aide pour effectuer des étapes similaires pour exécuter des notebooks dans d’autres environnements, y compris localement.

Pour plus d’informations, consultez [Utiliser des notebooks pour dynamiser l’investigation](hunting.md#use-notebooks-to-power-investigations) et [Utiliser des notebooks Jupyter pour rechercher des menaces de sécurité](notebooks.md).

> [!NOTE]
> Plusieurs notebooks Microsoft Sentinel n’utilisent pas MSTICPy, tels que les notebooks d’**analyse des informations d’identification**, ni les exemples PowerShell et C#. Les notebooks qui n’utilisent pas MSTICpy n’ont pas besoin de la configuration MSTICPy décrite dans cet article.
>

## <a name="prerequisites"></a>Prérequis

- Pour utiliser des notebooks dans Microsoft Sentinel, assurez-vous de disposer des autorisations requises. Pour plus d’informations, consultez [Gérer l’accès aux notebooks Microsoft Sentinel](notebooks.md#manage-access-to-microsoft-sentinel-notebooks).

- Pour effectuer les étapes de ce didacticiel, vous avez besoin de Python 3.6 ou version ultérieure. Dans Azure ML, vous pouvez utiliser un noyau Python 3.8 (recommandé) ou un noyau Python 3.6.

- Ce notebook utilise le service de recherche de géolocalisation [MaxMind GeoLite2](https://www.maxmind.com) pour les adresses IP. Pour utiliser le service MaxMind GeoLite2, vous avez besoin d’une clé de compte. Vous pouvez vous inscrire pour obtenir un compte gratuit et une clé sur la [page d’inscription à MaxMind](https://www.maxmind.com/en/geolite2/signup).

- Ce notebook utilise [VirusTotal](https://www.virustotal.com) (VT) comme source de renseignement sur les menaces. Pour utiliser la recherche de renseignements sur les menaces VirusTotal, vous avez besoin d’un compte VirusTotal et d’une clé API.

    Vous pouvez vous inscrire pour obtenir un compte VT gratuit sur la [page de prise en main de VirusTotal](https://developers.virustotal.com/v3.0/reference#getting-started). Si vous utilisez déjà VirusTotal, vous pouvez utiliser votre clé existante.

    > [!WARNING]
    > Si vous utilisez une clé d’entreprise VT, stockez-la dans Azure Key Vault au lieu du fichier **msticpyconfig.yaml**. Pour plus d’informations, consultez [Spécifier des secrets en tant que secrets Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets) dans la documentation MSTICPY.
    >
    > Si vous ne souhaitez pas configurer un Azure Key Vault pour le moment, inscrivez-vous et utilisez un compte gratuit jusqu’à ce que vous puissiez configurer le stockage Key Vault.

## <a name="run-and-initialize-the-getting-started-guide-notebook"></a>Exécuter et initialiser le notebook du guide de prise en main

Cette procédure décrit comment lancer votre notebook et initialiser MSTICpy.

1. Dans Microsoft Sentinel, sélectionnez **Notebooks** à gauche.

1. Dans l’onglet **Modèles**, sélectionnez **Guide de prise en main pour notebooks ML Microsoft Sentinel** > **Enregistrer le notebook** pour l’enregistrer dans votre espace de travail Azure ML.

    Sélectionnez **Lancer le notebook** pour exécuter le notebook. Le notebook contient une série de cellules :

    - Les cellules *Markdown* contiennent du texte et des graphiques avec des instructions sur l’utilisation du notebook
    - Les cellules *Code* contiennent du code exécutable qui exécute les fonctions de notebook

    **Lecture et exécution de cellules de code**

    Lisez et exécutez les cellules de code dans l’ordre. Les cellules ignorées ou exécutées dans le désordre peuvent provoquer des erreurs plus tard dans le notebook.

    Exécutez chaque cellule en sélectionnant le bouton de lecture situé à gauche de chaque cellule. En fonction de la fonction exécutée, le code dans la cellule peut s’exécuter très rapidement ou peut prendre quelques secondes.

    Lors de l’exécution, le bouton de lecture devient un compteur de chargement et l’état `Executing` s’affiche en bas de la cellule, ainsi que le temps écoulé.

    > [!TIP]
    > Si votre notebook ne semble pas fonctionner comme décrit, redémarrez le noyau et exécutez le notebook à partir du début. Par exemple, si l’exécution d’une cellule du notebook **Guide de prise en main** prend plus d’une minute, essayez de redémarrer le noyau et de réexécuter le notebook.
    >
    > Le notebook **Guide de prise en main** inclut des instructions pour l’utilisation de base des notebooks Jupyter, y compris le redémarrage du noyau Jupyter.
    >

    Une fois que vous avez terminé la lecture et l’exécution des cellules dans la section **Qu’est-ce qu’un Jupyter Notebook**, vous êtes prêt à démarrer les tâches de configuration, à partir de la section **configuration de l’environnement du notebook**.

1. Exécutez la première cellule de code dans la section **Configuration de l’environnement du notebook** de votre notebook, qui comprend le code suivant :

    ```python
    # import some modules needed in this cell
    from pathlib import Path
    from IPython.display import display, HTML

    REQ_PYTHON_VER="3.6"
    REQ_MSTICPY_VER="1.2.3"

    display(HTML("Checking upgrade to latest msticpy version"))
    %pip install --upgrade --quiet msticpy[azuresentinel]>=$REQ_MSTICPY_VER

    # intialize msticpy
    from msticpy.nbtools import nbinit
    nbinit.init_notebook(
    namespace=globals(),
    extra_imports=["urllib.request, urlretrieve"]
    )
    pd.set_option("display.html.table_schema", False)
    ```

    L’état d’initialisation est indiqué dans la sortie. Des avertissements de configuration concernant des paramètres manquants dans le fichier `Missing msticpyconfig.yaml` sont attendus, car vous n’avez pas encore configuré ce paramètre.

> [!NOTE]
> La plupart des notebooks Microsoft Sentinel commencent par une cellule d’initialisation MSTICPy qui :
>
> - Définit les versions minimales de Python et MSTICPy requises par le notebook.
> - Garantit que la dernière version de MSTICPy est installée.
> - Importe et exécute la fonction `init_notebook`.
>

## <a name="create-your-configuration-file"></a>Créer votre fichier de configuration

Après l’initialisation de base, vous êtes prêt à créer votre fichier de configuration avec les paramètres de base pour l’utilisation de MSTICPy.

De nombreux notebooks Microsoft Sentinel se connectent à des services externes tels que [VirusTotal](https://www.virustotal.com) (VT) pour collecter et enrichir les données. Pour vous connecter à ces services, vous devez définir et stocker les détails de configuration, tels que les jetons d’authentification. Le fait de disposer de ces données dans votre fichier de configuration vous évite d’avoir à taper des jetons d’authentification et des détails de l’espace de travail chaque fois que vous utilisez un notebook.

MSTICPy utilise un fichier **msticpyconfig.yaml** pour stocker un large éventail de détails de configuration.  Par défaut, un fichier **msticpyconfig.yaml** est généré par la fonction d’initialisation du notebook. Si vous avez [cloné ce notebook à partir du portail Microsoft Sentinel](#run-and-initialize-the-getting-started-guide-notebook), le fichier config est renseigné avec les données de l’espace de travail Microsoft Sentinel. Ces données sont lues à partir d’un fichier **config.json** créé dans l’espace de travail Azure ML lorsque vous lancez votre notebook. Pour plus d’informations, consultez la [documentation sur la configuration du package MSTICPy](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html).

Les sections suivantes décrivent comment ajouter des détails de configuration supplémentaires au fichier **msticpyconfig.yaml**.

> [!NOTE]
> Si vous réexécutez le notebook *Guide de prise en main* et que vous disposez déjà d’un fichier **msticpyconfig.yaml** avec une configuration minimale, la fonction `init_notebook` ne remplace pas ou ne modifie pas votre fichier existant.
>

> [!TIP]
> À tout moment, sélectionnez le menu déroulant **-Help** dans l’outil de configuration de MSTICPy pour obtenir des instructions supplémentaires et des liens vers une documentation détaillée.
>

### <a name="display-the-msticpy-settings-editor"></a>Afficher l’éditeur de paramètres MSTICPy

1. Dans une cellule de code, exécutez le code suivant pour importer l'outil `MpConfigEdit` et afficher un éditeur de paramètres pour votre fichier **msticpyconfig.yaml** :

    ```python
    from msticpy.config import MpConfigEdit

    mpedit = MpConfigEdit( "msticpyconfig.yaml")
    mpedit.set_tab("AzureSentinel")
    display(mpedit)
    ```

    Par exemple :

    :::image type="content" source="media/notebook-get-started/msticpy-editor.png" alt-text="Capture d’écran de l’éditeur de paramètres MSTICPy.":::

    Le fichier **msticpyconfig.yaml** créé automatiquement, affiché dans l’éditeur de paramètres, contient deux entrées dans la section Microsoft Sentinel. Elles sont toutes deux renseignées avec les détails de l’espace de travail Microsoft Sentinel à partir duquel le notebook a été cloné. Une entrée porte le nom de votre espace de travail et l’autre est nommée **Default**.

    MSTICPy vous permet de stocker des configurations pour plusieurs espaces de travail Microsoft Sentinel et de passer de l’un à l’autre. L’entrée **Default** vous permet de vous authentifier à votre espace de travail d’« accueil » par défaut, sans avoir à le nommer explicitement. Si vous ajoutez des espaces de travail supplémentaires, vous pouvez configurer l’un d’entre eux comme entrée **Default**.

    > [!NOTE]
    > Dans l’environnement Azure ML, l’affichage de l’éditeur de paramètres peut prendre 10 à 20 secondes.

1. Vérifiez vos paramètres actuels et sélectionnez **Enregistrer les paramètres**.

### <a name="add-threat-intelligence-provider-settings"></a>Ajouter des paramètres de fournisseur de renseignement sur les menaces

Cette procédure décrit comment stocker votre [clé API VirusTotal](#prerequisites) dans le fichier **msticpyconfig.yaml**. Vous pouvez choisir de charger la clé API dans Azure Key Vault, mais vous devez d’abord configurer les paramètres Key Vault. Pour plus d’informations, consultez [Configuration des paramètres Key Vault](#configure-key-vault-settings).

**Pour ajouter des détails de VirusTotal dans l’éditeur de paramètres MSTICPy** :

1. Entrez le code suivant dans une cellule de code et exécutez :

   ```python
   mpedit.set_tab("TI Providers")
   mpedit
   ```

1. Dans l’onglet **Fournisseurs TI**, sélectionnez **Ajouter fourn** > **VirusTotal** > **Ajouter**.

1. Sous **Clé d’authentification**, sélectionnez **Texte** en regard de l’option **Stockage**.

1. Dans le champ **Valeur**, collez votre clé API.

1. Sélectionnez **Mettre à jour**, puis sélectionnez **Enregistrer les paramètres** en bas de l’éditeur de paramètres.

> [!TIP]
> Pour plus d’informations sur les autres fournisseurs de renseignement sur les menaces pris en charge, consultez [Threat intelligence providers](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html) (Fournisseurs de renseignement sur les menaces) dans la documentation de MSTICPy et [Intégration du renseignement sur les menaces dans Microsoft Sentinel](threat-intelligence-integration.md).
>
### <a name="add-geoip-provider-settings"></a>Ajouter des paramètres de fournisseur GeoIP

Cette procédure décrit comment stocker une [clé de compte MaxMind GeoLite2](#prerequisites) dans le fichier **msticpyconfig.yaml**, ce qui permet à votre notebook d’utiliser des services de recherche de géolocalisation pour les adresses IP.

**Pour ajouter des paramètres de fournisseur GeoIP dans l’éditeur de paramètres MSTICPy** :

1. Entrez le code suivant dans une cellule de code vide et exécutez :

   ```python
   mpedit.set_tab("GeoIP Providers")
   mpedit
   ```

1. Dans l’onglet **Fournisseurs GeoIP**, sélectionnez **Ajouter fourn** > **GeoIPLite** > **Ajouter**.

1. Dans le champ **Valeur**, entrez votre clé de compte MaxMind.

1. Si nécessaire, mettez à jour le dossier **~/.msticpy** par défaut pour le stockage de la base de données GeoIP téléchargée.

    - Sur Windows, ce dossier est mappé à **%USERPROFILE%/.msticpy**.
    - Sur Linux ou macOS, ce chemin d’accès est mappé au dossier **.msticpy** dans votre dossier d’accueil.

> [!TIP]
> Pour plus d’informations sur les autres services de recherche de géolocalisation pris en charge, consultez la [documentation sur les fournisseurs MSTICPy GeoIP](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html).
>

### <a name="configure-azure-cloud-settings"></a>Configurer les paramètres du cloud Azure

Si votre organisation n’utilise pas le cloud public Azure, vous devez le spécifier dans vos paramètres pour réussir l’authentification et utiliser correctement les données de Microsoft Sentinel et d’Azure. Pour plus d’informations, consultez [Spécifier le cloud Azure et les méthodes d'authentification Azure par défaut](#specify-the-azure-cloud-and-azure-authentication-methods).

### <a name="validate-settings"></a>Valider les paramètres

Sélectionnez **Valider les paramètres** dans l’éditeur de paramètres.

Des messages d’avertissement relatifs à des configurations manquantes sont attendus, mais vous ne devriez pas avoir de paramètres pour le fournisseur de renseignement sur les menaces ou le fournisseur GeoIP.

En fonction de votre environnement, vous devrez peut-être également [Configurer des paramètres Key Vault](#configure-key-vault-settings) ou [Spécifier le cloud Azure](#specify-the-azure-cloud-and-azure-authentication-methods).

Si vous devez apporter des modifications en raison de la validation, apportez ces modifications, puis sélectionnez **Enregistrer les paramètres**.

Lorsque vous avez terminé, sélectionnez le bouton **Fermer** pour masquer la sortie de validation.

Pour plus d’informations, consultez [Configurations avancées pour les notebooks Jupyter et MSTICPy dans Microsoft Sentinel](notebooks-msticpy-advanced.md).

## <a name="load-saved-msticpy-settings"></a>Charger les paramètres MSTICPy enregistrés

Dans la procédure [Créer votre fichier de configuration](#create-your-configuration-file), vous avez enregistré vos paramètres dans votre fichier **msticpyconfig.yaml** local.

Toutefois, MSTICPy ne recharge pas automatiquement ces paramètres tant que vous ne redémarrez pas le noyau ou n’exécutez pas un autre notebook. Pour forcer MSTICPy à recharger à partir du nouveau fichier de configuration, passez à la cellule de code suivante, avec le code suivant, et exécutez-la :

```python
import msticpy
msticpy.settings.refresh_config()
```

## <a name="test-your-notebook"></a>Tester votre notebook

Maintenant que vous avez initialisé votre environnement et configuré les paramètres de base de votre espace de travail, utilisez la classe `QueryProvider` MSTICPy pour tester le notebook. `QueryProvider` interroge une source de données, dans le cas présent votre espace de travail Microsoft Sentinel, et rend les données interrogées disponibles pour les afficher et les analyser dans votre notebook.

Utilisez les procédures suivantes pour créer une instance de la classe `QueryProvider`, vous authentifier auprès de Microsoft Sentinel à partir de votre notebook et afficher et exécuter des requêtes avec différentes options de paramètre.

> [!TIP]
> Vous pouvez avoir plusieurs instances de `QueryProvider` chargées pour une utilisation avec plusieurs espaces de travail Microsoft Sentinel ou d’autres fournisseurs de données tels que Microsoft Defender pour point de terminaison.
>

### <a name="load-the-queryprovider"></a>Charger le QueryProvider

Pour charger le `QueryProvider` pour `AzureSentinel`, passez à la cellule suivante avec le code suivant et exécutez-la :

```python
# Initialize a QueryProvider for Microsoft Sentinel
qry_prov = QueryProvider("AzureSentinel")
```

> [!NOTE]
> Si vous voyez un avertissement `Runtime dependency of PyGObject is missing` lors du chargement du pilote Microsoft Sentinel, consultez l’[erreur : *La dépendance au runtime de PyGObject est manquante*](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/%22Runtime-dependency-of-PyGObject-is-missing%22-error).
Cet avertissement n’affecte pas la fonctionnalité du notebook.
>

### <a name="authenticate-to-your-microsoft-sentinel-workspace-from-your-notebook"></a>Vous authentifier auprès de votre espace de travail Microsoft Sentinel à partir de votre notebook

Authentifiez-vous auprès de votre espace de travail Microsoft Sentinel en utilisant l’[autorisation de l’appareil](../active-directory/develop/v2-oauth2-device-code.md) avec vos informations d’identification Azure.

L’autorisation de l’appareil ajoute un autre facteur à l’authentification en générant un code d’appareil à usage unique que vous fournissez dans le cadre du processus d’authentification.

**Pour s’authentifier à l’aide de l’autorisation de l’appareil** :

1. Exécutez la cellule de code suivante pour générer et afficher un code d’appareil :

   ```python
   # Get the Microsoft Sentinel workspace details from msticpyconfig
   # Loading WorkspaceConfig with no parameters uses the details
   # of your Default workspace
   # If you want to connect to a specific workspace use this syntax:
   #    ws_config = WorkspaceConfig(workspace="WorkspaceName")
   # ('WorkspaceName' should be one of the workspaces defined in msticpyconfig.yaml)
   ws_config = WorkspaceConfig()

   # Connect to Microsoft Sentinel with your QueryProvider and config details
   qry_prov.connect(ws_config)
   ```

    Par exemple :

    :::image type="content" source="media/notebook-get-started/device-authorization.png" alt-text="Capture d’écran montrant un code d’autorisation d’appareil.":::

1. Sélectionnez et copiez le code indiqué dans le Presse-papiers. Ensuite, accédez à [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) et collez le code là où vous y êtes invité.

1. Lorsque vous voyez le message de confirmation de votre connexion, fermez l’onglet du navigateur et revenez à votre notebook dans Microsoft Sentinel.

   Une sortie similaire à ce qui suit s’affiche dans votre notebook :

   :::image type="content" source="media/notebook-get-started/authorization-complete.png" alt-text="Capture d’écran montrant que le processus d’autorisation de l’appareil est terminé.":::

**Mettre en cache votre jeton de connexion à l’aide d’Azure CLI**

Pour éviter d’avoir à vous authentifier à nouveau si vous redémarrez le noyau ou si vous exécutez un autre notebook, vous pouvez mettre en cache votre jeton de connexion à l’aide d’Azure CLI.

Le composant Azure CLI sur l’instance de calcul met en cache un *jeton d’actualisation* qu’il peut réutiliser jusqu’à ce que le jeton expire. MSTICPy utilise automatiquement les informations d’identification Azure CLI, si elles sont disponibles.

Pour vous authentifier à l’aide d’Azure CLI, entrez ce qui suit dans une cellule vide et exécutez-la :

```python
!az login
```

> [!NOTE]
> Vous devrez vous authentifier à nouveau si vous redémarrez votre instance de calcul ou basculez vers une autre instance. Pour plus d’informations, consultez la section [Mettre en cache les informations d’identification avec Azure CLI](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/Caching-credentials-with-Azure-CLI) dans le wiki du référentiel GitHub des notebooks Microsoft Sentinel.
>

### <a name="view-the-microsoft-sentinel-workspace-data-schema-and-built-in-msticpy-queries"></a>Afficher le schéma de données de l’espace de travail Microsoft Sentinel et les requêtes MSTICPy intégrées

Une fois que vous êtes connecté à un QueryProvider Microsoft Sentinel, vous pouvez comprendre les types de données disponibles pour les requêtes en interrogeant le schéma de données de l’espace de travail Microsoft Sentinel.

Le QueryProvider Microsoft Sentinel a une propriété `schema_tables`, qui vous fournit une liste des tables du schéma et une propriété `schema`, qui inclut également les noms de colonnes et les types de données pour chaque table.

**Pour afficher les dix premières tables dans le schéma Microsoft Sentinel** :

Passez à la cellule suivante, avec le code suivant, et exécutez-la. Vous pouvez omettre `[:10]` pour répertorier toutes les tables dans votre espace de travail.

```python
# Get list of tables in the Workspace with the 'schema_tables' property
qry_prov.schema_tables[:10]  # Output only a sample of tables for brevity
                             # Remove the "[:10]" to see the whole list
```

Vous obtenez la sortie suivante :

```output
Sample of first 10 tables in the schema
    ['AACAudit',
     'AACHttpRequest',
     'AADDomainServicesAccountLogon',
     'AADDomainServicesAccountManagement',
     'AADDomainServicesDirectoryServiceAccess',
     'AADDomainServicesLogonLogoff',
     'AADDomainServicesPolicyChange',
     'AADDomainServicesPrivilegeUse',
     'AADDomainServicesSystemSecurity',
     'AADManagedIdentitySignInLogs']
```

MSTICPy inclut également de nombreuses requêtes intégrées que vous pouvez exécuter. Répertoriez les requêtes disponibles avec `.list_queries()` et obtenez des détails spécifiques sur une requête en l’appelant avec un point d’interrogation (`?`) inclus en tant que paramètre. Vous pouvez également afficher la liste des requêtes et l’aide associée dans le navigateur de requêtes.

**Pour afficher un exemple de requêtes disponibles** :

Passez à la cellule suivante, avec le code suivant, et exécutez-la. Vous pouvez omettre `[::5]` pour répertorier toutes les requêtes.

```python
# Get a sample of available queries
print(qry_prov.list_queries()[::5])  # showing a sample - remove "[::5]" for whole list
```

Vous obtenez la sortie suivante :

```output
Sample of queries
=================
['Azure.get_vmcomputer_for_host', 'Azure.list_azure_activity_for_account', 'AzureNetwork.az_net_analytics', 'AzureNetwork.get_heartbeat_for_ip', 'AzureSentinel.get_bookmark_by_id', 'Heartbeatget_heartbeat_for_host', 'LinuxSyslog.all_syslog', 'LinuxSyslog.list_logon_failures', 'LinuxSyslog.sudo_activity', 'MultiDataSource.get_timeseries_decompose', 'Network.get_host_for_ip','Office365.list_activity_for_ip', 'SecurityAlert.list_alerts_for_ip', 'ThreatIntelligence.list_indicators_by_filepath', 'WindowsSecurity.get_parent_process', 'WindowsSecurity.list_host_events','WindowsSecurity.list_hosts_matching_commandline', 'WindowsSecurity.list_other_events']
```

**Pour obtenir de l’aide sur une requête en passant `?` en tant que paramètre** :

```python
# Get help about a query by passing "?" as a parameter
qry_prov.Azure.list_all_signins_geo("?")
```

Vous obtenez la sortie suivante :

```output
Help for 'list_all_signins_geo' query
=====================================
Query:  list_all_signins_geo
Data source:  AzureSentinel
Gets Signin data used by morph charts

Parameters
----------
add_query_items: str (optional)
    Additional query clauses
end: datetime (optional)
    Query end time
start: datetime (optional)
    Query start time
    (default value is: -5)
table: str (optional)
    Table name
    (default value is: SigninLogs)
Query:
     {table} | where TimeGenerated >= datetime({start}) | where TimeGenerated <= datetime({end}) | extend Result = iif(ResultType==0, "Sucess", "Failed") | extend Latitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).latitude) | extend Longitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).longitude)
```

**Pour afficher les tables et les requêtes dans une liste pouvant être filtrée et défilée** :

Passez à la cellule suivante, avec le code suivant, et exécutez-la :

```python
qry_prov.browse_queries()
```

Pour la requête sélectionnée, tous les paramètres obligatoires et facultatifs sont affichés, ainsi que le texte complet de la requête. Par exemple :

:::image type="content" source="media/notebook-get-started/view-tables-queries-in-list.png" alt-text="Capture d’écran des tables et des requêtes affichées dans une liste pouvant être filtrée et défilée.":::

Pour plus d’informations, consultez [Exécution d’une requête prédéfinie](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-a-pre-defined-query) dans la documentation MSTICPy.

> [!NOTE]
> Bien que vous ne puissiez pas exécuter de requêtes à partir du navigateur, vous pouvez copier et coller l’exemple à la fin de chaque requête pour l’exécuter ailleurs dans le notebook.
>

### <a name="run-queries-with-time-parameters"></a>Exécuter des requêtes avec des paramètres de temps

La plupart des requêtes nécessitent des paramètres de temps. Les chaînes de date/heure sont fastidieuses à taper, et leur modification à plusieurs endroits peut être sujette à des erreurs.

Chaque fournisseur de requêtes a des paramètres d’heure de début et de fin par défaut pour les requêtes. Ces paramètres de temps sont utilisés par défaut chaque fois que des paramètres d’heure sont appelés. Vous pouvez modifier l’intervalle de temps par défaut en ouvrant le contrôle `query_time`. Les modifications restent effectives jusqu’à ce que vous les modifiiez à nouveau.

Passez à la cellule suivante, avec le code suivant, et exécutez-la :

```python
# Open the query time control for your query provider
qry_prov.query_time
```

Définissez les heures `start` et `end` en fonction des besoins. Par exemple :

:::image type="content" source="media/notebook-get-started/set-time-parameters.png" alt-text="Capture d’écran de la définition des paramètres de temps par défaut pour les requêtes.":::

### <a name="run-a-query-using-the-built-in-time-range"></a>Exécuter une requête à l’aide de l’intervalle de temps intégré

Les résultats de la requête sont retournés en tant que [DataFrame Pandas](https://pandas.pydata.org), à savoir une structure de données tabulaires, comme une feuille de calcul ou une table de base de données. Vous pouvez utiliser des [fonctions pandas](https://pandas.pydata.org/docs/user_guide/10min.html) pour effectuer des opérations de filtrage et d’analyse supplémentaires sur les résultats de la requête.

La cellule de code suivante exécute une requête à l’aide des paramètres de temps par défaut du fournisseur de requêtes. Vous pouvez modifier cet intervalle et réexécuter la cellule de code pour interroger le nouvel intervalle de temps.

```python
# The time parameters are taken from the qry_prov time settings
# but you can override this by supplying explict "start" and "end" datetimes
signins_df = qry_prov.Azure.list_all_signins_geo()

# display first 5 rows of any results
# If there is no data, just the column headings display
signins_df.head()
```

La sortie affiche les cinq premières lignes de résultats. Par exemple :

:::image type="content" source="media/notebook-get-started/run-query-with-built-in-time-range.png" alt-text="Capture d’écran d’une requête exécutée avec l’intervalle de temps intégré.":::

S’il n’y a pas de données, seuls les en-têtes de colonnes s’affichent.

### <a name="run-a-query-using-a-custom-time-range"></a>Exécuter une requête à l’aide d’un intervalle de temps personnalisé

Vous pouvez également créer un objet de temps de requête et le transmettre à une requête en tant que paramètre, ce qui vous permet d’exécuter une requête unique pour un autre intervalle de temps sans affecter les valeurs par défaut du fournisseur de requête.

```python
# Create and display a QueryTime control.
time_range = nbwidgets.QueryTime()
time_range
```

Une fois que vous avez défini l’intervalle de temps souhaité, vous pouvez transmettre l’intervalle de temps à la fonction de requête, en exécutant le code suivant dans une cellule distincte du code précédent :

```python
signins_df = qry_prov.Azure.list_all_signins_geo(time_range)
signins_df.head()
```

Vous pouvez également transmettre des chaînes DateHeure ou date-heure Python à l’aide des paramètres `start` et `end` :

```python
from datetime import datetime, timedelta
q_end = datetime.utc.now()
q_start = end – timedelta(5)
signins_df = qry_prov.Azure.list_all_signins_geo(start=q_start, end=q_end)
```

### <a name="customize-your-queries"></a>Personnaliser vos requêtes

Vous pouvez personnaliser les requêtes intégrées en ajoutant une logique de requête supplémentaire ou exécuter des requêtes complètes à l’aide de la fonction `exec_query`.

Par exemple, la plupart des requêtes intégrées prennent en charge le paramètre `add_query_items`, que vous pouvez utiliser pour ajouter des filtres ou d’autres opérations aux requêtes.

1. Exécutez la cellule de code suivante pour ajouter une trame de données qui résume le nombre d’alertes par nom d’alerte :

    ```python
    from datetime import datetime, timedelta

    qry_prov.SecurityAlert.list_alerts(
       start=datetime.utcnow() - timedelta(28),
        end=datetime.utcnow(),
        add_query_items="| summarize NumAlerts=count() by AlertName"
    )
    ```

1. Transmettez une chaîne de requête KQL complète au fournisseur de requête. La requête s’exécute sur l’espace de travail connecté, et les données sont retournées en tant que DataFrame Panda. Exécutez :

    ```python
    # Define your query
    test_query = """
    OfficeActivity
    | where TimeGenerated > ago(1d)
    | take 10
    """

    # Pass the query to your QueryProvider
    office_events_df = qry_prov.exec_query(test_query)
    display(office_events_df.head())

    ```

Pour plus d'informations, consultez les pages suivantes :

- Les [Informations de référence de requête MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataQueries.html)
- [Exécution de requêtes prédéfinies MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-an-pre-defined-query)

### <a name="test-virustotal-and-geolite2"></a>Tester VirusTotal et GeoLite2

**Pour rechercher une adresse IP dans les données VirusTotal** :

Pour utiliser le renseignement sur les menaces pour déterminer si une adresse IP apparaît dans les données VirusTotal, exécutez la cellule avec le code suivant :

```python
# Create your TI provider – note you can re-use the TILookup provider (‘ti’) for
# subsequent queries - you don’t have to create it for each query
ti = TILookup()

# Look up an IP address
ti_resp = ti.lookup_ioc("85.214.149.236")

ti_df = ti.result_to_df(ti_resp)
ti.browse_results(ti_df, severities="all")
```

La sortie affiche des détails sur les résultats. Par exemple :

:::image type="content" source="media/notebook-get-started/test-virustotal-ip.png" alt-text="Capture d’écran d’une adresse IP apparaissant dans les données VirusTotal.":::

Veillez à faire défiler la liste vers le bas pour afficher les résultats complets. Pour plus d’informations, consultez [Recherches de renseignements sur les menaces dans MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html).

**Pour tester la recherche IP de géolocalisation** :

Pour obtenir les détails de géolocalisation d’une adresse IP à l’aide du service MaxMind, exécutez la cellule avec le code suivant :

```python
# create an instance of the GeoLiteLookup provider – this
# can be re-used for subsequent queries.
geo_ip = GeoLiteLookup()
raw_res, ip_entity = geo_ip.lookup_ip("85.214.149.236")
display(ip_entity[0])
```

La sortie affiche les informations de géolocalisation de l’adresse IP. Par exemple :

```output
ipaddress
{ 'AdditionalData': {},
  'Address': '85.214.149.236',
  'Location': { 'AdditionalData': {},
                'CountryCode': 'DE',
                'CountryName': 'Germany',
                'Latitude': 51.2993,
                'Longitude': 9.491,
                'Type': 'geolocation',
                'edges': set()},
  'ThreatIntelligence': [],
  'Type': 'ipaddress',
  'edges': set()}
```

> [!NOTE]
> La première fois que vous exécutez ce code, le pilote GeoLite doit télécharger sa base de données.
>

Pour plus d’informations, consultez la [Fournisseurs GeoIP MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html).

## <a name="configure-key-vault-settings"></a>Configurer les paramètres Key Vault

Cette section s’applique uniquement au stockage de secrets dans Azure Key Vault.

Lorsque vous stockez des secrets dans Azure Key Vault, vous devez d’abord créer le Key Vault dans le [portail de gestion Azure Key Vault global](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.KeyVault%2Fvaults).

Les paramètres obligatoires sont toutes les valeurs que vous récupérez à partir des propriétés du coffre, bien que certains puissent avoir des noms différents. Par exemple :

- **VaultName** s’affiche en haut à gauche de l’écran **Propriétés** d’Azure Key Vault
- **TenantId** s’affiche en tant qu'**ID de répertoire**
- **AzureRegion** s’affiche en tant qu’**emplacement**
- **Authority** est le cloud de votre service Azure.

Seules les valeurs **VaultName**, **TenantId** et **Authority** sont requises pour récupérer des secrets à partir du coffre. Les autres valeurs sont nécessaires si vous choisissez de créer un coffre à partir de MSTICPy. Pour plus d’informations, consultez [Spécification de secrets en tant que secrets Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets).

L’option **Utiliser KeyRing** est sélectionnée par défaut et vous permet de mettre en cache les informations d’identification Key Vault dans un KeyRing local. Pour plus d’informations, consultez la [documentation de KeyRing](https://keyring.readthedocs.io/en/latest/index.html).

> [!CAUTION]
> N’utilisez pas l’option **Utiliser KeyRing** si vous ne faites pas entièrement confiance au calcul de l’hôte sur lequel le notebook s’exécute.
>
> Dans notre cas, le *calcul* est le serveur hub Jupyter, où le noyau du notebook s’exécute, et pas nécessairement l’ordinateur sur lequel votre navigateur s’exécute. Si vous utilisez Azure ML, le *calcul* sera l’instance Capacité de calcul Machine Learning que vous avez sélectionnée. KeyRing effectue sa mise en cache sur l’hôte sur lequel le noyau du notebook s’exécute.
>

**Pour ajouter des paramètres Key Vault dans l’éditeur de paramètres MSTICPy** :

1. Passez à la cellule suivante, avec le code suivant, et exécutez-la :

    ```python
    mpedit.set_tab("Key Vault")
    mpedit
    ```

1. Entrez les détails de coffre de votre Key Vault. Par exemple :

    :::image type="content" source="media/notebook-get-started/add-kv-settings.png" alt-text="Capture d’écran de la section de configuration de Key Vault":::

1. Sélectionnez **Enregistrer**, puis **Enregistrer les paramètres**.

### <a name="test-key-vault"></a>Tester Key Vault

Pour tester votre coffre de clés, vérifiez si vous pouvez vous connecter et afficher vos secrets. Si vous n’avez pas ajouté de secret, vous ne verrez aucun détail. Le cas échéant, ajoutez un secret de test dans le coffre à partir du portail Azure Key Vault, puis vérifiez qu’il s’affiche dans Microsoft Sentinel.

Par exemple :

```Python
mpconfig = MpConfigFile()
mpconfig.refresh_mp_config()
mpconfig.show_kv_secrets()
```

> [!CAUTION]
> Ne laissez pas la sortie affichée dans le notebook que vous avez enregistré. Si la sortie contient des secrets réels, utilisez la commande **Effacer la sortie** du notebook avant d’enregistrer le notebook.
>
> Supprimez également les copies mises en cache du notebook. Par exemple, recherchez dans le sous-dossier **.ipynb_checkpoints** du répertoire de votre notebook, puis supprimez toutes les copies trouvées de ce notebook. L’enregistrement du notebook avec une sortie effacée doit remplacer la copie du point de contrôle.
>

Une fois que vous avez configuré Key Vault, vous pouvez utiliser le bouton **Télécharger dans KV** dans les sections des fournisseurs de données et des fournisseurs TI pour déplacer le paramètre sélectionné vers le coffre. MSTICPy génère un nom par défaut pour le secret en fonction du chemin d’accès du paramètre, tel que `TIProviders-VirusTotal-Args-AuthKey`.

Si la valeur est correctement téléchargée, le contenu du champ **Valeur** de l’éditeur de paramètres est supprimé et le paramètre sous-jacent est remplacé par une valeur d’espace réservé. MSTICPy l’utilise pour indiquer qu’il doit générer automatiquement le chemin d’accès Key Vault lors de la tentative de récupération de la clé.

Si vous avez déjà les secrets requis stockés dans un Key Vault, vous pouvez entrer le nom du secret dans le champ **Valeur**. Si le secret n’est pas stocké dans votre coffre par défaut (les valeurs spécifiées dans la section [Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html#key-vault)), vous pouvez spécifier le chemin d’accès **VaultName/SecretName**.

La récupération des paramètres d’un coffre dans un autre locataire n’est pas prise en charge actuellement. Pour plus d’informations, consultez [Spécification de secrets en tant que secrets Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets).

## <a name="specify-the-azure-cloud-and-azure-authentication-methods"></a>Spécifier le cloud Azure et les méthodes d’authentification Azure

Si vous utilisez un cloud Azure souverain ou gouvernemental, plutôt que le cloud Azure public ou global, vous devez sélectionner le cloud approprié dans vos paramètres. Pour la plupart des organisations, le cloud global est la valeur par défaut.

Vous pouvez également utiliser ces paramètres Azure pour définir les préférences par défaut pour le type d’authentification Azure.

**Pour spécifier le cloud Azure et les méthodes d’authentification Azure** :

1. Passez à la cellule suivante, avec le code suivant, et exécutez-la :

    ```python
    mpedit.set_tab("Azure")
    mpedit
    ```

1. Sélectionnez le cloud utilisé par votre organisation ou laissez l’option **global** sélectionnée par défaut.

1. Sélectionnez une ou plusieurs des méthodes suivantes :

    - **env** pour stocker vos informations d’identification Azure dans des variables d’environnement.
    - **msi** pour utiliser Managed Service Identity, une identité affectée à l’hôte ou à la machine virtuelle sur lequel le hub Jupyter s’exécute. MSI n’est actuellement pas pris en charge dans les instances Capacité de calcul Machine Learning Azure.
    - **cli** pour utiliser les informations d’identification d’une session Azure CLI authentifiée.
    - **interactive** pour utiliser le flux d’autorisation d’appareil interactif à l’aide d’un [code d’appareil à usage unique](#authenticate-to-your-microsoft-sentinel-workspace-from-your-notebook).

    > [!TIP]
    > Dans la plupart des cas, nous vous recommandons de sélectionner plusieurs méthodes, telles que **cli** et **interactive**. L’authentification Azure essaiera chacune des méthodes configurées dans l’ordre indiqué ci-dessus jusqu’à ce que l’une d’elles aboutisse.
    >

1. Sélectionnez **Enregistrer**, puis **Enregistrer les paramètres**.

Par exemple :

:::image type="content" source="media/notebook-get-started/settings-for-azure-gov-cloud.png" alt-text="Capture d’écran des paramètres définis pour le cloud Azure Government.":::

## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit les principes de base de l’utilisation de MSTICPy avec des notebooks Jupyter dans Microsoft Sentinel. Pour plus d’informations, consultez [Configurations avancées pour les notebooks Jupyter et MSTICPy dans Microsoft Sentinel](notebooks-msticpy-advanced.md).

Vous pouvez également essayer d’autres notebooks stockés dans le [référentiel GitHub de notebooks Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks), par exemple :

- [Présentation des fonctionnalités de cybersécurité](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/A%20Tour%20of%20Cybersec%20notebook%20features.ipynb)
- [Exemples de Machine Learning](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/Machine%20Learning%20in%20Notebooks%20Examples.ipynb)
- Série de notebooks de l'[Explorateur d’entités](https://github.com/Azure/Azure-Sentinel-Notebooks/), qui permettent une exploration approfondie des détails sur un hôte, un compte, une adresse IP et d’autres entités.

> [!TIP]
> Si vous utilisez le notebook décrit dans ce didacticiel dans un autre environnement Jupyter, vous pouvez utiliser n’importe quel noyau prenant en charge Python 3.6 ou version ultérieure.
>
> Pour utiliser des notebooks MSTICPy en dehors de Microsoft Sentinel et d’Azure Machine Learning (ML), vous devez également configurer votre environnement Python. Installez Python 3.6 ou version ultérieure avec la distribution Anaconda, qui comprend un grand nombre des packages requis.
>

### <a name="more-reading-on-msticpy-and-notebooks"></a>Lectures complémentaires sur MSTICPy et les notebooks

Le tableau suivant présente d’autres références pour en savoir plus sur MSTICPy, Microsoft Sentinel et les notebooks Jupyter.

|Objet  |Plus de références  |
|---------|---------|
|**MSTICPy**     |      - [Configuration de package MSTICPy](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [Éditeur de Paramètres MSTICPy](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [Configuration de votre environnement de bloc-notes](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb).<br>    - [Bloc-notes MPSettingsEditor](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb). <br><br>**Remarque** : Le référentiel GitHub `Azure-Sentinel-Notebooks` contient également un fichier *msticpyconfig.yaml* modèle avec des sections commentées, ce qui peut vous aider à comprendre les paramètres.      |
|**Microsoft Sentinel et notebooks Jupyter**     |      - [Jupyter Notebook : présentation](https://realpython.com/jupyter-notebook-introduction/)<br>    - [Documentation MSTICPy](https://msticpy.readthedocs.io/)<br>    - [Documentation sur les notebooks Microsoft Sentinel](notebooks.md)<br>    - [Le livre Jupyter Infosec](https://infosecjupyterbook.com/introduction.html)<br>    - [Procédure pas-à-pas du bloc-notes Linux Host Explorer](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [Pourquoi utiliser Jupyter pour les investigations de sécurité](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [Investigations de sécurité avec Microsoft Sentinel et les notebooks](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Documentation Pandas](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Documentation Bokeh](https://docs.bokeh.org/en/latest/)       |
|     |         |
