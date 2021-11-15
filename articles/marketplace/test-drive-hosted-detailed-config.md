---
title: Configuration détaillée d’une version d'évaluation hébergée dans la Place de marché Azure
description: Explication des étapes de configuration d’une version d’évaluation hébergée dans la Place de marché commerciale
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/26/2021
ms.openlocfilehash: 5fde0e1d9dc78c735ae9e889af5fbd889a588540
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851018"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Configuration détaillée pour les versions d'évaluation hébergées

Cet article explique comment configurer une version d’évaluation hébergée pour Dynamics 365 for Customer Engagement et Power Apps ou Dynamics 365 for Operations.

> [!TIP]
> Pour voir la vue client du test drive dans la place de marché commerciale, consultez [Qu’est-ce que la Place de marché Azure](/marketplace/azure-marketplace-overview#take-action-on-a-listing) et [Qu’est-ce que Microsoft AppSource ?](/marketplace/appsource-overview).

## <a name="configure-for-dynamics-365-customer-engagement--power-apps"></a>Configuration pour Dynamics 365 Customer Engagement et Power Apps

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vue des espaces de travail](#tab/workspaces-view)

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
2. Si vous ne pouvez pas accéder au lien ci-dessus, vous devez envoyer une demande [ici](https://appsource.microsoft.com/partners/list-an-app) pour publier votre application. Une fois que nous examinerons la demande, vous disposerez d’un accès pour démarrer le processus de publication.
3. Trouvez une offre **Dynamics 365 for Customer Engagement et Power Apps** existante ou créez-en une nouvelle.
4. Dans la page **Configuration de l’offre**, cochez la case **Activer une version d’évaluation** et sélectionnez un **Type de version d’évaluation** (cf. puce ci-dessous), puis **Enregistrer le brouillon**.

    [![Illustre la sélection de la case à cocher « Activer une version d'évaluation ».](./media/test-drive/enable-test-drive-check-box-workspaces.png)](./media/test-drive/enable-test-drive-check-box-workspaces.png#lightbox)

    - **Type of Test Drive** (Type de version d’évaluation) : choisissez **Microsoft Hosted (Dynamics 365 for Customer Engagement)** (Hébergé par Microsoft (Dynamics 365 for Customer Engagement)). Cela indique que Microsoft héberge et assure la maintenance du service qui effectue l’approvisionnement et le déprovisionnement de l’utilisateur de la version d’évaluation.

5. Accordez l’autorisation Microsoft AppSource pour approvisionner et déprovisionner les utilisateurs de la version d’évaluation dans votre locataire à l’aide [des instructions se trouvant ici](./test-drive-azure-subscription-setup.md). Au cours de cette étape, vous allez générer les valeurs **Azure AD App ID** (ID Azure AD App) et **Azure AD App Key** (Clé Azure AD App) mentionnées ci-dessous.
6. Renseignez les champs de la page **Configuration technique** > **de la version d’évaluation**.

    [![Représente la page Configuration technique de la version d’évaluation.](media/test-drive/technical-config-details-workspaces.png)](media/test-drive/technical-config-details-workspaces.png#lightbox)

    - **Max Concurrent Test Drives** (Versions d’évaluation simultanées max.) : le nombre d’utilisateurs simultanés qui peuvent avoir une version d’évaluation active en même temps. Chaque utilisateur se sert d’une licence Dynamics quand sa version d’évaluation est active. Assurez-vous donc que vous avez au moins autant de licences Dynamics disponibles que d’utilisateurs. Nous recommandons une valeur de 3 à 5.
    - **Durée de la version d'évaluation** : nombre d’heures pendant lesquelles la version d'évaluation de l’utilisateur est active. Une fois le délai expiré, l’utilisateur sera déprovisionné de votre locataire. Nous recommandons de 2 à 24 heures en fonction de la complexité de votre application. L’utilisateur peut toujours demander une autre version d’évaluation s’il n’a pas eu assez de temps et s’il souhaite de nouveau accéder à la version d’évaluation.
    - **URL de l’instance** : URL à laquelle l’utilisateur de la version d'évaluation sera envoyé lorsqu’il démarrera la version d'évaluation. Il s’agit généralement de l’URL de votre instance Dynamics 365 sur laquelle votre application et les exemples de données sont installés. Valeur d'exemple : `https://testdrive.crm.dynamics.com`.
    - **Instance web API URL** (URL d’API web de l’instance) : l’URL d’API web de votre instance Dynamics 365. Récupérez cette valeur en vous connectant à votre instance Microsoft Dynamics 365 et en accédant à **Paramètres** > **Personnalisation** > **Ressources du développeur** > **API web de l’instance** et en copiant l’adresse (URL). Exemple de valeur :

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Exemple d’API web de l’instance.":::

    - **Nom du rôle** : le nom du rôle de sécurité Dynamics 365 personnalisé que vous avez créé pour la version d'évaluation, ou vous pouvez utiliser un rôle existant. Un nouveau rôle doit avoir les privilèges minimum requis ajoutés au rôle pour se connecter à une instance Customer Engagement. Reportez-vous aux [Privilèges minimaux requis pour se connecter à Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). C’est le rôle qui sera assigné aux utilisateurs lors de leur utilisation de la version d’évaluation. Valeur d'exemple : `testdriverole`.
    
        > [!IMPORTANT]
        > Assurez-vous que la vérification du groupe de sécurité n’est pas ajoutée. Cela permet à l’utilisateur d’être synchronisé avec l’instance Customer Engagement.

    - **ID de locataire Azure Active Directory** : l’ID du locataire Azure pour votre instance Dynamics 365. Pour récupérer cette valeur, connectez-vous au portail Azure et accédez à **Azure Active Directory** > **Propriétés** et copiez l’ID d’annuaire. Exemple de valeur : 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Nom de locataire Azure Active Directory** : le nom du locataire Azure pour votre instance Dynamics 365. Utilisez le format `<tenantname>.onmicrosoft.com`. Valeur d'exemple : `testdrive.onmicrosoft.com`.
    - **ID d’application Azure Active Directory ID** : l’ID d’application Azure Active Directory (AD) que vous avez créé à l’étape 5. Valeur d'exemple : `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Clé secrète client d’application Azure Active Directory** : le secret pour l’application Azure AD créée à l’étape 5. Valeur d'exemple : `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.

7. Renseignez les champs sur la page **Liste de test drives de la Place de marché**.

    [ ![Page de détails de la liste de test drives de la Place de marché.](./media/test-drive/marketplace-listing-details-workspaces.png) ](./media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **Description** : une présentation de votre version d’évaluation. L’utilisateur pourra voir ce texte pendant l’approvisionnement de la version d’évaluation. Ce champ prend en charge le langage HTML si vous souhaitez proposer du contenu mis en forme (obligatoire).
    - **Manuel de l’utilisateur** : manuel de l’utilisateur, au format PDF, qui aide les utilisateurs de la version d'évaluation à comprendre comment utiliser votre application (obligatoire).
    - **Test Drive Demo Video** : une vidéo de présentation de votre application (facultatif).

#### <a name="current-view"></a>[Affichage actuel](#tab/current-view)

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
2. Si vous ne pouvez pas accéder au lien ci-dessus, vous devez envoyer une demande [ici](https://appsource.microsoft.com/partners/list-an-app) pour publier votre application. Une fois que nous examinerons la demande, vous disposerez d’un accès pour démarrer le processus de publication.
3. Trouvez une offre **Dynamics 365 for Customer Engagement et Power Apps** existante ou créez-en une nouvelle.
4. Activez la case à cocher **Activer une version d’évaluation** et sélectionnez un type de **Version d’évaluation** (voir la puce ci-dessous), puis sélectionnez **Enregistrer**.

    [![Sélection de la case à cocher « Activer une version d’évaluation ».](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Type d'essai** - Choisissez **Microsoft Hosted (Dynamics 365 for Customer Engagement & Power Apps)** . Cela indique que Microsoft héberge et assure la maintenance du service qui effectue l’approvisionnement et le déprovisionnement de l’utilisateur de la version d’évaluation.

5. Accordez l’autorisation Microsoft AppSource pour approvisionner et déprovisionner les utilisateurs de la version d’évaluation dans votre locataire à l’aide [des instructions se trouvant ici](./test-drive-azure-subscription-setup.md). Au cours de cette étape, vous allez générer les valeurs **Azure AD App ID** (ID Azure AD App) et **Azure AD App Key** (Clé Azure AD App) mentionnées ci-dessous.
6. Renseignez les champs de la page **Configuration technique de la version d’évaluation**.

    [![La page Configuration technique de la version d’évaluation.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Max Concurrent Test Drives** (Versions d’évaluation simultanées max.) : le nombre d’utilisateurs simultanés qui peuvent avoir une version d’évaluation active en même temps. Chaque utilisateur se sert d’une licence Dynamics quand sa version d’évaluation est active. Assurez-vous donc que vous avez au moins autant de licences Dynamics disponibles que d’utilisateurs. Nous recommandons une valeur de 3 à 5.
    - **Durée de la version d'évaluation** : nombre d’heures pendant lesquelles la version d'évaluation de l’utilisateur est active. Une fois le délai expiré, l’utilisateur sera déprovisionné de votre locataire. Nous recommandons de 2 à 24 heures en fonction de la complexité de votre application. L’utilisateur peut toujours demander une autre version d’évaluation s’il n’a pas eu assez de temps et s’il souhaite de nouveau accéder à la version d’évaluation.
    - **URL d’instance**
        - *Engagement client* : URL à laquelle l’utilisateur de la version d’évaluation sera envoyé quand il démarrera la version d’évaluation. Il s’agit généralement de l’URL de votre instance Dynamics 365 sur laquelle votre application et les exemples de données sont installés. Valeur d'exemple : `https://testdrive.crm.dynamics.com`.
        - *Application canevas (Power Apps)*
            1. Ouvrez la page **PowerApps portal** (portail PowerApps) et connectez-vous.
            2. Sélectionnez **Apps**, puis les points de suspension au niveau de l’application.
            4. Sélectionnez **Details** (Détails).
            5. Copiez le **lien web** sous l’onglet **Details** :

                [ ![Fenêtre de l’application canevas TestDrive.](./media/test-drive/testdrive-canvas-app.png) ](./media/test-drive/testdrive-canvas-app.png#lightbox)

    - **URL d’API web de l’instance**
        - *Engagement client* : URL de l’API web pour votre instance Dynamics 365. Récupérez cette valeur en vous connectant à votre instance Microsoft Dynamics 365, en sélectionnant **Paramètres** > **Personnalisation** > **Ressources du développeur** > **API web de l’instance** et en copiant l’adresse (URL). Exemple de valeur :

            :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Exemple d’API web de l’instance.":::

        - *Application canevas (Power Apps)*  : si vous n’utilisez pas CE/Dataverse comme back-end pour votre application canevas, utilisez `https://localhost` comme espace réservé.

    - **Nom de rôle**
        - *Engagement client* : le nom du rôle de sécurité Dynamics 365 personnalisé que vous avez créé pour la version d’évaluation, ou vous pouvez utiliser un rôle existant. Un nouveau rôle doit avoir les privilèges minimum requis ajoutés au rôle pour se connecter à une instance Customer Engagement. Reportez-vous aux [Privilèges minimaux requis pour se connecter à Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). C’est le rôle qui sera assigné aux utilisateurs lors de leur utilisation de la version d’évaluation. Valeur d'exemple : `testdriverole`.
        - *Application canevas (Power Apps)*  : utilisez « NA » si vous n’utilisez pas CE/Dataverse comme source de données back-end.
    
    > [!IMPORTANT]
    > Assurez-vous que la vérification du groupe de sécurité n’est pas ajoutée. Cela permet à l’utilisateur d’être synchronisé avec l’instance Customer Engagement.

    - **ID de locataire Azure Active Directory** : l’ID du locataire Azure pour votre instance Dynamics 365. Pour récupérer cette valeur, connectez-vous au portail Azure et accédez à **Azure Active Directory** > **Propriétés** et copiez l’ID d’annuaire. Exemple de valeur : 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Nom de locataire Azure Active Directory** : le nom du locataire Azure pour votre instance Dynamics 365. Utilisez le format `<tenantname>.onmicrosoft.com`. Valeur d'exemple : `testdrive.onmicrosoft.com`.
    - **ID d’application Azure Active Directory ID** : l’ID d’application Azure Active Directory (AD) que vous avez créé à l’étape 5. Valeur d'exemple : `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Clé secrète client d’application Azure Active Directory** : le secret pour l’application Azure AD créée à l’étape 5. Valeur d'exemple : `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.

7. Fournissez les détails de l’annonce de la Place de marché. Sélectionnez **Langue** pour afficher les autres champs obligatoires.

    [ ![Page de détails de la liste de test drives de la Place de marché.](./media/test-drive/marketplace-listing-details-workspaces.png) ](./media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **Description** : une présentation de votre version d’évaluation. L’utilisateur pourra voir ce texte pendant l’approvisionnement de la version d’évaluation. Ce champ prend en charge le langage HTML si vous souhaitez proposer du contenu mis en forme (obligatoire).
    - **Manuel de l’utilisateur** : manuel de l’utilisateur, au format PDF, qui aide les utilisateurs de la version d'évaluation à comprendre comment utiliser votre application (obligatoire).
    - **Test Drive Demo Video** : une vidéo de présentation de votre application (facultatif).

---

## <a name="configure-for-dynamics-365-operations"></a>Configurer pour Dynamics 365 for Operations

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vue des espaces de travail](#tab/workspaces-view)

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
2. Si vous ne pouvez pas accéder au lien ci-dessus, vous devez envoyer une demande [ici](https://appsource.microsoft.com/partners/list-an-app) pour publier votre application. Une fois que nous examinerons la demande, vous disposerez d’un accès pour démarrer le processus de publication.
3. Trouvez une offre **Dynamics 365 for Operations** existante ou créez une nouvelle offre **Dynamics 365 for Operations**.
4. Dans la page **Configuration de l’offre**, cochez la case **Activer une version d’évaluation** et sélectionnez un **Type de version d’évaluation** (cf. puce ci-dessous), puis **Enregistrer le brouillon**.

    [![Sélectionnez la case Activer une version d’évaluation.](media/test-drive/enable-test-drive-check-box-operations-workspaces.png)](media/test-drive/enable-test-drive-check-box-operations-workspaces.png#lightbox)

    - **Type de version d'évaluation** : choisissez l’option **Dynamics 365 for Operations**. Cela signifie que Microsoft héberge et assure la maintenance du service qui effectue l’approvisionnement et le déprovisionnement de l’utilisateur de la version d’évaluation.

5. Accordez l’autorisation Microsoft AppSource pour approvisionner et déprovisionner les utilisateurs de la version d’évaluation dans votre locataire à l’aide [des instructions se trouvant ici](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md). Au cours de cette étape, vous allez générer les valeurs **Azure AD App ID** (ID Azure AD App) et **Azure AD App Key** (Clé Azure AD App) mentionnées ci-dessous.
6. Renseignez les champs de la page **Configuration technique** >  **de la version d’évaluation**.

    [ ![Illustre la page de configuration technique de la Place de marché.](./media/test-drive/technical-config-details-operations-workspaces.png) ](./media/test-drive/technical-config-details-operations-workspaces.png#lightbox)

    - **Max Concurrent Test Drives** (Versions d’évaluation simultanées max.) : le nombre d’utilisateurs simultanés qui peuvent avoir une version d’évaluation active en même temps. Chaque utilisateur se sert d’une licence Dynamics quand sa version d’évaluation est active. Assurez-vous donc que vous avez au moins autant de licences Dynamics disponibles que d’utilisateurs. Nous recommandons une valeur de 3 à 5.
    - **Durée de la version d'évaluation** : nombre d’heures pendant lesquelles la version d'évaluation de l’utilisateur est active. Une fois le délai expiré, l’utilisateur sera déprovisionné de votre locataire. Nous recommandons de 2 à 24 heures en fonction de la complexité de votre application. L’utilisateur peut toujours demander une autre version d’évaluation s’il n’a pas eu assez de temps et s’il souhaite de nouveau accéder à la version d’évaluation.
    - **URL de l’instance** : URL à laquelle l’utilisateur de la version d'évaluation sera envoyé lorsqu’il démarrera la version d'évaluation. Il s’agit généralement de l’URL de votre instance Dynamics 365 sur laquelle votre application et les exemples de données sont installés. Valeur d'exemple : `https://testdrive.crm.dynamics.com`.
    - **ID de locataire Azure Active Directory** : l’ID du locataire Azure pour votre instance Dynamics 365. Pour récupérer cette valeur, connectez-vous au portail Azure et accédez à **Azure Active Directory** > **Propriétés** et copiez l’ID d’annuaire. Exemple de valeur : 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Nom de locataire Azure Active Directory** : le nom du locataire Azure pour votre instance Dynamics 365. Utilisez le format `<tenantname>.onmicrosoft.com`. Valeur d'exemple : `testdrive.onmicrosoft.com`.
    - **ID d’application Azure Active Directory ID** : l’ID d’application Azure Active Directory (AD) que vous avez créé à l’étape 5. Valeur d'exemple : `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Clé secrète client d’application Azure Active Directory** : le secret pour l’application Azure AD créée à l’étape 5. Valeur d'exemple : `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.
    - **Entité légale de la version d’essai** : fournissez une entité légale pour affecter un utilisateur à l’évaluation. Vous pouvez en créer une nouvelle dans [Créer ou modifier une entité légale](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity).
    - **Nom du rôle** : nom de l’AOA (arborescence d’objets d’application) du rôle de sécurité Dynamics 365 personnalisé que vous avez créé pour la version d'évaluation. C’est le rôle qui sera assigné aux utilisateurs lors de leur utilisation de la version d’évaluation.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="La page de configuration de la sécurité.":::

7. Renseignez les champs sur la page **Liste de test drives de la Place de marché**.

    [![Page de détails d’annonce sur la Place de marché.](media/test-drive/marketplace-listing-details-ops-workspaces.png)](media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **Description** : une présentation de votre version d’évaluation. L’utilisateur pourra voir ce texte pendant l’approvisionnement de la version d’évaluation. Ce champ prend en charge le langage HTML si vous souhaitez proposer du contenu mis en forme (obligatoire).
    - **Manuel de l’utilisateur** : manuel de l’utilisateur, au format PDF, qui aide les utilisateurs de la version d'évaluation à comprendre comment utiliser votre application (obligatoire).
    - **Test Drive Demo Video** : une vidéo de présentation de votre application (facultatif).

#### <a name="current-view"></a>[Affichage actuel](#tab/current-view)

1. Connectez-vous à l’[Espace partenaires](https://go.microsoft.com/fwlink/?linkid=2165507).
2. Si vous ne pouvez pas accéder au lien ci-dessus, vous devez envoyer une demande [ici](https://appsource.microsoft.com/partners/list-an-app) pour publier votre application. Une fois que nous examinerons la demande, vous disposerez d’un accès pour démarrer le processus de publication.
3. Trouvez une offre **Dynamics 365 for Operations** existante ou créez une nouvelle offre **Dynamics 365 for Operations**.
4. Cochez la case **Activer une version d’évaluation** et sélectionnez un **Type de version d’évaluation** (cf. puce ci-dessous), puis **Enregistrer le brouillon**.

    [![Sélectionnez la case Activer une version d’évaluation.](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Type de version d'évaluation** : choisissez l’option **Dynamics 365 for Operations**. Cela signifie que Microsoft héberge et assure la maintenance du service qui effectue l’approvisionnement et le déprovisionnement de l’utilisateur de la version d’évaluation.

5. Accordez l’autorisation Microsoft AppSource pour approvisionner et déprovisionner les utilisateurs de la version d’évaluation dans votre locataire à l’aide [des instructions se trouvant ici](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md). Au cours de cette étape, vous allez générer les valeurs **Azure AD App ID** (ID Azure AD App) et **Azure AD App Key** (Clé Azure AD App) mentionnées ci-dessous.
6. Renseignez les champs de la page **Configuration technique de la version d’évaluation**.

    [![La page de configuration technique de la Place de marché.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Max Concurrent Test Drives** (Versions d’évaluation simultanées max.) : le nombre d’utilisateurs simultanés qui peuvent avoir une version d’évaluation active en même temps. Chaque utilisateur se sert d’une licence Dynamics quand sa version d’évaluation est active. Assurez-vous donc que vous avez au moins autant de licences Dynamics disponibles que d’utilisateurs. Nous recommandons une valeur de 3 à 5.
    - **Durée de la version d'évaluation** : nombre d’heures pendant lesquelles la version d'évaluation de l’utilisateur est active. Une fois le délai expiré, l’utilisateur sera déprovisionné de votre locataire. Nous recommandons de 2 à 24 heures en fonction de la complexité de votre application. L’utilisateur peut toujours demander une autre version d’évaluation s’il n’a pas eu assez de temps et s’il souhaite de nouveau accéder à la version d’évaluation.
    - **URL de l’instance** : URL à laquelle l’utilisateur de la version d'évaluation sera envoyé lorsqu’il démarrera la version d'évaluation. Il s’agit généralement de l’URL de votre instance Dynamics 365 sur laquelle votre application et les exemples de données sont installés. Valeur d'exemple : `https://testdrive.crm.dynamics.com`.
    - **ID de locataire Azure Active Directory** : l’ID du locataire Azure pour votre instance Dynamics 365. Pour récupérer cette valeur, connectez-vous au portail Azure et accédez à **Azure Active Directory** > **Propriétés** et copiez l’ID d’annuaire. Exemple de valeur : 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Nom de locataire Azure Active Directory** : le nom du locataire Azure pour votre instance Dynamics 365. Utilisez le format `<tenantname>.onmicrosoft.com`. Valeur d'exemple : `testdrive.onmicrosoft.com`.
    - **ID d’application Azure Active Directory ID** : l’ID d’application Azure Active Directory (AD) que vous avez créé à l’étape 5. Valeur d'exemple : `53852862-a2ae-4e43-9461-faa49650a096`.
    - **Clé secrète client d’application Azure Active Directory** : le secret pour l’application Azure AD créée à l’étape 5. Valeur d'exemple : `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`.
    - **Entité légale de la version d’essai** : fournissez une entité légale pour affecter un utilisateur à l’évaluation. Vous pouvez en créer une nouvelle dans [Créer ou modifier une entité légale](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity).
    - **Nom du rôle** : nom de l’AOA (arborescence d’objets d’application) du rôle de sécurité Dynamics 365 personnalisé que vous avez créé pour la version d'évaluation. C’est le rôle qui sera assigné aux utilisateurs lors de leur utilisation de la version d’évaluation.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="La page de configuration de la sécurité.":::

7. Fournissez les détails de l’annonce de la Place de marché. Sélectionnez **Langue** pour afficher les autres champs obligatoires.

    [![Page de détails d’annonce sur la Place de marché.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Description** : une présentation de votre version d’évaluation. L’utilisateur pourra voir ce texte pendant l’approvisionnement de la version d’évaluation. Ce champ prend en charge le langage HTML si vous souhaitez proposer du contenu mis en forme.
    - **Manuel de l’utilisateur** : un manuel de l’utilisateur PDF qui aide les utilisateurs de la version d'évaluation à comprendre comment utiliser votre application.
    - **Test Drive Demo Video** : une vidéo de présentation de votre application (facultatif).

---

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->
