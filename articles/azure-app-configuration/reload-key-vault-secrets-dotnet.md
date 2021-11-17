---
title: Recharger automatiquement les secrets et les certificats
titleSuffix: Azure App Configuration
description: Découvrez comment configurer votre application pour recharger automatiquement les secrets et les certificats à partir de Key Vault.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 05/25/2021
ms.author: avgupta
ms.openlocfilehash: 133c788b3bbe787f87cea3e5c72699c8dd0c4f6f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316182"
---
# <a name="reload-secrets-and-certificates-from-key-vault-automatically"></a>Recharger automatiquement les secrets et les certificats de Key Vault

App Configuration et Key Vault sont des services complémentaires utilisés côte à côte dans de nombreuses applications. App Configuration vous permet d’utiliser les services conjointement en créant des clés dans votre magasin App Configuration qui référencent les secrets et les certificats stockés dans Key Vault. Comme Key Vault stocke la paire de clés publique et privée d’un certificat en tant que secret, votre application peut récupérer n’importe quel certificat en tant que secret à partir de Key Vault.

Une bonne pratique de sécurité consiste à effectuer régulièrement une rotation des [secrets](../key-vault/secrets/tutorial-rotation.md) et des [certificats](../key-vault/certificates/tutorial-rotate-certificates.md). Une fois qu’ils ont fait l’objet d’une rotation dans Key Vault, vous souhaitez que votre application récupère les valeurs de secret et de certificat les plus récentes. Il existe deux façons de procéder sans redémarrer votre application :
- Mettez à jour une paire clé-valeur Sentinel pour déclencher l’actualisation de la totalité de votre configuration et recharger ainsi tous les secrets et certificats Key Vault. Pour plus d’informations, consultez [Utiliser la configuration dynamique dans une application ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md).
- Rechargez régulièrement tout ou partie des secrets et certificats à partir de Key Vault.

Dans la première option, vous devez mettre à jour la paire clé-valeur Sentinel dans App Configuration chaque fois que vous faites une rotation des secrets et des certificats dans Key Vault. Cette approche fonctionne bien lorsque vous souhaitez forcer un rechargement immédiat des secrets et des certificats dans votre application. Toutefois, lorsque les secrets et les certificats font l’objet d’une rotation automatique dans Key Vault, votre application peut rencontrer des erreurs si vous ne mettez pas à jour en temps voulu la valeur de clé Sentinel. La deuxième option vous permet d’automatiser complètement ce processus. Vous pouvez configurer votre application pour recharger les secrets et les certificats à partir de Key Vault dans votre délai acceptable à partir du moment de la rotation. Ce tutoriel vous guide tout au long de la deuxième option.

## <a name="prerequisites"></a>Configuration requise

- Ce tutoriel vous montre comment configurer votre application pour recharger automatiquement les secrets et les certificats à partir de Key Vault. Il s’appuie sur le tutoriel sur l’implémentation de références Key Vault dans votre code. Avant de continuer, terminez le [Tutoriel : Utiliser des références Key Vault dans une application ASP.NET Core](./use-key-vault-references-dotnet-core.md).

- Package [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) v4.4.0 ou version ultérieure.


## <a name="add-an-auto-rotating-certificate-to-key-vault"></a>Ajouter un certificat de rotation automatique à Key Vault

 Suivez le [Tutoriel : Configurer la rotation automatique des certificats dans Key Vault](../key-vault/certificates/tutorial-rotate-certificates.md) pour ajouter un certificat de rotation automatique appelé **ExampleCertificate** au coffre de clés créé dans le tutoriel précédent.


## <a name="add-a-reference-to-the-key-vault-certificate-in-app-configuration"></a>Ajouter une référence au certificat Key Vault dans App Configuration

1. Dans le portail Azure, sélectionnez **Toutes les ressources**, puis sélectionnez l’instance de magasin App Configuration que vous avez créée dans le tutoriel précédent.

1. Sélectionnez **Explorateur de configuration**.

1. Sélectionnez **+ Créer** > **Référence Key Vault**, puis choisissez les valeurs suivantes :
    - **Clé** : Sélectionnez **TestApp:Settings:KeyVaultCertificate**.
    - **Étiquette** : Laissez cette valeur vide.
    - **Abonnement**, **Groupe de ressources** et **Coffre de clés** : Entrez les valeurs correspondant aux valeurs du coffre de clés que vous avez créé dans le tutoriel précédent.
    - **Secret** : Sélectionnez le secret nommé **ExampleCertificate** que vous avez créé dans la section précédente.
    - **Version du secret** : **Version la plus récente**.

> [!Note]
> Si vous référencez une version spécifique, le rechargement de secret ou du certificat à partir de Key Vault retournera toujours la même valeur.


## <a name="update-code-to-reload-key-vault-secrets-and-certificates"></a>Mettre à jour le code pour recharger les secrets et certificats Key Vault

Dans votre fichier *Program.cs*, mettez à jour la méthode `AddAzureAppConfiguration` pour définir un intervalle d’actualisation pour votre certificat Key Vault à l’aide de la méthode `SetSecretRefreshInterval`. Avec cette modification, votre application va recharger la paire de clés publique/privée pour **ExampleCertificate** toutes les 12 heures.

```csharp
config.AddAzureAppConfiguration(options =>
{
    options.Connect(settings["ConnectionStrings:AppConfig"])
            .ConfigureKeyVault(kv =>
            {
                kv.SetCredential(new DefaultAzureCredential());
                kv.SetSecretRefreshInterval("TestApp:Settings:KeyVaultCertificate", TimeSpan.FromHours(12));
            });
});
```

Le premier argument de la méthode `SetSecretRefreshInterval` est la clé de la référence Key Vault dans App Configuration. Cet argument est facultatif. Si le paramètre de clé est omis, l’intervalle d’actualisation s’applique à tous les secrets et certificats qui n’ont pas d’intervalles d’actualisation individuels.

L’intervalle d’actualisation définit la fréquence à laquelle vos secrets et certificats seront rechargés à partir de Key Vault, indépendamment des modifications apportées à leurs valeurs dans Key Vault ou App Configuration. Si vous souhaitez recharger les secrets et les certificats lorsque leur valeur change dans App Configuration, vous pouvez les surveiller à l’aide de la méthode `ConfigureRefresh`. Pour plus d’informations, consultez [Utiliser la configuration dynamique dans une application ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md).

Choisissez l’intervalle d’actualisation en fonction de votre délai acceptable une fois que vos secrets et certificats ont été mis à jour dans Key Vault. Il est également important de prendre en compte les [limites du service Key Vault](../key-vault/general/service-limits.md) pour éviter d’être limité.


## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à configurer votre application pour recharger automatiquement les secrets et les certificats à partir de Key Vault. Pour apprendre à utiliser une identité managée afin de rationaliser l’accès à App Configuration et à Key Vault, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
