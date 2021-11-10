---
title: Sécuriser le serveur principal de Gestion des API à l’aide d’une authentification par certificat client
titleSuffix: Azure API Management
description: Découvrez comment gérer les certificats clients et sécuriser les services principaux à l’aide de l’authentification par certificat client dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 00d00c8dc5419b524eb71172adfb5271905d668f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057781"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Sécuriser les services principaux à l’aide d’une authentification par certificat client dans Gestion des API Azure

Gestion des API permet de sécuriser l’accès au service principal d’une API en utilisant des certificats clients. Ce guide montre comment gérer des certificats dans une instance de service Gestion des API Azure à l’aide du portail Azure. Il explique également comment configurer une API pour utiliser un certificat pour accéder à un service principal.

Vous pouvez également gérer des certificats Gestion des API à l’aide de l’[API REST Gestion des API](/rest/api/apimanagement/2021-01-01-preview/certificate).

## <a name="certificate-options"></a>Options de certificat

Gestion des API offre deux options pour gérer les certificats utilisés pour sécuriser l’accès aux services principaux :

* Référencer un certificat géré dans [Azure Key Vault](../key-vault/general/overview.md) 
* Ajouter un fichier de certificat directement dans Gestion des API

L’utilisation de certificats de coffre de clés est recommandée, car elle permet d’améliorer la sécurité de Gestion des API :

* Les certificats stockés dans des coffres de clés peuvent être réutilisés entre les services.
* Des [stratégies d’accès](../key-vault/general/security-features.md#privileged-access) granulaires peuvent être appliquées à des certificats stockés dans des coffres de clés.
* Les certificats mis à jour dans le coffre de clés sont automatiquement permutés dans Gestion des API. Après la mise à jour dans le coffre de clés, un certificat dans Gestion des API est mis à jour dans un délai de quatre heures. Vous pouvez également actualiser manuellement le certificat à l’aide du portail Azure ou par le biais de l’API REST de gestion.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Créer une instance de service Gestion des API][Create an API Management service instance].
* Votre service principal doit être configuré pour l’authentification par certificat client. Pour configurer l’authentification par certificat dans Azure App Service, consultez [cet article][to configure certificate authentication in Azure WebSites refer to this article]. 
* Vous devez avoir accès au certificat et au mot de passe pour la gestion dans un coffre de clés Azure ou les charger sur le service Gestion des API. Le certificat doit être au format **PFX**. Les certificats auto-signés sont autorisés.

### <a name="prerequisites-for-key-vault-integration"></a>Conditions préalables à l’intégration d’un coffre de clés

1. Pour connaître la procédure de création d’un coffre de clés, consultez [Démarrage rapide : Créer un coffre de clés avec le portail Azure](../key-vault/general/quick-create-portal.md).
1. Activer une[ identité managée](api-management-howto-use-managed-service-identity.md) attribuée par le système ou par l’utilisateur dans l’instance Gestion des API.
1. Attribuez une [stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md) à l’identité managée avec les autorisations pour récupérer et répertorier les certificats du coffre. Pour ajouter la stratégie :
    1. Dans le portail, accédez à votre coffre de clés.
    1. Sélectionnez **Paramètres > Stratégies d’accès > + Ajouter une stratégie d’accès**.
    1. Sélectionnez **Autorisations du certificat**, puis **Get** (Obtenir) et **List** (Lister).
    1. Dans **Sélectionner le principal**, sélectionnez le nom de ressource de votre identité managée. Si vous utilisez une identité attribuée par le système, le principal est le nom de votre instance Gestion des API.
1. Créez ou importez un certificat dans le coffre. Consultez [Démarrage rapide : Définir et récupérer un certificat dans Azure Key Vault à l’aide du portail Azure](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Ajouter un certificat de coffre de clés

Voir [Conditions préalables à l’intégration d’un coffre de clés](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Lorsque vous utilisez un certificat de coffre de clés dans Gestion des API, veillez à ne pas supprimer le certificat, le coffre de clés ou l’identité managée utilisée pour accéder au coffre de clés.

Pour ajouter un certificat de coffre de clés à Gestion des API :

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance APIM.
1. Sous **Sécurité**, sélectionnez **Certificats**.
1. Sélectionnez **Certificats** >  **+ Ajouter**.
1. Dans **ID**, entrez le nom de votre choix.
1. Dans **Certificat**, sélectionnez **Coffre de clés**.
1. Entrez l’identificateur d’un certificat de coffre de clés ou choisissez **Sélectionner** pour choisir un certificat dans un coffre de clés.
    > [!IMPORTANT]
    > Si vous entrez vous-même l’identificateur d’un certificat de coffre de clés, assurez-vous qu’il ne contient pas d’informations de version. Sinon, le certificat n’est pas automatiquement permuté dans Gestion des API après une mise à jour dans le coffre de clés.
1. Dans **Identité du client**, sélectionnez une entité managée affectée par le système ou une entité managée existante affectée par l’utilisateur. Découvrez comment [ajouter ou modifier des identités managées dans votre service Gestion des API](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > L’identité a besoin d’autorisations pour obtenir et lister les certificats du coffre de clés. Si vous n’avez pas encore configuré l’accès au coffre de clés, Gestion des API vous invite à configurer automatiquement l’identité avec les autorisations nécessaires.
1. Sélectionnez **Ajouter**.



    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Ajouter un certificat de coffre de clés":::
    
1. Sélectionnez **Enregistrer**.

## <a name="upload-a-certificate"></a>Téléchargement d'un certificat

Pour charger un certificat client dans Gestion des API : 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance APIM.
1. Sous **Sécurité**, sélectionnez **Certificats**.
1. Sélectionnez **Certificats** >  **+ Ajouter**.
1. Dans **ID**, entrez le nom de votre choix.
1. Dans **Certificat**, sélectionnez **Personnalisé**.
1. Accédez au fichier .pfx du certificat, sélectionnez-le et entrez son mot de passe.
1. Sélectionnez **Ajouter**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Charger le certificat client":::


1. Sélectionnez **Enregistrer**.

Une fois le certificat chargé, il s’affiche dans la fenêtre **Certificats**. Si vous avez beaucoup de certificats, notez l’empreinte du certificat souhaité pour configurer une API afin qu’elle utilise un certificat client pour l’[authentification de la passerelle](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Pour désactiver la validation des chaînes de certificat lorsque vous utilisez, par exemple, un certificat auto-signé, suivez les étapes décrites dans la section [Certificats auto-signés](#self-signed-certificates) de cet article.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Configurer une API afin d’utiliser un certificat client pour l’authentification de passerelle

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance APIM.
1. Sous **API**, sélectionnez **API**.
1. Sélectionnez une API dans la liste. 
1. Sous l’onglet **Conception**, sélectionnez l’icône de l’éditeur dans la section **Principal**.
1. Dans **Informations d’identification de la passerelle**, sélectionnez **Certificat client** et sélectionnez votre certificat dans la liste déroulante.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Utiliser un certificat client pour l’authentification de passerelle":::

> [!CAUTION]
> Cette modification s’applique immédiatement, et les appels d’opérations de cette API utiliseront désormais ce certificat pour s’authentifier sur le serveur principal.

> [!TIP]
> Lorsqu’un certificat est spécifié pour l’authentification de passerelle pour le service principal d’une API, il devient partie intégrante de la stratégie de cette API et peut être affiché dans l’éditeur de stratégies.

## <a name="self-signed-certificates"></a>Certificats auto-signés

Si vous utilisez des certificats auto-signés, vous devrez désactiver la validation des chaînes de certificat afin que le service Gestion des API puisse communiquer avec le système principal. Dans le cas contraire, un code d’erreur 500 est généré. Pour configurer ce paramètre, vous pouvez utiliser les cmdlets PowerShell [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (pour un nouveau serveur principal) ou [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (pour un serveur principal existant) et configurez le paramètre `-SkipCertificateChainValidation` sur `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Supprimer un certificat client

Pour supprimer un certificat, sélectionnez-le, puis sélectionnez **Supprimer** dans le menu contextuel ( **…** ).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Supprimer un certificat":::

> [!IMPORTANT]
> Si le certificat est référencé par des stratégies, un écran d’avertissement s’affiche. Pour supprimer le certificat, vous devez d’abord le supprimer de toutes les stratégies configurées pour l’utiliser.

## <a name="next-steps"></a>Étapes suivantes

* [Comment sécuriser les API à l'aide d'une authentification par certificat client dans la Gestion des API](api-management-howto-mutual-certificates-for-clients.md)
* En savoir plus sur les [stratégies dans Gestion des API](api-management-howto-policies.md)


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md
