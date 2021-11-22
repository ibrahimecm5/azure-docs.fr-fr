---
title: Meilleures pratiques pour la supervision du Stockage Blob Azure
description: Découvrez les meilleures pratiques d’utilisation des métriques et journaux pour superviser votre Stockage Blob Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 07/30/2021
ms.custom: monitoring
ms.openlocfilehash: 222c78803e268cef95dc322952874f58346c2919
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180173"
---
# <a name="best-practices-for-monitoring-azure-blob-storage"></a>Meilleures pratiques pour la supervision du Stockage Blob Azure

Cet article présente des scénarios courants de supervision du stockage, ainsi que des recommandations de meilleures pratiques pour les mettre en œuvre.

## <a name="identify-storage-accounts-with-no-or-low-use"></a>Identifier les comptes de stockage avec peu ou pas d’utilisation

Insights de stockage est un tableau de bord basé sur les journaux et métriques du service Stockage Azure. Vous pouvez l’utiliser pour examiner le volume de transactions et la capacité utilisée de tous vos comptes. Ces informations peuvent vous aider à déterminer les comptes que vous êtes susceptible de mettre hors service. Pour configurer Insights de stockage, consultez [Supervision de votre service de stockage avec le tableau de bord Insights de stockage Azure Monitor](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

### <a name="analyze-transaction-volume"></a>Analyser le volume de transactions

Dans la vue [Insights de stockage d’Azure Monitor](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json#view-from-azure-monitor), triez vos compte dans l’ordre croissant en utilisant la colonne **Transactions**. L’illustration suivante montre un compte avec un volume de transactions faible sur la période spécifiée.

> [!div class="mx-imgBorder"]
> ![Volume de transaction dans Insights de stockage](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-volume.png)

Cliquez sur le lien du compte pour en savoir plus sur ces transactions. Dans cet exemple, la plupart des demandes sont adressées au service Stockage Blob.

> [!div class="mx-imgBorder"]
> ![Transaction par type de service](./media/blob-storage-monitoring-scenarios/storage-insights-transactions-by-storage-type.png)

Pour déterminer quels tris de demandes sont effectués, regardez le détail du graphique **Transactions par nom d’API**.

> [!div class="mx-imgBorder"]
> ![API de transaction de stockage](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-apis.png)

Dans cet exemple, toutes les demandes répertorient les opérations ou les demandes pour les propriétés du compte. Il n’y a aucune transaction de lecture et d’écriture. Cela peut vous amener à penser que le compte n’est pas utilisé de manière significative.

### <a name="analyze-used-capacity"></a>Analyser la capacité utilisée

Sous l’onglet **Capacité** de la [vue Insights de stockage d’Azure Monitor](../common/storage-insights-overview.md#view-from-azure-monitor), triez vos compte dans l’ordre croissant en utilisant la colonne **Capacité utilisée du compte**. L’illustration suivante montre un compte dont le volume de capacité est inférieur à celui des autres comptes.

> [!div class="mx-imgBorder"]
> ![Capacité de stockage utilisée](./media/blob-storage-monitoring-scenarios/storage-insights-capacity-used.png)

Pour examiner les objets blob associés à cette capacité utilisée, vous pouvez utiliser l’Explorateur Stockage. Lorsqu’il existe un grand nombre d’objets blob, il est conseillé de générer un rapport à l’aide d’une [stratégie d’inventaire d’objets blob](blob-inventory.md).

## <a name="monitor-the-use-of-a-container"></a>Superviser l’utilisation d’un conteneur

Si vous partitionnez les données de votre client par conteneur, vous pouvez superviser le volume de capacité utilisé par chaque client. Vous pouvez utiliser l’inventaire d’objets Stockage Azure pour effectuer un inventaire des objets blob avec des informations sur la taille. Vous pouvez ensuite agréger la taille et le nombre au niveau du conteneur. Pour voir un exemple, consultez [Calculer le nombre et la taille totale des objets blob par conteneur à l’aide de l’inventaire Stockage Azure](calculate-blob-count-size.md).

Vous pouvez également évaluer le trafic au niveau du conteneur en interrogeant les journaux. Pour en savoir plus sur l’écriture de requêtes Log Analytics, consultez [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md). Pour en savoir plus sur le schéma des journaux de stockage, consultez [Informations de référence sur la supervision du Stockage Blob Azure](monitor-blob-storage-reference.md#resource-logs-preview).

Voici une requête pour obtenir le nombre de transactions de lecture et le nombre d’octets lus sur chaque conteneur.

```kusto
StorageBlobLogs
| where OperationName  == "GetBlob"
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize ReadSize = sum(ResponseBodySize), ReadCount = count() by tostring(ContainerName)
```

La requête suivante utilise une requête similaire pour obtenir des informations sur les opérations d’écriture.

```kusto
StorageBlobLogs
| where OperationName == "PutBlob" or
  OperationName == "PutBlock" or
  OperationName == "PutBlockList" or
  OperationName == "AppendBlock" or
  OperationName == "SnapshotBlob" or
  OperationName == "CopyBlob" or
  OperationName == "SetBlobTier"
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize WriteSize = sum(RequestBodySize), WriteCount = count() by tostring(ContainerName)
```

La requête ci-dessus fait référence aux noms de plusieurs opérations, car plusieurs types d’opération peuvent être comptabilisés comme une opération d’écriture. Pour en savoir plus sur les opérations considérées comme des opérations de lecture et d’écriture, consultez la page sur la [tarification Stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) ou la page sur la [tarification Azure Data Lake Storage](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="audit-account-activity"></a>Auditer l’activité du compte

Dans de nombreux cas, vous devez auditer les activités de vos comptes de stockage à des fins de sécurité et de conformité. Les opérations sur les comptes de stockage se répartissent en deux catégories : *Plan de contrôle* et *Plan de données*.

Une opération de plan de contrôle est une demande Azure Resource Manager de création de compte de stockage ou de mise à jour d’une propriété d’un compte de stockage existant. Pour plus d’informations, consultez [Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Une opération de plan de données est une opération sur les données d’un compte de stockage, qui résulte d’une demande adressée au point de terminaison du service de stockage. Par exemple, une opération de plan de données est exécutée lorsque vous chargez un objet blob dans un compte de stockage ou lorsque vous téléchargez un objet blob à partir d’un compte de stockage. Pour plus d’informations, consultez [API de Stockage Azure](/rest/api/storageservices/).

La section montre comment trouver les informations sur le « quand », le « qui », le « quoi » et le « comment » des opérations de plan de contrôle et de plan de données.

### <a name="auditing-control-plane-operations"></a>Audit des opérations de plan de contrôle

Les opérations de Resource Manager sont capturées dans le [journal d’activité Azure](../../azure-monitor/essentials/activity-log.md). Pour afficher le journal d’activité, ouvrez votre compte de stockage dans le portail Azure, puis sélectionnez **Journal d’activité**.

> [!div class="mx-imgBorder"]
> ![Journal d’activité](./media/blob-storage-monitoring-scenarios/activity-log.png)

Ouvrez une entrée de journal pour afficher un JSON décrivant l’activité. Le JSON suivant présente des informations sur le « quand », le « quoi » et le « comment » d’une opération de plan de contrôle :

> [!div class="mx-imgBorder"]
> ![JSON du journal d’activité](./media/blob-storage-monitoring-scenarios/activity-log-json.png)

La disponibilité des informations sur le « qui » dépend de la méthode d’authentification utilisée pour effectuer l’opération de plan de contrôle. Si l’autorisation est le fait d’un principal de sécurité Azure AD, l’identificateur d’objet de celui-ci apparaît également dans cette sortie JSON (par exemple, `"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`). Étant donné que vous ne verrez peut-être pas toujours d’autres informations relatives à l’identité, telles qu’une adresse e-mail ou un nom, l’identificateur d’objet est toujours la meilleure façon d’identifier de manière unique le principal de sécurité.

Vous pouvez trouver le nom convivial de celui-ci en prenant la valeur de l’identificateur d’objet et en recherchant le principal de sécurité dans la page Azure AD du portail Azure. La capture d’écran suivante montre un résultat de recherche dans Azure AD.

> [!div class="mx-imgBorder"]
> ![Recherche dans Azure Active Directory](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>Audit des opérations de plan de données

Les opérations de plan de données sont capturées dans des [journaux de ressources Azure pour le Stockage](monitor-blob-storage.md#analyzing-logs). Vous pouvez [configurer un paramètre de diagnostic](monitor-blob-storage.md#send-logs-to-azure-log-analytics) afin d’exporter les journaux vers l’espace de travail Log Analytics pour une expérience de requête native.

Voici une requête Log Analytics qui extrait les informations sur le « quand », le « qui », le « quoi » et le « comment » dans une liste d’entrées de journal.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

Pour la partie « quand » de votre audit, le champ `TimeGenerated` indique quand l’entrée de journal a été enregistrée.

Pour la partie « quoi » de votre audit, le champ `Uri` indique l’élément qui a été modifié ou lu.

Pour la partie « comment » de votre audit, le champ `OperationName` indique l’opération qui a été exécutée.

Pour la partie « qui » de votre audit, le champ `AuthenticationType` indique le type d’authentification utilisé pour effectuer une demande. Ce champ peut afficher tout type d’authentification pris en charge par Stockage Azure, y compris l’utilisation d’une clé de compte, d’un jeton de signature d’accès partagé (SAP) ou d’une authentification Azure Active Directory (Azure AD).

#### <a name="identifying-the-security-principal-used-to-authorize-a-request"></a>Identification du principal de sécurité utilisé pour autoriser une demande

Si une demande a été authentifiée à l’aide d’Azure AD, le champ `RequesterObjectId` constitue la méthode la plus fiable pour identifier le principal de sécurité. Vous pouvez trouver le nom convivial de celui-ci en prenant la valeur du champ `RequesterObjectId` et en recherchant le principal de sécurité dans la page Azure AD du portail Azure. La capture d’écran suivante montre un résultat de recherche dans Azure AD.

> [!div class="mx-imgBorder"]
> ![Recherche dans Azure Active Directory](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

Dans certains cas, un nom d’utilisateur principal, ou *UPN*, peut apparaître dans les journaux. Par exemple, si le principal de sécurité est un utilisateur Azure AD, l’UPN s’affichera probablement. Pour d’autres types de principaux de sécurité tels que les identités gérées attribuées par l’utilisateur, ou dans certains scénarios tels que l’authentification croisée de locataire Azure AD, l’UPN n’apparaît pas dans les journaux.

Cette requête affiche toutes les opérations de lecture effectuées par les principaux de sécurité OAuth.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
  and OperationName == "GetBlob"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

L’authentification avec clé partagée et SAP n’offre aucun moyen d’auditer des identités individuelles. Par conséquent, si vous souhaitez améliorer votre capacité à auditer sur la base de l’identité, nous vous recommandons de passer à Azure AD et d’empêcher l’authentification avec clé partagée et SAP. Pour savoir comment empêcher l’authentification avec clé partagée et SAP, consultez [Empêcher l’autorisation avec clé partagée pour un compte de stockage Azure](../common/shared-key-authorization-prevent.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=portal). Pour commencer à utiliser Azure AD, consultez [Autoriser l’accès aux blobs avec Azure Active Directory](authorize-access-azure-active-directory.md).

#### <a name="identifying-the-sas-token-used-to-authorize-a-request"></a>Identification du jeton SAP utilisé pour autoriser une demande

Vous pouvez interroger les opérations qui ont été autorisées à l’aide d’un jeton SAP. Par exemple, cette requête retourne toutes les opérations d’écriture qui ont été autorisées à l’aide d’un jeton SAP.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
  and OperationName == "PutBlob"
  and AuthenticationType == "SAS"
| project TimeGenerated, AuthenticationType, AuthenticationHash, OperationName, Uri
```

Pour des raisons de sécurité, les jetons SAP n’apparaissent pas dans les journaux. Toutefois, le hachage SHA-256 du jeton SAP s’affiche dans le champ `AuthenticationHash` retourné par cette requête. 

Si vous avez distribué plusieurs jetons SAP et que vous souhaitez savoir quels jetons SAP sont utilisés, vous devez convertir chacun de vos jetons SAP en hachage SHA-256, puis comparer ce hachage à la valeur de hachage qui s’affiche dans les journaux.

Commencez par décoder chaque chaîne de jeton SAP. L’exemple suivant décode une chaîne de jeton SAP à l’aide de PowerShell.

```powershell
[uri]::UnescapeDataString("<SAS token goes here>")
```

Vous pouvez ensuite passer cette chaîne à l’applet de commande PowerShell [Get-FileHash](/powershell/module/microsoft.powershell.utility/get-filehash). Pour obtenir un exemple, consultez [Exemple 4 : Calculer le hachage d’une chaîne](/powershell/module/microsoft.powershell.utility/get-filehash#example-4--compute-the-hash-of-a-string).

Vous pouvez également transmettre la chaîne décodée à la fonction [hash_sha256()](/azure/data-explorer/kusto/query/sha256hashfunction) dans le cadre d’une requête lorsque vous utilisez Azure Data Explorer.

Les jetons SAP ne contiennent pas d’informations d’identité. Une façon d’effectuer le suivi des activités des utilisateurs ou des organisations consiste à conserver un mappage des utilisateurs ou des organisations à différents hachages de jeton SAP.

## <a name="optimize-cost-for-infrequent-queries"></a>Optimiser les coûts des requêtes peu fréquentes

Vous pouvez exporter les journaux vers Log Analytics afin de bénéficier de fonctionnalités de requête natives riches. Lorsque vous avez des transactions massives sur votre compte de stockage, le coût d’utilisation des journaux avec Log Analytics peut être élevé. Pour plus d’informations, consultez la page [Tarification Azure Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). Si vous n’envisagez d’interroger des journaux qu’occasionnellement (par exemple, à des fins d’audit de conformité), vous pouvez envisager de réduire le coût total de ces requêtes en exportant les journaux vers un compte de stockage, puis en utilisant une solution de requête serverless sur données de journal, telle qu’Azure Synapse.

Azure Synapse vous permet de créer un pool SQL serverless pour interroger des données de journal quand vous en avez besoin. Cela peut réduire sensiblement les coûts.

1. Exportez les journaux vers un compte de stockage. Pour plus d’informations, consultez [Création d’un paramètre de diagnostic](monitor-blob-storage.md#creating-a-diagnostic-setting).

2. Créez et configurez un espace de travail Synapse. Pour plus d’informations, consultez [Démarrage rapide : Créer un environnement Synapse](../../synapse-analytics/quickstart-create-workspace.md).

2. Interrogez les journaux. Pour plus d’informations, consultez [Interroger des fichiers JSON à l’aide d’un pool SQL serverless dans Azure Synapse Analytics](../../synapse-analytics/sql/query-json-files.md).

   Voici un exemple :

   ```sql
    select
        JSON_VALUE(doc, '$.time') AS time,
        JSON_VALUE(doc, '$.properties.accountName') AS accountName,
        JSON_VALUE(doc, '$.identity.type') AS identityType,
        JSON_VALUE(doc, '$.identity.requester.objectId') AS requesterObjectId,
        JSON_VALUE(doc, '$.operationName') AS operationName,
        JSON_VALUE(doc, '$.callerIpAddress') AS callerIpAddress,
        JSON_VALUE(doc, '$.uri') AS uri
        doc
    from openrowset(
            bulk 'https://demo2uswest4log.blob.core.windows.net/insights-logs-storageread/resourceId=/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/demo2uswest/blobServices/default/y=2021/m=03/d=19/h=*/m=*/PT1H.json',
            format = 'csv', fieldterminator ='0x0b', fieldquote = '0x0b'
        ) with (doc nvarchar(max)) as rows
    order by JSON_VALUE(doc, '$.time') desc

   ```

## <a name="see-also"></a>Voir aussi

- [Supervision du service Stockage Blob Azure](monitor-blob-storage.md)
- [Informations de référence sur les données de supervision du Stockage Blob Azure](monitor-blob-storage-reference.md)
- [Tutoriel : Utiliser des requêtes Kusto dans Azure Data Explorer et Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)
- [Bien démarrer avec les requêtes de journal dans Azure Monitor](../../azure-monitor/logs/get-started-queries.md).
