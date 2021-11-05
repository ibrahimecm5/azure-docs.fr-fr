---
title: Configurer un réseau virtuel pour l’injection express du runtime d’intégration Azure-SSIS Integration Runtime
description: Découvrez comment configurer un réseau virtuel pour l’injection express du runtime d’intégration Azure-SSIS Integration Runtime.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1d904e33f830f36e41acd0afd32dc7dd7dfaf2c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097634"
---
# <a name="express-virtual-network-injection-method-preview"></a>Méthode d’injection express au réseau virtuel (préversion)

Quand vous utilisez SSIS (SQL Server Integration Services) dans ADF (Azure Data Factory), il existe deux méthodes pour joindre votre runtime d’intégration Azure-SSIS Integration Runtime à un réseau virtuel : standard et express. Si vous utilisez la méthode express, vous devez configurer votre réseau virtuel pour qu’il réponde aux impératifs suivants : 

- Vérifiez que *Microsoft.Batch* est un fournisseur de ressources inscrit dans l’abonnement Azure qui dispose du réseau virtuel auquel votre runtime d’intégration Azure-SSIS IR doit être joint. Pour obtenir des instructions détaillées, consultez la section [Inscrire Azure Batch en tant que fournisseur de ressources](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

- Vérifiez qu’il n’existe aucun verrou de ressource dans votre réseau virtuel.

- Sélectionnez le sous-réseau approprié dans le réseau virtuel auquel votre runtime d’intégration Azure-SSIS IR doit être joint. Pour plus d’informations, consultez la section [Sélectionner un sous-réseau](#subnet) ci-dessous.

- Vérifiez que l’utilisateur qui crée Azure-SSIS IR se voit octroyer les autorisations RBAC (contrôle d’accès en fonction du rôle) nécessaires pour rejoindre le réseau/sous-réseau virtuel.  Pour plus d’informations, consultez la section [Sélectionner des autorisations de réseau virtuel](#perms) ci-dessous.

Selon votre scénario spécifique, vous pouvez éventuellement configurer les éléments suivants :

- Si vous souhaitez utiliser une adresse IP publique statique pour le trafic sortant de votre runtime d’intégration Azure-SSIS IR, consultez la section [Configurer une adresse IP publique statique](#ip) ci-dessous.

- Si vous souhaitez utiliser votre propre serveur DNS (Domain Name System) dans le réseau virtuel, consultez la section [Configurer un serveur DNS personnalisé](#dns) ci-dessous.

- Si vous souhaitez utiliser un groupe NSG (groupe de sécurité réseau) pour limiter le trafic sortant sur le sous-réseau, consultez la section [Configurer un groupe NSG](#nsg) ci-dessous.

- Si vous souhaitez utiliser des routages UDR (routages définis par l’utilisateur) pour auditer/inspecter le trafic sortant, consultez la section [Configurer des routages UDR](#udr) ci-dessous.

Ce diagramme montre les connexions requises pour votre runtime d’intégration Azure-SSIS :

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-express.png" alt-text="Diagramme qui montre les connexions nécessaires à Azure-SSIS IR dans le cadre d’une injection express au réseau virtuel.":::

## <a name="select-a-subnet"></a><a name="subnet"></a>Sélectionner un sous-réseau

Pour activer l’injection express au réseau virtuel, vous devez sélectionner le sous-réseau approprié auquel votre runtime d’intégration Azure-SSIS IR doit être joint :

- Ne sélectionnez pas GatewaySubnet, car il est dédié aux passerelles de réseau virtuel.

- Vérifiez que le sous-réseau sélectionné dispose d’adresses IP pour au moins deux fois votre numéro de nœud Azure-SSIS IR. Celles-ci nous sont nécessaires pour éviter les interruptions durant le déploiement des patchs/mises à niveau de votre runtime d’intégration Azure-SSIS IR. Azure réserve également certaines adresses IP qui ne peuvent pas être utilisées dans chaque sous-réseau. La première et la dernière adresse IP sont réservées pour des raisons de conformité du protocole, tandis que trois autres adresses sont réservées aux services Azure. Pour plus d’informations, consultez la section [Restrictions liées aux adresses IP de sous-réseau](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

- N’utilisez pas un sous-réseau exclusivement occupé par d’autres services Azure (par exemple Azure SQL Managed Instance, App Service, etc.).

- Le sous-réseau sélectionné doit être délégué au service *Microsoft.Batch/batchAccounts*. Pour plus d’informations, consultez l’article [Vue d’ensemble de la délégation de sous-réseau](../virtual-network/subnet-delegation-overview.md). Pour obtenir des instructions détaillées, consultez la section [Déléguer un sous-réseau à Azure Batch](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet).

## <a name="select-virtual-network-permissions"></a><a name="perms"></a>Sélectionner des autorisations de réseau virtuel

Afin d’activer l’injection express au réseau virtuel, l’utilisateur qui crée Azure-SSIS IR doit se voir octroyer les autorisations RBAC nécessaires pour permettre la jonction au réseau/sous-réseau virtuel. Deux options s'offrent à vous :

- Utilisez le rôle *Contributeur de réseaux* intégré. Ce rôle inclut l’autorisation _Microsoft.Network/\*_ , mais dispose d’une étendue plus large que nécessaire.

- Créez un rôle personnalisé qui inclut uniquement l’autorisation *Microsoft.Network/virtualNetworks/subnets/join/action* nécessaire.

Pour obtenir des instructions détaillées, consultez la section [Octroyer des autorisations de réseau virtuel](azure-ssis-integration-runtime-virtual-network-configuration.md#grantperms).

## <a name="configure-a-static-public-ip-address"></a><a name="ip"></a>Configurer une adresse IP publique statique

Si vous souhaitez utiliser une adresse IP publique statique pour le trafic sortant de votre runtime d’intégration Azure-SSIS IR afin de l’autoriser dans vos pare-feu, vous devez configurer la [NAT (traduction d’adresses réseau) virtuelle](../virtual-network/nat-gateway/nat-overview.md).

## <a name="configure-a-custom-dns-server"></a><a name="dns"></a>Configurer un serveur DNS personnalisé

Si vous souhaitez utiliser votre propre serveur DNS dans le réseau virtuel pour résoudre vos noms d’hôtes privés, vérifiez qu’il peut également résoudre les noms d’hôtes Azure globaux (par exemple le Stockage Blob Azure nommé `<your storage account>.blob.core.windows`.).

Nous vous recommandons de configurer votre propre serveur DNS pour transférer les requêtes DNS non résolues vers l’adresse IP des programmes de résolution récursifs Azure (*168.63.129.16*).

Pour plus d’informations, consultez la section [Résolution de noms de serveurs DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

Pour le moment, si vous souhaitez qu’Azure-SSIS IR utilise votre propre serveur DNS, vous devez lui appliquer une configuration personnalisée standard en suivant les étapes ci-dessous :

1. Téléchargez un script de configuration personnalisée ([main.cmd](https://expressvnet.blob.core.windows.net/customsetup/main.cmd)) + son fichier associé ([setupdnsserver.ps1](https://expressvnet.blob.core.windows.net/customsetup/setupdnsserver.ps1)).

1. Remplacez « your-dns-server-ip » (ip-du-serveur-dns) dans main.cmd par l’adresse IP de votre propre serveur DNS.

1. Chargez main.cmd + setupdnsserver.ps1 dans votre propre conteneur Azure Storage Blob pour une configuration personnalisée standard, puis entrez son URI SAS au moment du provisionnement d’Azure-SSIS IR. Consultez l’article [Personnalisation d’Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

> [!NOTE]
> Utilisez un FQDN (nom de domaine complet) pour votre nom d’hôte privé (par exemple, utilisez `<your_private_server>.contoso.com` au lieu de `<your_private_server>`). Vous pouvez également utiliser une configuration personnalisée standard dans Azure-SSIS IR pour ajouter automatiquement votre propre suffixe DNS (par exemple `contoso.com`) à un nom de domaine non complet à une seule étiquette, et le convertir en FQDN avant de l’utiliser dans des requêtes DNS. Pour plus d’informations, consultez la section [Exemples de configurations personnalisées standard](how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

## <a name="configure-an-nsg"></a><a name="nsg"></a>Configurer un groupe NSG

Si vous souhaitez utiliser un groupe NSG sur le sous-réseau auquel votre runtime d’intégration Azure-SSIS IR est joint, autorisez le trafic sortant suivant : 

| Protocole de transfert | Source | Ports source | Destination | Ports de destination | Commentaires | 
|--------------------|--------|--------------|-------------|-------------------|----------| 
| TCP | *VirtualNetwork* | * | *DataFactoryManagement* | *443* | Nécessaire pour permettre à Azure-SSIS IR d’accéder aux services ADF.<br/><br/>Son trafic sortant utilise uniquement le point de terminaison public ADF pour le moment. | 
| TCP | *VirtualNetwork* | * | *Sql/VirtualNetwork* | *1433, 11000-11999* | (Facultatif) Obligatoire uniquement si vous utilisez le serveur Azure SQL Database/Managed Instance pour héberger le catalogue SSIS (SSISDB).<br/><br/>Si votre serveur Azure SQL Database/Managed Instance est configuré avec un point de terminaison public/point de terminaison de service de réseau virtuel, utilisez l’étiquette de service *Sql* en tant que destination.<br/><br/>Si votre serveur Azure SQL Database/Managed Instance est configuré avec un point de terminaison privé, utilisez l’étiquette de service *VirtualNetwork* en tant que destination.<br/><br/>Si votre stratégie de connexion de serveur a la valeur *Proxy* au lieu de *Redirect*, seul le port *1433* est nécessaire. | 
| TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *443* | (Facultatif) Obligatoire uniquement si vous utilisez le conteneur Azure Storage Blob pour stocker vos scripts/fichiers de configuration personnalisée standard.<br/><br/>Si votre instance de Stockage Azure est configurée avec un point de terminaison public/point de terminaison de service de réseau virtuel, utilisez l’étiquette de service *Storage* en tant que destination.<br/><br/>Si votre instance de Stockage Azure est configurée avec un point de terminaison privé, utilisez l’étiquette de service *VirtualNetwork* en tant que destination. | 
| TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *445* | (Facultatif) Obligatoire uniquement si vous devez accéder à Azure Files.<br/><br/>Si votre instance de Stockage Azure est configurée avec un point de terminaison public/point de terminaison de service de réseau virtuel, utilisez l’étiquette de service *Storage* en tant que destination.<br/><br/>Si votre instance de Stockage Azure est configurée avec un point de terminaison privé, utilisez l’étiquette de service *VirtualNetwork* en tant que destination. | 

## <a name="configure-udrs"></a><a name="udr"></a>Configurer des routages UDR

Si vous souhaitez auditer/inspecter le trafic sortant de votre runtime d’intégration Azure-SSIS IR, vous pouvez utiliser des [routages définis par l’utilisateur (UDR)](../virtual-network/virtual-networks-udr-overview.md) pour le rediriger vers une appliance de pare-feu locale via le tunneling forcé d’[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) qui annonce une route BGP (Border Gateway Protocol) *0.0.0.0/0* vers le réseau virtuel, vers une appliance virtuelle réseau configurée en tant que pare-feu ou vers le service [Pare-feu Azure](../firewall/overview.md).

Comme indiqué dans nos conseils d’aide à la section [Configurer un groupe NSG](#nsg) ci-dessus, vous devez implémenter des règles similaires sur l’appliance/le service de pare-feu pour autoriser le trafic sortant en provenance de votre runtime d’intégration Azure-SSIS IR :

- Si vous utilisez le pare-feu Azure :
  - Vous devez ouvrir le port *443* pour autoriser le trafic TCP sortant ayant l’étiquette de service *DataFactoryManagement* en tant que destination.

  - Si vous utilisez un serveur Azure SQL Database/Managed Instance pour héberger SSISDB, vous devez ouvrir les ports *1433, 11000-11999* pour autoriser le trafic TCP sortant ayant l’étiquette de service *Sql/VirtualNetwork* en tant que destination.

  - Si vous utilisez le conteneur Azure Storage Blob pour stocker vos scripts/fichiers de configuration personnalisée standard, vous devez ouvrir le port *443* pour autoriser le trafic TCP sortant ayant l’étiquette de service *Storage/VirtualNetwork* en tant que destination.

  - Si vous devez accéder à Azure Files, vous devez ouvrir le port *445* pour autoriser le trafic TCP sortant ayant l’étiquette de service *Storage/VirtualNetwork* en tant que destination.

- Si vous utilisez une autre appliance/un autre service de pare-feu :
  - Vous devez ouvrir le port *443* pour autoriser le trafic TCP sortant ayant l’adresse *0.0.0.0/0* ou le FQDN spécifique à l’environnement Azure suivant en tant que destination :

    | Environnement Azure | FQDN |
    |-------------------|------|
    | <b>Azure public</b> | _\*.frontend.clouddatahub.net_ |
    | <b>Azure Government</b> | _\*.frontend.datamovement.azure.us_ |
    | <b>Azure China 21Vianet</b> | _\*.frontend.datamovement.azure.cn_ |

  - Si vous utilisez un serveur Azure SQL Database/Managed Instance pour héberger SSISDB, vous devez ouvrir les ports *1433, 11000-11999* pour autoriser le trafic TCP sortant ayant l’adresse *0.0.0.0/0* ou le FQDN de votre serveur Azure SQL Database/Managed Instance en tant que destination.

  - Si vous utilisez le conteneur Azure Storage Blob pour stocker vos scripts/fichiers de configuration personnalisée standard, vous devez ouvrir le port *443* pour autoriser le trafic TCP sortant ayant l’adresse *0.0.0.0/0* ou votre FQDN Stockage Blob Azure en tant que destination.

  - Si vous devez accéder à Azure Files, vous devez ouvrir le port *445* pour autoriser le trafic TCP sortant ayant l’adresse *0.0.0.0/0* ou votre FQDN Azure Files en tant que destination.

## <a name="next-steps"></a>Étapes suivantes

- [Joindre Azure-SSIS IR à un réseau virtuel via l’IU d’ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Joindre Azure-SSIS IR à un réseau virtuel via Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 

- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise le serveur Azure SQL Database pour héberger SSISDB. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il fournit des instructions sur l’utilisation d’un serveur Azure SQL Database configuré avec un point de terminaison de service de réseau virtuel/une règle de pare-feu IP/un point de terminaison privé ou Azure SQL Managed Instance joignant un réseau virtuel pour héberger SSISDB. Il vous montre comment joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
