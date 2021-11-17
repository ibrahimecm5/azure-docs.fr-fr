---
title: Déployer et surveiller des honeytokens Azure Key Vault avec Azure Sentinel | Microsoft Docs
description: Introduisez des clés et secrets honeytoken Azure Key Vault et surveillez-les avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2021
ms.author: bagol
ms.openlocfilehash: 160abcf739cbece61d34cd3db4118fd9f2f0ed9f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479449"
---
# <a name="deploy-and-monitor-azure-key-vault-honeytokens-with-azure-sentinel-public-preview"></a>Déployer et surveiller des honeytokens Azure Key Vault avec Azure Sentinel (préversion publique)

> [!IMPORTANT]
> La solution Azure Sentinel Deception (Honey Tokens) est actuellement en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

Cet article explique comment utiliser la **solution Azure Sentinel Deception (Honey Tokens)** pour introduire des clés et secrets leurres [Azure Key Vault](/azure/key-vault/), appelés *honeytokens*, dans des charges de travail existantes.

Utilisez les [règles d’analyse](detect-threats-built-in.md), [watchlists](watchlists.md) et [classeurs](monitor-your-data.md) fournis par la solution pour surveiller l’accès aux honeytokens déployés.

Lorsque vous utilisez des honeytokens dans votre système, les principes de détection restent les mêmes. Étant donné qu’il n’existe aucune raison légitime d’accéder à un honeytoken, toute activité indique la présence d’un utilisateur qui n’est pas familiarisé avec l’environnement et pourrait être un attaquant.

## <a name="before-you-begin"></a>Avant de commencer

Pour commencer à utiliser la solution **Azure Sentinel Deception (Honey Tokens)** , assurez-vous de disposer des éléments suivants :

- **Rôles requis** : pour installer la solution **Azure Sentinel Deception (Honey Tokens)** , vous devez être un administrateur de locataire. Une fois la solution installée, vous pouvez partager le classeur avec les propriétaires du coffre de clés afin qu’ils puissent déployer leurs propres honeytokens.

- **Connecteurs de données requis**: assurez-vous que vous avez déployé l’[Azure Key Vault](data-connectors-reference.md#azure-key-vault) et les connecteurs de données d’[activité Azure](data-connectors-reference.md#azure-activity) dans votre espace de travail, et qu’ils sont connectés.

    Vérifiez que le routage des données a réussi et que les données **KeyVault** et **AzureActivity** circulent dans Azure Sentinel. Pour plus d'informations, consultez les pages suivantes :

    - [Connecter Azure Sentinel aux services Azure, Windows, Microsoft et Amazon](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)
    - [Rechercher votre connecteur de données Azure Sentinel](data-connectors-reference.md)

## <a name="install-the-solution"></a>Installer la solution

Installez la solution **Azure Sentinel Deception (Honey Tokens)** comme vous installeriez d’[autres solutions](monitor-key-vault-honeytokens.md). Sur la page solution de **Azure Sentinel Deception**, sélectionnez **Démarrer** pour commencer.

:::image type="content" source="media/monitor-key-vault-honeytokens/honeytoken-create-solution.png" alt-text="Capture d’écran de la page de création de solution.":::

**Pour installer la solution Deception** :

Les étapes suivantes décrivent les actions spécifiques requises pour la solution **Azure Sentinel Deception (Honey Tokens)** .

1. Sous l’onglet **De base**, sélectionnez le groupe de ressources dans lequel se trouve votre espace de travail Azure Sentinel.

1. Sous l’onglet **Composants requis**, dans le champ **Nom d’application de fonction**, entrez un nom explicite pour l’application de fonction Azure qui créera des honeytokens dans vos coffres de clés.

    Le nom de l’application de fonction doit être unique et d’une longueur de 2 à 22 caractères alphanumériques.

    Une commande est affichée ci-dessous avec le nom que vous avez défini. Par exemple :

    :::image type="content" source="media/monitor-key-vault-honeytokens/prerequisites.png" alt-text="Capture d’écran de l’onglet Composants requis montrant la commande curl mise à jour.":::

1. Sélectionnez **Cliquez ici pour ouvrir un interpréteur de commandes cloud** afin d’ouvrir un onglet Cloud Shell. Connectez-vous si vous y êtes invité, puis exécutez la commande affichée. 

    Le script que vous exécutez crée une application de fonction Azure AD (AAD) qui déploie vos honeytokens.    Par exemple :

    ```bash
    Requesting a Cloud Shell.Succeeded
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    maria@Azure:~$curl -sL https://aka.ms/sentinelhoneytokensappcreate | bash -s HoneyTokenFunctionApp
    ```

     La sortie du script inclut l’ID et le secret d’application AAD. Par exemple :

    ```bash
    WARNING: The output includes credentials that you must protect. Be sure that you do not include these credentials in your code or check the credentials into your source control. For more information, see https://aka.ms/azadsp-cli
    function app name: HoneyTokenFunctionApp
    AAD App Id: k4js48k3-97gg-3958=sl8d=48620nne59k4
    AAD App secret: v9kUtSoy3u~K8DKa8DlILsCM_K-s9FR3Kj
    maria@Azure:~$
    ```

1. De retour dans Azure Sentinel, en bas de l’onglet **Composants requis**, entrez l’ID et le secret d’application AAD dans les champs appropriés. Par exemple :

    :::image type="content" source="media/monitor-key-vault-honeytokens/client-app-secret-values.png" alt-text="Capture d’écran montrant l’application cliente de l’application de fonction et les valeurs de secret ajoutées.":::

1. Sélectionnez **Cliquez ici pour continuer dans les paramètres de votre application de fonction** à l’étape 4. Un nouvel onglet de navigateur s’ouvre dans les paramètres de l’application Azure AD.

    Connectez-vous si vous y êtes invité, puis sélectionnez **Accorder le consentement administrateur pour `<your directory name>`** afin de continuer. Par exemple :

    :::image type="content" source="media/monitor-key-vault-honeytokens/grant-admin-access.png" alt-text="Capture d’écran montrant le bouton Accorder le consentement administrateur pour votre annuaire.":::

    Pour plus d’informations, consultez [Accorder le consentement administrateur dans les inscriptions d’applications](/azure/active-directory/manage-apps/grant-admin-consent).

1. De retour dans Azure Sentinel, sous les onglets **Classeurs**, **Analytique**, **Watchlists** et **Règles**, notez le contenu de sécurité qui sera créé et modifiez les noms si nécessaire.

    > [!NOTE]
    > D’autres instructions dans cet article font référence aux classeurs **HoneyTokensIncidents** et **SOCHTManagement**. Si vous modifiez les noms de ces classeurs, veillez à noter les nouveaux noms pour votre propre référence, et utilisez-les au besoin à la place des noms par défaut.
    >

1. Sous l’onglet **Azure Functions** , définissez les valeurs suivantes :

    **Configuration du coffre de clés** : les champs suivants définissent des valeurs pour le coffre de clés dans lequel vous allez stocker le secret de votre application AAD. Ces champs ne définissent *pas* le coffre de clés dans lequel vous allez déployer les honeytokens.

    |Champ  |Description  |
    |---------|---------|
    | **Plan de services**     |   Opérez une sélection pour indiquer si vous souhaitez utiliser un plan **Premium** ou **Consommation** pour votre application de fonction. Pour plus d’informations, consultez [Hébergement du plan Consommation d’Azure Functions](/azure/azure-functions/consumption-plan) et [Plan Premium Azure Functions](/azure/azure-functions/functions-premium-plan).      |
    | **Si un nouveau coffre de clés doit être créé**     |    Sélectionnez **Nouveau** afin de créer un coffre de clés pour le secret de votre application, ou **Existant** afin d’utiliser un coffre de clés existant.   |
    | **Nom du coffre de clés**     | S’affiche uniquement si vous avez choisi de créer un coffre de clés. <br><br>Entrez le nom du coffre de clés que vous souhaitez utiliser pour stocker le secret de votre application. Ce nom doit être globalement unique.     |
    | **Groupe de ressources KeyVault**     |S’affiche uniquement si vous avez choisi de créer un coffre de clés. <br><br> Sélectionnez le nom du groupe de ressources dans lequel vous souhaitez stocker le coffre de clés pour votre clé d’application.      |
    | **Coffres de clés existants** | S’affiche uniquement si vous avez choisi d’utiliser un coffre de clés existant. Sélectionnez le coffre de clés que vous souhaitez utiliser. |
    | **Nom de secret KeyVault**     |   Entrez le nom du secret utilisé pour stocker la clé secrète client.      |

    **Configuration de honeytoken** : les champs suivants définissent les paramètres utilisés pour les clés et les secrets utilisés dans votre honeytokens. Utilisez des conventions d’affectation de noms qui seront fusionnées avec les exigences d’affectation de noms de votre organisation afin que des attaquants ne puissent pas les distinguer.

    |Champ  |Description  |
    |---------|---------|
    |**Mots clés Key**     |  Entrez les listes de valeurs séparées par des virgules que vous souhaitez utiliser avec vos noms de honeytokens leurres.  Par exemple : `key,prod,dev`.  Les valeurs doivent être exclusivement alphanumériques.   |
    |**Secrets**     |   Entrez les listes de valeurs séparées par des virgules que vous souhaitez utiliser avec vos secrets honeytokens leurres.  Par exemple : `secret,secretProd,secretDev`. Les valeurs doivent être exclusivement alphanumériques.    |
    |**Probabilité de honeytoken supplémentaire**     |  Entrez une valeur comprise entre `0` et `1`, par exemple `0.6`. Cette valeur définit la probabilité que plus d’un honeytoken soient ajoutés au coffre de clés.       |
    |     |         |

1. Sélectionnez  **Suivant : Vérifier + créer** pour terminer l’installation de votre solution.

    Une fois la solution installée, les éléments suivants s’affichent :

    - Un lien vers votre classeur **SOCHTManagement**. Vous avez peut-être modifié ce nom sous l’onglet **Classeurs** précédemment dans cette procédure.

    - URL d’un modèle ARM personnalisé. Vous pouvez utiliser ce modèle ARM pour déployer une initiative Azure Policy connectée à une recommandation personnalisée d’Azure Security Center, qui distribue le classeur **SOCHTManagement** aux propriétaires de Keyvault dans votre organisation.

1. L’onglet **Étapes de post-déploiement** indique que vous pouvez utiliser les informations affichées dans la sortie du déploiement pour distribuer la recommandation personnalisée d’Azure Security Center à tous les propriétaires de coffres de clés dans votre organisation, en vous recommandant de déployer des honeytokens dans leurs coffres de clés.

    Utilisez l’[URL de modèle ARM](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2faka.ms%2fsentinelhoneytokenspolicy) personnalisée indiquée dans la sortie d’installation pour ouvrir la page **Déploiement personnalisé** du modèle lié.

    Pour plus d’informations, consultez [Distribuer le classeur SOCHTManagement](#distribute-the-sochtmanagement-workbook).

## <a name="deploy-your-honeytokens"></a>Déployer vos honeytokens

Après avoir installé la solution **Azure Sentinel Deception (Honey Tokens)** , vous êtes prêt à commencer le déploiement des honeytokens dans vos coffres de clés en procédant de la manière décrite dans le classeur **SOCHTManagement**.

Nous vous recommandons de partager le classeur **SOCHTManagement** avec les propriétaires de coffres de clés dans votre organisation afin qu’ils puissent créer leurs propres honeytokens dans leurs coffres de clés. Vous avez peut-être renommé ce classeur lors de l’[installation de la solution](#install-the-solution). Lors du partage, veillez à accorder uniquement des autorisations de lecture.

**Déployez des honeytokens dans vos coffres de clés** :

1. Dans Azure Sentinel, accédez à **Classeurs > Mes classeurs**, puis ouvrez le classeur **SOCHTManagement**. Vous avez peut-être modifié son nom lors du déploiement de la solution.

1. Sélectionnez **Afficher le classeur enregistré** > **Ajouter comme approuvé**. Par exemple :

    :::image type="content" source="media/monitor-key-vault-honeytokens/add-as-trusted.png" alt-text="Capture d’écran du bouton « Ajouter comme approuvé » du classeur SOCHTManagement.":::

    L’infrastructure est déployée dans vos coffres de clés pour permettre le déploiement des honeytokens.

1. Sous l’onglet **Coffre de clés** du classeur, développez votre abonnement pour afficher les coffres de clés prêts pour le déploiement des honeytokens, et tous les coffres de clés dans lesquels des honeytokens sont déjà déployés.

    Dans la colonne **Est surveillé par SOC**, une coche verte :::image type="icon" source="media/monitor-key-vault-honeytokens/checkmark.png" border="false"::: indique que le coffre de clés contient déjà des honeytokens. Un x rouge :::image type="icon" source="media/monitor-key-vault-honeytokens/xmark.png" border="false"::: indique que le coffre de clés ne contient pas encore de honeytoken. Par exemple :

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-deployed.png" alt-text="Capture d’écran des classeurs SOCHTManagement montrant des honeytokens déployés.":::


1. Faites défiler la page du classeur, puis suivez les instructions et les liens figurant dans la section **Prendre une mesure** pour déployer des honeytokens dans tous les coffres de clés à grande échelle, ou déployez-les manuellement un à un.

    # <a name="deploy-at-scale"></a>[Déployer à l’échelle](#tab/deploy-at-scale)

    **Pour déployer des honeytokens à grande échelle** :

    1. Sélectionnez le lien **Activer un utilisateur** afin de déployer un modèle ARM qui déploie une stratégie d’accès au coffre de clés, en accordant à l’ID d’utilisateur spécifié les droits nécessaires pour créer les honeytokens.

        Connectez-vous si vous y êtes invité, puis entrez des valeurs dans les zones **Détails du projet** et **Détails de l’instance** pour le déploiement de votre modèle ARM. Sur la page d’accueil d’Azure Active Directory, recherchez vos **ID de locataire** et **ID de l’objet utilisateur** pour vos utilisateurs.

        Lorsque vous avez terminé, sélectionnez **Vérifier + créer** pour déployer le modèle ARM. Par exemple :

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-arm-template.png" alt-text="Capture d’écran de la page Déploiement personnalisé.":::

        Vos paramètres sont validés, puis une confirmation s’affiche : **Validation réussie**.

        En bas de la page, sélectionnez **Créer** pour déployer votre modèle ARM, puis attendez de voir s’afficher la page de confirmation de la réussite du déploiement.

    1. De retour dans Azure Sentinel, dans votre classeur **SOCHTManagement** > **Prendre une mesure** > **Déployer à grande échelle**, sélectionnez le lien **Cliquez pour déployer** afin d’ajouter des honeytokens à tous les coffres de clés auxquels vous avez accès dans l’abonnement sélectionné.

        Une fois l’opération terminée, les résultats de votre déploiement de honeytokens s’affichent dans une table sous un nouvel onglet.

    1. Veillez à sélectionner le lien **Désactiver votre utilisateur** pour supprimer la stratégie d’accès créée précédemment. Connectez-vous à nouveau si vous y êtes invité, entrez des valeurs pour votre déploiement ARM personnalisé, puis déployez le modèle ARM. Cette étape déploie une stratégie d’accès au coffre de clés qui supprime les droits d’utilisateur pour créer des clés et des secrets.

    # <a name="deploy-a-single-honeytoken"></a>[Déployer un honeytoken unique](#tab/deploy-a-single-honeytoken)

    **Pour déployer un honeytoken manuellement** :

    1. Dans le tableau en haut de la page, sélectionnez le coffre de clés dans lequel vous souhaitez déployer votre honeytoken. La section **Déployer sur un coffre de clés spécifique** s’affiche au bas de la page.

    1. Faites défiler vers le bas, puis, dans la liste déroulante **Type de honeytoken**, indiquez si vous souhaitez créer une clé ou un secret. Dans le champ **Nom du nouvel honeytoken**, entrez un nom explicite. Par exemple :

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-manually.png" alt-text="Capture d’écran montrant un déploiement dans une zone spécifique du coffre de clés.":::

    1. Dans le tableau **Opérations**, développez la section **Déployer un honeytoken**, puis sélectionnez chaque nom de tâche pour suivre les étapes requises. Si vous y êtes invité, connectez-vous.

        - Sélectionnez **Cliquez pour valider l’audit du coffre de clés**. Dans Azure Key Vault, vérifiez que les paramètres de diagnostic de votre coffre de clés sont définis pour envoyer des événements d’audit à Log Analytics.
        - Sélectionnez **Activez votre utilisateur dans la stratégie du coffre de clés s’il est manquant**. Dans Azure Key Vault, assurez-vous que votre utilisateur dispose de l’accès nécessaire pour déployer des honeytokens dans les emplacements requis. Sélectionnez **Save** (Enregistrer) pour enregistrer les modifications.
        - Sélectionnez **Cliquez pour ajouter un honeytoken au coffre de clés** afin d’ouvrir Azure Key Vault. Ajoutez un nouvel honeytoken, comme un nouveau secret, au coffre de clés configuré.
        - Sélectionnez **Cliquez pour ajouter la surveillance dans le SOC**. Si l’opération réussit, un message de confirmation s’affiche dans un nouvel onglet : `Honey-token was successfully added to monitored list`.

        Pour plus d’informations, consultez la [documentation sur Azure Key Vault](/azure/key-vault/secrets/about-secrets).

    > [!NOTE]
    > Veillez à sélectionner le lien **Désactivez l’utilisateur dans la stratégie du coffre de clés si nécessaire** afin de supprimer la stratégie d’accès permettant de créer des honeytokens.
    >

    # <a name="remove-a-honeytoken"></a>[Supprimer un honeytoken](#tab/remove-a-honeytoken)

    **Pour supprimer un honeytoken spécifique** :

    1. Dans le tableau en haut de la page, sélectionnez le coffre de clés dans lequel vous souhaitez supprimer un honeytoken. La section **Déployer sur un coffre de clés spécifique** s’affiche au bas de la page.

    1. Dans le tableau **Opérations**, développez la section **Supprimer un honeytoken**, puis sélectionnez chaque nom de tâche pour suivre les étapes requises. Si vous y êtes invité, connectez-vous.

        - Sélectionnez **Cliquez pour supprimer l’honeytoken du coffre de clés** afin d’ouvrir Azure Key Vault à la page où vous pouvez supprimer votre honeytoken. 
        - Sélectionnez **Envoyer un e-mail pour mettre à jour le SOC**. Un e-mail est ouvert dans votre client de courrier par défaut pour le SOC (centre des opérations de sécurité), qui vous recommande de supprimer la surveillance d’honeytoken pour le coffre de clés sélectionné.

    > [!TIP]
    > Nous vous recommandons de communiquer clairement avec votre SOC sur les honeytokens que vous supprimez.
    >

    ---

Il se peut que vous deviez patienter quelques minutes pendant le remplissage des données et la mise à jour des autorisations. Actualisez la page pour afficher les mises à jour dans le déploiement de votre coffre de clés.

## <a name="test-the-solution-functionality"></a>Tester la fonctionnalité de la solution

**Pour tester que vous êtes alerté de tout accès à vos honeytokens** :

1. Dans la page **Watchlists** d’Azure Sentinel, sélectionnez l’onglet **Mes watchlists**, puis la watchlist **HoneyTokens**.

    Sélectionnez **Afficher dans log Analytics** pour afficher la liste des valeurs de honeytoken actuelles trouvées. Dans la page **Journaux**, les éléments de votre watchlist sont automatiquement extraits pour votre requête. Par exemple :

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png" alt-text="Capture d’écran des valeurs de la watchlist de honeytokens dans Log Analytics." lightbox="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png":::


    Pour plus d’informations, consultez [Utilisation des watchlists Azure Sentinel](watchlists.md).

1. Dans la liste dans Log Analytics, choisissez une valeur de honeytoken à tester.

    Ensuite, accédez à Azure Key Vault et téléchargez la clé publique ou affichez le secret pour votre honeytoken choisi.

    Par exemple, sélectionnez votre honeytoken, puis choisissez **Télécharger la clé publique**. Cette action crée un journal `KeyGet` ou `SecretGet` qui déclenche une alerte dans Azure Sentinel.

    Pour plus d’informations, consultez la [documentation relative à Key Vault](/azure/key-vault/).

1. De retour dans Azure Sentinel, accédez à la page **Incidents**. Il se peut que vous deviez patienter cinq minutes, mais vous devriez voir s’afficher un nouvel incident nommé, par exemple **HoneyTokens : accès à une clé HoneyToken de KeyVault**.

    Sélectionnez l’incident pour afficher ses détails, tels que l’opération de clé effectuée, l’utilisateur qui a accédé à la clé honeytoken et le nom du coffre de clés compromis.

    > [!TIP]
    > Tout accès ou opération avec les clés et les secrets honeytoken génère des incidents sur lesquels vous pouvez enquêter dans Azure Sentinel. Étant donné qu’il n’y a aucune raison d’utiliser réellement les clés et les secrets honeytoken, toute activité similaire dans votre espace de travail peut être malveillante et doit être examinée.
    >

1. Affichez l’activité de honeytoken dans le classeur **HoneyTokensIncident**. Dans la page **Classeurs** d’Azure Sentinel, recherchez et ouvrez le classeur **HoneyTokensIncident**.

    Ce classeur affiche tous les incidents liés à des honeytokens, les entités associées, les coffres de clés compromis, les opérations de clé effectuées et les honeytokens ayant fait l’objet d’un accès.

    Sélectionnez des incidents et opérations spécifiques pour enquêter de plus près sur les activités associées.

## <a name="distribute-the-sochtmanagement-workbook"></a>Distribuer le classeur SOCHTManagement

Nous vous recommandons de déployer des honeytokens dans le plus grand nombre possible de coffres de clés pour être certain de disposer de capacités de détection optimales au sein de votre organisation.

Toutefois, de nombreuses équipes de SOC n’ont pas accès aux coffres de clés. Pour combler cette lacune, distribuez le classeur **SOCHTManagement** à tous les propriétaires de coffres de clés de votre locataire, afin que vos équipes de SOC puissent déployer leurs propres honeytokens. Vous avez peut-être modifié le nom de ce classeur lors de l’[installation de la solution](#install-the-solution).

Vous pouvez toujours partager le lien direct du classeur. Cette procédure décrit également comment utiliser un modèle ARM pour déployer une initiative Azure Policy connectée à une recommandation personnalisée d’Azure Security Center, qui distribue le classeur **SOCHTManagement** aux propriétaires de coffres de clés dans votre organisation.

> [!NOTE]
> Chaque fois que vous distribuez le classeur, veillez à accorder uniquement l’accès en lecture.
>

**Pour distribuer le classeur SOCHTManagement via l’initiative Azure Policy**

1. Dans le tableau suivant, sélectionnez l’un des boutons **Déployer sur Azure** pour ouvrir le modèle ARM dans la page **Déploiement personnalisé**, en fonction de la façon dont vous souhaitez déployer le modèle ARM.  Utilisez les liens GitHUb pour afficher les détails de ce qui est inclus dans le modèle ARM, ou personnaliser le modèle ARM pour votre environnement.

    Les boutons **Déployer sur Azure** utilisent les mêmes URL que celles affichées sous l’onglet **Sortie** après l’[installation de la solution](#install-the-solution).

    | Option de déploiement | Description | Déployer dans Azure | Lien GitHub |
    |-------------------|-------------|-------------|-----------------|
    | Groupe d’administration | Recommandé pour un opérer un déploiement dans l’ensemble de l’entreprise| [![DTA-Button-MG]][DTA-MG]  |[Exemple dans GitHub][GitHub-MG] |
    | Abonnement | Recommandé pour tester un seul abonnement | [![DTA-Button-Sub]][DTA-Sub]  | [Exemple dans GitHub][GitHub-Sub] |

    Connectez-vous lorsque vous y êtes invité.

1. Sous l’onglet **Déploiement de stratégie de solution Deception** > **De base**, sélectionnez la valeur et la région de votre groupe d’administration. Ensuite, sélectionnez **Suivant : Cible de déploiement >** pour continuer.

1. Sous l’onglet **cible de déploiement**, sélectionnez à nouveau votre groupe d’administration, puis **Suivant : Classeur Administration >** .

1. Sous l’onglet **Classeur Administration**, collez le lien vers votre classeur **SOCHTManagement**.

    Le lien du classeur figure dans le classeur **SOCHTManagement** dans Azure Sentinel, ainsi que sous l’onglet **Sortie** du déploiement de la solution.

    Par exemple, pour trouver le lien dans le classeur, sélectionnez **Classeurs** > **Mes classeurs** > **SOCHTManagement**, puis **Copier le lien** dans la barre d’outils.

1. Après avoir entré le lien de votre classeur, sélectionnez **Suivant : Vérifier + créer >** pour continuer. Attendez un message de confirmation indiquant que la validation a réussi, puis sélectionnez **Créer**.

1. Une fois le déploiement terminé, vous verrez que le déploiement inclut une nouvelle initiative **HoneyTokens** et deux nouvelles stratégies nommées **KeyVault HoneyTokens** et **KVReviewTag**. Par exemple :

    :::image type="content" source="media/monitor-key-vault-honeytokens/policy-deployment.png" alt-text="Capture d’écran d’une stratégie de modèle ARM correctement déployée." lightbox="media/monitor-key-vault-honeytokens/policy-deployment.png":::

1. Dans **Azure Policy**, affectez la nouvelle stratégie **KVReviewTag** avec l’étendue dont vous avez besoin. Cette affectation ajoute la balise **KVReview** et la valeur de **ReviewNeeded** à tous les coffres de clés dans l’étendue sélectionnée.

    1. Dans Azure Policy, sous **Création** à gauche, sélectionnez **Définitions**. Recherchez la ligne de votre stratégie **KVReviewTag**, puis sélectionnez le menu options à droite.

    1. Dans la page **Déployer les paramètres de diagnostic pour le journal d’activité dans l’espace de travail Log Analytics**, entrez les valeurs requises afin de déployer les paramètres de diagnostic pour votre environnement.

        Sous l’onglet **Correction**, veillez à sélectionner l’option **Créer une tâche de correction** afin d’appliquer l’étiquette aux coffres de clés existants.

    Pour plus d'informations, consultez la [documentation relative à Azure Policy](/azure/governance/policy/assign-policy-portal).

1. Dans **Azure Security Center**, ajoutez une recommandation d’audit à tous les coffres de clés figurant dans l’étendue sélectionnée :

    1. Choisissez **Conformité réglementaire > Gérer les stratégies de conformité**, puis sélectionnez votre étendue.

    1. Dans la page de détails de l’étendue sélectionnée, faites défiler la liste vers le bas, puis, dans la section **Vos initiatives personnalisées**, sélectionnez **Ajouter une initiative personnalisée**.

    1. Dans la ligne d’initiative **HoneyTokens**, sélectionnez **Ajouter**.

Une recommandation d’audit avec un lien vers le classeur **SOCHTManagement** est ajoutée à tous les coffres de clés figurant dans l’étendue sélectionnée. Vous avez peut-être modifié le nom de ce classeur lors de l’[installation de la solution](#install-the-solution).

Pour plus d’informations, consultez la [documentation Azure Security Center](/azure/security-center/security-center-recommendations).


## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [À propos des solutions Azure Sentinel](sentinel-solutions.md)
- [Découvrir et déployer des solutions Azure Sentinel](monitor-key-vault-honeytokens.md)
- [Catalogue de solutions Azure Sentinel](sentinel-solutions-catalog.md)
- [Détection des menaces prête à l’emploi](detect-threats-built-in.md)
- [Classeurs Azure Sentinel courants](top-workbooks.md)


<!-- The following section is used to store references to external images and links to reduce maintenance overhead and enable tooltips -->

[//]: # (*******************************)
[//]: # (RÉFÉRENCES D’IMAGES EXTERNES CI-DESSOUS)
[//]: # (*******************************)

[DTA-Button-MG]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "Déployer des stratégies ASC dans l’étendue d’un groupe d’administration."
[DTA-Button-Sub]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "Déployer des stratégies ASC dans l’étendue d’un abonnement."

[//]: # (**************************)
[//]: # (ÉTIQUETTES DE LIENS EXTERNES CI-DESSOUS)
[//]: # (**************************)

[GitHub-MG]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicy.json
[GitHub-Sub]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicySub.json

[DTA-MG]: https://portal.azure.com/#blade/Microsoft_Azure_CreateUIDef/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicy.json/uiFormDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicyUI.json
[DTA-Sub]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicySub.json
