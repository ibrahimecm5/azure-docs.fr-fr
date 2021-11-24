---
title: Utiliser des notebooks avec Microsoft Sentinel pour la chasse sécuritaire
description: Cet article décrit comment utiliser des notebooks avec les capacités de chasse de Microsoft Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: ace7df5954d288bb46430c84ccde8d2822f08115
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521222"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Utiliser Jupyter Notebook pour rechercher des menaces de sécurité

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel repose sur le magasin de données. Il associe des capacités d’interrogation hautes performances et un schéma dynamique et s’adapte à des volumes importants de données. Le portail Azure et tous les outils Microsoft Sentinel utilisent une API commune pour accéder à ce magasin de données.

La même API est également disponible pour les outils externes tels que les notebooks [Jupyter](https://jupyter.org/) et Python. Bien que de nombreuses tâches courantes puissent être effectuées dans le portail, Jupyter étend l’étendue de ce que vous pouvez faire avec ces données. Il combine une programmabilité totale à une vaste collection de bibliothèques pour l’apprentissage automatique, la visualisation et l’analyse des données. Ces attributs transforment Jupyter en un outil essentiel pour les scénarios de recherche et d’enquête relatifs à la sécurité.

Par exemple, utilisez les notebooks pour :

- **Effectuer des analyses** qui ne sont pas fournies prêtes à l’emploi dans Microsoft Sentinel, telles que certaines fonctionnalités de Machine Learning Python
- **Créer des visualisations de données** qui ne sont pas fournies prêtes à l’emploi dans Microsoft Sentinel, telles que des chronologies personnalisées et des arborescences de processus
- **Intégrer des sources de données** qui ne proviennent pas de Microsoft Sentinel, telles qu’un jeu de données local

Nous avons intégré l’expérience de Jupyter dans le portail Azure , pour que vous puissiez créer et exécuter facilement des notebooks pour analyser vos données. La bibliothèque *Kqlmagic* fournit le ciment qui vous permet d’extraire des requêtes [KQL](https://kusto.azurewebsites.net/docs/kusto/query/index.html) de Microsoft Sentinel et de les exécuter directement dans un notebook.

Plusieurs notebooks, développés par certains des analystes de sécurité de Microsoft, sont intégrés à Microsoft Sentinel :

- Quelques-uns d’entre eux sont conçus pour un scénario spécifique et peuvent être utilisés en tant que tel.
- D’autres sont des exemples pour illustrer les techniques et les fonctionnalités que vous pouvez copier ou adapter pour les utiliser dans vos propres notebooks.

D’autres notebooks peuvent également être importés à partir du [référentiel GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/).

## <a name="notebook-components"></a>Composants Notebook

Notebooks a deux composants :

- **L’interface basée sur un navigateur** où vous entrez et exécutez des requêtes et du code, et où les résultats de l’exécution sont affichés.
- Un **noyau* qui est responsable de l’analyse et de l’exécution du code.

Le noyau du notebook Microsoft Sentinel s’exécute sur une machine virtuelle Azure. Plusieurs options de licence existent pour utiliser des machines virtuelles plus puissantes si vos notebooks incluent des modèles d’apprentissage automatique complexes.

Les notebooks Microsoft Sentinel utilisent de nombreuses bibliothèques Python populaires telles que *pandas*, *matplotlib*, *bokeh* et bien d’autres. Vous avez le choix entre un grand nombre d’autres packages Python, couvrant des domaines tels que ceux énoncés ci-dessous :

- visualisations et graphiques
- traitement de données et analyse
- statistiques et calculs numériques
- Machine Learning et Deep Learning

Pour éviter d’avoir à saisir ou coller du code complexe et répétitif dans les cellules d’un notebook, la plupart des notebooks Python s’appuient sur des bibliothèques tierces appelées *packages*. Pour utiliser un package dans un notebook, vous devez à la fois installer et importer le package. Les packages les plus courants sont préinstallés dans Capacité de calcul Azure ML. Assurez-vous d’importer le package, ou la partie correspondante du package, comme un module, un fichier, une fonction ou une classe.

Les notebooks Microsoft Sentinel utilisent un package Python appelé [MSTICPy](https://github.com/Microsoft/msticpy/), qui est une collection d’outils de cybersécurité pour l’extraction, l’analyse, l’enrichissement et la visualisation de données. 

Les outils de MSTICPy sont conçus spécialement pour faciliter la création de notebooks pour la chasse et l’investigation, et nous travaillons activement pour vous proposer de nouvelles fonctionnalités et améliorations. Pour plus d'informations, consultez les pages suivantes :

- [Documentation relatives aux outils de sécurité MSTIC Jupyter et Python](https://msticpy.readthedocs.io/)
- [Tutoriel : Prendre en main les notebooks Jupyter et MSTICPy dans Microsoft Sentinel](notebook-get-started.md)
- [Configurations avancées pour les notebooks Jupyter et MSTICPy dans Microsoft Sentinel](notebooks-msticpy-advanced.md)

Le [référentiel GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/) est l’emplacement de tous les futurs notebooks Microsoft Sentinel créés par Microsoft ou fournis par la communauté.

## <a name="manage-access-to-microsoft-sentinel-notebooks"></a>Gérer l’accès aux notebooks Microsoft Sentinel

Pour utiliser les notebooks Jupyter dans Microsoft Sentinel, vous devez d’abord disposer des autorisations appropriées, en fonction de votre rôle utilisateur.

Bien que vous puissiez exécuter les notebooks Microsoft Sentinel dans JupyterLab ou Jupyter classic, dans Microsoft Sentinel, les notebooks sont exécutés sur une plateforme [Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md) (Azure ML). Pour exécuter des notebooks dans Microsoft Sentinel, vous devez disposer d’un accès approprié à l’espace de travail Microsoft Sentinel et à un [espace de travail Azure ML](../machine-learning/concept-workspace.md).

|Autorisation  |Description  |
|---------|---------|
|**Autorisations Microsoft Sentinel**     |   Comme pour les autres ressources Microsoft Sentinel, pour accéder aux notebooks sur le panneau Notebooks Microsoft Sentinel, un rôle Lecteur Microsoft Sentinel, Répondeur Microsoft Sentinel ou Contributeur Microsoft Sentinel est nécessaire. <br><br>Pour plus d’informations, consultez [Autorisations dans Microsoft Sentinel](roles.md).|
|**Autorisations d’Azure Machine Learning**     | Un espace de travail Azure Machine Learning est une ressource Azure. Comme toute autre ressource Azure, la création d'un espace de travail Azure Machine Learning s'accompagne de rôles par défaut. Vous pouvez ajouter des utilisateurs à l’espace de travail et leur attribuer un de ces rôles intégrés. Pour plus d’informations, voir [Rôles par défaut Azure Machine Learning](../machine-learning/how-to-assign-roles.md) et [Rôles intégrés Azure](../role-based-access-control/built-in-roles.md). <br><br>   **Important** : L'accès en fonction du rôle peut être limité à plusieurs niveaux dans Azure. Par exemple, un utilisateur disposant d’un accès propriétaire à un espace de travail peut ne pas disposer d'un accès propriétaire à un groupe de ressources contenu dans cet espace de travail. Pour plus d’informations, consultez [Fonctionnement du contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md). <br><br>Si vous êtes propriétaire d’un espace de travail Azure ML, vous pouvez ajouter et supprimer des rôles pour cet espace de travail et assigner des rôles aux utilisateurs. Pour plus d'informations, consultez les pages suivantes :<br>    - [Portail Azure](../role-based-access-control/role-assignments-portal.md)<br>    - [PowerShell](../role-based-access-control/role-assignments-powershell.md)<br>    - [Interface de ligne de commande Azure](../role-based-access-control/role-assignments-cli.md)<br>   - [REST API](../role-based-access-control/role-assignments-rest.md)<br>    - [Modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md)<br> - [Interface CLI Azure Machine Learning](../machine-learning/how-to-assign-roles.md#manage-workspace-access)<br><br>Si les rôles intégrés ne suffisent pas, vous pouvez également créer des rôles personnalisés. Les rôles personnalisés peuvent disposer d'autorisations en lecture, écriture, suppression et calcul dans cet espace de travail. Vous pouvez rendre le rôle disponible au niveau d’un espace de travail spécifique, d’un groupe de ressources spécifique ou d’un abonnement spécifique. Pour plus d'informations, consultez [Créer un rôle personnalisé](../machine-learning/how-to-assign-roles.md#create-custom-role). |
|     |         |

## <a name="create-an-azure-ml-workspace-from-microsoft-sentinel"></a>Créer un espace de travail Azure ML à partir de Microsoft Sentinel

Cette procédure décrit comment créer un espace de travail Azure ML à partir de Microsoft Sentinel pour vos notebooks Microsoft Sentinel.

**Pour créer votre espace de travail** :

1. À partir du portail Azure, accédez à **Microsoft Sentinel** > **Gestion des menaces** > **Notebooks**, puis sélectionnez **Créer un espace de travail AML**.

1. Entrez les détails suivants et sélectionnez **Suivant**.

    |Champ|Description|
    |--|--|
    |**Abonnement**|Sélectionnez l’abonnement Azure que vous souhaitez utiliser.|
    |**Groupe de ressources**|Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources contient les ressources associées d’une solution Azure.|
    |**Nom de l’espace de travail**|Entrez un nom unique qui identifie votre espace de travail. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail.|
    |**Région**|Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données pour créer votre espace de travail.|
    |**Compte de stockage**| Un compte de stockage est utilisé comme magasin de données par défaut pour l’espace de travail. Vous pouvez créer une ressource de stockage Azure ou en sélectionner une existante dans votre abonnement.|
    |**KeyVault**| Un coffre de clés est utilisé pour stocker les secrets et autres informations sensibles nécessaires à l’espace de travail. Vous pouvez créer une ressource Azure Key Vault ou en sélectionner une existante dans votre abonnement.|
    |**Application Insights**| L’espace de travail utilise Azure Application Insights pour stocker des informations de surveillance sur vos modèles déployés. Vous pouvez créer une ressource Azure Application Insights ou en sélectionner une existante dans votre abonnement.|
    |**Registre de conteneurs**| Un registre de conteneurs est utilisé pour enregistrer les images du docker utilisées dans les formations et les déploiements. Pour réduire les coûts, une ressource Azure Container Registry est créée uniquement après la création de votre première image. Vous pouvez également choisir de créer la ressource maintenant ou d’en sélectionner une existante dans votre abonnement, ou de sélectionner **Aucun** si vous ne souhaitez pas utiliser un registre de conteneurs.|
    | | |

1. Sous l’onglet **Mise en réseau**, indiquez si vous souhaitez connecter votre espace de travail à l’aide d’un point de terminaison public ou un point de terminaison privé que vous configurez. Si votre espace de travail Microsoft Sentinel possède un point de terminaison public, nous vous recommandons d’utiliser un point de terminaison public pour votre espace de travail Azure ML afin d’éviter tout problème potentiel dans la communication réseau. Quand vous avez terminé, sélectionnez **Vérifier + créer**.

1. Dans l’onglet **Vérifier + créer**, passez en revue les informations pour vérifier qu’elles sont correctes, puis sélectionnez **Créer** pour démarrer le déploiement de votre espace de travail. Par exemple :

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="Vérifier + créer votre espace de travail Machine Learning à partir de Microsoft Sentinel.":::

    La création de votre espace de travail dans le cloud peut prendre plusieurs minutes. Pendant ce temps, la page **Vue d’ensemble** de l’espace de travail affiche l’état actuel du déploiement et se met à jour une fois le déploiement terminé.

1. Une fois votre déploiement terminé, vous pouvez revenir aux **notebooks** Microsoft Sentinel et lancer des notebooks à partir de votre nouvel espace de travail Azure ML.

    Si vous disposez de plusieurs notebooks, veillez à sélectionner un espace de travail AML par défaut à utiliser lors du lancement de vos notebooks. Par exemple :

    :::image type="content" source="media/notebooks/default-machine-learning.png" alt-text="Sélectionnez un espace de travail AML par défaut pour vos notebooks.":::


## <a name="launch-a-notebook-in-your-azure-ml-workspace"></a>Lancer un notebook à l’aide de votre espace de travail Azure ML

Une fois que vous avez créé un espace de travail AML, commencez à lancer les notebooks de votre espace de travail Azure ML à partir de Microsoft Sentinel.

> [!NOTE]
> Vous pouvez afficher un notebook sous la forme d’un document statique, comme dans le renderer de notebook statique intégré de GitHub. Toutefois, pour exécuter du code dans un notebook, vous devez joindre le notebook à un processus serveur principal appelé noyau Jupyter. Le noyau exécute le code et contient l’ensemble des variables et objets créés par le code. Le navigateur est la visionneuse de ces données.
>
> Dans Azure ML, le noyau s’exécute sur une machine virtuelle appelée Capacité de calcul Azure ML. L’instance de calcul peut prendre en charge l’exécution de plusieurs notebooks à la fois.
>

**Pour lancer votre notebook à partir de Microsoft Sentinel** :

1. À partir du portail Azure, accédez à **Microsoft Sentinel** > **Gestion des menaces** > **Notebooks**, où vous pouvez voir les notebooks fournis par Microsoft Sentinel.

    > [!TIP]
    > En haut de la page **Notebooks**, sélectionnez **Guides & commentaires** pour afficher plus de ressources et plus d’aide sur un voler sur la droite.

1. Sélectionnez un notebook pour afficher sa description, les types de données requis et les sources de données.

    Lorsque vous avez trouvé le notebook que vous souhaitez utiliser, sélectionnez **Enregistrer le notebook** pour le cloner dans votre propre espace de travail.

    Changez le nom selon les besoins. Si le notebook existe déjà dans votre espace de travail, vous avez la possibilité de le remplacer ou d’en créer un nouveau.

    :::image type="content" source="media/notebooks/save-notebook.png" alt-text="Enregistrez un bloc-notes pour le cloner dans votre propre espace de travail.":::

1. Une fois le notebook enregistré, le bouton **Enregistrer le notebook** est remplacé par le bouton **Lancer le notebook**. Sélectionnez **Lancer le notebook** pour l’ouvrir dans votre espace de travail AML.

    Par exemple :

    :::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="Lancez votre notebook dans votre espace de travail AML.":::

1. En haut de la page, sélectionnez une instance de **calcul** à utiliser pour votre serveur de notebook.

    Si vous n’avez pas d’instance de calcul, [créez-en une](../machine-learning/how-to-create-manage-compute-instance.md?tabs=#use-the-script-in-the-studio). Si votre instance de calcul est arrêtée, assurez-vous de la démarrer. Pour plus d’informations, consultez [Exécuter un notebook dans Azure Machine Learning Studio](../machine-learning/how-to-run-jupyter-notebooks.md).

    Vous seul pouvez voir et utiliser les instances de calcul que vous créez. Vos fichiers utilisateur sont stockés séparément de la machine virtuelle, et partagés entre toutes les instances de calcul dans l’espace de travail.

    > [!TIP]
    > Si vous créez une nouvelle instance de calcul afin de tester vos notebooks, créez votre instance de calcul avec la catégorie **Usage général**.
    >
    > Le noyau est également affiché en haut à droite de votre fenêtre Azure ML. Si le noyau dont vous avez besoin n’est pas sélectionné, choisissez une autre version dans la liste déroulante.
    >

1. Une fois votre serveur de notebook créé et démarré, vous pouvez commencer à exécuter les cellules de votre notebook. Dans chaque cellule, sélectionnez l’icône **Exécuter** pour exécuter le code de votre notebook.

    Pour plus d’informations, consultez [Raccourcis du mode de commande](../machine-learning/how-to-run-jupyter-notebooks.md).

1. Si votre notebook se bloque ou si vous voulez recommencer, vous pouvez redémarrer le noyau et réexécuter les cellules du notebook depuis le début. Sélectionnez **Opérations du noyau** > **Redémarrer le noyau**. Par exemple :

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="Redémarrez un noyau de notebook.":::

    > [!IMPORTANT]
    > Le redémarrage du noyau efface toutes les variables et tout autre état. Après le redémarrage, vous devez réexécuter les cellules d’initialisation et d’authentification.
    >

## <a name="run-code-in-your-notebook"></a>Exécuter du code dans votre notebook

Dans un notebook :

- Les cellules **Markdown** contiennent du texte, y compris du code HTML, et des images statiques.
- Les cellules de **code** contiennent du code. Après avoir sélectionné une cellule de code, exécutez le code dans la cellule en sélectionnant l’icône **Lecture** à gauche de la cellule, ou en appuyant sur **MAJ + ENTRÉE**.

> [!IMPORTANT]
> Exécutez toujours les cellules de code du notebook dans l’ordre. Ignorer des cellules peut entraîner des erreurs.
>

Par exemple, exécutez la cellule de code suivante dans votre notebook :

```python
# This is your first code cell. This cell contains basic Python code.

# You can run a code cell by selecting it and then selecting
# the Play button to the left of the cell, or by pressing SHIFT+ENTER.
# Code output displays below the code.

print("Congratulations, you just ran this code cell")

y = 2 + 2

print("2 + 2 =", y)

```

L’exemple de code ci-dessus génère cette sortie :

```python
Congratulations, you just ran this code cell

2 + 2 = 4
```

Les variables définies dans une cellule de code du notebook persistent entre les cellules, de sorte que vous pouvez lier des cellules. Par exemple, la cellule de code suivante utilise la valeur `y` de la cellule précédente :

```python
# Note that output from the last line of a cell is automatically
# sent to the output cell, without needing the print() function.

y + 2
```

La sortie est la suivante :

```output
6
```

## <a name="download-all-microsoft-sentinel-notebooks"></a>Télécharger tous les notebooks Microsoft Sentinel

Cette section décrit comment utiliser Git pour télécharger tous les notebooks disponibles dans le [référentiel GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/), de l’intérieur d’un notebook Microsoft Sentinel, directement dans votre espace de travail Azure ML.

Le fait de stocker les notebooks Microsoft Sentinel dans votre espace de travail Azure ML vous permet de les mettre à jour facilement.

1. À partir d’un notebook Microsoft Sentinel, entrez le code suivant dans une cellule vide, puis exécutez la cellule :

    ```python
    !git clone https://github.com/Azure/Azure-Sentinel-Notebooks.git azure-sentinel-nb
    ```

    Une copie du contenu du référentiel GitHub est créée dans le répertoire **azure-Sentinel-nb** sur votre dossier utilisateur dans votre espace de travail Azure ML.

1. Dans ce dossier, copiez les notebooks que vous voulez vers votre répertoire de travail.

1. Pour mettre à jour vos notebooks avec toutes les modifications récentes à partir de GitHub, exécutez :

    ```python
    !cd azure-sentinel-nb && git pull
    ```

## <a name="troubleshooting"></a>Résolution des problèmes

En général, un notebook crée un noyau ou s’y attache naturellement, et vous n’avez pas besoin d’apporter des modifications manuelles. Si vous obtenez des erreurs, ou si le notebook ne semble pas fonctionner, vous devrez peut-être vérifier la version et l’état du noyau.

Si vous rencontrez des problèmes avec vos notebooks, reportez-vous à la [résolution des problèmes de notebook Azure Machine Learning](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting).

### <a name="force-caching-for-user-accounts-and-credentials-between-notebook-runs"></a>Forcer la mise en cache des comptes d’utilisateur et des informations d’identification entre les exécutions du notebook

Par défaut, les comptes d’utilisateur et les informations d’identification ne sont pas mis en cache entre les exécutions du notebook, même pour la même session.

**Pour forcer la mise en cache pendant la durée de votre session** :

1. Authentifiez-vous à l’aide d’Azure CLI. Dans une cellule vide du notebook, entrez et exécutez le code suivant :

    ```python
    !az login
    ```

    Vous obtenez la sortie suivante :

    ```python
    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the 9-digit device code to authenticate.
    ```

1. Sélectionnez et copiez le jeton de neuf caractères dans la sortie, puis sélectionnez l’URL `devicelogin` pour accéder à la page indiquée. 

1. Collez le jeton dans la boîte de dialogue et poursuivez la connexion quand vous y êtes invité.

    Une fois la connexion terminée, la sortie suivante s’affiche :

    ```python
    Subscription <subscription ID> 'Sample subscription' can be accessed from tenants <tenant ID>(default) and <tenant ID>. To select a specific tenant when accessing this subscription, use 'az login --tenant TENANT_ID'.

> [!NOTE]
> The following tenants don't contain accessible subscriptions. Use 'az login --allow-no-subscriptions' to have tenant level access.
>
> ```
> <tenant ID> 'foo'
><tenant ID> 'bar'
>[
> {
>    "cloudName": "AzureApp",
>    "homeTenantId": "<tenant ID>",
>    "id": "<ID>",
>    "isDefault": true,
>    "managedByTenants": [
>    ....
>```
>
### Error: *Runtime dependency of PyGObject is missing*

If the *Runtime dependency of PyGObject is missing* error appears when you load a query provider, try troubleshooting using the following steps:

1. Proceed to the cell with the following code and run it:

    ```python
    qry_prov = QueryProvider("AzureSentinel")
    ```

    Un avertissement semblable au suivant s’affiche, indiquant une dépendance Python manquante (`pygobject`) :

    ```output
    Runtime dependency of PyGObject is missing.

    Depends on your Linux distribution, you can install it by running code similar to the following:
    sudo apt install python3-gi python3-gi-cairo gir1.2-secret-1

    If necessary, see PyGObject's documentation: https://pygobject.readthedocs.io/en/latest/getting_started.html

    Traceback (most recent call last):
      File "/anaconda/envs/azureml_py36/lib/python3.6/site-packages/msal_extensions/libsecret.py", line 21, in <module>
    import gi  # https://github.com/AzureAD/microsoft-authentication-extensions-for-python/wiki/Encryption-on-Linux
    ModuleNotFoundError: No module named 'gi'
    ```

1. Utilisez le script [aml-compute-setup.sh](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/HowTos/aml-compute-setup.sh), situé dans le référentiel GitHub des notebooks Microsoft Sentinel, pour installer automatiquement le `pygobject` dans tous les notebooks et environnements Anaconda sur l’instance de calcul.

> [!TIP]
> Vous pouvez également corriger cet avertissement en exécutant le code suivant à partir d’un notebook :
>
> ```python
> !conda install --yes -c conda-forge pygobject
> ```
>


## <a name="next-steps"></a>Étapes suivantes

Intégrez votre expérience de notebook avec l’analytique Big Data dans Azure Synapse. Pour plus d’informations, consultez [Intégrer des notebooks avec Azure Synapse (préversion publique)](notebooks-with-synapse.md).

Les autres notebooks partagés dans le [référentiel GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks) sont conçus comme des outils utiles, des illustrations et des exemples de code que vous pouvez utiliser lors du développement de vos propres notebooks.

Nous apprécions les commentaires, qu’il s’agisse de suggestions, de demandes de fonctionnalités, de contribution aux notebooks, de signalements de bogues ou d’améliorations et d’ajouts aux notebooks existants. Rendez-vous sur le [référentiel GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel) pour créer un problème ou dupliquer (fork) et charger une contribution.

- **Pour en savoir plus** sur l’utilisation des notebooks dans la chasse et l’investigation des menaces, explorez certains modèles de notebook (par exemple, [**Analyse des informations d’identification dans Azure Log Analytics**](https://www.youtube.com/watch?v=OWjXee8o04M) et **Investigation guidée - Traiter les alertes**).

    Vous trouverez d’autres modèles de notebook dans l’onglet Microsoft Sentinel > **Notebooks** > **Modèles**.

- **Vous trouverez d’autres notebooks** dans le [référentiel GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks) :

  - Le répertoire [`Sample-Notebooks`](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) comprend des exemples de notebook enregistrés avec des données que vous pouvez utiliser pour afficher la sortie prévue.

  - Le répertoire [`HowTos`](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) comprend des notebooks qui décrivent des concepts tels que la définition de la version par défaut de Python, la création de signets Microsoft Sentinel à partir d’un notebook, et bien plus encore.

Pour plus d'informations, consultez les pages suivantes :

- [Tutoriel : Prendre en main les notebooks Jupyter et MSTICPy dans Microsoft Sentinel](notebook-get-started.md)
- [Tutoriel : Notebooks Microsoft Sentinel – Prise en main](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks) (vidéo)
- [Tutoriel : Modifier et exécuter des notebooks Jupyter sans quitter Azure ML studio](https://www.youtube.com/watch?v=AAj-Fz0uCNk) (vidéo)
- [Webinaire : Notions de base des notebooks Microsoft Sentinel](https://www.youtube.com/watch?v=rewdNeX6H94)
- [Hunt for threats with in Azure Sentinel Preview](hunting.md) (Rechercher des menaces dans Azure Sentinel en préversion)
- [Keep track of data during hunting](bookmarks.md) (Suivre des données pendant la recherche)
- [Jupyter, msticpy et Microsoft Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
