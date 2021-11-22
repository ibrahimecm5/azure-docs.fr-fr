---
title: Accès aux données réseau dans ML studio
titleSuffix: Azure Machine Learning
description: Découvrez comment l’accès aux données fonctionne avec Azure Machine Learning studio lorsque votre espace de travail ou votre stockage se trouve dans un réseau virtuel.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/08/2021
ms.openlocfilehash: c93a788686cd4ae7c0f20535b2ca0b4746fbdff2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350427"
---
# <a name="network-data-access-with-azure-machine-learning-studio"></a>Accès aux données réseau avec Azure Machine Learning studio

L'accès aux données est complexe et il est important de comprendre qu'il comporte de nombreux éléments. Par exemple, l'accès aux données à partir d'Azure Machine Learning studio est différent de l'utilisation du kit de développement logiciel (SDK). Lorsque vous utilisez le SDK dans votre environnement de développement local, vous accédez directement aux données disponibles dans le cloud. Lorsque vous utilisez ML studio, vous ne disposez pas toujours d'un accès direct au magasin de données depuis votre client. ML studio s’appuie sur l’espace de travail pour accéder aux données en votre nom.

> [!IMPORTANT]
> Les informations contenues dans cet article s’adressent aux administrateurs Azure qui créent l’infrastructure requise pour une solution Azure Machine Learning.

> [!TIP]
> ML studio prend uniquement en charge la lecture des données des types de magasins de données suivants dans un réseau virtuel :
>
> * Compte Stockage Azure (blob et fichier)
> * Azure Data Lake Storage Gen1
> * Azure Data Lake Storage Gen2
> * Azure SQL Database

## <a name="data-access"></a>Accès aux données

En général, l’accès aux données à partir de ML studio implique les vérifications suivantes :

1. Qui y accède ?
    - Différents types d’authentification sont disponibles en fonction du type de stockage. Par exemple : clé de compte, jeton, principal de service, identité managée et identité d'utilisateur.
    - Si l'authentification se fait à l'aide d'une identité d'utilisateur, il est important de savoir *quel* utilisateur essaie d'accéder au stockage.
2. Disposent-ils des autorisations nécessaires ?
    - Les informations d’identification sont-elles correctes ? Si oui, le principal de service, l’identité managée, etc., disposent-ils des autorisations nécessaires sur le stockage ? Les autorisations sont accordées à l’aide du contrôle d’accès en fonction du rôle d’Azure (Azure RBAC).
    - Le [Lecteur](/azure/role-based-access-control/built-in-roles#reader) du compte de stockage lit les métadonnées du stockage.
    - Le [Lecteur des données blob du stockage](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) lit les données dans un conteneur d’objets blob.
    - Le [Contributeur](/azure/role-based-access-control/built-in-roles#contributor) autorise l’accès en écriture à un compte de stockage.
    - D'autres rôles peuvent être nécessaires en fonction du type de stockage.
3. D'où vient l'accès ?
    - Utilisateur : l’adresse IP du client se trouve-t-elle dans la plage de réseau virtuel/sous-réseau ?
    - Espace de travail : l’espace de travail est-il public ou possède-t-il un point de terminaison privé situé dans un réseau virtuel/sous-réseau ?
    - Stockage : le stockage permet-il un accès public, ou limite-t-il l'accès via un point de terminaison de service ou un point de terminaison privé ?
4. Quelle est l'opération en cours ?
    - Les opérations de création, lecture, mise à jour et suppression (CRUD) sur un magasin de données/jeu de données sont gérées par Azure Machine Learning.
    - Les appels d’accès aux données (tels que l’aperçu ou le schéma) accèdent au stockage sous-jacent et requièrent des autorisations supplémentaires.
5. Où cette opération est-elle exécutée : ressources de calcul de votre abonnement Azure ou ressources hébergées dans un abonnement Microsoft ?
    - Tous les appels aux services de jeu de données et de magasin de données (à l'exception de l'option « Générer un profil ») utilisent des ressources hébergées dans un __abonnement Microsoft__ pour exécuter les opérations.
    - Les tâches, y compris l’option « Générer un profil » pour les jeux de données, s’exécutent sur une ressource de calcul de __votre abonnement__ et accèdent aux données à partir de là. C'est donc l'identité de l'ordinateur qui a besoin d'une autorisation pour le stockage plutôt que l'identité de l'utilisateur qui soumet la tâche.

Le schéma suivant illustre le déroulement général d'un appel d'accès aux données. Dans cet exemple, un utilisateur tente de passer un appel d’accès aux données par le biais d’un espace de travail Machine Learning, sans utiliser de ressource de calcul.

:::image type="content" source="./media/concept-network-data-access/data-access-flow.svg" alt-text="Schéma du déroulement logique lors de l’accès aux données":::

## <a name="azure-storage-account"></a>Compte Stockage Azure

Lorsque vous utilisez un compte Stockage Azure à partir d'Azure Machine Learning studio, vous devez ajouter l'identité managée de l'espace de travail aux rôles Azure RBAC suivants pour le compte de stockage :

* [Lecteur de données blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)
* Si le compte de stockage utilise un point de terminaison privé pour se connecter au réseau virtuel, vous devez accorder à l’identité managée le rôle de [Lecteur](../role-based-access-control/built-in-roles.md#reader) pour le point de terminaison privé du compte de stockage.

Pour plus d’informations, consultez [Utiliser Azure Machine Learning studio dans un Réseau virtuel Azure](how-to-enable-studio-virtual-network.md).

Consultez les sections suivantes pour plus d'informations sur les limitations liées à l'utilisation d'un compte Stockage Azure avec votre espace de travail dans un réseau virtuel.
### <a name="using-an-existing-storage-account"></a>Utiliser un compte de stockage existant

Si vous utilisez un compte de stockage existant comme __stockage par défaut__ lors de la création d’un espace de travail, le dossier `azureml-filestore` du magasin de fichiers n’est pas automatiquement créé. Ce dossier est nécessaire pour soumettre des expériences [AutoML](concept-automated-ml.md).

Pour éviter ce problème, vous pouvez autoriser Azure Machine Learning à créer le stockage par défaut pour vous lors de la création de l’espace de travail, ou bien vous assurer que le compte de stockage existant ne se trouve __pas__ dans le réseau virtuel lors de la création de l’espace de travail. Pour plus d’informations sur la mise en réseau avec un compte Stockage Azure, consultez [Configurer des comptes Stockage Azure avec des réseaux virtuels](/azure/storage/common/storage-network-security).

### <a name="azure-storage-firewall"></a>Pare-feu de Stockage Azure

Lorsqu’un compte Stockage Azure se trouve derrière un réseau virtuel, le pare-feu du stockage peut normalement être utilisé pour permettre à votre client de se connecter directement sur Internet. Toutefois, lorsque vous utilisez ML studio, ce n'est pas votre client qui se connecte au compte de stockage, mais le service Azure Machine Learning qui lance la requête. L’adresse IP du service n’est pas documentée et change fréquemment. __L’activation du pare-feu du stockage ne permet pas au ML studio d'accéder au compte de stockage dans une configuration de réseau virtuel__.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Lorsque vous utilisez Azure Data Lake Storage Gen1 en tant que magasin de données, vous ne pouvez utiliser que des listes de contrôle d’accès de type POSIX. Vous pouvez accorder à l’identité managée de l’espace de travail l’accès aux ressources, comme pour tout autre principal de sécurité. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Lorsque vous utilisez Azure Data Lake Storage Gen2 en tant que magasin de données, vous pouvez utiliser des listes de contrôle d’accès (ACL) de type Azure RBAC et POSIX pour contrôler l’accès aux données au sein d’un réseau virtuel.

**Pour utiliser Azure RBAC**, suivez les étapes décrites dans la section [Magasin de données : compte Stockage Azure](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account) de l’article « Utiliser Azure Machine Learning studio dans un Réseau virtuel Azure ». Data Lake Storage Gen2 est basé sur le service Stockage Azure ; par conséquent, les mêmes étapes s'appliquent lorsque vous utilisez Azure RBAC.

**Pour utiliser des listes de contrôle d’accès**, vous pouvez accorder l’accès à l’identité managée de l’espace de travail comme pour tout autre principal de sécurité. Pour plus d’informations, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

## <a name="azure-sql-database"></a>Azure SQL Database

Pour accéder aux données stockées dans une base de données Azure SQL Database à l’aide d’une identité managée, vous devez créer un utilisateur autonome SQL mappé à l’identité managée. Pour plus d’informations sur la création d’un utilisateur à partir d’un fournisseur externe, consultez [Créer des utilisateurs à relation contenant-contenu mappés à des identités Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Après avoir créé un utilisateur autonome SQL, vous devez lui accorder des autorisations à l’aide de la [commande T-SQL GRANT](/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="deny-public-network-access"></a>Refuser l’accès au réseau public

Dans Azure SQL Database, __Refuser l’accès au réseau public__ vous permet de bloquer l’accès public à la base de données. Nous __ne prenons pas en charge__ l’accès à SQL Database si cette option est activée. Lorsqu'une instance de SQL Database est utilisée avec Azure Machine Learning studio, l'accès aux données se fait toujours par le biais du point de terminaison public de l'instance de SQL Database.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur l'activation de ML studio dans un réseau, consultez [Utiliser Azure Machine Learning studio dans un Réseau virtuel Azure](how-to-enable-studio-virtual-network.md).

