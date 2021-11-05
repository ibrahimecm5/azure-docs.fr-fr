---
title: Utiliser Azure AD dans Azure Kubernetes Service
description: Découvrez comment utiliser Azure AD dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 10/20/2021
ms.author: miwithro
ms.openlocfilehash: 03d6b2e101103607ec1c699ebdf814d6f41cdb76
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427396"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Intégration d’Azure Active Directory géré par AKS

L’intégration d’Azure AD géré par AKS simplifie le processus d’intégration d’Azure AD. Auparavant, les utilisateurs devaient créer une application cliente et une application serveur, et nécessitaient que le locataire Azure AD accorde des autorisations de lecture d’annuaire. Dans la nouvelle version, le fournisseur de ressources AKS gère les applications cliente et serveur pour vous.

## <a name="azure-ad-authentication-overview"></a>Vue d’ensemble de l’authentification Azure AD

Les administrateurs de cluster peuvent configurer le contrôle d’accès en fonction du rôle Kubernetes (RBAC Kubernetes) en se basant sur l’identité ou l’appartenance à un groupe d’annuaires de l’utilisateur. L’authentification Azure AD est fournie aux clusters AKS à l’aide d’OpenID Connect. OpenID Connect est une couche d’identité basée sur le protocole OAuth 2.0. Pour plus d’informations sur OpenID Connect, voir la [documentation sur Open ID Connect][open-id-connect].

Pour en savoir plus sur le flux de l’intégration d’Azure AD, consultez la [documentation sur les concepts d’intégration d’Azure Active Directory](concepts-identity.md#azure-ad-integration).

## <a name="limitations"></a>Limites 

* L’intégration d’Azure AD géré par AKS ne peut pas être désactivée.
* La modification d’un cluster intégré Azure AD géré par AKS vers un cluster AAD hérité n’est pas prise en charge
* Les clusters sans RBAC Kubernetes activé ne sont pas pris en charge pour l’intégration d’Azure AD géré par AKS

## <a name="prerequisites"></a>Prérequis

* Azure CLI version 2.29.0 ou ultérieure
* Kubectl avec une version minimale [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) ou [kubelogin](https://github.com/Azure/kubelogin)
* Si vous utilisez [helm](https://github.com/helm/helm), version minimale de helm 3.3.

> [!Important]
> Vous devez utiliser Kubectl avec une version minimale 1.18.1 ou kubelogin. La différence entre les versions mineures de Kubernetes et kubectl ne doit pas être supérieure à 1 version. Si vous n’utilisez pas la version appropriée, vous remarquerez des problèmes d’authentification.

Pour installer kubectl et kubelogin, utilisez les commandes suivantes :

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Pour d’autres systèmes d’exploitation, utilisez [ces instructions](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## <a name="before-you-begin"></a>Avant de commencer

Pour votre cluster, vous avez besoin d’un groupe Azure AD. Ce groupe sera inscrit en tant que groupe d’administration sur le cluster pour accorder des autorisations d’administration de cluster. Vous pouvez utiliser un groupe Azure AD existant ou en créer un. Enregistrez l’ID d’objet de votre groupe Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Pour créer un groupe Azure AD pour vos administrateurs de cluster, utilisez la commande suivante :

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Créer un cluster AKS compatible Azure AD

Créez un cluster AKS à l’aide des commandes CLI suivantes.

Créez un groupe de ressources Azure :

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Créer un cluster AKS et activer l’accès administrateur pour votre groupe Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Lorsqu’un cluster Azure AD géré par AKS a bien été créé, vous voyez la section suivante dans le corps de la réponse.
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Une fois le cluster créé, vous pouvez commencer à y accéder.

## <a name="access-an-azure-ad-enabled-cluster"></a>Accéder à un cluster compatible Azure AD

Pour accéder au cluster à l’aide d’un groupe défini par Azure AD, vous avez besoin du rôle intégré d’[utilisateur de cluster Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role).

Récupérez les informations d’identification d’utilisateur permettant d’accéder au cluster :
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Suivez les instructions pour vous connecter.

Utilisez la commande kubectl get nodes pour voir les nœuds figurant dans le cluster :

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configurez le [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](./azure-ad-rbac.md) afin de configurer des groupes de sécurité supplémentaires pour vos clusters.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Résolution des problèmes d’accès avec Azure AD

> [!Important]
> Les étapes décrites ci-dessous contournent l’authentification normale des groupes Azure AD. Effectuez ces étapes uniquement en cas d’urgence.

Si vous restez bloqué sans pouvoir accéder à aucun groupe Azure AD valide ayant accès à votre cluster, vous pouvez obtenir les informations d’identification d’administrateur qui vous permettront d’accéder au cluster directement.

Pour effectuer ces étapes, vous devez avoir accès au rôle intégré [Azure Kubernetes Service Cluster Admin](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role).

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Activer l’intégration de Azure AD géré par AKS sur votre cluster existant

Vous pouvez activer l’intégration d’Azure AD géré par AKS sur votre cluster avec RBAC Kubernetes existant. Veillez à définir votre groupe d’administration pour conserver l’accès à votre cluster.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Lorsqu’un cluster Azure AD géré par AKS a bien été activé, vous voyez la section suivante dans le corps de la réponse

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Téléchargez à nouveau les informations d’identification de l’utilisateur pour accéder à votre cluster en suivant les étapes [ici][access-cluster].

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Mise à niveau vers l’intégration d’Azure AD géré par AKS

Si votre cluster utilise l’intégration d’Azure AD héritée, vous pouvez effectuer la mise à niveau vers l’intégration d’Azure AD géré par AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Lorsqu’un cluster Azure AD géré par AKS a bien été migré, vous voyez la section suivante dans le corps de la réponse.

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Mettez à jour kubeconfig pour accéder au cluster. Suivez les étapes décrites [ici][access-cluster].

## <a name="non-interactive-sign-in-with-kubelogin"></a>Connexion non interactive avec kubelogin

Certains scénarios non interactifs, tels que les pipelines d’intégration continue, ne sont pas possibles avec kubectl. Utilisez [`kubelogin`](https://github.com/Azure/kubelogin) pour accéder au cluster à l’aide d’une connexion non interactive avec le principal de service.

## <a name="disable-local-accounts"></a>Désactiver les comptes locaux

Lors du déploiement d’un cluster AKS, les comptes locaux sont activés par défaut. Même lors de l’activation de l’intégration RBAC ou Azure Active Directory, l’accès à `--admin` existe toujours, essentiellement comme une option d’accès par porte dérobée ne pouvant pas être auditée. Dans cette optique, AKS offre aux utilisateurs la possibilité de désactiver les comptes locaux via un indicateur, `disable-local-accounts`. Un champ `properties.disableLocalAccounts` a également été ajouté à l’API de cluster géré pour indiquer si la fonctionnalité a été activée sur le cluster.

> [!NOTE]
> Sur les clusters sur lesquels l’intégration Azure AD est activée, les utilisateurs qui appartiennent à un groupe spécifié par `aad-admin-group-object-ids` disposeront toujours d’un accès via des informations d’identification non-administrateur. Sur les clusters sans intégration Azure AD activée et pour lesquels `properties.disableLocalAccounts` est définie sur true, l’obtention d’informations d’identification échouera à la fois pour l’utilisateur et l’administrateur.

> [!NOTE]
> Après la désactivation de comptes locaux sur un cluster AKS existant où des utilisateurs ont peut-être utilisé un ou plusieurs comptes locaux, l’administrateur doit [effectuer une rotation des certificats de cluster](certificate-rotation.md#rotate-your-cluster-certificates) afin de révoquer les certificats auxquels ces utilisateurs peuvent avoir accès.  S’il s’agit d’un nouveau cluster, aucune action n’est requise.

### <a name="create-a-new-cluster-without-local-accounts"></a>Créer un nouveau cluster sans comptes locaux

Pour créer un nouveau cluster AKS sans comptes locaux, utilisez la commande [az aks create][az-aks-create] avec l’indicateur `disable-local-accounts` :

```azurecli-interactive
az aks create -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --disable-local-accounts
```

Dans la sortie, assurez-vous que les comptes locaux ont été désactivés en vérifiant que le champ `properties.disableLocalAccounts` est défini sur true :

```output
"properties": {
    ...
    "disableLocalAccounts": true,
    ...
}
```

La tentative d’obtenir des informations d’identification de l’administrateur échouera avec un message d’erreur indiquant que la fonctionnalité empêche l’accès :

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Operation failed with status: 'Bad Request'. Details: Getting static credential is not allowed because this cluster is set to disable local accounts.
```

### <a name="disable-local-accounts-on-an-existing-cluster"></a>Désactiver les comptes locaux sur un cluster existant

Pour désactiver les comptes locaux sur un cluster AKS existant, utilisez la commande [az aks update][az-aks-update] avec l’indicateur `disable-local-accounts` :

```azurecli-interactive
az aks update -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --disable-local-accounts
```

Dans la sortie, assurez-vous que les comptes locaux ont été désactivés en vérifiant que le champ `properties.disableLocalAccounts` est défini sur true :

```output
"properties": {
    ...
    "disableLocalAccounts": true,
    ...
}
```

La tentative d’obtenir des informations d’identification de l’administrateur échouera avec un message d’erreur indiquant que la fonctionnalité empêche l’accès :

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Operation failed with status: 'Bad Request'. Details: Getting static credential is not allowed because this cluster is set to disable local accounts.
```

### <a name="re-enable-local-accounts-on-an-existing-cluster"></a>Réactiver les comptes locaux sur un cluster existant

AKS offre également la possibilité de réactiver les comptes locaux sur un cluster existant avec l’indicateur `enable-local` :

```azurecli-interactive
az aks update -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --enable-local
```

Dans la sortie, assurez-vous que les comptes locaux ont été réactivés en vérifiant que le champ `properties.disableLocalAccounts` est défini sur false :

```output
"properties": {
    ...
    "disableLocalAccounts": false,
    ...
}
```

La tentative d’accès aux informations d’identification d’administrateur réussira :

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Merged "<cluster-name>-admin" as current context in C:\Users\<username>\.kube\config
```

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Utiliser l’accès conditionnel avec Azure AD et AKS

Lorsque vous intégrez Azure AD à votre cluster AKS, vous pouvez également utiliser l’[accès conditionnel][aad-conditional-access] pour contrôler l’accès à votre cluster.

> [!NOTE]
> L’accès conditionnel Azure AD est une fonctionnalité Azure AD Premium.

Pour créer un exemple de stratégie d’accès conditionnel à utiliser avec AKS, procédez comme suit :

1. En haut du portail Azure, recherchez et sélectionnez Azure Active Directory.
1. Dans le menu Azure Active Directory sur le côté gauche, sélectionnez *Applications d’entreprise*.
1. Dans le menu Applications d’entreprise sur le côté gauche, sélectionnez *Accès conditionnel*.
1. Dans le menu Accès conditionnel sur le côté gauche, sélectionnez *Stratégies* puis *Nouvelle stratégie*.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Ajout d’une stratégie d’accès conditionnel":::
1. Entrez le nom de la stratégie, par exemple *aks-policy*.
1. Sélectionnez *Utilisateurs et groupes*, puis sous *Inclure* sélectionnez *Sélectionner des utilisateurs et des groupes*. Choisissez les utilisateurs et les groupes auxquels vous souhaitez appliquer la stratégie. Pour cet exemple, choisissez le groupe Azure AD qui dispose déjà d’un accès d’administration à votre cluster.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Sélection d’utilisateurs ou de groupes pour appliquer la stratégie d’accès conditionnel":::
1. Sélectionnez *Applications ou actions cloud*, puis sous *Inclure* sélectionnez *Sélectionner des applications*. Recherchez *Azure Kubernetes Service* et sélectionnez *Serveur AAD Azure Kubernetes Service*.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Sélection du serveur AD Azure Kubernetes Service pour appliquer la stratégie d’accès conditionnel":::
1. Sous *Contrôles d’accès*, sélectionnez *Accorder*. Sélectionnez *Accorder l’accès* puis *Exiger que l’appareil soit marqué comme conforme*.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Sélection de l’option Autoriser uniquement les appareils conformes pour la stratégie d’accès conditionnel":::
1. Sous *Activer une stratégie*, sélectionnez *Activé*, puis *Créer*.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Activation de la stratégie d’accès conditionnel":::

Récupérez les informations d’identification d’utilisateur permettant d’accéder au cluster, par exemple :

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Suivez les instructions pour vous connecter.

Exécutez la commande `kubectl get nodes` pour afficher les nœuds du cluster :

```azurecli-interactive
kubectl get nodes
```

Suivez les instructions pour vous reconnecter. Notez qu’un message d’erreur s’affiche, indiquant que vous êtes correctement connecté, mais que votre administrateur requiert que l’appareil demandant l’accès soit géré par votre instance Azure AD pour accéder à la ressource.

Dans le portail Azure, accédez à Azure Active Directory, sélectionnez *Applications d’entreprise*, puis sous *Activité*, sélectionnez *Connexions*. Notez une entrée en haut avec l’*état* *Échec* et l’*accès conditionnel* *Réussite*. Sélectionnez l’entrée, puis *Accès conditionnel* dans *Détails*. Notez que votre stratégie d’accès conditionnel est indiquée.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Entrée de connexion ayant échoué en raison d’une stratégie d’accès conditionnel":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Configurer un accès juste-à-temps au cluster avec Azure AD et AKS

Une autre option pour le contrôle d’accès aux clusters consiste à utiliser Privileged Identity Management (PIM) pour les requêtes juste-à-temps.

>[!NOTE]
> PIM est une fonctionnalité Azure AD Premium nécessitant une référence SKU P2 Premium. Pour plus d’informations sur les références SKU Azure AD, consultez le [Guide de tarification][aad-pricing].

Pour intégrer des demandes d’accès juste-à-temps à un cluster AKS à l’aide de l’intégration d’Azure AD gérée par AKS, procédez comme suit :

1. En haut du portail Azure, recherchez et sélectionnez Azure Active Directory.
1. Prenez note de l’ID de locataire, référencé pour le reste de ces instructions, comme `<tenant-id>`. :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="Dans un navigateur Web, l’écran du Portail Azure pour Azure Active Directory s’affiche avec l’ID du locataire mis en surbrillance.":::
1. Dans le menu de Azure Active Directory sur le côté gauche, sous *Gérer* sélectionner *Groupes*, puis *Nouveau groupe*.
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Affiche l’écran des groupes Active Directory du Portail Azure avec l’option « Nouveau groupe » en surbrillance.":::
1. Assurez-vous qu’un Type de groupe de *Sécurité* est sélectionné et entrez un nom de groupe, comme *myJITGroup*. Sous *Rôles Azure AD pouvant être attribués à ce groupe (préversion)* , sélectionnez *Oui*. Pour finir, sélectionnez *Créer*.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Affiche l’écran de création du groupe dans le Portail Azure.":::
1. Vous serez redirigé vers la page *Groupes*. Sélectionnez le groupe que vous venez de créer et prenez note de l’ID d’objet, référencé pour le reste de ces instructions sous la forme de `<object-id>`.
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Affiche l’écran de Portail Azure pour le groupe que vous venez de créer, en mettant en surbrillance l’ID de l’objet":::
1. Déployez un cluster AKS avec l’intégration de Azure AD gérée par AKS à l’aide des valeurs `<tenant-id>` et `<object-id>` :
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. De retour dans le Portail Azure, dans le menu de *Activité* sur le côté gauche, sélectionnez *Accès privilégié (préversion)* et cliquez sur *Activer l’accès privilégié*.
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="La page Accès privilégié du Portail Azure (préversion) s’affiche, avec « Activer l’accès privilégié » en surbrillance":::
1. Sélectionnez *Ajouter des affectations* pour commencer à accorder l’accès.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="L’écran Accès privilégié du Portail Azure (préversion) après l’activation s’affiche. L’option « Ajouter des affectations » est mis en surbrillance.":::
1. Sélectionnez un rôle de *membre*, puis choisissez les utilisateurs et les groupes auxquels vous souhaitez accorder l’accès au cluster. Un administrateur de groupe peut modifier ces affectations à tout moment. Quand vous êtes prêt à continuer, choisissez *Suivant*.
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="L’écran Ajouter des affectations > Appartenance du Portail Azure s’affiche, avec un utilisateur sélectionné pour être ajouté en tant que membre (exemple). L’option « Suivant » est mis en surbrillance.":::
1. Choisissez un type d’affectation *Actif*, la durée souhaitée et fournissez une justification. Lorsque vous êtes prêt à continuer, sélectionnez *Attribuer*. Pour plus d’informations sur les types d’affectations, consultez [Attribuer l’éligibilité à l’appartenance ou la propriété d’un groupe d’accès privilégié (préversion) dans Privileged Identity Management][aad-assignments].
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="L’écran Ajouter des affectations > Paramètres du Portail Azure s’affiche. Un type d’affectation « Actif » est sélectionné et une justification a été donnée (exemple). L’option « Attribuer » est mis en surbrillance.":::

Une fois les attributions effectuées, vérifiez que l’accès juste-à-temps fonctionne en accédant au cluster. Par exemple :

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Suivez les étapes pour vous connecter.

Exécutez la commande `kubectl get nodes` pour afficher les nœuds du cluster :

```azurecli-interactive
kubectl get nodes
```

Notez la configuration requise pour l’authentification et suivez les étapes pour vous authentifier. Le résultat devrait ressembler à ce qui suit :

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```
### <a name="apply-just-in-time-access-at-the-namespace-level"></a>Appliquer l’accès juste-à-temps au niveau de l’espace de noms

1. Intégrez votre cluster AKS au [contrôle d’accès en fonction du rôle (RBAC) Azure](manage-azure-rbac.md).
2. Associez le groupe que vous voulez intégrer à l’accès juste-à-temps à un espace de noms du cluster par le biais de l’attribution de rôle.

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Reader" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```
3. Associez le groupe que vous venez de configurer au niveau de l’espace de noms avec PIM pour terminer la configuration.

### <a name="troubleshooting"></a>Dépannage

Si `kubectl get nodes` renvoie une erreur similaire à ce qui suit :

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Assurez-vous que l’administrateur du groupe de sécurité a donné à votre compte une attribution *Active*.

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur l’[intégration d’Azure RBAC pour l’autorisation Kubernetes][azure-rbac-integration].
* Apprenez-en davantage sur l’[intégration d’Azure AD avec Kubernetes RBAC][azure-ad-rbac].
* Utilisez [kubelogin](https://github.com/Azure/kubelogin) pour accéder aux fonctionnalités de l’authentification Azure non disponibles dans kubectl.
* Apprenez-en davantage sur les [concepts d’identité AKS et Kubernetes][aks-concepts-identity].
* Utilisez des [modèles ARM (Azure Resource Manager)][aks-arm-template] pour créer des clusters avec Azure AD géré par AKS.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-update]: /cli/azure/aks#az_aks_update
