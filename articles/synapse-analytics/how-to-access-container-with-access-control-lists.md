---
title: Parcourir un dossier ADLS Gen2 avec des listes de contrôle d’accès dans Azure Synapse Analytics
description: Découvrez comment parcourir un dossier ADLS Gen2 avec des listes de contrôle d’accès dans Azure Synapse Analytics.
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 10/28/2021
ms.openlocfilehash: 2d16c9eae1e48c1471eb7c250bd9cf9e71d16d79
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894938"
---
# <a name="browse-adls-gen2-folders-preview-in-an-azure-synapse-analytics-workspace"></a>Parcourir des dossiers ADLS Gen2 (préversion) dans un espace de travail Azure Synapse Analytics
Vous pouvez désormais parcourir un conteneur ou un dossier Azure Data Lake Storage Gen2 (ADLS Gen2) dans votre espace de travail Azure Synapse Analytics en vous connectant au conteneur ou à un dossier spécifique dans le hub Données. Si votre organisation n’accorde pas aux utilisateurs le rôle Contributeur aux données Blob du stockage sur le compte de stockage, mais permet de créer des listes de contrôle d’accès de type POSIX sur le conteneur ou des dossiers spécifiques, vous pouvez suivre les étapes décrites dans cet article pour parcourir les fichiers et les dossiers dans ADLS Gen2.

>[!Note]
>Cette fonctionnalité en préversion permet aux utilisateurs de parcourir des dossiers ADLS Gen2 pour lesquels ils ont des listes de contrôle d’accès, mais ne prend pas en charge le chargement, le téléchargement, la création, la modification, la suppression ou le changement de nom des actions sur des fichiers ou dossiers. Les utilisateurs titulaires du rôle Contributeur aux données Blob du stockage peuvent effectuer toutes les actions sur les dossiers ADLS Gen2.


## <a name="prerequisites"></a>Prérequis
Avant la connexion d’un conteneur ou d’un dossier dans Azure Synapse, les conditions préalables suivantes doivent être remplies :
* Le rôle Contributeur aux données Blob du stockage (RBAC Azure) Stockage ou des listes de contrôle d’accès (ACL) doivent être accordés à votre identité Azure AD.
* Un service lié au conteneur de ADLS Gen2 doit être créé dans l’espace de travail Synapse.


## <a name="connect-adls-gen2-folder-to-your-azure-synapse-analytics-workspace"></a>Connecter une dossier ADLS Gen2 à votre espace de travail Azure Synapse Analytics
1. Accédez à l’onglet **Lié** du hub Données
2. Cliquez avec le bouton droit sur **Azure Data Lake Storage Gen2**, puis sélectionnez **Se connecter à Stockage Azure (préversion)**
    * Spécifiez l’URL du conteneur ou du dossier ADLS Gen2 au format `https://storageaccountname.dfs.core.windows.net/containername/foldername/`
    * Fournissez un nom unique pour la connexion
    * Indiquez le nom du locataire contenant le compte ADLS Gen2. Si ce champ est vide, le client associé à l’espace de travail Synapse sera utilisé.
    ![Connecter à un dossier de stockage avec des listes de contrôle d’accès](./media/connect-to-azure-storage-with-access-control-lists/connect-to-azure-storage-with-acls.png)
3. Cliquez sur **Connexion**. Le conteneur ou le dossier connecté s’affiche sous **Conteneurs attachés**.


## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur les listes de contrôle d’accès dans Azure Data Lake Storage Gen2.
- [Listes de contrôle d'accès dans Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md)
- [Utiliser le portail Azure pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-acl-azure-portal.md)
