---
title: Utiliser un serveur DNS personnalisé
titleSuffix: Azure Machine Learning
description: Comment configurer un serveur DNS personnalisé pour qu’il fonctionne avec un espace de travail Azure Machine Learning et un point de terminaison privé.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 10/29/2021
ms.topic: how-to
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: f683d7192d73cc04b23b58243ba27ccc62afb010
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468629"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Utilisation de votre espace de travail avec un serveur DNS personnalisé

Lorsque vous utilisez un espace de travail Azure Machine Learning avec un point de terminaison privé, il existe [plusieurs manières de gérer la résolution de noms DNS](../private-link/private-endpoint-dns.md). Par défaut, Azure gère automatiquement la résolution de noms pour votre espace de travail et votre point de terminaison privé. Si vous __utilisez votre propre serveur DNS personnalisé__, vous devez créer manuellement des entrées DNS ou utiliser des redirecteurs conditionnels pour l’espace de travail.

> [!IMPORTANT]
> Cet article explique comment obtenir les noms de domaine complets (FQDN) et les adresses IP de ces entrées si vous souhaitez inscrire manuellement des enregistrements DNS dans votre solution DNS. Il fournit également des recommandations sur l’architecture, indiquant comment configurer votre solution DNS personnalisée pour résoudre automatiquement les FQDN en adresses IP correctes. Cet article ne fournit pas d’informations sur la configuration des enregistrements DNS pour ces éléments. Pour savoir comment ajouter des enregistrements, consultez la documentation de votre logiciel DNS.

> [!WARNING]
> Si vous utilisez un point de terminaison privé pour d’autres services Azure sur lesquels s’appuie l’espace de travail Machine Learning, comme Stockage Azure, Azure Key Vault ou Azure Container Registry. Vous devez également créer des entrées DNS ou utiliser un redirecteur conditionnel pour ces services. Pour plus d’informations, consultez [Configuration DNS des points de terminaison privés Azure](/azure/private-link/private-endpoint-dns).

> [!TIP]
> Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :
>
> * [Présentation du réseau virtuel](how-to-network-security-overview.md)
> * [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
> * [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
> * [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
> * [Activer les fonctionnalités de Studio](how-to-enable-studio-virtual-network.md)
> * [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)
## <a name="prerequisites"></a>Prérequis

- Réseau virtuel Azure qui utilise [votre propre serveur DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

- Un espace de travail Azure Machine Learning avec un point de terminaison privé. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

- Connaissances sur l’utilisation de l’[isolement réseau pendant la formation et l’inférence](./how-to-network-security-overview.md).

- Connaissances sur la [configuration de la zone DNS d’un point de terminaison privé Azure](../private-link/private-endpoint-dns.md)

- Connaissance du [DNS privé Azure](../dns/private-dns-privatednszone.md)

- Éventuellement, [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="automated-dns-server-integration"></a>Intégration de serveur DNS automatisée

### <a name="introduction"></a>Introduction

Il existe deux architectures courantes pour l’utilisation de l’intégration de serveur DNS automatisée avec Azure Machine Learning :

* Un [serveur DNS personnalisé hébergé dans un réseau virtuel Azure](#dns-vnet).
* Un [serveur DNS personnalisé hébergé localement](#dns-on-premises), connecté à Azure Machine Learning par le biais d’ExpressRoute.

Vous pouvez utiliser ces exemples comme points de référence même s’ils ne correspondent pas précisément à votre architecture. Les deux exemples d’architectures incluent des procédures de résolution des problèmes vous permettant d’identifier des composants qui pourraient être mal configurés.

Une autre option consiste à modifier le fichier `hosts` sur le client qui se connecte au réseau virtuel Azure (VNet) contenant votre espace de travail. Pour plus d’informations, consultez la section [Fichier hosts](#hosts).
### <a name="workspace-dns-resolution-path"></a>Chemin de résolution DNS de l’espace de travail

L’accès à un espace de travail Azure Machine Learning donné par le biais d’une liaison privée implique une communication avec les noms de domaine complets listés ci-dessous (FQDN de l’espace de travail) :

**Régions publiques Azure** :
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.azureml.ms```
- ```<compute instance name>.<region the workspace was created in>.instances.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.azure.net```

**Régions Azure China 21Vianet** :
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.cn```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.chinacloudapi.cn```

**Régions Azure US Government** :
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.us```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.usgovcloudapi.net```

Les noms de domaine complets sont résolus en noms canoniques (CNAME), appelés FQDN de liaison privée de l’espace de travail, comme suit :

**Régions publiques Azure** :
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.azure.net```

**Régions Azure Chine** :
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.chinacloudapi.cn```

**Régions Azure US Government** :
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.usgovcloudapi.net```

Les FQDN sont résolus en adresses IP de l’espace de travail Azure Machine Learning dans cette région. Toutefois, la résolution des noms de domaine complets de liaison privée de l’espace de travail peut être remplacée en utilisant un serveur DNS personnalisé hébergé dans le réseau virtuel. Pour obtenir un exemple de cette architecture, consultez l’exemple [Serveur DNS personnalisé hébergé dans un réseau virtuel](#example-custom-dns-server-hosted-in-vnet).

## <a name="manual-dns-server-integration"></a>Intégration de serveur DNS manuelle

Cette section traite des domaines complets pour lesquels des enregistrements A doivent être créés dans un serveur DNS et de l’adresses IP sur laquelle la valeur de l’enregistrement A doit être définie.

### <a name="retrieve-private-endpoint-fqdns"></a>Récupérer les FQDN de point de terminaison privé

#### <a name="azure-public-region"></a>Région publique Azure

La liste suivante contient les noms de domaine complets (FQDN) utilisés par votre espace de travail s’il se trouve dans le cloud public Azure :

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.azure.net`

    > [!NOTE]
    > Le nom de l’espace de travail pour ce nom de domaine complet peut être tronqué. Cette troncation a pour but de limiter la longueur de `ml-<workspace-name, truncated>-<region>-<workspace-guid>` à 63 caractères.
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > * Les instances de calcul sont accessibles uniquement à partir du réseau virtuel.
    > * L’adresse IP de ce nom de domaine complet n’est **pas** l’adresse IP de l’instance de calcul. Utilisez plutôt l’adresse IP privée du point de terminaison privé de l’espace de travail (l’adresse IP des entrées `*.api.azureml.ms`).

#### <a name="azure-china-region"></a>Région Azure Chine

Les noms de domaine complets suivants sont destinés aux régions Azure Chine :

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > Le nom de l’espace de travail pour ce nom de domaine complet peut être tronqué. Cette troncation a pour but de limiter la longueur de `ml-<workspace-name, truncated>-<region>-<workspace-guid>` à 63 caractères.
    
* `<instance-name>.<region>.instances.ml.azure.cn`

   * L’adresse IP de ce nom de domaine complet n’est **pas** l’adresse IP de l’instance de calcul. Utilisez plutôt l’adresse IP privée du point de terminaison privé de l’espace de travail (l’adresse IP des entrées `*.api.azureml.ms`).

#### <a name="azure-us-government"></a>Azure US Government

Les noms de domaine complets suivants sont destinés aux régions Azure US Government :

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.us`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.us`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.usgovcloudapi.net`

    > [!NOTE]
    > Le nom de l’espace de travail pour ce nom de domaine complet peut être tronqué. Cette troncation a pour but de limiter la longueur de `ml-<workspace-name, truncated>-<region>-<workspace-guid>` à 63 caractères.
* `<instance-name>.<region>.instances.ml.azure.us`
    > * L’adresse IP de ce nom de domaine complet n’est **pas** l’adresse IP de l’instance de calcul. Utilisez plutôt l’adresse IP privée du point de terminaison privé de l’espace de travail (l’adresse IP des entrées `*.api.azureml.ms`).

### <a name="find-the-ip-addresses"></a>Rechercher les adresses IP

Pour rechercher les adresses IP internes des noms de domaine complets dans le réseau virtuel, utilisez l’une des méthodes suivantes :

> [!NOTE]
> Les noms de domaine complets et les adresses IP seront différents en fonction de votre configuration. Par exemple, la valeur GUID dans le nom de domaine sera spécifique à votre espace de travail.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Pour connaître l’ID de l’interface réseau du point de terminaison privé, utilisez la commande suivante :

    ```azurecli
    az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'networkInterfaces[*].id' --output table
    ```

1. Pour récupérer les informations sur l’adresse IP et le nom de domaine complet, utilisez la commande suivante. Remplacez `<resource-id>` par l’ID obtenu à l’étape précédente :

    ```azurecli
    az network nic show --ids <resource-id> --query 'ipConfigurations[*].{IPAddress: privateIpAddress, FQDNs: privateLinkConnectionProperties.fqdns}'
    ```

    Le résultat ressemble au texte suivant :

    ```json
    [
        {
            "FQDNs": [
            "fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms",
            "fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms"
            ],
            "IPAddress": "10.1.0.5"
        },
        {
            "FQDNs": [
            "ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net"
            ],
            "IPAddress": "10.1.0.6"
        }
    ]
    ```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre __espace de travail__ Azure Machine Learning.
1. Dans la section __Paramètres__, sélectionnez __Connexions des points de terminaison privés__.
1. Sélectionnez le lien dans la colonne __Point de terminaison privé__ qui s’affiche.
1. La liste des noms de domaine complets (FQDN) et les adresses IP du point de terminaison privé de l’espace de travail se trouvent au bas de la page.

    :::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Liste des noms de domaine complets dans le portail":::

    > [!TIP]
    > Si les paramètres DNS n’apparaissent pas en bas de la page, utilisez le lien __Configuration DNS__ sur le côté gauche de la page pour afficher les noms de domaine complets (FQDN).

---

Les informations retournées par toutes les méthodes sont les mêmes : une liste du nom de domaine complet et de l’adresse IP privée pour les ressources. Le tableau suivant montre des exemples d’IP à partir du cloud public Azure :

| FQDN | Adresse IP |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

Le tableau suivant montre des exemples d’IP à partir de régions Azure Chine :

| FQDN | Adresse IP |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |

Le tableau suivant montre des exemples d’IP à partir de régions Azure US Government :

| FQDN | Adresse IP |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.us` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.us` | `10.1.0.5` |
| `ml-mype-plt-usgovvirginia-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.usgovcloudapi.net` | `10.1.0.6` |

<a id='dns-vnet'></a>

### <a name="create-a-records-in-custom-dns-server"></a>Créer des enregistrements A dans un serveur DNS personnalisé

Après avoir récupéré la liste des FQDN et adresses IP correspondantes, commencez à créer des enregistrements A dans le serveur DNS configuré. Reportez-vous à la documentation de votre serveur DNS pour savoir comment créer des enregistrements A. Notez qu’il est recommandé de créer une seule zone pour l’intégralité du FQDN et de créer l’enregistrement A à la racine de la zone.

## <a name="example-custom-dns-server-hosted-in-vnet"></a>Exemple : Serveur DNS personnalisé hébergé dans un réseau virtuel

Cette architecture utilise la topologie de réseau virtuel courante hub-and-spoke. Un réseau virtuel contient le serveur DNS et un autre contient le point de terminaison privé vers l’espace de travail Azure Machine Learning et les ressources associées. Une route valide doit exister entre les deux réseaux virtuels, par exemple, par le biais d’une série de réseaux virtuels appairés.

:::image type="content" source="./media/how-to-custom-dns/custom-dns-topology.svg" alt-text="Diagramme d’un DNS personnalisé hébergé dans une topologie Azure":::

Les étapes suivantes décrivent le fonctionnement de cette topologie :

1. **Créer une zone DNS privée et la lier au réseau virtuel du serveur DNS** :

    Pour garantir le bon fonctionnement d’une solution DNS personnalisée avec votre espace de travail Azure Machine Learning, la première étape consiste à créer deux zones DNS privées avec, pour racine, les domaines suivants :

    **Régions publiques Azure** :
    - ```privatelink.api.azureml.ms```
    - ```privatelink.notebooks.azure.net```
    
    **Régions Azure Chine** :
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Régions Azure US Government** :
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    Après sa création, la zone DNS privée doit être liée au réseau virtuel du serveur DNS (le réseau virtuel qui contient le serveur DNS).

    Une zone DNS privée remplace la résolution de noms pour tous les noms dans l’étendue de la racine de la zone. Ce remplacement s’applique à tous les réseaux virtuels auxquels la zone DNS privée est liée. Par exemple, si une zone DNS privée avec `privatelink.api.azureml.ms` pour racine est liée au réseau virtuel foo, toutes les ressources du réseau virtuel foo qui tentent de résoudre `bar.workspace.westus2.privatelink.api.azureml.ms` recevront un enregistrement listé dans la zone `privatelink.api.azureml.ms`.

    Toutefois, les enregistrements listés dans les zones DNS privées sont retournés uniquement aux appareils qui résolvent les domaines en utilisant l’adresse IP par défaut du serveur virtuel Azure DNS. Le serveur DNS personnalisé va donc résoudre les domaines pour tous les appareils présents dans votre topologie réseau. Cependant, le serveur DNS personnalisé devra résoudre les domaines liés à Azure Machine Learning par rapport à l’adresse IP du serveur virtuel Azure DNS.

2. **Créer un point de terminaison privé avec intégration au DNS privé, ciblant la zone DNS privée liée au réseau virtuel du serveur DNS** :

    L’étape suivante consiste à créer un point de terminaison privé vers l’espace de travail Azure Machine Learning. Le point de terminaison privé cible les deux zones DNS privées créées à l’étape 1. Ceci garantit que toutes les communications avec l’espace de travail sont effectuées par le biais du point de terminaison privé dans le réseau virtuel Azure Machine Learning.

    > [!IMPORTANT]
    > Le point de terminaison privé doit avoir l’intégration à un DNS privé activée pour que cet exemple fonctionne correctement.

3. **Créer un redirecteur conditionnel dans le serveur DNS pour la redirection vers Azure DNS** : 

    Vous allez à présent créer un redirecteur conditionnel vers le serveur virtuel Azure DNS. Le redirecteur conditionnel garantit que le serveur DNS interroge toujours l’adresse IP du serveur virtuel Azure DNS pour obtenir les FQDN associés à votre espace de travail. Cela signifie que le serveur DNS retourne l’enregistrement correspondant à partir de la zone DNS privée.

    Les zones concernées par la redirection conditionnelle sont listées ci-dessous. L’adresse IP du serveur virtuel Azure DNS est 168.63.129.16 :

    **Régions publiques Azure** :
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.ms```
    
    **Régions Azure Chine** :
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```
    
    **Régions Azure US Government** :
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > Les étapes de configuration du serveur DNS ne sont pas abordées ici, car de nombreuses solutions DNS peuvent être utilisées comme serveur DNS personnalisé. Reportez-vous à la documentation de votre solution DNS pour savoir comment configurer correctement la redirection conditionnelle.

4. **Résoudre le domaine de l’espace de travail** :

    La configuration est à présent effectuée. Désormais, tout client utilisant le serveur DNS pour la résolution de noms et disposant d’une route vers le point de terminaison privé Azure Machine Learning peut accéder à l’espace de travail.
    Le client commence par interroger le serveur DNS pour obtenir l’adresse des FQDN suivants :

    **Régions publiques Azure** :
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    **Régions Azure Chine** :
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    **Régions Azure US Government** :
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

5. **Azure DNS résout de manière récursive le domaine de l’espace de travail en CNAME** :

    Le serveur DNS résout les FQDN de l’étape 4 à partir d’Azure DNS. Azure DNS répond avec l’un des domaines listés à l’étape 1.

6. **Le serveur DNS résout de manière récursive l’enregistrement CNAME du domaine de l’espace de travail à partir d’Azure DNS** :

    Le serveur DNS procède à la résolution du CNAME reçu à l’étape 5 de manière récursive. Comme un redirecteur conditionnel a été configuré à l’étape 3, le serveur DNS envoie la requête à l’adresse IP du serveur virtuel Azure DNS pour la résolution.

7. **Azure DNS retourne les enregistrements de la zone DNS privée** :

    Les enregistrements correspondants stockés dans les zones DNS privées sont retournés au serveur DNS, ce qui signifie que le serveur virtuel Azure DNS retourne les adresses IP du point de terminaison privé.

8. **Le serveur DNS personnalisé résout le nom de domaine de l’espace de travail en adresse du point de terminaison privé** :

    Le serveur DNS personnalisé retourne à présent les adresses IP du point de terminaison privé au client de l’étape 4. Ceci garantit que tout le trafic vers l’espace de travail Azure Machine Learning s’effectue par le biais du point de terminaison privé.

#### <a name="troubleshooting"></a>Résolution des problèmes

Si vous ne pouvez pas accéder à l’espace de travail à partir d’une machine virtuelle ou si des travaux échouent sur des ressources de calcul dans le réseau virtuel, effectuez les étapes suivantes pour en identifier la cause :

1. **Localiser les FQDN de l’espace de travail sur le point de terminaison privé** :

    Accédez au portail Azure avec l’un des liens suivants :
    - [Régions publiques Azure](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Régions Azure Chine](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Régions Azure US Government](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    Accédez au point de terminaison privé vers l’espace de travail Azure Machine Learning. Les FQDN de l’espace de travail sont listés sous l’onglet « vue d’ensemble ».

1. **Accéder à la ressource de calcul dans la topologie de réseau virtuel** :

    Accédez à une ressource de calcul dans la topologie de réseau virtuel Azure. Ceci nécessitera probablement l’accès à une machine virtuelle dans un réseau virtuel appairé au réseau virtuel hub. 

1. **Résoudre les FQDN de l’espace de travail** :

    Ouvrez une invite de commandes, l’interpréteur de commandes ou PowerShell. Ensuite, pour chacun des FQDN de l’espace de travail, exécutez la commande suivante :

    `nslookup <workspace FQDN>`
        
    Chaque commande nslookup doit retourner l’une des deux adresses IP privées sur le point de terminaison privé vers l’espace de travail Azure Machine Learning. Si ce n’est pas le cas, cela signifie que la solution DNS personnalisée est configurée de façon incorrecte.

    Causes possibles :
    - La ressource de calcul qui exécute les commandes de résolution des problèmes n’utilise pas le serveur DNS pour la résolution DNS.
    - Les zones DNS privées choisies à la création du point de terminaison privé ne sont pas liées au réseau virtuel du serveur DNS.
    - Les redirecteurs conditionnels vers l’IP du serveur virtuel Azure DNS n’ont pas été configurés correctement.

<a id='dns-on-premises'></a>

## <a name="example-custom-dns-server-hosted-on-premises"></a>Exemple : Serveur DNS personnalisé hébergé localement

Cette architecture utilise la topologie de réseau virtuel courante hub-and-spoke. ExpressRoute est utilisé pour la connexion à partir de votre réseau local au réseau virtuel hub. Le serveur DNS personnalisé est hébergé localement. Un réseau virtuel distinct contient le point de terminaison privé vers l’espace de travail Azure Machine Learning et les ressources associées. Cette topologie implique la présence d’un autre réseau virtuel hébergeant un serveur DNS qui peut envoyer des requêtes à l’adresse IP du serveur virtuel Azure DNS.

:::image type="content" source="./media/how-to-custom-dns/custom-dns-express-route.svg" alt-text="Diagramme de la topologie avec DNS personnalisé hébergé localement":::

Les étapes suivantes décrivent le fonctionnement de cette topologie :

1. **Créer une zone DNS privée et la lier au réseau virtuel du serveur DNS** :

    Pour garantir le bon fonctionnement d’une solution DNS personnalisée avec votre espace de travail Azure Machine Learning, la première étape consiste à créer deux zones DNS privées avec, pour racine, les domaines suivants :
    
    **Régions publiques Azure** :
    - ``` privatelink.api.azureml.ms```
    - ``` privatelink.notebooks.azure.net```
    
    **Régions Azure Chine** :
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Régions Azure US Government** :
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    Après sa création, la zone DNS privée doit être liée au réseau virtuel du serveur DNS (le réseau virtuel qui contient le serveur DNS). 

    > [!NOTE]
    > Le serveur DNS dans le réseau virtuel est séparé du serveur DNS local.

    Une zone DNS privée remplace la résolution de noms pour tous les noms dans l’étendue de la racine de la zone. Ce remplacement s’applique à tous les réseaux virtuels auxquels la zone DNS privée est liée. Par exemple, si une zone DNS privée avec `privatelink.api.azureml.ms` pour racine est liée au réseau virtuel foo, toutes les ressources du réseau virtuel foo qui tentent de résoudre `bar.workspace.westus2.privatelink.api.azureml.ms` recevront un enregistrement listé dans la zone privatelink.api.azureml.m.

    Toutefois, les enregistrements listés dans les zones DNS privées sont retournés uniquement aux appareils qui résolvent les domaines en utilisant l’adresse IP par défaut du serveur virtuel Azure DNS. L’adresse IP du serveur virtuel Azure DNS est valide uniquement dans le contexte d’un réseau virtuel. Quand un serveur DNS local est utilisé, il ne peut pas interroger l’adresse IP du serveur virtuel Azure DNS pour récupérer les enregistrements.

    Pour contourner ce problème, créez un serveur DNS intermédiaire dans un réseau virtuel. Ce serveur DNS peut interroger l’adresse IP du serveur virtuel Azure DNS pour récupérer les enregistrements de n’importe quelle zone DNS privée liée au réseau virtuel.

    Même si le serveur DNS local résout les domaines pour tous les appareils présents dans votre topologie réseau, il résout les domaines liés à Azure Machine Learning par rapport au serveur DNS. Le serveur DNS résout ces domaines à partir de l’adresse IP du serveur virtuel Azure DNS.

2. **Créer un point de terminaison privé avec intégration au DNS privé, ciblant la zone DNS privée liée au réseau virtuel du serveur DNS** :

    L’étape suivante consiste à créer un point de terminaison privé vers l’espace de travail Azure Machine Learning. Le point de terminaison privé cible les deux zones DNS privées créées à l’étape 1. Ceci garantit que toutes les communications avec l’espace de travail sont effectuées par le biais du point de terminaison privé dans le réseau virtuel Azure Machine Learning.

    > [!IMPORTANT]
    > Le point de terminaison privé doit avoir l’intégration à un DNS privé activée pour que cet exemple fonctionne correctement.

3. **Créer un redirecteur conditionnel dans le serveur DNS pour la redirection vers Azure DNS** :

    Vous allez à présent créer un redirecteur conditionnel vers le serveur virtuel Azure DNS. Le redirecteur conditionnel garantit que le serveur DNS interroge toujours l’adresse IP du serveur virtuel Azure DNS pour obtenir les FQDN associés à votre espace de travail. Cela signifie que le serveur DNS retourne l’enregistrement correspondant à partir de la zone DNS privée.

    Les zones concernées par la redirection conditionnelle sont listées ci-dessous. L’adresse IP du serveur virtuel Azure DNS est 168.63.129.16.

    **Régions publiques Azure** :
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```     
    
    **Régions Azure Chine** :
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```

    **Régions Azure US Government** :
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > Les étapes de configuration du serveur DNS ne sont pas abordées ici, car de nombreuses solutions DNS peuvent être utilisées comme serveur DNS personnalisé. Reportez-vous à la documentation de votre solution DNS pour savoir comment configurer correctement la redirection conditionnelle.

4. **Créer un redirecteur conditionnel dans le serveur DNS local pour la redirection vers le serveur DNS** :

    Vous allez à présent créer un redirecteur conditionnel vers le serveur DNS dans le réseau virtuel du serveur DNS. Ce redirecteur concerne les zones listées à l’étape 1. Ceci s’apparente à l’étape 3. Cependant, au lieu d’effectuer la redirection vers l’adresse IP du serveur virtuel Azure DNS, le serveur DNS local ciblera l’adresse IP du serveur DNS. Étant donné que le serveur DNS local n’est pas dans Azure, il n’est pas en mesure de résoudre directement les enregistrements en zones DNS privées. Dans le cas présent, le serveur DNS redirige les requêtes du serveur DNS local vers l’IP du serveur virtuel Azure DNS. Ceci permet au serveur DNS local de récupérer les enregistrements dans les zones DNS privées liées au réseau virtuel du serveur DNS. 

    Les zones concernées par la redirection conditionnelle sont listées ci-dessous. Les adresses IP vers lesquelles la redirection doit être effectuée sont celles de vos serveurs DNS :

    **Régions publiques Azure** :
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```
    
    **Régions Azure Chine** :
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```
    
    **Régions Azure US Government** :
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > Les étapes de configuration du serveur DNS ne sont pas abordées ici, car de nombreuses solutions DNS peuvent être utilisées comme serveur DNS personnalisé. Reportez-vous à la documentation de votre solution DNS pour savoir comment configurer correctement la redirection conditionnelle.

5. **Résoudre le domaine de l’espace de travail** :

    La configuration est à présent effectuée. Tout client utilisant le serveur DNS local pour la résolution de noms et disposant d’une route vers le point de terminaison privé Azure Machine Learning peut accéder à l’espace de travail.

    Le client commence par interroger le serveur DNS local pour obtenir l’adresse des FQDN suivants :

    **Régions publiques Azure** :
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    **Régions Azure Chine** :
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    **Régions Azure US Government** :
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

6. **Le serveur DNS local résout de manière récursive le domaine de l’espace de travail** :

    Le serveur DNS local résout les noms de domaine complets de l’étape 5 à partir du serveur DNS. En raison de l’existence d’un redirecteur conditionnel (étape 4), le serveur DNS local envoie la demande au serveur DNS pour la résolution.

7. **Le serveur DNS résout le domaine de l’espace de travail en CNAME à partir d’Azure DNS** :

    Le serveur DNS résout les FQDN de l’étape 5 à partir d’Azure DNS. Azure DNS répond avec l’un des domaines listés à l’étape 1.

8. **Le serveur DNS local résout de manière récursive l’enregistrement CNAME du domaine de l’espace de travail à partir du serveur DNS** :

    Le serveur DNS local procède à la résolution du CNAME reçu à l’étape 7 de manière récursive. Comme un redirecteur conditionnel a été configuré à l’étape 4, le serveur DNS local envoie la requête au serveur DNS pour la résolution.

9. **Le serveur DNS résout de manière récursive l’enregistrement CNAME du domaine de l’espace de travail à partir d’Azure DNS** :

    Le serveur DNS procède à la résolution du CNAME reçu à l’étape 7 de manière récursive. Comme un redirecteur conditionnel a été configuré à l’étape 3, le serveur DNS envoie la requête à l’adresse IP du serveur virtuel Azure DNS pour la résolution.

10. **Azure DNS retourne les enregistrements de la zone DNS privée** :

    Les enregistrements correspondants stockés dans les zones DNS privées sont retournés au serveur DNS, ce qui signifie que le serveur virtuel Azure DNS retourne les adresses IP du point de terminaison privé.

11. **Le serveur DNS local résout le nom de domaine de l’espace de travail en adresse du point de terminaison privé** :

    La requête du serveur DNS local au serveur DNS effectuée à l’étape 8 retourne finalement les adresses IP associées au point de terminaison privé vers l’espace de travail Azure Machine Learning. Ces adresses IP sont retournées au client d’origine, qui communique désormais avec l’espace de travail Azure Machine Learning sur le point de terminaison privé configuré à l’étape 1.

<a id="hosts"></a>
## <a name="example-hosts-file"></a>Exemple : fichier hosts

Le fichier `hosts` est un document texte que Linux, macOS et Windows utilisent tous pour remplacer la résolution de noms pour l’ordinateur local. Le fichier contient une liste d’adresses IP et le nom d’hôte correspondant. Quand l’ordinateur local tente de résoudre un nom d’hôte, si ce dernier est listé dans le fichier `hosts`, le nom est résolu en l’adresse IP correspondante.

> [!IMPORTANT]
> Le fichier `hosts` remplace uniquement la résolution de noms pour l’ordinateur local. Si vous souhaitez utiliser un fichier `hosts` avec plusieurs ordinateurs, vous devez le modifier individuellement sur chaque ordinateur.

Le tableau suivant liste l’emplacement du fichier `hosts` :

| Système d’exploitation | Emplacement |
| ----- | ----- |
| Linux | `/etc/hosts` |
| macOS | `/etc/hosts` |
| Windows | `%SystemRoot%\System32\drivers\etc\hosts` |

> [!TIP]
> Le nom du fichier est `hosts` sans extension. Quand vous modifiez le fichier, utilisez un accès administrateur. Par exemple, sur Linux ou macOS, vous pouvez utiliser `sudo vi`. Sur Windows, exécutez le Bloc-notes en tant qu’administrateur.

Voici un exemple d’entrées de fichier `hosts` pour Azure Machine Learning :

```
# For core Azure Machine Learning hosts
10.1.0.5    fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms
10.1.0.5    fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms
10.1.0.6    ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net

# For a compute instance named 'mycomputeinstance'
10.1.0.5    mycomputeinstance.eastus.instances.azureml.ms
```

Pour plus d’informations sur le fichier `hosts`, consultez [https://wikipedia.org/wiki/Hosts_(file)](https://wikipedia.org/wiki/Hosts_(file)).

#### <a name="troubleshooting"></a>Résolution des problèmes

Si, après avoir effectué les étapes ci-dessus, vous ne parvenez pas à accéder à l’espace de travail à partir d’une machine virtuelle ou si des travaux échouent sur des ressources de calcul dans le réseau virtuel contenant le point de terminaison privé vers l’espace de travail Azure Machine Learning, effectuez les étapes suivantes pour essayer d’en identifier la cause.

1. **Localiser les FQDN de l’espace de travail sur le point de terminaison privé** :

    Accédez au portail Azure avec l’un des liens suivants :
    - [Régions publiques Azure](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Régions Azure Chine](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Régions Azure US Government](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    Accédez au point de terminaison privé vers l’espace de travail Azure Machine Learning. Les FQDN de l’espace de travail sont listés sous l’onglet « vue d’ensemble ».

1. **Accéder à la ressource de calcul dans la topologie de réseau virtuel** :

    Accédez à une ressource de calcul dans la topologie de réseau virtuel Azure. Ceci nécessitera probablement l’accès à une machine virtuelle dans un réseau virtuel appairé au réseau virtuel hub. 

1. **Résoudre les FQDN de l’espace de travail** :

    Ouvrez une invite de commandes, l’interpréteur de commandes ou PowerShell. Ensuite, pour chacun des FQDN de l’espace de travail, exécutez la commande suivante :

    `nslookup <workspace FQDN>`
        
    Chaque commande nslookup doit retourner l’une des deux adresses IP privées sur le point de terminaison privé vers l’espace de travail Azure Machine Learning. Si ce n’est pas le cas, cela signifie que la solution DNS personnalisée est configurée de façon incorrecte.

    Causes possibles :
    - La ressource de calcul qui exécute les commandes de résolution des problèmes n’utilise pas le serveur DNS pour la résolution DNS.
    - Les zones DNS privées choisies à la création du point de terminaison privé ne sont pas liées au réseau virtuel du serveur DNS.
    - Les redirecteurs conditionnels du serveur DNS vers l’IP du serveur virtuel Azure DNS n’ont pas été configurés correctement.
    - Les redirecteurs conditionnels du serveur DNS local vers le serveur DNS n’ont pas été configurés correctement.

## <a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :

* [Présentation du réseau virtuel](how-to-network-security-overview.md)
* [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
* [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
* [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
* [Activer les fonctionnalités de Studio](how-to-enable-studio-virtual-network.md)
* [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)

Pour plus d’informations sur l’intégration des points de terminaison privés dans votre configuration DNS, consultez [Configuration DNS du point de terminaison privé Azure](../private-link/private-endpoint-dns.md).

Pour plus d’informations sur le déploiement de modèles avec un nom DNS personnalisé ou la sécurité TLS, consultez [Sécuriser des services web en utilisant TLS](how-to-secure-web-service.md).
