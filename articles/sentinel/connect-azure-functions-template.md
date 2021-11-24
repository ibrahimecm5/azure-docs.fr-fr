---
title: Utiliser Azure Functions pour connecter Microsoft Sentinel à votre source de données | Microsoft Docs
description: Découvrez comment configurer des connecteurs de données qui utilisent Azure Functions pour obtenir des données à partir de sources de données dans Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c175df296f00a42fafff53c0488705f877bff4f9
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521868"
---
# <a name="use-azure-functions-to-connect-microsoft-sentinel-to-your-data-source"></a>Utiliser Azure Functions pour connecter Microsoft Sentinel à votre source de données

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Vous pouvez utiliser [Azure Functions](../azure-functions/functions-overview.md), conjointement avec différents langages de codage tels que [PowerShell](../azure-functions/functions-reference-powershell.md) ou Python, pour créer un connecteur serverless pour les points de terminaison de l’API REST de vos sources de données compatibles. Des applications Azure Function vous permettent de connecter Microsoft Sentinel à l’API REST de votre source de données pour extraire des journaux.

Cet article explique comment configurer Microsoft Sentinel pour l’utilisation des applications Azure Function. Vous devrez peut-être également configurer votre système source et les liens d’informations spécifiques au fournisseur et au produit dans chaque page du connecteur de données dans le portail, ou la section pour votre service dans la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md).




> [!NOTE]
> - Une fois qu’ils sont ingérés dans Microsoft Sentinel, les données sont stockées à l’emplacement géographique de l’espace de travail dans lequel vous exécutez Microsoft Sentinel.
>
>     Pour une rétention à long terme, vous souhaiterez peut-être également stocker des données dans Azure Data Explorer. Pour plus d’informations, consultez [Intégrer Azure Data Explorer](store-logs-in-azure-data-explorer.md).
>
> - L’utilisation de Azure Functions pour ingérer de données dans Microsoft Sentinel peut entraîner des coûts d’ingestion de données supplémentaires. Pour plus d’informations, consultez la page [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="prerequisites"></a>Prérequis

Vérifiez que vous disposez des autorisations et des informations d’identification suivantes avant d’utiliser Azure Functions pour connecter Microsoft Sentinel à votre source de données et extraire ses journaux dans Microsoft Sentinel :

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Microsoft Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Vous devez avoir des autorisations d’accès en lecture et en écriture à Azure Functions pour créer une application de fonction. [En savoir plus sur Azure Functions](../azure-functions/index.yml).

- Vous aurez également besoin d’informations d’identification pour accéder à l’API du produit, qu’il s’agisse d’un nom d’utilisateur et d’un mot de passe, d’un jeton, d’une clé ou d’une autre combinaison. Vous pouvez également avoir besoin d’autres informations sur l’API, telles qu’un URI de point de terminaison.

    Pour plus d’informations, consultez la documentation du service auquel vous vous connectez et la section de votre service dans la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md).

## <a name="configure-and-connect-your-data-source"></a>Configurer et connecter votre source de données

> [!NOTE]
> - Vous pouvez stocker en toute sécurité des clés ou des jetons d’autorisation de l’espace de travail et de l’API dans Azure Key Vault. Azure Key Vault fournit un mécanisme sécurisé pour stocker et récupérer des valeurs de clés. [Suivez ces instructions](../app-service/app-service-key-vault-references.md) pour utiliser Azure Key Vault avec une application Azure Function.
>
> - Certains connecteurs de données dépendent d’un analyseur basé sur une [fonction Kusto](/azure/data-explorer/kusto/query/functions/user-defined-functions) pour pouvoir fonctionner normalement. Consultez la section pour votre service dans la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md) pour obtenir des liens vers des instructions permettant de créer la fonction et l’alias Kusto.


### <a name="step-1---get-your-source-systems-api-credentials"></a>ÉTAPE 1 : obtenir les informations d’identification de l’API du système source

Suivez les instructions de votre système source pour obtenir ses **informations d’identification d’API/jetons/clés d’autorisation**. Copiez-les et collez-les dans un fichier texte pour une utilisation ultérieure.

Vous trouverez plus d’informations sur les informations d’identification dont vous avez besoin, ainsi que des liens vers les instructions de votre produit pour les trouver ou les créer, sur la page connecteur de données du portail et dans la section de votre service dans la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md).

Vous devrez peut-être également configurer la journalisation ou d’autres paramètres sur votre système source. Vous trouverez les instructions pertinentes ainsi que celles du paragraphe précédent.
### <a name="step-2---deploy-the-connector-and-the-associated-azure-function-app"></a>ÉTAPE 2 : Déployer le connecteur et l’application Azure Function associée

#### <a name="choose-a-deployment-option"></a>Choisir une option de déploiement

# <a name="azure-resource-manager-arm-template"></a>[Modèle Azure Resource Manager (ARM)](#tab/ARM)

Cette méthode fournit un déploiement automatisé de votre connecteur Azure Function à l’aide d’un modèle ARM.

1. Dans le portail Microsoft Sentinel, sélectionnez **Connecteurs de données**. Sélectionnez votre connecteur Azure Functions dans la liste, puis **Ouvrez la page connecteur**.

1. Sous **Configuration**, copiez **l’ID d’espace de travail** et la **clé primaire** Microsoft Sentinel, puis mettez-les de côté.

1. Sélectionnez **Déployer sur Azure**. (Vous devrez peut-être faire défiler l’écran pour trouver le bouton.)

1. L’écran **Déploiement personnalisé** s’affiche.
    - Sélectionnez un **abonnement**, un **groupe de ressources** et une **région** dans lesquels déployer votre Function App.

    - Entrez les informations d’identification de l’API/clés d’autorisation/jetons que vous avez enregistrés à l' [étape 1](#step-1---get-your-source-systems-api-credentials) ci-dessus.

    - Entrez votre **ID d’espace de travail** et votre **Clé d’espace de travail** (clé primaire) Microsoft Sentinel que vous avez copiés et mis de côté.

        > [!NOTE]
        > Si vous utilisez des secrets Azure Key Vault pour l’une des valeurs ci-dessus, utilisez le schéma `@Microsoft.KeyVault(SecretUri={Security Identifier})` à la place des valeurs de chaîne. Pour plus d’informations, reportez-vous à la documentation relative aux références de Key Vault.

    - Complétez tous les autres champs du formulaire sur l’écran de **Déploiement personnalisé**. Consultez la page de votre connecteur de données dans le portail ou la section relative à votre service dans la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md).

    - Sélectionnez **Revoir + créer**. Quand la validation est terminée, sélectionnez **Créer**.

# <a name="manual-deployment-with-powershell"></a>[Déploiement manuel avec PowerShell](#tab/MPS)

Utilisez les instructions pas à pas suivantes pour déployer manuellement des connecteurs basés sur Azure Functions qui utilisent des fonctions PowerShell.

1. Dans le portail Microsoft Sentinel, sélectionnez **Connecteurs de données**. Sélectionnez votre connecteur Azure Functions dans la liste, puis **Ouvrez la page connecteur**.

1. Sous **Configuration**, copiez **l’ID d’espace de travail** et la **clé primaire** Microsoft Sentinel, puis mettez-les de côté.

1. **Créer une Function App**
    1. À partir du portail Azure, recherchez et sélectionnez **Function App**.

    1. Dans la page **Function App**, sélectionnez **Créer**. L’assistant **Créer une Function App** s’ouvre.

    1. Sous l’onglet **Informations de base** :
        - Sélectionner un **abonnement** et un **groupe de ressources**.
        - Choisissez le nom de votre application de fonction.
        - Définissez la **Pile Runtime** sur *PowerShell Core*.
        - Sélectionnez le numéro de version approprié.
        - Sélectionnez la **région** dans laquelle vous souhaitez déployer, puis sélectionnez **Suivant : hébergement**.

    1. Dans l’onglet **Hébergement** :
        - Choisissez le **Compte de stockage** que vous souhaitez utiliser ou créez-en un nouveau.
        - Sélectionnez *Consommation (serverless)* pour votre **Type de plan**.

    1. Apportez les autres modifications de configuration dont vous avez besoin, puis sélectionnez **Examiner + créer**.

    1. Quand le message « Validation réussie » s’affiche, sélectionnez **Créer**.

    1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

1. **Importer le code Function App**
    1. Dans le Function App récemment créé, sélectionnez **Fonctions** dans le menu de navigation.

    1. Dans la page **Fonctions**, sélectionnez **+ Ajouter**.
    
    1. Dans le panneau **Ajouter une fonction**, sélectionnez le déclencheur du **Minuteur**.

    1. Entrez un nom unique pour votre fonction et entrez une expression *cron* pour spécifier la planification. L’intervalle par défaut est de 5 minutes.

    1. Lorsque la fonction a été créée, sélectionnez **Code + Test** dans le volet gauche.

    1. Téléchargez le code Function App fourni par le fournisseur de votre système source, copiez-le et collez-le dans l’éditeur de *run.ps1***Function App**, en remplaçant ce qui est présent par défaut. Vous pouvez trouver le lien de téléchargement sur la page du connecteur ou dans la section relative à votre service dans la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md).

    1. Sélectionnez **Enregistrer**.

1. **Configurer Function App**
    1. Dans la page de votre Function App, sélectionnez **Configuration** sous **Paramètres** dans le menu de navigation.

    1. Sous l’onglet **Paramètres d’application**, sélectionnez **+ Nouveau paramètre d’application**.

    1. Ajoutez les paramètres d’application prescrits pour votre produit individuellement, avec leurs valeurs de chaîne respectant la casse respectives. Consultez la page de votre connecteur de données ou la section de votre produit de la section relative à votre service dans la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md).

        > [!TIP]
        > Le cas échéant, utilisez le paramètre d’application *logAnalyticsUri* pour remplacer le point de terminaison de l’API d’analytique des journaux d'activité si vous utilisez un Cloud dédié. Par exemple, si vous utilisez le cloud public, laissez la valeur vide. pour l’environnement cloud Azure GovUS, spécifiez la valeur au format suivant : `https://<CustomerId>.ods.opinsights.azure.us`.
        >

# <a name="manual-deployment-with-python"></a>[Déploiement manuel avec Python](#tab/MPY)

Utilisez les instructions pas à pas suivantes pour déployer manuellement des connecteurs basés sur Azure Functions qui utilisent des fonctions Python. Ce type de déploiement requiert Visual Studio Code.

1. Dans le portail Microsoft Sentinel, sélectionnez **Connecteurs de données**. Sélectionnez votre connecteur Azure Functions dans la liste, puis **Ouvrez la page connecteur**.

1. Sous **Configuration**, copiez **l’ID d’espace de travail** et la **clé primaire** Microsoft Sentinel, puis mettez-les de côté.

1. **Déployer une Function App**

    > [!NOTE]
    > Vous devrez [préparer Visual Studio Code](../azure-functions/create-first-function-vs-code-python.md) (VS Code) pour le développement d’Azure Function.

    1. Téléchargez le fichier Azure Function App à l’aide du lien fourni sur la page connecteur de données et dans la section de votre service dans la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md). Extrayez l’archive sur votre ordinateur de développement local.

    1. Démarrez VS Code. Dans la barre de menus, sélectionnez **Fichier > Ouvrir le dossier...** .

    1. Sélectionnez le dossier de niveau supérieur dans les fichiers extraits de l’archive.

    1. Choisissez l’icône Azure dans la barre d’activité VS Code (à gauche). Puis dans la zone **Azure : Fonctions**, choisissez le bouton **Déployer sur une application de fonction**. 

        > [!NOTE]
        > Si vous n’êtes pas déjà connecté, choisissez l’icône Azure dans la barre d’activité. Puis, dans la zone **Azure : Functions**, choisissez **Se connecter à Azure**.  
        > Si vous êtes déjà connecté, passez à l’étape suivante.

    1. Quand vous y êtes invité, indiquez les informations suivantes :
        - **Sélectionnez le dossier** : choisissez un dossier dans votre espace de travail ou accédez à un dossier qui contient votre application de fonction.
        - **Sélectionnez l’abonnement** : choisissez l’abonnement à utiliser.
        - Sélectionnez **Créer une Function App dans Azure**. (Ne choisissez pas l’option **Avancé**.)
        - **Entrez un nom global unique pour l’application de fonction** : donnez à votre application de fonction un nom qui serait valide dans un chemin d’URL. Le nom que vous choisissez sera validé pour s’assurer qu’il est unique tout au long d’Azure Functions.
        - **Sélectionnez un runtime** : choisissez *Python 3.8*.

    1. Le déploiement commence. Une notification s’affiche après que votre application de fonction a été créée et que le package de déploiement a été appliqué.

    1. Revenez à la page de votre Function App pour la configuration.

1. **Configurer Function App**
    1. Dans la page de votre Function App, sélectionnez **Configuration** sous **Paramètres** dans le menu de navigation.

    1. Sous l’onglet **Paramètres d’application**, sélectionnez **+ Nouveau paramètre d’application**.

    1. Ajoutez les paramètres d’application prescrits pour votre produit individuellement, avec leurs valeurs de chaîne respectant la casse respectives. Consultez la page de votre connecteur de données ou la section de votre service de la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md) pour les paramètres d'application à ajouter.

        - Le cas échéant, utilisez le paramètre d’application *logAnalyticsUri* pour remplacer le point de terminaison de l’API d’analytique des journaux d'activité si vous utilisez un Cloud dédié. Par exemple, si vous utilisez le cloud public, laissez la valeur vide. pour l’environnement cloud Azure GovUS, spécifiez la valeur au format suivant : `https://<CustomerId>.ods.opinsights.azure.us`.

---

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données apparaissent dans les **Journaux** sous *CustomLogs*, dans les tables figurant dans la section de votre service dans la page de [référence des connecteurs de données Microsoft Sentinel](data-connectors-reference.md).

Pour interroger des données, entrez l’un de ces noms de table (ou l’alias de fonction Kusto approprié) dans la fenêtre de requête.

Consultez l’onglet **Étapes suivantes** de la page du connecteur pour obtenir des exemples de requêtes utiles.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Microsoft Sentinel à votre source de données à l’aide de connecteurs Azure Functions. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](./get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](./detect-threats-built-in.md).
- [Utilisez des classeurs](./monitor-your-data.md) pour superviser vos données.
