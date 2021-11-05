---
title: Utilisation des informations d’identification
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment utiliser les informations d’identification Azure pour Azure Data Factory.
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: synapse
ms.openlocfilehash: a588bf8a02c6683143cff8096ee6e03c6b120234
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097988"
---
# <a name="credentials-in-azure-data-factory-and-azure-synapse"></a>Informations d’identification dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="prerequisites"></a>Prérequis

Les utilisateurs doivent disposer du rôle Azure RBAC (Opérateur d’identité managée) ou d’un rôle personnalisé avec l’action RBAC **Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action** pour configurer une identité managée affectée par l’utilisateur sous forme d’informations d’identification. Un rôle RBAC supplémentaire est nécessaire pour créer et utiliser les informations d’identification dans Synapse. [Plus d’informations](../synapse-analytics/security/synapse-workspace-synapse-rbac-roles.md)

## <a name="using-credentials"></a>Utilisation des informations d’identification

Nous présentons ici les informations d’identification, qui peuvent contenir des identités managées affectées par l’utilisateur, des principaux de service ainsi que la liste des identités managées affectées par le système que vous pouvez utiliser dans les services liés qui prennent en charge l’authentification Azure Active Directory (AAD). Cela vous aidera à rassembler et à gérer toutes vos informations d’identification AAD.  

Effectuez les étapes générales ci-dessous si vous souhaitez utiliser une **identité managée affectée par l'utilisateur** dans les services liés pour les besoins d’authentification. 

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

1. Si vous ne disposez pas d’une identité managée affectée par l’utilisateur dans Azure, créez-en d’abord une via la page [Identités managées](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ManagedIdentity%2FuserAssignedIdentities) du portail Azure.

1. Associez l’identité managée affectée par l’utilisateur à l’instance de Data Factory à l’aide du portail Azure, du kit SDK, de PowerShell ou de l’API REST. La capture d’écran ci-dessous utilise le portail Azure (panneau Data Factory) pour associer l’identité managée affectée par l’utilisateur.

   :::image type="content" source="media/credentials/uami-azure-portal.png" alt-text="Capture d’écran illustrant l’utilisation du portail Azure pour associer une identité managée affectée par l’utilisateur.":::

1. Créez de manière interactive des **informations d’identification** dans l’interface utilisateur de Data Factory. Vous pouvez sélectionner l’identité managée affectée par l’utilisateur que vous avez associée à la fabrique de données à l’étape 1. 

   :::image type="content" source="media/credentials/create-new-credential.png" alt-text="Capture d’écran montrant la création des informations d’identification.":::

   :::image type="content" source="media/credentials/user-assigned-credential-configuration.png" alt-text="Capture d’écran montrant la Configuration des informations d’identification.":::

1. Créez un service lié, puis sélectionnez **Identité managée affectée par l’utilisateur** sous Authentification

   :::image type="content" source="media/credentials/create-new-linked-service.png" alt-text="Capture d’écran illustrant le nouveau service lié avec l’authentification d’identité managée affectée à l’utilisateur.":::

   :::image type="content" source="media/credentials/linked-service-credential-configuration.png" alt-text="Capture d’écran montrant la configuration du nouveau service lié. Le choix de la méthode d’authentification, Identité managée affectée par l’utilisateur, et des informations d’identification utilisées est mis en évidence.":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

1. Si vous ne disposez pas d’une identité managée affectée par l’utilisateur dans Azure, créez-en d’abord une via la page [Identités managées](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ManagedIdentity%2FuserAssignedIdentities) du portail Azure.

1. Associez l’identité managée affectée par l’utilisateur à l’espace de travail à l’aide du portail Azure, du kit SDK, de PowerShell ou de l’API REST. La capture d’écran ci-dessous utilise le portail Azure (panneau Identité) pour associer l’identité managée affectée par l’utilisateur.

   :::image type="content" source="media/credentials/synapse-uami-azure-portal.png" alt-text="Capture d’écran illustrant l’utilisation du portail Azure pour associer une identité managée affectée par l’utilisateur.":::

1. Créez de manière interactive des **Informations d’identification** dans Synapse Studio. Vous pouvez sélectionner l’identité managée affectée par l’utilisateur, que vous avez associée à l’espace de travail à l’étape 1.

   :::image type="content" source="media/credentials/synapse-create-new-credential.png" alt-text="Capture d’écran montrant la création des informations d’identification.":::

   :::image type="content" source="media/credentials/user-assigned-credential-configuration.png" alt-text="Capture d’écran montrant la Configuration des informations d’identification.":::

1. Créez un service lié, puis sélectionnez **Identité managée affectée par l’utilisateur** sous Authentification

   :::image type="content" source="media/credentials/synapse-create-new-linked-service.png" alt-text="Capture d’écran illustrant le nouveau service lié avec l’authentification d’identité managée affectée à l’utilisateur.":::

   :::image type="content" source="media/credentials/linked-service-credential-configuration.png" alt-text="Capture d’écran montrant la configuration du nouveau service lié. Le choix de la méthode d’authentification, Identité managée affectée par l’utilisateur, et des informations d’identification utilisées est mis en évidence.":::

---

> [!NOTE] 
> Vous pouvez utiliser au choix le SDK, PowerShell ou les API REST pour effectuer les actions ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

- [Identité gérée](data-factory-service-identity.md)

Consultez les rubriques suivantes, qui expliquent quand et comment utiliser l’identité managée :

- [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md)
- [Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l’aide d’Azure Data Factory](connector-azure-data-lake-store.md)

Pour plus d’informations sur les identités managées des ressources Azure, sur lesquelles l’identité managée de fabrique de données est basée, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).
