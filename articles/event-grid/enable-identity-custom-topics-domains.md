---
title: Activer l’identité managée sur des rubriques et domaines personnalisés Azure Event Grid
description: Cet article explique comment activer une identité de service managée pour une rubrique ou un domaine personnalisés Azure Event Grid.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 1f9cae4c25c37d0fcb670804ae3450871dbd5259
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156595"
---
# <a name="assign-a-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Attribuer une identité managée à une rubrique ou un domaine personnalisés Event Grid 
Cet article explique comment utiliser le Portail Azure et l’interface CLI pour affecter une [identité gérée](../active-directory/managed-identities-azure-resources/overview.md) affectée par le système ou par l’utilisateur à une Event Grid rubrique ou un domaine personnalisé. 

## <a name="enable-identity-when-creating-a-topic-or-domain"></a>Activer l’identité lors de la création d’une rubrique ou d’un domaine

# <a name="azure-portal"></a>[Azure portal](#tab/portal)
Dans le **Portail Azure**, lors de la création d’une rubrique ou d’un domaine, vous pouvez affecter une identité affectée par le système ou deux identités affectées par l’utilisateur, mais pas les deux types d’identités. Une fois la rubrique ou le domaine créé, vous pouvez assigner les deux types d’identités en suivant les étapes de la section [Activer l’identité pour une rubrique ou un domaine existant](#enable-identity-for-an-existing-custom-topic-or-domain).

### <a name="enable-system-assigned-identity"></a>Activer l’identité attribuée par le système
Sous l’onglet **Avancé** de l’Assistant Création de rubrique ou de domaine, sélectionnez **Activer l’identité attribuée par le système**. 

:::image type="content" source="./media/managed-service-identity/create-topic-identity.png" alt-text="Image présentant l’option Activer l’identité attribuée par le système sélectionnée.":::

### <a name="enable-user-assigned-identity"></a>Activer l’identité affectée par l’utilisateur
1. Sous l’onglet **Avancé** de l’Assistant Création de rubrique ou de domaine, sélectionnez **Activer l’identité attribuée par le système**, puis **Ajouter une identité attribuée par l’utilisateur**. 

    :::image type="content" source="./media/managed-service-identity/create-page-add-user-assigned-identity-link.png" alt-text="Image présentant l’option Activer l’identité attribuée par l’utilisateur sélectionnée.":::
1. Dans la fenêtre **Sélectionner une identité attribuée par l’utilisateur**, sélectionnez l’abonnement qui possède l’identité attribuée par l’utilisateur, sélectionnez l'**identité affectée à l’utilisateur**, puis cliquez sur **Sélectionner**. 

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)
Vous pouvez également utiliser Azure CLI pour créer une rubrique ou un domaine personnalisés avec une identité attribuée par le système. Actuellement, Azure CLI ne prend pas en charge l’affectation d’une identité affectée par l’utilisateur à une rubrique ou à un domaine.  

Utilisez la commande `az eventgrid topic create` avec le paramètre `--identity` défini sur `systemassigned`. Si vous ne spécifiez pas de valeur pour ce paramètre, la valeur par défaut `noidentity` est utilisée. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Vous pouvez également utiliser la commande `az eventgrid domain create` pour créer un domaine avec une identité attribuée par le système.

---

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Activer une identité pour une rubrique ou un domaine personnalisés existants
Dans cette section, vous allez apprendre à activer une identité attribuée par le système ou par l’utilisateur pour une rubrique ou un domaine personnalisés existants. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)
Lorsque vous utilisez le Portail Azure, vous pouvez affecter une identité attribuée par le système et jusqu’à deux identités affectées par l’utilisateur à une rubrique ou un domaine existant.

Les procédures suivantes vous montrent comment activer une identité pour une rubrique personnalisée. Les étapes d’activation d’une identité pour un domaine sont similaires. 

1. Accédez au [portail Azure](https://portal.azure.com).
2. Dans la barre de recherche en haut, recherchez **Rubriques Event Grid**.
3. Sélectionnez la **rubrique personnalisée** pour laquelle vous souhaitez activer l’identité managée. 
4. Sélectionnez **Identité** dans le menu de gauche.

### <a name="to-assign-a-system-assigned-identity-to-a-topic"></a>Pour attribuer une identité attribuée par le système à une rubrique
1. Sous l’onglet **Attribuée par le système**, **activez** le commutateur pour activer l’identité. 
1. Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer le paramètre. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Page identité pour une rubrique personnalisée"::: 

### <a name="to-assign-a-user-assigned-identity-to-a-topic"></a>Pour attribuer une identité attribuée par l’utilisateur à une rubrique
1. Créez une identité attribuée par l’utilisateur en suivant les instructions de l’article [Gérer les identités managées attribuées à l’utilisateur](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md). 
1. Dans la page **Identité**, basculez vers l’onglet **Attribuée par l’utilisateur** dans le volet de droite, puis sélectionnez **+ Ajouter** dans la barre d’outils.

    :::image type="content" source="./media/managed-service-identity/user-assigned-identity-add-button.png" alt-text="Image représentant l’onglet Identité attribuée par l’utilisateur":::     
1. Dans la fenêtre **Ajouter une identité managée par l’utilisateur**, procédez comme suit :
    1. Sélectionnez l’**abonnement Azure** doté de l’identité attribuée par l’utilisateur. 
    1. Sélectionnez l’**identité attribuée par l’utilisateur**. 
    1. Sélectionnez **Ajouter**. 
1. Actualisez la liste sous l’onglet **Attribuée par l’utilisateur** pour voir l’identité attribuée par l’utilisateur ajoutée.

La procédure permettant d’activer une identité pour un domaine Event Grid est similaire.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)
Vous pouvez également utiliser Azure CLI pour affecter une identité affectée par le système à une rubrique ou un domaine personnalisé existant. Actuellement, Azure CLI ne prend pas en charge l’affectation d’une identité affectée par l’utilisateur à une rubrique ou à un domaine.

Utilisez la commande `az eventgrid topic update` avec `--identity` défini sur `systemassigned` pour activer une identité affectée par le système pour une rubrique personnalisée existante. Si vous souhaitez désactiver l’identité, spécifiez la valeur `noidentity`. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

La commande de mise à jour d’un domaine existant est similaire (`az eventgrid domain update`).

---

## <a name="next-steps"></a>Étapes suivantes
Ajouter l’identité à un rôle approprié (par exemple, expéditeur de données Service Bus) sur la destination (par exemple, une file d’attente Service Bus). Pour obtenir des instructions détaillées, consultez [Accorder à une identité managée l’accès à une destination Event Grid](add-identity-roles.md). 
