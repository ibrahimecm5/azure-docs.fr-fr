---
title: Configurer votre liaison privée
description: Configurer une liaison privée
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: 99e8830b1b4b667d68bb8db1243d1c0ddaeab15a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006769"
---
# <a name="configure-your-private-link"></a>Configurer votre liaison privée
La configuration d’une liaison privée nécessite plusieurs étapes : 
* Création d’une étendue de liaison privée avec des ressources
* Création d’un point de terminaison privé sur votre réseau et connexion à l’étendue
* Configuration de l’accès requis sur vos ressources Azure Monitor

Cet article passe en revue la façon dont cela s’effectue via le portail Azure et fournit un exemple de modèle Azure Resource Manager (ARM) pour automatiser le processus. 

## <a name="create-a-private-link-connection-through-the-azure-portal"></a>Créer une connexion Private Link via le portail Azure
Dans cette section, nous allons passer en revue le processus de configuration d’un lien privé via le Portail Azure, étape par étape. Consultez [Utiliser des API et une ligne de commande](#use-apis-and-command-line) pour créer et gérer un lien privé à l’aide de la ligne de commande ou d’un modèle de Azure Resource Manager (modèle ARM).

### <a name="create-an-azure-monitor-private-link-scope"></a>Créer une étendue de liaison privée Azure Monitor

1. Accédez à **Créer une ressource** dans le portail Azure et recherchez **Étendue de liaison privée Azure Monitor**.

   ![Rechercher l’étendue de liaison privée Azure Monitor](./media/private-link-security/ampls-find-1c.png)

2. Sélectionnez **Créer**.
3. Choisissez un abonnement et un groupe de ressources.
4. Donnez un nom à l’AMPLS. Il est préférable d’utiliser un nom explicite et clair, par exemple « AppServerProdTelem ».
5. Sélectionnez **Vérifier + créer**. 

   ![Créer une étendue de liaison privée Azure Monitor](./media/private-link-security/ampls-create-1d.png)

6. Laissez le processus de validation se terminer, puis sélectionnez **Créer**.

### <a name="connect-azure-monitor-resources"></a>Connecter des ressources Azure Monitor

Connectez des ressources Azure Monitor (espaces de travail Log Analytics et composants Application Insights) à votre AMPLS.

1. Dans votre étendue de liaison privée Azure Monitor, sélectionnez **Ressources Azure Monitor** dans le menu de gauche. Sélectionnez le bouton **Ajouter**.
2. Ajoutez l’espace de travail ou le composant. Le fait de sélectionner sur le bouton **Ajouter** affiche une boîte de dialogue dans laquelle vous pouvez choisir des ressources Azure Monitor. Vous pouvez parcourir vos abonnements et groupes de ressources, ou vous pouvez saisir leur nom pour les filtrer. Choisissez l’espace de travail ou le composant, puis sélectionnez **Appliquer** pour les ajouter à votre étendue.

    ![Capture d’écran de l’expérience utilisateur Sélectionner une étendue](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Pour supprimer des ressources Azure Monitor, vous devez d’abord les déconnecter des objets AMPLS auxquels elles sont connectées. Vous ne pouvez pas supprimer des ressources connectées à un AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Se connecter à un point de terminaison privé

Maintenant que vous disposez de ressources connectées à votre AMPLS, créez un point de terminaison privé pour connecter notre réseau. Vous pouvez effectuer cette tâche dans le [centre Azure Private Link sur le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) ou au sein même de votre étendue de liaison privée Azure Monitor, comme dans cet exemple.

1. Dans votre ressource d’étendue, sélectionnez **Connexions de point de terminaison privé** dans le menu gauche de la ressource. Sélectionnez **Point de terminaison privé** pour démarrer le processus de création du point de terminaison. Vous pouvez également approuver ici des connexions qui ont été démarrées dans le centre Azure Private Link en les sélectionnant puis en choisissant **Approuver**.

    ![Capture d’écran de l’expérience utilisateur Connexions de point de terminaison privés](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Choisissez l’abonnement, le groupe de ressources et le nom du point de terminaison, ainsi que la région dans laquelle il doit résider. La région doit être la même que celle du réseau virtuel auquel vous vous connectez.

3. Sélectionnez **Suivant : Ressource**. 

4. Dans l’écran Ressource :

   a. Sélectionnez l’**abonnement** qui contient votre ressource d’étendue de liaison privée Azure Monitor. 

   b. Pour le **type de ressource**, choisissez **Microsoft.insights/privateLinkScopes**. 

   c. Dans la liste déroulante des **ressources**, choisissez l’étendue de liaison privée que vous avez créée précédemment. 

   d. Sélectionnez **Suivant : Configuration >** .
      ![Screenshot of select Create Private Endpoint](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. Dans le volet configuration :

   a.    Choisissez le **réseau virtuel** et le **sous-réseau** que vous souhaitez connecter à vos ressources Azure Monitor. 
 
   b.    Choisissez **Oui** pour **Intégrer à une zone DNS privée**, et laissez-le créer automatiquement une nouvelle zone DNS privée. Les zones DNS réelles peuvent différer de ce que montre la capture d’écran ci-dessous. 
   > [!NOTE]
   > Si vous choisissez **Non** et préférez gérer les enregistrements DNS manuellement, commencez par configurer votre liaison privée, y compris ce point de terminaison privé et la configuration AMPLS. Ensuite, configurez votre DNS conformément aux instructions de la rubrique [Configuration DNS des points de terminaison privés Azure](../../private-link/private-endpoint-dns.md). Veillez à ne pas créer d’enregistrements vides en préparation de votre configuration de liaison privée. Les enregistrements DNS que vous créez peuvent remplacer les paramètres existants et affecter votre connectivité avec Azure Monitor.
 
   c.    Sélectionnez **Revoir + créer**.
 
   d.    Laissez le processus de validation se terminer. 
 
   e.    Sélectionnez **Create** (Créer). 

    ![Capture d’écran de détails de la création d’un point de terminaison privé.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Vous avez maintenant créé un nouveau point de terminaison privé connecté à cet AMPLS.


## <a name="configure-access-to-your-resources"></a>Configurer l’accès à vos ressources
Jusqu’à présent, nous avons abordé la configuration de votre réseau, mais vous devez également réfléchir à la façon dont vous souhaitez configurer l’accès réseau à vos ressources supervisées, espaces de travail Log Analytics et composants Application Insights.

Accédez au portail Azure. Dans le menu de votre ressource se trouve un élément de menu appelé **Isolement réseau** sur le côté gauche. Cette page contrôle à la fois les réseaux qui peuvent atteindre la ressource via une liaison privée, et si d’autres réseaux peuvent y accéder ou non.


> [!NOTE]
> À partir de septembre 2021, l’isolement réseau sera appliqué de façon stricte. Les ressources définies pour bloquer les requêtes à partir de réseaux publics et qui ne sont pas connectées à un réseau privé (via un objet AMPLS) n’acceptent plus de requêtes d’un réseau.

![Isolement réseau LA](./media/private-link-security/ampls-network-isolation.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Étendues de liaison privée Azure Monitor connectées
Ici, vous pouvez examiner et configurer les connexions de la ressource aux étendues de liaisons privées Azure Monitor. La connexion aux étendues (objets AMPLS) permet au trafic provenant du réseau virtuel connecté à chaque objet AMPLS d’atteindre la ressource. Elle a le même effet si la connexion partait de l’étendue, comme c’est le cas dans [Connecter des ressources Azure Monitor](#connect-azure-monitor-resources). 

Pour ajouter une nouvelle connexion, sélectionnez **Ajouter**, puis choisissez l’étendue de liaison privée Azure Monitor. Sélectionnez **Appliquer** pour la connecter. Votre ressource peut se connecter à cinq objets AMPLS, comme mentionné dans [Considérer les limites des objets AMPLS](./private-link-design.md#consider-ampls-limits).

### <a name="virtual-networks-access-configuration---managing-access-from-outside-of-private-links-scopes"></a>Configuration d’accès des réseaux virtuels – La gestion de l’accès depuis l’extérieur des étendues de liaisons privées
Les paramètres de la partie inférieure de cette page contrôlent l'accès depuis les réseaux publics, c'est-à-dire les réseaux non connectés aux étendues répertoriées.

Si vous définissez **Autoriser l’accès au réseau public pour l’ingestion** sur **Non**, les clients (machines, kits SDK, etc.) situés hors des étendues connectées ne peuvent pas charger les données ni envoyer les journaux à la ressource.

Si vous définissez **Autoriser l’accès au réseau public pour les requêtes** sur **Non**, les clients (machines, kits SDK, etc.) situés hors des étendues connectées ne peuvent pas interroger de données dans la ressource. Ces données incluent l’accès aux journaux, aux métriques et au flux de métriques en temps réel, ainsi que des expériences basées sur des classeurs, des tableaux de bord, des expériences client basées sur l’API de requête, des insights dans le portail Azure, etc. Les expériences qui s’exécutent en dehors du portail Azure et qui interrogent des données Log Analytics doivent également être exécutées au sein du réseau virtuel connecté par liaison privée.


## <a name="use-apis-and-command-line"></a>Utiliser des API et une ligne de commande

Vous pouvez automatiser le processus décrit précédemment à l’aide de modèles Azure Resource Manager, d’interfaces REST et de ligne de commande.

### <a name="create-and-manage-azure-monitor-private-link-scopes-ampls"></a>Créer et gérer des étendues de liens privés Azure Monitor (AMPLS)
Pour créer et gérer des étendues de liens privés, utilisez l’[API REST](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) ou [Azure CLI (AZ Monitor private-link-scope)](/cli/azure/monitor/private-link-scope).

#### <a name="create-ampls-with-open-access-modes---cli-example"></a>Créer AMPLS avec les modes d’accès ouverts - exemple CLI
La commande CLI ci-dessous crée une nouvelle ressource AMPLS nommée « My-Scope », avec les modes d’accès requête et ingestion définis sur Open.
```
az resource create -g "my-resource-group" --name "my-scope" --api-version "2021-07-01-preview" --resource-type Microsoft.Insights/privateLinkScopes --properties "{\"accessModeSettings\":{\"queryAccessMode\":\"Open\", \"ingestionAccessMode\":\"Open\"}}"
```

#### <a name="create-ampls-with-mixed-access-modes---powershell-example"></a>Créer des AMPLS avec des modes d’accès mixtes - exemple PowerShell
Le script PowerShell ci-dessous crée une nouvelle ressource AMPLS nommée « My-Scope », avec le mode d’accès à la requête ouvert, mais les modes d’accès d’ingestion ont la valeur PrivateOnly (ce qui signifie qu’elle autorise la réception uniquement aux ressources dans AMPLS).

```
# scope details
$scopeSubscriptionId = "ab1800bd-ceac-48cd-...-..."
$scopeResourceGroup = "my-resource-group"
$scopeName = "my-scope"
$scopeProperties = @{
    accessModeSettings = @{
        queryAccessMode     = "Open"; 
        ingestionAccessMode = "PrivateOnly"
    } 
}

# login
Connect-AzAccount

# select subscription
Select-AzSubscription -SubscriptionId $scopeSubscriptionId

# create private link scope resource
$scope = New-AzResource -Location "Global" -Properties $scopeProperties -ResourceName $scopeName -ResourceType "Microsoft.Insights/privateLinkScopes" -ResourceGroupName $scopeResourceGroup -ApiVersion "2021-07-01-preview" -Force
```

#### <a name="create-ampls---azure-resource-manager-template-arm-template"></a>Créer un modèle AMPLS - Azure Resource Manager (modèle ARM)
Le modèle Azure Resource Manager ci-dessous crée les éléments suivants :
* Une étendue de liaison privée (AMPLS) nommée « my-scope », avec des modes d’accès de requête et d’ingestion définis sur « Open ».
* Un espace de travail Log Analytics nommé « my-workspace »
* Une ressource délimitée de l’AMPLS « my-scope », nommée « my-workspace-connection »

> [!NOTE]
> Veillez à utiliser une nouvelle version de l’API (2021-07-01-preview ou ultérieure) pour la création de l’objet d’étendue de liaison privée (saisissez « microsoft.insights/privatelinkscopes » ci-dessous). Le modèle ARM documenté dans le passé utilisait une ancienne version de l’API, ce qui se traduit par une AMPLS définie avec QueryAccessMode="Open" et IngestionAccessMode="PrivateOnly".

```
{
    "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
    "contentVersion": "1.0.0.0",
    "parameters": {
        "private_link_scope_name": {
            "defaultValue": "my-scope",
            "type": "String"
        },
        "workspace_name": {
            "defaultValue": "my-workspace",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.insights/privatelinkscopes",
            "apiVersion": "2021-07-01-preview",
            "name": "[parameters('private_link_scope_name')]",
            "location": "global",
            "properties": {
                "accessModeSettings":{
                    "queryAccessMode":"Open",
                    "ingestionAccessMode":"Open"
                }
            }
        },
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-10-01",
            "name": "[parameters('workspace_name')]",
            "location": "westeurope",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "publicNetworkAccessForIngestion": "Enabled",
                "publicNetworkAccessForQuery": "Enabled"
            }
        },
        {
            "type": "microsoft.insights/privatelinkscopes/scopedresources",
            "apiVersion": "2019-10-17-preview",
            "name": "[concat(parameters('private_link_scope_name'), '/', concat(parameters('workspace_name'), '-connection'))]",
            "dependsOn": [
                "[resourceId('microsoft.insights/privatelinkscopes', parameters('private_link_scope_name'))]",
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            ],
            "properties": {
                "linkedResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            }
        }
    ]
}
```

### <a name="set-ampls-access-modes---powershell-example"></a>Créer des modes d’accès AMPLS - exemple PowerShell
Pour définir les indicateurs du mode d’accès sur votre AMPLS, vous pouvez utiliser le script PowerShell suivant. Le script suivant définit les indicateurs sur Open. Pour utiliser le mode privé uniquement, utilisez la valeur « PrivateOnly ».

Laissez environ 10 minutes pour que la mise à jour des modes d’accès AMPLS prenne effet.

```
# scope details
$scopeSubscriptionId = "ab1800bd-ceac-48cd-...-..."
$scopeResourceGroup = "my-resource-group-name"
$scopeName = "my-scope"

# login
Connect-AzAccount

# select subscription
Select-AzSubscription -SubscriptionId $scopeSubscriptionId

# get private link scope resource
$scope = Get-AzResource -ResourceType Microsoft.Insights/privateLinkScopes -ResourceGroupName $scopeResourceGroup -ResourceName $scopeName -ApiVersion "2021-07-01-preview"

# set access mode settings
$scope.Properties.AccessModeSettings.QueryAccessMode = "Open";
$scope.Properties.AccessModeSettings.IngestionAccessMode = "Open";
$scope | Set-AzResource -Force
```

### <a name="set-resource-access-flags"></a>Définir les drapeaux d'accès aux ressources
Pour gérer l’indicateur d’accès au réseau sur votre espace de travail ou composant, utilisez les indicateurs `[--ingestion-access {Disabled, Enabled}]` et `[--query-access {Disabled, Enabled}]` sur les [espaces de travail Log Analytics ](/cli/azure/monitor/log-analytics/workspace) ou les [composants Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component).


## <a name="review-and-validate-your-private-link-setup"></a>Réviser et valider la configuration de votre liaison privée

### <a name="reviewing-your-endpoints-dns-settings"></a>Examen des paramètres DNS de votre point de terminaison
Le point de terminaison privé que vous avez créé devrait maintenant avoir cinq zones DNS configurées :

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net
* privatelink-blob-core-windows-net

> [!NOTE]
> Chacune de ces zones mappe des points de terminaison Azure Monitor spécifiques à des adresses IP privées à partir du pool d’adresses IP du réseau virtuel. Les adresses IP affichées dans les images ci-dessous ne sont que des exemples. À la place, votre configuration devrait afficher des adresses IP privées de votre propre réseau.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Cette zone couvre les points de terminaison globaux qu’Azure Monitor utilise, ce qui signifie que ces points de terminaison servent les demandes en prenant en compte toutes les ressources, pas une ressource spécifique. Cette zone devrait avoir des points de terminaison mappés pour :
* `in.ai` : point de terminaison d’ingestion Application Insights (une entrée mondiale et une entrée régionale)
* `api` : point de terminaison d’API Application Insights et Log Analytics
* `live` : point de terminaison de métriques en direct Application Insights
* `profiler` : point de terminaison du profileur Application Insights
* `snapshot` : point de terminaison de capture instantanée Application Insights[![Capture d’écran de la zone DNS privée monitor-azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Cette zone couvre le mappage spécifique de l’espace de travail aux points de terminaison OMS. Vous devriez voir une entrée pour chaque espace de travail lié à l’AMPLS connecté avec ce point de terminaison privé.
[![Capture d’écran de la zone DNS privée oms-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Cette zone couvre le mappage spécifique de l’espace de travail aux points de terminaison ODS, point de terminaison d’ingestion de Log Analytics. Vous devriez voir une entrée pour chaque espace de travail lié à l’AMPLS connecté avec ce point de terminaison privé.
[![Capture d’écran de la zone DNS privée ods-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
Cette zone couvre le mappage spécifique de l’espace de travail aux points de terminaison d’automatisation du service agent. Vous devriez voir une entrée pour chaque espace de travail lié à l’AMPLS connecté avec ce point de terminaison privé.
[![Capture d’écran de l’agent de zone DNS privée svc-azure-automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
Cette zone configure la connectivité au compte de stockage des packs de solutions des agents globaux. Par son intermédiaire, les agents peuvent télécharger des packs de solutions (également appelés packs d’administration) nouveaux ou mis à jour. Une seule entrée est requise pour gérer les agents Log Analytics, quel que soit le nombre d’espaces de travail utilisés.
[![Capture d’écran de la zone DNS privée blob-core-windows-net.](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> Cette entrée est uniquement ajoutée aux configurations Private Link créées à partir du 19 avril 2021 (ou de juin 2021 sur les clouds souverains Azure).


### <a name="validating-you-are-communicating-over-a-private-link"></a>Validation que vous communiquez sur une liaison privée
* Pour valider le fait que vos demandes sont désormais envoyées via le point de terminaison privé, vous pouvez les examiner avec un outil de suivi du réseau ou même votre navigateur. Par exemple, lorsque vous tentez d’interroger votre espace de travail ou votre application, assurez-vous que la demande est envoyée à l’adresse IP privée mappée au point de terminaison de l’API, dans cet exemple, *172.17.0.9*.

    Remarque : certains navigateurs peuvent utiliser d’autres paramètres DNS (consultez [Paramètres DNS du navigateur](./private-link-design.md#browser-dns-settings)). Vérifiez que vos paramètres DNS s’appliquent.

* Pour vous assurer que votre espace de travail ou composant ne reçoivent pas de demandes de réseaux publics (non connectés via AMPLS), définissez les indicateurs de requête et d’ingestion publique de la ressource sur *Non*, comme expliqué dans [Configurer l’accès à vos ressources](#configure-access-to-your-resources).

* À partir d’un client sur votre réseau protégé, utilisez `nslookup` sur l’un des points de terminaison répertoriés dans vos zones DNS. La demande devrait être résolue par votre serveur DNS en adresses IP privées mappées au lieu des adresses IP publiques utilisées par défaut.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [stockage privé](private-storage.md) pour les journaux personnalisés et les clés gérées par le client (CMK)
- Découvrir [Private Link pour Automation](../../automation/how-to/private-link-security.md)