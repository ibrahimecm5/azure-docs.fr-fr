---
title: Créer et utiliser une identité managée avec BYOS (bring-your-own-storage)
titleSuffix: Azure Applied AI Services
description: Comprendre comment créer et utiliser des identités managées avec des comptes BYOS
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 83b8867ae750fe85bc848e339ddb0409cba85093
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400601"
---
# <a name="create-and-use-managed-identity-for-your-form-recognizer-resource"></a>Créer et utiliser une identité managée pour votre ressource Form Recognizer

> [!IMPORTANT]
> L’attribution du contrôle d’accès en fonction du rôle Azure (Azure RBAC) est actuellement en préversion et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Les attributions RBAC Azure sont utilisées pour accorder des autorisations d’identité managée.

## <a name="what-is-managed-identity"></a>Qu’est-ce qu’une identité managée ?

L’identité managée Azure est un principal de service qui crée une identité Azure Active Directory (Azure AD) et des autorisations spécifiques pour les ressources managées Azure. Vous pouvez utiliser une identité managée pour accorder l’accès à une ressource qui prend en charge l’authentification Azure AD. Pour accorder l’accès, attribuez un rôle à une identité managée en utilisant le [contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md) (Azure RBAC).  Il n’y a aucun coût supplémentaire pour utiliser l’identité managée dans Azure.

L’identité managée prend en charge les comptes de stockage Blob Azure accessibles en privé et publiquement.  Pour les comptes de stockage avec accès public, vous pouvez choisir d’utiliser une signature d’accès partagé (SAS) pour accorder un accès limité.   Dans cet article, vous allez apprendre à activer une identité managée affectée par le système pour votre instance Form Recognizer.

## <a name="private-storage-account-access"></a>Accès aux comptes de stockage privé
> [!NOTE]
>
> Actuellement, Form Recognizer ne prend en charge que les identités managées affectées par le système. Les identités managées affectées par l’utilisateur figurent sur la feuille de route et seront activées dans un avenir proche. 


 L’accès et l’authentification des comptes de stockage Azure privé sont pris en charge par les [identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Si vous avez un compte de stockage Azure protégé par un réseau virtuel ou un pare-feu ou si vous avez activé l’option BYOS (bring-your-own-storage), Form Recognizer ne peut pas accéder directement aux données de votre compte de stockage. Toutefois, une fois qu’une identité managée est activée, le service Form Recognizer peut accéder à votre compte de stockage à l’aide des informations d’identification d’une identité managée attribuée.

> [!NOTE]
>
> * Si vous envisagez d’analyser vos données de stockage à l’aide de l’outil [**Form Recognizer Sample Labeling (FOTT)**](https://fott-2-1.azurewebsites.net/), vous devez déployer l’outil derrière votre réseau virtuel ou votre pare-feu.
>
> * Les API [**Analyser le reçu**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync), [**Analyser la carte de visite**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync), [**Analyser la facture**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291), [**Analyser le document d’identité**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707) et [**Analyser le formulaire personnalisé**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) peuvent extraire les données d’un seul document en publiant les demandes sous forme de contenu binaire brut. Dans ces scénarios, aucune information d’identification d’identité managée n’est requise.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

* Un [**compte Azure**](https://azure.microsoft.com/free/cognitive-services/) actif : si vous n’en avez pas, vous pouvez [**créer un compte gratuit**](https://azure.microsoft.com/free/).

* Une ressource [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) ou [**Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure. Pour des instructions détaillées, _consultez_ [Créer une ressource Cognitive Services dans le portail Azure](../../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows).

* Un [**compte de stockage Blob Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) dans la même région que votre ressource Form Recognizer. Vous allez créer des conteneurs pour stocker et organiser vos données d’objet blob dans votre compte de stockage. 

  * Si votre compte de stockage se trouve derrière un pare-feu, **vous devez activer la configuration suivante** : </br></br>

  * Dans la page de votre compte de stockage, dans le menu de gauche, sélectionnez **Sécurité + réseau** → **Mise en réseau**.
    :::image type="content" source="media/managed-identities/security-and-networking-node.png" alt-text="Capture d’écran : onglet Sécurité + réseau.":::

  * Dans la fenêtre principale, sélectionnez **Autoriser l’accès à partir de réseaux sélectionnés**.
  :::image type="content" source="media/managed-identities/firewalls-and-virtual-networks.png" alt-text="Capture d’écran : case d’option Réseaux sélectionnés activée.":::

  * Dans la page Réseaux sélectionnés, accédez à la catégorie **Exceptions** et assurez-vous que la case à cocher [**Autoriser les services Azure de la liste des services approuvés à accéder à ce compte de stockage**](../../storage/common/storage-network-security.md?tabs=azure-portal#manage-exceptions) est activée.

    :::image type="content" source="media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="Capture d’écran : case à cocher Autoriser les services approuvés, vue du portail":::
* Connaissances générales sur le [**contrôle d’accès en fonction du rôle Azure (Azure RBAC)**](../../role-based-access-control/role-assignments-portal.md) à l’aide du portail Azure.

## <a name="managed-identity-assignments"></a>Attributions d’identités managées

Il existe deux types d’identités managées : celles qui sont **affectées par le système** et celles qui sont **affectées par l’utilisateur**. Actuellement, Form Recognizer est pris en charge par l’identité managée affectée par le système. Une identité managée affectée par le système est **activée** directement sur une instance de service. Elle n’est pas activée par défaut. Vous devez accéder à votre ressource et mettre à jour le paramètre d’identité. L’identité managée affectée par le système est liée à votre ressource tout au long de son cycle de vie. Si vous supprimez votre ressource, l’identité managée est également supprimée.

Dans les étapes suivantes, nous allons activer une identité managée affectée par le système et accorder à Form Recognizer un accès limité à votre compte de stockage Blob Azure.

## <a name="enable-a-system-assigned-managed-identity"></a>Activer une identité managée affectée par le système

>[!IMPORTANT]
>
> Pour activer une identité managée affectée par le système, vous avez besoin d’autorisations **Microsoft.Authorization/roleAssignments/write**, telles que [**Propriétaire**](../../role-based-access-control/built-in-roles.md#owner) ou [**Administrateur de l’accès utilisateur**](../../role-based-access-control/built-in-roles.md#user-access-administrator). Vous pouvez spécifier une étendue à quatre niveaux : groupe d’administration, abonnement, groupe de ressources ou ressource.

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à votre abonnement Azure.

1. Accédez à la page de votre ressource **Form Recognizer** dans le portail Azure.

1. Dans le rail de gauche, sélectionnez **Identité** dans la liste **Gestion des ressources** :

    :::image type="content" source="media/managed-identities/resource-management-identity-tab.png" alt-text="Capture d’écran : onglet identité de gestion des ressources dans le portail Azure":::

1. Dans la fenêtre principale, basculez le curseur **État affecté par le système** sur **Activé**.

1. Sous **Autorisations**, sélectionnez **Attributions des rôles Azure** :

    :::image type="content" source="media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="Capture d’écran : activer une identité managée affectée par le système dans le portail Azure":::

1. Une page des attributions de rôles Azure s’ouvre. Choisissez votre abonnement dans le menu déroulant, puis sélectionnez **&plus; Ajouter une attribution de rôle**.

    :::image type="content" source="media/managed-identities/azure-role-assignments-page-portal.png" alt-text="Capture d’écran : page des attributions de rôles Azure dans le portail Azure":::

    > [!NOTE]
    >
    > Si vous ne parvenez pas à affecter un rôle dans le portail Azure parce que l’option Ajouter > Ajouter une attribution de rôle est désactivée ou que vous recevez l’erreur d’autorisation « Vous ne disposez pas des autorisations nécessaires pour ajouter une attribution de rôle à cette étendue », vérifiez que vous êtes actuellement connecté en tant qu’utilisateur avec un rôle attribué qui dispose des autorisations Microsoft.Authorization/roleAssignments/write, telles que Propriétaire ou Administrateur de l’accès utilisateur, dans l’étendue Stockage de la ressource de stockage.

 7. Ensuite, vous allez attribuer un rôle **Lecteur des données Blob du stockage** à votre ressource de service Form Recognizer. Dans la fenêtre contextuelle **Ajouter une attribution de rôle**, renseignez les champs comme suit et sélectionnez **Enregistrer** :

    | Champ | Valeur|
    |------|--------|
    |**Étendue**| **_Stockage_**|
    |**Abonnement**| **_L’abonnement associé à votre ressource de stockage_**.|
    |**Ressource**| **_Le nom de votre ressource de stockage_**|
    |**Rôle** | **_Lecteur des données Blob du stockage_** – Permet l’accès en lecture aux données et aux conteneurs blob du Stockage Azure.|

     :::image type="content" source="media/managed-identities/add-role-assignment-window.png" alt-text="Capture d’écran : page d’ajout d’attributions de rôles dans le portail Azure":::

1. Une fois que vous avez reçu le message de confirmation _Attribution de rôle ajoutée_, actualisez la page pour voir l’attribution de rôle ajoutée.

    :::image type="content" source="media/managed-identities/add-role-assignment-confirmation.png" alt-text="Capture d’écran : message contextuel de confirmation de l’ajout de l’attribution de rôle":::

1. Si vous ne voyez pas immédiatement la modification, patientez et essayez d’actualiser la page une nouvelle fois. Lorsque vous attribuez des rôles ou supprimez des attributions de rôle, un délai maximal de 30 minutes peut être nécessaire avant que les modifications prennent effet.

    :::image type="content" source="media/managed-identities/assigned-roles-window.png" alt-text="Capture d’écran : fenêtre des attributions de rôles Azure":::

 Et voilà ! Vous avez terminé les étapes pour activer une identité managée affectée par le système. Avec ces informations d’identification d’identité, vous pouvez octroyer à Form Recognizer des droits d’accès spécifiques à des documents et des fichiers stockés dans votre compte BYOS.

## <a name="learn-more-about--managed-identity"></a>Plus d’informations sur l’identité managée

> [!div class="nextstepaction"]
> [Identités managées pour les ressources Azure : Forum aux questions – Azure AD](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)
