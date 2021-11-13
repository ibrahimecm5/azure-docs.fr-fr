---
title: Créer une application de fonction sans secrets de stockage par défaut dans sa définition
titleSuffix: Azure Functions
ms.service: azure-functions
description: Découvrez comment supprimer des chaînes de connexion de stockage de la définition de votre application de fonction.
ms.topic: tutorial
ms.date: 10/20/2021
ms.openlocfilehash: 8e6e4bfc80f2154843aab5d3971854054f4319b4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271152"
---
# <a name="tutorial-create-a-function-app-that-connects-to-azure-services-using-identities-instead-of-secrets"></a>Tutoriel : Créer une application de fonction se connectant aux services Azure à l’aide d’identités plutôt que de secrets

Ce tutoriel vous montre comment configurer une application de fonction à l’aide d’identités Azure Active Directory plutôt que de secrets ou de chaînes de connexion, dans la mesure du possible. L’utilisation d’identités vous permet d’éviter une fuite accidentelle de secrets sensibles et peut offrir une meilleure visibilité de la façon dont les données sont accessibles. Pour en savoir plus sur les connexions basées sur l’identité, consultez [Configurer une connexion basée sur l’identité](functions-reference.md#configure-an-identity-based-connection).

Même si les procédures indiquées fonctionnent généralement pour tous les langages, ce tutoriel prend plus particulièrement en charge les fonctions de bibliothèque de classes C# sur Windows. 

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Créer une application de fonction dans Azure à l’aide d’un modèle ARM
> * Activer les identités managées affectées par le système et les identités managées affectées par l’utilisateur sur l’application de fonction
> * Créer des attributions de rôles qui accordent des autorisations à d’autres ressources
> * Déplacer les secrets qui ne peuvent pas être remplacés par des identités dans Azure Key Vault
> * Configurer une application pour se connecter au stockage hôte par défaut à l’aide de son identité managée

Une fois ce tutoriel terminé, vous devez effectuer le tutoriel suivant qui montre comment [Utiliser des connexions basées sur l’identité plutôt que des secrets avec des déclencheurs et des liaisons]. 

## <a name="prerequisites"></a>Prérequis

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.

## <a name="why-use-identity"></a>Pourquoi utiliser l’identité ?

La gestion des secrets et des informations d’identification est un défi courant pour les équipes de toutes tailles. Les secrets doivent être sécurisés contre le vol ou les divulgations accidentelles, et il peut être nécessaire de les alterner régulièrement. De nombreux services Azure vous permettent d’utiliser à la place une identité dans [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) pour authentifier les clients et effectuer des vérifications par rapport à des autorisations qui peuvent être modifiées et révoquées rapidement. Cela permet de mieux contrôler la sécurité des applications, avec moins de frais opérationnels. Une identité peut être un utilisateur humain, tel que le développeur d’une application, ou une application en cours d’exécution dans Azure avec une [identité managée](../active-directory/managed-identities-azure-resources/overview.md).

Certains services ne prennent pas en charge l’authentification Azure Active Directory, de sorte que les secrets peuvent toujours être exigés par vos applications. Toutefois, ils peuvent être stockés dans [Azure Key Vault](../key-vault/general/overview.md), ce qui permet de simplifier le cycle de vie de gestion de vos secrets. L’accès à un coffre de clés est également contrôlé avec des identités.

En comprenant comment utiliser des identités plutôt que des secrets quand c’est possible et comment utiliser Key Vault quand ça ne l’est pas, vous pouvez réduire les risques, diminuer les frais opérationnels et améliorer globalement la posture de sécurité pour vos applications.

## <a name="create-a-function-app-that-uses-key-vault-for-necessary-secrets"></a>Créer une application de fonction utilisant Key Vault pour les secrets nécessaires

Azure Files est un exemple de service qui ne prend pas encore en charge l’authentification Azure Active Directory pour les partages de fichiers SMB. Azure Files est le système de fichiers par défaut pour les déploiements Windows sur les plans Premium et Consommation. Nous pourrions [supprimer entièrement Azure Files](./storage-considerations.md#create-an-app-without-azure-files), mais cela introduit des limitations que vous ne souhaitez peut-être pas. Au lieu de cela, vous allez déplacer la chaîne de connexion Azure Files dans Azure Key Vault. De cette façon, elle est gérée de manière centralisée, avec l’accès contrôlé par l’identité.

### <a name="create-an-azure-key-vault"></a>Créer un Azure Key Vault

Tout d’abord, vous avez besoin d’un coffre de clés pour stocker les secrets. Vous allez le configurer pour qu’il utilise le [contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/overview.md) afin de déterminer qui peut lire les secrets dans le coffre.

1. Dans le [portail Azure](https://portal.azure.com), choisissez **Créer une ressource (+)** .

1. Dans la page **Créer une ressource**, sélectionnez **Sécurité** > **Key Vaut**.

1. Dans la page **Informations de base**, configurez le coffre de clés à l’aide du tableau suivant.

    | Option      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel est créée cette nouvelle application de fonction est créée. |
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nom du nouveau groupe de ressources dans lequel créer votre application de fonction. |
    | **Nom du coffre de clés** | Nom globalement unique | Nom qui identifie votre nouveau coffre de clés. Le nom du coffre doit contenir uniquement des caractères alphanumériques et des tirets, et ne peut pas commencer par un chiffre. |
    | **Niveau tarifaire** | Standard | Options de facturation. Le niveau Standard est suffisant pour ce tutoriel. |
    |**Région**| Région recommandée | Choisissez une [région](https://azure.microsoft.com/regions/) près de chez vous ou près d’autres services auxquels ont accès vos fonctions. |

    Utilisez les sélections par défaut pour les sections « Options de récupération ». 

1. Notez le nom que vous avez utilisé, car vous en aurez besoin plus tard.

1. Cliquez sur **Suivant : Stratégies d’accès** pour accéder à l’onglet **Stratégie d’accès**.

1. Sous **Modèle d’autorisation**, choisissez **Contrôle d’accès en fonction du rôle Azure**

1. Sélectionnez **Revoir + créer**. Examinez la configuration, puis cliquez sur **Créer**.

### <a name="set-up-an-identity-and-permissions-for-the-app"></a>Configurer une identité et des autorisations pour l’application

Pour utiliser Azure Key Vault, votre application doit avoir une identité qui peut se voir accorder l’autorisation de lire les secrets. Cette application utilise une identité affectée par l’utilisateur afin que les autorisations puissent être configurées avant même la création de l’application. Vous pouvez en savoir plus sur les identités managées pour Azure Functions dans la rubrique [Guide pratique pour utiliser des identités managées dans Azure Functions](../app-service/overview-managed-identity.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

1. Dans le [portail Azure](https://portal.azure.com), choisissez **Créer une ressource (+)** .

1. Dans la page **Créer une ressource**, sélectionnez **Identité** > **Identité managée affectée par l’utilisateur**.

1. Dans la page **Informations de base**, configurez l’identité à l’aide du tableau suivant.

    | Option      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel est créée cette nouvelle application de fonction est créée. |
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nom du nouveau groupe de ressources dans lequel créer votre application de fonction. |
    |**Région**| Région recommandée | Choisissez une [région](https://azure.microsoft.com/regions/) près de chez vous ou près d’autres services auxquels ont accès vos fonctions. |
    | **Nom** | Nom globalement unique | Nom qui identifie votre nouvelle identité affectée par l’utilisateur. |

1. Sélectionnez **Revoir + créer**. Examinez la configuration, puis cliquez sur **Créer**.

1. Lorsque l’identité est créée, accédez-y dans le portail. Sélectionnez **Propriétés**, puis notez l’**ID de la ressource**, car vous en aurez besoin ultérieurement.

1. Sélectionnez **Attributions de rôles Azure**, puis cliquez sur **Ajouter une attribution de rôle (préversion)** .

1. Dans la page **Ajouter une attribution de rôle (préversion)** , utilisez les options comme indiqué dans le tableau ci-dessous.

    | Option      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **Portée** |  Key Vault |  Une étendue est un ensemble de ressources auxquelles s’applique l’attribution de rôle. La portée a des niveaux hérités à des niveaux inférieurs. Par exemple, si vous sélectionnez une étendue d’abonnement, l’attribution de rôle s’applique à tous les groupes de ressources et à toutes les ressources de l’abonnement. |
    |**Abonnement**| Votre abonnement | Abonnement sous lequel est créée cette nouvelle application de fonction est créée. |
    |**Ressource**| Votre coffre de clés | Le coffre de clés que vous avez créé précédemment. |
    | **Rôle** | Utilisateur des secrets Key Vault | Un rôle est une collection d’autorisations qui sont accordées. Le rôle Utilisateur des secrets Key Vault autorise l’identité à lire les valeurs de secret à partir du coffre. |

1. Sélectionnez **Enregistrer**. L’affichage du rôle peut prendre une minute ou deux lorsque vous actualisez la liste des attributions de rôles pour l’identité.

L’identité est maintenant en mesure de lire les secrets stockés dans le coffre. Plus loin dans ce tutoriel, vous allez ajouter des attributions de rôles supplémentaires à des fins différentes.

### <a name="generate-a-template-for-creating-a-function-app"></a>Générer un modèle pour créer une application de fonction

L’expérience du portail pour la création d’une application de fonction n’interagit pas avec Azure Key Vault. Vous devez donc générer et modifier un modèle Azure Resource Manager. Vous pouvez ensuite utiliser ce modèle pour créer votre application de fonction référençant la chaîne de connexion Azure Files à partir de votre coffre de clés.

> [!IMPORTANT]
> Ne créez pas l’application de fonction tant que vous n’avez pas modifié le modèle ARM. La configuration Azure Files doit être effectuée au moment de la création de l’application.

1. Dans le [portail Azure](https://portal.azure.com), choisissez **Créer une ressource (+)** .

1. Dans la page **Créer une ressource**, sélectionnez **Calcul** > **Application de fonction**.

1. Dans la page **Informations de base**, utilisez l’application de fonction comme indiqué dans le tableau ci-dessous.

    | Option      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel est créée cette nouvelle application de fonction est créée. |
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nom du nouveau groupe de ressources dans lequel créer votre application de fonction. |
    | **Nom de l’application de fonction** | Nom globalement unique | Nom qui identifie votre nouvelle Function App. Les caractères valides sont `a-z` (insensible à la casse), `0-9`et `-`.  |
    |**Publier**| Code | Choix de publier des fichiers de code ou un conteneur Docker. |
    | **Pile d’exécution** | .NET | Ce tutoriel utilise .NET. |
    |**Région**| Région recommandée | Choisissez une [région](https://azure.microsoft.com/regions/) près de chez vous ou près d’autres services auxquels ont accès vos fonctions. |

1. Sélectionnez **Revoir + créer**. Votre application utilise les valeurs par défaut de la page **Hébergement** et **Supervision**. Vous êtes invité à passer en revue les options par défaut. Elles seront incluses dans le modèle ARM que vous générez. 

1. Au lieu de créer votre application de fonction ici, choisissez **Télécharger un modèle pour automation**, à droite du bouton **Suivant**.

1. Dans la page du modèle, sélectionnez **Déployer**, puis dans la page Déploiement personnalisé, sélectionnez **Modifier le modèle**.

    :::image type="content" source="./media/functions-identity-connections-tutorial/function-app-portal-template-deploy-button.png" alt-text="Capture d’écran de l’emplacement du bouton Déployer en haut de l’écran du modèle.":::

### <a name="edit-the-template"></a>Modifier le modèle

Vous allez maintenant modifier le modèle pour stocker la chaîne de connexion Azure Files dans Key Vault et permettre à votre application de fonction de la référencer. Avant de poursuivre, assurez-vous de disposer des valeurs suivantes définies dans les sections précédentes :

- ID de ressource de l’identité affectée par l’utilisateur
- nom de votre coffre de clés

> [!NOTE]
> Si vous deviez créer un modèle complet pour l’automatisation, vous pourriez inclure des définitions pour les ressources d’identité et d’attribution de rôle, avec les clauses `dependsOn` appropriées. Cela remplacerait les étapes précédentes qui utilisaient le portail. Consultez les [conseils Azure Resource Manager](../azure-resource-manager/templates/syntax.md) et la documentation propre à chaque service.


1. Dans l’éditeur, recherchez l’emplacement où le tableau `resources` commence. Avant la définition de la fonction d’application, ajoutez la section suivante qui place la chaîne de connexion Azure Files dans Key Vault. Remplacez « VAULT_NAME » par le nom de votre coffre de clés.

    ```json
    {
        "type": "Microsoft.KeyVault/vaults/secrets",
        "apiVersion": "2016-10-01",
        "name": "VAULT_NAME/azurefilesconnectionstring",
        "properties": {
            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
        },
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
        ]
    },
    ``` 
    
1. Dans la définition de la ressource d’application de fonction (qui a la valeur `type` définie sur `Microsoft.Web/sites`), ajoutez `Microsoft.KeyVault/vaults/VAULT_NAME/secrets/azurefilesconnectionstring` au tableau `dependsOn`. À nouveau, remplacez « VAULT_NAME » par le nom de votre coffre de clés. Ainsi, votre application n’est pas créée tant que le secret n’est pas défini. Le tableau `dependsOn` doit ressembler à l’exemple qui suit.

    ```json
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "tags": null,
            "dependsOn": [
                "microsoft.insights/components/idcxntut",
                "Microsoft.KeyVault/vaults/VAULT_NAME/secrets/azurefilesconnectionstring",
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
            ],
            // ...
        }
    ```

1. Ajoutez le bloc `identity` de l’exemple suivant dans la définition de votre ressource d’application de fonction. Remplacez « IDENTITY_RESOURCE_ID » par l’ID de ressource de votre identité affectée par l’utilisateur.

    ```json
    {
        "apiVersion": "2018-11-01",
        "name": "[parameters('name')]",
        "type": "Microsoft.Web/sites",
        "kind": "functionapp",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities": {
                "IDENTITY_RESOURCE_ID": {}
            }
        },
        "tags": null,
        // ...
    }
    ```

    Ce bloc `identity` définit également une identité affectée par le système, que vous allez utiliser ultérieurement dans ce tutoriel.

1. Ajoutez la propriété `keyVaultReferenceIdentity` à l’objet `properties` pour l’application de fonction, comme dans l’exemple ci-dessous. Remplacez « IDENTITY_RESOURCE_ID » par l’ID de ressource de votre identité affectée par l’utilisateur.

    ```json
    {
        // ...
         "properties": {
                "name": "[parameters('name')]",
                "keyVaultReferenceIdentity": "IDENTITY_RESOURCE_ID",
                // ...
         }
    }
    ```

    Vous avez besoin de cette configuration car une application peut avoir plusieurs identités affectées par l’utilisateur configurées. Chaque fois que vous souhaitez utiliser une identité affectée par l’utilisateur, vous devez spécifier laquelle à l’aide d’un ID. Cela ne s’applique pas aux identités affectées par le système, car une application n’en aura toujours qu’une. De nombreuses fonctionnalités qui utilisent une identité managée supposent qu’elles doivent utiliser celle affectée par défaut par le système.

1. À présent, recherchez l’objet JSON qui définit le paramètre d’application `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`, ce qui doit ressembler à l’exemple suivant :

    ```json
    {
        "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
    },
    ```

1. Remplacez le champ `value` par une référence au secret, comme indiqué dans l’exemple suivant. Remplacez « VAULT_NAME » par le nom de votre coffre de clés.

    ```json
    {
        "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
        "value": "[concat('@Microsoft.KeyVault(SecretUri=', reference(resourceId('Microsoft.KeyVault/vaults/secrets', 'VAULT_NAME', 'azurefilesconnectionstring')).secretUri, ')')]"
    },
    ```

1. Sélectionnez **Enregistrer** pour enregistrer le modèle ARM mis à jour.

### <a name="deploy-the-modified-template"></a>Déployer le modèle modifié

1. Assurez-vous que vos options de création, y compris **Groupe de ressources**, sont toujours correctes, puis sélectionnez **Vérifier + créer**.

1. Une fois votre modèle validé, notez le **Nom de votre compte de stockage**, car vous utiliserez ce compte ultérieurement. Enfin, sélectionnez **Créer** pour créer vos ressources Azure et déployer votre code dans l’application de fonction. 

1. Une fois le déploiement terminé, sélectionnez **Accéder au groupe de ressources**, puis sélectionnez la nouvelle application de fonction. 

Félicitations ! Vous avez correctement créé votre application de fonction pour référencer la chaîne de connexion Azure Files à partir d’Azure Key Vault.

Chaque fois que votre application doit ajouter une référence à un secret, vous devez simplement définir un nouveau paramètre d’application pointant vers la valeur stockée dans Key Vault. Vous pouvez en savoir plus à ce sujet dans [Informations de références sur Key Vault pour Azure Functions](../app-service/app-service-key-vault-references.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

> [!TIP]
> La [chaîne de connexion Application Insights](../azure-monitor/app/sdk-connection-string.md) et sa clé d’instrumentation incluse ne sont pas considérées comme des secrets et peuvent être récupérées à partir d’App Insights à l’aide d’autorisations [Lecteur](../role-based-access-control/built-in-roles.md#reader). Vous n’avez pas besoin de les déplacer dans Key Vault, même si cela est bien sûr possible.

## <a name="use-managed-identity-for-azurewebjobsstorage-preview"></a>Utiliser une identité managée pour AzureWebJobsStorage (préversion)

Vous allez ensuite utiliser l’identité affectée par le système que vous avez configurée dans les étapes précédentes pour la connexion `AzureWebJobsStorage`. `AzureWebJobsStorage` est utilisé par le runtime Functions et par plusieurs déclencheurs et liaisons pour assurer la coordination entre plusieurs instances en cours d’exécution. Il est obligatoire pour que votre application de fonction fonctionne, et comme Azure Files, il est configuré avec une chaîne de connexion par défaut lorsque vous créez une application de fonction.

### <a name="grant-the-system-assigned-identity-access-to-the-storage-account"></a>Accorder à l’identité affectée par le système l’accès au compte de stockage

Comme pour les étapes que vous avez suivies avec l’identité affectée par l’utilisateur et votre coffre de clés, vous allez maintenant créer une attribution de rôle qui accorde à votre compte de stockage l’accès à l’identité affectée par le système.

1. Dans le [portail Azure](https://portal.azure.com), accédez au compte de stockage qui a été créé précédemment avec votre application de fonction.

1. Sélectionnez **Contrôle d’accès (IAM)** . C’est là que vous pouvez voir et configurer qui a accès à la ressource.

1. Cliquez sur **Ajouter**, puis sélectionnez **Ajouter une attribution de rôle**.

1. Recherchez **Propriétaire des données Blob du stockage**, sélectionnez-le, puis cliquez sur **Suivant**

1. Dans l’onglet **Membres**, sous **Attribuer l’accès à**, choisissez **Identité managée**

1. Cliquez sur **Sélectionner des membres** pour ouvrir le volet **Sélectionner des identités managées**.

1. Vérifiez que l’**Abonnement** est celui dans lequel vous avez créé les ressources précédemment.

1. Dans le sélecteur **Identité managée**, choisissez **Application de fonction** dans la catégorie **Identité managée affectée par le système**. Un nombre entre parenthèses peut être affiché en regard de l’étiquette « Application de fonction », indiquant le nombre d’applications de l’abonnement ayant des identités affectées par le système.

1. Votre application doit apparaître dans une liste sous les champs d’entrée. Si vous ne la voyez pas, vous pouvez utiliser la zone **Sélectionner** pour filtrer les résultats avec le nom de votre application.

1. Cliquez sur votre application. Elle doit descendre dans la section **Membres sélectionnés**. Cliquez sur **Sélectionner**.

1. De retour dans l’écran **Ajouter une attribution de rôle**, cliquez sur **Vérifier + attribuer**. Vérifiez la configuration, puis cliquez sur **Vérifier + attribuer**.

### <a name="edit-the-azurewebjobsstorage-configuration"></a>Modifier la configuration d’AzureWebJobsStorage

Vous allez ensuite mettre à jour votre application de fonction afin d’utiliser son identité affectée par le système lorsqu’elle utilise le service BLOB pour le stockage hôte.

> [!IMPORTANT]
> La configuration d’`AzureWebJobsStorage` est utilisée par certains déclencheurs et liaisons, et ces extensions doivent également pouvoir utiliser des connexions basées sur l’identité. Les applications qui utilisent des déclencheurs d’objet blob ou de hub d’événements peuvent avoir besoin de mettre à jour ces extensions. Étant donné qu’aucune fonction n’a été définie pour cette application, il n’y a pas encore de problème. Pour en savoir plus sur cette exigence, consultez [Connexion au stockage hôte à l’aide d’une identité (préversion)](./functions-reference.md#connecting-to-host-storage-with-an-identity-preview).
>
> De même, `AzureWebJobsStorage` est utilisé pour les artefacts de déploiement lors d’une utilisation de la génération côté serveur dans le plan Consommation Linux. Lorsque vous activez les connexions basées sur l’identité pour `AzureWebJobsStorage` dans le plan Consommation Linux, vous devez effectuer le déploiement via un [package de déploiement externe](/run-functions-from-deployment-package).

1. Sur le [portail Azure](https://portal.azure.com), accédez à votre application de fonction.

1. Sous **Paramètres**, sélectionnez **Configuration**.

1. Sélectionnez le bouton **Modifier** en regard du paramètre d’application **AzureWebJobsStorage**, puis modifiez ce dernier en fonction des valeurs suivantes.

    | Option      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **Nom** |  AzureWebJobsStorage__accountName | Mettez à jour le nom spécifié pour **AzureWebJobsStorage** avec le nom exact `AzureWebJobsStorage__accountName`. Ce paramètre indique à l’hôte d’utiliser l’identité au lieu de rechercher un secret stocké. Le nouveau paramètre utilise un trait de soulignement double (`__`), qui est un caractère spécial dans les paramètres d’application.  |
    | **Valeur** | Nom de votre compte | Mettez à jour le nom figurant dans la chaîne de connexion avec votre **Nom de compte**. |

    Cette configuration permet au système de savoir qu’il doit utiliser une identité pour se connecter à la ressource.

1. Sélectionnez **OK**, puis **Enregistrer** > **Continuer** pour enregistrer vos modifications. 

Vous avez supprimé l’exigence de chaîne de connexion de stockage pour AzureWebJobsStorage en configurant votre application pour qu’elle se connecte plutôt à des objets blob à l’aide d’identités managées.  

## <a name="next-steps"></a>Étapes suivantes 

Ce tutoriel vous a montré comment créer une application de fonction sans stocker de secrets dans sa configuration.

Dans le prochain tutoriel, vous allez apprendre à utiliser une identité dans des connexions de déclencheurs et de liaisons.

> [!div class="nextstepaction"]
> [Utiliser des connexions basées sur l’identité plutôt que des secrets avec des déclencheurs et des liaisons]

[Utiliser des connexions basées sur l’identité plutôt que des secrets avec des déclencheurs et des liaisons]: ./functions-identity-based-connections-tutorial-2.md
