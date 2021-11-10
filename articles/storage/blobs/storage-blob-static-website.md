---
title: Hébergement de sites web statiques dans le service Stockage Azure
description: L’hébergement de sites web statiques dans Stockage Azure constitue une solution évolutive économique pour l’hébergement d’applications web modernes.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 11/04/2021
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: 3cfa4113d0b2307c6683b8a103d3480ed5dfb41c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843175"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hébergement de sites web statiques dans le service Stockage Azure

Vous pouvez servir du contenu statique (fichiers HTML, CSS, JavaScript et images) directement à partir d’un conteneur de stockage nommé *$web*. L’hébergement de votre contenu dans le stockage Azure vous permet d’utiliser des architectures serverless qui incluent [Azure Functions](../../azure-functions/functions-overview.md) et d’autres services Paas (Platform as a service). L’hébergement de site web statique dans le stockage Azure est une option intéressante quand vous n’avez pas besoin d’un serveur web pour restituer du contenu. 

[!INCLUDE [static websites vs static web apps](../../../includes/storage-blob-static-website-vs-static-web-app.md)]

Si vous avez besoin d’un serveur web pour restituer du contenu, vous pouvez utiliser [Azure App Service](https://azure.microsoft.com/services/app-service/).

## <a name="setting-up-a-static-website"></a>Configuration d’un site web statique

L’hébergement de site web statique est une fonctionnalité que vous devez activer sur le compte de stockage.

Pour activer l’hébergement de site web statique, sélectionnez le nom de votre fichier par défaut puis, le cas échéant, fournissez un chemin à une page 404 personnalisée. Si un conteneur de stockage d’objets blob nommé **$web** n’existe pas déjà dans le compte, il est créé pour vous. Ajoutez les fichiers de votre site à ce conteneur.

Pour obtenir des instructions détaillées, consultez [Héberger un site web statique dans le stockage Azure](storage-blob-static-website-how-to.md).

![Métrique des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Les fichiers présents dans le conteneur **$web** respectent la casse ; ils sont pris en charge par le biais de requêtes d’accès anonyme et sont disponibles uniquement par l’intermédiaire d’opérations de lecture.

## <a name="uploading-content"></a>Téléchargement de contenu

Vous pouvez utiliser un de ces outils pour charger du contenu sur le conteneur **$web** :

> [!div class="checklist"]
> - [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> - [Module Azure PowerShell](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> - [AZCopy](../common/storage-use-azcopy-v10.md)
> - [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> - [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> - [Extension Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) et la [démonstration vidéo de Channel 9](https://channel9.msdn.com/Shows/Docs-Azure/Deploy-static-website-to-Azure-from-Visual-Studio-Code/player)

## <a name="viewing-content"></a>Affichage du contenu

Les utilisateurs peuvent afficher le contenu du site dans un navigateur en utilisant l’URL publique du site web. Vous trouvez cette URL à l’aide du portail Azure, de PowerShell ou d’Azure CLI. Voir [Trouver l’URL du site web](storage-blob-static-website-how-to.md#portal-find-url).

Le document d’index, que vous spécifiez lorsque vous activez l’hébergement de site web statique, s’affiche lorsque les utilisateurs ouvrent le site et ne spécifient aucun fichier en particulier (par exemple : `https://contosoblobaccount.z22.web.core.windows.net`).

Si le serveur retourne une erreur 404, et que vous n’avez spécifié aucun document d’erreur lorsque vous avez activé le site web, une page 404 par défaut est retournée à l’utilisateur.

> [!NOTE]
> Le [service Partage des ressources cross-origine (CORS) pour Stockage Azure](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) n’est pas pris en charge avec les sites web statiques.

### <a name="secondary-endpoints"></a>Points de terminaison secondaires

Si vous configurez [la redondance dans une région secondaire](../common/storage-redundancy.md#redundancy-in-a-secondary-region), vous pouvez également accéder au contenu du site web à l’aide d’un point de terminaison secondaire. Dans la mesure où les données sont répliquées de façon asynchrone dans des régions secondaires, les fichiers disponibles au niveau du point de terminaison secondaire ne sont pas toujours synchronisés avec ceux qui sont disponibles sur le point de terminaison principal.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impact de la définition du niveau d’accès public du conteneur web

Vous pouvez modifier le niveau d’accès public du conteneur **$web**, mais cela n’a aucun impact sur le point de terminaison principal du site web statique, car ces fichiers sont pris en charge par le biais de requêtes d’accès anonyme. Cela signifie un accès public (en lecture seule) à tous les fichiers.

La capture d’écran suivante montre la définition du niveau d’accès public dans le portail Azure :

![Capture d’écran illustrant la façon de définir le niveau d’accès public dans le portail](./media/anonymous-read-access-configure/configure-public-access-container.png)

Si le point de terminaison principal du site web statique n’est pas affecté, en revanche, une modification du niveau d’accès public a bien une incidence sur le point de terminaison principal du service Blob.

Par exemple, si vous modifiez le niveau d’accès public du conteneur **$web** en le passant de **Privé (aucun accès anonyme)** à **Blob (accès en lecture anonyme pour les objets Blob uniquement)** , le niveau d’accès public au point de terminaison principal du site web statique `https://contosoblobaccount.z22.web.core.windows.net/index.html` ne change pas.

Toutefois, l’accès public au point de terminaison principal du service Blob `https://contosoblobaccount.blob.core.windows.net/$web/index.html` passe, lui, de privé à public. Désormais, les utilisateurs peuvent ouvrir ce fichier à l’aide, au choix, d’un de ces deux points de terminaison.

La désactivation de l’accès public sur un compte de stockage n’affecte pas les sites web statiques hébergés dans ce compte de stockage. Pour en savoir plus, consultez la section [Configure anonymous public read access for containers and blobs](anonymous-read-access-configure.md) (Configurer l’accès en lecture publique anonyme pour les conteneurs et les objets blob).

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mappage d’un domaine personnalisé à une URL de site web statique

Vous pouvez rendre votre site web statique disponible via un domaine personnalisé.

Stockage Azure prenant en charge votre domaine personnalisé en mode natif, l'accès HTTP est plus facile à activer. Pour activer HTTPS, vous devez utiliser Azure CDN car Stockage Azure ne prend pas encore en charge HTTPS avec les domaines personnalisés en mode natif. Pour obtenir des instructions pas à pas, consultez [Mapper un domaine personnalisé à un point de terminaison du Stockage Blob Azure](storage-custom-domain-name.md).

Si le compte de stockage est configuré pour [exiger un transfert sécurisé](../common/storage-require-secure-transfer.md) via HTTPS, les utilisateurs doivent utiliser le point de terminaison HTTPS.

> [!TIP]
> Envisagez l'hébergement de votre domaine sur Azure. Pour plus d’informations, consultez [Héberger votre domaine dans Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Ajout d’en-têtes HTTP

La fonctionnalité de site web statique n’offre aucun moyen de configurer des en-têtes. Toutefois, vous pouvez utiliser Azure CDN pour ajouter des en-têtes et ajouter (ou remplacer) des valeurs d’en-tête. Consultez [Informations de référence sur le moteur de règles standard pour Azure CDN](../../cdn/cdn-standard-rules-engine-reference.md).

Si vous souhaitez utiliser des en-têtes pour contrôler la mise en cache, consultez [Contrôler le comportement de mise en cache d’Azure CDN avec des règles de mise en cache](../../cdn/cdn-caching-rules.md).

## <a name="multi-region-website-hosting"></a>Hébergement de site web multirégion

Si vous envisagez d’héberger un site web dans plusieurs zones géographiques, nous vous recommandons d’utiliser un [réseau de distribution de contenu](../../cdn/index.yml) pour la mise en cache régionale. Utilisez [Azure Front Door](../../frontdoor/index.yml) si vous souhaitez proposer un contenu différent dans chaque région. Il fournit également des fonctionnalités de basculement. [Azure Traffic Manager](../../traffic-manager/index.yml) n’est pas recommandé si vous envisagez d’utiliser un domaine personnalisé. Des problèmes peuvent survenir en raison de la manière dont le stockage Azure vérifie les noms des domaines personnalisés.

## <a name="permissions"></a>Autorisations

L’autorisation permettant d’activer le site web statique est Microsoft.Storage/storageAccounts/blobServices/clé en écriture ou partagée.  Les rôles intégrés qui fournissent cet accès incluent le Contributeur de compte de stockage.

## <a name="pricing"></a>Tarifs

L’activation de l’hébergement de site web statique est gratuite. Vous êtes facturé uniquement pour le stockage des objets blob qu’utilise votre site et pour les coûts d’exploitation. Pour plus de détails sur les prix du stockage Blob Azure, consultez la [page relative aux tarifs du stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mesures

Vous pouvez activer les métriques sur des pages de site web statique. Une fois les métriques activées, les statistiques de trafic relatives aux fichiers du conteneur **$web** sont signalées dans le tableau de bord des métriques.

Pour activer les métriques sur les pages de votre site web statique, consultez [Activer les métriques sur des pages de site web statique](storage-blob-static-website-how-to.md#metrics).

## <a name="feature-support"></a>Prise en charge des fonctionnalités

Ce tableau montre comment cette fonctionnalité est prise en charge dans votre compte ainsi que l’impact sur la prise en charge lorsque vous activez certaines fonctionnalités.

| Type de compte de stockage                | Stockage Blob (prise en charge par défaut)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Usage général v2 Standard | ![Oui](../media/icons/yes-icon.png) |![Oui](../media/icons/yes-icon.png)              | ![Oui](../media/icons/yes-icon.png) |
| Objets blob de blocs Premium          | ![Oui](../media/icons/yes-icon.png)|![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

## <a name="faq"></a>Forum aux questions

##### <a name="does-the-azure-storage-firewall-work-with-a-static-website"></a>Le pare-feu de stockage Azure fonctionne-t-il avec un site web statique ?

Oui. Les [règles de sécurité réseau](../common/storage-network-security.md) du compte de stockage, y compris les pare-feu basés sur IP et sur réseaux virtuels, sont prises en charge pour le point de terminaison de site web statique et peuvent être utilisées pour protéger votre site web.

##### <a name="do-static-websites-support-azure-active-directory-azure-ad"></a>Les sites web statiques prennent-ils en charge Azure Active Directory (Azure AD) ?

Non. Les sites web statiques ne prennent en charge l’accès en lecture public et anonyme des fichiers que dans le conteneur **$web**.

##### <a name="how-do-i-use-a-custom-domain-with-a-static-website"></a>Comment utiliser un domaine personnalisé avec un site web statique ?

Vous pouvez configurer un [domaine personnalisé](./static-website-content-delivery-network.md) avec un site web statique en utilisant [Azure Content Delivery Network (Azure CDN)](./storage-custom-domain-name.md#map-a-custom-domain-with-https-enabled). Azure CDN fournit une latence faible et cohérente à votre site web partout dans le monde.

##### <a name="how-do-i-use-a-custom-ssl-certificate-with-a-static-website"></a>Comment utiliser un certificat SSL personnalisé avec un site web statique ?

Vous pouvez configurer un certificat [SSL personnalisé](./static-website-content-delivery-network.md) avec un site web statique en utilisant [Azure CDN](./storage-custom-domain-name.md#map-a-custom-domain-with-https-enabled). Azure CDN fournit une latence faible et cohérente à votre site web partout dans le monde.

##### <a name="how-do-i-add-custom-headers-and-rules-with-a-static-website"></a>Comment ajouter des règles et des en-têtes personnalisés avec un site web statique ?

Vous pouvez configurer l’en-tête d’hôte pour un site web statique à l’aide d’[Azure CDN-Verizon Premium](../../cdn/cdn-verizon-premium-rules-engine.md). Nous aimerions avoir votre avis [ici](https://feedback.azure.com/d365community/idea/694b08ef-3525-ec11-b6e6-000d3a4f0f84).

##### <a name="why-am-i-getting-an-http-404-error-from-a-static-website"></a>Pourquoi est-ce que je reçois l’erreur HTTP 404 d’un site web statique ?

Cela peut se produire si vous faites référence à un nom de fichier à l’aide d’une casse incorrecte. Par exemple, `Index.html` au lieu de `index.html`. Les noms de fichiers et les extensions de l’URL d’un site web statique respectent la casse, même s’ils sont traités sur HTTP. Cela peut également se produire si votre point de terminaison Azure CDN n’est pas encore approvisionné. La propagation peut nécessiter jusqu’à 90 minutes après le provisionnement d’un nouveau CDN Azure.

##### <a name="why-isnt-the-root-directory-of-the-website-not-redirecting-to-the-default-index-page"></a>Pourquoi le répertoire racine du site web n’est-il pas redirigé vers la page d’index par défaut ?

Dans le portail Azure, ouvrez la page de configuration du site web statique de votre compte et recherchez le nom et l’extension définis dans le champ **Nom du document d’index**. Assurez-vous que ce nom est exactement le même que le nom du fichier situé dans le conteneur **$web** du compte de stockage. Les noms de fichiers et les extensions de l’URL d’un site web statique respectent la casse, même s’ils sont traités sur HTTP.

## <a name="next-steps"></a>Étapes suivantes

- [Héberger un site web statique dans le stockage Azure](storage-blob-static-website-how-to.md)
- [Mapper un domaine personnalisé à un point de terminaison du Stockage Blob Azure](storage-custom-domain-name.md)
- [Azure Functions](../../azure-functions/functions-overview.md)
- [Azure App Service](../../app-service/overview.md)
- [Générer votre première application web sans serveur](/azure/functions/tutorial-static-website-serverless-api-with-database)
- [Tutoriel : Héberger votre domaine dans Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
