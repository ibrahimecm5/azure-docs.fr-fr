---
title: Clé gérée par le client dans Azure Monitor
description: Informations et étapes relatives à la configuration d’une clé gérée par le client pour chiffrer des données dans vos espaces de travail Log Analytics à l’aide d’une clé Azure Key Vault.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/29/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2378daa38d1fba86bbb9194b7993cee9a862ad4c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461966"
---
# <a name="azure-monitor-customer-managed-key"></a>Clé gérée par le client dans Azure Monitor 

Les données dans Azure Monitor sont chiffrées avec des clés gérées par Microsoft. Vous pouvez utiliser votre propre clé de chiffrement pour protéger les données et les requêtes enregistrées dans vos espaces de travail. Lorsque vous spécifiez une clé gérée par le client, cette clé est utilisée pour protéger et contrôler l’accès à vos données. Et une fois qu’elle est configurée, toutes les données envoyées à vos espaces de travail sont chiffrées à l’aide de votre clé Azure Key Vault. Les clés gérées par le client offrent davantage de flexibilité pour gérer les contrôles d’accès.

Nous vous recommandons de passer en revue les [Limitations et contraintes](#limitationsandconstraints) ci-dessous avant de procéder à la configuration.

## <a name="customer-managed-key-overview"></a>Vue d’ensemble des clés gérées par le client

Le [chiffrement au repos](../../security/fundamentals/encryption-atrest.md) est une exigence de sécurité et de confidentialité courante dans les organisations. Si vous pouvez laisser Azure gérer complètement le chiffrement au repos, plusieurs options vous permettent de gérer le chiffrement et des clés de chiffrement.

Azure Monitor veille à ce que toutes les données et requêtes enregistrées soient chiffrées au repos à l’aide de clés gérées par Microsoft (MMK). Azure Monitor fournit également une option de chiffrement à l’aide de votre propre clé qui est stockée dans votre coffre [Azure Key Vault](../../key-vault/general/overview.md), ce qui vous permet de révoquer à tout moment l’accès à vos données. L’utilisation du chiffrement par Azure Monitor est identique à celle du [chiffrement par Stockage Azure](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption).

La clé gérée par le client est fournie sur des [clusters dédiés](./logs-dedicated-clusters.md) offrant un niveau de protection et un contrôle plus élevés. Les données ingérées dans les clusters dédiés sont chiffrées deux fois : une fois au niveau du service à l’aide de clés gérées par Microsoft ou de clés gérées par le client, et une fois au niveau de l’infrastructure à l’aide de deux algorithmes de chiffrement et de deux clés différents. Le [double chiffrement](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) permet d’éviter un scénario impliquant une possible compromission d’un algorithme ou d’une clé de chiffrement. Dans ce cas, la couche de chiffrement supplémentaire continue de protéger vos données. Le cluster dédié vous permet également de protéger vos données à l’aide du contrôle [Lockbox](#customer-lockbox-preview).

Les données ingérées au cours des 14 derniers jours et les données récemment utilisées dans les requêtes sont également conservées dans le cache à chaud (SSD) pour l’efficacité des requêtes, et chiffrées avec les clés Microsoft, quelle que soit la configuration des clés gérées par le client. Votre contrôle d’accès aux données SSD s’applique et adhère à la [révocation de clé](#key-revocation).

Le [modèle de tarification](./logs-dedicated-clusters.md#cluster-pricing-model) des Clusters dédiés Log Analytics requiert un niveau d’engagement à partir de 500 Go/jour et peut avoir des valeurs de 500, 1 000, 2 000 ou 5 000 Go/jour.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Fonctionnement de la clé gérée par le client dans Azure Monitor

Azure Monitor utilise l’identité pour accorder l’accès à votre Azure Key Vault. L’identité du cluster Log Analytics est prise en charge au niveau du cluster. Pour permettre la protection de clé gérée par le client sur plusieurs espaces de travail, une nouvelle ressource *cluster* Log Analytics s’exécute en tant que connexion d’identité intermédiaire entre votre coffre de clés et vos espaces de travail Log Analytics. Le stockage du cluster utilise l’identité managée associée\' à la ressource *cluster* pour s’authentifier auprès de votre Azure Key Vault via Azure Active Directory. 

Après la configuration d’une clé gérée par le client, toutes les nouvelles données ingérées dans les espaces de travail liés à votre cluster dédié sont chiffrées à l’aide de votre clé. Vous pouvez dissocier les espaces de travail du cluster à tout moment. Les nouvelles données sont ensuite ingérées dans un stockage Log Analytics et chiffrées avec une clé Microsoft. Vous pourrez interroger vos données, nouvelles et anciennes, sans la moindre difficulté.

> [!IMPORTANT]
> La capacité de clé gérée par le client est régionale. Vos Azure Key Vault, le cluster et les espaces de travail Log Analytics liés doivent se trouver dans la même région, mais ils peuvent être dans des abonnements différents.

![Vue d’ensemble des clés gérées par le client](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Ressource *cluster* Log Analytics ayant une identité gérée avec des autorisations pour Key Vault. L’identité est propagée vers le stockage de cluster Log Analytics dédié sous-jacent.
3. Cluster Log Analytics dédié.
4. Espaces de travail liés à la ressource *cluster* 

### <a name="encryption-keys-operation"></a>Opération de clés de chiffrement

Il existe trois types de clés impliquées dans le chiffrement des données de Stockage :

- **KEK** : clé de chiffrement de clé (votre clé gérée par le client)
- **AEK** : clé de chiffrement de compte
- **DEK** : clé de chiffrement de données

Les règles suivantes s’appliquent :

- Les comptes de stockage de cluster Log Analytics génèrent une clé de chiffrement unique pour chaque compte de stockage, appelée clé de chiffrement de compte (AEK, Account Encryption Key).
- La clé AEK est utilisée pour dériver les clés DEK, clés utilisées pour chiffrer chaque bloc de données écrites sur le disque.
- Lorsque vous configurez votre clé dans Key Vault et que vous la référencez dans le cluster, Stockage Azure envoie des requêtes à votre Azure Key Vault pour envelopper et désenvelopper l’AEK afin d’effectuer des opérations de chiffrement et de déchiffrement de données.
- Votre KEK ne quitte jamais votre Key Vault.
- Stockage Azure utilise l’identité managée associée à la ressource *cluster* pour s’authentifier et accéder à Azure Key Vault par le biais d’Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Étapes de configuration de clé gérée par le client

1. Création du coffre de clés Azure et stockage de la clé
1. Création du cluster
1. Octroi d’autorisations d’accès à votre coffre de clés
1. Mise à jour du cluster avec les détails de l’identificateur de clé
1. Liaison d’espaces de travail Log Analytics

La configuration de clé gérée par le client n’est pas actuellement prise en charge dans le portail Azure et l’approvisionnement peut être effectué par le biais de requêtes [PowerShell](/powershell/module/az.operationalinsights/), [CLI](/cli/azure/monitor/log-analytics) ou [REST](/rest/api/loganalytics/).

## <a name="storing-encryption-key-kek"></a>Stockage de la clé de chiffrement (KEK)

Créez ou utilisez une instance Azure Key Vault existante dans la région prévue pour le cluster, puis générez ou importez une clé à utiliser pour le chiffrement des journaux. Le coffre de clés Azure doit être configuré comme récupérable pour protéger votre clé et l’accès à vos données Azure Monitor. Vous pouvez vérifier cette configuration dans les propriétés de votre coffre de clés : les fonctionnalités de *suppression réversible* et de *protection contre la suppression définitive* doivent être activées.

![Paramètres de suppression réversible et de protection contre la suppression définitive](media/customer-managed-keys/soft-purge-protection.png)

Ces paramètres peuvent être mis à jour dans Key Vault par le biais de l’interface CLI et de PowerShell :

- [Suppression réversible](../../key-vault/general/soft-delete-overview.md)
- La [protection contre la suppression définitive](../../key-vault/general/soft-delete-overview.md#purge-protection) protège contre la suppression forcée du secret ou du coffre, même après activation de la suppression réversible.

## <a name="create-cluster"></a>Créer un cluster

Les clusters utilisent l’identité managée pour le chiffrement des données avec votre coffre de clés. Affectez la valeur `SystemAssigned` à la propriété d’identité `type` lors de la création de votre cluster pour autoriser l’accès à votre coffre de clés pour les opérations d’encapsulation et de désencapsulation. 
  
  Paramètres d’identité dans le cluster pour l’identité managée affectée par le système
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

Suivez la procédure illustrée dans l’article sur les [Clusters dédiés](./logs-dedicated-clusters.md#create-a-dedicated-cluster). 

## <a name="grant-key-vault-permissions"></a>Octroi d’autorisations d’accès au coffre de clés

Créez une stratégie d’accès dans Key Vault pour accorder des autorisations à votre cluster. Ces autorisations sont utilisées par le stockage Azure Monitor sous-jacent. Ouvrez votre coffre de clés dans le portail Azure, puis cliquez sur *Stratégies d’accès*, puis sur *+ Ajouter une stratégie d’accès* pour créer une stratégie avec les paramètres ci-après :

- Autorisations de clé : sélectionnez *Obtenir*, *Inclure la clé* et *Ne pas inclure la clé*.
- Sélectionner le principal : selon le type d’identité utilisé dans le cluster (identité managée par le système ou par l’utilisateur), entrez soit le nom du cluster, soit l’ID du principal du cluster pour l’identité managée attribuée par le système ou le nom de l’identité managée attribuée par l’utilisateur.

![Octroi d’autorisations d’accès au coffre de clés](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

L’autorisation *Obtenir* est nécessaire pour vérifier que votre coffre de clés est configuré comme récupérable pour protéger votre clé et l’accès à vos données Azure Monitor.

## <a name="update-cluster-with-key-identifier-details"></a>Mettre à jour le cluster avec les détails de l’identificateur de clé

Toutes les opérations sur le cluster requièrent l’autorisation de l’action `Microsoft.OperationalInsights/clusters/write`. Cette autorisation peut être accordée via le propriétaire ou le contributeur qui contient l’action `*/write` ou via le rôle Contributeur Log Analytics qui contient l’action `Microsoft.OperationalInsights/*`.

Cette étape met à jour le stockage Azure Monitor avec la clé et la version à utiliser pour le chiffrement des données. Une fois mise à jour, votre nouvelle clé est utilisée pour envelopper et désenvelopper la clé de stockage (AEK).

>[!IMPORTANT]
>- La rotation des clés peut être automatique ou nécessiter une mise à jour des clés explicite. Consultez [Rotation des clés](#key-rotation) pour déterminer l’approche adaptée à vos besoins avant de mettre à jour les détails relatifs à l’identificateur de clé dans le cluster.
>- La mise à jour du cluster ne doit pas inclure les détails relatifs à l’identité et à l’identificateur de clé dans la même opération. S’il vous faut les mettre à jour, cette mise à jour doit faire l’objet de deux opérations consécutives.

![Octroi d’autorisations d’accès au coffre de clés](media/customer-managed-keys/key-identifier-8bit.png)

Mettez à jour la propriété KeyVaultProperties du cluster avec les détails de l’identificateur de clé.

L’opération est asynchrone et peut prendre du temps.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster update --no-wait --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"

# Wait for job completion when `--no-wait` was used
$clusterResourceId = az monitor log-analytics cluster list --resource-group "resource-group-name" --query "[?contains(name, "cluster-name")].[id]" --output tsv
az resource wait --created --ids $clusterResourceId --include-response-body true

```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Select-AzSubscription "cluster-subscription-id"

Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version" -AsJob

# Check when the job is done when `-AsJob` was used
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2021-06-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "keyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 500
  }
}
```

**Réponse**

La propagation de la clé prend quelques instants. Vous pouvez vérifier l’état de la mise à jour en envoyant une requête GET sur le cluster et en examinant les propriétés *KeyVaultProperties*. Les détails de la clé récemment mise à jour doivent être retournés dans la réponse.

Une réponse à la requête GET doit ressembler à ceci lorsque la mise à jour de la clé est terminée : 202 (Accepté) et en-tête
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
  },
  "sku": {
    "name": "capacityreservation",
    "capacity": 500
  },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "keyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterId": "cluster-id",
    "billingType": "Cluster",
    "lastModifiedDate": "last-modified-date",
    "createdDate": "created-date",
    "isDoubleEncryptionEnabled": false,
    "isAvailabilityZonesEnabled": false,
    "capacityReservationProperties": {
      "lastSkuUpdate": "last-sku-modified-date",
      "minCapacity": 500
    }
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "cluster-region"
}
```

---

## <a name="link-workspace-to-cluster"></a>Lier un espace de travail à un cluster

> [!IMPORTANT]
> Cette étape ne doit être accomplie qu’une fois l’approvisionnement du cluster Log Analytics terminé. Si vous liez des espaces de travail et ingérez des données avant cet approvisionnement, les données ingérées sont définitivement supprimées.

Pour effectuer cette opération, vous devez disposer des autorisations « écrire » sur votre espace de travail et le cluster, ce qui implique notamment `Microsoft.OperationalInsights/workspaces/write` et `Microsoft.OperationalInsights/clusters/write`.

Suivez la procédure illustrée dans l’article sur les [Clusters dédiés](./logs-dedicated-clusters.md#link-a-workspace-to-a-cluster).

## <a name="key-revocation"></a>Révocation de la clé

> [!IMPORTANT]
> - La méthode recommandée pour révoquer l’accès à vos données consiste à désactiver votre clé ou à supprimer la stratégie d’accès dans votre coffre de clés.
> - La définition de `identity` `type` du cluster sur `None` révoque également l’accès à vos données, mais cette approche n’est pas recommandée, car vous ne pouvez pas l’annuler sans contacter le support.

Le stockage de cluster respecte toujours les modifications des autorisations de clé en maximum une heure. Il devient alors indisponible. Toutes les nouvelles données ingérées dans les espaces de travail liés à votre cluster sont définitivement supprimées. Les données deviennent donc inaccessibles sur ces espaces de travail et les requêtes échouent. Les données précédemment ingérées restent dans le stockage tant que votre cluster et vos espaces de travail ne sont pas supprimés. Les données inaccessibles sont régies par la stratégie de conservation des données et sont vidées à la fin de la durée de conservation. Les données ingérées au cours des 14 derniers jours et les données récemment utilisées dans les requêtes sont également conservées dans un cache à chaud (SSD) pour l’efficacité des requêtes. Les données sur SSD sont supprimées lors d’une opération de révocation de clé et deviennent inaccessibles. Le stockage du cluster tente de désencapsuler régulièrement le chiffrement avec votre coffre de clés, et une fois que vous avez rétabli la révocation la désencapsulation réussit, les données de disque SSD sont rechargées à partir du stockage, et l’ingestion des données et la requête sont relancées dans les 30 minutes.

## <a name="key-rotation"></a>Rotation des clés

La rotation des clés propose deux modes : 
- Rotation automatique : quand vous mettez à jour votre cluster avec ```"keyVaultProperties"``` mais omettez la propriété ```"keyVersion"```, ou que vous lui affectez la valeur ```""```, le stockage utilise automatiquement les versions les plus récentes.
- Mise à jour de version de clé explicite : lorsque vous mettez à jour votre cluster et fournissez une version de clé dans la propriété ```"keyVersion"```, toutes les nouvelles versions de clé requièrent une mise à jour ```"keyVaultProperties"``` explicite dans le cluster. Consultez [Mettre à jour le cluster avec les détails d’identificateur de clé](#update-cluster-with-key-identifier-details) pour plus de détails. Si vous générez la nouvelle version de la clé dans Key Vault mais ne la mettez pas à jour dans le cluster, le stockage de cluster Log Analytics continue d’utiliser votre clé précédente. Si vous désactivez ou supprimez votre ancienne clé avant de mettre à jour la nouvelle dans le cluster, vous passez à l’état [révocation de clé](#key-revocation).

Toutes vos données restent accessibles après l’opération de rotation de clé, car les données sont toujours chiffrées avec la clé de chiffrement de compte (AEK, Account Encryption Key), tandis que celle-ci est désormais chiffrée avec votre nouvelle version de clé de chiffrement de clé (KEK, Key Encryption Key) dans Key Vault.

## <a name="customer-managed-key-for-saved-queries-and-log-alerts"></a>Clé gérée par le client pour les requêtes enregistrées et les alertes de journal

Le langage de requête utilisé dans Log Analytics est expressif et peut contenir des informations sensibles dans les commentaires que vous ajoutez aux requêtes ou dans la syntaxe de requête. Certaines organisations requièrent que ces informations soient protégées dans le cadre de la stratégie de clé gérée par le client, et vous devez sauvegarder vos requêtes en les chiffrant avec votre clé. Azure Monitor vous permet de stocker des requêtes de *recherches enregistrées* et d’*alertes de journal* chiffrées avec votre clé dans votre propre compte de stockage lorsque vous êtes connecté à votre espace de travail. 

> [!NOTE]
> Les requêtes Log Analytics peuvent être enregistrées dans différents magasins en fonction du scénario utilisé. Les requêtes restent chiffrées avec la clé Microsoft (MMK) dans les scénarios suivants, quelle que soit la configuration de clé gérée par le client : Classeurs dans Azure Monitor, tableaux de bord Azure, application logique Azure, Azure Notebooks et Automation Runbooks.

Lorsque vous apportez votre propre stockage (BYOS) et que vous le liez à votre espace de travail, le service charge les requêtes de *recherches enregistrées* et d’*alertes de journal* dans votre compte de stockage. Cela signifie que vous contrôlez le compte de stockage et la [stratégie de chiffrement au repos](../../storage/common/customer-managed-keys-overview.md) à l’aide de la même clé que celle utilisée pour chiffrer les données dans le cluster Log Analytics ou d’une clé différente. Toutefois, vous êtes responsable des coûts associés à ce compte de stockage. 

**Considérations à prendre en compte avant de définir la clé gérée par le client pour les requêtes**
* Vous devez disposer d’autorisations d’écriture pour votre espace de travail et votre compte de stockage.
* Veillez à créer votre compte de stockage dans la même région que celle où se trouve votre espace de travail Log Analytics.
* Les *recherches enregistrées* en stockage sont considérées comme des artefacts de service et leur format peut changer.
* Les *recherches enregistrées* existantes sont supprimées de votre espace de travail. Copiez toutes les *recherches enregistrées* dont vous avez besoin avant la configuration. Vous pouvez afficher vos *recherches enregistrées* à l’aide de [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch).
* L’historique des requêtes n’est pas pris en charge et vous ne pouvez pas voir les requêtes que vous avez exécutées.
* Vous pouvez lier un seul compte de stockage à l’espace de travail pour l’enregistrement des requêtes, mais il peut être utilisé pour les requêtes de *recherches enregistrées* et d’*alertes de journal*.
* L’action d’épingler au tableau de bord n’est pas prise en charge.
* Les alertes de journal déclenchées ne contiennent pas de résultats de recherche ou de requête d’alerte. Vous pouvez utiliser les [dimensions d’alerte](../alerts/alerts-unified-log.md#split-by-alert-dimensions) pour obtenir du contexte dans les alertes déclenchées.

**Configurer BYOS pour les requêtes de recherches enregistrées**

Liez un compte de stockage à *interroger* à votre espace de travail. Les requêtes de *recherches enregistrées* sont enregistrées dans votre compte de stockage. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'

az account set --subscription "workspace-subscription-id"

az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"

Select-AzSubscription "workspace-subscription-id"

New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2021-06-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Après la configuration, toute nouvelle requête *de recherche enregistrée* sera sauvegardée dans votre stockage.

**Configurer BYOS pour les requêtes d’alertes de journal**

Liez un compte de stockage pour les *alertes* à votre espace de travail. Les requêtes d’*alertes de journal* sont enregistrées dans votre compte de stockage. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'

az account set --subscription "workspace-subscription-id"

az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"

Select-AzSubscription "workspace-subscription-id"

New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2021-06-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Après la configuration, toute nouvelle requête d’alerte sera sauvegardée dans votre stockage.

## <a name="customer-lockbox-preview"></a>Customer Lockbox (préversion)

Lockbox vous permet d’approuver ou de rejeter la demande d’un ingénieur Microsoft d’accéder à vos données lors d’une demande de support.

Dans Azure Monitor, vous disposez de ce contrôle sur les données des espaces de travail liés à votre cluster Log Analytics dédié. Le contrôle Lockbox s’applique aux données stockées dans un cluster Log Analytics dédié dans lequel elles sont maintenues isolées dans les comptes de stockage du cluster configurés dans votre abonnement protégé par Lockbox.  

En savoir plus sur [Customer Lockbox pour Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Opérations de clés gérées par le client

Une clé gérée par le client est fournie sur un cluster dédié et ces opérations sont mentionnées dans l’[article de cluster dédié](./logs-dedicated-clusters.md#change-cluster-properties)

- Obtenir tous les clusters dans un groupe de ressources  
- Obtenir tous les clusters dans un abonnement
- Mettre à jour la *réservation de capacité* dans un cluster
- Mettre à jour la propriété *billingType* dans le cluster
- Dissocier un espace de travail d’un cluster
- Supprimer un cluster

## <a name="limitations-and-constraints"></a>Limitations et contraintes

- Le nombre maximal de clusters par région et abonnement est de 2

- Le nombre maximal d’espaces de travail qui peuvent être liés à un cluster est de 1 000

- Vous pouvez lier un espace de travail à votre cluster, puis le dissocier. Le nombre de liaisons d’espaces de travail sur un espace de travail particulier est limité à 2 sur une période de 30 jours.

- Le chiffrement par clé gérée par le client s’applique aux données nouvellement ingérées après l’heure de la configuration. Les données ingérées avant la configuration restent chiffrées avec une clé Microsoft. Vous pouvez interroger les données ingérées avant et après la configuration la clé gérée par le client en toute simplicité.

- Le coffre de clés Azure doit être configuré comme récupérable. Les propriétés ci-après, qui ne sont pas activées par défaut, doivent être configurées à l’aide de l’interface CLI ou de PowerShell :<br>
  - [Suppression réversible](../../key-vault/general/soft-delete-overview.md)
  - La [protection contre le vidage](../../key-vault/general/soft-delete-overview.md#purge-protection) doit être activée pour bénéficier d’une protection contre la suppression forcée du secret ou du coffre, même après activation de la suppression réversible.

- Le déplacement d’un cluster vers un autre groupe de ressources ou abonnement n’est pas pris en charge.

- Votre coffre Azure Key Vault, le cluster et les espaces de travail doivent se trouver dans la même région et dans le même locataire Azure Active Directory (Azure AD), mais peuvent être dans des abonnements différents.

- La mise à jour du cluster ne doit pas inclure les détails relatifs à l’identité et à l’identificateur de clé dans la même opération. S’il vous faut les mettre à jour, cette mise à jour doit faire l’objet de deux opérations consécutives.

- Actuellement, Lockbox n’est pas disponible en Chine. 

- Le [double chiffrement](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) est automatiquement configuré pour les clusters créés depuis octobre 2020 dans les régions prises en charge. Pour vérifier si votre cluster est configuré pour le chiffrement double, envoyez une requête GET sur le cluster et observez la valeur de `isDoubleEncryptionEnabled`. Une valeur `true` correspond aux clusters pour lesquels le chiffrement double est activé. 
  - Si vous créez un cluster et recevez une erreur « region-name ne prend pas en charge le chiffrement double pour les clusters », vous pouvez toujours créer le cluster sans le double chiffrement en ajoutant `"properties": {"isDoubleEncryptionEnabled": false}` dans le corps de la demande REST.
  - Le paramètre de double chiffrement ne peut pas être modifié une fois le cluster créé.

  - La définition de `identity` `type` du cluster sur `None` révoque également l’accès à vos données, mais cette approche n’est pas recommandée, car vous ne pouvez pas l’annuler sans contacter le support. La méthode recommandée pour révoquer l’accès à vos données est la [révocation de clé](#key-revocation).

  - Vous ne pouvez pas utiliser une clé gérée par le client avec une identité managée affectée par l’utilisateur si votre coffre de clés se trouve dans Azure Private Link (réseau virtuel). Dans ce scénario, vous pouvez utiliser une identité managée affectée par le système.

## <a name="troubleshooting"></a>Dépannage

- Comportement avec disponibilité du Key Vault
  - En temps normal, le Stockage met en cache la clé AEK pendant de courtes périodes et revient régulièrement dans Key Vault pour la désencapsulation.
    
  - Erreurs de connexion du Coffre de clés : Le stockage gère les erreurs temporaires (délais d’attente, échecs de connexion, problèmes DNS) en autorisant les clés à rester en cache pendant la durée du problème de disponibilité, ce qui compense les problèmes de blocages et de disponibilité. Les fonctionnalités de requête et d’ingestion se poursuivent sans interruption.
    
- Taux d’accès au Key Vault : la fréquence à laquelle le stockage Azure Monitor accède au Key Vault pour les opérations d’encapsulation (wrap) et de désencapsulation (unwrap) est comprise entre 6 et 60 secondes.

- Si vous mettez à jour votre cluster alors que le cluster est en cours d’approvisionnement ou de mise à jour, la mise à jour échoue.

- Si vous recevez une erreur de conflit lors de la création d’un cluster, il est possible que vous ayez supprimé votre cluster au cours des derniers 14 jours et qu’il se trouve dans une période de suppression réversible. Le nom du cluster reste réservé pendant la période de suppression réversible et vous ne pouvez pas l’utiliser pour un autre cluster. Le nom est libéré après la période de suppression réversible, lorsque le cluster est définitivement supprimé.

- La liaison d’un espace de travail à un cluster échoue s’il est lié à un autre cluster.

- Si vous créez un cluster et spécifiez la propriété KeyVaultProperties immédiatement, l’opération peut échouer, car la stratégie d’accès ne peut pas être définie tant que l’identité système n’a pas été attribuée au cluster.

- Si vous mettez à jour le cluster existant avec KeyVaultProperties et que la stratégie d’accès de clé « Obtenir » est manquante dans Key Vault, l’opération échoue.

- Si vous ne parvenez pas à déployer votre cluster, vérifiez que votre Azure Key Vault, le cluster et les espaces de travail Log Analytics liés se trouvent dans la même région. Ils peuvent être liés à des abonnements différents.

- Si vous mettez à jour votre version de clé dans Key Vault et ne mettez pas à jour les détails de l’identificateur de clé dans le cluster, le cluster Log Analytics continue d’utiliser votre clé précédente et vos données deviennent inaccessibles. Mettez à jour les nouveaux détails de l’identificateur de clé dans le cluster pour reprendre l’ingestion des données et avoir la possibilité d’interroger les données.

- Certaines opérations sont longues et peuvent prendre du temps. Il s’agit des opérations de création du cluster, de mise à jour de la clé du cluster et de la suppression du cluster. Vous pouvez vérifier l’état de l’opération en envoyant une requête GET au cluster ou à l’espace de travail et en observant la réponse. Par exemple, l’espace de travail dissocié n’a pas de *clusterResourceId* sous *features*.

- Messages d’erreur
  
  **Création de cluster**
  -  400 -- Le nom du cluster n’est pas valide. Le nom du cluster peut contenir les caractères a-z, A-Z, 0-9 et doit compter entre 3 et 63 caractères.
  -  400 -- Le corps de la demande a la valeur null ou est dans un format incorrect.
  -  400 -- Nom de SKU non valide. Définissez le nom de la SKU sur capacityReservation.
  -  400 -- La capacité a été fournie, mais la référence SKU n’est pas capacityReservation. Définissez le nom de la SKU sur capacityReservation.
  -  400 -- Xapacité manquante dans la SKU. Définissez la valeur de capacité sur 500, 1 000, 2 000 ou 5 000 Go/jour.
  -  400 -- La capacité est verrouillée pendant 30 jours. La réduction de la capacité est autorisée 30 jours après la mise à jour.
  -  400 -- Aucune SKU n’a été définie. Définissez le nom du niveau tarifaire sur capacityReservation et la valeur de capacité sur 500, 1 000, 2 000 ou 5 000 Go/jour.
  -  400 -- L’identité a la valeur nul ou est vide. Définissez l’identité avec le type systemAssigned.
  -  400 -- Les KeyVaultProperties sont définies à la création. Mettez à jour les KeyVaultProperties après la création du cluster.
  -  400 -- Impossible d’exécuter une opération pour le moment. L’opération asynchrone est dans un état autre que réussi. Le cluster doit effectuer cette opération avant l’exécution d’une opération de mise à jour.

  **Mise à jour d’un cluster**
  -  400 -- Le cluster est en cours de suppression. L’opération asynchrone est en cours. Le cluster doit effectuer cette opération avant l’exécution d’une opération de mise à jour.
  -  400 -- Les KeyVaultProperties ne sont pas vides, mais leur format est incorrect. Consultez [mise à jour de l’identificateur de la clé](#update-cluster-with-key-identifier-details).
  -  400 -- Échec de validation de la clé dans Key Vault. Peut être dû à un manque d’autorisations ou à l’inexistence de la clé. Vérifiez que vous [avez défini la clé et la stratégie d’accès](#grant-key-vault-permissions) dans Key Vault.
  -  400 -- La clé n’est pas récupérable. La suppression réversible et la protection contre le vidage doivent être définis pour Key Vault. Consulter la [documentation sur Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400 -- Impossible d’exécuter une opération pour le moment. Attendez que l’opération asynchrone se termine et réessayez.
  -  400 -- Le cluster est en cours de suppression. Attendez que l’opération asynchrone se termine et réessayez.

  **Obtention de cluster**
    -  404 -- Cluster introuvable, le cluster a peut-être été supprimé. Si vous essayez de créer un cluster portant ce nom et que cela génère un conflit, le cluster est supprimé de manière réversible pendant 14 jours. Vous pouvez contacter le support technique pour le récupérer ou utiliser un autre nom pour créer un nouveau cluster. 

  **Suppression de cluster**
    -  409 -- Impossible de supprimer un cluster en état d’approvisionnement. Attendez que l’opération asynchrone se termine et réessayez.

  **Liaison d’un espace de travail**
  -  404 -- Espace de travail introuvable. L’espace de travail que vous avez spécifié n’existe pas ou a été supprimé.
  -  409 -- Opération de liaison ou de dissociation d’espace de travail en cours.
  -  400 -- Cluster introuvable, le cluster que vous avez spécifié n’existe pas ou a été supprimé. Si vous essayez de créer un cluster portant ce nom et que cela génère un conflit, le cluster est supprimé de manière réversible pendant 14 jours. Contactez le support technique pour le récupérer.

  **Dissociation d’un espace de travail**
  -  404 -- Espace de travail introuvable. L’espace de travail que vous avez spécifié n’existe pas ou a été supprimé.
  -  409 -- Opération de liaison ou de dissociation d’espace de travail en cours.
## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [facturation des clusters dédiés Log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- En savoir plus sur la [conception appropriée des espaces de travail Log Analytics](./design-logs-deployment.md)
