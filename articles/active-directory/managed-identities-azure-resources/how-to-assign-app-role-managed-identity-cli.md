---
title: Affecter une identité managée à un rôle d’application à l’aide d’Azure CLI – Azure AD
description: Instructions détaillées pour affecter à une identité managée l’accès au rôle d’une autre application, à l’aide d’Azure CLI.
services: active-directory
documentationcenter: ''
author: christoc
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2021
ms.author: christoc
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 846085c773c1923f581968c06c02c5c4f8372e65
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180209"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-azure-cli"></a>Affecter à une identité managée l’accès à un rôle d’application à l’aide d’Azure CLI

Les identités managées pour les ressources Azure fournissent aux services Azure une identité dans Azure Active Directory. Elles fonctionnent sans avoir besoin d’informations d’identification dans votre code. Les services Azure utilisent cette identité pour s’authentifier auprès des services prenant en charge l’authentification Azure AD. Les rôles d’application fournissent une forme de contrôle d’accès en fonction du rôle et permettent à un service d’implémenter des règles d’autorisation.

Dans cet article, vous allez apprendre à affecter une identité managée à un rôle d’application exposé par une autre application à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#managed-identity-types)** .

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Affecter à une identité managée l’accès au rôle d’application d’une autre application

1. Activez l’identité managée sur une ressource Azure, telle qu’une [machine virtuelle Azure](qs-configure-cli-windows-vm.md).

1. Recherchez l’ID d’objet du principal de service de l’identité managée.

   **Pour une identité managée affectée par le système**, vous pouvez rechercher l’ID d’objet dans le portail Azure, dans la page **Identité** de la ressource. Vous pouvez également utiliser le script suivant pour rechercher l’ID d’objet. Vous aurez besoin de l’ID de ressource de la ressource que vous avez créée à l’étape 1, qui est disponible dans le portail Azure, dans la page **Propriétés** de la ressource.

    ```azurecli
    resourceIdWithManagedIdentity="/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}"
    
    oidForMI=$(az resource show --ids $resourceIdWithManagedIdentity --query "identity.principalId" -o tsv | tr -d '[:space:]')
    echo "object id for managed identity is: $oidForMI"
    ```

    **Pour une identité managée affectée par l’utilisateur**, vous pouvez rechercher l’ID d’objet de l’identité managée dans le portail Azure, dans la page **Vue d’ensemble** de la ressource. Vous pouvez également utiliser le script suivant pour rechercher l’ID d’objet. Vous aurez besoin de l’ID de ressource de l’identité managée affectée par l’utilisateur.

    ```azurecli
    userManagedIdentityResourceId="/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}"
    
    oidForMI=$(az resource show --id $userManagedIdentityResourceId --query "properties.principalId" -o tsv | tr -d '[:space:]')
    echo "object id for managed identity is: $oidForMI"
    ```

1. Créez une nouvelle inscription d’application pour représenter le service auquel votre identité managée enverra une demande. Si l’API ou le service qui expose l’octroi de rôle d’application à l’identité managée a déjà un principal de service dans votre locataire Azure AD, ignorez cette étape.

1. Recherchez l’ID d’objet du principal de service de l’application de service. Vous le trouverez en utilisant le portail Azure. Accédez à Azure Active Directory et ouvrez la page **Applications d’entreprise**, puis recherchez l’application et recherchez l’**ID d’objet**. Vous pouvez également rechercher l’ID d’objet du principal de service par son nom d’affichage à l’aide du script suivant :

    ```azurecli
    appName="{name for your application}"
    serverSPOID=$(az ad sp list --filter "displayName eq 'My App'" --query '[0].objectId' -o tsv | tr -d '[:space:]')
    echo "object id for server service principal is: $serverSPOID"
    ```

    > [!NOTE]
    > Les noms d’affichage des applications ne sont pas uniques et vous devez vérifier que vous obtenez le principal de service de l’application appropriée.

    Ou vous pouvez rechercher l’ID d’objet par l’ID d’application unique pour votre inscription d’application :

    ```azurecli
    appID="{application id for your application}"
    serverSPOID=$(az ad sp list --filter "appId eq '$appID'" --query '[0].objectId' -o tsv | tr -d '[:space:]')
    echo "object id for server service principal is: $serverSPOID"
    ```

1. Ajoutez un [rôle d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) à l’application que vous avez créée à l’étape 3. Vous pouvez créer ce rôle à l’aide du portail Azure ou de Microsoft Graph. Par exemple, vous pouvez ajouter un rôle d’application comme suit :

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Affectez le rôle d’application à l’identité managée. Vous aurez besoin des informations suivantes pour affecter le rôle d’application :
    * `managedIdentityObjectId` : ID d’objet du principal de service de l’identité managée, que vous avez trouvé à l’étape 2.
    * `serverServicePrincipalObjectId` : ID d’objet du principal de service de l’application serveur, que vous avez trouvé à l’étape 4.
    * `appRoleId` : ID du rôle d’application exposé par l’application serveur, que vous avez généré à l’étape 5 – dans cet exemple, l’ID du rôle d’application est `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6`.
   
   Exécutez le script suivant pour ajouter l’attribution de rôle.  Notez que cette fonctionnalité n’est pas directement exposée sur l’Azure CLI, et qu’une commande REST est utilisée ici à la place :

    ```azurecli
    roleguid="0566419e-bb95-4d9d-a4f8-ed9a0f147fa6"
    az rest -m POST -u https://graph.microsoft.com/beta/servicePrincipals/$oidForMI/appRoleAssignments -b "{\"principalId\": \"$oidForMI\", \"resourceId\": \"$serverSPOID\",\"appRoleId\": \"$roleguid\"}"
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’identité managée pour les ressources Azure](overview.md)
- Pour activer l’identité managée sur une machine virtuelle Azure, consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure à l’aide de PowerShell](qs-configure-cli-windows-vm.md).
