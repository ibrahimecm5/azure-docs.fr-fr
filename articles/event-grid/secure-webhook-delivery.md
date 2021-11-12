---
title: Livraison sécurisée de webhooks à l’aide d’Azure AD dans Azure Event Grid
description: Décrit comment livrer des événements aux points de terminaison HTTPS protégés par Azure Active Directory à l’aide d’Azure Event Grid
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 2e5c816d0902fe34ecdff9b967422814e345599a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511089"
---
# <a name="deliver-events-to-azure-active-directory-protected-endpoints"></a>Délivrer des événements sur des points de terminaison protégés par Azure Active Directory
Cet article explique comment utiliser Azure Active Directory (Azure AD) pour sécuriser la connexion entre votre **abonnement aux événements** et votre **point de terminaison webhook**. Pour obtenir une vue d’ensemble des applications et des principaux de service Azure AD, consultez [Présentation de la plateforme d’identités Microsoft (v2.0)](../active-directory/develop/v2-overview.md).

Cet article utilise le Portail Azure à des fins de démonstration, mais la fonctionnalité peut également être activée à l’aide de l’interface CLI, de PowerShell ou des Kits de développement logiciel (SDK).

> [!IMPORTANT]
> Une vérification d’accès supplémentaire a été introduite dans le cadre de la création ou de la mise à jour de l’abonnement aux événements le 30 mars 2021 afin de résoudre une faille de sécurité. Le principal de service du client abonné doit être soit propriétaire, soit avoir un rôle attribué sur le principal de service de l’application de destination. Reconfigurez votre application AAD en suivant les nouvelles instructions ci-dessous.

## <a name="deliver-events-to-a-webhook-in-the-same-azure-ad-tenant"></a>Remettre des événements à un webhook dans le même locataire Azure AD

![Livraison sécurisée de webhooks à l’aide d’Azure AD dans Azure Event Grid](./media/secure-webhook-delivery/single-tenant-diagram.png)

Sur la base du diagramme ci-dessus, procédez comme suit pour configurer le locataire.

### <a name="configure-the-event-subscription-by-using-azure-ad-user"></a>Configurer l’abonnement aux événements à l’aide d’un utilisateur Azure AD

1. Créez une application Azure AD pour le webhook configuré pour fonctionner avec l’annuaire Microsoft (monolocataire).

2. Ouvrez [Azure Shell](https://portal.azure.com/#cloudshell/) dans le locataire, puis sélectionnez l’environnement PowerShell.

3. Modifiez la valeur de **$webhookAadTenantId** pour vous connecter au locataire.

    - Variables:
        - **$webhookAadTenantId** : ID de locataire Azure

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

4. Ouvrez le [script suivant](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-user.md) et mettez à jour les valeurs de **$webhookAppObjectId** et **$eventSubscriptionWriterUserPrincipalName** avec vos identificateurs, puis poursuivez l’exécution du script.

    - Variables:
        - **$webhookAppObjectId** : ID d’application Azure AD créé pour le webhook
        - **$eventSubscriptionWriterUserPrincipalName** : nom d’utilisateur principal Azure de l’utilisateur qui va créer l’abonnement aux événements

    > [!NOTE]
    > Vous n’avez pas besoin de modifier la valeur de **$eventGridAppId**, pour ce script. Nous définissons **AzureEventGridSecureWebhookSubscriber** comme valeur de **$eventGridRoleName**. N’oubliez pas que, pour exécuter ce script, vous devez être membre du [rôle Administrateur d’application Azure AD](../active-directory/roles/permissions-reference.md#all-roles).

5. Dans le portail, lorsque vous créez un abonnement aux événements, procédez comme suit :

    1. Sélectionnez le type de point de terminaison **Webhook**.
    2. Spécifiez l’**URI** du point de terminaison.
    
        ![Sélectionner un webhook de type de point de terminaison](./media/secure-webhook-delivery/select-webhook.png)
    3. Sélectionnez l’onglet **Fonctionnalités supplémentaires** en haut de la page **Créer des abonnements aux événements**.
    4. Sous l’onglet **Fonctionnalités supplémentaires**, procédez comme suit :
        1. Sélectionnez **Utiliser l’authentification AAD**, puis configurez l’ID de locataire et l’ID d’application :
        2. Copiez l’ID de locataire Azure AD à partir de la sortie du script, puis entrez-le dans le champ **ID de locataire AAD**.
        3. Copiez l’ID d’application Azure AD à partir de la sortie du script, puis entrez-le dans le champ **ID d’application AAD**. Vous pouvez utiliser l’URI de l’ID d’application AAD au lieu d’utiliser l’ID de l’application. Pour plus d’informations sur l’URI de l’ID d’application, consultez [cet article](../app-service/configure-authentication-provider-aad.md).
    
            ![Action de webhook sécurisé](./media/secure-webhook-delivery/aad-configuration.png)

### <a name="configure-the-event-subscription-by-using-azure-ad-application"></a>Configurer l’abonnement aux événements à l’aide d’une application Azure AD

1. Créez une application Azure AD pour le rédacteur de l’abonnement Event Grid configuré pour fonctionner avec l’annuaire Microsoft (monolocataire).

2. Créez un secret pour l’application Azure AD créée précédemment, puis enregistrez la valeur (vous en aurez besoin plus tard).

3. Accédez au contrôle d’accès (IAM) dans la rubrique Event Grid et ajoutez l’attribution de rôle du rédacteur de l’abonnement Event Grid en tant que contributeur Event Grid. Cette étape nous permettra d’accéder à la ressource Event Grid lorsque nous nous connectons à Azure avec l’application Azure AD à l’aide d’Azure CLI.

4. Créez une application Azure AD pour le webhook configuré pour fonctionner avec l’annuaire Microsoft (monolocataire).

5. Ouvrez [Azure Shell](https://portal.azure.com/#cloudshell/) dans le locataire, puis sélectionnez l’environnement PowerShell.

6. Modifiez la valeur de **$webhookAadTenantId** pour vous connecter au locataire.

    - Variables:
        - **$webhookAadTenantId** : ID de locataire Azure

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. Ouvrez le [script suivant](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md) et mettez à jour les valeurs de **$webhookAppObjectId** et **$eventSubscriptionWriterAppId** avec vos identificateurs, puis poursuivez l’exécution du script.

    - Variables:
        - **$webhookAppObjectId** : ID d’application Azure AD créé pour le webhook
        - **$eventSubscriptionWriterAppId** : ID d’application Azure AD pour le rédacteur de l’abonnement Event Grid

    > [!NOTE]
    > Vous n’avez pas besoin de modifier la valeur de **```$eventGridAppId```** , pour ce script. Nous définissons **AzureEventGridSecureWebhookSubscriber** comme valeur de **```$eventGridRoleName```** . N’oubliez pas que, pour exécuter ce script, vous devez être membre du [rôle Administrateur d’application Azure AD](../active-directory/roles/permissions-reference.md#all-roles).

8. Connectez-vous en tant qu’application Azure AD de rédacteur de l’abonnement Event Grid en exécutant la commande.

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_ID] -p [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. Créez votre abonnement en exécutant la commande.

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > Dans ce scénario, nous utilisons une rubrique système Event Grid. Consultez [cette page](/cli/azure/eventgrid) si vous souhaitez créer un abonnement pour des rubriques personnalisées ou des domaines Event Grid à l’aide d’Azure CLI.

10. Si tout a été correctement configuré, vous pourrez créer l’abonnement webhook dans votre rubrique Event Grid.

    > [!NOTE]
    > À ce stade, Event Grid transmet maintenant le jeton du porteur Azure AD au client webhook dans chaque message. Vous devrez valider le jeton d’autorisation dans votre webhook.

## <a name="deliver-events-to-a-webhook-in-a-different-azure-ad-tenant"></a>Remettre des événements à un webhook dans un autre locataire Azure AD 

Pour sécuriser la connexion entre votre abonnement aux événements et votre point de terminaison webhook qui se trouvent dans des locataires Azure AD différents, vous devez utiliser une application Azure AD comme indiqué dans cette section. Actuellement, il n’est pas possible de sécuriser cette connexion à l’aide d’un utilisateur Azure AD dans le portail Azure. 

![Événements multi-locataire avec Azure AD et webhooks](./media/secure-webhook-delivery/multitenant-diagram.png)

Sur la base du diagramme ci-dessus, procédez comme suit pour configurer les locataires.

### <a name="tenant-a"></a>Locataire A

Procédez comme suit dans le **Locataire A** : 

1. Créez une application Azure AD pour le rédacteur de l’abonnement Event Grid configuré pour fonctionner avec n’importe quel annuaire Azure AD (monolocataire).

2. Créez un secret pour l’application Azure AD créée précédemment dans le **locataire A**, puis enregistrez la valeur (vous en aurez besoin plus tard).

3. Accédez à la page **Contrôle d’accès (IAM)** de la rubrique Event Grid. Ajoutez l’application Azure AD de l’enregistreur d’abonnement Event Grid au rôle de **Contributeur Event Grid**. Cette étape permet à l’application d’accéder à la ressource Event Grid lorsque vous vous connectez à Azure avec l’application Azure AD à l’aide d’Azure CLI.

### <a name="tenant-b"></a>Locataire B

Procédez comme suit dans le **Locataire B** :

1. Créez une application Azure AD pour le webhook configuré pour fonctionner avec l’annuaire Microsoft (monolocataire).
5. Ouvrez [Azure Shell](https://portal.azure.com/#cloudshell/), puis sélectionnez l’environnement PowerShell.
6. Modifiez la valeur **$webhookAadTenantId** pour vous connecter au **locataire B**.
    - Variables:
        - **$webhookAadTenantId** : ID de locataire Azure pour le **locataire B**

        ```Shell
        PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
        PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
        ```
7. Ouvrez le [script suivant](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md) et mettez à jour les valeurs de **$webhookAppObjectId** et **$eventSubscriptionWriterAppId** avec vos identificateurs, puis poursuivez l’exécution du script.

    - Variables:
        - **$webhookAppObjectId** : ID d’application Azure AD créé pour le webhook
        - **$eventSubscriptionWriterAppId** : ID d’application Azure AD pour le rédacteur de l’abonnement Event Grid

            > [!NOTE]
            > Vous n’avez pas besoin de modifier la valeur de **```$eventGridAppId```** , pour ce script. Nous définissons **AzureEventGridSecureWebhookSubscriber** comme valeur de **```$eventGridRoleName```** . N’oubliez pas que, pour exécuter ce script, vous devez être membre du [rôle Administrateur d’application Azure AD](../active-directory/roles/permissions-reference.md#all-roles).

### <a name="tenant-a"></a>Locataire A

Revenez dans le **Locataire A** et procédez comme suit : 

1. Ouvrez [Azure Shell](https://portal.azure.com/#cloudshell/), puis connectez-vous en tant qu’application Azure AD du rédacteur de l’abonnement Event Grid en exécutant la commande.

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_APP_ID] -p [REPLACE_WITH_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```
2. Créez votre abonnement en exécutant la commande.

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_B_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > Dans ce scénario, nous utilisons une rubrique système Event Grid. Consultez [cette page](/cli/azure/eventgrid) si vous souhaitez créer un abonnement pour des rubriques personnalisées ou des domaines Event Grid à l’aide d’Azure CLI.
3. Si tout a été correctement configuré, vous pourrez créer l’abonnement webhook dans votre rubrique Event Grid.

    > [!NOTE]
    > À ce stade, Event Grid transmet le jeton du porteur Azure AD au client webhook dans chaque message. Vous devez valider le jeton d’autorisation dans votre webhook.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’information sur la surveillance des remises des événements, consultez [Surveiller la remise des messages Event Grid](monitor-event-delivery.md).
* Pour plus d’informations sur la clé d’authentification, consultez la page [Sécurité et authentification Azure Event Grid](security-authentication.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).