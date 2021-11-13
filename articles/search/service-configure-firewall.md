---
title: Configurer un pare-feu IP
titleSuffix: Azure Cognitive Search
description: Configurez des stratégies de contrôle IP pour restreindre l’accès à votre service Recherche cognitive Azure à des adresses IP spécifiques.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: e403a71525a8400f47dee01c14ac192c13ab3826
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223180"
---
# <a name="configure-an-ip-firewall-for-azure-cognitive-search"></a>Configurer un pare-feu IP pour le service Recherche cognitive Azure

Recherche cognitive Azure prend en charge les règles IP pour l’accès entrant via un pare-feu, de manière similaire aux règles IP que vous trouvez dans un groupe de sécurité réseau d’un réseau virtuel Azure. En tirant profit des règles IP, vous pouvez restreindre l’accès au service de recherche à un ensemble approuvé de machines et de services cloud. L’accès aux données stockées dans votre service de recherche à partir des ensembles approuvés de machines et de services impose toujours à l’appelant de présenter un jeton d’autorisation valide.

Vous pouvez définir des règles IP dans le portail Azure, comme indiqué dans cet article, pour les services de recherche provisionnés au niveau De base et supérieur. Vous pouvez également utiliser l’[API REST de gestion version 2020-03-13](/rest/api/searchmanagement/), [Azure PowerShell](/powershell/module/az.search) ou [Azure CLI](/cli/azure/search).

<a id="configure-ip-policy"></a> 

## <a name="set-ip-ranges-in-azure-portal"></a>Définir des plages d’adresses IP dans le portail Azure

Pour définir la stratégie de contrôle d’accès IP dans le portail Azure, accédez à la page du service Recherche cognitive Azure, puis sélectionnez **Réseau** dans le volet de navigation de gauche. La connectivité réseau des points de terminaison doit être définie à **Accès public**. Si votre connectivité est définie à **Accès privé** ou **Accès privé partagé**, vous pouvez uniquement accéder à votre service de recherche via un point de terminaison privé.

:::image type="content" source="media/service-configure-firewall/azure-portal-firewall.png" alt-text="Capture d’écran montrant comment configurer le pare-feu IP dans le Portail Azure" border="true":::

Le Portail Azure permet de spécifier des adresses IP et des plages d’adresses IP au format CIDR. Un exemple de notation CIDR est 8.8.8.0/24, qui représente les adresses IP comprises entre 8.8.8.0 et 8.8.8.255.

Une fois que vous avez activé la stratégie de contrôle d’accès IP pour votre service Recherche cognitive Azure, toutes les demandes adressées au plan de données à partir d’ordinateurs ne figurant pas dans la liste des plages d’adresses IP autorisées sont rejetées. 

## <a name="allow-access-from-azure-portal"></a>Autoriser l’accès à partir du portail Azure

Par défaut, quand des règles IP sont configurées, certaines fonctionnalités du portail Azure sont désactivées. Vous pouvez voir et gérer les informations au niveau du service. Toutefois, l’accès au portail par les index, les indexeurs et les autres ressources de niveau supérieur est restreint.

Pour conserver l’administration du service via le portail, sélectionnez l’option « Autoriser l’accès » sous **Exceptions**. Vous pouvez également utiliser l’[extension VS Code](https://aka.ms/vscode-search) pour gérer le contenu.

## <a name="allow-access-from-your-client"></a>Autoriser l’accès à partir de votre client

Les applications clientes qui envoient (push) les requêtes d’indexation et d’interrogation au service de recherche doivent être représentées dans une plage d’adresses IP. Sur Azure, vous pouvez généralement déterminer l’adresse IP en effectuant un test ping sur le FQDN d’un service (par exemple `ping <your-search-service-name>.search.windows.net` retourne l’adresse IP d’un service de recherche). 

La fourniture d’adresses IP aux clients permet de garantir que la requête n’est pas rejetée purement et simplement. Toutefois, pour permettre un accès réussi au contenu et aux opérations, une autorisation est également nécessaire. Utilisez l’une des méthodologies suivantes pour authentifier votre requête :

+ [Authentification par clé](search-security-api-keys.md), où une clé API d’administrateur ou d’interrogation est fournie dans la requête
+ [Autorisation basée sur les rôles](search-security-rbac.md), où l’appelant est membre d’un rôle de sécurité d’un service de recherche, et où l’[application inscrite présente un jeton OAuth](search-howto-aad.md) d’Azure Active Directory.

### <a name="rejected-requests"></a>Requêtes rejetées

Quand les requêtes proviennent d’adresses IP qui ne figurent pas dans la liste verte, une réponse générique **403 Interdit** est retournée sans détails supplémentaires.

## <a name="next-steps"></a>Étapes suivantes

Si votre application cliente est une application web statique sur Azure, découvrez comment déterminer sa plage d’adresses IP pour l’inclure dans une règle de pare-feu IP d’un service de recherche.

> [!div class="nextstepaction"]
> [Adresses IP entrantes et sortantes dans Azure App Service](../app-service/overview-inbound-outbound-ips.md)