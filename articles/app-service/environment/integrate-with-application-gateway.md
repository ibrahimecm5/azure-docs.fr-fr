---
title: Intégrer à Application Gateway
description: Découvrez comment intégrer une application de votre environnement App Service ILB à une passerelle App Gateway dans cette procédure pas à pas complète.
author: madsd
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 10/12/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: f52f984b1e72d685dae46002e9ae4bac543c068a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447577"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Intégrer votre environnement App Service ILB à l’aide de la passerelle d’application Azure #

L’[environnement Azure App Service][AppServiceEnvironmentoverview] est un déploiement d’Azure App Service dans le sous-réseau du réseau virtuel Azure d’un client. Il peut être déployé avec un point de terminaison externe ou interne pour accéder aux applications. Le déploiement de l’environnement App Service avec un point de terminaison interne est appelé environnement App Service (ASE) avec équilibreur de charge interne (ILB).

Les pare-feu d’applications web permettent de sécuriser vos applications web en inspectant le trafic web entrant pour bloquer les injections SQL, les attaques XSS, les téléchargements de programmes malveillants, les attaques DDoS, ainsi que les autres attaques. Vous pouvez obtenir un appareil WAF à partir de la Place de marché Azure ou vous pouvez utiliser [Azure Application Gateway][appgw].

Azure Application Gateway est une appliance virtuelle qui fournit l’équilibrage de charge de couche 7, le déchargement TLS/SSL et la protection du pare-feu d’applications web (WAF). Elle peut écouter sur une adresse IP publique et acheminer le trafic jusqu’au point de terminaison de votre application. Les informations suivantes expliquent comment intégrer une passerelle applicative configurée avec le WAF à une application dans un environnement App Service ILB.  

L’intégration de la passerelle applicative à l’environnement App Service ILB se fait au niveau de l’application. Quand vous configurez la passerelle applicative avec votre environnement App Service ILB, vous le faites pour des applications spécifiques dans cet environnement. Cette technique permet d’héberger des applications multi-locataires sécurisées dans un environnement App Service ILB unique.  

:::image type="content" source="./media/integrate-with-application-gateway/appgw-highlevel.png" alt-text="Capture d’écran du diagramme d’intégration de haut niveau":::

Lors de cette procédure pas à pas, vous allez :

* Créez une passerelle Azure Application Gateway.
* Configurez la passerelle applicative pour pointer vers une application dans votre environnement App Service ILB.
* Modifier le nom d’hôte DNS public qui pointe vers votre passerelle d’application

## <a name="prerequisites"></a>Prérequis

Pour intégrer votre passerelle applicative à votre environnement App Service ILB, les éléments suivants sont nécessaires :

* Environnement App Service ILB.
* Zone DNS privée pour l’environnement App Service ILB.
* Application exécutée dans l’environnement App Service ILB.
* Nom DNS public à utiliser ultérieurement pour pointer vers votre passerelle applicative.
* Si vous devez utiliser le chiffrement TLS/SSL sur la passerelle applicative, un certificat public valide utilisé pour la liaison à votre passerelle applicative est requis.

### <a name="ilb-app-service-environment"></a>Environnement App Service ILB

Pour plus d’informations sur la création d’un environnement App Service ILB, consultez [Créer un environnement App Service Environment dans le portail Azure][creation] et [Créer un environnement App Service Environment avec ARM][createfromtemplate].

* Une fois l’environnement ASE ILB créé, le domaine par défaut est `<YourAseName>.appserviceenvironment.net`.

    :::image type="content" source="./media/integrate-with-application-gateway/ilb-ase.png" alt-text="Capture d’écran de la vue d’ensemble de l’environnement ILB ASE":::

* Un équilibreur de charge interne est provisionné pour l’accès entrant. Vous pouvez vérifier l’adresse entrante dans les adresses IP sous Paramètres ASE. Vous pouvez créer ultérieurement une zone DNS privée mappée à cette adresse IP.

    :::image type="content" source="./media/integrate-with-application-gateway/ip-addresses.png" alt-text="Capture d’écran montrant l’obtention de l’adresse entrante à partir des paramètres d’adresses IP de l’environnement ILB ASE.":::

### <a name="a-private-dns-zone"></a>Zone DNS privée

Vous avez besoin d’une [zone DNS privée][privatednszone] pour la résolution de noms interne. Créez la zone à l’aide du nom de l’environnement ASE à l’aide des jeux d’enregistrements indiqués dans le tableau suivant (pour obtenir des instructions, consultez [Démarrage rapide : créer une zone Azure DNS privée avec le portail Azure][createprivatednszone]).

| Nom  | Type | Valeur               |
| ----- | ---- | ------------------- |
| *     | Un    | Adresse entrante ASE |
| @     | Un    | Adresse entrante ASE |
| @     | SOA  | Nom DNS ASE        |
| *.scm | Un    | Adresse entrante ASE |

### <a name="app-service-on-ilb-ase"></a>App Service sur un environnement ILB ASE

Vous devez créer un plan App Service et une application dans votre environnement ASE ILB. Lors de la création de l’application dans le portail, sélectionnez votre environnement ASE ILB comme **Région**.

### <a name="a-public-dns-name-to-the-application-gateway"></a>Un nom DNS public à la passerelle applicative

Pour vous connecter à la passerelle applicative à partir d’Internet, vous avez besoin d’un nom de domaine routable. Dans ce cas, j’ai utilisé un nom de domaine routable `asabuludemo.com` et je prévois de me connecter à App Service avec ce nom de domaine `app.asabuludemo.com`. Les adresses IP mappées à ce nom de domaine d’application doivent être définies sur l’adresse IP publique après la création de la passerelle applicative.
Avec un domaine public mappé à la passerelle applicative, vous n’avez pas besoin de configurer un domaine personnalisé dans App Service. Vous pouvez ajouter un nom de domaine personnalisé avec [Domaines App Service](../manage-custom-dns-buy-domain.md#buy-an-app-service-domain). 

### <a name="a-valid-public-certificate"></a>Certificat public valide

Pour améliorer la sécurité, il est recommandé de lier le certificat TLS/SSL pour le chiffrement de la session. Pour lier le certificat TLS/SSL à la passerelle applicative, un certificat public valide avec les informations suivantes est nécessaire. Avec [Certificats App Service](../configure-ssl-certificate.md#start-certificate-order), vous pouvez acheter un certificat TLS/SSL et l’exporter au format .pfx.

| Nom  | Valeur               | Description|
| ----- | ------------------- |------------|
| **Nom commun** |`<yourappname>.<yourdomainname>`, par exemple : `app.asabuludemo.com`  <br/> ou `*.<yourdomainname>`, par exemple : `*.asabuludemo.com` | Un certificat standard ou un [certificat générique](https://wikipedia.org/wiki/Wildcard_certificate) pour la passerelle applicative|
| **Autre nom de l’objet** | `<yourappname>.scm.<yourdomainname>`, par exemple : `app.scm.asabuludemo.com`  <br/>ou `*.scm.<yourdomainname>`, par exemple : `*.scm.asabuludemo.com` |Le réseau SAN qui permet de se connecter au service Kudu App Service. Il s’agit d’un paramètre facultatif si vous ne souhaitez pas publier le service Kudu App Service sur Internet.|

Le fichier de certificat doit disposer d’une clé privée enregistrée au format .pfx qui est importé ultérieurement dans la passerelle applicative.

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Pour la création de la passerelle applicative de base, reportez-vous à la section [Tutoriel : créer une passerelle applicative avec un pare-feu d’applications web à l’aide du portail Azure][Tutorial: Create an application gateway with a Web Application Firewall using the Azure portal].

Dans ce tutoriel, nous allons utiliser le portail Azure pour créer une passerelle applicative avec l’environnement ILB App Service.

Dans le portail Azure, sélectionnez **Nouveau** > **Réseau** > **Application Gateway** pour créer une passerelle applicative.

1. Paramètre de base

    Dans la liste déroulante **Niveau**, vous pouvez sélectionner **Standard V2** ou **WAF V2** pour activer la fonctionnalité **WAF** sur la passerelle applicative. 

2. Paramètre front-end

    Sélectionnez le type d’adresse IP front-end **Public**, **Privé** ou **Les deux**. Si vous définissez **Privé** ou **Les deux**, vous devez attribuer une adresse IP statique dans la plage de sous-réseau de la passerelle applicative. Dans ce cas, nous définissons l’adresse IP sur Public pour le point de terminaison public uniquement.
    
    * Adresse IP publique : vous devez associer une adresse IP publique pour l’accès public de la passerelle applicative. Enregistrez cette adresse IP. Vous devez ajouter un enregistrement dans votre service DNS ultérieurement.
    
        :::image type="content" source="./media/integrate-with-application-gateway/frontends.png" alt-text="Capture d’écran montrant l’obtention de l’adresse IP publique à partir du paramètre front-end de la passerelle applicative.":::

3. Paramètre des serveurs back-end

    Entrez un nom de pool back-end et sélectionnez **App Services** ou **Adresse IP ou nom de domaine complet (FQDN)** dans **Type de cible**. Dans ce cas, nous sélectionnons **App Service**, puis le nom App Service dans la liste déroulante de la cible.
    
    :::image type="content" source="./media/integrate-with-application-gateway/add-backend-pool.png" alt-text="Capture d’écran montrant l’ajout d’un nom du pool back-end dans le paramètre back-end.":::

4. Paramètre de configuration

    Dans paramètre **Configuration**, vous devez ajouter une règle d’acheminement en cliquant sur l’icône **Ajouter une règle d’acheminement**.
    
    :::image type="content" source="./media/integrate-with-application-gateway/configuration.png" alt-text="Capture d’écran de l’ajout d’une règle d’acheminement dans le paramètre de configuration.":::
    
    Vous devez configurer un **écouteur** et des **cibles back-end** dans une règle d’acheminement. Vous pouvez ajouter un écouteur HTTP pour le déploiement de la preuve de concept ou ajouter un écouteur HTTPS pour l’amélioration de la sécurité.
    
    * Pour vous connecter à la passerelle applicative avec le protocole HTTP, vous pouvez créer un écouteur avec les paramètres suivants,
    
        | Paramètre      | Valeur                             | Description                                                  |
        | -------------- | --------------------------------- | ------------------------------------------------------------ |
        | Nom de la règle      | Par exemple : `http-routingrule`    | Nom du routage                                                 |
        | Nom de l’écouteur  | Par exemple : `http-listener`       | Nom de l’écouteur                                                |
        | Adresse IP du front-end    | Public                            | Pour l’accès à Internet, définir sur Public                           |
        | Protocol       | HTTP                             | Ne pas utiliser le chiffrement TLS/SSL                                       |
        | Port           | 80                               | Port HTTP par défaut                                           |
        | Type d’écouteur  | Multisite                        | Autoriser à écouter plusieurs sites sur la passerelle applicative           |
        | Type d’hôte      | Multiple/caractère générique                 | Définissez sur un nom de site web générique ou multiple si le type d’écouteur est défini sur plusieurs sites. |
        | Nom de l’hôte      | Par exemple : `app.asabuludemo.com` | Définir sur un nom de domaine routable pour App Service              |
        
        :::image type="content" source="./media/integrate-with-application-gateway/http-routing-rule.png" alt-text="Capture d’écran de l’écouteur HTTP de la règle d’acheminement de la passerelle applicative.":::
    
    * Pour vous connecter à la passerelle applicative avec le chiffrement TLS/SSL, vous pouvez créer un écouteur avec les paramètres suivants,
    
        | Paramètre      | Valeur                             | Description                                                  |
        | -------------- | --------------------------------- | ------------------------------------------------------------ |
        | Nom de la règle      | Par exemple : `https-routingrule`    | Nom du routage                                                 |
        | Nom de l’écouteur  | Par exemple : `https-listener`       | Nom de l’écouteur                                                |
        | Adresse IP du front-end    | Public                            | Pour l’accès à Internet, définir sur Public                           |
        | Protocol       | HTTPS                             | Utiliser le chiffrement TLS/SSL                                       |
        | Port           | 443                               | Port HTTPS par défaut                                           |
        | Paramètres HTTPS | Téléchargement d'un certificat              | Chargez un certificat qui contient le nom commun et la clé privée au format .pfx. |
        | Type d’écouteur  | Multisite                        | Autoriser à écouter plusieurs sites sur la passerelle applicative           |
        | Type d’hôte      | Multiple/caractère générique                 | Définissez sur un nom de site web générique ou multiple si le type d’écouteur est défini sur plusieurs sites. |
        | Nom de l’hôte      | Par exemple : `app.asabuludemo.com` | Définir sur un nom de domaine routable pour App Service              |
        
        :::image type="content" source="./media/integrate-with-application-gateway/https-routing-rule.png" alt-text="Écouteur HTTPS de la règle d’acheminement de passerelle applicative.":::
    
    * Vous devez configurer un **Pool back-end** et un **Paramètre HTTP** dans les **Cibles back-end**. Le pool back-end a été configuré lors des étapes précédentes. Cliquez sur le lien **Ajouter nouveau** pour ajouter un paramètre HTTP.
    
        :::image type="content" source="./media/integrate-with-application-gateway/add-new-http-setting.png" alt-text="Capture d’écran du lien Ajouter nouveau pour ajouter un paramètre HTTP.":::
    
    * Paramètres HTTP répertoriés comme suit :
    
        | Paramètre                     | Valeur                                                        | Description                                                  |
        | ----------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
        | Nom du paramètre HTTP             | Par exemple : `https-setting`                                   | Nom du paramètre HTTP                                            |
        | Protocole back-end              | HTTPS                                                        | Utiliser le chiffrement TLS/SSL                                       |
        | Port principal                  | 443                                                          | Port HTTPS par défaut                                           |
        | Utiliser le certificat de l’autorité de certification connue | Oui                                                          | Le nom de domaine par défaut de l’environnement ILB ASE est **.appserviceenvironment.net**, le certificat de ce domaine est émis par une autorité racine approuvée publique. Dans le paramètre Certificat racine approuvé, vous pouvez définir d’utiliser un **certificat racine approuvé d’autorité de certification connu**. |
        | Substituer avec le nouveau nom d’hôte   | Oui                                                          | L’en-tête de nom d’hôte est remplacé lors de la connexion à l’application sur l’environnement ILB ASE |
        | Remplacement du nom d’hôte            | Choisir un nom d’hôte à partir d’une cible de back-end | Quand vous définissez le pool back-end sur App Service, vous pouvez choisir l’hôte à partir de la cible de back-end |
        | Créer des sondes personnalisées | Non | Utiliser la sonde d’intégrité par défaut|
        
        :::image type="content" source="./media/integrate-with-application-gateway/https-setting.png" alt-text="Capture d’écran de l’ajout des détails des paramètres HTTP.":::


## <a name="configure-an-application-gateway-integration-with-ilb-ase"></a>Configurer l’intégration de la passerelle applicative à l’environnement ILB ASE

Pour accéder à l’environnement ILB ASE à partir de la passerelle applicative, vous devez vérifier si un réseau virtuel est lié à une zone DNS privée. Si aucun réseau virtuel n’est lié au réseau virtuel de votre passerelle applicative, ajoutez une liaison de réseau virtuel en procédant comme suit.

### <a name="configure-virtual-network-links-with-a-private-dns-zone"></a>Configurer les liaisons de réseau virtuel avec une zone DNS privée

* Pour configurer la liaison de réseau virtuel avec une zone DNS privée, accédez au plan de configuration de la zone DNS privée. Sélectionner les **Liens de réseau virtuel** > **Ajouter** 

:::image type="content" source="./media/integrate-with-application-gateway/add-vnet-link.png" alt-text="Ajoutez une liaison de réseau virtuel vers la zone DNS privée.":::

* Entrez le **Nom de la liaison** et sélectionnez respectivement l’abonnement et le réseau virtuel dans lesquels réside la passerelle applicative.

:::image type="content" source="./media/integrate-with-application-gateway/vnet-link.png" alt-text="Capture d’écran des détails de l’ajout d’un nom de liaison pour le paramètre de liaisons de réseau virtuel dans la zone DNS privée.":::

* Vous pouvez vérifier l’état d’intégrité du serveur back-end à partir de **l’intégrité du serveur back-end** dans le plan de la passerelle applicative.

:::image type="content" source="./media/integrate-with-application-gateway/backend-health.png" alt-text="Capture d’écran de la confirmation de l’état d’intégrité à partir de l’intégrité back-end.":::

### <a name="add-a-public-dns-record"></a>Ajouter un enregistrement DNS public

Vous devez configurer un mappage DNS approprié pour accéder à la passerelle applicative à partir d’Internet.

* L’adresse IP publique de la passerelle applicative se trouve dans **Configurations d’adresses IP frontales** dans le plan de la passerelle applicative.

:::image type="content" source="./media/integrate-with-application-gateway/frontend-ip.png" alt-text="L’adresse IP front-end de la passerelle applicative est disponible dans la configuration de l’adresse IP front-end.":::

* Utilisez le service Azure DNS comme exemple, vous pouvez ajouter un jeu d’enregistrements pour mapper le nom de domaine d’application à l’adresse IP publique de la passerelle applicative.

:::image type="content" source="./media/integrate-with-application-gateway/dns-service.png" alt-text="Capture d’écran montrant l’ajout d’un jeu d’enregistrements pour mapper le nom de domaine d’application à l’adresse IP publique de la passerelle applicative.":::

### <a name="validate-connection"></a>Valider la connexion

* Sur un ordinateur qui accède à partir d’Internet, vous pouvez vérifier la résolution de noms pour le nom de domaine de l’application à l’adresse IP publique de la passerelle applicative.

:::image type="content" source="./media/integrate-with-application-gateway/name-resolution.png" alt-text="validez la résolution de noms à partir d’une invite de commandes.":::

* Sur un ordinateur qui accède à partir d’Internet, testez l’accès web à partir d’un navigateur.

:::image type="content" source="./media/integrate-with-application-gateway/access-web.png" alt-text="Capture d’écran de l’ouverture d’un navigateur, accès au web.":::

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[creation]: ./creation.md
[createfromtemplate]: ./create-from-template.md
[createprivatednszone]: ../../dns/private-dns-getstarted-portal.md
[AppServiceEnvironmentoverview]: ./overview.md
[privatednszone]: ../../dns/private-dns-overview.md
[Tutorial: Create an application gateway with a Web Application Firewall using the Azure portal]: ../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md
