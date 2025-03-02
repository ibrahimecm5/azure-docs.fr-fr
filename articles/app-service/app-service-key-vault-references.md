---
title: Utiliser des références Key Vault
description: Découvrez comment configurer Azure App Service et Azure Functions pour leur faire utiliser des références Azure Key Vault. Mettez les secrets Key Vault à la disposition de votre code d’application.
author: mattchenderson
ms.topic: article
ms.date: 06/11/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 74341e9f4c2436768a73591878f29e7f392b1f3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006826"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Utiliser des références Key Vault pour App Service et Azure Functions

Cette rubrique vous montre comment utiliser des secrets d’Azure Key Vault dans votre application App Service ou Azure Functions, sans avoir à modifier le code. [Azure Key Vault](../key-vault/general/overview.md) est un service qui fournit une gestion centralisée des secrets, avec un contrôle total sur les stratégies d’accès et l’historique d’audit.

## <a name="granting-your-app-access-to-key-vault"></a>Autoriser votre application à accéder à Key Vault

Pour pouvoir lire les secrets dans Key Vault, vous devez créer un coffre et donner à votre application l’autorisation d’y accéder.

1. Créez un coffre de clés en suivant le [Guide de démarrage rapide de Key Vault](../key-vault/secrets/quick-create-cli.md).

1. Créez une [identité managée](overview-managed-identity.md) pour votre application.

    Les références Key Vault utiliseront l’identité attribuée par le système de l’application par défaut, mais vous pouvez [spécifier une identité attribuée par l’utilisateur](#access-vaults-with-a-user-assigned-identity).

1. Créez une [stratégie d’accès dans Key Vault](../key-vault/general/security-features.md#privileged-access) pour l’identité d’application que vous avez créée précédemment. Activez l’autorisation de secret « Get » sur cette stratégie. Ne configurez pas les paramètres « application autorisée » ou `applicationId` car ils sont incompatibles avec une identité managée.

### <a name="access-network-restricted-vaults"></a>Accéder aux coffres restreints du réseau

Si votre coffre est configuré avec des [restrictions réseau](../key-vault/general/overview-vnet-service-endpoints.md), vous devrez également vous assurer que l’application dispose d’un accès réseau.

1. Vérifiez que les fonctionnalités de mise en réseau sortantes de l’application sont configurées, comme décrit dans les [fonctionnalités de mise en réseau App Service](./networking-features.md) et les [options de mise en réseau Azure Functions](../azure-functions/functions-networking-options.md).

    Les applications Linux tentant d’utiliser des points de terminaison privés requièrent également que l’application soit explicitement configurée pour que tout le trafic soit acheminé via le réseau virtuel. Cette exigence sera supprimée dans une prochaine mise à jour. Pour définir cela, utilisez la commande CLI suivante :

    ```azurecli
    az webapp config set --subscription <sub> -g <rg> -n <appname> --generic-configurations '{"vnetRouteAllEnabled": true}'
    ```

2. Assurez-vous que la configuration du coffre compte pour le réseau ou le sous-réseau auquel votre application accède.

> [!NOTE]
> Windows conteneur ne prend actuellement pas en charge les références de Coffre de clé sur l’intégration au réseau virtuel.

### <a name="access-vaults-with-a-user-assigned-identity"></a>Accédez aux coffres avec une identité attribuée par l’utilisateur

Certaines applications doivent référencer des secrets au moment de la création, lorsqu’une identité attribuée par le système n’est pas encore disponible. Dans ce cas, une identité attribuée par l’utilisateur peut être créée et l’accès au coffre doit être accordé à l’avance.

Une fois que vous avez accordé des autorisations à l’identité attribuée par l’utilisateur, procédez comme suit :

1. [Attribuez l’identité](./overview-managed-identity.md#add-a-user-assigned-identity) à votre application si vous ne l’avez pas déjà fait.

1. Configurez l’application pour utiliser cette identité pour les opérations de référence Key Vault en définissant la `keyVaultReferenceIdentity` propriété sur l’ID de ressource de l’identité attribuée par l’utilisateur.

    ```azurecli-interactive
    userAssignedIdentityResourceId=$(az identity show -g MyResourceGroupName -n MyUserAssignedIdentityName --query id -o tsv)
    appResourceId=$(az webapp show -g MyResourceGroupName -n MyAppName --query id -o tsv)
    az rest --method PATCH --uri "${appResourceId}?api-version=2021-01-01" --body "{'properties':{'keyVaultReferenceIdentity':'${userAssignedIdentityResourceId}'}}"
    ```

Cette configuration s’applique à toutes les références de l’application.

## <a name="reference-syntax"></a>Syntaxe de référence

Une référence Key Vault est de la forme `@Microsoft.KeyVault({referenceString})`, où `{referenceString}` est remplacé par une des options suivantes :

> [!div class="mx-tdBreakAll"]
> | Chaîne de référence                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_URI_secret_                                                       | **URI_secret** doit être l’URI complet du plan de données d’un secret dans Key Vault, y compris éventuellement une version, par exemple `https://myvault.vault.azure.net/secrets/mysecret/` ou `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`.  |
> | VaultName=_Nom_coffre_;SecretName=_Nom_secret_;SecretVersion=_Version_secret_ | **Nom_coffre** est obligatoire et doit correspondre au nom de la ressource Key Vault. **Nom_secret** est obligatoire et doit correspondre au nom du secret cible. **Version_secret** est facultatif, mais indique, s’il est mentionné, la version du secret à utiliser. |

Par exemple, une référence complète ressemble à ce qui suit :

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

Sinon :

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Rotation

Si aucune version n’est spécifiée dans la référence, l’application utilise la dernière version qui existe dans le coffre de clés. Lorsque des versions plus récentes sont disponibles, par exemple avec un événement de renouvellement, l’application est automatiquement mise à jour de façon à utiliser la dernière version dans un délai de 24 heures. Le délai est dû au fait que App Service met en cache les valeurs des références du coffre de clés et les récupère toutes les 24 heures. Toute modification de la configuration de l’application entraîne une récupération immédiate de tous les secrets référencés.

## <a name="source-application-settings-from-key-vault"></a>Paramètres d’application sources dans Key Vault

Les références Key Vault peuvent servir de valeurs pour les [Paramètres d’application](configure-common.md#configure-app-settings), ce qui vous permet de garder des secrets dans Key Vault au lieu de la configuration de site. Les paramètres d’application sont chiffrés au repos de manière sécurisée, mais si vous avez besoin de fonctionnalités de gestion des secrets, ils doivent être gardés dans Key Vault.

Pour utiliser une référence Key Vault pour un [paramètre d’application](configure-common.md#add-or-edit), définissez la référence comme valeur du paramètre. Votre application peut référencer le secret par le biais de sa clé comme d’habitude. Le code n’a pas besoin d’être modifié.

> [!TIP]
> La plupart des paramètres d’application qui utilisent des références Key Vault doivent être marqués comme des paramètres d’emplacement, car vous devez avoir des coffres distincts pour chaque environnement.

### <a name="considerations-for-azure-files-mounting"></a>Considérations relatives au montage Azure Files

Les applications peuvent utiliser le paramètre d’application `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` pour monter Azure Files en tant que système de fichiers. Ce paramètre a des contrôles de validation supplémentaires pour garantir que l’application peut être démarrée correctement. La plateforme s’appuie sur un partage de contenu dans Azure Files et suppose un nom par défaut, sauf si un nom est spécifié via le paramètre `WEBSITE_CONTENTSHARE`. Pour toutes les requêtes qui modifient ces paramètres, la plateforme tente de valider si ce partage de contenu existe, et il tente de le créer si ce n’est pas le cas. S’il ne peut pas localiser ou créer le partage de contenu, la requête est bloquée.

Lorsque vous utilisez des références Key Vault pour ce paramètre, ce contrôle de validation échoue par défaut, car la clé secrète elle-même ne peut pas être résolue lors du traitement de la requête entrante. Pour éviter ce problème, vous pouvez ignorer la validation en affectant « 1 » à `WEBSITE_SKIP_CONTENTSHARE_VALIDATION`. Cela contourne toutes les vérifications, et le partage de contenu n’est pas créé pour vous. Vous devez vous assurer qu’il est créé à l’avance. 

> [!CAUTION]
> Si vous ignorez la validation et que la chaîne de connexion ou le partage de contenu ne sont pas valides, l’application ne pourra pas démarrer correctement et renverra uniquement des erreurs HTTP 500.

Dans le cadre de la création du site, il est également possible que la tentative de montage du partage de contenu échoue en raison d’autorisations d’identité gérées qui ne sont pas propagées ou d’une intégration de réseau virtuel qui n’est pas configurée. Vous pouvez différer la configuration d’Azure Files jusqu’à une date ultérieure dans le modèle de déploiement pour traiter ce cas. Pour en savoir plus, consultez [Modes de déploiement d’Azure Resource Manager](#azure-resource-manager-deployment). App Service utilise un système de fichiers par défaut jusqu’à ce qu’Azure Files soit configuré. Les fichiers ne sont pas copiés, vous devez donc vous assurer qu’aucune tentative de déploiement ne se produit pendant la période intermédiaire avant le montage d’Azure Files.

### <a name="azure-resource-manager-deployment"></a>Déploiement Azure Resource Manager

Quand vous automatisez des déploiements de ressources par le biais de modèles Azure Resource Manager, vous pouvez avoir besoin de séquencer vos dépendances dans un ordre particulier pour que cette fonctionnalité fonctionne. Sinon, vous devez définir vos paramètres d’application comme leur propre ressource, au lieu d’utiliser une propriété `siteConfig` dans la définition de site. C’est parce que le site doit être défini en premier pour que le système puisse affecter l’identité et que cette identité puisse être utilisée dans la stratégie d’accès.

Voici à quoi peut ressembler un exemple de pseudo-modèle d’application de fonction :

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(resourceId('Microsoft.Web/sites/', variables('functionAppName')), '2020-12-01', 'Full').identity.tenantId]",
                        "objectId": "[reference(resourceId('Microsoft.Web/sites/', variables('functionAppName')), '2020-12-01', 'Full').identity.principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2019-09-01').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2019-09-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> Dans cet exemple, le déploiement du contrôle de code source varie selon les paramètres d’application. Il s’agit normalement d’un comportement non sécurisé, car la mise à jour du paramètre d’application se comporte de façon asynchrone. Toutefois, comme nous avons inclus le paramètre d'application `WEBSITE_ENABLE_SYNC_UPDATE_SITE`, la mise à jour est synchrone. Cela signifie que le déploiement de contrôle source commence uniquement une fois que les paramètres d’application ont été entièrement mis à jour. Pour plus d’informations sur les paramètres d’application, consultez [Variables d’environnement et paramètres d’application dans Azure App Service](reference-app-settings.md).

## <a name="troubleshooting-key-vault-references"></a>Résolution des problèmes liés aux références Key Vault

Si une référence ne se résout pas correctement, la valeur de référence est utilisée. Ainsi, pour les paramètres d’application, une variable d’environnement dont la valeur répond à la syntaxe `@Microsoft.KeyVault(...)` est créée. L'application peut alors générer des erreurs car elle s'attendait à un secret répondant à une certaine structure.

En règle générale, il s'agit d'une configuration incorrecte de la [stratégie d'accès à Key Vault](#granting-your-app-access-to-key-vault). Mais cela peut également être dû à un secret qui n'existe plus ou à une erreur de syntaxe dans la référence elle-même.

Si la syntaxe est correcte, vous pouvez afficher d’autres causes d’erreur en vérifiant l’état de résolution actuel dans le portail. Accédez à Paramètres d’application et sélectionnez « Modifier » pour la référence en question. Sous la configuration du paramètre, vous devez voir des informations d’état, y compris les erreurs éventuelles. L’absence de ces informations implique que la syntaxe de la référence n’est pas valide.

Vous pouvez également utiliser l’un des détecteurs intégrés pour obtenir des informations supplémentaires.

### <a name="using-the-detector-for-app-service"></a>Utilisation du détecteur pour App Service

1. Dans le portail, accédez à votre application.
2. Sélectionnez **Diagnostiquer et résoudre les problèmes**.
3. Sélectionnez **Disponibilité et performances**, puis **Application web inactive**.
4. Recherchez **Diagnostics des paramètres de l'application Key Vault** et cliquez sur **En savoir plus**.


### <a name="using-the-detector-for-azure-functions"></a>Utilisation du détecteur pour Azure Functions

1. Dans le portail, accédez à votre application.
2. Accédez à **Fonctionnalités de la plateforme**.
3. Sélectionnez **Diagnostiquer et résoudre les problèmes**.
4. Sélectionnez **Disponibilité et performances**, puis **Function App cesse de fonctionner ou signale des erreurs**.
5. Cliquez sur **Diagnostics des paramètres de l'application Key Vault**.
