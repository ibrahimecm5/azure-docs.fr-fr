---
title: Activer le studio Azure Machine Learning dans un réseau virtuel
titleSuffix: Azure Machine Learning
description: Découvrez comment configurer Azure Machine Learning studio permet d’accéder aux données stockées au sein d’un réseau virtuel.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/10/2021
ms.custom: contperf-fy20q4, tracking-python, security
ms.openlocfilehash: b978955c375ff30f677a395ea549276b960a80d9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293095"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Utiliser le studio Azure Machine Learning dans un réseau virtuel Azure

Dans cet article, vous allez apprendre à utiliser le studio Azure Machine Learning dans un réseau virtuel. Le studio inclut des fonctionnalités telles que AutoML, le concepteur et l’étiquetage des données. 

Certaines des fonctionnalités du studio sont désactivées par défaut dans un réseau virtuel. Pour réactiver ces fonctionnalités, vous devez activer l’identité managée pour les comptes de stockage que vous prévoyez d’utiliser dans le studio. 

Les opérations suivantes sont désactivées par défaut dans un réseau virtuel :

* Aperçu des données dans Studio
* Visualisation des données dans le concepteur
* Déploiement d’un modèle dans le concepteur
* Envoi d’une expérience AutoML
* Démarrage d’un projet d’étiquetage

Studio prend en charge la lecture de données à partir des types de magasins de données suivants sur un réseau virtuel :

* Compte Stockage Azure (blob et fichier)
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> - Accordez au studio l’accès aux données stockées au sein d’un réseau virtuel.
> - Accédez au studio à partir d’une ressource à l’intérieur d’un réseau virtuel.
> - Découvrez l'impact du studio sur la sécurité du stockage.

> [!TIP]
> Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :
>
> * [Présentation du réseau virtuel](how-to-network-security-overview.md)
> * [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
> * [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
> * [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
> * [Utiliser le DNS personnalisé](how-to-custom-dns.md)
> * [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)


## <a name="prerequisites"></a>Prérequis

+ Lisez [Vue d’ensemble de la sécurité réseau](how-to-network-security-overview.md) pour comprendre l’architecture des réseaux virtuels et les scénarios associés.

+ Un réseau virtuel et un sous-réseau préexistants à utiliser.

+ Un [espace de travail Azure Machine Learning avec un point de terminaison privé](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) existant.

+ Un [compte de stockage Azure existant a ajouté votre réseau virtuel](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts).

## <a name="limitations"></a>Limites

### <a name="azure-storage-account"></a>Compte Stockage Azure

Il existe un problème connu où le magasin de fichiers par défaut ne crée pas automatiquement le dossier `azureml-filestore`, qui est nécessaire pour soumettre des expériences AutoML. Ce problème se produit quand des utilisateurs importent un magasin de fichiers et le définissent comme magasin de fichiers par défaut lors de la création de l’espace de travail.

Pour éviter ce problème, vous avez deux options : 1) utiliser le magasin de fichiers par défaut créé automatiquement lors de la création de l’espace de travail ; 2) importer votre propre magasin de fichiers en veillant à ce que celui-ci soit à l’extérieur du réseau virtuel pendant la création de l’espace de travail. Une fois l’espace de travail créé, ajoutez le compte de stockage au réseau virtuel.

Pour résoudre ce problème, supprimez le compte de magasin de fichiers du réseau virtuel, puis rajoutez-le au réseau virtuel.

### <a name="designer-sample-pipeline"></a>Exemple de pipeline de concepteur

Il existe un problème connu à cause duquel l’utilisateur ne peut pas exécuter l’exemple de pipeline dans la page d’accueil du concepteur. L’exemple de jeu de données utilisé dans l’exemple de pipeline est le jeu de données Azure Global, qui ne convient pas pour tout environnement de réseau virtuel.

Pour résoudre ce problème, vous pouvez utiliser un espace de travail public pour exécuter un exemple de pipeline afin de savoir comment utiliser le concepteur, puis remplacer l’exemple de jeu de données par votre propre jeu de données dans l’espace de travail au sein du réseau virtuel.

## <a name="datastore-azure-storage-account"></a>Magasin de données : compte Stockage Azure

Procédez comme suit pour activer l’accès aux données stockées dans le Stockage Blob et le Stockage Fichier Azure :

> [!TIP]
> La première étape n’est pas requise pour le compte de stockage par défaut de l’espace de travail. Toutes les autres étapes sont requises pour *tout* compte de stockage se trouvant derrière le réseau virtuel et utilisé par l’espace de travail, notamment le compte de stockage par défaut.

1. **Si le compte de stockage est le stockage *par défaut* pour votre espace de travail, ignorez cette étape**. Si ce n’est pas le cas, **accordez à l’identité managée de l’espace de travail le rôle 'Lecteur des données blob du stockage'** pour le compte de stockage Azure afin qu’il puisse lire les données à partir du stockage blob.

    Pour plus d’informations, voir le rôle intégré [Lecteur des données blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader).

1. **Accordez à l’identité managée de l’espace de travail le rôle 'Lecteur' pour les points de terminaison privés du stockage**. Si votre service de stockage utilise un __point de terminaison privé__, accordez à l’identité managée de l’espace de travail l’accès **Lecteur** au point de terminaison privé. L’identité mangée de l’espace de travail dans Azure AD a le même nom que votre espace de travail Azure Machine Learning.

    > [!TIP]
    > Votre compte de stockage peut avoir plusieurs points de terminaison privés. Par exemple, un compte de stockage peut avoir un point de terminaison privé distinct pour les objets blob, les fichiers et les dfs (Azure Data Lake Storage Gen2). Ajoutez l’identité managée à tous ces points de terminaison.

    Pour plus d’informations, voir le rôle intégré [Lecteur](../role-based-access-control/built-in-roles.md#reader).

   <a id='enable-managed-identity'></a>
1. **Activez l’authentification via une identité managée pour les comptes de stockage par défaut**. Chaque espace de travail Azure Machine Learning dispose de deux comptes de stockage par défaut : un compte de stockage BLOB par défaut et un compte de magasin de fichiers par défaut, qui sont définis lors de la création de votre espace de travail. Vous pouvez également définir de nouvelles valeurs par défaut dans la page de gestion **Magasin de données**.

    ![Capture d’écran montrant où se trouvent les magasins de données par défaut](./media/how-to-enable-studio-virtual-network/default-datastores.png)

    Le tableau suivant décrit les raisons pour lesquelles l’authentification via l’identité managée est utilisée pour les comptes de stockage par défaut de votre espace de travail.

    |Compte de stockage  | Notes  |
    |---------|---------|
    |Stockage blob par défaut de l’espace de travail| Stocke les ressources de modèle à partir du concepteur. Activez l’authentification via une identité managée sur ce compte de stockage pour déployer des modèles dans le concepteur. <br> <br> Vous pouvez visualiser et exécuter un pipeline de concepteur s’il utilise un magasin de données non défini par défaut qui a été configuré pour utiliser l’identité managée. Toutefois, si vous essayez de déployer un modèle entraîné sans que l’identité managée soit activée sur le magasin de données par défaut, le déploiement échoue, quelles que soient les autres magasins de données en cours d’utilisation.|
    |Magasin de fichiers par défaut de l’espace de travail| Stocke les ressources d’expérimentation AutoML. Activez l’authentification via une identité managée sur ce compte de stockage pour soumettre des expériences AutoML. |

1. **Configurez des magasins de données pour utiliser l’authentification via une identité managée**. Après avoir ajouté un compte de stockage Azure à votre réseau virtuel avec un [point de terminaison de service](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts) ou un [point de terminaison privé](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts), vous devez configurer votre magasin de données afin qu’il utilise l’authentification via une [identité managée](../active-directory/managed-identities-azure-resources/overview.md). Ainsi, le studio peut accéder aux données de votre compte de stockage.

    Azure Machine Learning utilise des [magasins de données](concept-data.md#datastores) pour se connecter aux comptes de stockage. Lors de la création d’un magasin de données, procédez comme suit pour configurer un magasin de données afin d’utiliser l’authentification via une identité managée :

    1. Dans Studio, sélectionnez __Magasins de données__.

    1. Pour mettre à jour un magasin de données existant, sélectionnez-le et sélectionnez __Mettre à jour les informations d’identification__.

        Pour créer un magasin de données, sélectionnez __+ Nouveau magasin de données__.

    1. Dans les paramètres de magasin de données, sélectionnez __Oui__ pour __Utiliser l’identité managée d’espace de travail pour afficher un aperçu des données et les profiler dans Azure Machine Learning Studio__.

        ![Capture d’écran montrant comment activer l’identité managée de l’espace de travail](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

    Ces étapes ajoutent l’identité managée de l’espace de travail en tant que __Lecteur__ au nouveau service de stockage à l’aide du contrôle d’accès en fonction du rôle Azure (Azure RBAC). L’accès __lecteur__ permet à l’espace de travail d’afficher la ressource, mais pas d’apporter des modifications.

## <a name="datastore-azure-data-lake-storage-gen1"></a>Magasin de données : Azure Data Lake Storage Gen1

Lorsque vous utilisez Azure Data Lake Storage Gen1 en tant que magasin de données, vous ne pouvez utiliser que des listes de contrôle d’accès de type POSIX. Vous pouvez accorder à l’identité managée de l’espace de travail l’accès aux ressources, comme pour tout autre principal de sécurité. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="datastore-azure-data-lake-storage-gen2"></a>Magasin de données : Azure Data Lake Storage Gen2

Lorsque vous utilisez Azure Data Lake Storage Gen2 en tant que magasin de données, vous pouvez utiliser des listes de contrôle d’accès (ACL) de type Azure RBAC et POSIX pour contrôler l’accès aux données au sein d’un réseau virtuel.

**Pour utiliser un RBAC Azure**, suivez les étapes décrites dans la section [Magasin de données : compte Stockage Azure](#datastore-azure-storage-account) de cet article. Data Lake Storage Gen2 est basé sur le service Stockage Azure ; par conséquent, les mêmes étapes s’appliquent lorsque vous utilisez Azure RBAC.

**Pour utiliser des listes de contrôle d’accès**, vous pouvez accorder l’accès à l’identité managée de l’espace de travail comme tout autre principal de sécurité. Pour plus d’informations, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

## <a name="datastore-azure-sql-database"></a>Magasin de données : Azure SQL Database

Pour accéder aux données stockées dans une base de données Azure SQL Database à l’aide d’une identité managée, vous devez créer un utilisateur autonome SQL mappé à l’identité managée. Pour plus d’informations sur la création d’un utilisateur à partir d’un fournisseur externe, consultez [Créer des utilisateurs à relation contenant-contenu mappés à des identités Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Après avoir créé un utilisateur autonome SQL, vous devez lui accorder des autorisations à l’aide de la [commande T-SQL GRANT](/sql/t-sql/statements/grant-object-permissions-transact-sql).

## <a name="intermediate-component-output"></a>Sortie du composant intermédiaire

Lorsque vous utilisez la sortie de composant intermédiaire du concepteur Azure Machine Learning, vous pouvez spécifier l’emplacement de sortie de n’importe quel composant dans le concepteur. Vous pouvez ainsi stocker les jeux de données intermédiaires dans un emplacement distinct à des fins de sécurité, de journalisation ou d’audit. Pour spécifier la sortie, procédez comme suit :

1. Sélectionnez le composant dont vous souhaitez spécifier la sortie.
1. Dans le volet Paramètres du composant qui s’affiche à droite, sélectionnez **Paramètres de sortie**.
1. Spécifiez le magasin de données que vous souhaitez utiliser pour chaque sortie de composant.

Assurez-vous que vous avez accès aux comptes de stockage intermédiaires de votre réseau virtuel. Dans le cas contraire, le pipeline échoue.

[Activez l’authentification via une identité managée](#enable-managed-identity) pour les comptes de stockage intermédiaires afin de visualiser les données de sortie.
## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Accéder au studio à partir d’une ressource au sein d’un réseau virtuel

Si vous accédez à Studio à partir d’une ressource au sein d’un réseau virtuel (par exemple une instance de calcul ou une machine virtuelle), vous devez autoriser le trafic sortant du réseau virtuel vers Studio. 

Par exemple, si vous utilisez des groupes de sécurité réseau pour limiter le trafic sortant, ajoutez une règle à une destination d’__étiquette de service__ __AzureFrontDoor.Frontend__.

## <a name="firewall-settings"></a>Paramètres du pare-feu

Certains services de stockage, comme le compte de stockage Azure, ont des paramètres de pare-feu qui s’appliquent au point de terminaison public de cette instance de service spécifique. Généralement, ce paramètre vous permet d’autoriser ou d’interdire l’accès à partir d’adresses IP spécifiques à partir de l’Internet public. __Cela n’est pas pris en charge__ lors de l’utilisation d’Azure Machine Learning studio. C’est pris en charge lors de l’utilisation du kit de développement logiciel Azure Machine Learning ou de l’interface de ligne de commande.

> [!TIP]
> Azure Machine Learning studio est pris en charge lors de l’utilisation du service de Pare-feu Azure. Pour plus d’informations, consultez [Utiliser votre espace de travail derrière un pare-feu](how-to-access-azureml-behind-firewall.md).
## <a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :

* [Présentation du réseau virtuel](how-to-network-security-overview.md)
* [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
* [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
* [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
* [Utiliser le DNS personnalisé](how-to-custom-dns.md)
* [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)
