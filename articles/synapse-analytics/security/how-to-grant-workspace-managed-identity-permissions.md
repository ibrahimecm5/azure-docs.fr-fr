---
title: Accorder des autorisations à l’identité managée dans l’espace de travail Synapse
description: Cet article explique comment octroyer des autorisations pour l'identité managée dans l’espace de travail Azure Synapse.
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.custom: subject-rbac-steps
ms.openlocfilehash: 009f611e1c80f4c4e0e7bafaa80036d809b3393d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853070"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>Octroyer des autorisations à une identité managée de l’espace de travail

Cet article vous explique comment octroyer des autorisations pour l’identité managée dans l’espace de travail Azure Synapse. Les autorisations permettent, à leur tour, d’accéder aux pools SQL dédiés de l’espace de travail et au compte de stockage ADLS Gen2 via le portail Azure.

>[!NOTE]
>Cette identité managée de l’espace de travail sera appelée « identité managée » dans le reste de ce document.

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Octroyer les autorisations de l'identité managée au compte de stockage ADLS Gen2

Un compte de stockage ADLS Gen2 est nécessaire pour créer un espace de travail Azure Synapse. Pour correctement lancer les pools Spark dans l’espace de travail Azure Synapse, l’identité managée Azure Synapse doit disposer du rôle *Contributeur aux données Blob du stockage* sur ce compte de stockage. L’orchestration de pipeline dans Azure Synapse tire également parti de ce rôle.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Octroyer des autorisations pour l'identité managée lors de la création de l'espace de travail

Azure Synapse tente d'octroyer le rôle Contributeur aux données Blob du stockage à l’identité managée une fois l'espace de travail Azure Synapse créé à l’aide du portail Azure. Vous fournissez les détails du compte de stockage ADLS Gen2 dans l’onglet **Informations de base**.

![Onglet Informations de base du flux de création de l'espace de travail](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Sélectionnez le compte de stockage ADLS Gen2 et le système de fichiers dans **Nom du compte** et **Nom du système de fichiers**.

![Détails d'un compte de stockage ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Si le créateur de l’espace de travail est également **propriétaire** du compte de stockage ADLS Gen2, Azure Synapse affecte le rôle *Contributeur aux données Blob du stockage* à l’identité managée. Le message suivant s’affiche sous les détails du compte de stockage que vous avez entrés.

![Affectation du rôle Contributeur aux données Blob du stockage réussie](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Si le créateur de l’espace de travail n’est pas propriétaire du compte de stockage ADLS Gen2, Azure Synapse n’affecte pas le rôle *Contributeur aux données Blob du stockage* à l’identité managée. Le message qui s’affiche sous les détails du compte de stockage informe le créateur de l’espace de travail qu’il ne dispose pas d'autorisations suffisantes pour octroyer le rôle *Contributeur aux données Blob du stockage* à l’identité managée.

![Affectation du rôle Contributeur aux données Blob du stockage non réussie](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Comme l’indique le message, vous ne pouvez pas créer de pools Spark tant que le rôle *Contributeur aux données Blob du stockage* n'est pas affecté à l’identité managée.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Octroyer des autorisations pour l'identité managée après création de l'espace de travail

Lors de la création de l’espace de travail, si vous n’affectez pas le rôle *Contributeur aux données Blob du stockage* à l’identité managée, le **propriétaire** du compte de stockage ADLS Gen2 s'en charge manuellement. Les étapes suivantes vous aideront à procéder à cette affectation manuellement.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Étape 1 : Accéder au compte de stockage ADLS Gen2 dans le Portail Azure

Dans le Portail Microsoft Azure, ouvrez le compte de stockage ADLS Gen2 et sélectionnez **Vue d’ensemble** dans le volet de navigation gauche. Vous devez affecter le rôle *Contributeur aux données Blob du stockage* au niveau du conteneur ou du système de fichiers. Sélectionnez **Conteneurs**.  
![Vue d'ensemble du compte de stockage ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Étape 2 : Sélection du conteneur

L’identité managée doit pouvoir accéder aux données du conteneur (système de fichiers) fourni lors de la création de l’espace de travail. Ce conteneur ou système de fichiers est disponible dans le portail Azure. Ouvrez l'espace de travail Azure Synapse dans le portail Azure et sélectionnez l'onglet **Vue d’ensemble** dans le volet de navigation gauche.
![Conteneur de comptes de stockage ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Sélectionnez le même conteneur ou système de fichiers pour octroyer le rôle *Contributeur aux données Blob du stockage* à l'identité managée.
![Capture d’écran montrant le conteneur ou système de fichiers que vous devez sélectionner.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-open-access-control-and-add-role-assignment"></a>Étape 3 : Ouvrir le contrôle d’accès et ajouter une attribution de rôle

1. Sélectionnez **Contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | Contributeur du Stockage Blob |
    | Attribuer l’accès à | MANAGEDIDENTITY |
    | Membres | Nom de l’identité managée  |

    > [!NOTE]
    > Le nom de l’identité managée correspond également au nom de l’espace de travail.

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

1. Sélectionnez **Enregistrer** pour ajouter l’attribution de rôle.

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Étape 9 : Vérifiez que le rôle Contributeur aux données Blob du stockage est attribué à l’identité managée.

Sélectionnez **Contrôle d’accès (IAM)** , puis **Attributions de rôle**.

![Vérifier l'attribution de rôle](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Votre identité managée doit être répertoriée sous la section **Contributeur aux données Blob du stockage** et le rôle *Contributeur aux données Blob du stockage* doit lui avoir été attribué. 
![Sélection du conteneur de comptes de stockage ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l'[identité managée de l'espace de travail](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics)
