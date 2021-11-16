---
title: Résoudre des problèmes liés à SQL Insights (préversion)
description: Découvrez comment résoudre les problèmes liés à SQL Insights dans Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/03/2021
ms.openlocfilehash: bf3b03f7837be31cf025d9ec7ea06a86a5c62e18
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989353"
---
# <a name="troubleshoot-sql-insights-preview"></a>Résoudre des problèmes liés à SQL Insights (préversion)
Pour résoudre les problèmes liés à la collecte de données dans SQL Insights, vérifiez l’état de la machine d’analyse dans l’onglet **Gérer le profil**. Les états sont les suivants :

- **Collecte** 
- **Collecte non active** 
- **Collecte avec des erreurs** 
 
Sélectionnez l’état pour afficher les journaux et plus de détails susceptibles de vous aider à résoudre le problème. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Capture d’écran montrant l’état d’une machine d’analyse.":::

## <a name="status-not-collecting"></a>État : Collecte non active 
L’état de la machine d’analyse est **Collecte non active** s’il n’existe aucune donnée dans *InsightsMetrics* pour SQL au cours des dix dernières minutes. 

> [!NOTE]
> Vérifiez que vous utilisez une [version prise en charge de SQL](sql-insights-overview.md#supported-versions) pour tenter de collecter des données. Par exemple, si votre profil et votre chaîne de connexion sont valides, mais que votre version d’Azure SQL Database n’est pas prise en charge, vous obtenez un état **Collecte non active**.

SQL Insights utilise la requête suivante pour récupérer ces informations :

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Vérifiez si des journaux de Telegraf aident à identifier la cause racine du problème. S’il existe des entrées de journal, vous pouvez sélectionner **Collecte non active**, puis consulter les journaux et les informations de résolution des problèmes courants. 

S’il n’existe aucune entrée de journal, vérifiez les journaux sur la machine virtuelle d’analyse pour les services suivants installés par deux extensions de machine virtuelle :

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - Service : mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - Service : wli 
  - Service : ms-telegraf 
  - Service : td-agent-bit-wli 
  - Journal d’extension pour vérifier les échecs d’installation : /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 

### <a name="wli-service-logs"></a>Journaux d’activité du service wli 

Journaux d’activité de service : `/var/log/wli.log`

Pour afficher les journaux d’activité récents : `tail -n 100 -f /var/log/wli.log`
 
Si vous voyez le journal des erreurs suivant, il y a un problème avec le service mdsd.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```

### <a name="telegraf-service-logs"></a>Journaux d’activité du service Telegraf 

Journaux d’activité de service : `/var/log/ms-telegraf/telegraf.log`

Pour afficher les journaux d’activité récents : `tail -n 100 -f /var/log/ms-telegraf/telegraf.log`

Pour afficher les journaux récents des erreurs et des avertissements : `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

La configuration que Telegraf utilise est générée par le service wli et placée sous le chemin : `/etc/ms-telegraf/telegraf.d/wli`
 
Si une mauvaise configuration est générée, le service ms-telegraf peut échouer à démarrer. Vérifiez si le service ms-telegraf est en cours d’exécution à l’aide de cette commande : `service ms-telegraf status`

Pour afficher les messages d’erreur du service Telegraf, exécutez-le manuellement à l’aide de la commande suivante : 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>Journaux d’activité du service mdsd 

Vérifiez les [prérequis](../agents/azure-monitor-agent-install.md#prerequisites) pour l’agent Azure Monitor. 


Journaux d’activité de service :  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Pour afficher les erreurs récentes : `tail -n 100 -f /var/log/mdsd.err`

Si vous devez contacter le support, collectez les informations suivantes : 

- Journaux d’activité dans `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Journal dans `/var/log/waagent.log` 
- Journaux d’activité dans `/var/log/mdsd*`
- Fichiers dans `/etc/mdsd.d/`
- File `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Configuration de machine virtuelle d’analyse non valide

L’une des causes de l’état **Collecte non active** est une configuration non valide de la machine virtuelle d’analyse. Voici la configuration la plus simple :

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Cette configuration spécifie les jetons de remplacement à utiliser dans la configuration du profil sur votre machine virtuelle d’analyse. Elle vous permet également de référencer des secrets à partir d’Azure Key Vault. Vous n’avez donc pas besoin de conserver les valeurs secrètes dans une configuration (ce que nous vous recommandons vivement).

Dans cette configuration, la chaîne de connexion de base de données inclut un jeton de remplacement `$telegrafPassword`. SQL Insights remplace ce jeton par le mot de passe d’authentification SQL extrait du Key Vault. L’URI du Key Vault est spécifié dans la section de configuration `telegrafPassword` sous `secrets`.

#### <a name="secrets"></a>Secrets
Les secrets sont des jetons dont les valeurs sont récupérées au moment de l’exécution d’un coffre de clés Azure. Un secret est défini par une paire de valeurs incluant un URI de coffre de clés et un nom de secret. Cette définition permet à SQL Insights d’extraire la valeur du secret lors du runtime, et de l’utiliser dans la configuration en aval.

Vous pouvez définir autant de secrets que nécessaire, y compris des secrets stockés dans plusieurs coffres de clés.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

L’autorisation d’accéder au coffre de clés est accordée à une identité managée sur la machine virtuelle d’analyse. Cette identité managée doit disposer de l’autorisation Get sur tous les secrets du Key Vault référencés dans le configuration du profil de surveillance. Vous pouvez veiller à cela à partir du portail Azure, de PowerShell, d’Azure CLI ou d’un modèle Azure Resource Manager.

#### <a name="parameters"></a>Paramètres
Les paramètres sont des jetons qui peuvent être référencés dans la configuration du profil via des modèles JSON. Les paramètres ont un nom et une valeur. Les valeurs peuvent être de n’importe quel type JSON, y compris des objets et des tableaux. Un paramètre est référencé dans la configuration du profil par son nom selon la convention suivante : `.Parameters.<name>`.

Les paramètres peuvent référencer des secrets dans Key Vault à l’aide de la même convention. Par exemple, `sqlAzureConnections` référence le secret `telegrafPassword` selon la convention `$telegrafPassword`.

Au moment de l’exécution, tous les paramètres et secrets sont résolus et fusionnés avec la configuration du profil pour construire la configuration réelle à utiliser sur la machine.

> [!NOTE]
> Les noms de paramètre `sqlAzureConnections`, `sqlVmConnections` et `sqlManagedInstanceConnections` sont tous obligatoires dans la configuration, même si vous ne fournissez pas de chaînes de connexion pour certains d’entre eux.

## <a name="status-collecting-with-errors"></a>État : Collecte avec des erreurs
L’état de la machine d’analyse est **Collecte avec des erreurs** s’il existe au moins un journal *InsightsMetrics* récent, et qu’il existe également des erreurs dans la table *Operation*.

SQL Insights utilise les requêtes suivantes pour récupérer ces informations :

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```kusto
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> Si vous ne voyez pas de données dans `WorkloadDiagnosticLogs`, vous devez peut-être mettre à jour votre profil de surveillance. Dans SQL Insights dans le portail Azure, sélectionnez **Gérer le profil** > **Modifier le profil** > **Mettre à jour le profil de surveillance**.

Pour les cas courants, nous fournissons des informations de résolution des problèmes dans l’affichage de vos journaux : 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Vue des journaux d’activité de résolution des problèmes.":::

## <a name="known-issues"></a>Problèmes connus

Pendant la période de préversion de SQL Insights, il se peut que vous rencontriez les problèmes connus suivants.

* **Erreur « Échec de la connexion » lors de la connexion au serveur ou à la base de données**. L’utilisation de certains caractères spéciaux dans des mots de passe d’authentification SQL enregistrés dans la configuration de la machine virtuelle de surveillance ou dans le coffre de clés peut empêcher la machine virtuelle de surveillance de se connecter à un serveur ou à une base de données SQL. Ce jeu de caractères inclut les parenthèses, les crochets, les accolades, le signe dollar, les barres oblique et oblique inverse, et le point (`[ { ( ) } ] $ \ / .`).

## <a name="best-practices"></a>Meilleures pratiques

* **Vérifiez l’accès au coffre de clés à partir de la machine virtuelle de surveillance**. Si vous utilisez un coffre de clés pour stocker les mots de passe d’authentification SQL (fortement recommandé), vous devez vous assurer que la configuration du réseau et de la sécurité permet à la machine virtuelle de surveillance d’accéder au coffre de clés. Pour plus d’informations, consultez [Accès à Azure Key Vault derrière un pare-feu](/key-vault/general/access-behind-firewall.md) et [Configurer les paramètres de mise en réseau Azure Key Vault](/key-vault/general/how-to-azure-key-vault-network-security.md). Pour vérifier que la machine virtuelle de surveillance peut accéder au coffre de clés, vous pouvez exécuter les commandes suivantes à partir d’une session SSH connectée à la machine virtuelle. Vous devriez être en mesure de récupérer correctement le jeton d’accès et le secret. Remplacez `[YOUR-KEY-VAULT-URL]`, `[YOUR-KEY-VAULT-SECRET]` et `[YOUR-KEY-VAULT-ACCESS-TOKEN]` par des valeurs réelles.

  ```bash
  # Get an access token for accessing Key Vault secrets
  curl 'http://[YOUR-KEY-VAULT-URL]/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true
  
  # Get Key Vault secret
  curl 'https://[YOUR-KEY-VAULT-URL]/secrets/[YOUR-KEY-VAULT-SECRET]?api-version=2016-10-01' -H "Authorization: Bearer [YOUR-KEY-VAULT-ACCESS-TOKEN]"
  ```

* **Mettez à jour le logiciel sur la machine virtuelle de surveillance**. Nous vous recommandons vivement de mettre à jour périodiquement le système d’exploitation et les extensions sur la machine virtuelle de surveillance. Si une extension prend en charge la mise à niveau automatique, activez cette option.

* **Enregistrez les configurations précédentes**. Si vous souhaitez apporter des modifications au profil de surveillance ou à la configuration de la machine virtuelle de surveillance, nous vous recommandons d’enregistrer d’abord une copie de travail de vos données de configuration. Dans la page SQL Insights dans le portail Azure, sélectionnez **Gérer le profil** > **Modifier le profil**, puis copiez le texte de la **Configuration du profil de surveillance actuelle** dans un fichier. De même, sélectionnez **Gérer le profil** > **Configurer** pour la machine virtuelle de surveillance, puis copiez le texte de la **Configuration de surveillance actuelle** dans un fichier. Si des erreurs de collecte de données se produisent après des changements de configuration, vous pouvez comparer la nouvelle configuration à la configuration de travail connue en vous aidant d’un outil de comparaison de texte pour trouver les modifications susceptibles d’avoir eu une incidence sur la collecte.

## <a name="next-steps"></a>Étapes suivantes

- Obtenez plus d’informations sur l’[activation de SQL Insights](sql-insights-enable.md).
