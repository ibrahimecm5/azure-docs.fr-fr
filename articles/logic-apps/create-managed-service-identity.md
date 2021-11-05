---
title: Authentifier les connexions avec des identités managées
description: Utilisez une identité managée pour authentifier les connexions de workflow aux ressources protégées par Azure AD sans informations d’identification ni secrets dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell, subject-rbac-steps, ignite-fall-2021
ms.openlocfilehash: c6e814f0b0b36408210cac7657c947f16a0076c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085159"
---
# <a name="authenticate-access-to-azure-resources-with-managed-identities-in-azure-logic-apps"></a>Authentifier l’accès aux ressources Azure avec des identités managées dans Azure Logic Apps

Dans un workflow d’application logique, certains déclencheurs et actions prennent en charge l’utilisation d’une [identité managée](../active-directory/managed-identities-azure-resources/overview.md), anciennement appelée *identité MSI (Managed Service Identity)* , pour authentifier les connexions aux ressources protégées par Azure Active Directory (Azure AD). Si une identité managée est activée pour votre ressource d’application logique, vous n’êtes pas obligé de fournir des informations d’identification, des secrets ou des jetons Azure AD. Azure gère cette identité et sécurise les informations d’authentification dans la mesure où vous n’avez pas à gérer ces informations sensibles.

Azure Logic Apps prend en charge les [identités managées *affectées par le système*](../active-directory/managed-identities-azure-resources/overview.md), que vous pouvez utiliser avec une seule ressource d’application logique, et les [identités managées *affectées par l’utilisateur*](../active-directory/managed-identities-azure-resources/overview.md), que vous pouvez partager dans un groupe de ressources d’application logique selon l’emplacement d’exécution de vos workflows d’application logique.

| Type de ressource d’application logique | Environnement | Description |
|-------------------------|-------------|-------------|
| Consommation | - Azure Logic Apps multilocataire <p><p>- Environnement de service d’intégration (ISE) | Vous pouvez activer et utiliser l’identité affectée par le système *ou* une *seule* identité affectée par l’utilisateur au niveau de la connexion et au niveau de la ressource d’application logique. |
| Standard | - Azure Logic Apps monolocataire <p><p>- App Service Environment v3 (ASEv3) <p><p>- Logic Apps avec Azure Arc | Pour le moment, vous pouvez *uniquement* utiliser l’identité affectée par le système (automatiquement activée). L’identité affectée par l’utilisateur n’est pas disponible actuellement. |
|||

Pour en savoir plus sur les limites des identités managées dans Azure Logic Apps, passez en revue les [limites sur les identités managées pour les applications logiques](logic-apps-limits-and-config.md#managed-identity). Pour plus d’informations sur les types de ressources d’application logique Consommation et Standard, consultez la documentation suivante :

* [Qu’est-ce qu’Azure Logic Apps ?](logic-apps-overview.md#resource-environment-differences)
* [Architecture monolocataire ou multilocataire et environnement de service d’intégration](single-tenant-overview-compare.md)
* [Logic Apps avec Azure Arc](azure-arc-enabled-logic-apps-overview.md)

<a name="triggers-actions-managed-identity"></a>
<a name="managed-connectors-managed-identity"></a>

## <a name="where-you-can-use-a-managed-identity"></a>Où utiliser une identité managée

Seules les opérations de connecteur intégrées et managées spécifiques qui prennent en charge l’authentification ouverte Azure AD (Azure AD OAuth) peuvent utiliser une identité managée pour l’authentification. Le tableau suivant fournit uniquement une *sélection d’exemples*. Pour obtenir une liste plus complète, passez en revue [Types d’authentification pour les déclencheurs et les actions qui prennent en charge l’authentification](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) et [Services Azure qui prennent en charge l’authentification Azure AD avec des identités managées](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="consumption"></a>[Consommation](#tab/consumption)

Le tableau suivant liste les opérations compatibles avec l’identité managée affectée par le système ou par l’utilisateur dans le type de ressource **Application logique (Consommation)**  :

| Type d'opération | Opérations prises en charge |
|----------------|----------------------|
| Intégré | - Gestion des API Azure <br>- Azure App Services <br>- Azure Functions <br>- HTTP <br>- HTTP + Webhook <p>**Remarque** : Les opérations HTTP peuvent authentifier les connexions aux comptes Stockage Azure derrière des pare-feux Azure avec l’identité affectée par le système. Toutefois, elles ne prennent pas en charge l’identité managée affectée par l’utilisateur pour authentifier les mêmes connexions. |
| Connecteur managé (**Préversion**) | Authentification unique : <br>- Azure Automation <br>- Azure Event Grid <br>- Azure Key Vault <br>- Azure Resource Manager <br>- HTTP avec Azure AD <p>Authentification multiple : <br> - Stockage Blob Azure <br>- SQL Server |
|||

### <a name="standard"></a>[Standard](#tab/standard)

Le tableau suivant liste les opérations compatibles avec l’identité managée affectée par le système dans le type de ressource **Application logique (Standard)**  :

| Type d'opération | Opérations prises en charge |
|----------------|----------------------|
| Intégré | - HTTP <br>- HTTP + Webhook <p>**Remarque** : Les opérations HTTP peuvent authentifier les connexions aux comptes Stockage Azure derrière des pare-feux Azure avec l’identité affectée par le système. |
| Connecteur managé (**Préversion**) | Authentification unique : <br>- Azure Automation <br>- Azure Event Grid <br>- Azure Key Vault <br>- Azure Resource Manager <br>- HTTP avec Azure AD <p>Authentification multiple : <br> - Stockage Blob Azure <br>- SQL Server |
|||

---

Cet article explique comment activer et configurer l’identité affectée par le système ou par l’utilisateur selon que vous utilisez le type de ressource **Application logique (Consommation)** ou **Application logique (Standard)** . Contrairement à l’identité affectée par le système, vous *devez* créer manuellement l’identité affectée par l’utilisateur pour le type de ressource **Application logique (Consommation)** . Cet article présente les étapes permettant de créer l’identité affectée par l’utilisateur avec le portail Azure et le modèle ARM (Azure Resource Manager). Pour Azure PowerShell, Azure CLI et l’API REST Azure, consultez la documentation suivante :

| Outil | Documentation |
|------|---------------|
| Azure PowerShell | [Créer une identité affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) |
| Azure CLI | [Créer une identité affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |
| API REST Azure | [Créer une identité affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) |
|||

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/). L’identité managée et la ressource Azure cible à laquelle vous souhaitez accéder doivent utiliser le même abonnement Azure.

* Pour accorder à une identité managée l’accès à une ressource Azure, vous devez ajouter un rôle à la ressource cible pour cette identité. Pour ajouter des rôles, vous devez disposer d’[autorisations d’administrateur Azure AD](../active-directory/roles/permissions-reference.md) qui permettent d’attribuer des rôles à des identités dans le locataire Azure AD correspondant.

* Ressource Azure cible à laquelle vous souhaitez accéder. Dans cette ressource, vous allez ajouter un rôle pour l’identité managée, ce qui permet à la connexion ou ressource d’application logique d’authentifier l’accès à la ressource cible.

* Ressource d’application logique où vous souhaitez utiliser [le déclencheur ou les actions qui prennent en charge les identités managées](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

  | Type de ressource d’application logique | Prise en charge des identités managées |
  |-------------------------|--------------------------|
  | Consommation | Identité affectée par le système ou par l’utilisateur |
  | Standard | Identité affectée par le système (activée automatiquement) |
  |||

<a name="system-assigned-azure-portal"></a>
<a name="azure-portal-system-logic-app"></a>

## <a name="enable-system-assigned-identity-in-azure-portal"></a>Activer une identité attribuée par le système sur le portail Azure

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Identité**.

1. Dans le volet **Identité**, sous **Affecté(e) par le système**, sélectionnez **Activé** > **Enregistrer**. Quand Azure vous invite à confirmer l’opération, sélectionnez **Oui**.

   ![Capture d’écran montrant le portail Azure avec le volet « Identité » de l’application logique Consommation et l’onglet « Affecté(e) par le système » avec les options « Activé » et « Enregistrer » sélectionnées.](./media/create-managed-service-identity/enable-system-assigned-identity-consumption.png)

   > [!NOTE]
   > Si vous recevez une erreur indiquant que vous ne pouvez avoir qu’une seule identité managée, cela signifie que votre ressource d’application logique est déjà associée à l’identité affectée par l’utilisateur. Avant de pouvoir ajouter l’identité affectée par le système, vous devez d’abord *supprimer* l’identité affectée par l’utilisateur de votre ressource d’application logique.

   Votre ressource d’application logique peut désormais utiliser l’identité affectée par le système qui est inscrite auprès d’Azure AD et représentée par un ID d’objet.

   ![Capture d’écran montrant le volet « Identité » de l’application logique Consommation avec l’ID d’objet pour l’identité affectée par le système.](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Propriété | Valeur | Description |
   |----------|-------|-------------|
   | **ID de l’objet (principal)** | <*identity-resource-ID*> | GUID (identificateur global unique) qui représente l’identité affectée par le système pour votre application logique dans un locataire Azure AD. |
   ||||

1. À présent, suivez les [étapes qui donnent à cette identité l’accès à la ressource](#access-other-resources), et que vous trouverez plus loin dans cette rubrique.

### <a name="standard"></a>[Standard](#tab/standard)

Sur une ressource **Application logique (Standard)** , l’identité affectée par le système est automatiquement activée.

1. Sur le [Portail Azure](https://portal.azure.com), ouvrez votre flux de travail d’application logique dans le Concepteur.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Identité**.

1. Dans le volet **Identité**, sous **Affecté(e) par le système**, sélectionnez **Activé** > **Enregistrer**. Quand Azure vous invite à confirmer l’opération, sélectionnez **Oui**.

   ![Capture d’écran montrant le portail Azure avec le volet « Identité » de l’application logique Standard et l’onglet « Affecté(e) par le système » avec les options « Activé » et « Enregistrer » sélectionnées.](./media/create-managed-service-identity/enable-system-assigned-identity-standard.png)

   Votre ressource d’application logique peut désormais utiliser l’identité affectée par le système qui est inscrite auprès d’Azure AD et représentée par un ID d’objet.

   ![Capture d’écran montrant le volet « Identité » de l’application logique Standard avec l’ID d’objet pour l’identité affectée par le système.](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Propriété | Value | Description |
   |----------|-------|-------------|
   | **ID de l’objet (principal)** | <*identity-resource-ID*> | GUID (identificateur global unique) qui représente l’identité affectée par le système pour votre application logique dans un locataire Azure AD. |
   ||||

1. À présent, suivez les [étapes qui donnent à cette identité l’accès à la ressource](#access-other-resources), et que vous trouverez plus loin dans cette rubrique.

---

<a name="system-assigned-template"></a>
<a name="template-system-logic-app"></a>

## <a name="enable-system-assigned-identity-in-an-arm-template"></a>Activer une identité affectée par le système dans un modèle ARM

Pour automatiser la création et le déploiement de ressources Azure telles que des applications logiques, vous pouvez utiliser un [modèle ARM](logic-apps-azure-resource-manager-templates-overview.md). Pour activer l’identité managée affectée par le système pour votre ressource d’application logique dans le modèle, ajoutez l’objet `identity` et la propriété enfant `type` à la définition de ressource de l’application logique dans le modèle, par exemple :

### <a name="consumption"></a>[Consommation](#tab/consumption)

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {},
   <...>
}
```

### <a name="standard"></a>[Standard](#tab/standard)

```json
{
   "apiVersion": "2021-01-15",
   "type": "Microsoft.Web/sites",
   "name": "[variables('sites_<logic-app-resource-name>_name')]",
   "location": "[resourceGroup().location]",
   "kind": "functionapp,workflowapp",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {},
   <...>
}
```

---

Quand Azure crée la définition de ressource de votre application logique, l’`identity`objet obtient les propriétés supplémentaires suivantes :

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propriété (JSON) | Valeur | Description |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Identificateur global unique (GUID) de l’objet du principal de service pour l’identité managée qui représente votre application logique dans le locataire Azure AD. Ce GUID apparaît parfois sous la forme d’un « ID d’objet » ou `objectID`. |
| `tenantId` | <*Azure-AD-tenant-ID*> | Identificateur global unique (GUID) représentant le locataire Azure AD où l’application logique est maintenant membre. À l’intérieur du locataire Azure AD, le principal du service a le même nom que l’instance d’application logique. |
||||

<a name="azure-portal-user-identity"></a>
<a name="user-assigned-azure-portal"></a>

## <a name="create-user-assigned-identity-in-the-azure-portal-consumption-only"></a>Créer une identité affectée par l’utilisateur dans le portail Azure (Consommation uniquement)

Avant de pouvoir activer l’identité affectée par l’utilisateur sur votre ressource **Application logique (Consommation)** , vous devez d’abord créer cette identité en tant que ressource Azure distincte.

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com), entrez `managed identities`. Sélectionnez **Identités managées**.

   ![Capture d’écran montrant le portail Azure avec l’option « Identités managées » sélectionnée.](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Dans le volet **Identités managées**, sélectionnez **Créer**.

   ![Capture d’écran montrant le volet « Identités managées » avec l’option « Créer » sélectionnée.](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Fournissez des informations sur votre identité managée, puis sélectionnez **Vérifier + créer**, par exemple :

   ![Capture d’écran montrant le volet « Créer une identité managée affectée par l’utilisateur » avec les détails de l’identité managée.](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Abonnement** | Oui | <*Azure-subscription-name*> | Nom de l’abonnement Azure à utiliser. |
   | **Groupe de ressources** | Oui | <*nom-groupe-de-ressources-Azure*> | Nom du groupe de ressources Azure à utiliser. Créez un groupe, ou sélectionnez un groupe existant. Cet exemple crée un groupe nommé `fabrikam-managed-identities-RG`. |
   | **Région** | Oui | <*Azure-region*> | Région Azure où stocker les informations relatives à votre ressource. Cet exemple utilise la région **USA Ouest**. |
   | **Nom** | Oui | <*nom-identité-affectée-par-utilisateur*> | Nom à donner à votre identité affectée par l’utilisateur. Cet exemple utilise `Fabrikam-user-assigned-identity`. |
   |||||

   Une fois les informations validées, Azure crée votre identité managée. Vous pouvez maintenant ajouter l’identité affectée par l’utilisateur à votre ressource d’application logique, qui ne peut avoir qu’une seule identité affectée par l’utilisateur.

1. Dans le portail Azure, ouvrez votre ressource d’application logique.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Identité**.

1. Dans le volet **Identité**, sélectionnez **Affecté(e) par l’utilisateur** > **Ajouter**.

   ![Capture d’écran montrant le volet « Identité » avec l’option « Ajouter » sélectionnée.](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Dans le volet **Ajouter une identité managée affectée par l’utilisateur**, effectuez les étapes suivantes :

   1. Dans la liste **Abonnement**, sélectionnez votre abonnement Azure si cela n’est pas déjà fait.

   1. Dans la liste présentant *toutes* les identités managées de cet abonnement, sélectionnez l’identité affectée par l’utilisateur qui vous intéresse. Pour filtrer la liste, dans la zone de recherche **Identités managées affectées par l’utilisateur**, entrez le nom de l’identité ou du groupe de ressources.

      ![Capture d’écran montrant l’identité affectée par l’utilisateur sélectionnée.](./media/create-managed-service-identity/select-user-assigned-identity.png)

   1. Une fois que vous avez terminé, sélectionnez **Ajouter**.

      > [!NOTE]
      > Si vous recevez une erreur indiquant que vous ne pouvez avoir qu’une seule identité managée, cela signifie que votre application logique est déjà associée à l’identité affectée par le système. Avant de pouvoir ajouter l’identité affectée par l’utilisateur, vous devez d’abord désactiver l’identité affectée par le système.

   Votre application logique est désormais associée à l’identité managée affectée par l’utilisateur.

   ![Capture d’écran montrant l’association entre l’identité affectée par l’utilisateur et la ressource d’application logique.](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. À présent, suivez les [étapes qui donnent à cette identité l’accès à la ressource](#access-other-resources), et que vous trouverez plus loin dans cette rubrique.

<a name="template-user-identity"></a>

## <a name="create-user-assigned-identity-in-an-arm-template-consumption-only"></a>Créer une identité affectée par l’utilisateur dans un modèle ARM (Consommation uniquement)

Pour automatiser la création et le déploiement de ressources Azure telles que les applications logiques, vous pouvez utiliser un [modèle ARM](logic-apps-azure-resource-manager-templates-overview.md), lequel prend en charge les [identités affectées par l’utilisateur pour l’authentification](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). Dans la section `resources` de votre modèle, la définition de ressource de votre application logique nécessite les éléments suivants :

* Objet `identity` dont la propriété `type` a la valeur `UserAssigned`

* Objet `userAssignedIdentities` enfant qui spécifie la ressource et le nom affectés par l’utilisateur

Cet exemple montre une définition de ressource d’application logique pour une requête HTTP PUT, et comprend un objet `identity` non paramétré. La réponse à la requête PUT et à l’opération GET qui suit comporte également cet objet `identity` :

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

Si votre modèle inclut également la définition de ressource de l’identité managée, vous pouvez paramétrer l’objet `identity`. Cet exemple montre comment l’objet `userAssignedIdentities` enfant référence une variable `userAssignedIdentity` que vous définissez dans la section `variables` de votre modèle. Cette variable référence l’ID de ressource de l’identité affectée par l’utilisateur.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Accorder à une identité l’accès aux ressources

Avant de pouvoir utiliser l’identité gérée de votre application logique pour l’authentification, sur la ressource Azure dans laquelle vous souhaitez utiliser l’identité, vous devez configurer l’accès à votre identité à l’aide du contrôle d’accès en fonction du rôle Azure (Azure RBAC). Les étapes de cette section expliquent comment attribuer le rôle approprié à cette identité sur la ressource Azure en utilisant le [portail Azure](#azure-portal-assign-access) et le [modèle ARM (Azure Resource Manager)](../role-based-access-control/role-assignments-template.md). Pour Azure PowerShell, Azure CLI et l’API REST Azure, consultez la documentation suivante :

| Outil | Documentation |
|------|---------------|
| Azure PowerShell | [Ajouter une attribution de rôle](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) |
| Azure CLI | [Ajouter une attribution de rôle](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md) |
| API REST Azure | [Ajouter une attribution de rôle](../role-based-access-control/role-assignments-rest.md) |
|||

<a name="azure-portal-assign-access"></a>

### <a name="assign-managed-identity-role-based-access-in-the-azure-portal"></a>Attribuer un accès d’identité managée basée sur le rôle dans le Portail Azure

Sur la ressource Azure dans laquelle vous souhaitez utiliser l’identité managée à des fins d’authentification, vous devez affecter cette identité à un rôle qui peut accéder à cette ressource cible. Pour plus d’informations générales sur cette tâche, consultez [Attribuer un accès d’identité managée à une autre ressource à l’aide du RBAC Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

> [!NOTE]
> Quand une identité managée a accès à une ressource Azure dans le même abonnement, l’identité ne peut accéder qu’à cette ressource. Toutefois, dans certains déclencheurs et actions qui prennent en charge les identités managées, vous devez d’abord sélectionner le groupe de ressources Azure qui contient la ressource cible. Si l’identité ne dispose pas d’un accès au niveau du groupe de ressources, aucune ressource de ce groupe n’est listée malgré l’accès à la ressource cible.
>
> Pour gérer ce comportement, vous devez également autoriser l’identité à accéder au groupe de ressources, et pas seulement à la ressource. De même, si vous devez sélectionner votre abonnement avant de pouvoir sélectionner la ressource cible, vous devez autoriser l’identité à accéder à l’abonnement.

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez la ressource où vous souhaitez utiliser l’identité.

1. Dans le menu de la ressource, sélectionnez **Contrôle d’accès (IAM)**  > **Ajouter** > **Ajouter une attribution de rôle**.

   > [!NOTE]
   > Si l’option **Ajouter une attribution de rôle** est désactivée, vous n’avez pas les autorisations pour attribuer les rôles. Pour plus d’informations, consultez [Rôles intégrés Azure AD](../active-directory/roles/permissions-reference.md).

1. À présent, attribuez le rôle nécessaire à votre identité managée. Dans l’onglet **Rôle**, attribuez un rôle à votre identité qui lui permette d’accéder à la ressource actuelle.

   Pour cet exemple, attribuez le rôle nommé **Contributeur aux données Blob de stockage**, qui comprend un accès en écriture aux objets blob dans un conteneur de Stockage Azure. Pour plus d’informations sur les rôles de conteneur de stockage spécifiques, passez en revue les [Rôles qui peuvent accéder aux objets blob dans un conteneur Stockage Azure](../storage/blobs/authorize-access-azure-active-directory.md#assign-azure-roles-for-access-rights).

1. Ensuite, choisissez l’identité managée dans laquelle vous souhaitez affecter le rôle. Sous **Attribuer l’accès à**, sélectionnez **Identité managée** > **Ajouter des membres**.

1. En fonction du type de votre identité managée, sélectionnez ou fournissez les valeurs suivantes :

   | Type | Instance de service Azure | Abonnement | Membre |
   |------|------------------------|--------------|--------|
   | **Attribué par le système** | **Application logique** | <*Azure-subscription-name*> | <*your-logic-app-name*> |
   | **Affectée par l’utilisateur** (Consommation uniquement) | Non applicable | <*Azure-subscription-name*> | <*your-user-assigned-identity-name*> |
   |||||

   Pour plus d’informations sur l’attribution de rôles, consultez la documentation,[Attribution de rôles à l’aide du Portail Azure](../role-based-access-control/role-assignments-portal.md).

1. Une fois que vous avez fini de configurer l’accès pour l’identité, vous pouvez utiliser l’identité pour [authentifier l’accès aux déclencheurs et aux actions qui prennent en charge les identités managées](#authenticate-access-with-identity).

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Authentifier l’accès avec des identités managées

Une fois que vous avez [activé l’identité managée pour votre ressource d’application logique](#azure-portal-system-logic-app) et [accordé à cette identité l’accès à la ressource ou entité cibles](#access-other-resources), vous pouvez utiliser cette identité dans des [déclencheurs et actions prenant en charge les identités managées](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

> [!IMPORTANT]
> Si vous avez une fonction Azure dans laquelle vous voulez utiliser l’identité affectée par le système, commencez par [activer l’authentification pour les fonctions Azure](logic-apps-azure-functions.md#enable-authentication-for-functions).

Ces étapes montrent comment utiliser l’identité managée avec un déclencheur ou une action via le portail Azure. Pour spécifier l’identité managée dans la définition JSON sous-jacente d’un déclencheur ou d’une action, passez en revue [Authentification avec des identités managées](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique.

1. Si ce n’est encore fait, ajoutez [le déclencheur ou l’action prenant en charge les identités managées](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > Tous les déclencheurs et toutes les actions ne vous permettent pas d’ajouter un type d’authentification. Pour plus d’informations, consultez [Types d’authentification pour les déclencheurs et les actions qui prennent en charge l’authentification](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Sur le déclencheur ou l’action que vous avez ajouté, procédez comme suit :

   * **Opérations intégrées qui prennent en charge l’authentification avec des identités managées**

     1. Dans la liste **Ajouter un nouveau paramètre**, ajoutez la propriété **Authentification** si la propriété n’apparaît pas déjà.

        ![Capture d’écran montrant un exemple d’action intégrée avec la liste « Ajouter un nouveau paramètre » ouverte et l’option « Authentification » sélectionnée dans Consommation.](./media/create-managed-service-identity/built-in-authentication-consumption.png)

     1. Dans la liste **Type d’authentification**, sélectionnez **Identité managée**.

        ![Capture d’écran montrant un exemple d’action intégrée avec la liste « Type d’authentification » ouverte et l’option « Identité managée » sélectionnée dans Consommation.](./media/create-managed-service-identity/built-in-managed-identity-consumption.png)

     Pour plus d’informations, passez en revue [Exemple : Authentifier le déclencheur ou l’action intégré avec une identité managée](#authenticate-built-in-managed-identity).

   * **Opérations de connecteur managé qui prennent en charge l’authentification avec des identités managées** (préversion)

     1. Dans la page de sélection du locataire, sélectionnez **Se connecter avec une identité managée (préversion)** . Par exemple :

        ![Capture d’écran montrant l’action Azure Resource Manager et l’option « Se connecter avec une identité managée » sélectionnée dans Consommation.](./media/create-managed-service-identity/select-connect-managed-identity-consumption.png)

     1. Dans la page suivante, renseignez le champ **Nom de la connexion**.

     1. Pour le type d’authentification, choisissez l’une des options suivantes en fonction de votre connecteur managé :

        * **Authentification unique** : ces connecteurs ne prennent en charge qu’un seul type d’authentification. Dans la liste **Identité managée**, sélectionnez l’identité managée actuellement activée si cela n’est pas déjà fait, puis sélectionnez **Créer**. Par exemple :

          ![Capture d’écran montrant la page du nom de la connexion avec une seule identité managée sélectionnée dans Consommation.](./media/create-managed-service-identity/single-system-identity-consumption.png)

        * **Authentification multiple** : ces connecteurs prennent en charge plusieurs types d’authentification. Dans la liste **Type d’authentification**, sélectionnez **Identité managée Logic Apps** > **Créer**. Par exemple :

          ![Capture d’écran montrant la page du nom de la connexion avec l’option « Identité managée Logic Apps » sélectionnée dans Consommation.](./media/create-managed-service-identity/multi-system-identity-consumption.png)

        Pour plus d’informations, passez en revue [Exemple : Authentifier un déclencheur ou une action de connecteur managé avec une identité managée](#authenticate-managed-connector-managed-identity).

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique.

1. Si ce n’est encore fait, ajoutez [le déclencheur ou l’action prenant en charge les identités managées](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > Tous les déclencheurs et toutes les actions ne vous permettent pas d’ajouter un type d’authentification. Pour plus d’informations, consultez [Types d’authentification pour les déclencheurs et les actions qui prennent en charge l’authentification](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Sur le déclencheur ou l’action que vous avez ajouté, procédez comme suit :

   * **Opérations intégrées qui prennent en charge l’authentification avec des identités managées**

     1. Dans la liste **Ajouter un nouveau paramètre**, ajoutez la propriété **Authentification** si la propriété n’apparaît pas déjà.

        ![Capture d’écran montrant un exemple d’action intégrée avec la liste « Ajouter un nouveau paramètre » ouverte et l’option « Authentification » sélectionnée dans Standard.](./media/create-managed-service-identity/built-in-authentication-standard.png)

     1. Dans la liste **Type d’authentification**, sélectionnez **Identité managée**.

        ![Capture d’écran montrant un exemple d’action intégrée avec la liste « Type d’authentification » ouverte et l’option « Identité managée » sélectionnée dans Standard.](./media/create-managed-service-identity/built-in-managed-identity-standard.png)

     Pour plus d’informations, passez en revue [Exemple : Authentifier le déclencheur ou l’action intégré avec une identité managée](#authenticate-built-in-managed-identity).

   * **Opérations de connecteur managé qui prennent en charge l’authentification avec des identités managées** (préversion)

     1. Dans la page de sélection du locataire, sélectionnez **Se connecter avec une identité managée (préversion)** . Par exemple :

        ![Capture d’écran montrant l’action Azure Resource Manager et l’option « Se connecter avec une identité managée » sélectionnée dans Standard.](./media/create-managed-service-identity/select-connect-managed-identity-standard.png)

     1. Dans la page suivante, renseignez le champ **Nom de la connexion**.

     1. Pour le type d’authentification, choisissez l’une des options suivantes en fonction de votre connecteur managé :

        * **Authentification unique** : ces connecteurs ne prennent en charge qu’un seul type d’authentification. Dans la liste **Identité managée**, sélectionnez l’identité managée actuellement activée si cela n’est pas déjà fait, puis sélectionnez **Créer**. Par exemple :

          ![Capture d’écran montrant la page du nom de la connexion avec une seule identité managée sélectionnée dans Standard.](./media/create-managed-service-identity/single-system-identity-standard.png)

        * **Authentification multiple** : ces connecteurs prennent en charge plusieurs types d’authentification. Dans la liste **Type d’authentification**, sélectionnez **Identité managée Logic Apps** > **Créer**. Par exemple :

          ![Capture d’écran montrant la page du nom de la connexion avec l’option « Identité managée Logic Apps » sélectionnée dans Standard.](./media/create-managed-service-identity/multi-system-identity-standard.png)

        Pour plus d’informations, passez en revue [Exemple : Authentifier un déclencheur ou une action de connecteur managé avec une identité managée](#authenticate-managed-connector-managed-identity).

---

<a name="authenticate-built-in-managed-identity"></a>

## <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Exemple : Authentifier le déclencheur ou l’action intégré avec une identité managée

Le déclencheur ou l’action HTTP intégré peut utiliser l’identité affectée par le système que vous activez sur votre ressource d’application logique. En général, le déclencheur ou l’action HTTP utilise les propriétés suivantes pour spécifier la ressource ou l’entité à laquelle vous souhaitez accéder :

| Propriété | Obligatoire | Description |
|----------|----------|-------------|
| **Méthode** | Oui | Méthode HTTP utilisée par l’opération que vous souhaitez exécuter |
| **URI** | Oui | URL de point de terminaison pour accéder à la ressource ou entité Azure cibles. La syntaxe de l’URI comprend généralement l’[ID de ressource](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) pour la ressource ou le service Azure. |
| **En-têtes** | Non | Toute valeur d’en-tête que vous devez ou souhaitez inclure dans la demande sortante, telle que le type de contenu |
| **Requêtes** | Non | Tout paramètre de requête que vous devez ou souhaitez inclure dans la demande, tel que le paramètre pour une opération spécifique ou la version de l’API pour l’opération que vous souhaitez exécuter |
| **Authentification** | Oui | Type d’authentification à utiliser pour authentifier l’accès à la ressource ou entité cibles |
||||

À titre d’exemple, supposons que vous souhaitez exécuter l’[opération de capture instantanée d’objet blob](/rest/api/storageservices/snapshot-blob) sur un blob dans le compte de Stockage Azure où vous avez précédemment configuré l’accès pour votre identité. Toutefois, le [connecteur de Stockage Blob Azure](/connectors/azureblob/) ne propose pas cette opération actuellement. Au lieu de cela, vous pouvez l’exécuter à l’aide de l’[action HTTP](logic-apps-workflow-actions-triggers.md#http-action) ou d’une autre [opération de l’API REST du service BLOB](/rest/api/storageservices/operations-on-blobs).

> [!IMPORTANT]
> Pour accéder aux comptes de stockage Azure derrière des pare-feu en utilisant des requêtes HTTP et des identités managées, veillez à configurer également votre compte de stockage avec l’[exception qui autorise l’accès de services Microsoft approuvés](../connectors/connectors-create-api-azureblobstorage.md#access-blob-storage-with-managed-identities).

Pour exécuter l’[opération de capture instantanée d’objet blob](/rest/api/storageservices/snapshot-blob), l’action HTTP spécifie les propriétés suivantes :

| Propriété | Obligatoire | Valeur d'exemple | Description |
|----------|----------|---------------|-------------|
| **Méthode** | Oui | `PUT`| Méthode HTTP utilisée par l’opération de capture instantanée d’objet blob |
| **URI** | Oui | `https://<storage-account-name>/<folder-name>/{name}` | ID de ressource d’un fichier de Stockage Blob Azure dans l’environnement global (public) Azure qui utilise cette syntaxe |
| **En-têtes** | Pour Stockage Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | Les valeurs d’en-tête `x-ms-blob-type`, `x-ms-version` et `x-ms-date` sont requises pour les opérations du service Stockage Azure. <p><p>**Important !** Dans le déclencheur HTTP sortant et les demandes d’action pour Stockage Azure, l’en-tête requiert la propriété `x-ms-version` et la version de l’API pour l’opération que vous souhaitez exécuter. La valeur `x-ms-date` doit être la date actuelle. Dans le cas contraire, votre workflow échoue avec une erreur `403 FORBIDDEN`. Pour obtenir la date actuelle au format requis, vous pouvez utiliser l’expression dans l’exemple de valeur. <p>Pour plus d’informations, consultez les rubriques suivantes : <p><p>- [En-têtes de demande – Capture instantanée d’objet blob](/rest/api/storageservices/snapshot-blob#request) <br>- [Contrôle de version pour les services Stockage Azure](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Requêtes** | Uniquement pour l’opération de capture instantanée de blob | `comp` = `snapshot` | Nom et valeur du paramètre de requête pour l’opération. |
|||||

### <a name="consumption"></a>[Consommation](#tab/consumption)

L’exemple suivant montre un exemple d’action HTTP avec toutes les valeurs de propriété précédemment décrites à utiliser pour l’opération Snapshot Blob :

![Capture d’écran montrant le portail Azure avec le workflow d’application logique Consommation et l’action HTTP configurée pour accéder à la ressource.](./media/create-managed-service-identity/http-action-example.png)

1. Après avoir ajouté l’action HTTP, ajoutez la propriété **Authentification** à l’action HTTP. Dans la liste **Ajouter un nouveau paramètre**, sélectionnez **Authentification**.

   ![Capture d’écran montrant le workflow Consommation avec l’action HTTP et la liste « Ajouter un nouveau paramètre » ouverte avec la propriété « Authentification » sélectionnée.](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Tous les déclencheurs et toutes les actions ne vous permettent pas d’ajouter un type d’authentification. Pour plus d’informations, consultez [Types d’authentification pour les déclencheurs et les actions qui prennent en charge l’authentification](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Dans la liste **Type d’authentification**, sélectionnez **Identité managée**.

   ![Capture d’écran montrant le workflow Consommation avec l’action HTTP et la propriété « Authentification » avec la valeur « Identité managée » sélectionnée.](./media/create-managed-service-identity/select-managed-identity.png)

1. Dans la liste des identités managées, sélectionnez les options disponibles en fonction de votre scénario.

   * Si vous configurez l’identité affectée par le système, sélectionnez **Identité managée affectée par le système** si cela n’est pas déjà fait.

     ![Capture d’écran montrant le workflow Consommation avec l’action HTTP et la propriété « Identité managée » avec la valeur « Identité managée affectée par le système » sélectionnée.](./media/create-managed-service-identity/select-system-assigned-identity.png)

   * Si vous configurez une identité affectée par l’utilisateur, sélectionnez cette identité, si cela n’est pas déjà fait.

     ![Capture d’écran montrant le workflow Consommation avec l’action HTTP et la propriété « Identité managée » avec l’identité affectée par l’utilisateur sélectionnée.](./media/create-managed-service-identity/select-user-assigned-identity-action.png)

   Cet exemple se poursuit avec l’**identité managée affectée par le système**.

1. Sur certains déclencheurs et actions, la propriété **Audience** apparaît également pour vous permettre de définir l’ID de ressource cible. Affectez à la propriété **Audience** la valeur de l’[ID de ressource de la ressource ou du service cible](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Autrement, par défaut, la propriété **Audience** utilise l’ID de ressource `https://management.azure.com/`, qui est l’ID de ressource pour Azure Resource Manager.
  
    Par exemple, si vous souhaitez authentifier l’accès à une [ressource de Coffre de clés dans le cloud global Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault), vous devez définir la propriété **Audience** *précisément* sur l’ID de ressource suivante : `https://vault.azure.net`. Veuillez noter que cet ID de ressource spécifique ne possède *pas* de barres obliques de fin. En réalité, l’inclusion d’une barre oblique de fin pourrait produire soit une erreur `400 Bad Request` soit une erreur `401 Unauthorized`.

   > [!IMPORTANT]
   > Vérifiez que l’ID de ressource cible *correspond exactement* à la valeur qu’attend Azure Active Directory, y compris les barres obliques de fin obligatoires. Par exemple, l’ID de ressource pour tous les comptes de Stockage Blob Azure requiert une barre oblique finale. Toutefois, l’ID de ressource pour un compte de stockage spécifique ne requiert pas de barre oblique finale. Vérifiez les [ID de ressource des services Azure qui prennent en charge Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Cet exemple définit la propriété **Audience** sur `https://storage.azure.com/` afin que les jetons d’accès utilisés pour l’authentification soient valides pour tous les comptes de stockage. Toutefois, vous pouvez également spécifier l’URL du service racine, `https://<your-storage-account>.blob.core.windows.net`, pour un compte de stockage spécifique.

   ![Capture d’écran montrant le workflow Consommation avec l’action HTTP et la propriété « Audience » définie sur l’ID de la ressource cible.](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Pour plus d’informations sur l’autorisation de l’accès au Stockage Azure avec Azure AD, consultez la documentation suivante :

   * [Autoriser l’accès aux objets blob et aux files d’attente Azure avec Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md)

   * [Autoriser l’accès au Stockage Azure avec Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Continuez à créer le workflow comme vous le souhaitez.

### <a name="standard"></a>[Standard](#tab/standard)

L’exemple suivant montre un exemple d’action HTTP avec toutes les valeurs de propriété précédemment décrites à utiliser pour l’opération Snapshot Blob :

![Capture d’écran montrant le portail Azure avec le workflow d’application logique Standard et l’action HTTP configurée pour accéder à la ressource.](./media/create-managed-service-identity/http-action-example-standard.png)

1. Après avoir ajouté l’action HTTP, ajoutez la propriété **Authentification** à l’action HTTP. Dans la liste **Ajouter un nouveau paramètre**, sélectionnez **Authentification**.

   ![Capture d’écran montrant le workflow Standard avec l’action HTTP et la liste « Ajouter un nouveau paramètre » ouverte avec la propriété « Authentification » sélectionnée.](./media/create-managed-service-identity/add-authentication-property-standard.png)

   > [!NOTE]
   > Tous les déclencheurs et toutes les actions ne vous permettent pas d’ajouter un type d’authentification. Pour plus d’informations, consultez [Types d’authentification pour les déclencheurs et les actions qui prennent en charge l’authentification](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Dans la liste **Type d’authentification**, sélectionnez **Identité managée**.

   ![Capture d’écran montrant le workflow Standard avec l’action HTTP et la propriété « Authentification » avec la valeur « Identité managée » sélectionnée.](./media/create-managed-service-identity/select-managed-identity-standard.png)

1. Dans la liste Identité managée, sélectionnez **Identité managée affectée par le système** si cela n’est pas déjà fait.

     ![Capture d’écran montrant le workflow Standard avec l’action HTTP et la liste « Identité managée » ouverte avec l’option « Identité managée affectée par le système » sélectionnée.](./media/create-managed-service-identity/select-system-assigned-identity-standard.png)

1. Sur certains déclencheurs et actions, la propriété **Audience** apparaît également pour vous permettre de définir l’ID de ressource cible. Affectez à la propriété **Audience** la valeur de l’[ID de ressource de la ressource ou du service cible](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Autrement, par défaut, la propriété **Audience** utilise l’ID de ressource `https://management.azure.com/`, qui est l’ID de ressource pour Azure Resource Manager.
  
    Par exemple, si vous souhaitez authentifier l’accès à une [ressource de Coffre de clés dans le cloud global Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-key-vault), vous devez définir la propriété **Audience** *précisément* sur l’ID de ressource suivante : `https://vault.azure.net`. Veuillez noter que cet ID de ressource spécifique ne possède *pas* de barres obliques de fin. En réalité, l’inclusion d’une barre oblique de fin pourrait produire soit une erreur `400 Bad Request` soit une erreur `401 Unauthorized`.

   > [!IMPORTANT]
   > Vérifiez que l’ID de ressource cible *correspond exactement* à la valeur qu’attend Azure Active Directory, y compris les barres obliques de fin obligatoires. Par exemple, l’ID de ressource pour tous les comptes de Stockage Blob Azure requiert une barre oblique finale. Toutefois, l’ID de ressource pour un compte de stockage spécifique ne requiert pas de barre oblique finale. Vérifiez les [ID de ressource des services Azure qui prennent en charge Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Cet exemple définit la propriété **Audience** sur `https://storage.azure.com/` afin que les jetons d’accès utilisés pour l’authentification soient valides pour tous les comptes de stockage. Toutefois, vous pouvez également spécifier l’URL du service racine, `https://<your-storage-account>.blob.core.windows.net`, pour un compte de stockage spécifique.

   ![Affecter à la propriété « Audience » l’ID de ressource cible](./media/create-managed-service-identity/specify-audience-url-target-resource-standard.png)

   Pour plus d’informations sur l’autorisation de l’accès au Stockage Azure avec Azure AD, consultez la documentation suivante :

   * [Autoriser l’accès aux objets blob et aux files d’attente Azure avec Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md)
   * [Autoriser l’accès au Stockage Azure avec Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Continuez à créer le workflow comme vous le souhaitez.

---

<a name="authenticate-managed-connector-managed-identity"></a>

## <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Exemple : Authentifier un déclencheur ou une action de connecteur géré avec une identité managée

Le connecteur managé Azure Resource Manager a une action, **Lire une ressource**, qui peut utiliser l’identité managée que vous activez sur votre ressource d’application logique. Cet exemple montre comment utiliser l’identité managée affectée par le système.

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Une fois que vous avez ajouté l’action à votre workflow et sélectionné votre locataire Azure AD, sélectionnez **Se connecter avec une identité managée (préversion)** .

   ![Capture d’écran montrant l’action Azure Resource Manager et l’option « Se connecter avec une identité managée » sélectionnée.](./media/create-managed-service-identity/select-connect-managed-identity-consumption.png)

1. Dans la page du nom de la connexion, indiquez un nom pour la connexion et sélectionnez l’identité managée à utiliser.

   L’action Azure Resource Manager étant une action à authentification unique, le volet d’informations de connexion présente une liste **Identité managée** qui sélectionne automatiquement l’identité managée actuellement activée sur la ressource d’application logique. Si vous avez activé une identité managée affectée par le système, la liste **Identité managée** sélectionne **Identité managée affectée par le système**. Si vous avez activé une identité managée affectée par l’utilisateur à la place, la liste sélectionne cette identité.

   Si vous utilisez un déclencheur ou une action à authentification multiple, comme le Stockage Blob Azure, le volet d’informations de connexion présente une liste **Type d’authentification** comprenant notamment l’option **Identité managée Logic Apps**.

   Dans cet exemple, **Identité managée affectée par le système** est la seule sélection disponible.

   ![Capture d’écran montrant l’action Azure Resource Manager avec le nom de connexion entré et l’option « Identité managée affectée par le système » sélectionnée.](./media/create-managed-service-identity/single-system-identity-consumption.png)

   > [!NOTE]
   > Si l’identité managée n’est pas activée quand vous essayez de créer ou de modifier la connexion ou qu’elle a été supprimée alors qu’une connexion avec une identité managée existe encore, vous recevez une erreur indiquant que vous devez activer l’identité et accorder l’accès à la ressource cible.

1. Quand vous êtes prêt, sélectionnez **Créer**.

1. Une fois la connexion créée, le concepteur peut extraire les valeurs, le contenu ou le schéma dynamiques à l’aide de l’authentification par identité managée.

1. Continuez à créer le workflow comme vous le souhaitez.

### <a name="standard"></a>[Standard](#tab/standard)

1. Une fois que vous avez ajouté l’action à votre workflow, dans le volet **Créer la connexion** de l’action, sélectionnez votre locataire Azure AD et **Se connecter avec une identité managée (préversion)** .

   ![Capture d’écran montrant l’action Azure Resource Manager et l’option « Se connecter avec une identité managée » sélectionnée.](./media/create-managed-service-identity/select-connect-managed-identity-standard.png)

1. Dans la page Nom de la connexion, indiquez un nom pour la connexion.

   L’action Azure Resource Manager étant une action à authentification unique, le volet d’informations de connexion présente une liste **Identité managée** qui sélectionne automatiquement l’identité managée actuellement activée sur la ressource d’application logique. Si vous avez activé une identité managée affectée par le système, la liste **Identité managée** sélectionne **Identité managée affectée par le système**. Si vous avez activé une identité managée affectée par l’utilisateur à la place, la liste sélectionne cette identité.

   Dans cet exemple, **Identité managée affectée par le système** est la seule sélection disponible.

   ![Capture d’écran montrant l’action Azure Resource Manager avec le nom de connexion entré et l’option « Identité managée affectée par le système » sélectionnée.](./media/create-managed-service-identity/single-system-identity-standard.png)

   Si vous utilisez un déclencheur ou une action à authentification multiple, comme le Stockage Blob Azure, le volet d’informations de connexion présente une liste **Type d’authentification** comprenant notamment l’option **Identité managée**.

   > [!NOTE]
   > Si l’identité managée n’est pas activée quand vous essayez de créer ou de modifier la connexion ou qu’elle a été supprimée alors qu’une connexion avec une identité managée existe encore, vous recevez une erreur indiquant que vous devez activer l’identité et accorder l’accès à la ressource cible.

1. Quand vous êtes prêt, sélectionnez **Créer**.

1. Une fois la connexion créée, le concepteur peut extraire les valeurs, le contenu ou le schéma dynamiques à l’aide de l’authentification par identité managée.

1. Continuez à créer le workflow comme vous le souhaitez.

---

<a name="logic-app-resource-definition-connection-managed-identity"></a>

## <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity-consumption"></a>Définition et connexions de ressources d’application logique qui utilisent une identité managée (Consommation)

Une connexion qui active et utilise une identité managée est un type particulier de connexion qui fonctionne uniquement avec une identité managée. Au moment de l’exécution, la connexion utilise l’identité managée qui est activée sur la ressource d’application logique. Au moment de l’exécution, le service Azure Logic Apps vérifie si un déclencheur et des actions de connecteur managé dans le workflow de l’application logique sont configurés pour utiliser l’identité managée et que toutes les autorisations requises sont configurées pour utiliser l’identité managée afin d’accéder aux ressources cibles spécifiées par le déclencheur et les actions. En cas de réussite, Azure Logic Apps récupère le jeton Azure AD associé à l’identité managée et utilise cette identité pour authentifier l’accès à la ressource cible et effectuer l’opération configurée dans le déclencheur et les actions.

### <a name="consumption"></a>[Consommation](#tab/consumption)

Dans une ressource **Application logique (Consommation)** , la configuration de la connexion est enregistrée dans l’objet `parameters` de la définition de la ressource d’application logique, qui contient l’objet `$connections` qui inclut des pointeurs vers l’ID de ressource de la connexion ainsi que l’ID de ressource de l’identité, si l’identité affectée par l’utilisateur est activée.

Cet exemple montre à quoi ressemble la configuration lorsque l’application logique active l’identité managée affectée par le système :

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

Cet exemple montre à quoi ressemble la configuration lorsque l’application logique active l’identité managée affectée par l’utilisateur :

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

### <a name="standard"></a>[Standard](#tab/standard)

Dans une ressource **Application logique (Standard)** , la configuration de la connexion est enregistrée dans la ressource d’application logique ou le fichier `connections.json` du projet, qui contient un objet JSON `managedApiConnections` qui inclut les informations de configuration de la connexion pour chaque connecteur managé utilisé dans un workflow. Par exemple, ces informations de connexion incluent des pointeurs vers l’ID de ressource de la connexion, ainsi que les propriétés de l’identité managée comme l’ID de ressource si l’identité affectée par l’utilisateur est activée.

Cet exemple montre à quoi ressemble la configuration lorsque l’application logique active l’identité managée affectée par l’utilisateur :

```json
{
    "managedApiConnections": {
        "<connector-name>": {
            "api": {
                "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{region}/managedApis/<connector-name>"
            },
            "connection": {
                "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/connections/<connection-name>"
            },
            "connectionRuntimeUrl": <connection-URL>,
            "authentication": { // Authentication with APIHub
                "type": "ManagedServiceIdentity"
            },
            "connectionProperties": {
                "authentication": { //Authentication with the target resource
                    "type": "ManagedServiceIdentity",
                    "identity": "<user-assigned-identity>", // Optional
                    "audience": "<resource-URL>"
                }
            }
        }
    }
}
```

---

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities-consumption"></a>Modèle ARM pour les connexions managées et les identités managées (Consommation)

Si vous automatisez le déploiement avec un modèle ARM et que votre workflow d’application logique inclut un déclencheur ou une action de connecteur managé qui utilise une identité managée, vérifiez que la définition de la ressource de connexion sous-jacente inclut la propriété `parameterValueType` avec `Alternative` comme valeur de propriété. Sinon, votre déploiement ARM ne pourra pas configurer la connexion pour utiliser l’identité managée à des fins d’authentification, et la connexion ne fonctionnera pas dans le workflow de votre application logique. Cet impératif s’applique uniquement aux [déclencheurs et actions de connecteur managé spécifiques](#triggers-actions-managed-identity) pour lesquels vous avez sélectionné l’[option **Se connecter avec une identité managée**](#authenticate-managed-connector-managed-identity).

Par exemple, voici la définition de ressource de connexion sous-jacente pour une action Azure Automation qui utilise une identité managée où la définition inclut la propriété `parameterValueType`, dont la valeur de propriété est `Alternative` :

```json
{
    "type": "Microsoft.Web/connections",
    "name": "[variables('automationAccountApiConnectionName')]",
    "apiVersion": "2016-06-01",
    "location": "[parameters('location')]",
    "kind": "V1",
    "properties": {
        "api": {
            "id": "[subscriptionResourceId('Microsoft.Web/locations/managedApis', parameters('location'), 'azureautomation')]"
        },
        "customParameterValues": {},
        "displayName": "[variables('automationAccountApiConnectionName')]",
        "parameterValueType": "Alternative"
    }
},
```

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Désactiver l’identité managée

Pour cesser d’utiliser l’identité managée pour l’authentification, commencez par [supprimer l’accès de l’identité à la ressource cible](#disable-identity-target-resource). Ensuite, sur votre ressource d’application logique, [désactivez l’identité affectée par le système ou supprimez l’identité affectée par l’utilisateur](#disable-identity-logic-app).

Quand vous désactivez l’identité managée sur votre ressource d’application logique, vous supprimez la capacité de cette identité à demander l’accès aux ressources Azure auxquelles elle avait accès.

> [!NOTE]
> Si vous désactivez l’identité affectée par le système, toutes les connexions utilisées par les workflows dans le workflow de cette application logique ne fonctionnent pas au moment de l’exécution, même si vous réactivez immédiatement l’identité. Ce comportement se produit car la désactivation de l’identité entraîne la suppression de l’ID d’objet. Chaque fois que vous activez l’identité, Azure génère l’identité avec un ID d’objet différent et unique. Pour résoudre ce problème, vous devez recréer les connexions afin qu’elles utilisent l’ID d’objet actuel pour l’identité actuelle affectée par le système.
>
> Essayez autant que possible de ne pas désactiver l’identité affectée par le système. Si vous souhaitez supprimer l’accès de l’identité aux ressources Azure, supprimez l’attribution de rôle de l’identité de la ressource cible. Si vous supprimez votre ressource d’application logique, Azure supprime automatiquement l’identité managée d’Azure AD.

Les étapes décrites dans cette section traitent de l’utilisation du [portail Azure](#azure-portal-disable) et du [modèle ARM (Azure Resource Manager)](#template-disable). Pour Azure PowerShell, Azure CLI et l’API REST Azure, consultez la documentation suivante :

| Outil | Documentation |
|------|---------------|
| Azure PowerShell | 1. [Supprimer une attribution de rôle](../role-based-access-control/role-assignments-powershell.md) <br>2. [Supprimer une identité affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) |
| Azure CLI | 1. [Supprimer une attribution de rôle](../role-based-access-control/role-assignments-cli.md) <br>2. [Supprimer une identité affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |
| API REST Azure | 1. [Supprimer une attribution de rôle](../role-based-access-control/role-assignments-rest.md) <br>2. [Supprimer une identité affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) |
|||

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Désactiver l’identité managée dans le portail Azure

Pour supprimer l’accès pour l’identité managée, supprimez l’attribution de rôle de l’identité de la ressource cible, puis désactivez l’identité managée.

<a name="disable-identity-target-resource"></a>

#### <a name="remove-role-assignment"></a>Supprimer une attribution de rôle

Les étapes suivantes permettent de supprimer l’accès à la ressource cible de l’identité managée :

1. Dans le [portail Azure](https://portal.azure.com), accédez à la ressource Azure cible dont vous souhaitez supprimer l’accès à l’identité managée.

1. Dans le menu de la ressource cible, sélectionnez **Contrôle d’accès (IAM)** . Sous la barre d’outils, sélectionnez **Attributions de rôle**.

1. Dans la liste des rôles, sélectionnez les identités managées que vous souhaitez supprimer. Dans la barre d’outils, sélectionnez **Supprimer**.

   > [!TIP]
   > Si l’option **Supprimer** est désactivée, vous n’avez probablement pas les autorisations appropriées. Pour plus d’informations sur les autorisations vous permettant de gérer des rôles pour des ressources, passez en revue [Autorisations des rôles d’administrateur dans Azure Active Directory](../active-directory/roles/permissions-reference.md).

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app-resource"></a>Désactiver l’identité managée sur la ressource d’application logique

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique.

1. Dans le menu de navigation de l’application logique, sous **Paramètres**, sélectionnez **Identité**, puis suivez les étapes correspondant à votre identité :

   * Sélectionnez **Affecté(e) par le système** > **Actif** > **Enregistrer**. Quand Azure vous invite à confirmer l’opération, sélectionnez **Oui**.

   * Sélectionnez **Affecté(e) par l’utilisateur** et l’identité managée, puis sélectionnez **Supprimer**. Quand Azure vous invite à confirmer l’opération, sélectionnez **Oui**.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-an-arm-template"></a>Désactiver l’identité managée dans un modèle ARM

Si vous avez créé l’identité managée de l’application logique à l’aide d’un modèle ARM, définissez la propriété enfant de l’`identity`objet `type` sur `None`.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser l’accès et les données dans Azure Logic Apps](logic-apps-securing-a-logic-app.md)
