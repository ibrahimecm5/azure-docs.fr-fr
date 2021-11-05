---
title: Joindre un runtime d'intégration Azure-SSIS à un réseau virtuel
description: Apprenez à joindre un runtime d’intégration Azure-SSIS à un réseau virtuel.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9766ed3ed870e41c44a2dd84bbaad578ec54509c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087872"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network"></a>Joindre un runtime d'intégration Azure-SSIS à un réseau virtuel

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Lorsque vous utilisez SQL Server Integration Services (SSIS) dans Azure Data Factory (ADF), vous devez joindre votre runtime d'intégration (IR) Azure-SSIS à un réseau virtuel dans les scénarios suivants :

- Vous souhaitez accéder à des ressources/magasins de données locaux à partir de packages SSIS en cours d’exécution sur votre Azure-SSIS IR sans configurer ou gérer un runtime d'intégration auto-hébergé en tant que proxy.

- Vous souhaitez utiliser un serveur Microsoft Azure SQL Database configuré avec un point de terminaison privé, une règle de pare-feu IP ou un point de terminaison de service de réseau virtuel, ou Azure SQL Managed Instance qui joint un réseau virtuel pour héberger la base de données de catalogue SSIS (SSISDB).

- Vous souhaitez accéder à des ressources/magasins de données Azure configurés avec un point de terminaison privé, une règle de pare-feu IP ou un point de terminaison de service de réseau virtuel à partir de packages SSIS qui s’exécutent sur votre Azure-SSIS IR.

- Vous souhaitez accéder à d’autres ressources/magasins de données cloud configurés avec une règle de pare-feu IP à partir de packages SSIS exécutés sur votre Azure-SSIS IR.

ADF vous permet de joindre votre Azure-SSIS IR à un réseau virtuel créé via le modèle de déploiement classique ou via le modèle de déploiement Azure Resource Manager.

> [!IMPORTANT]
> Le réseau virtuel classique est déconseillé. Par conséquent, utilisez plutôt le réseau virtuel Azure Resource Manager. Si vous utilisez déjà le réseau virtuel classique, basculez dès que possible vers le réseau virtuel Azure Resource Manager.

Le tutoriel [Configurer Azure-SSIS IR pour rejoindre un réseau virtuel](tutorial-deploy-ssis-virtual-network.md) présente les étapes minimales avec la méthode d’injection de réseau virtuel Express par le biais de l’interface utilisateur Portail Azure/ADF. Cet article et d’autres, tels que l’article [Configurer un réseau virtuel pour injecter Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md), complètent le tutoriel et décrivent toutes les étapes facultatives :

- Si vous utilisez la méthode d’injection de réseau virtuel standard.
- Si vous utilisez le réseau virtuel classique.
- Si vous apportez vos propres adresses IP publiques statiques (BYOIP) pour Azure-SSIS IR.
- Si vous utilisez votre propre serveur DNS (Domain Name System).
- Si vous utilisez un groupe de sécurité réseau (NSG).
- Si vous utilisez des itinéraires définis par l’utilisateur (UDR).
- Si vous utilisez un Azure-SSIS IR personnalisé.
- Si vous utilisez Azure PowerShell pour approvisionner votre Azure-SSIS IR.

## <a name="access-to-on-premises-data-stores"></a>Accéder aux magasins de données locaux

Si vos packages SSIS accèdent à des magasins de données locaux, vous pouvez joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel qui est connecté au réseau local. Vous pouvez également configurer et gérer un IR auto-hébergé comme proxy pour votre Azure-SSIS IR. Pour plus d’informations, consultez [Configurer un IR auto-hébergé comme proxy pour Azure-SSIS IR](self-hosted-integration-runtime-proxy-ssis.md). 

Lorsque vous joignez votre Azure-SSIS IR à un réseau virtuel, rappelez-vous ces points importants : 

- Si aucun réseau virtuel n’est connecté à votre réseau local, créez d’abord un réseau virtuel [Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) auquel votre runtime d’intégration Azure-SSIS IR pourra se joindre. Ensuite, configurez une [connexion de passerelle VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) de site à site ou une connexion [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) de ce réseau virtuel vers votre réseau local. 

- Si un réseau virtuel Azure Resource Manager est déjà connecté à votre réseau local, au même emplacement que votre Azure-SSIS IR, vous pouvez joindre votre IR à ce réseau virtuel. 

- Si un réseau virtuel classique est déjà connecté à votre réseau local, à un autre emplacement que celui de votre runtime d’intégration Azure-SSIS IR, vous devez créer un [réseau virtuel Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) pour le runtime d’intégration Azure-SSIS IR à joindre. Ensuite, configurez une connexion du type [réseau virtuel classique vers Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Si un réseau virtuel Azure Resource Manager est déjà connecté à votre réseau local, à un autre emplacement que celui de votre runtime d’intégration Azure-SSIS IR, vous devez commencer par créer un [réseau virtuel Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) pour le runtime d’intégration Azure-SSIS IR à joindre. Ensuite, configurez une connexion [du type réseau virtuel Azure Resource Manager vers Azure Resource Manager](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). 

## <a name="hosting-ssisdb-in-azure-sql-database-server-or-managed-instance"></a>Hébergement de SSISDB dans un serveur Microsoft Azure SQL Database ou Managed Instance

Si vous hébergez SSISDB dans un serveur Microsoft Azure SQL Database configuré avec un point de terminaison de service de réseau virtuel, assurez-vous de joindre votre Azure-SSIS IR aux mêmes réseau virtuel et sous-réseau virtuels.

Si vous hébergez SSISDB dans Azure SQL Managed Instance qui rejoint un réseau virtuel, assurez-vous de joindre votre Azure-SSIS IR au même réseau virtuel mais dans un sous-réseau différent de celui de l’instance gérée. Pour joindre votre Azure-SSIS IR à un réseau virtuel différent de celui de l’instance gérée, nous recommandons le peering de réseau virtuel (qui est limité à la même région) ou une connexion de réseau virtuel à réseau virtuel. Pour plus d’informations, consultez [Connecter votre application à Azure SQL Managed Instance](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-data-stores"></a>Accès aux magasins de données Azure

Si vos packages SSIS accèdent à des ressources/magasins de données Azure qui prennent en charge des [points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) et que vous souhaitez sécuriser l’accès à ces ressources dans Azure-SSIS IR, vous pouvez joindre votre Azure-SSIS IR à un sous-réseau de réseau virtuel configuré pour des points de terminaison de service de réseau virtuel, puis ajouter une règle de réseau virtuel sur le pare-feu des ressources appropriées pour autoriser l’accès à partir du même sous-réseau.

## <a name="access-to-other-cloud-data-stores"></a>Accès à d’autres magasins de données cloud

Si vos packages SSIS accèdent à d’autres magasins de données/ressources cloud qui n’autorisent que certaines adresses IP publiques statiques et que vous souhaitez sécuriser l’accès à ces ressources dans Azure-SSIS IR, vous pouvez associer des [adresses IP publiques](../virtual-network/virtual-network-public-ip-address.md) à votre Azure-SSIS IR tout en le joignant à un réseau virtuel, puis ajouter une règle de pare-feu IP sur le pare-feu des ressources appropriées pour autoriser l’accès à partir de ces adresses IP. Pour ce faire, vous disposez de deux options : 

- Lorsque vous créez Azure-SSIS IR, vous pouvez apporter vos propres adresses IP publiques statiques et les associer à votre Azure-SSIS IR via [l’interface utilisateur ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md) ou [Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md). Seule la connectivité Internet sortante de votre Azure-SSIS IR utilisera ces adresses IP publiques et les autres ressources dans le sous-réseau ne les utiliseront pas.

- Vous pouvez également configurer une [traduction d’adresses réseau (NAT) de réseau virtuel](../virtual-network/nat-gateway/nat-overview.md) dans le sous-réseau que votre Azure-SSIS IR rejoint et toute la connectivité Internet sortante de ce sous-réseau utilisera une adresse IP publique spécifiée.

Dans tous les cas, le réseau virtuel peut être déployé uniquement par le biais du modèle de déploiement Azure Resource Manager.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer un réseau virtuel pour injecter Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Méthode d’injection de réseau virtuel Express](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Méthode d’injection de réseau virtuel standard](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Joindre Azure-SSIS IR à un réseau virtuel via une interface utilisateur ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Joindre Azure-SSIS IR à un réseau virtuel via Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 

- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise un serveur Microsoft Azure SQL Database pour héberger SSISDB. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il fournit des instructions sur l’utilisation d’un serveur Microsoft Azure SQL Database configuré avec un point de terminaison de service de réseau virtuel, une règle de pare-feu IP ou un point de terminaison privé, ou d’Azure SQL Managed Instance qui rejoint un réseau virtuel pour héberger SSISDB. Il explique comment joindre votre Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
