---
title: Activer SQL Insights
description: Activer SQL Insights dans Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/5/2021
ms.openlocfilehash: 35d6547b9351a86ac400ad441ad0dfec953257f2
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989540"
---
# <a name="enable-sql-insights-preview"></a>Activer SQL Insights (préversion)
Cet article explique comment activer [SQL Insights](sql-insights-overview.md) pour analyser vos déploiements SQL. L’analyse est effectuée à partir d’une machine virtuelle Azure qui établit une connexion à vos déploiements SQL et utilise des vues de gestion dynamique (DMV) pour collecter les données d’analyse. Vous pouvez contrôler les jeux de données qui sont collectés et la fréquence de collecte à l’aide d’un profil d’analyse.

> [!NOTE]
> Pour activer SQL Insights en créant le profil de surveillance et la machine virtuelle à l’aide d’un modèle Resource Manager, consultez [Exemples de modèles Resource Manager pour SQL Insights](resource-manager-sql-insights.md).

Pour en savoir plus sur l’activation de SQL Insights, vous pouvez également vous référer à cet épisode de Data Exposed.
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/How-to-Set-up-Azure-Monitor-for-SQL-Insights/player?format=ny]

## <a name="create-log-analytics-workspace"></a>Créer un espace de travail Log Analytics
SQL Insights stocke ses données dans un ou plusieurs [espaces de travail Log Analytics](../logs/data-platform-logs.md#log-analytics-and-workspaces). Avant de pouvoir activer SQL Insights, vous devez [créer un espace de travail](../logs/quick-create-workspace.md) ou en sélectionner un existant. Un seul espace de travail peut être utilisé avec plusieurs profils d’analyse, mais l’espace de travail et les profils doivent se trouver dans la même région Azure. Pour activer les fonctionnalités et y accéder dans SQL Insights, vous devez avoir le [rôle de contributeur Log Analytics](../logs/manage-access.md) dans l’espace de travail. 

## <a name="create-monitoring-user"></a>Créer des règles d’analyse 
Vous avez besoin d’un utilisateur (ID de connexion) sur les déploiements SQL que vous souhaitez surveiller. Suivez les procédures ci-dessous pour les différents types de déploiements SQL.

Les instructions ci-dessous décrivent le processus par type de SQL que vous pouvez surveiller. Pour effectuer cette opération avec un script sur plusieurs ressources SQL à la fois, reportez-vous au [fichier LISEZMOI](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation-README.txt) et à l’[exemple de script](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation.ps1) suivants.

### <a name="azure-sql-database"></a>Azure SQL Database

> [!NOTE]
> SQL Insights ne prend pas en charge les scénarios Azure SQL Database suivants :
> - **Pools élastiques** : Les métriques ne peuvent pas être collectées pour les pools élastiques. Les métriques ne peuvent pas être collectées pour les bases de données dans des pools élastiques.
> - **Niveaux de service bas** : Les métriques ne peuvent pas être collectées pour les bases de données sur les [niveaux de service](../../azure-sql/database/resource-limits-dtu-single-databases.md) De base, S0, S1 et S2
> 
> SQL Insights offre une prise en charge limitée des scénarios Azure SQL Database suivants :
> - **Niveau serverless** : Les métriques peuvent être collectées pour les bases de données à l’aide du [niveau de calcul serverless](../../azure-sql/database/serverless-tier-overview.md). Toutefois, le processus de collecte des métriques réinitialise le minuteur de délai de pause automatique, ce qui empêche la base de données d’entrer dans un état de pause automatique.

Connectez-vous à une base de données Azure SQL avec [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou l’[Éditeur de requête (préversion)](../../azure-sql/database/connect-query-portal.md) dans le portail Azure ou tout autre outil client SQL.

Exécutez le script suivant pour créer un utilisateur avec les autorisations requises. Remplacez *utilisateur* par un nom d’utilisateur et *mystrongpassword* par un mot de passe fort.

```sql
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Créez un script utilisateur Telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Vérifiez que l’utilisateur a été créé.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Vérifiez le script utilisateur Telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
Connectez-vous à votre Azure SQL Managed Instance en utilisant [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou un outil similaire, puis exécutez le script suivant afin de créer l’utilisateur surveillant avec les autorisations nécessaires. Remplacez *utilisateur* par un nom d’utilisateur et *mystrongpassword* par un mot de passe fort.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
Connectez-vous à SQL Server sur votre machine virtuelle Azure, et utilisez [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou un outil similaire pour exécuter le script suivant afin de créer l’utilisateur surveillant avec les autorisations nécessaires. Remplacez *utilisateur* par un nom d’utilisateur et *mystrongpassword* par un mot de passe fort.
 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

Vérifiez que l’utilisateur a été créé.

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type
from sys.server_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

## <a name="create-azure-virtual-machine"></a>Créer une machine virtuelle Azure 
Vous devrez créer une ou plusieurs machines virtuelles Azure qui seront utilisées pour collecter des données afin d’analyser SQL.  

> [!NOTE]
>  Les [profils d’analyse](#create-sql-monitoring-profile) spécifient les données que vous recueillerez parmi les différents types de SQL que vous souhaitez analyser. Chaque ordinateur virtuel d’analyse ne peut être associé qu’à un seul profil d’analyse. Si vous avez besoin de plusieurs profils d’analyse, vous devez créer une machine virtuelle pour chacun d’entre eux.

### <a name="azure-virtual-machine-requirements"></a>Configuration requise des machines virtuelles Azure
La configuration requise est la suivante pour les machines virtuelles Azure.

- Système d’exploitation : Ubuntu 18.04 
- Tailles de machines virtuelles Azure minimales recommandées : Standard_B2s (2 processeurs, 4 Gio de mémoire) 
- Régions prises en charge : toutes les [régions prises en charge par l’agent de Azure Monitor](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> La taille de machine virtuelle Standard_B2s (2 processeurs, 4 Gio de mémoire) prend en charge jusqu’à 100 chaînes de connexion. Vous ne devez pas allouer plus de 100 connexions à une seule machine virtuelle.

En fonction des paramètres réseau de vos ressources SQL, les machines virtuelles devront peut-être être placées dans le même réseau virtuel que vos ressources SQL afin qu’elles puissent établir des connexions réseau pour collecter les données d’analyse.

## <a name="configure-network-settings"></a>Configurer les paramètres réseau
Chaque type de SQL offre des méthodes pour que votre machine virtuelle d’analyse accède en toute sécurité à SQL. Les sections ci-dessous couvrent les options basées sur le type de déploiement SQL.

### <a name="azure-sql-database"></a>Azure SQL Database

SQL Insights prend en charge l’accès à votre Azure SQL Database via son point de terminaison public, ainsi qu’à partir de son réseau virtuel.

Pour accéder via le point de terminaison public, vous devez ajouter une règle dans la page **Paramètres de pare-feu** et dans la section [Paramètres du pare-feu IP](../../azure-sql/database/network-access-controls-overview.md#ip-firewall-rules) . Pour spécifier l’accès à partir d’un réseau virtuel, vous pouvez définir des [règles de pare-feu de réseau virtuel](../../azure-sql/database/network-access-controls-overview.md#virtual-network-firewall-rules) et définir les [balises de service requises par l’agent de Azure Monitor](../agents/azure-monitor-agent-overview.md#networking). [Cet article](../../azure-sql/database/network-access-controls-overview.md#ip-vs-virtual-network-firewall-rules) décrit les différences entre ces deux types de règles de pare-feu.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Définir le pare-feu du serveur" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Paramètres du pare-feu." lightbox="media/sql-insights-enable/firewall-settings.png":::

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

Si votre machine virtuelle d’analyse se trouve dans le même réseau virtuel que vos ressources SQL MI, consultez [Se connecter à l’intérieur du même réseau virtuel](../../azure-sql/managed-instance/connect-application-instance.md#connect-inside-the-same-vnet). Si votre machine virtuelle d’analyse se trouve dans le réseau virtuel différent de vos ressources SQL MI, consultez [Se connecter à l’intérieur d’un autre réseau virtuel](../../azure-sql/managed-instance/connect-application-instance.md#connect-inside-a-different-vnet).

### <a name="sql-server"></a>SQL Server 
Si votre machine virtuelle d’analyse se trouve dans le même réseau virtuel que les ressources de votre machine virtuelle SQL, consultez [Se connecter à SQL Server au sein d’un réseau virtuel](../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md#connect-to-sql-server-within-a-virtual-network). Si votre machine virtuelle d’analyse se trouve dans le même réseau virtuel que les ressources de votre machine virtuelle SQL, consultez  [Se connecter à SQL Server via Internet](../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md#connect-to-sql-server-over-the-internet).

## <a name="store-monitoring-password-in-key-vault"></a>Stocker le mot de passe d’analyse dans Key Vault
En guise de meilleure pratique de sécurité, nous vous recommandons vivement de stocker vos mots de passe d’utilisateur (ID de connexion) SQL dans un coffre de clés, au lieu de les entrer directement dans vos chaînes de connexion de profil surveillant.

Lorsque vous configurez votre profil pour l’analyse SQL, vous devez disposer de l’une des autorisations suivantes sur la ressource Key Vault que vous souhaitez utiliser :

- Microsoft.Authorization/roleAssignments/write 
- Microsoft.Authorization/roleAssignments/delete

Si vous disposez de ces autorisations, une nouvelle stratégie d’accès au coffre de clés sera automatiquement créée dans le cadre de la création de votre profil SQL Monitoring qui utilise le coffre de clés que vous avez spécifié. 

> [!IMPORTANT]
> Vous devez vous assurer que la configuration du réseau et de la sécurité permet à la machine virtuelle de surveillante d’accéder au coffre de clés. Pour plus d’informations, consultez [Accès à Azure Key Vault derrière un pare-feu](/key-vault/general/access-behind-firewall.md) et [Configurer les paramètres de mise en réseau Azure Key Vault](/key-vault/general/how-to-azure-key-vault-network-security.md).

## <a name="create-sql-monitoring-profile"></a>Créer un profil d’analyse SQL
Ouvrez SQL Insights en sélectionnant **SQL (préversion)** de la section **Insight** du menu **Azure Monitor** dans le Portail Azure. Cliquez sur **Créer un profil**. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Créer un nouveau profil." lightbox="media/sql-insights-enable/create-new-profile.png":::

Le profil stocke les informations que vous souhaitez collecter à partir de vos systèmes SQL.  Il dispose de paramètres spécifiques pour : 

- Azure SQL Database 
- Azure SQL Managed Instance
- Exécution de SQL Server sur des machines virtuelles  

Par exemple, vous pouvez créer un profil nommé *Production SQL* et un autre nommé *Mise en lots SQL* avec des paramètres différents pour la fréquence de collecte des données, les données à collecter et l’espace de travail auquel les données doivent être envoyées. 

Le profil est stocké en tant que ressource de [règle de collecte de données](../agents/data-collection-rule-overview.md) dans l’abonnement et le groupe de ressources que vous sélectionnez. Chaque profil a besoin des éléments suivants :

- Nom. Ne peut pas être modifié une fois créé.
- Lieu. Il s’agit d’une région Azure.
- Espace de travail Log Analytics pour stocker les données d’analyse.
- Paramètres de collecte pour la fréquence et le type de données d’analyse SQL à collecter.

> [!NOTE]
> L’emplacement du profil doit se trouver au même emplacement que l’espace de travail Log Analytics auquel vous envisagez d’envoyer les données d’analyse.

:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Détails du profil." lightbox="media/sql-insights-enable/profile-details.png":::

Cliquez sur **Créer un profil d’analyse** une fois que vous avez entré les détails de votre profil d’analyse. Le déploiement du fichier peut prendre jusqu’à une minute.  Si vous ne voyez pas le nouveau profil répertorié dans la zone de liste modifiable **Profil d’analyse**, cliquez sur le bouton Actualiser. il doit apparaître une fois le déploiement terminé.  Une fois que vous avez sélectionné le nouveau profil, sélectionnez l’onglet **Gérer le profil** pour ajouter une machine d’analyse qui sera associée au profil.

### <a name="add-monitoring-machine"></a>Ajouter une machine d’analyse
Sélectionnez **Ajouter une machine d’analyse** pour ouvrir un panneau contextuel et choisir la machine virtuelle à configurer pour analyser vos instances SQL et fournir les chaînes de connexion.

Sélectionnez l’abonnement et le nom de votre machine virtuelle d’analyse. Si vous utilisez un coffre de clés pour stocker votre mot de passe pour l’utilisateur surveillant, sélectionnez les ressources du coffre de clés avec ces secrets, puis entrez l’URI et le nom du secret pour le mot de passe à utiliser dans les chaînes de connexion. Pour plus d’informations sur l’identification de la chaîne de connexion pour différents déploiements SQL, consultez la section suivante.

:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Ajouter une machine d’analyse." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::

### <a name="add-connection-strings"></a>Ajouter des chaînes de connexion 
La chaîne de connexion spécifie le nom de connexion que SQL insights doit utiliser pour se connecter à SQL afin de collecter des données de surveillance. Si vous utilisez un coffre de clés pour stocker le mot de passe de votre utilisateur surveillant, indiquez l’URI du coffre de clés et le nom du secret contenant le mot de passe.

La chaîne de connexions varie en fonction de chaque type de ressource SQL :

#### <a name="azure-sql-database"></a>Azure SQL Database
Entrer la chaîne de connexion dans le formulaire :

```
sqlAzureConnections": [
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
]
```

Obtenez les détails de l’élément de menu **Chaînes de connexion** pour la base de données.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="Chaîne de connexion à la base de données SQL" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Pour analyser un secondaire accessible en lecture, incluez la valeur de clé `ApplicationIntent=ReadOnly` dans la chaîne de connexion. SQL Insights prend en charge l’analyse d’un seul secondaire. Les données collectées seront marquées pour refléter le primaire ou le secondaire. 

#### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
Entrer la chaîne de connexion dans le formulaire :

```
"sqlManagedInstanceConnections": [
   "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;" 
] 
```
Obtenez les détails de l’élément de menu **Chaînes de connexion** pour l’instance gérée.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="Chaîne de connexion SQL Managed Instance" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Pour analyser un secondaire accessible en lecture, incluez la valeur de clé `ApplicationIntent=ReadOnly` dans la chaîne de connexion. SQL Insights prend en charge la surveillance d’un seul secondaire. Les données collectées seront étiquetées pour refléter le Primaire ou le Secondaire. 

#### <a name="sql-server"></a>SQL Server 
Entrer la chaîne de connexion dans le formulaire :

```
"sqlVmConnections": [
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Si votre machine virtuelle d’analyse se trouve dans le même réseau virtuel, utilisez l’adresse IP privée du serveur.  Dans le cas contraire, utilisez l’adresse IP publique. Si vous utilisez une machine virtuelle Azure SQL, vous pouvez voir le port à utiliser ici sur la page **Sécurité** de la ressource.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Sécurité de la machine virtuelle SQL" lightbox="media/sql-insights-enable/sql-vm-security.png":::

## <a name="monitoring-profile-created"></a>Création d’un profil d’analyse 

Sélectionnez **Ajouter une analyse de machine virtuelle** pour configurer la machine virtuelle afin de collecter les données de vos ressources SQL. Ne revenez pas à l’onglet **Vue d’ensemble**. Dans quelques minutes, la colonne État doit changer pour indiquer « En cours de collecte », et vous devez voir les données des ressources SQL que vous avez choisi d’analyser.

Si vous ne voyez pas les données, consultez [Résolution des problèmes liés à SQL Insights](sql-insights-troubleshoot.md) pour identifier le problème. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profil créé" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> Si vous devez mettre à jour votre profil d’analyse ou les chaînes de connexion sur vos machines virtuelles d’analyse, vous pouvez le faire via l’onglet **Gérer le profil** de SQL Insights. Une fois que vos mises à jour ont été enregistrées, les modifications sont appliquées dans les cinq minutes.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Résolution des problèmes liés à SQL Insights](sql-insights-troubleshoot.md) si SQL Insights ne fonctionne pas correctement après avoir été activé.
